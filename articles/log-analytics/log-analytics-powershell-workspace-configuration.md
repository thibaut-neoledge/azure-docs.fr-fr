---
title: Utiliser PowerShell pour créer et configurer un espace de travail Log Analytics | Microsoft Docs
description: Log Analytics utilise des données des serveurs dans votre infrastructure locale ou dans le cloud. Vous pouvez collecter des données de la machine à partir du stockage Azure lorsqu’elles sont générées par les diagnostics Azure.
services: log-analytics
documentationcenter: ''
author: richrundmsft
manager: jochan
editor: ''

ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: powershell
ms.topic: article
ms.date: 08/15/2016
ms.author: richrund

---
# <a name="manage-log-analytics-using-powershell"></a>Gérer Log Analytics à l’aide de PowerShell
Vous pouvez utiliser les [applets de commande PowerShell Log Analytics](http://msdn.microsoft.com/library/mt188224.aspx) pour exécuter diverses fonctions dans Log Analytics à partir d’une ligne de commande ou via un script.  Voici quelques exemples des tâches que vous pouvez effectuer avec PowerShell :

* Créer un espace de travail
* Ajouter ou supprimer une solution
* Importer et exporter des recherches enregistrées
* Créer un groupe d’ordinateurs
* Activer la collecte de journaux IIS à partir d’ordinateurs sur lesquels l’agent Windows est installé
* Collecter les compteurs de performances d’ordinateurs Linux et Windows
* Collecter les événements de Syslog sur des ordinateurs Linux 
* Collecter les événements des journaux des événements Windows
* Collecter des journaux des événements personnalisés
* Ajouter l’agent Log Analytics à une machine virtuelle Azure
* Configurer Log Analytics pour indexer les données collectées à l’aide des diagnostics Azure

Cet article fournit deux exemples de code qui illustrent quelques-unes des fonctions que vous pouvez effectuer à partir de PowerShell.  Pour obtenir des informations sur d’autres fonctions, consultez [Informations de référence sur les applets de commande PowerShell Log Analytics](http://msdn.microsoft.com/library/mt188224.aspx) .

> [!NOTE]
> Avant, Log Analytics s’appelait Operational Insights, ce qui explique pourquoi ce nom est présent dans les applets de commande.
> 
> 

## <a name="prerequisites"></a>Composants requis
Pour utiliser PowerShell avec votre espace de travail Log Analytics, vous devez disposer des éléments suivants :

* Un abonnement Azure 
* Un espace de travail Azure Log Analytics lié à votre abonnement Azure

Si vous avez créé un espace de travail OMS qui n’est pas encore lié à un abonnement Azure, vous pouvez créer le lien :

* Dans le portail Azure
* Dans le portail OMS ou 
* En utilisant les applets de commande Get-AzureRmOperationalInsightsLinkTargets et New-AzureRmOperationalInsightsWorkspace.

## <a name="create-and-configure-a-log-analytics-workspace"></a>Créer et configurer un espace de travail Log Analytics
L’exemple de script suivant illustre comment :

1. Créer un espace de travail
2. Répertorier les solutions disponibles
3. Ajouter des solutions à l’espace de travail
4. Importer des recherches enregistrées
5. Exporter des recherches enregistrées
6. Créer un groupe d’ordinateurs
7. Activer la collecte de journaux IIS à partir d’ordinateurs sur lesquels l’agent Windows est installé
8. Collecter les compteurs de performances de disque logique d’ordinateurs Linux (% d’Inodes utilisés, Mo libres, % d’espace utilisé, Transferts disque/s, Lectures disque/s, Écritures disque/s)
9. Collecter les événements Syslog d’ordinateurs Linux
10. Collecter les événements d’erreur et d’avertissement du journal des événements d’application d’ordinateurs Windows
11. Collecter le compteur de performances Mo de mémoire disponible d’ordinateurs Windows
12. Collecter un journal personnalisé 

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
        "Query":  "Type=Perf ObjectName=LogicalDisk InstanceName=\"C:\" CounterName=\"Current Disk Queue Length\"",
        "Version":  1
    }
]
"@ | ConvertFrom-Json

# Custom Log to collect
$CustomLog = @"
{
    "customLogName": "sampleCustomLog1", 
    "description": "Example custom log datasource", 
    "inputs": [
        { 
            "location": { 
            "fileSystemLocations": { 
                "windowsFileTypeLogPaths": [ "e:\\iis5\\*.log" ], 
                "linuxFileTypeLogPaths": [ "/var/logs" ] 
                } 
            }, 
        "recordDelimiter": { 
            "regexDelimiter": { 
                "pattern": "\\n", 
                "matchIndex": 0, 
                "matchIndexSpecified": true, 
                "numberedGroup": null 
                } 
            } 
        }
    ], 
    "extractions": [
        { 
            "extractionName": "TimeGenerated", 
            "extractionType": "DateTime", 
            "extractionProperties": { 
                "dateTimeExtraction": { 
                    "regex": null, 
                    "joinStringRegex": null 
                    } 
                } 
            }
        ] 
    }
"@

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
foreach ($solution in $Solutions) {
    Set-AzureRmOperationalInsightsIntelligencePack -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -IntelligencePackName $solution -Enabled $true
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

# Create Computer Group
New-AzureRmOperationalInsightsComputerGroup -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -SavedSearchId "My Web Servers" -DisplayName "Web Servers" -Category "My Saved Searches" -Query "Computer=""web*"" | distinct Computer" -Version 1

# Enable IIS Log Collection using agent
Enable-AzureRmOperationalInsightsIISLogCollection -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName

# Linux Perf
New-AzureRmOperationalInsightsLinuxPerformanceObjectDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -ObjectName "Logical Disk" -InstanceName "*"  -CounterNames @("% Used Inodes", "Free Megabytes", "% Used Space", "Disk Transfers/sec", "Disk Reads/sec", "Disk Reads/sec", "Disk Writes/sec") -IntervalSeconds 20  -Name "Example Linux Disk Performance Counters"
Enable-AzureRmOperationalInsightsLinuxCustomLogCollection -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName

# Linux Syslog
New-AzureRmOperationalInsightsLinuxSyslogDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -Facility "kern" -CollectEmergency -CollectAlert -CollectCritical -CollectError -CollectWarning -Name "Example kernal syslog collection"
Enable-AzureRmOperationalInsightsLinuxSyslogCollection -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName

# Windows Event
New-AzureRmOperationalInsightsWindowsEventDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -EventLogName "Application" -CollectErrors -CollectWarnings -Name "Example Application Event Log"

# Windows Perf
New-AzureRmOperationalInsightsWindowsPerformanceCounterDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -ObjectName "Memory" -InstanceName "*" -CounterName "Available MBytes" -IntervalSeconds 20 -Name "Example Windows Performance Counter"

# Custom Logs
New-AzureRmOperationalInsightsCustomLogDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -CustomLogRawJson "$CustomLog" -Name "Example Custom Log Collection"

```

## <a name="configuring-log-analytics-to-index-azure-diagnostics"></a>Configuration de Log Analytics pour indexer les diagnostics Azure
Pour une analyse sans agent des ressources Azure, celles-ci doivent avoir les diagnostics Azure activés et configurés pour écrire dans un compte de stockage. Log Analytics peut ensuite être configuré pour collecter les journaux du compte de stockage. Les ressources dont vous avez besoin pour effectuer la configuration précédente sont les suivantes :

* Services cloud classiques (rôles de travail et web)
* Clusters Service Fabric
* Groupes de sécurité réseau
* Key Vaults et 
* Passerelles d’application

Il est également possible d’utiliser PowerShell pour configurer un espace de travail Log Analytics dans un abonnement Azure en vue de collecter les journaux de différents abonnements Azure.

L’exemple suivant montre comment :

1. Répertorier les emplacements et les comptes de stockage existants à partir desquels Log Analytics indexe les données
2. Créer une configuration pour lire à partir d’un compte de stockage
3. Mettre à jour la configuration créée pour indexer les données à partir d’emplacements supplémentaires
4. Supprimer la configuration créée

```
# validTables = "WADWindowsEventLogsTable", "LinuxsyslogVer2v0", "WADServiceFabric*EventTable", "WADETWEventTable" 
$workspace = (Get-AzureRmOperationalInsightsWorkspace).Where({$_.Name -eq "your workspace name"})

# Update these two lines with the storage account resource ID and the storage account key for the storage account you want to Log Analytics to  
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

## <a name="next-steps"></a>Étapes suivantes
* [Passez en revue les applets de commande PowerShell de Log Analytics](http://msdn.microsoft.com/library/mt188224.aspx) pour obtenir plus d’informations sur l’utilisation de PowerShell pour configurer Log Analytics.

<!--HONumber=Oct16_HO2-->


