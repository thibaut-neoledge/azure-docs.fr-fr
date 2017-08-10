---
title: "Collecte des journaux et des métriques des services Azure pour Log Analytics | Microsoft Docs"
description: "Configuration des diagnostics sur les ressources Azure pour écrire des journaux et métriques dans Log Analytics."
services: log-analytics
documentationcenter: 
author: MGoedtel
manager: carmonm
editor: 
ms.assetid: 84105740-3697-4109-bc59-2452c1131bfe
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/12/2017
ms.author: magoedte
ms.custom: H1Hack27Feb2017
ms.translationtype: HT
ms.sourcegitcommit: 1dbb1d5aae55a4c926b9d8632b416a740a375684
ms.openlocfilehash: 79122493a8639dba1c1d6c92072dd682dae040a7
ms.contentlocale: fr-fr
ms.lasthandoff: 08/07/2017

---
# <a name="collect-azure-service-logs-and-metrics-for-use-in-log-analytics"></a>Collecte des journaux et des métriques des services Azure à utiliser dans Log Analytics

Il existe quatre façons différentes de collecter des journaux et des métriques pour les services Azure :

1. Diagnostics Azure directement dans Log Analytics (*Diagnostics* dans le tableau suivant)
2. Diagnostics Azure vers stockage Azure, puis vers Log Analytics (*Stockage* dans le tableau suivant)
3. Connecteurs pour les services Azure (*Connecteurs* dans le tableau suivant)
4. Des scripts pour collecter puis publier les données dans Log Analytics (vide dans le tableau suivant et pour les services qui ne sont pas répertoriés)


| Service                 | Type de ressource                           | Journaux        | Mesures     | Solution |
| --- | --- | --- | --- | --- |
| Passerelles d’application    | Microsoft.Network/applicationGateways   | Diagnostics | Diagnostics | [Azure Application Gateway Analytics](log-analytics-azure-networking-analytics.md#azure-application-gateway-analytics-solution-in-log-analytics) |
| Application Insights    |                                         | Connecteur   | Connecteur   | [Connecteur Application Insights](https://blogs.technet.microsoft.com/msoms/2016/09/26/application-insights-connector-in-oms/) (version préliminaire) |
| Comptes Automation     | Microsoft.Automation/AutomationAccounts | Diagnostics |             | [Plus d’informations](../automation/automation-manage-send-joblogs-log-analytics.md)|
| Comptes Batch          | Microsoft.Batch/batchAccounts           | Diagnostics | Diagnostics | |
| Services cloud classiques  |                                         | Storage     |             | [Plus d’informations](log-analytics-azure-storage-iis-table.md) |
| Cognitive services      | Microsoft.CognitiveServices/accounts    |             | Diagnostics | |
| Data Lake analytics     | Microsoft.DataLakeAnalytics/accounts    | Diagnostics |             | |
| Data Lake Store         | Microsoft.DataLakeStore/accounts        | Diagnostics |             | |
| Espace de noms Event Hub     | Microsoft.EventHub/namespaces           | Diagnostics | Diagnostics | |
| IoT Hubs                | Microsoft.Devices/IotHubs               |             | Diagnostics | |
| Key Vault               | Microsoft.KeyVault/vaults               | Diagnostics |             | [KeyVault Analytics](log-analytics-azure-key-vault.md) |
| Équilibreurs de charge          | Microsoft.Network/loadBalancers         | Diagnostics |             |  |
| Logic Apps              | Microsoft.Logic/workflows <br> Microsoft.Logic/integrationAccounts | Diagnostics | Diagnostics | |
| Groupes de sécurité réseau | Microsoft.Network/networksecuritygroups | Diagnostics |             | [Azure Network Security Group Analytics](log-analytics-azure-networking-analytics.md#azure-network-security-group-analytics-solution-in-log-analytics) |
| Coffres de récupération         | Microsoft.RecoveryServices/vaults       |             |             | [Azure Recovery Services Analytics (version préliminaire)](https://github.com/krnese/AzureDeploy/blob/master/OMS/MSOMS/Solutions/recoveryservices/)|
| Services de recherche         | Microsoft.Search/searchServices         | Diagnostics | Diagnostics | |
| Espace de noms Service Bus   | Microsoft.ServiceBus/namespaces         | Diagnostics | Diagnostics | [Service Bus Analytics (version préliminaire)](https://github.com/Azure/azure-quickstart-templates/tree/master/oms-servicebus-solution)|
| Service Fabric          |                                         | Storage     |             | [Service Fabric Analytics (version préliminaire)](log-analytics-service-fabric.md) |
| SQL (v12)               | Microsoft.Sql/servers/databases <br> Microsoft.Sql/servers/elasticPools |             | Diagnostics | [Azure SQL Analytics (version préliminaire)](log-analytics-azure-sql.md) |
| Storage                 |                                         |             | Script      | [Azure Storage Analytics (version préliminaire)](https://github.com/Azure/azure-quickstart-templates/tree/master/oms-azure-storage-analytics-solution) |
| Machines virtuelles        | Microsoft.Compute/virtualMachines       | Extension   | Extension <br> Diagnostics  | |
| Groupes de machines virtuelles identiques | Microsoft.Compute/virtualMachines <br> Microsoft.Compute/virtualMachineScaleSets/virtualMachines |             | Diagnostics | |
| Batteries de serveurs web        | Microsoft.Web/serverfarms               |             | Diagnostics | |
| Sites web               | Microsoft.Web/sites <br> Microsoft.Web/sites/slots |             | Diagnostics | [Azure Web Apps Analytics (version préliminaire)](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.AzureWebAppsAnalyticsOMS?tab=Overview) |


> [!NOTE]
> Pour l’analyse des machines virtuelles (Linux et Windows), nous vous recommandons d’installer l’[extension de machine virtuelle Log Analytics](log-analytics-azure-vm-extension.md). L’agent vous fournit les informations collectées à partir de vos machines virtuelles. Vous pouvez également utiliser l’extension pour les jeux de mise à l’échelle de machine virtuelle.
>
>

## <a name="azure-diagnostics-direct-to-log-analytics"></a>Diagnostics Azure directement dans Log Analytics
De nombreuses ressources Azure sont en mesure d’écrire des journaux de diagnostic et métriques directement dans Log Analytics, et il s’agit du moyen recommandé de collecter les données à analyser. Lorsque vous utilisez les diagnostics Azure, les données sont écrites immédiatement dans Log Analytics, et il est inutile d’écrire les données dans le stockage avant.

Mes ressources Azure qui prennent en charge [Azure Monitor](../monitoring-and-diagnostics/monitoring-overview.md) peuvent envoyer leurs journaux et métriques directement vers Log Analytics.

* Pour plus d’informations sur les métriques disponibles, voir [Mesures prises en charge avec Azure Monitor](../monitoring-and-diagnostics/monitoring-supported-metrics.md).
* Pour plus d’informations sur les journaux disponibles, voir [Schéma et services pris en charge pour les journaux de diagnostic](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md#supported-services-and-schema-for-resource-diagnostic-logs).

### <a name="enable-diagnostics-with-powershell"></a>Activer les diagnostics avec PowerShell
Vous devez disposer de la version de novembre 2016 (v2.3.0) ou d’une version ultérieure d’[Azure PowerShell](/powershell/azure/overview).

L’exemple PowerShell suivant montre comment utiliser [Set-AzureRmDiagnosticSetting](/powershell/module/azurerm.insights/set-azurermdiagnosticsetting) pour activer les diagnostics sur un groupe de sécurité réseau. La même approche fonctionne pour toutes les ressources prises en charge : définissez `$resourceId` sur l’ID de la ressource pour laquelle vous souhaitez activer les diagnostics.

```powershell
$workspaceId = "/subscriptions/d2e37fee-1234-40b2-5678-0b2199de3b50/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/rollingbaskets"

$resourceId = "/SUBSCRIPTIONS/ec11ca60-1234-491e-5678-0ea07feae25c/RESOURCEGROUPS/DEMO/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/DEMO"

Set-AzureRmDiagnosticSetting -ResourceId $ResourceId  -WorkspaceId $workspaceId -Enabled $true
```

### <a name="enable-diagnostics-with-resource-manager-templates"></a>Activer les diagnostics avec des modèles Resource Manager

Pour activer les diagnostics sur une ressource lors de sa création, et envoyer les diagnostics vers votre espace de travail Log Analytics, vous pouvez utiliser un modèle semblable à celui ci-dessous. Cet exemple est destiné à un compte Automation, mais fonctionne pour tous les types de ressources pris en charge.

```json
        {
            "type": "Microsoft.Automation/automationAccounts/providers/diagnosticSettings",
            "name": "[concat(parameters('omsAutomationAccountName'), '/', 'Microsoft.Insights/service')]",
            "apiVersion": "2015-07-01",
            "dependsOn": [
                "[concat('Microsoft.Automation/automationAccounts/', parameters('omsAutomationAccountName'))]",
                "[concat('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspaceName'))]"
            ],
            "properties": {
                "workspaceId": "[resourceId('Microsoft.OperationalInsights/workspaces', parameters('omsWorkspaceName'))]",
                "logs": [
                    {
                        "category": "JobLogs",
                        "enabled": true
                    },
                    {
                        "category": "JobStreams",
                        "enabled": true
                    }
                ]
            }
        }
```

[!INCLUDE [log-analytics-troubleshoot-azure-diagnostics](../../includes/log-analytics-troubleshoot-azure-diagnostics.md)]

## <a name="azure-diagnostics-to-storage-then-to-log-analytics"></a>Diagnostics Azure vers stockage, puis vers Log Analytics

Pour la collecte des journaux sur certaines ressources, il est possible d’envoyer les journaux vers le stockage Azure, puis de configurer Log Analytics pour lire les journaux depuis le stockage.

Log Analytics peut utiliser cette approche pour collecter des diagnostics à partir du stockage Azure pour les journaux et les ressources qui suivent :

| Ressource | Journaux |
| --- | --- |
| Service Fabric |ETWEvent <br> Événement opérationnel <br> Événement Reliable Actor <br> Événement de service fiable |
| Machines virtuelles |Syslog Linux <br> Événement Windows <br> Journal IIS <br> ETWEvent Windows |
| Rôles web <br> Rôles de travail |Syslog Linux <br> Événement Windows <br> Journal IIS <br> ETWEvent Windows |

> [!NOTE]
> Vous êtes facturé aux tarifs de données Azure normaux pour le stockage et les transactions lorsque vous envoyez des diagnostics à un compte de stockage et lorsque Log Analytics lit les données de votre compte de stockage.
>
>

Consultez la page [Utiliser un Stockage Blob pour IIS et un Stockage Table pour les événements](log-analytics-azure-storage-iis-table.md) pour en savoir plus sur la façon dont Log Analytics peut collecter ces journaux.

## <a name="connectors-for-azure-services"></a>Connecteurs pour les services Azure

Il existe un connecteur pour Application Insights qui permet aux données collectées par Application Insights d’être envoyées vers Log Analytics.

En savoir plus sur le [connecteur Application Insights](https://blogs.technet.microsoft.com/msoms/2016/09/26/application-insights-connector-in-oms/).

## <a name="scripts-to-collect-and-post-data-to-log-analytics"></a>Scripts pour collecter et publier des données sur Log Analytics

Pour les services Azure qui ne fournissent pas de moyen direct d’envoyer les journaux et métriques vers Log Analytics, vous pouvez utiliser un script Azure Automation pour les collecter. Le script peut alors envoyer les données vers Log Analytics à l’aide de [l’API de collecte de données](log-analytics-data-collector-api.md)

La galerie de modèles Azure propose des [exemples d’utilisation d’Azure Automation](https://azure.microsoft.com/en-us/resources/templates/?term=OMS) pour collecter des données à partir des services et en les envoyer à Log Analytics.

## <a name="next-steps"></a>Étapes suivantes

* [Utiliser un Stockage Blob pour IIS et un Stockage Table pour les événements](log-analytics-azure-storage-iis-table.md) afin de lire les journaux pour les services Azure qui écrivent des diagnostics dans Stockage Table ou les journaux IIS écrits dans le Stockage Blob.
* [Activer les solutions](log-analytics-add-solutions.md) pour fournir des informations sur les données.
* [Utiliser les requêtes de recherche](log-analytics-log-searches.md) pour analyser les données.

