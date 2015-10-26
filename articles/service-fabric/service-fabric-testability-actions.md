<properties
   pageTitle="Action de testabilité | Microsoft Azure"
   description="Cet article présente les actions de testabilité de Microsoft Azure Service Fabric."
   services="service-fabric"
   documentationCenter=".net"
   authors="heeldin"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/31/2015"
   ms.author="heeldin;motanv"/>

# Actions de testabilité
Pour simuler une infrastructure non fiable, Service Fabric procure aux développeurs des moyens d’intégrer des défaillances et des transitions d’état réalistes. Elles sont exposées en tant qu’actions de testabilité. Les actions sont les API de bas niveau provoquant l’injection des erreurs, la transition entre les états ou la validation. En combinant ces actions, un développeur de service est en mesure d’écrire des scénarios de test complets pour vos services.

Service Fabric fournit en standard des scénarios courants de test, constitués de ces actions. Il est hautement recommandé de recourir à ces scénarios intégrés, qui sont soigneusement choisis pour l’évaluation des transitions d’état et des défaillances courantes. Toutefois, vous pouvez solliciter certaines actions pour élaborer des scénarios de test plus complets et adaptés à votre application.

L’implémentation C# de ces actions est accessible sur l’assembly System.Fabric.Testability.dll. Le module PowerShell de testabilité est disponible sur l’assembly Microsoft.ServiceFabric.Testability.Powershell.dll. Pour une utilisation simplifiée, le module PowerShell ServiceFabricTestability est installé avec le runtime.

## Actions d’erreurs avec et sans perte de données
Les actions de testabilité sont répertoriées en deux groupes principaux :

* Erreurs avec perte de données : ces erreurs simulent des défaillances comme les redémarrages de machines ou les incidents de processus. Dans de tels cas, le contexte d’exécution du processus s’interrompt abruptement. Ici, aucun nettoyage d’état ne peut être exécuté avant le redémarrage de l’application.

* Erreurs sans perte de données : ces erreurs simulent des actions comme des déplacements ou des abandons de réplicas provoqués par l’équilibrage de la charge. Le cas échéant, le service reçoit une notification de fermeture et peut nettoyer l’état avant de quitter.

Pour une qualité améliorée de validation, exécutez le service et la charge de travail métier pendant l’introduction des erreurs avec et sans perte de données. Les erreurs avec perte de données enrichissent les scénarios où le service s’interrompt soudainement au milieu d’un flux de travail. Cela permet de tester le chemin de récupération, une fois que le réplica de service est restauré par Service Fabric. Ici, la cohérence des données est examinée, et vous évaluez si l’état du service est stabilisé efficacement après les défaillances. L’autre ensemble de défaillance (erreurs sans perte de données) évalue la réaction du service aux déplacements des réplicas par Service Fabric. Cela permet de tester le traitement de l’annulation dans la méthode RunAsync. Le service doit vérifier le jeton d’annulation défini, enregistrer son état de manière appropriée et quitter la méthode RunAsync.

## Liste des actions de testabilité

| Actions | Description | API gérée | Applet de commande PowerShell | Erreurs sans/avec perte de données |
|---------|-------------|-------------|-------------------|------------------------------|
|CleanTestState| Supprime l’ensemble des états de test du cluster en cas d’interruption inappropriée du pilote test. | CleanTestStateAsync | Remove-ServiceFabricTestState | Non applicable |
| InvokeDataLoss | Provoque une perte de données dans une partition de service. | InvokeDataLossAsync | Invoke-ServiceFabricPartitionDataLoss | Sans perte de données |
| InvokeQuorumLoss | Entraîne une perte de quorum dans une partition considérée de service sans état. | InvokeQuorumLossAsync | Invoke-ServiceFabricQuorumLoss | Sans perte de données |
| MovePrimary | Déplace le réplica principal spécifié d’un service avec état sur le nœud de cluster spécifié. | MovePrimaryAsync | Move-ServiceFabricPrimaryReplica | Sans perte de données |
| MoveSecondary | Déplace le réplica secondaire actuel d’un service avec état sur un nœud de cluster différent. | MoveSecondaryAsync | Move-ServiceFabricSecondaryReplica | Sans perte de données |
| RemoveReplica | Simule une défaillance de réplica en supprimant un réplica d’un cluster. Cette action ferme le réplica et entraîne le passage au rôle « None », ce qui supprime l’intégralité de l’état du cluster. | RemoveReplicaAsync | Remove-ServiceFabricReplica | Sans perte de données |
| RestartDeployedCodePackage | Simule une défaillance de processus de package de code en redémarrant un package de code déployé sur un nœud de cluster. Cela interrompt le processus de package de code qui redémarrera l’ensemble des réplicas du service utilisateur hébergés dans ce processus. | RestartDeployedCodePackageAsync | Restart-ServiceFabricDeployedCodePackage | Avec perte de données |
| RestartNode | Simule la défaillance d’un nœud de cluster Service Fabric en redémarrant un nœud. | RestartNodeAsync | Restart-ServiceFabricNode | Avec perte de données |
| RestartPartition | Simule un scénario d’indisponibilité de centre de données ou de cluster en redémarrant certains des réplicas d’une partition, ou tous ces réplicas. | RestartPartitionAsync | Restart-ServiceFabricPartition | Sans perte de données |
| RestartReplica | Simule une défaillance de réplica en redémarrant un réplica persistant au sein d’un cluster (fermeture suivie d’une réouverture). | RestartReplicaAsync | Restart-ServiceFabricReplica | Sans perte de données |
| StartNode | Démarre un nœud dans un cluster déjà arrêté. | StartNodeAsync | Start-ServiceFabricNode | Non applicable |
| StopNode | Simule une défaillance de nœud en arrêtant un nœud dans un cluster. Le nœud restera inactif jusqu’à l’exécution de StartNode. | StopNodeAsync | Stop-ServiceFabricNode | Avec perte de données |
| ValidateApplication | Valide la disponibilité et l’intégrité de l’ensemble des services Service Fabric au sein d’une application, habituellement après avoir provoqué quelques erreurs dans le système. | ValidateApplicationAsync | Test-ServiceFabricApplication | Non applicable |
| ValidateService | Valide la disponibilité et l’intégrité d’un service Service Fabric, habituellement après avoir provoqué quelques erreurs dans le système. | ValidateServiceAsync | Test-ServiceFabricService | Non applicable |

## Exécution d’une action de testabilité avec PowerShell

Ce didacticiel vous explique comment exécuter une action de testabilité avec PowerShell. Vous allez apprendre à exécuter une action de testabilité dans un cluster local (à boîtier unique) ou dans un cluster Microsoft Azure. Microsoft.Fabric.Testability.Powershell.dll (le module de testabilité PowerShell) est installé automatiquement lorsque vous installez le MSI Microsoft Service Fabric. Par ailleurs, le module est chargé automatiquement lors de l’ouverture d’une invite PowerShell.

Sections du didacticiel :

- [Exécuter une action dans un cluster à boîtier unique](#run-an-action-against-a-one-box-cluster)
- [Exécuter une action dans un cluster Microsoft Azure](#run-an-action-against-an-azure-cluster)

### Exécuter une action dans un cluster à boîtier unique

Pour exécuter une action de testabilité dans un cluster local, vous devez tout d’abord vous connecter au cluster et ouvrir l’invite PowerShell en mode administrateur. Examinons l’action **Restart-ServiceFabricNode**.

```powershell
Restart-ServiceFabricNode -NodeName Node1 -CompletionMode DoNotVerify
```

Ici, l’action **Restart-ServiceFabricNode** est exécutée sur un nœud appelé « Node1 » et le mode d’achèvement indique que l’action de redémarrage ne doit faire l’objet d’aucune vérification. La définition du mode d’achèvement sur « Verify » configure la vérification de l’action de redémarrage. Au lieu de spécifier directement le nœud par son nom, vous pouvez effectuer la définition via une clé de partition et le type de réplica, comme suit :

```powershell
Restart-ServiceFabricNode -ReplicaKindPrimary  -PartitionKindNamed -PartitionKey Partition3 -CompletionMode Verify
```


```powershell
$connection = "localhost:19000"
$nodeName = "Node1"

Connect-ServiceFabricCluster $connection
Restart-ServiceFabricNode -NodeName $nodeName -CompletionMode DoNotVerify
```

**Restart-ServiceFabricNode** doit être utilisé pour redémarrer un nœud Service Fabric dans un cluster. Cela arrêtera le processus Fabric.exe qui redémarrera l’ensemble des réplicas du service utilisateur et du service système hébergés sur ce nœud. En utilisant cette API pour tester votre service, vous vous donnez les moyens d’isoler les bogues des chemins de récupération de basculement. Cela permet de simuler les défaillances de nœud dans le cluster.

La capture suivante représente la commande de testabilité **Restart-ServiceFabricNode** en action.

![](media/service-fabric-testability-actions/Restart-ServiceFabricNode.png)

La sortie de la première applet de commande *Get-ServiceFabricNode* (du module Service Fabric PowerShell) indique que le cluster local comporte cinq nœuds : de Node.1 à Node.5. Après avoir exécuté l’action de testabilité (applet de commande) **Restart-ServiceFabricNode** sur le nœud appelé Node.4, nous constatons que le temps d’activité du nœud a été redéfini.

### Exécuter une action dans un cluster Microsoft Azure

L’exécution d’une action de testabilité (avec PowerShell) dans un cluster Azure est similaire à l’exécution de la même action dans un cluster local. Seule différence : avant d’exécuter l’action, au lieu de vous connecter au cluster local, vous vous connectez au cluster Microsoft Azure.

## Exécution d’une action de testabilité avec C## 

Pour exécuter une action de testabilité avec C#, vous devez vous connecter au cluster à l’aide de FabricClient. Obtenez ensuite les paramètres nécessaires à l’exécution de l’action. Différents paramètres peuvent être utilisés pour exécuter une même action. L’action RestartServiceFabricNode peut s’exécuter à l’aide des informations de nœud (nom de nœud et ID d’instance de nœud) dans le cluster.

```csharp
RestartNodeAsync(nodeName, nodeInstanceId, completeMode, operationTimeout, CancellationToken.None)
```

Quelques explications de paramètres :

**CompleteMode** : le mode d’achèvement indique que l’action de redémarrage ne doit faire l’objet d’aucune vérification. La définition du mode d’achèvement sur « Verify » configure la vérification de l’action de redémarrage. **OperationTimeout** : définit le délai précédant la fin de l’opération, avant qu’une exception TimeoutException ne soit lancée. **CancellationToken** : annule un appel en attente.

Au lieu de spécifier directement le nœud par son nom, vous pouvez effectuer la définition via une clé de partition et le type de réplica.

Pour plus d’informations, consultez la section [Sélecteur de partitions et sélecteur de réplicas](#partition_replica_selector).


```csharp
// Add a reference to System.Fabric.Testability.dll and System.Fabric.dll.
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.Fabric.Testability;
using System.Fabric;
using System.Threading;
using System.Numerics;

class Test
{
    public static int Main(string[] args)
    {
        string clusterConnection = "localhost:19000";
        Uri serviceName = new Uri("fabric:/samples/PersistentToDoListApp/PersistentToDoListService");
        string nodeName = "N0040";
        BigInteger nodeInstanceId = 130743013389060139;

        Console.WriteLine("Starting RestartNode test");
        try
        {
            //RestartNode using the replicaSelector
            RestartNodeAsync(clusterConnection, serviceName).Wait();

            //Another way to Restart Node using Nodename and NodeInstanceID.
            RestartNodeAsync(clusterConnection, nodeName, nodeInstanceId).Wait();
        }
        catch (AggregateException exAgg)
        {
            Console.WriteLine("RestartNode did not complete: ");
            foreach (Exception ex in exAgg.InnerExceptions)
            {
                if (ex is FabricException)
                {
                    Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
                }
            }
            return -1;
        }

        Console.WriteLine("RestartNode completed.");
        return 0;
    }

    static async Task RestartNodeAsync(string clusterConnection, Uri serviceName)
    {
        PartitionSelector randomPartitionSelector = PartitionSelector.RandomOf(serviceName);
        ReplicaSelector primaryofReplicaSelector = ReplicaSelector.PrimaryOf(randomPartitionSelector);

        // Create FabricClient with connection & security information here.
        FabricClient fabricclient = new FabricClient(clusterConnection);
        await fabricclient.ClusterManager.RestartNodeAsync(primaryofReplicaSelector, CompletionMode.Verify);
    }

    static async Task RestartNodeAsync(string clusterConnection, string nodeName, BigInteger nodeInstanceId)
    {
        // Create FabricClient with connection & security information here.
        FabricClient fabricclient = new FabricClient(clusterConnection);
        await fabricclient.ClusterManager.RestartNodeAsync(nodeName, nodeInstanceId, CompletionMode.Verify);
    }
}
```

## Sélecteur de partitions et sélecteur de réplicas

### Sélecteur de partitions
Le sélecteur de partitions est une application auxiliaire de testabilité qui est utilisée pour sélectionner une partition spécifique sur laquelle exécuter les actions de testabilité. Si l’ID de partition est connu au préalable, sa sélection est effectuée à l’aide de cette application. Sinon, vous pouvez fournir la clé de partition ; l’opération résoudra en interne l’ID de partition. Il est également possible de sélectionner une partition aléatoire.

Pour ce faire, créez l’objet PartitionSelector, puis sélectionnez la partition à l’aide d’une des méthodes Select*. Ensuite, transmettez l’objet PartitionSelector à l’API qui en a besoin. Si aucune option n’est sélectionnée, la partition aléatoire est utilisée par défaut.

```csharp
Uri serviceName = new Uri("fabric:/samples/InMemoryToDoListApp/InMemoryToDoListService");
Guid partitionIdGuid = new Guid("8fb7ebcc-56ee-4862-9cc0-7c6421e68829");
string partitionName = "Partition1";
Int64 partitionKeyUniformInt64 = 1;

// Select Random partition
PartitionSelector randomPartitionSelector = PartitionSelector.RandomOf(serviceName);

// Select partition based on Id
PartitionSelector partitionSelectorById = PartitionSelector.PartitionIdOf(serviceName, partitionIdGuid);

// Select partition based on name
PartitionSelector namedPartitionSelector = PartitionSelector.PartitionKeyOf(serviceName, partitionName);

// Select partition based on partition key
PartitionSelector uniformIntPartitionSelector = PartitionSelector.PartitionKeyOf(serviceName, partitionKeyUniformInt64);
```

### Sélecteur de réplicas
ReplicaSelector est une application auxiliaire de testabilité qui est utilisée pour sélectionner un réplica sur lequel exécuter les actions de testabilité. Si l’ID de réplica est connu au préalable, sa sélection est effectuée à l’aide de cette application. En outre, vous pouvez également sélectionner un réplica principal ou un réplica aléatoire secondaire. ReplicaSelector dérivant de PartitionSelector, vous devez sélectionner à la fois le réplica et la partition sur lesquels effectuer l’opération de testabilité.

Pour ce faire, créez un objet ReplicaSelector, puis définissez la méthode de sélection du réplica et de la partition. Il est alors temps de les transmettre à l’API qui en a besoin. Si aucune option n’est sélectionnée, la partition et le réplica aléatoire sont utilisés par défaut.

Guid partitionIdGuid = new Guid("8fb7ebcc-56ee-4862-9cc0-7c6421e68829"); PartitionSelector partitionSelector = PartitionSelector.PartitionIdOf(serviceName, partitionIdGuid); long replicaId = 130559876481875498;


```csharp
// Select Random replica
ReplicaSelector randomReplicaSelector = ReplicaSelector.RandomOf(partitionSelector);

// Select primary replica
ReplicaSelector primaryReplicaSelector = ReplicaSelector.PrimaryOf(partitionSelector);

// Select replica by Id
ReplicaSelector replicaByIdSelector = ReplicaSelector.ReplicaIdOf(partitionSelector, replicaId);

// Select random secondary replica
ReplicaSelector secondaryReplicaSelector = ReplicaSelector.RandomSecondaryOf(partitionSelector);
```

## Étapes suivantes

- [Scénarios de testabilité](service-fabric-testability-scenarios.md)
- Procédure de test de votre service
   - [Simuler des défaillances au cours des charges de travail de services](service-fabric-testability-workload-tests.md)
   - [Échecs de communication de service à service](service-fabric-testability-scenarios-service-communication.md)
 

<!---HONumber=Oct15_HO3-->