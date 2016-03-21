<properties
   pageTitle="Présentation de Service Fabric Cluster Resource Manager"
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
   ms.date="03/03/2016"
   ms.author="masnider"/>

# Présentation de Service Fabric Cluster Resource Manager

Jusqu’ici, pour gérer des systèmes informatiques ou un ensemble de services, il suffisait de leur dédier quelques machines. Les principaux services étaient généralement divisés en plusieurs couches : une couche « web» et une couche « données » ou « stockage ». Vous pouviez aussi avoir quelques composants spécialisés comme un cache. D’autres types d’applications disposaient d’une couche « messagerie » dans laquelle circulaient les requêtes entrantes et sortantes. Cette couche était connectée à une couche « travail » où était effectuée toute analyse ou transformation nécessaire à la messagerie. Chaque partie recevait une machine spécifique ou quelques machines dédiées, comme la base de données et les serveurs web. Si un type particulier de charge de travail entraînait une surcharge des machines, il suffisait d’ajouter d’autres machines configurées pour exécuter ce type de charge de travail ou de remplacer quelques machines par des machines offrant davantage de stockage. Facile. Si une machine venait à tomber en panne, cette partie de l’application globale s’exécutait alors à une capacité inférieure jusqu’à la restauration de la machine. Toujours aussi facile (mais pas forcément très amusant).

Bon, supposons à présent que vous devez augmenter la taille des instances et que vous avez fait le grand saut dans le monde des conteneurs et/ou des microservices. Vous vous retrouvez soudain avec des centaines voire des milliers de machines, des dizaines de types de services différents et peut-être des centaines d’instances différentes de ces services, avec pour chaque service un ou plusieurs réplicas ou instances pour la haute disponibilité.

Tout à coup, la gestion de votre environnement se complique et ne se résume plus à quelques machines dédiées à des types de charges de travail uniques. Vos serveurs sont virtuels et n’ont plus de noms (vous êtes passé des [animaux domestiques (« pets ») au bétail (« cattle »)](http://www.slideshare.net/randybias/architectures-for-open-and-scalable-clouds/20), voilà tout). Les machines ne sont plus dédiées à des types uniques de charges de travail. La configuration est désormais axée davantage sur les services que sur les machines.

En raison de ce découplage et de la division de votre application monolithique à une couche en plusieurs services distincts, vous devez faire face à beaucoup plus de combinaisons. Qui détermine les types de charges de travail qui peuvent s’exécuter sur du matériel spécifique ou côté à côte ? Quand une machine tombe en panne, sait-on ce qu’elle exécutait ? Qui doit veiller à ce que la machine redevienne opérationnelle ? Est-ce que vous attendez que la machine (virtuelle) soit de nouveau en état de marche ou est-ce que vos charges de travail continuent de s’exécuter ? Une intervention humaine est-elle nécessaire pour que tout refonctionne ? Et qu’en est-il des mises à niveau dans ce type d’environnement ? Nous allons avoir besoin d’aide, mais comme vous vous en doutez, une stratégie d’embauche massive de personnel dans le seul but de masquer la complexité de la situation n’est pas la bonne solution.

Que faire ?

## Présentation des Orchestrators
Un « Orchestrator » est un terme général qui désigne un élément logiciel permettant aux administrateurs de gérer ces types de déploiements. Les Orchestrators sont les composants qui acceptent des requêtes du type « Je souhaiterais exécuter 5 copies de ce service dans mon environnement », les mettent en œuvre et tentent de les maintenir dans cet état. Les Orchestrators (qui ne sont pas des humains) passent à l’action quand une machine tombe en panne ou quand une charge de travail se termine pour une raison inattendue. La plupart des Orchestrators ne se limitent pas à la gestion des échecs, puisqu’ils contribuent aussi aux nouveaux déploiements, au traitement des mises à niveau, à la gestion de la consommation des ressources, etc. Mais leur rôle fondamental est de maintenir la configuration de l’environnement à un certain état souhaité. Dans l’idéal, vous devez pouvoir dire à un Orchestrator ce que vous souhaitez accomplir et le laisser faire le gros du travail. Chronos (sur Mesosphere), Fleet, Swarm, Kubernetes et Service Fabric sont des Orchestrators ou des services qui intègrent des Orchestrators. Dans le monde réel, vous devez gérer des déploiements dans différents types d’environnements et de conditions. Pour faire face à ces complexités qui changent et évoluent sans cesse, des Orchestrators sont créés régulièrement.

## Orchestration en tant que service
Le travail de l’Orchestrator dans un cluster Service Fabric est géré principalement par Cluster Resource Manager. Service Fabric Cluster Resource Manager, l’un des services système de Service Fabric, est démarré automatiquement dans chaque cluster. En règle générale, le travail de Resource Manager est divisé en trois parties :

1. Application des règles
2. Optimisation de l’environnement
3. Participation à d’autres processus

Avant d’examiner toutes les fonctionnalités de Resource Manager, penchons-nous brièvement sur son fonctionnement. Service Fabric Cluster Resource Manager

## Architecture générale et implémentation
Avant d’examiner Cluster Resource Manager et toutes ses fonctionnalités dans le détail, voyons d’abord en quoi il consiste et comment il fonctionne.

### Ce qu’il n’est pas
Les applications web traditionnelles à n couches étaient toujours associées au concept d’équilibreur de charge, généralement appelé « équilibreur de charge réseau » (NLB) ou « équilibreur de charge d’application » (ALB) selon sa position dans la pile de mise en réseau. Il y avait des équilibreurs de charge matériels, comme la gamme BigIP de F5, et d’autres logiciels, comme l’équilibreur de charge réseau Microsoft. Dans ces architectures, le travail d’équilibrage de la charge était de s’assurer que les différentes machines frontales sans état ou machines du cluster recevaient (à peu près) la même quantité de travail. Les méthodes pour y parvenir variaient : envoi de chaque appel à un serveur différent, adhérence/épinglage de session, estimation réelle, ou encore allocation des appels en fonction du coût prévu et de la charge machine actuelle.

Ce mécanisme permettait au mieux de s’assurer que la couche web était à peu près équilibrée. Les stratégies pour équilibrer la couche de données étaient complètement différentes et variaient selon le mécanisme de stockage de données. Elles étaient généralement axées sur le partitionnement des données, la mise en cache, les vues managées de bases de données, les procédures stockées, etc.

Bien que certaines de ces stratégies présentent un intérêt, Service Fabric Cluster Resource Manager n’a rien à voir avec un équilibreur de charge ou un cache. Pour s’assurer que les machines frontales sont équilibrées, un équilibreur de charge réseau déplace le trafic à l’endroit où les services sont exécutés. Service Fabric Resource Manager suit une approche différente : en fait, il déplace les services à l’endroit le plus logique. Il peut s’agir par exemple de nœuds qui sont actuellement froids, car les services qui s’y trouvent ne sont pas très actifs pour le moment. Il peut également tenir à l’écart un nœud qui doit être mis à niveau ou qui est surchargé en raison d’un pic de consommation des services qui s’exécutaient sur celui-ci. Comme il est responsable du déplacement des services (il n’achemine pas le trafic réseau à l’endroit où les services se trouvent déjà), Service Fabric Resource Manager est plus polyvalent. Il contient également des fonctionnalités supplémentaires qui permettent de contrôler à quel endroit les services sont déplacés et de quelle manière.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Étapes suivantes
- [En savoir plus sur l’architecture de Cluster Resource Manager](service-fabric-cluster-resource-manager-architecture.md)
- [Décrire le cluster](service-fabric-cluster-resource-manager-cluster-description.md)
- [En savoir plus sur la configuration des services](service-fabric-cluster-resource-manager-configure-services.md)
- [En savoir plus sur les métriques](service-fabric-cluster-resource-manager-metrics.md)
- [En savoir plus sur la façon dont Cluster Resource Manager s’intègre au reste des fonctionnalités de Service Fabric Management](service-fabric-cluster-resource-manager-management-integration.md)

<!---------HONumber=AcomDC_0309_2016-->