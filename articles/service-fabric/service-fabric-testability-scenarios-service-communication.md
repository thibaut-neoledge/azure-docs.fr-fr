<properties 
   pageTitle="Scénarios de testabilité de Service Fabric : Communication de service" 
   description="La communication service à service constitue un point d’intégration critique d’une application Service Fabric. Cet article aborde les problématiques de conception et les techniques de test." 
   services="service-fabric" 
   documentationCenter=".net" 
   authors="vturecek" 
   manager="timlt" 
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA" 
   ms.date="04/17/2015"
   ms.author="vturecek"/>

# Scénarios de testabilité de Service Fabric : Communication de service

Les microservices et les styles architecturaux orientés services émergent naturellement dans Service Fabric. Dans ces types d’architectures distribuées, les applications de microservices compartimentés sont généralement composées de plusieurs services qui interagissent entre eux. Même dans le cas le plus simple, vous disposez habituellement d’au moins un service Web sans état et d’un service de stockage de données avec état qui communiquent.

La communication de service à service constitue un point d’intégration critique d’une application, dans la mesure où chaque service expose une API distante aux autres services. L’utilisation d’un ensemble de limites d’API impliquant un trafic d’E/S nécessite bien souvent une attention particulière, secondée par de solides méthodes de test et de validation.

Lorsque ces limites de services sont liées au sein d’un système distribué, de nombreux aspects sont à prendre en compte :

 - **Protocole de transfert**. S’agira-t-il du protocole HTTP, pour une interopérabilité améliorée, ou d’un protocole personnalisé binaire favorisant un débit optimal ?
 - **Gestion des erreurs**. Comment les erreurs permanentes et temporaires sont-elles traitées ? Que se passe-t-il lorsqu’un service est déplacé vers un nœud différent ?
 - **Délais d’attente et latence**. Dans les applications multiniveau, comment chaque couche de service traite-t-elle la latence de bout en bout, au sein de la pile jusqu’à l’utilisateur ?

Vous utilisez l’un des composants de communication de service intégrés de Service Fabric ? Vous développez le vôtre ? Pour garantir la résilience de votre application, il est primordial de tester l’interaction entre vos services.

## Où est mon service ?

Les instances de service peuvent se déplacer au fil du temps, en particulier si elles sont configurées avec des mesures de charge dédiées à l’équilibrage personnalisé optimal des ressources. Les mises à niveau, les basculements, les mises à l’échelle et les autres différentes situations ponctuant le cycle de vie d’un système distribué impliquent le déplacement, par Service Fabric, de vos instances de services dans un objectif de disponibilité maximale.

Lors du déplacement de vos services dans le cluster, vos clients et les autres entités de votre organisation doivent envisager deux scénarios d’interaction avec ces derniers :

 + L’instance de service ou le réplica de partition ont subi un déplacement depuis votre dernière interaction. Cette configuration, qui fait partie du cycle de vie normal du service, se produit naturellement au cours de la vie d’une application.
 + L’instance de service ou le réplica de partition est en cours de déplacement. Le basculement d’un service entre deux nœuds se produit très rapidement dans Service Fabric, mais vous pouvez constater un délai de mise à disposition en cas de démarrage lent du composant de communication de votre service.

Pour bénéficier d’un système pleinement fonctionnel, il est nécessaire de gérer ces scénarios de manière appropriée. Pour ce faire, nous devons examiner certaines considérations :

+ Chaque service auquel peut être allouée une connexion présente une *adresse* d’écoute (HTTP, WebSockets, etc.). Lorsqu’une instance ou une partition de service a été déplacée, son point de terminaison d’adresse est modifié (il est déplacé vers un nœud différent, associé à une adresse IP différente). Si vous utilisez des composants de communication intégrés, ils traitent pour vous la nouvelle résolution des adresses de service. 
+ Le temps que l’instance de service redémarre son écouteur, vous pouvez constater une augmentation temporaire de la latence de service, suivant la réactivité du service après son déplacement.
+ L’ensemble des connexions existantes devront être fermées, puis rouvertes lors de la réouverture du service sur un nouveau nœud. Si l’arrêt ou le redémarrage d’un nœud sont correctement gérés, les connexions existantes peuvent être interrompues en douceur.

### Test : Déplacement des instances de service

À l’aide des outils de testabilité de Service Fabric, nous pouvons établir un scénario test afin d’évaluer ces situations dans des contextes différents.

1. Déplacez un réplica principal de service avec état.
 
    Le réplica principal d’une partition de service avec état peut être déplacé pour diverses raisons. Appliquez ce scénario pour cibler le réplica principal d’une partition spécifique et examiner la réaction de vos services à ce déplacement, dans un cadre très strict.

    ```powershell

    PS > Move-ServiceFabricPrimaryReplica -PartitionId 6faa4ffa-521a-44e9-8351-dfca0f7e0466 -ServiceName fabric:/MyApplication/MyService

    ```

2. Arrêtez un nœud.

    Lorsqu’un nœud est arrêté, Service Fabric déplace l’ensemble des instances et partitions de services positionnées sur ce nœud vers l’un des autres nœuds disponibles dans le cluster. Utilisez ce scénario pour tester une configuration où un nœud de votre cluster est perdu et l’ensemble de vos instances et réplicas de service sur ce nœud doivent être déplacés.

    Un nœud peut être arrêté à l’aide de l’applet de commande Stop-ServiceFabricNode PowerShell :

    ```powershell

    PS > Restart-ServiceFabricNode -NodeName Node.1

    ```

    
    


### Disponibilité des services

Service Fabric est une plateforme conçue pour assurer la haute disponibilité de vos services. Toutefois, les problèmes liés à l’infrastructure sous-jacente peuvent entraîner une indisponibilité dans des cas extrêmes. Par conséquent, il est important d’également tester ce scénario.

Les services avec état valorisent un système avec quorum pour répliquer l’état de haute disponibilité. Cela signifie qu’un quorum de réplicas doit être disponible pour l’exécution des opérations d’écriture. Dans de rares cas, comme lors d’une défaillance matérielle étendue, un quorum de réplicas peut être rendu indisponible. Le cas échéant, vous ne pourrez pas exécuter d’opérations d’écriture, mais disposerez des opérations de lecture.

### Test : Écriture de l’indisponibilité des opérations

Grâce aux outils de testabilité de Service Fabric, vous pouvez tester ce type de scénario en injectant une erreur qui entraîne une perte de quorum. Rarement confrontés à ce type de situations, les clients et les entités qui dépendent d’un service avec état doivent néanmoins s’y préparer. Que faire quand les requêtes d’écriture sur le service avec état sont impossibles ? Parallèlement, il est essentiel que le service avec état ait conscience de cette possibilité, et qu’il en fasse part de manière adaptée aux appelants.

Pour provoquer une perte de quorum, utilisez l’applet de commande suivante : Invoke-ServiceFabricPartitionQuorumLoss PowerShell :

```powershell

PS > Invoke-ServiceFabricPartitionQuorumLoss -ServiceName fabric:/Myapplication/MyService -QuorumLossMode PartialQuorumLoss -QuorumLossDurationInSeconds 20

```

Dans cet exemple, nous avons défini `QuorumLossMode` sur `PartialQuorumLoss` afin d’indiquer que nous souhaitons provoquer une perte de quorum sans retirer l’ensemble des réplicas. Ainsi, les opérations de lecture sont toujours possibles. Pour tester ce scénario avec l’intégralité de la partition indisponible, définissez ce commutateur sur `FullQuorumLoss`.

## Étapes suivantes

[En savoir plus sur les actions de testabilité](service-fabric-testability-actions.md)

[En savoir plus sur les scénarios de testabilité](service-fabric-testability-scenarios.md)

<!---HONumber=July15_HO4-->