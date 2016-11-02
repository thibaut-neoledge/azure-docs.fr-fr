<properties
    pageTitle="Analyser les journaux de diagnostic Azure à l’aide de Log Analytics | Microsoft Azure"
    description="Log Analytics peut lire les journaux des services Azure qui écrivent des journaux de diagnostic Azure dans Stockage Blob au format JSON."
    services="log-analytics"
    documentationCenter=""
    authors="bandersmsft"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="log-analytics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="banders"/>



# <a name="analyze-azure-diagnostic-logs-using-log-analytics"></a>Analyser les journaux de diagnostic Azure à l’aide de Log Analytics

Log Analytics peut collecter les journaux pour les services Azure suivants, qui écrivent des [journaux de diagnostic Azure](../azure-portal/monitoring-overview-of-diagnostic-logs.md) dans le Stockage Blob au format JSON :

+ Automation (version préliminaire)
+ Key Vault (version préliminaire)
+ Application Gateway (version préliminaire)
+ Groupe de sécurité réseau (version préliminaire)

Les sections suivantes vous guident dans l’utilisation de PowerShell pour effectuer les opérations suivantes :

+ Configurer Log Analytics afin qu’il collecte les journaux à partir du stockage pour chaque ressource  
+ Activer la solution Log Analytics pour le service Azure

Pour que Log Analytics puisse collecter les données pour ces ressources, les diagnostics Azure doivent être activés. Utilisez l’applet de commande `Set-AzureRmDiagnosticSetting` pour activer la journalisation.

Pour plus d’informations sur la façon d’activer la journalisation des diagnostics, voir les articles suivants :

+ [Key Vault](../key-vault/key-vault-logging.md)
+ [Application Gateway](../application-gateway/application-gateway-diagnostics.md)
+ [Groupe de sécurité réseau](../virtual-network/virtual-network-nsg-manage-log.md)

Cette documentation comprend également des détails concernant les aspects suivants :

+ Résolution des problèmes de collecte de données
+ Arrêt de la collecte de données

## <a name="configure-log-analytics-to-collect-azure-diagnostic-logs"></a>Configurer Log Analytics pour collecter les journaux de diagnostic Azure

La collecte de journaux pour ces services et l’activation de la solution pour visualiser les journaux sont effectuées à l’aide de scripts PowerShell.

L’exemple suivant active la journalisation sur toutes les ressources prises en charge :

```
# update to be the storage account that logs will be written to. Storage account must be in the same region as the resource to monitor
# format is similar to "/subscriptions/ec11ca60-ab12-345e-678d-0ea07bbae25c/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/mystorageaccount"
$storageAccountId = ""

$supportedResourceTypes = ("Microsoft.Automation/AutomationAccounts", "Microsoft.KeyVault/Vaults", "Microsoft.Network/NetworkSecurityGroups", "Microsoft.Network/ApplicationGateways")

# update location to match your storage account location
$resources = Get-AzureRmResource | where { $_.ResourceType -in $supportedResourceTypes -and $_.Location -eq "westus" }

foreach ($resource in $resources) {
    Set-AzureRmDiagnosticSetting -ResourceId $resource.ResourceId -StorageAccountId $storageAccountId -Enabled $true -RetentionEnabled $true -RetentionInDays 1
}
```


Pour certaines ressources, il est possible d’effectuer la configuration précédente à partir du portail Azure en procédant de la manière décrite dans [Présentation des journaux de diagnostic Azure](../azure-portal/monitoring-overview-of-diagnostic-logs.md#how-to-enable-collection-of-diagnostic-logs).

## <a name="configure-log-analytics-to-collect-azure-diagnostic-logs"></a>Configurer Log Analytics pour collecter les journaux de diagnostic Azure

Nous avons fourni un module de script PowerShell qui exporte les deux applets de commande afin de faciliter la configuration de Log Analytics :

1. `Add-AzureDiagnosticsToLogAnalyticsUI` vous invite à saisir une entrée et peut définir des configurations simples
2. `Add-AzureDiagnosticsToLogAnalytics` prend en entrée les ressources à analyser, puis configure Log Analytics  

### <a name="pre-requisites"></a>Conditions préalables

1. Azure PowerShell avec la version 1.0.8 ou une version plus récente des applets de commande Operational Insights.
  - [Installation et configuration d’Azure PowerShell](../powershell-install-configure.md)
  - Vérifiez votre version des applets de commande : `Import-Module AzureRM.OperationalInsights -MinimumVersion 1.0.8 `
2. La journalisation des diagnostics est configurée pour la ressource Azure à surveiller. Utilisez `Set-AzureRmDiagnosticSetting`consultez [Utiliser Log Analytics pour collecter des données à partir de comptes de stockage Azure](log-analytics-azure-storage.md) pour savoir comment activer les diagnostics.
3. Un espace de travail [Log Analytics](https://portal.azure.com/#create/Microsoft.LogAnalyticsOMS)  
4. Le module PowerShell AzureDiagnosticsAndLogAnalytics
  - Téléchargez le module [AzureDiagnosticsAndLogAnalytics](https://www.powershellgallery.com/packages/AzureDiagnosticsAndLogAnalytics/) à partir de la galerie PowerShell

### <a name="option-1:-run-the-interactive-configuration-scripts"></a>Option 1 : Exécuter les scripts de configuration interactive

Ouvrez PowerShell et exécutez :

```
# Connect to Azure
Login-AzureRmAccount
# If you have diagnostics logs being written to classic storage you will also need to run
Add-AzureAccount

# Import the module
Install-Module -Name AzureDiagnosticsAndLogAnalytics

# Run the UI configuration script
Add-AzureDiagnosticsToLogAnalyticsUI

```

La liste des sélections disponibles s’affiche et vous êtes invité à effectuer votre sélection.
Vous êtes invité à opérer des sélections pour chacun des éléments suivants :

+ Types de ressources (Services Azure) à partir desquels les journaux doivent être collectés
+ Instances de ressources à partir desquelles les journaux doivent être collectés
+ Espace de travail Log Analytics pour la collecte des données

Après exécution de ce script, vous devriez voir les enregistrements dans Log Analytics environ 30 minutes après l’écriture de nouvelles données de diagnostic dans le stockage. Si les enregistrements ne sont pas disponibles après ce délai, consultez la section de dépannage ci-dessous.

### <a name="option-2:-build-a-list-of-resources-and-pass-them-to-the-configuration-cmdlet"></a>Option 2 : Créer une liste de ressources et la transmettre à l’applet de commande de configuration

Vous pouvez dresser la liste des ressources pour lesquelles les diagnostics Azure sont activés, puis transmettre les ressources à l’applet de commande de configuration.

Vous pouvez voir des informations supplémentaires sur l’applet de commande en exécutant la commande `Get-Help Add-AzureDiagnosticsToLogAnalytics`.


Pour en savoir plus sur OMS, voir [Applets de commande PowerShell Log Analytics](https://msdn.microsoft.com/library/mt188224.aspx).

>[AZURE.NOTE] Si la ressources et l’espace de travail se trouvent dans des abonnements Azure différents, basculez entre eux en fonction des besoins à l’aide de la commande `Select-AzureRmSubscription -SubscriptionId <Subscription the resource is in>`.

```
# Connect to Azure
Login-AzureRmAccount
# If you have diagnostics logs being written to classic storage you will also need to run
Add-AzureAccount

# Import the module
Install-Module -Name AzureDiagnosticsAndLogAnalytics

# Update the values below with the name of the resource that has Azure diagnostics enabled and the name of your Log Analytics workspace
$resourceName = "***example-resource***"
$workspaceName = "***log-analytics-workspace***"

# Find the resource to monitor:
$resource = Find-AzureRmResource -ResourceType "Microsoft.KeyVault/Vaults" -ResourceNameContains $resourceName

# Find the Log Analytics workspace to configure, for example:
$workspace = Find-AzureRmResource -ResourceType "Microsoft.OperationalInsights/workspaces" -ResourceNameContains $workspaceName

# Perform configuration
Add-AzureDiagnosticsToLogAnalytics $resource $workspace

# Enable the Log Analytics solution
Set-AzureRmOperationalInsightsIntelligencePack -ResourceGroupName $workspace.ResourceGroupName -WorkspaceName $workspace.Name -intelligencepackname KeyVault -Enabled $true

```
Après exécution de ce script, vous devriez voir les enregistrements dans Log Analytics environ 30 minutes après l’écriture de nouvelles données de diagnostic dans le stockage. Si les enregistrements ne sont pas disponibles après ce délai, consultez la section de dépannage ci-dessous.  

>[AZURE.NOTE] La configuration n’est pas visible dans le portail Azure. Vous pouvez vérifier la configuration à l’aide de l’applet de commande `Get-AzureRmOperationalInsightsStorageInsight` .  


## <a name="stopping-log-analytics-from-collecting-azure-diagnostic-logs"></a>Arrêt de la collecte des journaux de diagnostic Azure par Log Analytics

Pour supprimer la configuration Log Analytics pour une ressource, utilisez l’applet de commande `Remove-AzureRmOperationalInsightsStorageInsight`.

## <a name="troubleshooting-configuration-for-azure-diagnostic-logs"></a>Résolution des problèmes de configuration pour les journaux de diagnostic Azure

*Problème*

L’erreur suivante s’affiche lors de la configuration d’une ressource qui se connecte au stockage classique :

```
Select-AzureSubscription : The subscription id 7691b0d1-e786-4757-857c-7360e61896c3 doesn't exist.

Parameter name: id
```

*Résolution :*

Connectez-vous à l’API pour le modèle de déploiement classique avec `Add-AzureAccount`.

## <a name="troubleshooting-data-collection-for-azure-diagnostic-logs"></a>Résolution des problèmes de collecte de données pour les journaux de diagnostic Azure

Si vous ne voyez pas de données pour votre ressource Azure dans Log Analytics, vous pouvez suivre les étapes de résolution de problèmes suivantes :

+ Vérifier que les données circulent vers le compte de stockage
+ Vérifier que la solution Log Analytics est activée pour le service
+ Vérifier que Log Analytics est configuré pour lire à partir du stockage
+ Mettre à jour la clé du compte de stockage

### <a name="verify-data-is-flowing-to-the-storage-account"></a>Vérifier que les données circulent vers le compte de stockage

Vous pouvez vérifier si des données circulent vers le compte de stockage avec un outil permettant de parcourir Stockage Azure (par exemple, Visual Studio) ou à l’aide de PowerShell.

Pour trouver le compte de stockage auquel la ressource est configurée pour se connecter, utilisez la commande PowerShell suivante :

`Find-AzureRmResource -ResourceType "Microsoft.KeyVault/Vaults" | select ResourceId | Get-AzureRmDiagnosticSetting `

Le conteneur de stockage utilisé par les diagnostics Azure a un nom au format :  

`insights-logs-<Category>`

Pour en savoir plus sur l’affichage du contenu du compte de stockage, voir [Utilisation des applets de commande de stockage pour trouver des données récentes dans un conteneur](../storage/storage-powershell-guide-full.md).

### <a name="verify-the-log-analytics-solution-for-the-service-is-enabled"></a>Vérifier que la solution Log Analytics est activée pour le service

Si vous utilisez `Add-AzureDiagnosticsToLogAnalyticsUI` , la solution Log Analytics adéquate est activée automatiquement pour vous.

Pour vérifier si une solution est activée, exécutez la commande PowerShell suivante :

`Get-AzureRmOperationalInsightsIntelligencePacks -ResourceGroupName $logAnalyticsResourceGroup -WorkspaceName $logAnalyticsWorkspaceName`

Si la solution n’est pas activée, vous pouvez l’activer à l’aide de la commande PowerShell suivante :

`Set-AzureRmOperationalInsightsIntelligencePack -ResourceGroupName $logAnalyticsResourceGroup -WorkspaceName $logAnalyticsWorkspaceName -IntelligencePackName $solution -Enabled $true`

Pour trouver le nom de la solution à activer pour chaque type de ressource, utilisez la commande PowerShell suivante (cette variable est disponible dès que vous avez importé le module) :

`$MonitorableResourcesToOMSSolutions`

### <a name="verify-that-log-analytics-is-configured-to-read-from-storage"></a>Vérifier que Log Analytics est configuré pour lire à partir du stockage

Si vous ajoutez des ressources Azure, vous devez activer la journalisation des diagnostics et configurer Log Analytics pour celles-ci.
Pour vérifier les comptes de stockage et les ressources pour lesquels Log Analytics est configuré pour collecter les journaux, utilisez la commande PowerShell suivante :

```
# Find the Workspace ResourceGroup and Name
$logAnalyticsWorkspace = Get-AzureRmOperationalInsightsWorkspace

#Get the configuration for all resources:
Get-AzureRmOperationalInsightsStorageInsight -ResourceGroupName $logAnalyticsWorkspace.ResourceGroupName -WorkspaceName $logAnalyticsWorkspace.Name

#Get the just the resources configured:
Get-AzureRmOperationalInsightsStorageInsight -ResourceGroupName $logAnalyticsWorkspace.ResourceGroupName -WorkspaceName $logAnalyticsWorkspace.Name | select Containers
```

### <a name="update-the-storage-key"></a>Mettre à jour la clé de stockage

Si vous modifiez la clé du compte de stockage, la configuration Log Analytics doit également être mise à jour avec la nouvelle clé.
Vous pouvez mettre à jour la configuration Log Analytics avec une nouvelle clé à l’aide de la commande PowerShell suivante :

`Set-AzureRmOperationalInsightsStorageInsight -ResourceGroupName $logAnalyticsWorkspace.ResourceGroupName -WorkspaceName $logAnalyticsWorkspace.Name –Name <Storage Insight Name> -StorageAccountKey $newKey `

Pour rechercher le nom Storage Insight, utilisez l’applet de commande `Get-AzureRmOperationalInsightsStorageInsight` , comme l’indiquent les exemples précédents.

## <a name="next-steps"></a>Étapes suivantes

- [Utiliser un Stockage Blob pour IIS et un Stockage Table pour les événements](log-analytics-azure-storage-iis-table.md) afin de lire les journaux pour les services Azure qui écrivent des diagnostics dans Stockage Table ou les journaux IIS écrits dans le Stockage Blob.
- [Activer les solutions](log-analytics-add-solutions.md) pour fournir des informations sur les données.
- [Utiliser les requêtes de recherche](log-analytics-log-searches.md) pour analyser les données.



<!--HONumber=Oct16_HO2-->


