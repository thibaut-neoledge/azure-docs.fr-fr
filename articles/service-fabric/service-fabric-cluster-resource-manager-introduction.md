<properties
   pageTitle="Présentation de Service Fabric Cluster Resource Manager | Microsoft Azure"
   description="Présentation de Service Fabric Cluster Resource Manager."
   services="service-fabric"
   documentationCenter=".net"
   authors="masnider"
   manager="timlt"
   editor=""/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/19/2016"
   ms.author="masnider"/>

# Présentation de Service Fabric Cluster Resource Manager
Jusqu’ici, Jusqu’ici, pour gérer des systèmes informatiques ou un ensemble de services, il suffisait de leur dédier quelques machines physiques ou virtuelles. Les principaux services étaient généralement divisés en plusieurs couches : une couche « web» et une couche « données » ou « stockage ». Vous pouviez aussi avoir quelques composants spécialisés comme un cache. D’autres types d’applications disposaient d’une couche « messagerie » dans laquelle circulaient les requêtes entrantes et sortantes. Cette couche était connectée à une couche « travail » où était effectuée toute analyse ou transformation nécessaire à la messagerie. Chaque type de charge de travail recevait des machines spécifiques dédiées : quelques machines pour la base de données, quelques-unes pour les serveurs web. Si un type particulier de charge de travail entraînait une surcharge des machines, il suffisait d’ajouter d’autres machines configurées pour exécuter ce type de charge de travail ou de remplacer quelques machines par des machines offrant davantage de stockage. Facile. Si une machine venait à tomber en panne, cette partie de l’application globale s’exécutait alors à une capacité inférieure jusqu’à la restauration de la machine. Toujours aussi facile (mais pas forcément très amusant).

Bon, supposons à présent que vous devez augmenter la taille des instances et que vous avez fait le grand saut dans le monde des conteneurs et/ou des microservices. Vous vous retrouvez soudain avec des dizaines, des centaines, voire des milliers de machines, des dizaines de types de services différents et peut-être des centaines d’instances différentes de ces services, avec pour chaque service un ou plusieurs réplicas ou instances pour la haute disponibilité.

Tout à coup, la gestion de votre environnement se complique et ne se résume plus à quelques machines dédiées à des types de charges de travail uniques. Vos serveurs sont virtuels et n’ont plus de noms (vous *êtes* passé des [animaux domestiques (« pets ») au bétail (« cattle »)](http://www.slideshare.net/randybias/architectures-for-open-and-scalable-clouds/20), voilà tout). La configuration est désormais axée davantage sur les services que sur les machines. Le matériel dédié fait partie du passé, et les services sont devenus de petits systèmes distribués couvrant de nombreux plus petits éléments de machines de grande série.

En raison de la division de votre application anciennement monolithique à une couche en plusieurs services distincts sur du matériel, vous devez faire face à beaucoup plus de combinaisons. Qui détermine les types ou le nombre de charges de travail pouvant être exécutées sur du matériel spécifique ? Quelles charges de travail fonctionnent bien sur le même matériel, et lesquelles sont en conflit ? Quand une machine tombe en panne, sait-on ce qu’elle exécutait ? Qui doit veiller à ce que la charge de travail redevienne opérationnelle ? Attendez-vous le retour de la machine (virtuelle ?), ou vos charges de travail basculent-elles automatiquement sur d’autres machines avant de continuer à s’exécuter ? Une intervention humaine est-elle nécessaire ? Qu’en est-il des mises à niveau dans ce type d’environnement ?

Les développeurs et opérateurs vivant dans ce type d’environnement ont besoin d’aide pour en gérer la complexité. Cependant, comme vous vous en doutez, une stratégie d’embauche massive de personnel dans le seul but de masquer la complexité de la situation n’est pas la bonne solution.

Que faire, alors ?

## Présentation des orchestrators
Un « Orchestrator » est un terme général qui désigne un élément logiciel permettant aux administrateurs de gérer ces types d’environnement. Les Orchestrators sont les composants qui acceptent des requêtes du type « Je souhaiterais exécuter 5 copies de ce service dans mon environnement », les mettent en œuvre et tentent ensuite de les maintenir dans cet état.

Les Orchestrators (qui ne sont pas des humains) passent à l’action quand une machine tombe en panne ou quand une charge de travail se termine pour une raison inattendue. La plupart des Orchestrators ne se limitent pas à la gestion des échecs, puisqu’ils contribuent aussi aux nouveaux déploiements, au traitement des mises à niveau, à la gestion de la consommation des ressources, etc. Mais leur rôle fondamental est de maintenir la configuration de l’environnement à un certain état souhaité. Dans l’idéal, vous devez pouvoir dire à un Orchestrator ce que vous souhaitez accomplir et le laisser faire le gros du travail. Chronos ou Marathon sur Mesos, Fleet, Swarm, Kubernetes et Service Fabric sont des exemples d’Orchestrators ou des services qui intègrent des Orchestrators. Dans le monde réel, vous devez gérer des déploiements dans différents types d’environnements et de conditions. Pour faire face à ces complexités qui changent et évoluent sans cesse, des Orchestrators sont créés régulièrement.

## Orchestration en tant que service
Le travail de l’Orchestrator dans un cluster Service Fabric est géré principalement par Cluster Resource Manager. Service Fabric Cluster Resource Manager, l’un des services système de Service Fabric, est démarré automatiquement dans chaque cluster. En règle générale, le travail de Cluster Resource Manager est divisé en trois parties :

1. Application des règles
2. Optimisation de l’environnement
3. Participation à d’autres processus

### Ce qu’il n’est pas
Les applications web traditionnelles à n couches étaient toujours associées au concept d’équilibreur de charge, généralement appelé « équilibreur de charge réseau » (NLB) ou « équilibreur de charge d’application » (ALB) selon sa position dans la pile de mise en réseau. Il y avait des équilibreurs de charge matériels, comme la gamme BigIP de F5, et d’autres logiciels, comme l’équilibreur de charge réseau Microsoft. Dans d’autres environnements, vous verrez un élément similaire à HAProxy dans ce rôle. Dans ces architectures, le travail d’équilibrage de la charge était de s’assurer que les différentes machines frontales sans état ou machines du cluster recevaient (à peu près) la même quantité de travail. Les méthodes pour y parvenir variaient : envoi de chaque appel à un serveur différent, adhérence/épinglage de session, estimation réelle, ou encore allocation des appels en fonction du coût prévu et de la charge machine actuelle.

Ce mécanisme permettait au mieux de s’assurer que la couche web était à peu près équilibrée. Les stratégies pour équilibrer la couche de données étaient complètement différentes et variaient selon le mécanisme de stockage de données. Elles étaient généralement axées sur le partitionnement des données, la mise en cache, les vues managées de bases de données, les procédures stockées, etc.

Bien que certaines de ces stratégies présentent un intérêt, Service Fabric Cluster Resource Manager n’a rien à voir avec un équilibreur de charge ou un cache. Pour s’assurer que les machines frontales sont équilibrées, un équilibreur de charge réseau déplace le trafic à l’endroit où les services sont exécutés. Service Fabric Cluster Resource Manager suit une stratégie différente : en fait, Service Fabric déplace les *services* à l’endroit le plus logique (et le trafic ou la charge sont supposés suivre). Il peut s’agir par exemple de nœuds qui sont actuellement froids, car les services qui s’y trouvent ne sont pas très actifs pour le moment, ont été supprimés ou déplacés. Le Cluster Resource Manager peut également déplacer un service d’un ordinateur qui doit être mis à niveau ou qui est surchargé en raison d’un pic de consommation des services qui s’y exécutaient. Étant donné que le Cluster Resource Manager est responsable du déplacement des services (sans distribution du trafic réseau vers les endroits où les services se trouvent), il contient un ensemble de fonctionnalités très différentes par rapport à ce que vous trouveriez dans un équilibrage de charge réseau et emploie des stratégies fondamentalement différentes pour s’assurer que les ressources matérielles dans le cluster sont correctement utilisées.

## Étapes suivantes
- Pour plus d’informations sur l’architecture et le flux d’informations dans Cluster Resource Manager, consultez [cet article ](service-fabric-cluster-resource-manager-architecture.md)
- Cluster Resource Manager comporte de nombreuses options permettant de décrire le cluster. Pour en savoir plus sur celles-ci, consultez cet article sur la [description d’un cluster Service Fabric](service-fabric-cluster-resource-manager-cluster-description.md)
- Pour plus d’informations sur les autres options disponibles pour la configuration des services, consultez la rubrique sur les autres configurations de Cluster Resource Manager disponibles [En savoir plus sur la configuration des services](service-fabric-cluster-resource-manager-configure-services.md)
- Les métriques représentent la façon dont Service Fabric Cluster Resource Manager gère la consommation et la capacité du cluster. Pour en savoir plus sur ces métriques et la façon de les configurer, consultez [cet article](service-fabric-cluster-resource-manager-metrics.md)
- Cluster Resource Manager fonctionne avec les fonctionnalités de gestion de Service Fabric. Pour en savoir plus sur cette intégration, consultez [cet article](service-fabric-cluster-resource-manager-management-integration.md)
- Pour en savoir plus sur la façon dont Cluster Resource Manager gère et équilibre la charge du cluster, consultez l’article sur [l’équilibrage de la charge](service-fabric-cluster-resource-manager-balancing.md)

<!---HONumber=AcomDC_0824_2016-->