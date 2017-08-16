---
title: "Présentation des journaux de diagnostic Azure | Microsoft Docs"
description: "Découvrez les journaux de diagnostic Azure et comment les utiliser pour comprendre les événements qui se produisent au sein d’une ressource Azure."
author: johnkemnetz
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: fe8887df-b0e6-46f8-b2c0-11994d28e44f
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/02/2017
ms.author: johnkem; magoedte
ms.translationtype: HT
ms.sourcegitcommit: 8b857b4a629618d84f66da28d46f79c2b74171df
ms.openlocfilehash: 8961676a60d922912e383937ca38c5d2f89a348a
ms.contentlocale: fr-fr
ms.lasthandoff: 08/04/2017

---
# <a name="collect-and-consume-log-data-from-your-azure-resources"></a>Collecter et utiliser des données de journaux à partir de vos ressources Azure

## <a name="what-are-azure-resource-diagnostic-logs"></a>Présentation des journaux de diagnostic des ressources Azure
Les **journaux de diagnostic des ressources Azure** sont des journaux émis par une ressource, qui fournissent des informations détaillées sur l’utilisation de celle-ci, à intervalles réguliers. Le contenu de ces journaux varie en fonction du type de ressource. Les compteurs de règles du groupe de sécurité réseau et les audits de coffres de clés sont deux exemples de catégories de journaux de ressource.

Les journaux de diagnostic des ressources ne fournissent pas les mêmes informations que le [Journal d’activité](monitoring-overview-activity-logs.md). Le journal d’activité fournit un aperçu des opérations qui ont été effectuées sur les ressources de votre abonnement à l’aide de Resource Manager (par exemple, la création d’une machine virtuelle ou la suppression d’une application logique). Il s’intéresse aux opérations effectuées au niveau de l’abonnement. Les journaux de diagnostic des ressources, quant à eux, donnent un aperçu des opérations qui ont été effectuées au sein d’une ressource (par exemple, l’obtention d’un secret à partir d’un coffre de clés).

Les journaux de diagnostic des ressources diffèrent également des journaux de diagnostic de système d’exploitation invité. Les journaux de diagnostic de système d’exploitation invité sont collectés par un agent exécuté sur une machine virtuelle ou un autre type de ressource pris en charge. Les journaux de diagnostic des ressources ne nécessitent aucun agent et capturent les données relatives à la ressource à partir de la plateforme Azure, alors que les journaux de diagnostic du système d’exploitation invité capturent les données provenant du système d’exploitation et des applications exécutées sur la machine virtuelle.

Le nouveau type de journal de diagnostic décrit ici n’est cependant pas pris en charge par toutes les ressources. Cet article comprend une section répertoriant les types de ressources qui prennent en charge les nouveaux journaux de diagnostic des ressources.

![Comparaison des journaux de diagnostic des ressources et des autres types de journaux ](./media/monitoring-overview-of-diagnostic-logs/Diagnostics_Logs_vs_other_logs_v5.png)

Figure 1 : Comparaison des journaux de diagnostic des ressources et des autres types de journaux

## <a name="what-you-can-do-with-resource-level-diagnostic-logs"></a>Comment utiliser les journaux de diagnostic des ressources
Voici ce que vous pouvez faire avec les journaux de diagnostic des ressources :

![Positionnement logique des journaux de diagnostic des ressources](./media/monitoring-overview-of-diagnostic-logs/Diagnostics_Logs_Actions.png)


* Enregistrez-les dans un [**compte de stockage**](monitoring-archive-diagnostic-logs.md) pour l’audit ou l’inspection manuelle. Vous pouvez spécifier la durée de rétention (en jours) à l’aide des **paramètres de diagnostic des ressources**.
* [Diffusez-les en streaming sur **Event Hubs**](monitoring-stream-diagnostic-logs-to-event-hubs.md) pour qu’un service tiers ou une solution d’analyse personnalisée (comme PowerBI) les ingère.
* Analysez-les avec [OMS Log Analytics](../log-analytics/log-analytics-azure-storage.md)

Vous pouvez utiliser un compte de stockage ou un espace de noms Event Hubs qui n’est pas dans le même abonnement que celui générant des journaux. L’utilisateur qui configure le paramètre doit disposer d’un accès RBAC approprié aux deux abonnements.

## <a name="resource-diagnostic-settings"></a>Paramètres de diagnostic des ressources
Les journaux de diagnostic des ressources non liées au calcul sont configurés à l’aide des paramètres de diagnostic des ressources. Les **paramètres de diagnostic des ressources** permettent de contrôler :

* Où les journaux de diagnostic des ressources sont envoyés (compte de stockage, Event Hubs et/ou OMS Log Analytics)
* Les catégories de journal qui sont envoyées
* La durée pendant laquelle chaque catégorie de journal doit être conservée dans un compte de stockage
    - Une durée de rétention de zéro jour signifie que les journaux sont conservés indéfiniment. La valeur peut également être n’importe quel nombre de jours, compris entre 1 et 2147483647.
    - Si des stratégies de rétention sont définies, mais que le stockage des journaux dans un compte de stockage est désactivé (par exemple si seules les options Event Hubs ou OMS sont sélectionnées), les stratégies de rétention n’ont aucun effet.
    - Les stratégies de rétention sont appliquées sur une base quotidienne. Donc, à la fin d’une journée (UTC), les journaux de la journée qui est désormais au-delà de la stratégie de rétention sont supprimés. Par exemple, si vous aviez une stratégie de rétention d’une journée, au début de la journée d’aujourd’hui les journaux d’avant-hier seront supprimés.

Ces paramètres peuvent être facilement configurés via le panneau Diagnostics d’une ressource dans le portail Azure, via les commandes de l’interface de ligne de commande et d’Azure PowerShell, ou via [l’API REST Azure Monitor](https://msdn.microsoft.com/library/azure/dn931943.aspx).

> [!WARNING]
> Les mesures et les journaux de diagnostic des ressources Compute (comme les machines virtuelles ou Service Fabric) utilisent [un mécanisme distinct pour la configuration et la sélection des sorties](../azure-diagnostics.md).
>
>

## <a name="how-to-enable-collection-of-resource-diagnostic-logs"></a>Comment activer la collecte des journaux de diagnostic des ressources
La collecte des journaux de diagnostic des ressources peut être activée [dans le cadre de la création d’une ressource à l’aide d’un modèle Resource Manager](./monitoring-enable-diagnostic-logs-using-template.md), ou après avoir créé une ressource via le panneau d’une ressource dans le portail. Vous pouvez également activer la collecte à tout moment via les commandes de l’interface de ligne de commande ou d’Azure PowerShell, ou via l’API REST Azure Monitor.

> [!TIP]
> Ces instructions peuvent ne pas s’appliquer directement à toutes les ressources. Consultez les liens de schéma en bas de cette page pour comprendre les étapes spécifiques qui peuvent concerner certains types de ressources.
>
>

### <a name="enable-collection-of-resource-diagnostic-logs-in-the-portal"></a>Activer la collecte des journaux de diagnostic des ressources dans le portail
Vous pouvez activer les journaux de diagnostic des ressources dans le portail Azure après la création d’une ressource, en effectuant les étapes suivantes :

1. Accédez au panneau de la ressource et ouvrez le panneau **Diagnostics** .
2. Cliquez sur **Activé** et choisissez un compte de stockage et/ou un hub d’événements.

   ![Activer les journaux de diagnostic après la création de ressources](./media/monitoring-overview-of-diagnostic-logs/enable-portal-existing.png)
3. Sous **Journaux**, sélectionnez les **catégories de journaux** que vous souhaitez collecter ou diffuser en streaming.
4. Cliquez sur **Enregistrer**.

### <a name="enable-collection-of-resource-diagnostic-logs-via-powershell"></a>Activer la collecte des journaux de diagnostic des ressources via PowerShell
Pour activer la collecte des journaux de diagnostic des ressources via Azure PowerShell, utilisez les commandes suivantes :

Pour activer le stockage des journaux de diagnostic dans un compte de stockage, utilisez cette commande :

```powershell
Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -StorageAccountId [your storage account id] -Enabled $true
```

L’ID de compte de stockage est l’ID de ressource du compte de stockage auquel vous souhaitez envoyer les journaux.

Pour activer le streaming des journaux de diagnostic vers un hub d’événements, utilisez cette commande :

```powershell
Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -ServiceBusRuleId [your Service Bus rule id] -Enabled $true
```

L’ID de règle Service Bus est une chaîne au format suivant : `{Service Bus resource ID}/authorizationrules/{key name}`.

Pour activer l’envoi des journaux de diagnostic vers un espace de travail Log Analytics, utilisez cette commande :

```powershell
Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -WorkspaceId [resource id of the log analytics workspace] -Enabled $true
```

Vous pouvez obtenir l’ID de ressource de votre espace de travail Log Analytics à l’aide de la commande suivante :

```powershell
(Get-AzureRmOperationalInsightsWorkspace).ResourceId
```

Vous pouvez combiner ces paramètres pour activer plusieurs options de sortie.

### <a name="enable-collection-of-resource-diagnostic-logs-via-cli"></a>Activer la collecte des journaux de diagnostic des ressources via l’interface de ligne de commande (CLI)
Pour activer la collecte des journaux de diagnostic des ressources via Azure CLI, utilisez les commandes suivantes :

Pour activer le stockage des journaux de diagnostic dans un compte de stockage, utilisez cette commande :

```azurecli
azure insights diagnostic set --resourceId <resourceId> --storageId <storageAccountId> --enabled true
```

L’ID de compte de stockage est l’ID de ressource du compte de stockage auquel vous souhaitez envoyer les journaux.

Pour activer le streaming des journaux de diagnostic vers un hub d’événements, utilisez cette commande :

```azurecli
azure insights diagnostic set --resourceId <resourceId> --serviceBusRuleId <serviceBusRuleId> --enabled true
```

L’ID de règle Service Bus est une chaîne au format suivant : `{Service Bus resource ID}/authorizationrules/{key name}`.

Pour activer l’envoi des journaux de diagnostic vers un espace de travail Log Analytics, utilisez cette commande :

```azurecli
azure insights diagnostic set --resourceId <resourceId> --workspaceId <resource id of the log analytics workspace> --enabled true
```

Vous pouvez combiner ces paramètres pour activer plusieurs options de sortie.

### <a name="enable-collection-of-resource-diagnostic-logs-via-rest-api"></a>Activer la collecte des journaux de diagnostic des ressources via l’API REST
Pour modifier les paramètres de diagnostic via l’API REST Azure Monitor, consultez [ce document](https://msdn.microsoft.com/library/azure/dn931931.aspx).

## <a name="manage-resource-diagnostic-settings-in-the-portal"></a>Gérer les paramètres de diagnostic des ressources dans le portail
Assurez-vous que toutes vos ressources sont configurées avec des paramètres de diagnostic. Accédez au panneau **Surveillance** dans le portail et ouvrez le panneau **Journaux de Diagnostic**.

![Panneau Journaux de diagnostic dans le portail](./media/monitoring-overview-of-diagnostic-logs/manage-portal-nav.png)

Vous devrez peut-être cliquer sur « Plus de services » pour trouver le panneau Analyse.

Dans ce panneau, vous pouvez afficher et filtrer toutes les ressources qui prennent en charge les journaux de diagnostic pour voir si les diagnostics y sont activés. Vous pouvez également vérifier le compte de stockage, le hub d’événements et/ou l’espace de travail Log Analytics vers lesquels ces journaux circulent.

![Résultats du panneau Journaux de diagnostic dans le portail](./media/monitoring-overview-of-diagnostic-logs/manage-portal-blade.png)

Cliquer sur une ressource affiche tous les journaux qui ont été stockés dans le compte de stockage et vous donne la possibilité de désactiver ou modifier les paramètres de diagnostic. Cliquez sur l’icône de téléchargement pour télécharger les journaux sur une période donnée.

![Panneau Journaux de diagnostic pour une ressource](./media/monitoring-overview-of-diagnostic-logs/manage-portal-logs.png)

> [!NOTE]
> Les journaux de diagnostic s’affichent dans cette vue uniquement et sont disponibles pour téléchargement si vous avez configuré les paramètres de diagnostic pour les enregistrer dans un compte de stockage.
>
>

Cliquer sur le lien pour les **Paramètres de diagnostic** affiche le panneau Paramètres de diagnostic, où vous pouvez activer, désactiver ou modifier vos paramètres de diagnostic pour la ressource sélectionnée.

## <a name="supported-services-and-schema-for-resource-diagnostic-logs"></a>Schéma et services pris en charge pour les journaux de diagnostic des ressources
Le schéma des journaux de diagnostic des ressources varie en fonction de la ressource et de la catégorie de journal.   

| Service | Schéma et documentation |
| --- | --- |
| API Management | Schéma non disponible. |
| Passerelles d’application |[Journalisation des diagnostics pour Application Gateway](../application-gateway/application-gateway-diagnostics.md) |
| Azure Automation |[Log Analytics pour Azure Automation](../automation/automation-manage-send-joblogs-log-analytics.md) |
| Azure Batch |[Journalisation des diagnostics Azure Batch](../batch/batch-diagnostics.md) |
| Insights client | Schéma non disponible. |
| Réseau de distribution de contenu | Schéma non disponible. |
| CosmosDB | Schéma non disponible. |
| Data Lake Analytics |[Accès aux journaux de diagnostic d’Azure Data Lake Analytics](../data-lake-analytics/data-lake-analytics-diagnostic-logs.md) |
| Data Lake Store |[Accès aux journaux de diagnostic d’Azure Data Lake Store](../data-lake-store/data-lake-store-diagnostic-logs.md) |
| Event Hubs |[Journaux de diagnostic d’Azure Event Hubs](../event-hubs/event-hubs-diagnostic-logs.md) |
| Key Vault |[Journalisation d’Azure Key Vault](../key-vault/key-vault-logging.md) |
| Load Balancer |[Analytique des journaux de l'équilibreur de charge Azure](../load-balancer/load-balancer-monitor-log.md) |
| Logic Apps |[Schéma de suivi personnalisé Logic Apps B2B](../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md) |
| Network Security Group |[Analytique des journaux pour les groupes de sécurité réseau (NSG)](../virtual-network/virtual-network-nsg-manage-log.md) |
| Recovery Services | Schéma non disponible.|
| Recherche |[Activation et utilisation de la fonctionnalité Rechercher l’analyse du trafic](../search/search-traffic-analytics.md) |
| Gestion de serveur | Schéma non disponible. |
| Service Bus |[Journaux de diagnostic Azure Service Bus](../service-bus-messaging/service-bus-diagnostic-logs.md) |
| Stream Analytics |[Journaux de diagnostic des travaux](../stream-analytics/stream-analytics-job-diagnostic-logs.md) |

## <a name="supported-log-categories-per-resource-type"></a>Catégories de journaux prises en charge par type de ressource
|Type de ressource|Catégorie|Nom d’affichage de la catégorie|
|---|---|---|
|Microsoft.ApiManagement/service|GatewayLogs|Journaux relatifs à la passerelle ApiManagement|
|Microsoft.Automation/automationAccounts|JobLogs|Journaux de travail|
|Microsoft.Automation/automationAccounts|JobStreams|Flux de travail|
|Microsoft.Automation/automationAccounts|DscNodeStatus|État du nœud DSC|
|Microsoft.Batch/batchAccounts|ServiceLog|Journaux de service|
|Microsoft.Cdn/profiles/endpoints|CoreAnalytics|Obtient les métriques du point de terminaison, par exemple, la bande passante, la sortie, etc.|
|Microsoft.CustomerInsights/hubs|AuditEvents|AuditEvents|
|Microsoft.DataLakeAnalytics/accounts|Audit|Journaux d’audit|
|Microsoft.DataLakeAnalytics/accounts|Requêtes|Journaux de requête|
|Microsoft.DataLakeStore/accounts|Audit|Journaux d’audit|
|Microsoft.DataLakeStore/accounts|Requêtes|Journaux de requête|
|Microsoft.DocumentDB/databaseAccounts|DataPlaneRequests|DataPlaneRequests|
|Microsoft.EventHub/namespaces|ArchiveLogs|Journaux d’archivage|
|Microsoft.EventHub/namespaces|OperationalLogs|Journaux des opérations|
|Microsoft.EventHub/namespaces|AutoScaleLogs|Journaux de mise à l’échelle automatique|
|Microsoft.KeyVault/vaults|AuditEvent|Journaux d’audit|
|Microsoft.Logic/workflows|WorkflowRuntime|Événements de diagnostic de runtime de workflow|
|Microsoft.Logic/integrationAccounts|IntegrationAccountTrackingEvents|Suivi des événements de compte d’intégration|
|Microsoft.Network/networksecuritygroups|NetworkSecurityGroupEvent|Événement de groupe de sécurité réseau|
|Microsoft.Network/networksecuritygroups|NetworkSecurityGroupRuleCounter|Compteur de règle de groupe de sécurité réseau|
|Microsoft.Network/loadBalancers|LoadBalancerAlertEvent|Événements d’alerte d’équilibreur de charge|
|Microsoft.Network/loadBalancers|LoadBalancerProbeHealthStatus|État d’intégrité de la sonde d’équilibreur de charge|
|Microsoft.Network/applicationGateways|ApplicationGatewayAccessLog|Journal d’accès à la passerelle d’application|
|Microsoft.Network/applicationGateways|ApplicationGatewayPerformanceLog|Journal de performance de la passerelle d’application|
|Microsoft.Network/applicationGateways|ApplicationGatewayFirewallLog|Journal de pare-feu de la passerelle d’application|
|Microsoft.RecoveryServices/Vaults|AzureBackupReport|Données de rapport de sauvegarde Azure|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryJobs|Travaux Azure Site Recovery|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryEvents|Événements Azure Site Recovery|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryReplicatedItems|Éléments répliqués d’Azure Site Recovery|
|Microsoft.Search/searchServices|OperationLogs|Journaux des opérations|
|Microsoft.ServiceBus/namespaces|OperationalLogs|Journaux des opérations|
|Microsoft.StreamAnalytics/streamingjobs|Exécution|Exécution|
|Microsoft.StreamAnalytics/streamingjobs|Création|Création|

## <a name="next-steps"></a>Étapes suivantes

* [Diffuser en continu les journaux de diagnostic des ressources vers **Event Hubs**](monitoring-stream-diagnostic-logs-to-event-hubs.md)
* [Modifier les paramètres de diagnostic des ressources via l’API REST Azure Monitor](https://msdn.microsoft.com/library/azure/dn931931.aspx)
* [Analyser les journaux du stockage Azure avec Log Analytics](../log-analytics/log-analytics-azure-storage.md)

