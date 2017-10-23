---
title: "Journalisation des métriques et diagnostics d’Azure SQL Database | Microsoft Docs"
description: "Découvrez comment configurer une ressource Azure SQL Database pour stocker les statistiques d’utilisation des ressources, de connectivité et d’exécution de requête."
services: sql-database
documentationcenter: 
author: vvasic
manager: jhubbard
editor: 
ms.assetid: 89c2a155-c2fb-4b67-bc19-9b4e03c6d3bc
ms.service: sql-database
ms.custom: monitor & tune
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/16/2017
ms.author: vvasic
ms.openlocfilehash: a56d48eaf335d9e78eeba99162cea7c61d96b7cb
ms.sourcegitcommit: 51ea178c8205726e8772f8c6f53637b0d43259c6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-sql-database-metrics-and-diagnostics-logging"></a>Journalisation des métriques et diagnostics d’Azure SQL Database 
Azure SQL Database peut émettre des journaux de métriques et de diagnostics pour faciliter la surveillance. Vous pouvez configurer Azure SQL Database pour stocker l’utilisation des ressources, les workers et sessions, ainsi que la connectivité dans l’une des ressources Azure suivantes :
- **Stockage Azure** : pour archiver des quantités importantes de données de télémétrie à un petit prix
- **Azure Event Hub** : pour intégrer des données de télémétrie Azure SQL Database à votre solution de surveillance personnalisée ou à vos pipelines très actifs
- **Azure Log Analytics** : pour une solution de surveillance prête à l’emploi avec des fonctionnalités de génération de rapports, d’alerte et d’atténuation 

    ![architecture](./media/sql-database-metrics-diag-logging/architecture.png)

## <a name="enable-logging"></a>Activation de la journalisation

La journalisation des métriques et diagnostics n’est pas activée par défaut. Vous pouvez activer et gérer la journalisation des métriques et diagnostics à l’aide de l’une des méthodes suivantes :
- Portail Azure
- PowerShell
- Interface de ligne de commande Azure
- API REST 
- Modèle Resource Manager

Lorsque vous activez la journalisation des métriques et diagnostics, vous devez spécifier la ressource Azure dans laquelle les données sélectionnées sont collectées. Options disponibles :
- Log Analytics
- Event Hub
- Stockage Azure 

Vous pouvez approvisionner une nouvelle ressource Azure ou sélectionner une ressource existante. Après avoir sélectionné la ressource de stockage, vous devez spécifier les données à collecter. Les options disponibles sont les suivantes :

- **[Métriques de 1 minute](sql-database-metrics-diag-logging.md#1-minute-metrics)** : contient Pourcentage DTU, Limite DTU, Pourcentage UC, Pourcentage de lecture de données physiques, Pourcentage d’écriture du journal, Connexions réussies/en échec/bloquées par pare-feu, Pourcentage de sessions, Pourcentage de workers, Stockage, Pourcentage de stockage, Pourcentage de stockage XTP
- **[QueryStoreRuntimeStatistics](sql-database-metrics-diag-logging.md#query-store-runtime-statistics)** : contient des informations sur les statistiques d’exécution de requête telles que l’utilisation du processeur, la durée des requêtes, etc.
- **[QueryStoreWaitStatistics](sql-database-metrics-diag-logging.md#query-store-wait-statistics)** : contient des informations sur les statistiques d’attente des requêtes vous indiquant ce que vos requêtes ont attendu comme CPU, LOG, LOCKING...
- **[Errors](sql-database-metrics-diag-logging.md#errors-dataset)** : contient des informations sur les erreurs SQL qui se sont produites dans cette base de données.
- **[DatabaseWaitStatistics](sql-database-metrics-diag-logging.md#database-waits-dataset)** : contient des informations sur le temps qu’une base de données a passé à attendre pour différents types d’attente.
- **[Timeouts](sql-database-metrics-diag-logging.md#timeouts-dataset)** : contient des informations sur le temps qu’une base de données a passé à attendre pour différents types d’attente.
- **[Blockings](sql-database-metrics-diag-logging.md#blockings-dataset)** : contient des informations sur les événements bloquants qui se sont produits dans une base de données.
- **[SQLInsights](sql-database-metrics-diag-logging.md#intelligent-insights-dataset)** : contient les informations Intelligent Insights. [En savoir plus sur Intelligent Insights](sql-database-intelligent-insights.md)

Si vous spécifiez un compte Event Hub ou Stockage Azure, vous pouvez définir une stratégie de rétention pour spécifier que les données antérieures à un intervalle de temps sélectionné sont supprimées. Si vous spécifiez Log Analytics, la stratégie de rétention dépend du niveau tarifaire sélectionné. Pour en savoir plus, voir [Tarification de Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/). 

Pour comprendre non seulement comment activer la journalisation, mais aussi les métriques et les catégories de journaux prises en charge par les différents services Azure, nous vous recommandons de lire les articles [Vue d’ensemble des mesures dans Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md) et [Présentation des journaux de diagnostic Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md).

### <a name="azure-portal"></a>Portail Azure

Pour activer la collecte des journaux de métriques et de diagnostics dans le portail Azure, accédez à votre base de données SQL Azure ou à une page de pool élastique, puis cliquez sur **Paramètres de diagnostic**.

   ![activer dans le portail Azure](./media/sql-database-metrics-diag-logging/enable-portal.png)

Créez ou modifiez les paramètres de diagnostic existants en sélectionnant la cible et la télémétrie.

   ![configurer les paramètres de diagnostic](./media/sql-database-metrics-diag-logging/diagnostics-portal.png)

### <a name="powershell"></a>PowerShell

Pour activer la journalisation des métriques et diagnostics à l’aide de PowerShell, utilisez les commandes suivantes :

- Pour activer le stockage des journaux de diagnostic dans un compte de stockage, utilisez cette commande :

   ```powershell
   Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -StorageAccountId [your storage account id] -Enabled $true
   ```

   L’ID de compte de stockage est l’ID de ressource pour le compte de stockage auquel vous souhaitez envoyer les journaux.

- Pour activer le streaming des journaux de diagnostic vers un hub d’événements, utilisez cette commande :

   ```powershell
   Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -ServiceBusRuleId [your service bus rule id] -Enabled $true
   ```

   L’ID de règle Service Bus est une chaîne au format suivant :

   ```powershell
   {service bus resource ID}/authorizationrules/{key name}
   ``` 

- Pour activer l’envoi des journaux de diagnostic à un espace de travail Log Analytics, utilisez cette commande :

   ```powershell
   Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -WorkspaceId [resource id of the log analytics workspace] -Enabled $true
   ```

- Vous pouvez obtenir l’ID de ressource de votre espace de travail Log Analytics à l’aide de la commande suivante :

   ```powershell
   (Get-AzureRmOperationalInsightsWorkspace).ResourceId
   ```

Vous pouvez combiner ces paramètres pour activer plusieurs options de sortie.

### <a name="to-configure-multiple-azure-subscriptions"></a>Pour configurer plusieurs abonnements Azure

Pour prendre en charge plusieurs abonnements, utilisez le script PowerShell de [Enable Azure resource metrics logging using PowerShell](https://blogs.technet.microsoft.com/msoms/2017/01/17/enable-azure-resource-metrics-logging-using-powershell/) (Activer la journalisation des mesures de ressources Azure à l’aide de PowerShell). Fournissez l’ID de ressource d’espace de travail en tant que paramètre lors de l’exécution du script pour transmettre les données des ressources d’un abonnement Azure vers un espace de travail d’un autre abonnement Azure.

- Pour configurer plusieurs abonnements Azure, utilisez les commandes suivantes :

    ```powershell
    PS C:\> $WSID = "/subscriptions/<subID>/resourcegroups/oms/providers/microsoft.operationalinsights/workspaces/omsws"
    PS C:\> .\Enable-AzureRMDiagnostics.ps1 -WSID $WSID
    ```

### <a name="azure-cli"></a>Interface de ligne de commande Azure

Pour activer la journalisation des métriques et diagnostics à l’aide d’Azure CLI, utilisez les commandes suivantes :

- Pour activer le stockage des journaux de diagnostic dans un compte de stockage, utilisez cette commande :

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --storageId <storageAccountId> --enabled true
   ```

   L’ID de compte de stockage est l’ID de ressource pour le compte de stockage auquel vous souhaitez envoyer les journaux.

- Pour activer le streaming des journaux de diagnostic vers un hub d’événements, utilisez cette commande :

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --serviceBusRuleId <serviceBusRuleId> --enabled true
   ```

   L’ID de règle Service Bus est une chaîne au format suivant :

   ```azurecli-interactive
   {service bus resource ID}/authorizationrules/{key name}
   ```

- Pour activer l’envoi des journaux de diagnostic à un espace de travail Log Analytics, utilisez cette commande :

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --workspaceId <resource id of the log analytics workspace> --enabled true
   ```

Vous pouvez combiner ces paramètres pour activer plusieurs options de sortie.

### <a name="rest-api"></a>API REST

Découvrez comment [modifier les paramètres de diagnostic à l’aide de l’API RESTS Azure Monitor](https://msdn.microsoft.com/library/azure/dn931931.aspx). 

### <a name="resource-manager-template"></a>Modèle Resource Manager

Découvrez comment [activer automatiquement les paramètres de diagnostic lors de la création de ressources à l’aide d’un modèle Resource Manager](../monitoring-and-diagnostics/monitoring-enable-diagnostic-logs-using-template.md). 

## <a name="stream-into-log-analytics"></a>Envoyer à Log Analytics 
Les journaux de métriques et diagnostics d’Azure SQL Database peuvent être envoyés à Log Analytics à l’aide de l’option « Envoyer à Log Analytics » intégrée dans le portail, ou en activant Log Analytics dans un paramètre de diagnostic via des applets de commande Azure PowerShell, Azure CLI ou l’API REST Azure Monitor.

### <a name="installation-overview"></a>Vue d’ensemble de l’installation

La surveillance d’une flotte Azure SQL Database est simple avec Log Analytics. Trois étapes sont requises :

1. Créer une ressource Log Analytics
2. Configurer des bases de données pour enregistrer des journaux de métriques et diagnostics dans la ressource Log Analytics créée
3. Installer la solution **Azure SQL Analytics** à partir de la galerie de Log Analytics

### <a name="create-log-analytics-resource"></a>Créer une ressource Log Analytics

1. Cliquez sur **Nouveau** dans le menu de gauche.
2. Cliquez sur **Surveillance et gestion**.
3. Cliquez sur **Log Analytics**.
4. Complétez le formulaire de Log Analytics avec les informations supplémentaires requises : nom d’espace de travail, abonnement, groupe de ressources, emplacement et niveau tarifaire.

   ![Log Analytics](./media/sql-database-metrics-diag-logging/log-analytics.png)

### <a name="configure-databases-to-record-metrics-and-diagnostic-logs"></a>Configurer des bases de données pour enregistrer des journaux de métriques et diagnostics

La manière la plus simple de configurer l’emplacement où les bases de données enregistrent leurs mesures est d’utiliser le portail Azure. Dans le portail Azure, accédez à vos ressources Azure SQL Database, puis cliquez sur **Paramètres de diagnostic**. 

### <a name="install-the-azure-sql-analytics-solution-from-gallery"></a>Installer la solution Azure SQL Analytics à partir de la galerie  

1. Une fois la ressource Log Analytics créée et vos données en circulation dans celle-ci, installez la solution Azure SQL Analytics. Vous pouvez faire cela via la **Galerie de solutions** accessible dans la page d’accueil de Microsoft Operations Management Suite et dans le menu latéral. Dans la galerie, trouvez la solution **Azure SQL Analytics**, cliquez dessus, puis cliquez sur **Ajouter**.

   ![solution de surveillance](./media/sql-database-metrics-diag-logging/monitoring-solution.png)

2. Dans votre page d’accueil de Microsoft Operations Management Suite, une nouvelle vignette intitulée **Azure SQL Analytics** s’affiche. La sélection de cette vignette a pour effet d’ouvrir le tableau de bord Azure SQL Analytics.

### <a name="using-azure-sql-analytics-solution"></a>Utilisation de la solution Azure SQL Analytics

Azure SQL Analytics est un tableau de bord hiérarchique qui vous permet de naviguer dans la hiérarchie des ressources d’Azure SQL Analytics. [Cliquez ici pour savoir comment utiliser la solution Azure SQL Analytics](../log-analytics/log-analytics-azure-sql.md).

## <a name="stream-into-azure-event-hub"></a>Envoyer à Azure Event Hub

Les journaux de métriques et diagnostics d’Azure SQL Database peuvent être envoyés à Azure Event Hub à l’aide de l’option « Diffuser vers Event Hub » intégrée dans le portail, ou en activant l’ID de règle Service Bus dans un paramètre de diagnostic via des applets de commande Azure PowerShell, Azure CLI ou l’API REST Azure Monitor. 

### <a name="what-to-do-with-metrics-and-diagnostic-logs-in-event-hub"></a>Que faire des journaux de métriques et diagnostics dans Event Hub ?
En sélectionnant les données envoyées à Event Hub, vous vous rapprochez de l’activation de scénarios d’analyse avancée. Event Hubs fonctionne comme la « porte d'entrée » d’un pipeline d’événements, et une fois que les données sont collectées dans un hub d'événements, peut être transformées et stockées à l'aide de n'importe quel fournisseur d'analyse en temps réel ou d’adaptateurs de traitement par lot ou de stockage. Les hubs d'événements dissocient la production d'un flux d'événements de la consommation de ces événements, de manière à ce que les consommateurs d'événements puissent accéder aux événements selon leur propre planification. Pour plus d’informations sur Event Hub, voir :

- [Qu’est-ce qu’Azure Event Hubs](../event-hubs/event-hubs-what-is-event-hubs.md) ?
- [Prise en main des hubs d’événements](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)


Voici quelques façons d’utiliser la fonctionnalité de streaming :

-             Afficher l’état d’intégrité du service en diffusant des données de chemin réactif vers PowerBI : en utilisant Event Hubs, Stream Analytics et PowerBI, vous pouvez facilement transformer vos données de métriques et de diagnostic en informations en temps réel sur vos services Azure. Pour une vue d’ensemble de la manière de configurer un Event Hub, de traiter les données avec Stream Analytics, et d’utiliser PowerBI comme sortie, voir [Stream Analytics et Power BI](../stream-analytics/stream-analytics-power-bi-dashboard.md).
-             Envoyer les journaux à des flux de journalisation et de télémétrie tiers : le streaming d’Event Hubs vous permet d’envoyer vos journaux de métriques et diagnostics à différentes solution tierces de surveillance et d’analytique des journaux. 
-             Créer une plateforme de journalisation et de télémétrie personnalisée : si vous disposez déjà d’une plateforme de télémétrie personnalisée, ou si vous envisagez d’en créer une, la nature hautement évolutive de publication et d’abonnement d’Event Hubs vous permet d’intégrer avec souplesse les journaux de diagnostic. Lisez le [guide de Dan Rosanova sur l’utilisation d’Event Hubs dans une plateforme de télémétrie à l’échelle mondiale](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/).

## <a name="stream-into-azure-storage"></a>Envoyer à Stockage Azure

Les journaux de métriques et diagnostics d’Azure SQL Database peuvent être stockés dans Stockage Aure à l’aide de l’option « Archiver dans un compte de stockage » intégrée dans le portail Azure, ou en activant Stockage Azure dans un paramètre de diagnostic via des applets de commande Azure PowerShell, Azure CLI ou l’API REST Azure Monitor.

### <a name="schema-of-metrics-and-diagnostic-logs-in-the-storage-account"></a>Schéma des journaux de métriques et diagnostics dans le compte de stockage

Après que vous avez configuré la collecte des journaux de métriques et diagnostics, lorsque les premières lignes de données sont disponibles, un conteneur de stockage est créé dans le compte de stockage que vous avez sélectionné. La structure de ces objets blob est la suivante :

```powershell
insights-{metrics|logs}-{category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/ RESOURCEGROUPS/{resource group name}/PROVIDERS/Microsoft.SQL/servers/{resource_server}/ databases/{database_name}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```
    
Ou, plus simplement :

```powershell
insights-{metrics|logs}-{category name}/resourceId=/{resource Id}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

Par exemple, un nom d’objet blob pour des métriques sur 1 minute pourrait être :

```powershell
insights-metrics-minute/resourceId=/SUBSCRIPTIONS/s1id1234-5679-0123-4567-890123456789/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.SQL/ servers/Server1/databases/database1/y=2016/m=08/d=22/h=18/m=00/PT1H.json
```

Si vous souhaitez enregistrer les données du Pool élastique, le nom d’objet blob est un peu différent :

```powershell
insights-{metrics|logs}-{category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/ RESOURCEGROUPS/{resource group name}/PROVIDERS/Microsoft.SQL/servers/{resource_server}/ elasticPools/{elastic_pool_name}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

### <a name="download-metrics-and-logs-from-azure-storage"></a>Télécharger les métriques et journaux de Stockage Azure

Consultez [Télécharger les journaux de métriques et diagnostics de Stockage Azure](../storage/blobs/storage-dotnet-how-to-use-blobs.md#download-blobs).

## <a name="metrics-and-logs-available"></a>Métriques et journaux disponibles

### <a name="1-minute-metrics"></a>Métriques de 1 minute

|**Ressource**|**Métriques**|
|---|---|
|Base de données|Pourcentage DTU, Limite DTU, Pourcentage UC, Pourcentage de lecture de données physiques, Pourcentage d’écriture du journal, Connexions réussies/en échec/bloquées par pare-feu, Pourcentage de sessions, Pourcentage de workers, Stockage, Pourcentage de stockage, Pourcentage de stockage XTP, blocages |
|Pool élastique|Pourcentage DTU, eDTU utilisé, Limite eDTU, Pourcentage UC, Pourcentage de lecture de données physiques, Pourcentage d’écriture du journal, Pourcentage de sessions, Pourcentage de workers, Stockage, Pourcentage de stockage, Limite de stockage, Pourcentage de stockage XTP |
|||

### <a name="query-store-runtime-statistics"></a>Statistiques d’exécution du magasin des requêtes

|Propriété|Description|
|---|---|
|TenantId|Votre id de locataire.|
|SourceSystem|Toujours : Azure|
|TimeGenerated [UTC]|Timestamp du moment où le journal a été enregistré.|
|Type|Toujours : AzureDiagnostics|
|ResourceProvider|Nom du fournisseur de ressources. Toujours : MICROSOFT.SQL|
|Catégorie|Nom de la catégorie. Toujours : QueryStoreRuntimeStatistics|
|Nom d'opération|Nom de l’opération. Toujours : QueryStoreRuntimeStatisticsEvent|
|Ressource|Nom de la ressource|
|ResourceType|Nom du type de ressource. Toujours : SERVEURS/BASES DE DONNÉES|
|SubscriptionId|Identificateur global unique auquel la base de données appartient.|
|ResourceGroup|Nom du groupe de ressources auquel la base de données appartient.|
|LogicalServerName_s|Nom du serveur auquel la base de données appartient.|
|ElasticPoolName_s|Nom du pool élastique auquel la base de données appartient, le cas échéant.|
|DatabaseName_s|Nom de la base de données|
|ResourceId|URI de ressource|
|query_hash_s|Hachage de requête|
|query_plan_hash_s|Hachage de plan de requête|
|statement_sql_handle_s|Descripteur sql d’instruction|
|interval_start_time_d|Démarrez le datetimeoffset de l’intervalle en nombre de cycles à partir 1900-1-1.|
|interval_end_time_d|Terminez le datetimeoffset de l’intervalle en nombre de cycles à partir 1900-1-1.|
|logical_io_writes_d|Nombre total d’écritures E/S logiques.|
|max_logical_io_writes_d|Nombre maximal d’écritures E/S logiques par exécution.|
|physical_io_reads_d|Nombre total de lectures E/S physiques.|
|max_physical_io_reads_d|Nombre maximal de lectures E/S logiques par exécution.|
|logical_io_reads_d|Nombre total de lectures E/S logiques.|
|max_logical_io_reads_d|Nombre maximal de lectures E/S logiques par exécution.|
|execution_type_d|Type d’exécution|
|count_executions_d|Nombre d’exécutions de la requête.|
|cpu_time_d|Temps processeur total consommé par la requête, en microsecondes.|
|max_cpu_time_d|Consommateur de temps processeur maximal par une exécution unique en microsecondes.|
|dop_d|Somme des degrés du parallélisme.|
|max_dop_d|Degré maximal de parallélisme utilisé pour une seule exécution.|
|rowcount_d|Nombre total de lignes retournées.|
|max_rowcount_d|Nombre maximal de lignes retournées dans une seule exécution.|
|query_max_used_memory_d|Quantité totale de mémoire utilisée en Ko.|
|max_query_max_used_memory_d|Quantité maximale de mémoire utilisée par une exécution unique, en Ko.|
|duration_d|Durée d’exécution totale en microsecondes.|
|max_duration_d|Durée d’exécution maximal d’une seule exécution.|
|num_physical_io_reads_d|Nombre total de lectures physiques.|
|max_num_physical_io_reads_d|Nombre maximal de lectures physiques par exécution.|
|log_bytes_used_d|Quantité totale d’octets de journal utilisés.|
|max_log_bytes_used_d|Quantité maximale d’octets de journal utilisés par exécution.|
|query_id_d|ID de la requête dans le magasin des requêtes|
|plan_id_d|ID du plan dans le magasin des requêtes|

[En savoir plus sur les données de statistiques d’exécution du magasin des requêtes.](https://docs.microsoft.com/en-us/sql/relational-databases/system-catalog-views/sys-query-store-runtime-stats-transact-sql)

### <a name="query-store-wait-statistics"></a>Statistiques d’attente du magasin des requêtes

|Propriété|Description|
|---|---|
|TenantId|Votre id de locataire.|
|SourceSystem|Toujours : Azure|
|TimeGenerated [UTC]|Timestamp du moment où le journal a été enregistré.|
|Type|Toujours : AzureDiagnostics|
|ResourceProvider|Nom du fournisseur de ressources. Toujours : MICROSOFT.SQL|
|Catégorie|Nom de la catégorie. Toujours : QueryStoreRuntimeStatistics|
|Nom d'opération|Nom de l’opération. Toujours : QueryStoreRuntimeStatisticsEvent|
|Ressource|Nom de la ressource|
|ResourceType|Nom du type de ressource. Toujours : SERVEURS/BASES DE DONNÉES|
|SubscriptionId|Identificateur global unique auquel la base de données appartient.|
|ResourceGroup|Nom du groupe de ressources auquel la base de données appartient.|
|LogicalServerName_s|Nom du serveur auquel la base de données appartient.|
|ElasticPoolName_s|Nom du pool élastique auquel la base de données appartient, le cas échéant.|
|DatabaseName_s|Nom de la base de données|
|ResourceId|URI de ressource|
|wait_category_s|Catégorie de l’attente.|
|is_parameterizable_s|Indique si la requête est paramétrable.|
|statement_type_s|Type de l’instruction.|
|statement_key_hash_s|Hachage de clé d’instruction.|
|exec_type_d|Type d’exécution|
|total_query_wait_time_ms_d|Temps d’attente total de la requête sur la catégorie d’attente spécifique.|
|max_query_wait_time_ms_d|Temps d’attente maximal de la requête dans une exécution individuelle sur la catégorie d’attente spécifique|
|query_param_type_d|0|
|query_hash_s|Hachage de requête dans le magasin des requêtes.|
|query_plan_hash_s|Hachage de plan de requêtes dans le magasin des requêtes|
|statement_sql_handle_s|Descripteur d’instruction dans le magasin des requêtes|
|interval_start_time_d|Démarrez le datetimeoffset de l’intervalle en nombre de cycles à partir 1900-1-1.|
|interval_end_time_d|Terminez le datetimeoffset de l’intervalle en nombre de cycles à partir 1900-1-1.|
|count_executions_d|Comptabilisation des exécutions de la requête|
|query_id_d|ID de la requête dans le magasin des requêtes|
|plan_id_d|ID du plan dans le magasin des requêtes|

[Cliquez ici pour en savoir plus sur les données des statistiques d’attente du magasin des requêtes.](https://docs.microsoft.com/en-us/sql/relational-databases/system-catalog-views/sys-query-store-wait-stats-transact-sql)

### <a name="errors-dataset"></a>Jeu de données d’erreurs

|Propriété|Description|
|---|---|
|TenantId|Votre id de locataire.|
|SourceSystem|Toujours : Azure|
|TimeGenerated [UTC]|Timestamp du moment où le journal a été enregistré.|
|Type|Toujours : AzureDiagnostics|
|ResourceProvider|Nom du fournisseur de ressources. Toujours : MICROSOFT.SQL|
|Catégorie|Nom de la catégorie. Toujours : QueryStoreRuntimeStatistics|
|Nom d'opération|Nom de l’opération. Toujours : QueryStoreRuntimeStatisticsEvent|
|Ressource|Nom de la ressource|
|ResourceType|Nom du type de ressource. Toujours : SERVEURS/BASES DE DONNÉES|
|SubscriptionId|Identificateur global unique auquel la base de données appartient.|
|ResourceGroup|Nom du groupe de ressources auquel la base de données appartient.|
|LogicalServerName_s|Nom du serveur auquel la base de données appartient.|
|ElasticPoolName_s|Nom du pool élastique auquel la base de données appartient, le cas échéant.|
|DatabaseName_s|Nom de la base de données|
|ResourceId|URI de ressource|
|Message|Message d’erreur en texte brut|
|user_defined_b|Indique si l’erreur est un bit défini par l’utilisateur|
|error_number_d|Code d'erreur|
|Severity|Gravité de l’erreur|
|state_d|État de l’erreur|
|query_hash_s|Hachage de requête de la requête ayant échoué si disponible|
|query_plan_hash_s|Hachage du plan de requête de la requête ayant échoué si disponible|

[Messages d’erreur SQL Server](https://msdn.microsoft.com/en-us/library/cc645603.aspx)

### <a name="database-waits-dataset"></a>Jeu de données d’attentes de la base de données

|Propriété|Description|
|---|---|
|TenantId|Votre id de locataire.|
|SourceSystem|Toujours : Azure|
|TimeGenerated [UTC]|Timestamp du moment où le journal a été enregistré.|
|Type|Toujours : AzureDiagnostics|
|ResourceProvider|Nom du fournisseur de ressources. Toujours : MICROSOFT.SQL|
|Catégorie|Nom de la catégorie. Toujours : QueryStoreRuntimeStatistics|
|Nom d'opération|Nom de l’opération. Toujours : QueryStoreRuntimeStatisticsEvent|
|Ressource|Nom de la ressource|
|ResourceType|Nom du type de ressource. Toujours : SERVEURS/BASES DE DONNÉES|
|SubscriptionId|Identificateur global unique auquel la base de données appartient.|
|ResourceGroup|Nom du groupe de ressources auquel la base de données appartient.|
|LogicalServerName_s|Nom du serveur auquel la base de données appartient.|
|ElasticPoolName_s|Nom du pool élastique auquel la base de données appartient, le cas échéant.|
|DatabaseName_s|Nom de la base de données|
|ResourceId|URI de ressource|
|wait_type_s|Nom du type d’attente|
|start_utc_date_t [UTC]|Heure de début de période mesuré.|
|end_utc_date_t [UTC]|Heure de fin de période mesuré.|
|delta_max_wait_time_ms_d|Temps d’attente maximal par exécution|
|delta_signal_wait_time_ms_d|Durée d’attente totale de signal|
|delta_wait_time_ms_d|Durée d’attente totale dans la période|
|delta_waiting_tasks_count_d|Nombre de tâches en attente|

[Cliquez ici pour en savoir plus sur les statistiques d’attente de base de données.](https://docs.microsoft.com/en-us/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql)

### <a name="timeouts-dataset"></a>Jeu de données des délais d’expiration

|Propriété|Description|
|---|---|
|TenantId|Votre id de locataire.|
|SourceSystem|Toujours : Azure|
|TimeGenerated [UTC]|Timestamp du moment où le journal a été enregistré.|
|Type|Toujours : AzureDiagnostics|
|ResourceProvider|Nom du fournisseur de ressources. Toujours : MICROSOFT.SQL|
|Catégorie|Nom de la catégorie. Toujours : QueryStoreRuntimeStatistics|
|Nom d'opération|Nom de l’opération. Toujours : QueryStoreRuntimeStatisticsEvent|
|Ressource|Nom de la ressource|
|ResourceType|Nom du type de ressource. Toujours : SERVEURS/BASES DE DONNÉES|
|SubscriptionId|Identificateur global unique auquel la base de données appartient.|
|ResourceGroup|Nom du groupe de ressources auquel la base de données appartient.|
|LogicalServerName_s|Nom du serveur auquel la base de données appartient.|
|ElasticPoolName_s|Nom du pool élastique auquel la base de données appartient, le cas échéant.|
|DatabaseName_s|Nom de la base de données|
|ResourceId|URI de ressource|
|error_state_d|Code d’état d’erreur|
|query_hash_s|Hachage de requête si disponible|
|query_plan_hash_s|Hachage de plan de requête si disponible|

### <a name="blockings-dataset"></a>Jeu de données de blocages

|Propriété|Description|
|---|---|
|TenantId|Votre id de locataire.|
|SourceSystem|Toujours : Azure|
|TimeGenerated [UTC]|Timestamp du moment où le journal a été enregistré.|
|Type|Toujours : AzureDiagnostics|
|ResourceProvider|Nom du fournisseur de ressources. Toujours : MICROSOFT.SQL|
|Catégorie|Nom de la catégorie. Toujours : QueryStoreRuntimeStatistics|
|Nom d'opération|Nom de l’opération. Toujours : QueryStoreRuntimeStatisticsEvent|
|Ressource|Nom de la ressource|
|ResourceType|Nom du type de ressource. Toujours : SERVEURS/BASES DE DONNÉES|
|SubscriptionId|Identificateur global unique auquel la base de données appartient.|
|ResourceGroup|Nom du groupe de ressources auquel la base de données appartient.|
|LogicalServerName_s|Nom du serveur auquel la base de données appartient.|
|ElasticPoolName_s|Nom du pool élastique auquel la base de données appartient, le cas échéant.|
|DatabaseName_s|Nom de la base de données|
|ResourceId|URI de ressource|
|lock_mode_s|Mode de verrouillage utilisé par la requête|
|resource_owner_type_s|Propriétaire du verrou.|
|blocked_process_filtered_s|XML de rapport de processus bloqué.|
|duration_d|Durée du verrou en millisecondes.|

### <a name="intelligent-insights-dataset"></a>Jeu de données Intelligent Insights
[Cliquez ici pour en savoir plus sur le format de journal Intelligent Insights](sql-database-intelligent-insights-use-diagnostics-log.md)

## <a name="next-steps"></a>Étapes suivantes

- Pour comprendre non seulement comment activer la journalisation, mais aussi les métriques et les catégories de journaux prises en charge par les différents services Azure, voir [Vue d’ensemble des mesures dans Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md) et [Présentation des journaux de diagnostic Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md).
- Pour en savoir plus sur les hubs d’événements, lisez les articles suivants :
   - [Qu’est-ce qu’Azure Event Hubs](../event-hubs/event-hubs-what-is-event-hubs.md) ?
   - [Prise en main des hubs d’événements](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)
- Consultez [Télécharger les journaux de métriques et diagnostics de Stockage Azure](../storage/blobs/storage-dotnet-how-to-use-blobs.md#download-blobs).
