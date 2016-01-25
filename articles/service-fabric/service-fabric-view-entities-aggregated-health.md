<properties
   pageTitle="Affichage de l’état d’intégrité agrégé des entités Azure Service Fabric | Microsoft Azure"
   description="Explique comment interroger, afficher et évaluer l’état d’intégrité agrégé des entités Azure Service Fabric, via des requêtes d’intégrité et des requêtes générales."
   services="service-fabric"HealthManager
   documentationCenter=".net"
   authors="oanapl"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/23/2015"
   ms.author="oanapl"/>

# Affichage rapports d’intégrité de Service Fabric
Azure Service Fabric propose un [modèle d’intégrité](service-fabric-health-introduction.md) constitué d’entités d’intégrité sur lesquelles les composants système et les agents de surveillance peuvent signaler les conditions locales qu’ils identifient. Le [magasin d’intégrité](service-fabric-health-introduction.md#health-store) agrège toutes les données d’intégrité pour déterminer si les entités sont saines.

Dès le départ, le cluster comprend des rapports d’intégrité envoyés par les composants système. Pour en savoir plus, consultez l’article [Utiliser les rapports d’intégrité du système pour la résolution des problèmes](service-fabric-understand-and-troubleshoot-with-system-health-reports.md).

Service Fabric offre de multiples moyens d’obtenir les données d’intégrité agrégée des entités :

- [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) ou d’autres outils de visualisation

- Requêtes d’intégrité (via PowerShell, l’API ou REST)

- Requêtes générales renvoyant une liste d’entités qui présentent l’intégrité comme l’une de leurs propriétés (via PowerShell, l’API ou REST)

Pour illustrer ces options, utilisons un cluster local présentant cinq nœuds. Mis à part l’application **fabric:/System** (qui existe de façon standard), d’autres applications sont déployées, dont **fabric:/WordCount**. Cette application contient un service avec état configuré avec sept réplicas. Comme il n’existe que cinq nœuds, les composants système affichent un avertissement indiquant que la partition présente une valeur inférieure au nombre cible.

```xml
<Service Name="WordCount.Service">
  <StatefulService ServiceTypeName="WordCount.Service" MinReplicaSetSize="2" TargetReplicaSetSize="7">
    <UniformInt64Partition PartitionCount="1" LowKey="1" HighKey="26" />
  </StatefulService>
</Service>
```

## Intégrité dans Service Fabric Explorer
Service Fabric Explorer procure un aperçu visuel du cluster. Dans l’image ci-dessous, vous pouvez constater que :

- L’application **fabric:/WordCount** apparaît en rouge (erreur), car un événement d’erreur a été signalé par **MyWatchdog** pour la propriété **Availability**.

- L’un de ses services, **fabric:/WordCount/WordCount.Service** apparaît en jaune (avertissement). Comme évoqué plus haut, le service est configuré avec sept réplicas, qui ne peuvent pas être tous placés (puisqu’il n’existe que cinq nœuds). Même si elle n’est pas représentée ici, la partition de service est en jaune, conformément au rapport système. La partition en jaune déclenche le service en jaune.

- Le cluster apparaît en rouge en raison de l’application rouge.

L’évaluation utilise les stratégies par défaut du manifeste de cluster et du manifeste de l’application. Il s’agit de stratégies strictes qui ne tolèrent aucun échec.

Vue du cluster avec Service Fabric Explorer :

![Vue du cluster avec Service Fabric Explorer.][1]

[1]: ./media/service-fabric-view-entities-aggregated-health/servicefabric-explorer-cluster-health.png


> [AZURE.NOTE]En savoir plus sur [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md).

## Requêtes d'intégrité
Service Fabric expose les requêtes d’intégrité pour chacun des [types d’entité](service-fabric-health-introduction.md#health-entities-and-hierarchy) pris en charge. Elles sont accessibles via l’API (les méthodes se trouvent sur **FabricClient.HealthManager**), les applets de commande PowerShell et REST. Ces requêtes renvoient des informations complètes sur l’intégrité de l’entité, notamment sur l’état d’intégrité agrégé, les événements d’intégrité signalés sur l’entité, les états d’intégrité des enfants (le cas échéant) et les évaluations de défaut d’intégrité (le cas échéant).

> [AZURE.NOTE]Une entité d’intégrité est renvoyée à l’utilisateur si elle est intégralement renseignée dans le magasin d’intégrité. Elle doit être active (pas supprimée) et doit disposer d’un rapport système. Ses entités parentes dans la chaîne hiérarchique doivent également disposer de rapports système. Si l’une de ces conditions n’est pas satisfaite, les requêtes d’intégrité renvoient une exception indiquant le non-renvoi de l’entité.

Les requêtes d’intégrité doivent transmettre l’identificateur de l’entité, qui dépend du type d’entité. Elles acceptent des paramètres facultatifs de stratégie de contrôle d’intégrité. Si ceux-ci ne sont pas spécifiés, les [stratégies de contrôle d’intégrité](service-fabric-health-introduction.md#health-policies) du manifeste de cluster ou d’application sont utilisées pour l’évaluation. Les requêtes acceptent également des filtres pour ne renvoyer le cas échéant que des enfants ou des événements partiels.

> [AZURE.NOTE]Les filtres de sortie sont appliqués côté serveur, ce qui réduit la taille de la réponse du message. Nous vous recommandons d’utiliser les filtres de sortie afin de limiter le volume de données renvoyées, plutôt que d’appliquer des filtres côté client.

Les données d’intégrité d’une entité contiennent les informations suivantes :

- L’état agrégé d’intégrité de l’entité. Cette valeur est calculée par le magasin d’intégrité en fonction des rapports d’intégrité de l’entité, de l’état d’intégrité des enfants (le cas échéant) et des stratégies de contrôle d’intégrité. En savoir plus sur l’[évaluation de l’intégrité de l’entité](service-fabric-health-introduction.md#entity-health-evaluation).  

- Les événements d’intégrité sur l’entité.

- La collection des états d’intégrité de l’ensemble des enfants pour les entités pouvant en avoir. Les états d’intégrité contiennent les identificateurs d’entité et l’état d’intégrité agrégé. Pour obtenir les données d’intégrité complètes d’un enfant, sollicitez la requête d’intégrité du type de l’entité enfant et transmettez l’identificateur de l’enfant.

- Les évaluations de défaut d’intégrité qui indiquent le rapport qui a déclenché l’état de l’entité si celle-ci n’est pas saine.

## Obtenir les données d’intégrité du cluster
Cette requête renvoie les données d’intégrité de l’entité du cluster et contient les états d’intégrité des applications et des nœuds (enfants du cluster). Entrée :

- [Facultatif] Mappage de la stratégie de contrôle d’intégrité de l’application avec les stratégies de contrôle d’intégrité utilisées pour remplacer les stratégies du manifeste de l’application.

- [Facultatif] Filtres des événements, des nœuds et des applications qui spécifient les entrées intéressantes qui doivent être retournées dans le résultat (par exemple, uniquement les erreurs ou à la fois les avertissements et les erreurs). Notez que tous les événements, nœuds et applications permettent d’évaluer l’intégrité d’entité agrégée, quel que soit le filtre utilisé.

### API
Pour obtenir les données d’intégrité du cluster, créez un élément **FabricClient** et appelez la méthode [**GetClusterHealthAsync**](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.healthclient.getclusterhealthasync.aspx) sur son élément **HealthManager**.

Le code suivant permet d’obtenir les données d’intégrité du cluster :

```csharp
ClusterHealth clusterHealth = fabricClient.HealthManager.GetClusterHealthAsync().Result;
```

Le code suivant renvoie les données d’intégrité du cluster à l’aide d’une stratégie personnalisée de contrôle d’intégrité du cluster et de filtres dédiés aux nœuds et aux applications. Notez que l’élément **System.Fabric.Description.[ClusterHealthQueryDescription](https://msdn.microsoft.com/library/azure/system.fabric.description.clusterhealthquerydescription.aspx)** est créé ; il contient l’ensemble des données d’entrée.

```csharp
var policy = new ClusterHealthPolicy()
{
    MaxPercentUnhealthyNodes = 20
};
var nodesFilter = new NodeHealthStatesFilter()
{
    HealthStateFilter = (long)(HealthStateFilter.Error | HealthStateFilter.Warning)
};
var applicationsFilter = new ApplicationHealthStatesFilter()
{
    HealthStateFilter = (long)HealthStateFilter.Error
};
var queryDescription = new ClusterHealthQueryDescription()
{
    HealthPolicy = policy,
    ApplicationsFilter = applicationsFilter,
    NodesFilter = nodesFilter,
};
ClusterHealth clusterHealth = fabricClient.HealthManager.GetClusterHealthAsync(queryDescription).Result;
```

### PowerShell
L’applet de commande permettant d’obtenir les données d’intégrité du cluster est **[Get-ServiceFabricClusterHealth](https://msdn.microsoft.com/library/mt125850.aspx)**. Commencez par vous connecter au cluster à l’aide de l’applet de commande **Connect-ServiceFabricCluster**.

L’état du cluster indique cinq nœuds, l’application système et fabric:/WordCount configurés comme ci-dessus.

L’applet de commande suivante permet d’obtenir les données d’intégrité du cluster à l’aide des stratégies de contrôle d’intégrité par défaut. L’état d’intégrité agrégé est l’avertissement, car l’application fabric:/WordCount présente cet état. Notez que les évaluations de défaut d’intégrité fournissent des détails sur les conditions qui ont déclenché l’intégrité agrégée.

```xml
PS C:\> Get-ServiceFabricClusterHealth

AggregatedHealthState   : Warning
UnhealthyEvaluations    :
                          Unhealthy applications: 50% (1/2), MaxPercentUnhealthyApplications=0%.

                          Unhealthy application: ApplicationName='fabric:/WordCount', AggregatedHealthState='Warning'.

                          Unhealthy services: 100% (1/1), ServiceType='WordCount.Service', MaxPercentUnhealthyServices=0%.

                          Unhealthy service: ServiceName='fabric:/WordCount/WordCount.Service', AggregatedHealthState='Warning'.

                          Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                          Unhealthy partition: PartitionId='889909a3-04d6-4a01-97c1-3e9851d77d6c', AggregatedHealthState='Warning'.

                          Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=false.

NodeHealthStates        :
                          NodeName              : Node.4
                          AggregatedHealthState : Ok

                          NodeName              : Node.2
                          AggregatedHealthState : Ok

                          NodeName              : Node.1
                          AggregatedHealthState : Ok

                          NodeName              : Node.5
                          AggregatedHealthState : Ok

                          NodeName              : Node.3
                          AggregatedHealthState : Ok

ApplicationHealthStates :
                          ApplicationName       : fabric:/CalculatorActor
                          AggregatedHealthState : Ok

                          ApplicationName       : fabric:/System
                          AggregatedHealthState : Ok

                          ApplicationName       : fabric:/WordCount
                          AggregatedHealthState : Warning

HealthEvents            : None
```

L’applet de commande PowerShell suivante permet d’obtenir les données d’intégrité du cluster à l’aide d’une stratégie d’application personnalisée. Elle filtre les résultats afin d’identifier uniquement les applications et les nœuds indiquant une erreur ou un avertissement. En conséquence, aucun nœud n’est renvoyé puisqu’ils sont tous sains. Seule l’application fabric:/WordCount respecte le filtre d’applications. Comme la stratégie personnalisée exige de considérer les avertissements comme des erreurs pour l’application fabric:/WordCount, l’application présente l’état d’erreur tout comme le cluster.

```powershell
PS c:> $appHealthPolicy = New-Object -TypeName System.Fabric.Health.ApplicationHealthPolicy
$appHealthPolicy.ConsiderWarningAsError = $true
$appHealthPolicyMap = New-Object -TypeName System.Fabric.Health.ApplicationHealthPolicyMap
$appUri1 = New-Object -TypeName System.Uri -ArgumentList "fabric:/WordCount"
$appHealthPolicyMap.Add($appUri1, $appHealthPolicy)
$warningAndErrorFilter = [System.Fabric.Health.HealthStateFilter]::Warning.value__  + [System.Fabric.Health.HealthStateFilter]::Error.value__
Get-ServiceFabricClusterHealth -ApplicationHealthPolicyMap $appHealthPolicyMap -ApplicationsHealthStateFilter $warningAndErrorFilter -NodesHealthStateFilter $warningAndErrorFilter

AggregatedHealthState   : Error
UnhealthyEvaluations    :
                          Unhealthy applications: 50% (1/2), MaxPercentUnhealthyApplications=0%.

                          Unhealthy application: ApplicationName='fabric:/WordCount', AggregatedHealthState='Error'.

                          Unhealthy services: 100% (1/1), ServiceType='WordCount.Service', MaxPercentUnhealthyServices=0%.

                          Unhealthy service: ServiceName='fabric:/WordCount/WordCount.Service', AggregatedHealthState='Error'.

                          Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                          Unhealthy partition: PartitionId='889909a3-04d6-4a01-97c1-3e9851d77d6c', AggregatedHealthState='Error'.

                          Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=true.


NodeHealthStates        : None
ApplicationHealthStates :
                          ApplicationName       : fabric:/WordCount
                          AggregatedHealthState : Error

HealthEvents            : None

```

## Obtenir les données d’intégrité du nœud
Cette requête renvoie les données d’intégrité d’une entité de nœud et contient les événements d’intégrité signalés sur le nœud. Entrée :

- [Obligatoire] Nom du nœud qui identifie le nœud.

- [Facultatif] Paramètres de la stratégie de contrôle d’intégrité du cluster utilisés pour l’évaluation de l’intégrité.

- [Facultatif] Filtres des événements qui spécifient les entrées intéressantes qui doivent être retournées dans le résultat (par exemple, uniquement les erreurs ou à la fois les avertissements et les erreurs). Notez que tous les événements permettent d’évaluer l’intégrité d’entité agrégée, quel que soit le filtre utilisé.

### API
Pour obtenir les données d’intégrité du nœud via l’API, créez un élément FabricClient et appelez la méthode **GetNodeHealthAsync** sur son élément HealthManager.

Le code suivant permet d’obtenir les données d’intégrité du nœud spécifié :

```csharp
NodeHealth nodeHealth = fabricClient.HealthManager.GetNodeHealthAsync(nodeName).Result;
```

Le code suivant permet d’obtenir les données d’intégrité du nœud spécifié, et transmet un filtre d’événements et la stratégie personnalisée via **System.Fabric.Description.[NodeHealthQueryDescription](https://msdn.microsoft.com/library/azure/system.fabric.description.nodehealthquerydescription.aspx)** :

```csharp
var queryDescription = new NodeHealthQueryDescription(nodeName)
{
    HealthPolicy = new ClusterHealthPolicy() {  ConsiderWarningAsError = true },
    EventsFilter = new HealthEventsFilter() { HealthStateFilter = (long)HealthStateFilter.Warning },
};

NodeHealth nodeHealth = fabricClient.HealthManager.GetNodeHealthAsync(queryDescription).Result;
```

### PowerShell
L’applet de commande permettant d’obtenir les données d’intégrité du nœud est **Get-ServiceFabricNodeHealth**. Commencez par vous connecter au cluster à l’aide de l’applet de commande **Connect-ServiceFabricCluster**. L’applet de commande suivante permet d’obtenir les données d’intégrité du nœud à l’aide des stratégies de contrôle d’intégrité par défaut :

```powershell
PS C:\> Get-ServiceFabricNodeHealth -NodeName Node.1

NodeName              : Node.1
AggregatedHealthState : Ok
HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 5
                        SentAt                : 4/21/2015 8:01:17 AM
                        ReceivedAt            : 4/21/2015 8:02:12 AM
                        TTL                   : Infinite
                        Description           : Fabric node is up.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/21/2015 8:02:12 AM
```

L’applet de commande suivante permet d’obtenir les données d’intégrité de tous les nœuds du cluster :

```powershell
PS C:\> Get-ServiceFabricNode | Get-ServiceFabricNodeHealth | select NodeName, AggregatedHealthState | ft -AutoSize

NodeName AggregatedHealthState
-------- ---------------------
Node.4                      Ok
Node.2                      Ok
Node.1                      Ok
Node.5                      Ok
Node.3                      Ok
```

## Obtenir les données d’intégrité des applications
Cette requête renvoie les données d’intégrité d’une entité d’application. Elle contient les états d’intégrité de l’application déployée et des enfants du service. Entrée :

- [Obligatoire] Nom de l’application (URI) qui identifie l’application.

- [Facultatif] Stratégie de contrôle d’intégrité d’application remplaçant les stratégies du manifeste d’application.

- [Facultatif] Filtres des événements, des services et des applications déployées qui spécifient les entrées intéressantes qui doivent être retournées dans le résultat (par exemple, uniquement les erreurs ou à la fois les avertissements et les erreurs). Notez que tous les événements, services et applications déployées permettent d’évaluer l’intégrité d’entité agrégée, quel que soit le filtre utilisé.

### API
Pour obtenir les données d’intégrité de l’application, créez un élément FabricClient et appelez la méthode **GetApplicationHealthAsync** sur son élément HealthManager.

Le code suivant permet d’obtenir les données d’intégrité de l’application spécifiée (URI) :

```csharp
ApplicationHealth applicationHealth = fabricClient.HealthManager.GetApplicationHealthAsync(applicationName).Result;
```

Le code suivant permet d’obtenir les données d’intégrité de l’application spécifiée (URI), avec les filtres et les stratégies personnalisées spécifiés via **System.Fabric.Description.ApplicationHealthQueryDescription**.

```csharp
HealthStateFilter warningAndErrors = HealthStateFilter.Error | HealthStateFilter.Warning;
var serviceTypePolicy = new ServiceTypeHealthPolicy()
{
    MaxPercentUnhealthyPartitionsPerService = 0,
    MaxPercentUnhealthyReplicasPerPartition = 5,
    MaxPercentUnhealthyServices = 0,
};
var policy = new ApplicationHealthPolicy()
{
    ConsiderWarningAsError = false,
    DefaultServiceTypeHealthPolicy = serviceTypePolicy,
    MaxPercentUnhealthyDeployedApplications = 0,
};

var queryDescription = new ApplicationHealthQueryDescription(applicationName)
{
    HealthPolicy = policy,
    EventsFilter = new HealthEventsFilter() { HealthStateFilter = (long)warningAndErrors },
    ServicesFilter = new ServiceHealthStatesFilter() { HealthStateFilter = (long)warningAndErrors },
    DeployedApplicationsFilter = new DeployedApplicationHealthStatesFilter() { HealthStateFilter = (long)warningAndErrors },
};

ApplicationHealth applicationHealth = fabricClient.HealthManager.GetApplicationHealthAsync(queryDescription).Result;
```

### PowerShell
L’applet de commande permettant d’obtenir les données d’intégrité de l’application est **Get-ServiceFabricApplicationHealth**. Commencez par vous connecter au cluster à l’aide de l’applet de commande **Connect-ServiceFabricCluster**.

L’applet de commande suivante renvoie les données d’intégrité de l’application fabric:/WordCount :

```powershell
PS c:> Get-ServiceFabricApplicationHealth fabric:/WordCount

ApplicationName                 : fabric:/WordCount
AggregatedHealthState           : Warning
UnhealthyEvaluations            :
                                  Unhealthy services: 100% (1/1), ServiceType='WordCount.Service',
                                  MaxPercentUnhealthyServices=0%.

                                  Unhealthy service: ServiceName='fabric:/WordCount/WordCount.Service',
                                  AggregatedHealthState='Warning'.

                                  Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                                  Unhealthy partition: PartitionId='325da69f-16d4-4418-9c30-1feaa40a072c',
                                  AggregatedHealthState='Warning'.

                                  Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning',
                                  ConsiderWarningAsError=false.

ServiceHealthStates             :
                                  ServiceName           : fabric:/WordCount/WordCount.WebService
                                  AggregatedHealthState : Ok

                                  ServiceName           : fabric:/WordCount/WordCount.Service
                                  AggregatedHealthState : Warning

DeployedApplicationHealthStates :
                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : Node.2
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : Node.5
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : Node.4
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : Node.1
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : Node.3
                                  AggregatedHealthState : Ok

HealthEvents                    :
                                  SourceId              : System.CM
                                  Property              : State
                                  HealthState           : Ok
                                  SequenceNumber        : 2456
                                  SentAt                : 4/20/2015 9:57:06 PM
                                  ReceivedAt            : 4/20/2015 9:57:06 PM
                                  TTL                   : Infinite
                                  Description           : Application has been created.
                                  RemoveWhenExpired     : False
                                  IsExpired             : False
                                  Transitions           : ->Ok = 4/20/2015 9:57:06 PM
```

L’applet de commande PowerShell suivante transmet des stratégies personnalisées et filtre les enfants et les événements.

```powershell
PS C:\> $errorFilter = [System.Fabric.Health.HealthStateFilter]::Error.value__
Get-ServiceFabricApplicationHealth -ApplicationName fabric:/WordCount -ConsiderWarningAsError $true -ServicesHealthStateFilter $errorFilter -EventsHealthStateFilter $errorFilter -DeployedApplicationsHealthStateFilter $errorFilter

ApplicationName                 : fabric:/WordCount
AggregatedHealthState           : Error
UnhealthyEvaluations            :
                                  Unhealthy services: 100% (1/1), ServiceType='WordCount.Service', MaxPercentUnhealthyServices=0%.

                                  Unhealthy service: ServiceName='fabric:/WordCount/WordCount.Service', AggregatedHealthState='Error'.

                                  Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                                  Unhealthy partition: PartitionId='8f82daff-eb68-4fd9-b631-7a37629e08c0', AggregatedHealthState='Error'.

                                  Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=true.

ServiceHealthStates             :
                                  ServiceName           : fabric:/WordCount/WordCount.Service
                                  AggregatedHealthState : Error

DeployedApplicationHealthStates : None
HealthEvents                    : None
```

## Obtenir l’état d’intégrité du service
Cette requête renvoie les données d’intégrité d’une entité de service. Elle contient les états d’intégrité des partitions. Entrée :

- [Obligatoire] Nom (URI) qui identifie le service.
- [Facultatif] Stratégie de contrôle d’intégrité d’application remplaçant la stratégie du manifeste d’application.
- [Facultatif] Filtres des événements et des partitions qui spécifient les entrées intéressantes qui doivent être retournées dans le résultat (par exemple, uniquement les erreurs ou à la fois les avertissements et les erreurs). Notez que tous les événements et les partitions permettent d’évaluer l’intégrité d’entité agrégée, quel que soit le filtre utilisé.

### API
Pour obtenir les données d’intégrité de service via l’API, créez un élément FabricClient et appelez la méthode **GetServiceHealthAsync** sur son élément HeathManager.

L’exemple suivant permet d’obtenir les données d’intégrité d’un service présentant un nom (URI) spécifique :

```charp
ServiceHealth serviceHealth = fabricClient.HealthManager.GetServiceHealthAsync(serviceName).Result;
```

Le code suivant permet d’obtenir les données d’intégrité du service présentant le nom (URI) spécifique, en spécifiant des filtres et une stratégie personnalisée via System.Fabric.Description.ServiceHealthQueryDescription :

```csharp
var queryDescription = new ServiceHealthQueryDescription(serviceName)
{
    EventsFilter = new HealthEventsFilter() { HealthStateFilter = (long)HealthStateFilter.All },
    PartitionsFilter = new PartitionHealthStatesFilter() { HealthStateFilter = (long)HealthStateFilter.Error },
};

ServiceHealth serviceHealth = fabricClient.HealthManager.GetServiceHealthAsync(queryDescription).Result;
```

### PowerShell
L’applet de commande permettant d’obtenir les données d’intégrité du service est **Get-ServiceFabricServiceHealth**. Commencez par vous connecter au cluster à l’aide de l’applet de commande **Connect-ServiceFabricCluster**.

L’applet de commande suivante permet d’obtenir les données d’intégrité du service à l’aide des stratégies de contrôle d’intégrité par défaut :

```powershell
PS C:\> Get-ServiceFabricServiceHealth -ServiceName fabric:/WordCount/WordCount.Service


ServiceName           : fabric:/WordCount/WordCount.Service
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                        Unhealthy partition: PartitionId='8f82daff-eb68-4fd9-b631-7a37629e08c0', AggregatedHealthState='Warning'.

                        Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=false.

PartitionHealthStates :
                        PartitionId           : 8f82daff-eb68-4fd9-b631-7a37629e08c0
                        AggregatedHealthState : Warning

HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 3
                        SentAt                : 4/20/2015 10:12:29 PM
                        ReceivedAt            : 4/20/2015 10:12:33 PM
                        TTL                   : Infinite
                        Description           : Service has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/20/2015 10:12:33 PM
```

## Obtenir l'intégrité de la partition
Cette requête renvoie les données d’intégrité d’une entité de partition. Elle contient les états d’intégrité des réplicas. Entrée :

- [Obligatoire] ID de partition (GUID) qui identifie la partition.

- [Facultatif] Stratégie de contrôle d’intégrité d’application remplaçant la stratégie du manifeste d’application.

- [Facultatif] Filtres des événements et des réplicas qui spécifient les entrées intéressantes qui doivent être retournées dans le résultat (par exemple, uniquement les erreurs ou à la fois les avertissements et les erreurs). Notez que tous les événements et les réplicas permettent d’évaluer l’intégrité d’entité agrégée, quel que soit le filtre utilisé.

### API
Pour obtenir les données d’intégrité de partition via l’API, créez un élément FabricClient et appelez la méthode **GetPartitionHealthAsync** sur son élément HealthManager. Pour spécifier des paramètres facultatifs, créez **System.Fabric.Description.PartitionHealthQueryDescription**.

```csharp
PartitionHealth partitionHealth = fabricClient.HealthManager.GetPartitionHealthAsync(partitionId).Result;
```

### PowerShell
L’applet de commande permettant d’obtenir les données d’intégrité de partition est **Get-ServiceFabricPartitionHealth**. Commencez par vous connecter au cluster à l’aide de l’applet de commande **Connect-ServiceFabricCluster**.

L’applet de commande suivante permet d’obtenir les données d’intégrité de l’ensemble des partitions du service de statistiques :

```powershell
PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCount.Service | Get-ServiceFabricPartitionHealth

PartitionId           : 8f82daff-eb68-4fd9-b631-7a37629e08c0
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=false.

ReplicaHealthStates   :
                        ReplicaId             : 130740415594605870
                        AggregatedHealthState : Ok

                        ReplicaId             : 130740415502123433
                        AggregatedHealthState : Ok

                        ReplicaId             : 130740415594605867
                        AggregatedHealthState : Ok

                        ReplicaId             : 130740415594605869
                        AggregatedHealthState : Ok

                        ReplicaId             : 130740415594605868
                        AggregatedHealthState : Ok

HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Warning
                        SequenceNumber        : 39
                        SentAt                : 4/20/2015 10:12:59 PM
                        ReceivedAt            : 4/20/2015 10:13:03 PM
                        TTL                   : Infinite
                        Description           : Partition is below target replica or instance count.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Ok->Warning = 4/20/2015 10:13:03 PM
```

## Obtenir les données d’intégrité des réplicas
Cette requête renvoie les données d’intégrité d’un réplica. Entrée :

- [Obligatoire] ID de partition (GUID) et ID de réplica qui identifient le réplica.

- [Facultatif] Paramètres de stratégie de contrôle d’intégrité d’application remplaçant les stratégies de manifeste d’application.

- [Facultatif] Filtres des événements qui spécifient les entrées intéressantes qui doivent être retournées dans le résultat (par exemple, uniquement les erreurs ou à la fois les avertissements et les erreurs). Notez que tous les événements permettent d’évaluer l’intégrité d’entité agrégée, quel que soit le filtre utilisé.

### API
Pour obtenir les données d’intégrité de réplica via l’API, créez un élément FabricClient et appelez la méthode **GetReplicaHealthAsync** sur son élément HealthManager. Pour spécifier des paramètres avancés, utilisez **System.Fabric.Description.ReplicaHealthQueryDescription**.

```csharp
ReplicaHealth replicaHealth = fabricClient.HealthManager.GetReplicaHealthAsync(partitionId, replicaId).Result;
```

### PowerShell
L’applet de commande permettant d’obtenir les données d’intégrité du réplica est **Get-ServiceFabricReplicaHealth**. Commencez par vous connecter au cluster à l’aide de l’applet de commande **Connect-ServiceFabricCluster**.

L’applet de commande suivante permet d’obtenir les données d’intégrité du réplica principal de l’ensemble des partitions du service :

```powershell
PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCount.Service | Get-ServiceFabricReplica | where {$_.ReplicaRole -eq "Primary"} | Get-ServiceFabricReplicaHealth

PartitionId           : 8f82daff-eb68-4fd9-b631-7a37629e08c0
ReplicaId             : 130740415502123433
AggregatedHealthState : Ok
HealthEvents          :
                        SourceId              : System.RA
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 130740415502802942
                        SentAt                : 4/20/2015 10:12:30 PM
                        ReceivedAt            : 4/20/2015 10:12:34 PM
                        TTL                   : Infinite
                        Description           : Replica has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/20/2015 10:12:34 PM
```

## Obtenir les données d’intégrité des applications déployées
Cette requête renvoie les données d’intégrité d’une application déployée sur une entité de nœud. Elle contient les états d’intégrité du package de services déployé. Entrée :

- [Obligatoire] Nom d’application (URI) et nom de nœud (chaîne) qui identifient l’application déployée.

- [Facultatif] Stratégie de contrôle d’intégrité d’application remplaçant les stratégies du manifeste d’application.

- [Facultatif] Filtres des événements et des packages de services déployés qui spécifient les entrées intéressantes qui doivent être retournées dans le résultat (par exemple, uniquement les erreurs ou à la fois les avertissements et les erreurs). Notez que tous les événements et packages de services déployés permettent d’évaluer l’intégrité d’entité agrégée, quel que soit le filtre utilisé.

### API
Pour obtenir les données d’intégrité d’une application déployée sur un nœud via l’API, créez un élément FabricClient et appelez la méthode **GetDeployedApplicationHealthAsync** sur son élément HealthManager. Pour spécifier des paramètres facultatifs, utilisez **System.Fabric.Description.DeployedApplicationHealthQueryDescription**.

```csharp
DeployedApplicationHealth health = fabricClient.HealthManager.GetDeployedApplicationHealthAsync(
    new DeployedApplicationHealthQueryDescription(applicationName, nodeName)).Result;
```

### PowerShell
L’applet de commande permettant d’obtenir les données d’intégrité des applications déployées est **Get-ServiceFabricDeployedApplicationHealth**. Commencez par vous connecter au cluster à l’aide de l’applet de commande **Connect-ServiceFabricCluster**. Pour connaître l’emplacement de déploiement d’une application, exécutez **Get-ServiceFabricApplicationHealth** et visualisez les enfants de l’application déployée.

L’applet de commande suivante permet d’obtenir les données d’intégrité de l’application fabric:/WordCount déployée sur Node.1.

```powershell
PS C:\> Get-ServiceFabricDeployedApplicationHealth -ApplicationName fabric:/WordCount -NodeName Node.1
ApplicationName                    : fabric:/WordCount
NodeName                           : Node.1
AggregatedHealthState              : Ok
DeployedServicePackageHealthStates :
                                     ServiceManifestName   : WordCount.WebService
                                     NodeName              : Node.1
                                     AggregatedHealthState : Ok

                                     ServiceManifestName   : WordCount.Service
                                     NodeName              : Node.1
                                     AggregatedHealthState : Ok

HealthEvents                       :
                                     SourceId              : System.Hosting
                                     Property              : Activation
                                     HealthState           : Ok
                                     SequenceNumber        : 130740415502842941
                                     SentAt                : 4/20/2015 10:12:30 PM
                                     ReceivedAt            : 4/20/2015 10:12:34 PM
                                     TTL                   : Infinite
                                     Description           : The application was activated successfully.
                                     RemoveWhenExpired     : False
                                     IsExpired             : False
                                     Transitions           : ->Ok = 4/20/2015 10:12:34 PM
```

## Obtenir les données d’intégrité d’un package de services déployé
Cette requête renvoie les données d’intégrité d’une entité de package de services déployé. Entrée :

- [Obligatoire] Nom d’application (URI), nom de nœud (chaîne) et nom de manifeste de service (chaîne) qui identifient le package de services déployé.

- [Facultatif] Stratégie de contrôle d’intégrité d’application remplaçant la stratégie du manifeste d’application.

- [Facultatif] Filtres des événements qui spécifient les entrées intéressantes qui doivent être retournées dans le résultat (par exemple, uniquement les erreurs ou à la fois les avertissements et les erreurs). Notez que tous les événements permettent d’évaluer l’intégrité d’entité agrégée, quel que soit le filtre utilisé.

### API
Pour obtenir les données d’intégrité d’un package de services déployé via l’API, créez un élément FabricClient et appelez la méthode **GetDeployedServicePackageHealthAsync** sur son élément HealthManager.

```csharp
DeployedServicePackageHealth health = fabricClient.HealthManager.GetDeployedServicePackageHealthAsync(
    new DeployedServicePackageHealthQueryDescription(applicationName, nodeName, serviceManifestName)).Result;
```

### PowerShell
L’applet de commande permettant d’obtenir les données d’intégrité du package de services déployé est **Get-ServiceFabricDeployedServicePackageHealth**. Commencez par vous connecter au cluster à l’aide de l’applet de commande **Connect-ServiceFabricCluster**. Pour connaître l’emplacement de déploiement d’une application, exécutez **Get-ServiceFabricApplicationHealth** et visualisez les applications déployées. Pour connaître les packages de services d’une application, consultez les enfants du package de services déployé dans la sortie de **Get-ServiceFabricDeployedApplicationHealth**.

L’applet de commande suivante permet d’obtenir les données d’intégrité du package de services **WordCount.Service** de l’application fabric:/WordCount déployée sur Node.1. L’entité présente des rapports **System.Hosting** indiquant la réussite de l’activation du package de services et du point d’entrée ainsi que la réussite de l’inscription du type de service.

```powershell
PS C:\> Get-ServiceFabricDeployedApplication -ApplicationName fabric:/WordCount -NodeName Node.1 | Get-ServiceFabricDeployedServicePackageHealth -ServiceManifestName WordCount.Service

ApplicationName       : fabric:/WordCount
ServiceManifestName   : WordCount.Service
NodeName              : Node.1
AggregatedHealthState : Ok
HealthEvents          :
                        SourceId              : System.Hosting
                        Property              : Activation
                        HealthState           : Ok
                        SequenceNumber        : 130740415506383060
                        SentAt                : 4/20/2015 10:12:30 PM
                        ReceivedAt            : 4/20/2015 10:12:34 PM
                        TTL                   : Infinite
                        Description           : The ServicePackage was activated successfully.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/20/2015 10:12:34 PM

                        SourceId              : System.Hosting
                        Property              : CodePackageActivation:Code:EntryPoint
                        HealthState           : Ok
                        SequenceNumber        : 130740415506543054
                        SentAt                : 4/20/2015 10:12:30 PM
                        ReceivedAt            : 4/20/2015 10:12:34 PM
                        TTL                   : Infinite
                        Description           : The CodePackage was activated successfully.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/20/2015 10:12:34 PM

                        SourceId              : System.Hosting
                        Property              : ServiceTypeRegistration:WordCount.Service
                        HealthState           : Ok
                        SequenceNumber        : 130740415520193499
                        SentAt                : 4/20/2015 10:12:32 PM
                        ReceivedAt            : 4/20/2015 10:12:34 PM
                        TTL                   : Infinite
                        Description           : The ServiceType was registered successfully.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/20/2015 10:12:34 PM
```

## Requêtes générales
Les requêtes générales renvoient la liste des entités Service Fabric d’un type spécifié. Elles sont exposées via l’API (méthodes sur **FabricClient.QueryManager**), les applets de commande PowerShell et REST. Ces requêtes agrègent les sous-requêtes de plusieurs composants. L’un d’eux est le [magasin d’intégrité](service-fabric-health-introduction.md#health-store), qui renseigne l’état d’intégrité agrégé pour chaque résultat de requête.

> [AZURE.NOTE]Les requêtes générales renvoient l’état d’intégrité agrégé de l’entité et ne contiennent pas de données d’intégrité enrichies. Si une entité n’est pas saine, vous pouvez recourir à des requêtes d’intégrité afin d’obtenir l’ensemble des informations sur son intégrité, comme les événements, l’état d’intégrité des enfants et les évaluations de défaut d’intégrité.

Si les requêtes générales renvoient un état d’intégrité inconnu pour une entité, il se peut que le magasin d’intégrité ne dispose pas des données complètes sur l’entité. Il est également possible qu’une sous-requête adressée au magasin d’intégrité ait échoué (par exemple, en cas d’erreur de communication ou de limitation du magasin d’intégrité). Recourez à une requête d’intégrité sur l’entité. Si la sous-requête a rencontré des erreurs temporaires telles que des problèmes de réseau, cette requête de suivi peut aboutir. Elle peut également vous donner plus de détails du magasin d’intégrité sur la raison pour laquelle l’entité n’est pas exposée.

Les requêtes contenant le paramètre **HealthState** pour les entités sont les suivantes :

- Liste de nœuds : renvoie la liste de nœuds du cluster.
  - API : FabricClient.QueryManager.GetNodeListAsync
  - Powershell : Get-ServiceFabricNode
- Liste d’applications : renvoie la liste des applications du cluster.
  - API : FabricClient.QueryManager.GetApplicationListAsync
  - Powershell : Get-ServiceFabricApplication
- Liste de services : renvoie la liste des services d’une application.
  - API : FabricClient.QueryManager.GetServiceListAsync
  - Powershell : Get-ServiceFabricService
- Liste de partitions : renvoie la liste des partitions d’un service.
  - API : FabricClient.QueryManager.GetPartitionListAsync
  - PowerShell : Get-ServiceFabricPartition
- Liste de réplicas : renvoie la liste des réplicas d’une partition.
  - API : FabricClient.QueryManager.GetReplicaListAsync
  - PowerShell : Get-ServiceFabricReplica
- Liste des applications déployées : renvoie la liste des applications déployées dans un nœud.
  - API : FabricClient.QueryManager.GetDeployedApplicationListAsync
  - PowerShell : Get-ServiceFabricDeployedApplication
- Liste des packages de services déployés : renvoie la liste des packages de services d’une application déployée.
  - API : FabricClient.QueryManager.GetDeployedServicePackageListAsync
  - PowerShell : Get-ServiceFabricDeployedApplication

### Exemples

La commande suivante permet d’obtenir les applications défectueuses du cluster :

```csharp
var applications = fabricClient.QueryManager.GetApplicationListAsync().Result.Where(
  app => app.HealthState == HealthState.Error);
```

L’applet de commande suivante permet d’obtenir les détails de l’application fabric:/WordCount. Notez l’état d’intégrité d’avertissement.

```powershell
PS C:\> Get-ServiceFabricApplication -ApplicationName fabric:/WordCount

ApplicationName        : fabric:/WordCount
ApplicationTypeName    : WordCount
ApplicationTypeVersion : 1.0.0.0
ApplicationStatus      : Ready
HealthState            : Warning
ApplicationParameters  : { "_WFDebugParams_" = "[{"ServiceManifestName":"WordCount.WebService","CodePackageName":"Code","EntryPointType":"Main"}]" }
```

L’applet de commande suivante permet d’obtenir les services présentant un état d’intégrité d’avertissement :

```powershell
PS C:\> Get-ServiceFabricApplication | Get-ServiceFabricService | where {$_.HealthState -eq "Warning"}

ServiceName            : fabric:/WordCount/WordCount.Service
ServiceKind            : Stateful
ServiceTypeName        : WordCount.Service
IsServiceGroup         : False
ServiceManifestVersion : 1.0
HasPersistedState      : True
ServiceStatus          : Active
HealthState            : Warning
```

## Mises à niveau d’applications et de clusters
Au cours d’une mise à niveau surveillée d’un cluster et d’une application, Service Fabric vérifie les données d’intégrité afin de garantir le maintien de l’état d’intégrité. Si une entité n’est pas saine au vu de l’évaluation effectuée à l’aide des stratégies de contrôle d’intégrité configurées, la mise à niveau applique des stratégies qui lui sont propres pour déterminer l’action ultérieure. La mise à niveau peut être interrompue pour permettre une intervention de l’utilisateur (par exemple la correction des conditions d’erreur ou la modification des stratégies), ou elle peut automatiquement restaurer la version correcte précédente.

Pendant la mise à niveau d’un *cluster*, vous pouvez obtenir son statut de mise à niveau. Cela inclut les évaluations de défaut d’intégrité, qui désignent les éléments qui ne sont pas sains dans le cluster. Si la mise à niveau est annulée en raison de problèmes d’intégrité, son statut conserve les derniers motifs de défaut d’intégrité. Ce faisant, les administrateurs peuvent rechercher la cause du problème.

De la même manière, durant la mise à niveau d’une *application*, des évaluations de défaut d’intégrité sont contenues dans le statut de mise à niveau de l’application.

Le code suivant représente l’état de mise à niveau d’une application fabric:/WordCount modifiée. Un agent de surveillance a signalé une erreur sur l’un de ses réplicas. La mise à niveau est annulée en raison du non-respect des contrôles d’intégrité.

```powershell
PS C:\> Get-ServiceFabricApplicationUpgrade fabric:/WordCount

ApplicationName               : fabric:/WordCount
ApplicationTypeName           : WordCount
TargetApplicationTypeVersion  : 1.0.0.0
ApplicationParameters         : {}
StartTimestampUtc             : 4/21/2015 5:23:26 PM
FailureTimestampUtc           : 4/21/2015 5:23:37 PM
FailureReason                 : HealthCheck
UpgradeState                  : RollingBackInProgress
UpgradeDuration               : 00:00:23
CurrentUpgradeDomainDuration  : 00:00:00
CurrentUpgradeDomainProgress  : UD1

                                NodeName            : Node1
                                UpgradePhase        : Upgrading

                                NodeName            : Node2
                                UpgradePhase        : Upgrading

                                NodeName            : Node3
                                UpgradePhase        : PreUpgradeSafetyCheck
                                PendingSafetyChecks :
                                EnsurePartitionQuorum - PartitionId: 30db5be6-4e20-4698-8185-4bd7ca744020
NextUpgradeDomain             : UD2
UpgradeDomainsStatus          : { "UD1" = "Completed";
                                "UD2" = "Pending";
                                "UD3" = "Pending";
                                "UD4" = "Pending" }
UnhealthyEvaluations          :
                                Unhealthy services: 100% (1/1), ServiceType='WordCount.Service', MaxPercentUnhealthyServices=0%.

                                Unhealthy service: ServiceName='fabric:/WordCount/WordCount.Service', AggregatedHealthState='Error'.

                                Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                                Unhealthy partition: PartitionId='30db5be6-4e20-4698-8185-4bd7ca744020', AggregatedHealthState='Error'.

                                Unhealthy replicas: 16% (1/6), MaxPercentUnhealthyReplicasPerPartition=0%.

                                Unhealthy replica: PartitionId='30db5be6-4e20-4698-8185-4bd7ca744020', ReplicaOrInstanceId='130741105362491906', AggregatedHealthState='Error'.

                                Error event: SourceId='DiskWatcher', Property='Disk'.

UpgradeKind                   : Rolling
RollingUpgradeMode            : UnmonitoredAuto
ForceRestart                  : False
UpgradeReplicaSetCheckTimeout : 00:15:00
```

En savoir plus sur la [Mise à niveau des applications Service Fabric](service-fabric-application-upgrade.md).

## Résoudre les problèmes à l’aide des évaluations d’intégrité
Chaque fois qu’un problème est identifié dans le cluster ou dans une application, examinez l’état d’intégrité du cluster ou de l’application afin d’isoler le problème. Les évaluations de défaut d’intégrité fournissent des détails sur les raisons de l’état actuel de défaut d’intégrité. Si vous le souhaitez, vous pouvez descendre dans la hiérarchie jusqu’aux entités enfants non saines afin d’identifier l’origine du problème.

## Étapes suivantes
[Utiliser les rapports d’intégrité du système pour la résolution des problèmes](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)

[Ajout de rapports d’intégrité Service Fabric personnalisés](service-fabric-report-health.md)

[Surveiller et diagnostiquer les services localement](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Mise à niveau des applications Service Fabric](service-fabric-application-upgrade.md)

<!---HONumber=AcomDC_0114_2016-->