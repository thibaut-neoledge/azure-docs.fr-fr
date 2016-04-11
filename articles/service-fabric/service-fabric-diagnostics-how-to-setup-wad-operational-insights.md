<properties
   pageTitle="Comment collecter des journaux avec Diagnostics et Operational Insights | Microsoft Azure"
   description="Cet article décrit la procédure de configuration d’Azure Diagnostics et d’Azure Operational Insights pour la collecte de journaux d’un cluster Service Fabric exécuté dans Azure.Create an Image instructions in this article."
   services="service-fabric"
   documentationCenter=".net"
   authors="ms-toddabel"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="03/25/2016"
   ms.author="toddabel"/>


# Collecte de journaux d’un cluster Service Fabric avec les diagnostics Azure et Operational Insights

Lorsque vous exécutez un cluster Service Fabric dans Azure, il peut être intéressant de collecter les journaux de tous les nœuds pour les regrouper dans un emplacement central. La centralisation des journaux facilite l’analyse et la résolution des problèmes que vous pourriez rencontrer dans votre cluster ou dans les applications et services exécutés dans ce cluster. L’une des façons de charger et collecter les journaux consiste à utiliser l’extension Azure Diagnostics, qui charge les journaux dans Azure Storage.

Azure [Operational Insights](https://azure.microsoft.com/services/operational-insights/) (composant de Microsoft Operations Management Suite) est une solution SaaS qui facilite l’analyse et la recherche de journaux. Les étapes ci-dessous vous expliquent comment configurer l’extension Azure Diagnostics sur les machines virtuelles du cluster afin de charger les journaux vers un magasin central, puis comment configurer Operational Insights pour extraire les journaux et les rendre consultables depuis le portail Operational Insights.

Operational Insights identifie les sources des différents types de journaux chargés à partir d’un cluster Service Fabric grâce aux noms des tables de stockage dans lesquelles ils sont stockés. Cela signifie que l’extension Azure Diagnostics doit être configurée de manière à charger les journaux dans les tables de stockage avec des noms qui correspondent à la recherche d’Operational Insights. Les exemples de paramètres de configuration inclus dans ce document indiquent les noms souhaités pour les tables de stockage.


## Composants requis
Les outils suivants sont utilisés pour exécuter certaines opérations décrites dans ce document :

* [Azure PowerShell](../powershell-install-configure.md)
* [Operational Insights](https://azure.microsoft.com/services/operational-insights/)


## Configuration d’Operational Insights pour l’affichage et l’analyse des journaux de cluster
L’article détaillant la [procédure de collecte de journaux avec les diagnostics Azure](service-fabric-diagnostics-how-to-setup-wad.md) décrit Étape par Étape comment activer les journaux envoyés à un compte de stockage Azure. Lorsque Diagnostics est configuré sur le cluster et charge des journaux sur un compte de stockage, vous devez alors configurer Operational Insights pour afficher, rechercher et interroger tous les journaux du cluster via le portail d’Operational Insights.

### Création d’un espace de travail Operational Insights
Pour connaître les étapes de création d’un espace de travail Operational Insights, consultez l’article ci-dessous. Notez qu’il décrit deux façons différentes de créer un espace de travail. Choisissez le portail Azure et l’approche basée sur l’abonnement. Dans les sections suivantes de ce document, vous aurez besoin du nom de l’espace de travail Operational Insights. Créez l’espace de travail Operational Insights en utilisant le même abonnement que celui que vous avez utilisé pour créer toutes les ressources de cluster, y compris les comptes de stockage.

[Intégration d’Operational Insights (en anglais)](https://technet.microsoft.com/library/mt484118.aspx)

### Configuration d’un espace de travail Operational Insights pour l’affichage des journaux du cluster
Après avoir créé l’espace de travail Operational Insights comme décrit ci-dessus, l’étape suivante consiste à configurer cet espace de travail afin d’extraire les journaux des tables de stockage Azure sur lesquelles ils sont chargés par l’extension Diagnostics, à partir du cluster. À l’heure actuelle, cette configuration ne peut pas être mise en œuvre via le portail Operational Insights et ne peut être effectuée qu’à l’aide des commandes PowerShell. Exécutez le script PowerShell suivant :

```powershell

    <#
    This script will configure an Operations Management Suite workspace (aka Operational Insights workspace) to read Diagnostics from an Azure Storage account.

    It will enable all supported data types (currently Windows Event Logs, Syslog, Service Fabric Events, ETW Events and IIS Logs).

    It supports both classic and Resource Manager storage accounts.

    If you have more than one OMS workspace you will be prompted for the workspace to configure.

    If you have more than one storage account you will be prompted for which storage account to configure.
    #>

Add-AzureAccount

Switch-AzureMode -Name AzureResourceManager

$validTables = "WADServiceFabric*EventTable", "WADETWEventTable"

function Select-Workspace {

    $workspace = ""

    $allWorkspaces = Get-AzureOperationalInsightsWorkspace  

    switch ($allWorkspaces.Count) {
        0 {Write-Error "No Operations Management Suite workspaces found"}
        1 {return $allWorkspaces}
        default {
            $uiPrompt = "Enter the number corresponding to the workspace you want to configure.`n"

            $count = 1
            foreach ($workspace in $allWorkspaces) {
                $uiPrompt += "$count. " + $workspace.Name + " (" + $workspace.CustomerId + ")`n"
                $count++
            }
            $answer = (Read-Host -Prompt $uiPrompt) - 1
            $workspace = $allWorkspaces[$answer]
        }  
    }
    return $workspace
}

function Select-StorageAccount {

    $storage = ""

    $allStorageAccounts = (get-azureresource -ResourceType Microsoft.ClassicStorage/storageAccounts) + (get-azureresource -ResourceType Microsoft.Storage/storageAccounts)

    switch ($allStorageAccounts.Count) {
        0 {Write-Error "No storage accounts found"}
        1 {$storage = $allStorageAccounts}
        default {

            $uiPrompt = "Enter the number corresponding to the storage account with diagnostics.`n"

            $count = 1
            foreach ($storageAccount in $allStorageAccounts) {
                $uiPrompt += "$count. " + $storageAccount.Name + " (" + $storageAccount.Location + ")`n"
                $count++
            }
            $answer = (Read-Host -Prompt $uiPrompt)

            $storage = $allStorageAccounts[$answer - 1]
        }
    }
    return $storage
}

$workspace = Select-Workspace
$storageAccount = Select-StorageAccount

$insightsName = $storageAccount.Name + $workspace.Name

$existingConfig = ""

try
{
    $existingConfig = Get-AzureOperationalInsightsStorageInsight -Workspace $workspace -Name $insightsName -ErrorAction Stop
}
catch [Hyak.Common.CloudException]
{
    # HTTP Not Found is returned if the storage insight doesn't exist
}

if ($existingConfig) {
    Set-AzureOperationalInsightsStorageInsight -Workspace $workspace -Name $insightsName -Tables $validTables -Containers $validContainers

} else {
    if ($storageAccount.ResourceType -eq "Microsoft.ClassicStorage/storageAccounts") {
        Switch-AzureMode -Name AzureServiceManagement
        $key = (Get-AzureStorageKey -StorageAccountName $storageAccount.Name).Primary
        Switch-AzureMode -Name AzureResourceManager
    } else {
        $key = (Get-AzureStorageAccountKey -ResourceGroupName $storageAccount.ResourceGroupName -Name $storageAccount.Name).Key1
    }
    New-AzureOperationalInsightsStorageInsight -Workspace $workspace -Name $insightsName -StorageAccountResourceId $storageAccount.ResourceId -StorageAccountKey $key -Tables $validTables -Containers $validContainers
}
```

Une fois que vous avez configuré l’espace de travail Operational Insights pour la lecture à partir des tables Azure dans votre compte de stockage, vous devez vous connecter au portail et accéder à l’onglet **Stockage** associé à la ressource Operational Insights. Vous devriez obtenir le résultat suivant : ![Configuration du stockage Operational Insights dans le portail Azure](./media/service-fabric-diagnostics-how-to-setup-wad-operational-insights/oi-connected-tables-list.png)

### Recherche et affichage des journaux dans Operational Insights
Après avoir configuré votre espace de travail Operational Insights pour lire les journaux à partir du compte de stockage spécifié, l’affichage des journaux dans l’interface Operational Insights peut prendre jusqu’à 10 minutes. Pour avoir la certitude que de nouveaux journaux sont bien générés, vous pouvez déployer une application Service Fabric sur votre cluster de manière à générer des événements opérationnels à partir de la plateforme Service Fabric.

1. Pour afficher les journaux, sélectionnez **Recherche de journal** sur la page principale du portail Operational Insights. ![Option de recherche de journaux Operational Insights](./media/service-fabric-diagnostics-how-to-setup-wad-operational-insights/log-search-button-oi.png)

2. Sur la page de recherche de journaux, utilisez la requête **Type=ServiceFabricOperationalEvent** pour afficher les journaux opérationnels de votre cluster, comme illustré ci-dessous. Pour afficher tous les journaux du modèle de programmation d’acteur, recherchez **Type = ServiceFabricReliableActorEvent**. Pour afficher tous les journaux du modèle de programmation Reliable Services, tapez **Type = ServiceFabricReliableServiceEvent**. ![Requête et affichage des journaux Operational Insights](./media/service-fabric-diagnostics-how-to-setup-wad-operational-insights/view-logs-oi.png)

### Exemples de requêtes facilitant la résolution des problèmes
Voici quelques exemples de scénarios et de requêtes que vous pouvez utiliser pour les résoudre :

1. **Pour savoir si la tâche RunAsync a été appelée pour un service spécifique par Service Fabric :** vous pouvez agir ainsi si vous devez vous assurer qu’un service se bloque lors du démarrage. Pour cela, effectuez une recherche en utilisant une requête similaire à celle ci-dessous, en remplaçant le nom du service et de l’application de manière à correspondre à ce que vous avez déployé et analysez les résultats obtenus :

    ```
    Type=ServiceFabricReliableServiceEvent AND ServiceName="fabric:/Application2/Stateless1" AND "RunAsync has been invoked"
    ```

2. **Si vous exécutez un service avec état et que vous souhaitez voir si des exceptions ont été levées et marquées comme des échecs par Service Fabric :** recherchez ces événements avec une requête similaire à celle-ci :

    ```
    Type=ServiceFabricReliableServiceEvent AND ServiceName="fabric:/Application2/Stateful1" AND TaskName=StatefulRunAsyncFailure
    ```

3. **Pour rechercher des événements correspondant aux exceptions levées par les méthodes d’acteur dans tous les services et applications déployés :** vous pouvez utiliser une requête de ce type :

    ```
    Type=ServiceFabricReliableActorEvent AND TaskName=ActorMethodThrewException
    ```

## Mise à jour de Diagnostics pour collecter et charger des journaux depuis de nouveaux canaux EventSource
Pour mettre à jour Diagnostics de manière à collecter des journaux à partir de nouveaux canaux EventSource représentant une nouvelle application que vous allez déployer, vous devez simplement exécuter les mêmes étapes que celles décrites dans la [section ci-dessus](#deploywadarm) relative à la configuration de Diagnostics pour un cluster existant.

Vous devrez mettre à jour la section EtwEventSourceProviderConfiguration dans le fichier WadConfigUpdate.json pour ajouter des entrées pour les nouveaux EventSources avant d’appliquer la mise à jour de la configuration via la commande Resource Manager. La table pour le chargement reste la même (ETWEventTable), puisqu’il s’agit de la table qui est configurée pour lire les événements ETW d’application depuis Operational Insights.


## Étapes suivantes
Vérifiez les événements de diagnostic émis pour [Reliable Actors](service-fabric-reliable-actors-diagnostics.md) et [Reliable Services](service-fabric-reliable-services-diagnostics.md) pour comprendre plus en détail les événements auxquels vous devriez être attentif lors de la résolution des problèmes.

<!---HONumber=AcomDC_0330_2016-->