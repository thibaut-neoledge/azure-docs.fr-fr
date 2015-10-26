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
	ms.date="10/13/2015" 
	ms.author="spelluru"/>

# Azure Data Factory - Journal des modifications .NET SDK 
Cet article fournit des informations sur les modifications apportées au SDK Azure Data Factory dans une version spécifique. Vous trouverez [ici](https://www.nuget.org/packages/Microsoft.Azure.Management.DataFactories) le dernier package NuGet pour Microsoft Azure Data Factory

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
    
- La **version de l’API** pour cette version est : **01-10-2015**.

- Les méthodes de **liste** renvoient désormais des résultats paginés. Si la réponse contient une propriété **NextLink** non vide, l'application cliente doit continuer à récupérer la page suivante jusqu'à ce que toutes les pages soient renvoyées. Voici un exemple :

		PipelineListResponse response = client.Pipelines.List("ResourceGroupName", "DataFactoryName");
	    var pipelines = new List<Pipeline>(response.Pipelines);
	
	    string nextLink = response.NextLink;
	    while (string.IsNullOrEmpty(response.NextLink))
	    {
	        PipelineListResponse nextResponse = client.Pipelines.ListNext(nextLink);
	        pipelines.AddRange(nextResponse.Pipelines);
	
	        nextLink = nextResponse.NextLink;
	    }
	
- L’API d’un pipeline de **liste** renvoie uniquement le résumé d'un pipeline au lieu des détails complets. Par exemple, les activités d’un résumé de pipeline ne contiennent que le nom et le type.

### Ajouts de fonctionnalités
- La classe [SqlDWSink](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.sqldwsink.aspx) prend en charge deux nouvelles propriétés, **SliceIdentifierColumnName** et **SqlWriterCleanupScript**, pour prendre en charge la copie idempotente vers Azure SQL Data Warehouse. Consultez l’article [Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md), en particulier les sections [Mécanisme 1](data-factory-azure-sql-data-warehouse-connector.md#mechanism-1) and [Mécanisme 2](data-factory-azure-sql-data-warehouse-connector.md#mechanism-2), pour plus d'informations sur ces propriétés.

- Nous prenons désormais en charge l'exécution d’une procédure stockée avec des sources Azure SQL Database et Azure SQL Data Warehouse dans le cadre de l'activité de copie. Pour cette prise en charge, les classes [SqlSource](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.sqlsource.aspx) et [SqlDWSource](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.sqldwsource.aspx) comportent les propriétés suivantes : **SqlReaderStoredProcedureName** et **StoredProcedureParameters**. Consultez les articles [Azure SQL Database](data-factory-azure-sql-connector.md#sqlsource) et [Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md#sqldwsource) sur Azure.com pour plus d'informations sur ces propriétés.

<!---HONumber=Oct15_HO3-->