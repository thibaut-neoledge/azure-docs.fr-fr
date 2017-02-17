---
title: "Présentation de Service Fabric Cluster Resource Manager | Microsoft Docs"
description: "Présentation de Service Fabric Cluster Resource Manager."
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: 
ms.assetid: cfab735b-923d-4246-a2a8-220d4f4e0c64
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/05/2017
ms.author: masnider
translationtype: Human Translation
ms.sourcegitcommit: dafaf29b6827a6f1c043af3d6bfe62d480d31ad5
ms.openlocfilehash: ec470466f006265af5c4ccfddeeba975e6e648b5


---
# <a name="introducing-the-service-fabric-cluster-resource-manager"></a>Présentation de Service Fabric Cluster Resource Manager
Jusqu’ici, Jusqu’ici, pour gérer des systèmes informatiques ou un ensemble de services, il suffisait de leur dédier quelques machines physiques ou virtuelles. Les principaux services étaient généralement divisés en plusieurs couches : une couche « web» et une couche « données » ou « stockage ». Vous pouviez aussi avoir quelques composants spécialisés comme un cache. D’autres types d’applications avaient une couche de messagerie dédiée à l’entrée et à la sortie des requêtes. Ce niveau se connectait à un niveau de travail pour toute analyse ou transformation nécessaire dans le cadre de la messagerie. Chaque type de charge de travail recevait des machines spécifiques dédiées : quelques machines pour la base de données, quelques-unes pour les serveurs web. Si un type particulier de charge de travail entraînait la surchauffe des machines sur lesquelles il était exécuté, alors vous ajoutiez davantage de machines avec cette même configuration. Toutefois, la plupart du temps, vous remplaciez quelques-unes des machines par des machines de plus grande taille. Facile. Si une machine venait à tomber en panne, cette partie de l’application globale s’exécutait alors à une capacité inférieure jusqu’à la restauration de la machine. Toujours aussi facile (mais pas forcément très amusant).

Bon, supposons à présent que vous devez augmenter la taille des instances et que vous avez fait le grand saut dans le monde des conteneurs et/ou des microservices. Soudain, vous vous retrouvez avec des dizaines, des centaines voire des milliers de machines. Vous avez des dizaines de types de services différents (aucun ne consommant des ressources équivalentes à une machine complète), voire des centaines d’instances différentes de ces services. Chaque instance nommée possède un(e) ou plusieurs instances ou réplicas pour la haute disponibilité (HA).

Tout à coup, la gestion de votre environnement se complique et ne se résume plus à quelques machines dédiées à des types de charges de travail uniques. Vos serveurs sont virtuels et n’ont plus de noms (vous *êtes* passé des [animaux domestiques (« pets ») au bétail (« cattle »)](http://www.slideshare.net/randybias/architectures-for-open-and-scalable-clouds/20) , voilà tout). La configuration est désormais axée davantage sur les services que sur les machines. Le matériel dédié fait partie du passé, et même les services sont devenus de petits systèmes distribués couvrant de nombreux plus petits éléments de machines de grande série.

En raison de la division de votre application anciennement monolithique à une couche en plusieurs services distincts sur du matériel, vous devez faire face à beaucoup plus de combinaisons. Qui détermine les types ou le nombre de charges de travail pouvant être exécutées sur du matériel spécifique ? Quelles charges de travail fonctionnent bien sur le même matériel, et lesquelles sont en conflit ? Quand une machine tombe en panne, sait-on ce qu’elle exécutait ? Qui doit veiller à ce que la charge de travail redevienne opérationnelle ? Attendez-vous le retour de la machine (virtuelle ?), ou vos charges de travail basculent-elles automatiquement sur d’autres machines avant de continuer à s’exécuter ? Une intervention humaine est-elle nécessaire ? Qu’en est-il des mises à niveau dans cet environnement ?

En tant que développeurs et opérateurs gérant cela, nous allons avoir besoin d’aide pour gérer cette complexité. Vous vous en doutez, une stratégie d’embauche massive de personnel dans le seul but de masquer la complexité de la situation n’est pas la bonne solution.

Que faire, alors ?

## <a name="introducing-orchestrators"></a>Présentation des orchestrators
Un « Orchestrator » est un terme général qui désigne un élément logiciel permettant aux administrateurs de gérer ces types d’environnement. Les Orchestrators sont les composants qui acceptent des requêtes du type « Je souhaiterais exécuter cinq copies de ce service dans mon environnement ». Puis, ils tentent de faire correspondre l’environnement à l’état souhaité, quoi qu’il arrive.

Les Orchestrators (qui ne sont pas des humains) passent à l’action quand une machine tombe en panne ou quand une charge de travail se termine pour une raison inattendue. La plupart des Orchestrators ne se contentent pas de gérer l’échec. La gestion de nouveaux déploiements, des mises à niveau et de la consommation de ressources sont quelques-unes de leurs autres fonctions. Tous les Orchestrators se consacrent essentiellement à la maintenance d’un état souhaité de configuration dans l’environnement. Dans l’idéal, vous devez pouvoir dire à un Orchestrator ce que vous souhaitez accomplir et le laisser faire le gros du travail. Chronos ou Marathon sur Mesos, Fleet, Docker Datacenter/Docker Swarm, Kubernetes et Service Fabric sont des exemples d’Orchestrators ou de services qui intègrent des Orchestrators. Dans le monde réel, vous devez gérer des déploiements dans différents types d’environnements et de conditions. Pour faire face à ces complexités qui changent et évoluent sans cesse, des Orchestrators sont créés régulièrement.

## <a name="orchestration-as-a-service"></a>Orchestration en tant que service
Le travail de l’Orchestrator dans un cluster Service Fabric est géré principalement par Cluster Resource Manager. Service Fabric Cluster Resource Manager, l’un des services système de Service Fabric, est démarré automatiquement dans chaque cluster. En règle générale, le travail de Cluster Resource Manager est divisé en trois parties :

1. Application des règles
2. Optimisation de l’environnement
3. Aide des autres processus

Pour découvrir comment fonctionne Cluster Resource Manager, visionnez cette vidéo de la Microsoft Virtual Academy :<center><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=d4tka66yC_5706218965">
<img src="./media/service-fabric-cluster-resource-manager-introduction/ConceptsAndDemoVid.png" WIDTH="360" HEIGHT="244">
</a></center>

### <a name="what-it-isnt"></a>Ce qu’il n’est pas
Une notion « d’équilibreur de charge » a toujours existé dans les applications de couche N traditionnelles. Généralement, il s’agissait d’un équilibrage de charge réseau (NLB) ou d’un équilibrage de charge d’application (ALB) suivant son emplacement dans la pile de mise en réseau. Il existe des équilibreurs de charge matériels, comme la gamme BigIP de F5, et d’autres logiciels, comme l’équilibreur de charge réseau Microsoft. Dans d’autres environnements, vous verrez un élément similaire à HAProxy ou nginx dans ce rôle. Dans ces architectures, le travail d’équilibrage de charge consiste à garantir que les charges de travail sans état reçoivent (à peu près) la même quantité de travail. Les stratégies d’équilibrage de charge étaient variées. Certains équilibreurs de charge envoyaient chaque appel distinct vers un serveur différent. D’autres fournissaient l’épinglage/l’adhérence de session. Les équilibreurs de charge les plus avancés utilisent une estimation réelle ou la création de rapports pour acheminer un appel en fonction de son coût prévu et de la charge de la machine actuelle.

Les équilibreurs de réseau ou les routeurs de messages essayaient de s’assurer que le niveau web/travail restait à peu près équilibré. Les stratégies pour équilibrer la couche de données étaient différentes et variaient selon le mécanisme de stockage de données. Elles étaient généralement axées sur le partitionnement des données, la mise en cache, les vues managées, les procédures stockées et d’autres mécanismes spécifiques au magasin.

Bien que certaines de ces stratégies présentent un intérêt, Service Fabric Cluster Resource Manager n’a rien à voir avec un équilibreur de charge ou un cache. Un équilibreur de charge réseau garantit que les serveurs frontaux sont équilibrés en déplaçant le trafic vers l’emplacement où les services sont en cours d’exécution. Service Fabric Cluster Resource Manager utilise une stratégie différente. Essentiellement, Service Fabric déplace des *services* vers l’emplacement où ils sont les plus significatifs, en attente de trafic ou d’une charge à venir. Par exemple, il peut déplacer des services vers des nœuds qui sont actuellement à froid, car les services qui y sont présents n’effectuent pas beaucoup de travail. Les nœuds peuvent être à froid, car les services qui étaient présents ont été supprimés ou déplacés ailleurs. Autre exemple, Cluster Resource Manager peut également déplacer un service en dehors d’une machine. Peut-être que celle-ci est sur le point d’être mise à niveau ou qu’elle est surchargée en raison d’un pic de consommation par les services qui y sont exécutés.

Étant donné que Cluster Resource Manager est responsable du déplacement des services (sans distribution du trafic réseau vers les endroits où les services se trouvent), il contient un ensemble de fonctionnalités différentes par rapport à ce que vous trouveriez dans un équilibrage de charge réseau. Dans notre exploration détaillée à venir, nous constaterons qu’il utilise des stratégies très différentes pour s’assurer que les ressources matérielles dans le cluster sont utilisées efficacement.

## <a name="next-steps"></a>Étapes suivantes
* Pour plus d’informations sur l’architecture et le flux d’informations dans Cluster Resource Manager, consultez [cet article ](service-fabric-cluster-resource-manager-architecture.md)
* Cluster Resource Manager comporte de nombreuses options permettant de décrire le cluster. Pour en savoir plus sur celles-ci, consultez cet article sur la [description d’un cluster Service Fabric](service-fabric-cluster-resource-manager-cluster-description.md)
* Pour plus d’informations sur les autres options disponibles pour la configuration des services, consultez la rubrique sur les autres configurations de Cluster Resource Manager disponibles [En savoir plus sur la configuration des services](service-fabric-cluster-resource-manager-configure-services.md)
* Les métriques représentent la façon dont Service Fabric Cluster Resource Manager gère la consommation et la capacité du cluster. Pour en savoir plus sur ces métriques et la façon de les configurer, consultez [cet article](service-fabric-cluster-resource-manager-metrics.md)
* Cluster Resource Manager fonctionne avec les fonctionnalités de gestion de Service Fabric. Pour en savoir plus sur cette intégration, consultez [cet article](service-fabric-cluster-resource-manager-management-integration.md)
* Pour en savoir plus sur la façon dont Cluster Resource Manager gère et équilibre la charge du cluster, consultez l’article sur [l’équilibrage de la charge](service-fabric-cluster-resource-manager-balancing.md)



<!--HONumber=Feb17_HO3-->


