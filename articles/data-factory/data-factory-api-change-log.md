<properties 
	pageTitle="Data Factory - Journal des modifications .NET API | Microsoft Azure" 
	description="Décrit les dernières modifications, les ajouts de fonctionnalités, les correctifs de bogues, etc., d’une version spécifique de l'API .NET de Azure Data Factory." 
	services="data-factory" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/05/2016" 
	ms.author="spelluru"/>

# Azure Data Factory : Journal des modifications de l’API .NET 
Cet article fournit des informations sur les modifications apportées au SDK Azure Data Factory dans une version spécifique. Vous trouverez [ici](https://www.nuget.org/packages/Microsoft.Azure.Management.DataFactories) le dernier package NuGet pour Microsoft Azure Data Factory

## Version 4.7.0
Date de lancement : 20/05/2016

### Ajouts de fonctionnalités
* Ajout du nouveau type StorageFormat [OrcFormat](https://msdn.microsoft.com/library/mt723391.aspx) pour copier des fichiers au format ORC.
* Ajout des propriétés [AllowPolyBase](https://msdn.microsoft.com/library/mt723396.aspx) et PolyBaseSettings à SqlDWSink.
    * Permet l’utilisation de PolyBase pour copier des données dans SQL Data Warehouse.

## Version 4.6.1
Date de lancement : 26/04/2016

### Résolution des bogues
* Résout les requêtes HTTP pour les listes de fenêtres d’activité.
    * Supprime le nom du groupe de ressources et le nom de la fabrique de données de la charge utile des requêtes.

## Version 4.6.0
Date de lancement : 14.04.2016

### Ajouts de fonctionnalités

- Les propriétés suivantes ont été ajoutées à [PipelineProperties](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.pipelineproperties_properties.aspx) :
	- [PipelineMode](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.pipelineproperties.pipelinemode.aspx)
	- [ExpirationTime](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.pipelineproperties.expirationtime.aspx)
	- [Groupes de données](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.pipelineproperties.datasets.aspx)
- Les propriétés suivantes ont été ajoutées à [PipelineRuntimeInfo](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.common.models.pipelineruntimeinfo.aspx) :
	- [PipelineState](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.common.models.pipelineruntimeinfo.pipelinestate.aspx)
- Ajout de nouveaux types [StorageFormat](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.storageformat.aspx) et [JsonFormat](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.jsonformat.aspx) pour définir les jeux de données dont les données sont au format JSON.

## Version 4.5.0
Date de lancement : 24.02.2016

### Ajouts de fonctionnalités
* Ajout de [lister les opérations pour la fenêtre d’activité](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.activitywindowoperationsextensions.aspx).
    * Ajout de méthodes pour récupérer les fenêtres d'activité avec des filtres en fonction du type des entités (par exemple, fabriques de données, jeux de données, pipelines et activités).
* Les types de services liés suivants ont été ajoutés :
    * [ODataLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.odatalinkedservice.aspx), [WebLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.weblinkedservice.aspx)
* Les types de jeux de données suivants ont été ajoutés :
    * [ODataResourceDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.odataresourcedataset.aspx), [WebTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.webtabledataset.aspx)
* Les types de sources de copie suivants ont été ajoutés :
    * [WebSource](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.websource.aspx)

## Version 4.4.0
Date de sortie : 28/01/2016

### Ajouts de fonctionnalités

- Le type de service lié suivant a été ajouté en tant que source de données et récepteur pour les activités de copie :
	- [AzureStorageSasLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.azurestoragesaslinkedservice.aspx). Consultez la section [Service lié SAP Azure Storage](data-factory-azure-blob-connector.md#azure-storage-sas-linked-service) pour obtenir des informations conceptuelles et des exemples.

## Version 4.3.0
Date de sortie : 25/11/2015

### Ajouts de fonctionnalités

- Les types de service lié suivants ont été ajoutés en tant que sources de données pour les activités de copie :
	- [HdfsLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.hdfslinkedservice.aspx). Consultez la section [Déplacer des données de HDFS à l’aide de Data Factory](data-factory-hdfs-connector.md) pour obtenir des informations conceptuelles et des exemples.
	- [OnPremisesOdbcLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.onpremisesodbclinkedservice.aspx). Consultez la section [Déplacer des données à partir de magasins de données ODBC à l’aide d’Azure Data Factory](data-factory-odbc-connector.md) pour obtenir des informations conceptuelles et des exemples.

## Version 4.2.0
Date de sortie : 10/11/2015

### Ajouts de fonctionnalités

- Le nouveau type d’activité suivant a été ajouté : [AzureMLUpdateResourceActivity](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuremlupdateresourceactivity.aspx). Pour plus d’informations sur l’activité, consultez la section [Mise à jour des modèles Azure ML à l’aide de l’activité de mise à jour des ressources](data-factory-azure-ml-batch-execution-activity.md#updating-azure-ml-models-using-the-update-resource-activity).
- Une nouvelle propriété facultative [updateResourceEndpoint](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuremllinkedservice.updateresourceendpoint.aspx) a été ajoutée à la [classe AzureMLLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuremllinkedservice.aspx).
- Les propriétés [LongRunningOperationInitialTimeout](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.datafactorymanagementclient.longrunningoperationinitialtimeout.aspx) et [LongRunningOperationRetryTimeout](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.datafactorymanagementclient.longrunningoperationretrytimeout.aspx) ont été ajoutées à la classe [DataFactoryManagementClient](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.datafactorymanagementclient.aspx).
- Elles permettent de configurer les délais d’attente pour les appels du client vers le service Data Factory.


## Version 4.1.0
Date de lancement : 28/10/2015

### Ajouts de fonctionnalités
* Les types de services liés suivants ont été ajoutés :
    * [AzureDataLakeStoreLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice.aspx)
    * [AzureDataLakeAnalyticsLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice.aspx)
* Les types d’activités suivants ont été ajoutés :
    * [DataLakeAnalyticsUSQLActivity](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datalakeanalyticsusqlactivity.aspx)
* Les types de jeux de données suivants ont été ajoutés :
    * [AzureDataLakeStoreDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestoredataset.aspx)
* Les types de source et de récepteur suivants pour l’activité de copie ont été ajoutés :
    * [AzureDataLakeStoreSource](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestoresource.aspx)
    * [AzureDataLakeStoreSink](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestoresink.aspx)


## Version 4.0.1
Date de lancement : 13/10/2015

### Dernières modifications
Les classes suivantes ont été renommées. Les nouveaux noms étaient les noms d'origine des classes avant la version 4.0.0.
 
Nom dans 4.0.0 | Nom dans 4.0.1
:------------ | :------------ 
AzureSqlDataWarehouseDataset | [AzureSqlDataWarehouseTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuresqldatawarehousetabledataset.aspx)
AzureSqlDataset | [AzureSqlTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuresqltabledataset.aspx)
AzureDataset | [AzureTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuretabledataset.aspx)
OracleDataset | [OracleTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.oracletabledataset.aspx)
RelationalDataset | [RelationalTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.relationaltabledataset.aspx)
SqlServerDataset | [SqlServerTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.sqlservertabledataset.aspx)


## Version 4.0.0
Date de lancement : 02/10/2015

### Dernières modifications



- Les classes/interfaces suivantes ont été renommées.

| Ancien nom | Nouveau nom |
| :-------- | :-------- |
| ITableOperations | [IDatasetOperations](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.idatasetoperations.aspx) |  
| Table | [Dataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.dataset.aspx) | 
| TableProperties | [DatasetProperties](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetproperties.aspx) | 
| TableTypeProprerties | [DatasetTypeProperties](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasettypeproperties.aspx) |
| TableCreateOrUpdateParameters | [DatasetCreateOrUpdateParameters](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetcreateorupdateparameters.aspx) | 
| TableCreateOrUpdateResponse | [DatasetCreateOrUpdateResponse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetcreateorupdateresponse.aspx) | 
| TableGetResponse | [DatasetGetResponse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetgetresponse.aspx) | 
| TableListResponse | [DatasetListResponse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetlistresponse.aspx) |
| CreateOrUpdateWithRawJsonContentParameters | [DatasetCreateOrUpdateWithRawJsonContentParameters](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetcreateorupdatewithrawjsoncontentparameters.aspx) | 
    
- La **version de l’API** pour cette version est : **01-10-2015**.

- Les méthodes **Liste** renvoient désormais des résultats paginés. Si la réponse contient une propriété **NextLink** non vide, l’application cliente doit continuer à récupérer la page suivante jusqu’à ce que toutes les pages soient renvoyées. Voici un exemple :

		PipelineListResponse response = client.Pipelines.List("ResourceGroupName", "DataFactoryName");
	    var pipelines = new List<Pipeline>(response.Pipelines);
	
	    string nextLink = response.NextLink;
	    while (string.IsNullOrEmpty(response.NextLink))
	    {
	        PipelineListResponse nextResponse = client.Pipelines.ListNext(nextLink);
	        pipelines.AddRange(nextResponse.Pipelines);
	
	        nextLink = nextResponse.NextLink;
	    }
	
- L’API d’un pipeline de **liste** renvoie uniquement le résumé d’un pipeline au lieu des détails complets. Par exemple, les activités d’un résumé de pipeline ne contiennent que le nom et le type.

### Ajouts de fonctionnalités
- La classe [SqlDWSink](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.sqldwsink.aspx) prend en charge deux nouvelles propriétés, **SliceIdentifierColumnName** et **SqlWriterCleanupScript**, pour prendre en charge la copie idempotent vers Azure SQL Data Warehouse. Pour plus d’informations sur ces propriétés, consultez l’article [Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md), en particulier les sections [Mécanisme 1](data-factory-azure-sql-data-warehouse-connector.md#mechanism-1) et [Mécanisme 2](data-factory-azure-sql-data-warehouse-connector.md#mechanism-2).

- Nous prenons désormais en charge l'exécution d’une procédure stockée avec des sources Azure SQL Database et Azure SQL Data Warehouse dans le cadre de l'activité de copie. Pour cette prise en charge, les classes [SqlSource](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.sqlsource.aspx) et [SqlDWSource](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.sqldwsource.aspx) comportent les propriétés suivantes : **SqlReaderStoredProcedureName** et **StoredProcedureParameters**. Pour plus d’informations sur ces propriétés, consultez les articles [Base de données SQL Azure](data-factory-azure-sql-connector.md#sqlsource) et [Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md#sqldwsource) sur Azure.com.

<!---HONumber=AcomDC_0706_2016-->