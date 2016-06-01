<properties
	pageTitle="Utiliser PowerShell pour créer et configurer un espace de travail Log Analytics | Microsoft Azure"
	description="Log Analytics utilise des données des serveurs dans votre infrastructure locale ou dans le cloud. Vous pouvez collecter des données de la machine à partir du stockage Azure lorsqu’elles sont générées par les diagnostics Azure."
	services="log-analytics"
	documentationCenter=""
	authors="richrundmsft"
	manager="jochan"
	editor=""/>

<tags
	ms.service="log-analytics"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="powershell"
	ms.topic="article"
	ms.date="05/16/2016"
	ms.author="richrund"/>

# Gérer Log Analytics à l’aide de PowerShell

Vous pouvez utiliser les [applets de commande PowerShell Log Analytics](http://msdn.microsoft.com/library/mt188224.aspx) pour effectuer diverses fonctions dans Log Analytics à partir d’une ligne de commande ou via un script. Voici quelques exemples des tâches que vous pouvez effectuer avec PowerShell :

+ Créer un espace de travail
+ Ajouter ou supprimer une solution
+ Importer et exporter des recherches enregistrées
+ Ajouter l’agent Log Analytics à une machine virtuelle Azure
+ Configurer Log Analytics pour indexer les données collectées à l’aide des diagnostics Azure

Cet article fournit deux exemples de code qui illustrent quelques-unes des fonctions que vous pouvez effectuer à partir de PowerShell. Pour obtenir des informations sur d’autres fonctions, consultez [Informations de référence sur les applets de commande PowerShell Log Analytics](http://msdn.microsoft.com/library/mt188224.aspx).

> [AZURE.NOTE] Avant, Log Analytics s’appelait Operational Insights, ce qui explique pourquoi ce nom est présent dans les applets de commande.

## Configuration requise

Pour utiliser PowerShell avec votre espace de travail Log Analytics, vous devez disposer des éléments suivants :

+ Un abonnement Azure 
+ Un espace de travail Azure Log Analytics lié à votre abonnement Azure

Si vous avez déjà créé un espace de travail OMS, mais que vous ne l’avez pas encore lié à un abonnement Azure, vous pouvez soit créer le lien dans le portail Azure ou OMS, soit utiliser les applets de commande Get-AzureRMOperationalInsightsLinkTargets et New-AzureRMOperationalInsightsWorkspace.

## Créer un espace de travail Log Analytics, ajouter des solutions et des recherches enregistrées

L’exemple de script suivant illustre comment :

1.	Créer un espace de travail
2.	Répertorier les solutions disponibles
3.	Ajouter des solutions à l’espace de travail
4.	Importer des recherches enregistrées
5.	Exporter des recherches enregistrées

```

$ResourceGroup = "oms-example"
$WorkspaceName = "log-analytics-" + (Get-Random -Maximum 99999) # workspace names need to be unique - Get-Random helps with this for the example code
$Location = "westeurope"

# List of solutions to enable
$Solutions = "Security", "Updates", "SQLAssessment"

# Saved Searches to import
$ExportedSearches = @"
[
    {
        "Category":  "My Saved Searches",
        "DisplayName":  "WAD Events (All)",
        "Query":  "Type=Event SourceSystem:AzureStorage ",
        "Version":  1
    },
    {        
        "Category":  "My Saved Searches",
        "DisplayName":  "Current Disk Queue Length",
        "Query":  "Type=Perf ObjectName=LogicalDisk InstanceName="C:" CounterName="Current Disk Queue Length"",
        "Version":  1
    }
]
"@ | ConvertFrom-Json

# Create the resource group if needed
try {
    Get-AzureRmResourceGroup -Name $ResourceGroup -ErrorAction Stop
} catch {
    New-AzureRmResourceGroup -Name $ResourceGroup -Location $Location
}

# Create the workspace
New-AzureRmOperationalInsightsWorkspace -Location $Location -Name $WorkspaceName -Sku Standard -ResourceGroupName $ResourceGroup

# List all solutions and their installation status
Get-AzureRmOperationalInsightsIntelligencePacks -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName

# Add solutions
foreach ($soln in $Solutions) {
    Set-AzureRmOperationalInsightsIntelligencePack -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -IntelligencePackName $soln -Enabled $true
}

#List enabled solutions
(Get-AzureRmOperationalInsightsIntelligencePacks -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName).Where({($_.enabled -eq $true)})

# Import Saved Searches
foreach ($search in $ExportedSearches) {
    $id = $search.Category + "|" + $search.DisplayName
    New-AzureRmOperationalInsightsSavedSearch -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -SavedSearchId $id -DisplayName $search.DisplayName -Category $search.Category -Query $search.Query -Version $search.Version
}

# Export Saved Searches
(Get-AzureRmOperationalInsightsSavedSearch -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName).Value.Properties | ConvertTo-Json 

```

## Configuration de Log Analytics pour indexer les diagnostics Azure 

Pour effectuer une analyse sans agent des ressources Azure, notamment comme rôles web et de travail, clusters Service Fabric, groupes de sécurité réseau, coffres de clés et passerelles d’application, vous devez d’abord vérifier que les diagnostics Azure sont autorisés à écrire dans un compte de stockage. Ensuite, vous pouvez configurer Log Analytics pour qu’il collecte les journaux à partir du compte de stockage.

Il est également possible d’utiliser PowerShell pour configurer un espace de travail Log Analytics dans un abonnement Azure en vue de collecter les journaux de différents abonnements Azure.

L’exemple suivant montre comment :

1.	Répertorier les emplacements et les comptes de stockage existants à partir desquels Log Analytics indexe les données
2.	Créer une configuration pour lire un compte de stockage
3.	Mettre à jour la configuration créée pour indexer les données à partir d’emplacements supplémentaires
4.	Supprimer la configuration créée

```
# validTables = "WADWindowsEventLogsTable", "LinuxsyslogVer2v0", "WADServiceFabric*EventTable", "WADETWEventTable" 
$workspace = (Get-AzureRmOperationalInsightsWorkspace).Where({$_.Name -eq "your workspace name"})

# Update these two with the storage account resource id and the storage account key for the storage account you want to Log Analytics to  
$storageId = "/subscriptions/ec11ca60-1234-491e-5678-0ea07feae25c/resourceGroups/demo/providers/Microsoft.Storage/storageAccounts/wadv2storage"
$key = "abcd=="

# List existing insights
Get-AzureRmOperationalInsightsStorageInsight -ResourceGroupName $workspace.ResourceGroupName -WorkspaceName $workspace.Name

# Create a new insight
New-AzureRmOperationalInsightsStorageInsight -ResourceGroupName $workspace.ResourceGroupName -WorkspaceName $workspace.Name -Name "newinsight" -StorageAccountResourceId $storageId -StorageAccountKey $key -Tables @("WADWindowsEventLogsTable") -Containers @("wad-iis-logfiles")

# Update existing insight
Set-AzureRmOperationalInsightsStorageInsight -ResourceGroupName $workspace.ResourceGroupName -WorkspaceName $workspace.Name -Name "newinsight" -Tables @("WADWindowsEventLogsTable", "WADETWEventTable") -Containers @("wad-iis-logfiles", "insights-logs-networksecuritygroupevent/resourceId=/SUBSCRIPTIONS/ec11ca60-1234-491e-5678-0ea07feae25c/RESOURCEGROUPS/DEMO/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/DEMO")

# Remove the insight
Remove-AzureRmOperationalInsightsStorageInsight -ResourceGroupName $workspace.ResourceGroupName -WorkspaceName $workspace.Name -Name "newinsight" 

```

## Étapes suivantes

- [Passez en revue les applets de commande PowerShell de Log Analytics](http://msdn.microsoft.com/library/mt188224.aspx) pour obtenir plus d’informations sur l’utilisation de PowerShell pour configurer Log Analytics.

<!---HONumber=AcomDC_0518_2016-->