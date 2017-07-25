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
ms.date: 05/24/2017
ms.author: vvasic
ms.translationtype: Human Translation
ms.sourcegitcommit: 3bbc9e9a22d962a6ee20ead05f728a2b706aee19
ms.openlocfilehash: 71c286061e214898a7f10fa8dfd6c6d86af8db39
ms.contentlocale: fr-fr
ms.lasthandoff: 06/10/2017


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

- **[Métriques de 1 minute](sql-database-metrics-diag-logging.md#1-minute-metrics)**  : contient Pourcentage DTU, Limite DTU, Pourcentage UC, Pourcentage de lecture de données physiques, Pourcentage d’écriture du journal, Connexions réussies/en échec/bloquées par pare-feu, Pourcentage de sessions, Pourcentage de workers, Stockage, Pourcentage de stockage, Pourcentage de stockage XTP

Si vous spécifiez un compte Event Hub ou Stockage Azure, vous pouvez définir une stratégie de rétention pour spécifier que les données antérieures à un intervalle de temps sélectionné sont supprimées. Si vous spécifiez Log Analytics, la stratégie de rétention dépend du niveau tarifaire sélectionné. Pour en savoir plus, voir [Tarification de Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/). 

Pour comprendre non seulement comment activer la journalisation, mais aussi les métriques et les catégories de journaux prises en charge par les différents services Azure, nous vous recommandons de lire les articles [Vue d’ensemble des mesures dans Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md) et [Présentation des journaux de diagnostic Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md).

### <a name="azure-portal"></a>Portail Azure

Pour activer la collecte des journaux de métriques et de diagnostics dans le portail Azure, accédez à votre base de données SQL Azure ou à une page de pool élastique, puis cliquez sur **Paramètres de diagnostic**.

   ![activer dans le portail Azure](./media/sql-database-metrics-diag-logging/enable-portal.png)

### <a name="powershell"></a>PowerShell

Pour activer la journalisation des métriques et diagnostics à l’aide de PowerShell, utilisez les commandes suivantes :

- Pour activer le stockage des journaux de diagnostic dans un compte de stockage, utilisez cette commande :

   ```powershell
   Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -StorageAccountId [your storage account id] -Enabled $true
   ```

   L’ID de compte de stockage est l’ID de ressource pour le compte de stockage auquel vous souhaitez envoyer les journaux.

- Pour activer la diffusion en continu des journaux de diagnostic vers un Event Hub, utilisez cette commande :

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

### <a name="cli"></a>Interface de ligne de commande

Pour activer la journalisation des métriques et diagnostics à l’aide d’Azure CLI, utilisez les commandes suivantes :

- Pour activer le stockage des journaux de diagnostic dans un compte de stockage, utilisez cette commande :

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --storageId <storageAccountId> --enabled true
   ```

   L’ID de compte de stockage est l’ID de ressource pour le compte de stockage auquel vous souhaitez envoyer les journaux.

- Pour activer la diffusion en continu des journaux de diagnostic vers un Event Hub, utilisez cette commande :

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

1.  Créer une ressource Log Analytics
2.  Configurer des bases de données pour enregistrer des journaux de métriques et diagnostics dans la ressource Log Analytics créée
3.  Installer la solution **Azure SQL Analytics** à partir de la galerie de Log Analytics

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

Azure SQL Analytics est un tableau de bord hiérarchique qui vous permet de naviguer dans la hiérarchie des ressources d’Azure SQL Analytics. Cette fonctionnalité vous permet d’opérer une surveillance générale, ainsi que d’étendre votre analyse à l’ensemble approprié de ressources.
Le tableau de bord contient les listes des différentes ressources sous la ressource sélectionnée. Par exemple, pour un abonnement sélectionné, vous pouvez voir l’ensemble des serveurs, des pools élastiques et des bases appartenant à l’abonnement sélectionné. En outre, pour les pools élastiques et les bases de données, vous pouvez voir les métriques d’utilisation de ces ressources. Celles-ci incluent des graphiques pour DTU, UC, E/S, journal, sessions, workers, connexions et stockage en Go.

## <a name="stream-into-azure-event-hub"></a>Envoyer à Azure Event Hub

Les journaux de métriques et diagnostics d’Azure SQL Database peuvent être envoyés à Azure Event Hub à l’aide de l’option « Diffuser vers Event Hub » intégrée dans le portail, ou en activant l’ID de règle Service Bus dans un paramètre de diagnostic via des applets de commande Azure PowerShell, Azure CLI ou l’API REST Azure Monitor. 

### <a name="what-to-do-with-metrics-and-diagnostic-logs-in-event-hub"></a>Que faire des journaux de métriques et diagnostics dans Event Hub ?
En sélectionnant les données envoyées à Event Hub, vous vous rapprochez de l’activation de scénarios d’analyse avancée. Event Hubs fonctionne comme la « porte d'entrée » d’un pipeline d’événements, et une fois que les données sont collectées dans un concentrateur d'événements, peut être transformées et stockées à l'aide de n'importe quel fournisseur d'analyse en temps réel ou d’adaptateurs de traitement par lot ou de stockage. Les concentrateurs d'événements dissocient la production d'un flux d'événements de la consommation de ces événements, de manière à ce que les consommateurs d'événements puissent accéder aux événements selon leur propre planification. Pour plus d’informations sur Event Hub, voir :

- [Qu’est-ce qu’Azure Event Hubs](../event-hubs/event-hubs-what-is-event-hubs.md) ?
- [Prise en main des hubs d’événements](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)


Voici quelques façons d’utiliser la fonctionnalité de diffusion en continu :

-   Afficher l’état d’intégrité du service en diffusant des données de chemin réactif vers PowerBI : en utilisant Event Hubs, Stream Analytics et PowerBI, vous pouvez facilement transformer vos données de métriques et de diagnostic en informations en temps réel sur vos services Azure. Pour une vue d’ensemble de la manière de configurer un Event Hub, de traiter les données avec Stream Analytics, et d’utiliser PowerBI comme sortie, voir [Stream Analytics et Power BI](../stream-analytics/stream-analytics-power-bi-dashboard.md).
-   Envoyer les journaux à des flux de journalisation et de télémétrie tiers : une diffusion en continu Event Hubs vous permet d’envoyer vos journaux de métriques et diagnostics à différentes solution tierces de surveillance et d’analytique des journaux. 
-   Créer une plateforme de journalisation et de télémétrie personnalisée : si vous disposez déjà d’une plateforme de télémétrie personnalisée, ou si vous envisagez d’en créer une, la nature hautement évolutive de publication et d’abonnement d’Event Hubs vous permet d’intégrer avec souplesse les journaux de diagnostic. Lisez le [guide de Dan Rosanova sur l’utilisation d’Event Hubs dans une plateforme de télémétrie à l’échelle mondiale](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/).

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

Consultez [Télécharger les journaux de métriques et diagnostics de Stockage Azure](../storage/storage-dotnet-how-to-use-blobs.md#download-blobs).

## <a name="1-minute-metrics"></a>Métriques de 1 minute

| |  |
|---|---|
|**Ressource**|**Métriques**|
|Base de données|Pourcentage DTU, Limite DTU, Pourcentage UC, Pourcentage de lecture de données physiques, Pourcentage d’écriture du journal, Connexions réussies/en échec/bloquées par pare-feu, Pourcentage de sessions, Pourcentage de workers, Stockage, Pourcentage de stockage, Pourcentage de stockage XTP, blocages |
|Pool élastique|Pourcentage DTU, eDTU utilisé, Limite eDTU, Pourcentage UC, Pourcentage de lecture de données physiques, Pourcentage d’écriture du journal, Pourcentage de sessions, Pourcentage de workers, Stockage, Pourcentage de stockage, Limite de stockage, Pourcentage de stockage XTP |
|||

## <a name="next-steps"></a>Étapes suivantes

- Pour comprendre non seulement comment activer la journalisation, mais aussi les métriques et les catégories de journaux prises en charge par les différents services Azure, voir [Vue d’ensemble des mesures dans Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md) et [Présentation des journaux de diagnostic Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md).
- Pour en savoir plus sur les concentrateurs d’événements, lisez les articles suivants :
   - [Qu’est-ce qu’Azure Event Hubs](../event-hubs/event-hubs-what-is-event-hubs.md) ?
   - [Prise en main des hubs d’événements](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)
- Consultez [Télécharger les journaux de métriques et diagnostics de Stockage Azure](../storage/storage-dotnet-how-to-use-blobs.md#download-blobs).

