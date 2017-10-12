---
title: "Récupération d’urgence Azure Service Fabric | Microsoft Docs"
description: "Azure Service Fabric offre les fonctionnalités nécessaires pour gérer tous les types d’incidents. Cet article décrit les types d’incidents qui peuvent se produire et comment les gérer."
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: 
ms.assetid: ab49c4b9-74a8-4907-b75b-8d2ee84c6d90
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 5346e331d76149ac3aed7aaf11eb3171e0ac5cfc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="disaster-recovery-in-azure-service-fabric"></a>Récupération d’urgence dans Azure Service Fabric
Pour fournir une haute disponibilité, il est essentiel que les services puissent survivre à tous les types d’échecs. Ceci est particulièrement important pour les échecs inattendus et hors de votre contrôle. Cet article décrit certains modes d’échec courants qui peuvent aboutir à une situation critique s’ils ne sont pas modélisés et gérés correctement. Il traite également des atténuations de risques et des actions à entreprendre si un incident se produit. L’objectif est de limiter ou d’éliminer le risque de temps d’arrêt ou de perte de données en cas de défaillances, planifiées ou non.

## <a name="avoiding-disaster"></a>Éviter les incidents
L’objectif principal de Service Fabric est de vous aider à modéliser votre environnement et vos services de sorte que les échecs courants ne se transforment pas en incidents. 

En général, il existe deux types de scénarios d’incident/échec :

1. Défaillances matérielles ou logicielles
2. Défaillances opérationnelles

### <a name="hardware-and-software-faults"></a>Défaillances matérielles et logicielles
Les défaillances matérielles et logicielles sont imprévisibles. Le moyen le plus simple de survivre aux défaillances est d’exécuter plusieurs copies du service au-delà des limites des défaillances matérielles ou logicielles. Par exemple, si votre service s’exécute sur une seule machine, l’échec de cette machine est une situation d’urgence pour le service. Pour éviter les situations d’urgence, le service doit être exécuté sur plusieurs machines. Des tests sont également nécessaires pour garantir que la défaillance d’une machine ne va pas perturber le service en cours d’exécution. La planification de la capacité garantit qu’une instance de remplacement peut être créée ailleurs et que la réduction de la capacité ne va pas surcharger les services restants. Le même modèle fonctionne, quel que soit le type d’échec que vous cherchez à éviter. Par exemple, Si vous cherchez surtout à éviter les défaillances d’un réseau SAN, vous pouvez exécuter le service sur plusieurs réseaux SAN. Si vous cherchez surtout à éviter la perte d’un rack de serveurs, vous pouvez exécuter le service sur plusieurs racks. Si vous cherchez surtout à éviter la perte de centres de données, vous pouvez exécuter le service sur plusieurs régions ou centres de données Azure. 

Si vous exécutez le service à plusieurs endroits, vous pouvez toujours rencontrer certains types d’échecs simultanés. Toutefois, les échecs (survenant seuls ou groupés) d’un type particulier (par exemple, l’échec d’une liaison avec une machine virtuelle ou avec le réseau) sont gérés automatiquement (et n’aboutissent donc plus à des situations d’urgence). Service Fabric fournit de nombreux mécanismes pour le développement du cluster et gère la restauration des nœuds et services ayant échoué. Service Fabric permet également d’exécuter de nombreuses instances de vos services afin d’éviter que ces types de défaillances non planifiées n’aboutissent à des situations d’urgence.

Il peut y avoir des cas dans lesquels il n’est pas possible d’effectuer un déploiement suffisamment grand pour couvrir tous les échecs. Par exemple, un tel déploiement peut nécessiter davantage de ressources matérielles dans lesquelles vous n’êtes pas disposé à investir, après évaluation des risques de défaillances. Lorsque vous utilisez des applications distribuées, il est possible que des tronçons de communication ou des coûts de réplication d’état supplémentaires vers des sites distants provoquent une latence inacceptable. Votre décision doit être prise en fonction de chaque application. Pour les défaillances logicielles en particulier, la défaillance peut se situer au niveau du service que vous souhaitez mettre à l’échelle. Dans ce cas, l’utilisation de copies supplémentaires ne permet pas d’empêcher l’incident, étant donné que la condition d’échec concerne toutes les instances.

### <a name="operational-faults"></a>Défaillances opérationnelles
Même si votre service est exécuté dans le monde entier avec de nombreuses redondances, les situations d’urgence sont toujours possibles. Par exemple, il est possible qu’une personne reconfigure le nom DNS du service par inadvertance, ou qu’il le supprime. Par exemple, imaginons que vous disposiez d’un service Service Fabric avec état, et que quelqu’un ait supprimé ce service par inadvertance. À moins que d’autres mesures d’atténuation des risques ne soient en place, le service et son état ont désormais disparu. Ces incidents opérationnels (de type « Désolé… ») nécessitent des mesures d’atténuation des risques et une procédure de récupération différentes de celles employées pour les défaillances inattendues. 

Les méthodes recommandées pour éviter ce type de défaillances opérationnelles sont les suivantes :
1. Restreindre l’accès opérationnel à l’environnement
2. Réaliser un audit strict des opérations dangereuses
3. Imposer l’automatisation, empêcher les modifications manuelles ou hors bande et valider les modifications par rapport à l’environnement avant de les appliquer
4. Faire en sorte que les opérations de destruction ne soient pas définitives. Les opérations non définitives ne sont pas appliquées immédiatement ou peuvent être annulées dans un certain délai.

Service Fabric fournit certains mécanismes pour éviter les défaillances opérationnelles, tels que le contrôle d’accès [en fonction du rôle](service-fabric-cluster-security-roles.md) pour les opérations de cluster. Toutefois, pour éviter la plupart de ces défaillances, des efforts d’organisation et des systèmes supplémentaires sont nécessaires. Service Fabric fournit un mécanisme permettant de survivre aux défaillances opérationnelles, notamment la sauvegarde et la restauration des services avec état.

## <a name="managing-failures"></a>Gestion des défaillances
L’objectif de Service Fabric est de pouvoir gérer automatiquement la quasi totalité des défaillances. Toutefois, pour que la gestion de certains types de défaillances soit possible, les services doivent comprendre du code supplémentaire. Certains types de défaillances _ne doivent pas_ être gérés automatiquement, pour des raisons de sécurité et de continuité opérationnelle. 

### <a name="handling-single-failures"></a>Gestion des défaillances uniques
Les machines uniques peuvent connaître une défaillance pour diverses raisons. L’origine de la défaillance peut être matérielle, comme dans le cas d’une défaillance de l’alimentation ou du matériel réseau. Certaines défaillances peuvent se situer au niveau du logiciel. Il s’agit notamment des défaillances du système d’exploitation ou du service. Service Fabric détecte automatiquement ce type de défaillance, notamment lorsqu’une machine est isolée des autres machines en raison d’un problèmes réseau.

Quel que soit le type du service, si vous n’exécutez qu’une seule instance de ce service et que son code échoue pour une raison quelconque, le service connaîtra un temps d’arrêt. 

Pour gérer les défaillances uniques, le plus simple est de faire en sorte que vos services soient exécutés sur plusieurs nœuds par défaut. Pour les services sans état, il suffit de définir `InstanceCount` sur une valeur supérieure à 1. Pour les services avec état, il est recommandé de définir `TargetReplicaSetSize` et `MinReplicaSetSize` sur une valeur supérieure ou égale à 3. Le fait d’exécuter plusieurs copies du code de votre service permet au service de gérer automatiquement les défaillances uniques. 

### <a name="handling-coordinated-failures"></a>Gestion des défaillances multiples
Les défaillances multiples peuvent se produire dans un cluster en raison de défaillances ou de modifications planifiées ou non planifiées, ou en cas de modifications logicielles planifiées. Service Fabric modélise les zones d’infrastructure qui rencontrent des défaillances multiples comme des domaines d’erreur. Les zones qui subissent des modifications logicielles coordonnées sont modélisées comme des domaines de mise à niveau. Pour plus d’informations sur les domaines d’erreur et de mise à niveau, consultez [ce document](service-fabric-cluster-resource-manager-cluster-description.md) qui donne une définition des clusters et explique leur topologie.

Par défaut, Service Fabric prend en compte les domaines d’erreur et de mise à niveau lorsqu’il planifie où vos services doivent s’exécuter. Par défaut, Service Fabric tente de faire en sorte que vos services s’exécutent sur plusieurs domaines d’erreur et de mise à niveau. Ainsi, si des modifications planifiées ou non planifiées se produisent, vos services restent disponibles. 

Par exemple, supposons que la défaillance d’une source d’alimentation provoque la défaillance simultanée de toutes les machines d’un rack. Si plusieurs copies du service sont exécutées, la perte de plusieurs machines en raison d’une défaillance de domaine d’erreur aboutit à la défaillance unique d’un service donné. C’est pourquoi la gestion des domaines d’erreur est essentielle pour garantir la haute disponibilité de vos services. Lorsque vous exécutez Service Fabric dans Azure, les domaines d’erreur sont gérés automatiquement. Cela peut ne pas être le cas dans d’autres environnements. Si vous créez vos propres clusters locaux, veillez à mapper et à planifier la disposition du domaine d’erreur correctement.

Les domaines de mise à niveau sont utiles pour modéliser les zones qui vont subir en même temps une mise à niveau logicielle. Pour cette raison, les domaines de mise à niveau définissent souvent les limites du logiciel pendant les mises à niveau planifiées. Les mises à niveau de Service Fabric et de vos services suivent le même modèle. Pour plus d’informations sur le déploiement des mises à niveau, les domaines de mise à niveau et le modèle d’intégrité Service Fabric qui permet d’empêcher des modifications non souhaitées d’avoir un impact sur le cluster et sur votre service, consultez ces documents :

 - [Mise à niveau de l’application](service-fabric-application-upgrade.md)
 - [Tutoriel sur la mise à niveau des applications](service-fabric-application-upgrade-tutorial.md)
 - [Modèle d’intégrité Service Fabric](service-fabric-health-introduction.md)

Vous pouvez visualiser la disposition de votre cluster à l’aide de la carte de cluster fournie dans [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) :

<center>
![Nœuds répartis dans les domaines d’erreur dans Service Fabric Explorer][sfx-cluster-map]
</center>

> [!NOTE]
> La modélisation des zones de défaillance, la propagation des mises à niveau, l’exécution de plusieurs instances du code et de l’état de votre service, les règles de sélection élective pour garantir l’exécution de vos services sur plusieurs domaines d’erreur et de mise à niveau, et la surveillance intégrée de l’intégrité ne constituent que **quelques exemples** des fonctionnalités fournies par Service Fabric pour empêcher les problèmes et les défaillances opérationnels de se transformer en incidents. 
>

### <a name="handling-simultaneous-hardware-or-software-failures"></a>Gestion des défaillances matérielles ou logicielles simultanées
Nous avons parlé précédemment des défaillances uniques. Comme vous pouvez le voir, les défaillances sont faciles à gérer, aussi bien pour les services avec que sans état. Il vous suffit pour cela d’exécuter plusieurs copies du code (et de l’état) sur les différents domaines d’erreur et de mise à niveau. Les défaillances multiples, simultanées et aléatoires sont également possibles. Ces défaillances sont davantage susceptibles d’aboutir à un incident.


### <a name="random-failures-leading-to-service-failures"></a>Défaillances aléatoires entraînant un échec du service
Supposons que le service ait un `InstanceCount` d’une valeur de 5, et que plusieurs nœuds exécutés sur ces instances aient échoué en même temps. Service Fabric répond à cette situation en créant automatiquement des instances de remplacement sur d’autres nœuds. Il va continuer à créer des remplacements jusqu’à ce que le service atteigne de nouveau le nombre d’instances souhaité. Prenons un autre exemple. Supposons un service sans état avec un `InstanceCount` d’une valeur égale à -1, ce qui signifie qu’il s’exécute sur tous les nœuds valides du cluster. Supposons que certaines de ces instances connaissent une défaillance. Dans ce cas, Service Fabric détecte que le service n’est pas dans son état souhaité, et essaie de créer des instances là où elles manquent sur les nœuds. 

Pour les services avec état, la situation varie selon que les services sont persistants ou non. Cela dépend également du nombre de réplicas dont dispose le service, et de combien d’entre eux ont échoué. Pour déterminer si un incident s’est produit pour un service avec état et pour gérer cet incident, effectuez les trois étapes suivantes :

1. Déterminez s’il y a eu perte de quorum, ou non
 - Une perte de quorum se produit lorsque la majorité des réplicas d’un service avec état sont défaillants en même temps, y compris le réplica principal.
2. Déterminez si la perte de quorum est permanente ou non
 - La plupart du temps, ces défaillances sont passagères. Les processus sont redémarrés, les nœuds sont redémarrés, les machines virtuelles sont relancées et les partitions réseau sont réparées. Cependant, il arrive que les défaillances soient permanentes. 
    - Pour les services non persistants, la défaillance d’un quorum ou de plusieurs réplicas aboutit _immédiatement_ à une perte de quorum permanente. Quand Service Fabric détecte une perte de quorum dans un service avec état non persistant, il passe immédiatement à l’étape 3 en déclarant une perte de données (potentielle). La déclaration de perte de données est logique. En effet, Service Fabric sait qu’il est inutile d’attendre le retour des réplicas, car même s’ils pouvaient être récupérés, ils seraient vides.
    - Pour les services persistants avec état, lorsqu’une défaillance de quorum ou de plusieurs réplicas se produit, Service Fabric se met à attendre le retour des réplicas et la restauration du quorum. Cela entraîne une interruption de service pour les _opérations d’écriture_ dans la partition (ou « jeu de réplicas ») concernée. Cependant, les opérations de lecture sont toujours possibles, avec toutefois une moins bonne garantie de cohérence. Par défaut, Service Fabric attend la restauration du quorum indéfiniment, car le fait de continuer sans attendre la restauration peut entraîner une perte de données et présente d’autres risques. Vous pouvez remplacer la valeur `QuorumLossWaitDuration` par défaut, bien que cela ne soit pas recommandé. À ce stade, tous les efforts devraient plutôt être consacrés à la restauration des réplicas défaillants. Pour cela, vous devez restaurer les nœuds arrêtés et faire en sorte que les lecteurs où est stocké l’état permanent local puissent être remontés. Si la perte de quorum est provoquée par l’échec d’un processus, Service Fabric essaie automatiquement de recréer ces processus et de redémarrer les réplicas qu’ils contiennent. En cas d’échec, Service Fabric signale des erreurs d’intégrité. Si ces erreurs peuvent être résolues, les réplicas sont généralement restaurés. Parfois, cependant, les réplicas ne peuvent pas être restaurés. Ce sera le cas, par exemple, si les lecteurs sont tous défaillants ou si les ordinateurs ont été détruits. Dans ces cas-là, nous avons une perte de quorum permanente. Pour indiquer à Service Fabric de cesser d’attendre les réplicas défaillants, un administrateur de cluster doit déterminer quelles partitions de quels services sont affectées, et appeler l’API `Repair-ServiceFabricPartition -PartitionId` ou ` System.Fabric.FabricClient.ClusterManagementClient.RecoverPartitionAsync(Guid partitionId)`.  Cette API permet de spécifier l’ID de la partition à faire passer de l’état de perte de quorum à celui de perte de données potentielle.

> [!NOTE]
> L’utilisation de cette API autrement que pour cibler certaines partitions n’est _jamais_ sûre. 
>

3. Déterminez si une perte de données a bien eu lieu et restaurez-les à partir des sauvegardes
  - Lorsque Service Fabric appelle la méthode `OnDataLossAsync`, c’est toujours dans le cas d’une perte de données _supposée_. Service Fabric vérifie que cet appel est remis au _meilleur_ réplica restant. Il s’agira du réplica qui a le plus progressé. La raison pour laquelle nous parlons toujours de perte de données _supposée_ est parce qu’il est possible que le réplica restant ait le même état que celui du réplica principal au moment où il est devenu défaillant. Toutefois, sans cet état auquel le comparer, il n’existe aucun moyen pour Service Fabric ou les opérateurs de s’en assurer. À ce stade, Service Fabric sait également que les autres réplicas ne pourront pas être restaurés. C’est la décision que nous avons prise lorsque nous avons arrêté d’attendre la résolution de la perte de quorum. Le meilleur plan d’action pour le service est généralement de tout stopper et d’attendre une intervention de l’administrateur. Qu’est-ce que fait une implémentation classique de la méthode `OnDataLossAsync` ?
  - Tout d’abord, créez une entrée de journal dans laquelle vous indiquerez que `OnDataLossAsync` a été déclenché, puis déclenchez les alertes d’administration nécessaires.
   - En général, à ce stade, vous devez attendre que l’administrateur ait pris une décision et entreprenne les actions nécessaires. En effet, même si des sauvegardes sont disponibles, elles doivent d’abord être préparées. Par exemple, si deux services coordonnent des informations, ces sauvegardes devront peut-être être modifiées afin de garantir qu’une fois la restauration effectuée, les informations qui importent le plus pour ces deux services sont cohérentes. 
  - Le service implique parfois des données de télémétrie. Ces métadonnées peuvent être contenues dans d’autres services ou dans des journaux. Ces informations peuvent être nécessaires pour déterminer si des appels reçus et traités sur le réplica principal ne sont pas présents dans la sauvegarde ou n’ont pas été répliqués sur un réplica donné. Ces appels devront peut-être être relus ou ajoutés à la sauvegarde pour permettre la restauration.  
   - Comparez l’état du réplica restant à celui contenu dans toutes les sauvegardes disponibles. Si vous utilisez des collections fiables Service Fabric, consultez [cet article](service-fabric-reliable-services-backup-restore.md) pour connaître les outils et les processus permettant d’effectuer cette comparaison. L’objectif est de voir si l’état au sein du réplica est suffisant ou quels éléments manquent à la sauvegarde.
  - Une fois la comparaison effectuée, et si nécessaire, une fois la restauration terminée, le code du service doit retourner la valeur true si des modifications d’état ont été apportées. Si le réplica détermine qu’il s’agit de la meilleure copie disponible de l’état et n’apporte aucune modification, le code retourne la valeur false. La valeur true indique que les _autres_ réplicas restants peuvent maintenant ne plus être cohérents avec ce réplica. Ils sont donc supprimés et reconstruits à partir de ce réplica. La valeur false indique qu’aucune modification d’état n’a été apportée, et donc, que les autres réplicas peuvent être conservés tels quels. 

Il est extrêmement important que les auteurs du service testent les scénarios de défaillance et de perte de données potentielle avant de déployer les services dans l’environnement de production. Pour vous protéger contre le risque de perte, il est important de régulièrement [sauvegarder l’état](service-fabric-reliable-services-backup-restore.md) de vos services avec état dans un magasin géoredondant. Vous devez également être sûr que vous avez la possibilité de le restaurer. Étant donné que des sauvegardes de nombreux services différents sont effectuées à des moments différents, vous devez être sûr qu’après la restauration, vos services ont une vision cohérente des uns des autres. Par exemple, prenons le cas d’un service qui génère un numéro et le stocke, puis l’envoie à un autre service qui le stocke également. Après une restauration, vous pouvez vous rendre compte que le deuxième service contient le numéro, mais pas le premier service, parce que sa sauvegarde n’a pas inclus cette opération.

Si vous découvrez que les réplicas restants ne suffisent pas pour continuer dans un scénario de perte, et si vous ne pouvez pas reconstruire l’état du service à partir des données de télémétrie, c’est la fréquence de vos sauvegardes qui va déterminer votre meilleur objectif de point de récupération (RPO). Service Fabric fournit de nombreux outils pour tester divers scénarios d’échec, y compris la perte de quorum permanente et la perte de données qui nécessitent une restauration à partir d’une sauvegarde. Ces scénarios sont inclus dans les outils de testabilité de Service Fabric, qui sont gérés par Fault Analysis Service. Pour plus d’informations sur ces outils et ces modèles, [cliquez ici](service-fabric-testability-overview.md). 

> [!NOTE]
> Les services système peuvent également subir une perte de quorum, l’impact étant propre au service en question. Par exemple, une perte de quorum dans le service d’affectation de noms affecte la résolution de noms, tandis qu’une perte de quorum dans le service Failover Manager bloque la création de services et les basculements. Même si les services système de Service Fabric suivent le même modèle que vos services pour la gestion des états, il est déconseillé de les faire passer de l’état de perte de quorum à celui de perte de données potentielle. Il est plutôt recommandé de [contacter le support Service Fabric](service-fabric-support.md) afin de déterminer la solution qui convient le mieux à votre situation.  En général, il est préférable d’attendre le retour des réplicas défaillants.
>

## <a name="availability-of-the-service-fabric-cluster"></a>Disponibilité du cluster Service Fabric
En règle générale, le cluster Service Fabric est un environnement hautement distribué sans point de défaillance unique. Une défaillance au niveau d’un nœud n’entraîne pas de problèmes de disponibilité ou de fiabilité pour le cluster. Ceci est principalement dû au fait que les services système de Service Fabric suivent les instructions fournies plus haut, c’est-à-dire qu’ils sont toujours exécutés avec un minimum de trois réplicas par défaut, et que les services système qui sont sans état sont exécutés sur tous les nœuds. Les couches sous-jacentes Service Fabric de détection du réseau et des défaillances sont entièrement distribuées. La plupart des services système peuvent être reconstruits à partir des métadonnées dans le cluster, ou sinon, savent comment resynchroniser leur état dans d’autres emplacements. La disponibilité du cluster peut être compromise si les services système connaissent une perte de quorum, comme celle décrite plus haut. Dans ce cas, vous pouvez ne pas être en mesure d’effectuer certaines opérations sur le cluster, comme démarrer une mise à niveau ou déployer de nouveaux services. Toutefois, le cluster reste disponible. Les services qui sont déjà en cours d’exécution le resteront dans ces conditions, sauf s’ils ont besoin que les opérations d’écriture continuent de fonctionner dans les services système. Par exemple, si Failover Manager connaît une perte de quorum, tous les services continuent de s’exécuter. Toutefois, les services qui échouent ne pourront pas redémarrer automatiquement, puisque cela nécessite l’intervention de Failover Manager. 

### <a name="failures-of-a-datacenter-or-azure-region"></a>Défaillance d’un centre de données ou d’une région Azure
Dans de rares cas, un centre de données physique peut devenir temporairement indisponible à cause d’une perte d’alimentation ou de connectivité réseau. Dans ce cas, les clusters et services Service Fabric du centre de données ou de la région Azure ne sont plus disponibles. Toutefois, _vos données sont conservées_. Pour les clusters exécutés dans Azure, vous pouvez afficher les mises à jour concernant les pannes sur la [page d’état Azure][azure-status-dashboard]. En cas de destruction partielle ou intégrale d’un centre de données physique (ce qui est très peu probable), les clusters Service Fabric qui y sont hébergés et les services qu’ils contiennent peuvent être perdus. Cela comprend les états non sauvegardés en dehors du centre de données ou de la région.

Il existe deux stratégies pour survivre à la défaillance permanente ou prolongée d’un centre de données ou d’une région. 

1. Exécuter des clusters Service Fabric distincts dans plusieurs régions de ce type et utiliser un mécanisme de basculement et de restauration automatiques entre ces environnements. Ce type de modèle multicluster actif-actif ou actif-passif nécessite du code de gestion et d’opération supplémentaire. Cela nécessite également la coordination des sauvegardes des services d’un centre de données ou d’une région afin qu’ils soient disponibles dans d’autres centres de données ou régions quand l’un d’eux échoue. 
2. Exécuter un cluster Service Fabric qui s’étend sur plusieurs centres de données ou régions. Pour cette méthode, la configuration minimale prise en charge est de trois régions ou centres de données. Il est recommandé d’utiliser cinq régions ou centres de données. Cette configuration nécessite une topologie de cluster plus complexe. Toutefois, l’avantage de ce modèle est que la défaillance d’un centre de données ou d’une région passe de l’état d’incident à celui de défaillance normale. Ces défaillances peuvent être gérées par les mécanismes qui fonctionnent pour les clusters d’une même région. Les domaines d’erreur, les domaines de mise à niveau et les règles de sélection élective de Service Fabric garantissent que les charges de travail sont distribuées de manière à survivre aux défaillances normales. Pour plus d’informations sur les stratégies qui peuvent aider à utiliser les services dans ce type de cluster, lisez cet article sur les [stratégies de sélection élective](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md).

### <a name="random-failures-leading-to-cluster-failures"></a>Défaillances aléatoires entraînant un échec du cluster
Service Fabric utilise le concept de nœud initial. Il s’agit de nœuds qui permettent de maintenir la disponibilité du cluster sous-jacent. Ces nœuds vous aident à garantir que le cluster reste actif en établissant des baux avec d’autres nœuds et en servant de tiebreaker pour certains types de défaillances réseau. Si des défaillances aléatoires suppriment la majorité des nœuds initiaux du cluster et que ceux-ci ne sont pas restaurés, le cluster est automatiquement arrêté. Dans Azure, les nœuds initiaux sont gérés automatiquement. Ils sont distribués sur les domaines d’erreur et de mise à niveau disponibles, et si un nœud initial est supprimé du cluster, un autre est créé à la place. 

Dans les clusters Service Fabric autonomes et Azure, le type de nœud principal est celui qui exécute les nœuds initiaux. Lorsque vous définissez un type de nœud principal, Service Fabric utilise automatiquement le nombre de nœuds fournis en créant jusqu’à 9 nœuds initiaux et 9 réplicas de chaque service système. Si un ensemble de défaillances aléatoires supprime simultanément une majorité de ces réplicas de services système, ceux-ci passent à l’état de perte de quorum, comme décrit plus haut. Si la majorité des nœuds initiaux est perdue, le cluster est arrêté rapidement après.

## <a name="next-steps"></a>Étapes suivantes
- Découvrez comment simuler différentes défaillances à l’aide de [l’infrastructure de testabilité](service-fabric-testability-overview.md)
- Consultez les autres ressources de récupération d’urgence et de haute disponibilité. Microsoft a publié de nombreuses rubriques d’aide sur ces sujets. Même si certains de ces documents font référence à des techniques propres à d’autres produits, ils contiennent un grand nombre de bonnes pratiques générales que vous pouvez appliquer aussi dans le contexte de Service Fabric :
  - [Liste de contrôle de disponibilité](../best-practices-availability-checklist.md)
  - [Exécution d’un exercice de récupération d’urgence](../sql-database/sql-database-disaster-recovery-drills.md)
  - [Récupération d’urgence et haute disponibilité pour les applications Azure][dr-ha-guide]
- En savoir plus sur les [options de prise en charge de Service Fabric](service-fabric-support.md)

<!-- External links -->

[repair-partition-ps]: https://msdn.microsoft.com/library/mt163522.aspx
[azure-status-dashboard]:https://azure.microsoft.com/status/
[azure-regions]: https://azure.microsoft.com/regions/
[dr-ha-guide]: https://msdn.microsoft.com/library/azure/dn251004.aspx


<!-- Images -->

[sfx-cluster-map]: ./media/service-fabric-disaster-recovery/sfx-clustermap.png
