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
ms.date: 07/05/2017
ms.author: johnkem; magoedte
ms.translationtype: HT
ms.sourcegitcommit: cddb80997d29267db6873373e0a8609d54dd1576
ms.openlocfilehash: 2517972b63bbd1a552fe591e937c9e34db580865
ms.contentlocale: fr-fr
ms.lasthandoff: 07/18/2017

---
# <a name="collect-and-consume-diagnostic-data-from-your-azure-resources"></a>Collecte et utilisation des données de diagnostic à partir de vos ressources Azure

## <a name="what-are-azure-diagnostic-logs"></a>Présentation des journaux de diagnostic Azure
Les **journaux de diagnostic Azure** sont des journaux émis par une ressource qui fournissent des informations riches et fréquentes relatives à l’opération de cette ressource. Le contenu de ces journaux varie en fonction du type de ressource. Par exemple, les journaux des événements système Windows sont une catégorie de journal de diagnostic pour les machines virtuelles et les objets blob, les tables, et les journaux de file d’attente sont les catégories de journaux de diagnostic pour les comptes de stockage.

Les journaux de diagnostic diffèrent du [journal d’activité (anciennement appelé journal d’audit ou journal des opérations)](monitoring-overview-activity-logs.md). Le journal d’activité fournit des informations sur les opérations qui ont été effectuées sur les ressources de votre abonnement. Les journaux de diagnostic fournissent des informations sur les opérations effectuées par votre ressource.

Toutes les ressources ne prennent pas en charge le nouveau type de journal de diagnostic décrit ici. Cet article contient une section répertoriant les types de ressources prenant en charge les nouveaux journaux de diagnostic.

![Journaux de diagnostics et autres types de journaux ](./media/monitoring-overview-of-diagnostic-logs/Diagnostics_Logs_vs_other_logs_v5.png)

Figure 1 : Journaux de diagnostics et autres types de journaux

## <a name="what-you-can-do-with-diagnostic-logs"></a>Ce que vous pouvez faire avec les journaux de diagnostic
Voici ce que vous pouvez faire avec les journaux de diagnostic :

![Positionnement logique des journaux de diagnostic](./media/monitoring-overview-of-diagnostic-logs/Diagnostics_Logs_Actions.png)


* Enregistrez-les dans un [**compte de stockage**](monitoring-archive-diagnostic-logs.md) pour l’audit ou l’inspection manuelle. Vous pouvez spécifier la durée de rétention (en jours) à l’aide des **paramètres de diagnostic**.
* [Diffusez-les en streaming sur **Event Hubs**](monitoring-stream-diagnostic-logs-to-event-hubs.md) pour qu’un service tiers ou une solution d’analyse personnalisée (comme PowerBI) les ingère.
* Analysez-les avec [OMS Log Analytics](../log-analytics/log-analytics-azure-storage.md)

Vous pouvez utiliser un compte de stockage ou un espace de noms Event Hubs qui n’est pas dans le même abonnement que celui générant des journaux. L’utilisateur qui configure le paramètre doit disposer d’un accès RBAC approprié aux deux abonnements.

## <a name="diagnostic-settings"></a>Paramètres de diagnostic
Les journaux de diagnostic pour les ressources non liées au calcul sont configurés à l’aide des paramètres de diagnostic. **Paramètres de diagnostic** pour un contrôle de ressource :

* L’emplacement où les journaux de diagnostic sont envoyés (compte de stockage, Event Hubs et/ou OMS Log Analytics).
* Les catégories de journal qui sont envoyées.
* Durée pendant laquelle chaque catégorie de journal doit être conservée dans un compte de stockage
    - Une durée de rétention de zéro jour signifie que les journaux sont conservés indéfiniment. La valeur peut également être n’importe quel nombre de jours, compris entre 1 et 2147483647.
    - Si des stratégies de rétention sont définies, mais que le stockage des journaux dans un compte de stockage est désactivé (par exemple si seules les options Event Hubs ou OMS sont sélectionnées), les stratégies de rétention n’ont aucun effet.
    - Les stratégies de rétention sont appliquées sur une base quotidienne. Donc, à la fin d’une journée (UTC), les journaux de la journée qui est désormais au-delà de la stratégie de rétention sont supprimés. Par exemple, si vous aviez une stratégie de rétention d’une journée, au début de la journée d’aujourd’hui les journaux d’avant-hier seront supprimés.

Ces paramètres sont facilement configurés via le panneau Diagnostics pour une ressource dans le portail Azure, via les commandes d’interface de ligne de commande et Azure PowerShell ou via [l’API REST Azure Monitor](https://msdn.microsoft.com/library/azure/dn931943.aspx).

> [!WARNING]
> Les mesures et les journaux de diagnostic des ressources Compute (comme les machines virtuelles ou Service Fabric) utilisent [un mécanisme distinct pour la configuration et la sélection des sorties](../azure-diagnostics.md).
>
>

## <a name="how-to-enable-collection-of-diagnostic-logs"></a>Comment activer la collecte des journaux de diagnostic
La collecte des journaux de diagnostic peut être activée dans le cadre de la création d’une ressource, ou après avoir créé une ressource via le panneau des ressources dans le portail. Vous pouvez également activer les journaux de diagnostic à tout moment via les commandes d’interface de ligne de commande ou Azure PowerShell ou via l’API REST Azure Monitor.

> [!TIP]
> Ces instructions peuvent ne pas s’appliquer directement à toutes les ressources. Consultez les liens de schéma en bas de cette page pour comprendre les étapes spécifiques qui peuvent concerner certains types de ressources.
>
>

[Cet article vous explique comment utiliser un modèle de ressource pour activer les paramètres de diagnostic lors de la création d’une ressource](monitoring-enable-diagnostic-logs-using-template.md)

### <a name="enable-diagnostic-logs-in-the-portal"></a>Activation des journaux de diagnostic dans le portail
Vous pouvez activer les journaux de diagnostic dans le portail Azure lorsque vous créez des types de ressources de calcul en activant l’extension Azure Diagnostics Windows ou Linux :

1. Accédez à **Nouveau** et choisissez la ressource qui vous intéresse.
2. Après avoir configuré les paramètres de base et sélectionné une taille, dans le panneau **Paramètres**, sous **Analyse**, sélectionnez **Activé** et choisissez un compte de stockage dans lequel vous souhaitez stocker les journaux de diagnostic. Des frais de données normaux vous sont facturés pour le stockage et les transactions lorsque vous envoyez des diagnostics à un compte de stockage.

   ![Activer les journaux de diagnostic lors de la création de ressources](./media/monitoring-overview-of-diagnostic-logs/enable-portal-new.png)
3. Cliquez sur **OK** et créez la ressource.

Pour les ressources non calculées, vous pouvez activer les journaux de diagnostic dans le portail Azure après la création d’une ressource, en procédant comme suit :

1. Accédez au panneau de la ressource et ouvrez le panneau **Diagnostics** .
2. Cliquez sur **Activé** et choisissez un compte de stockage et/ou un hub d’événements.

   ![Activer les journaux de diagnostic après la création de ressources](./media/monitoring-overview-of-diagnostic-logs/enable-portal-existing.png)
3. Sous **Journaux**, sélectionnez les **catégories de journaux** que vous souhaitez collecter ou diffuser en streaming.
4. Cliquez sur **Enregistrer**.

### <a name="enable-diagnostic-logs-via-powershell"></a>Activation des journaux de diagnostic via PowerShell
Pour activer les journaux de diagnostic via les applets de commande Azure PowerShell, utilisez les commandes suivantes.

Pour activer le stockage des journaux de diagnostic dans un compte de stockage, utilisez cette commande :

```powershell
Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -StorageAccountId [your storage account id] -Enabled $true
```

L’ID de compte de stockage est l’ID de ressource pour le compte de stockage auquel vous souhaitez envoyer les journaux.

Pour activer la diffusion en continu des journaux de diagnostic vers un hub d’événements, utilisez cette commande :

```powershell
Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -ServiceBusRuleId [your Service Bus rule id] -Enabled $true
```

L’ID de règle Service Bus est une chaîne au format : `{Service Bus resource ID}/authorizationrules/{key name}`.

Pour activer l’envoi des journaux de diagnostic à un espace de travail Log Analytics, utilisez cette commande :

```powershell
Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -WorkspaceId [resource id of the log analytics workspace] -Enabled $true
```

Vous pouvez obtenir l’ID de ressource de votre espace de travail Log Analytics à l’aide de la commande suivante :

```powershell
(Get-AzureRmOperationalInsightsWorkspace).ResourceId
```

Vous pouvez combiner ces paramètres pour activer plusieurs options de sortie.

### <a name="enable-diagnostic-logs-via-cli"></a>Activation des journaux de diagnostic via l’interface de ligne de commande
Pour activer les journaux de diagnostic via l’interface de ligne de commande Azure, utilisez les commandes suivantes :

Pour activer le stockage des journaux de diagnostic dans un compte de stockage, utilisez cette commande :

```azurecli
azure insights diagnostic set --resourceId <resourceId> --storageId <storageAccountId> --enabled true
```

L’ID de compte de stockage est l’ID de ressource pour le compte de stockage auquel vous souhaitez envoyer les journaux.

Pour activer la diffusion en continu des journaux de diagnostic vers un hub d’événements, utilisez cette commande :

```azurecli
azure insights diagnostic set --resourceId <resourceId> --serviceBusRuleId <serviceBusRuleId> --enabled true
```

L’ID de règle Service Bus est une chaîne au format : `{Service Bus resource ID}/authorizationrules/{key name}`.

Pour activer l’envoi des journaux de diagnostic à un espace de travail Log Analytics, utilisez cette commande :

```azurecli
azure insights diagnostic set --resourceId <resourceId> --workspaceId <resource id of the log analytics workspace> --enabled true
```

Vous pouvez combiner ces paramètres pour activer plusieurs options de sortie.

### <a name="enable-diagnostic-logs-via-rest-api"></a>Activation des journaux de diagnostic via l’API REST
Pour modifier les paramètres de diagnostic via l’API REST Azure Monitor, consultez [ce document](https://msdn.microsoft.com/library/azure/dn931931.aspx).

## <a name="manage-diagnostic-settings-in-the-portal"></a>Gérer les paramètres de diagnostic dans le portail
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

## <a name="supported-services-and-schema-for-diagnostic-logs"></a>Schéma et services pris en charge pour les journaux de diagnostic
Le schéma pour les journaux de diagnostic varie en fonction de la ressource et de la catégorie de journal.   

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
| Load Balancer |[Analyse des journaux de l'équilibreur de charge Azure](../load-balancer/load-balancer-monitor-log.md) |
| Logic Apps |[Schéma de suivi personnalisé Logic Apps B2B](../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md) |
| Groupes de sécurité réseau |[Analyse de journaux pour les groupes de sécurité réseau (NSG)](../virtual-network/virtual-network-nsg-manage-log.md) |
| Recovery Services | Schéma non disponible.|
| Search |[Activation et utilisation de la fonctionnalité Rechercher l’analyse du trafic](../search/search-traffic-analytics.md) |
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

* [Diffuser en streaming les journaux de diagnostic sur **Event Hubs**](monitoring-stream-diagnostic-logs-to-event-hubs.md)
* [Modification des paramètres de diagnostic via l’API REST Azure Monitor](https://msdn.microsoft.com/library/azure/dn931931.aspx)
* [Analyser les journaux du stockage Azure avec Log Analytics](../log-analytics/log-analytics-azure-storage.md)

