<properties
   pageTitle="Didacticiel Charger des données avec PolyBase | Microsoft Azure"
   description="Découvrez comment utiliser PolyBase pour charger des données dans SQL Data Warehouse."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sahajs"
   manager="jhubbard"
   editor="sahajs"/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="10/20/2015"
   ms.author="sahajs;barbkess"/>


# Charger des données avec PolyBase

> [AZURE.SELECTOR]
- [Data Factory](sql-data-warehouse-get-started-load-with-azure-data-factory.md)
- [PolyBase](sql-data-warehouse-load-with-polybase-short.md)
- [BCP](sql-data-warehouse-load-with-bcp.md)

Ce didacticiel vous explique comment charger des données dans votre base de données Azure SQL Data Warehouse avec PolyBase.


## Configuration requise
Pour parcourir ce didacticiel, vous avez besoin des éléments suivants

- une base de données SQL Data Warehouse
- un compte Azure Storage ;


## Étape 1 : Créer un fichier de données source
Ouvrez le Bloc-notes et copiez les lignes de données suivantes dans un nouveau fichier. Enregistrez-les dans votre répertoire temporaire local, C:\\Temp\\DimDate2.txt.

```
20150301,1,3
20150501,2,4
20151001,4,2
20150201,1,3
20151201,4,2
20150801,3,1
20150601,2,4
20151101,4,2
20150401,2,4
20150701,3,1
20150901,3,1
20150101,1,3
```


## Étape 2 : Migrer les données vers le stockage d’objets blob Azure

- Téléchargez la [dernière version d'AzCopy][].
- Ouvrez une fenêtre Commande et naviguez jusqu'au répertoire d'installation d'AzCopy sur votre ordinateur, où se trouve AzCopy.exe. Par défaut, le répertoire d'installation est %ProgramFiles(x86)%\\Microsoft SDKs\\Azure\\AzCopy\\ sur un ordinateur Windows 64 bits.
- Exécutez la commande suivante pour télécharger le fichier. Spécifiez votre clé de compte de stockage Azure pour /DestKey.

```
.\AzCopy.exe /Source:C:\Temp\ /Dest:https://pbdemostorage.blob.core.windows.net/datacontainer/datedimension/ /DestKey:<azure_storage_account_key> /Pattern:DimDate2.txt
```

Pour en savoir plus sur AzCopy, consultez l’article [Prise en main de l’utilitaire de ligne de commande AzCopy][].


## Étape 3 : Créer des tables externes

Vous devez ensuite créer des tables externes dans la base de données SQL Data Warehouse pour faire référence aux données dans le stockage d’objets blob Azure. Pour créer une table externe, procédez comme suit :

- [Créer une clé principale][] : pour chiffrer la clé secrète de vos informations d’identification de niveau base de données.
- [Créer des informations d’identification de niveau base de données] : pour spécifier les informations d’authentification de votre compte de stockage Azure.
- [Créer une source de données externe] : pour spécifier l’emplacement de votre stockage d’objets blob Azure.
- [Créer un format de fichier externe] : pour spécifier la disposition de vos données.
- [Créer une table externe] : pour référencer les données de stockage Azure.



```

-- A: Create Master Key
-- Required to encrypt the credential secret in the next step.
CREATE MASTER KEY;


-- B: Create Database Scoped Credential
-- Provide the Azure storage account key. The identity name does not affect authentication to Azure storage.
CREATE DATABASE SCOPED CREDENTIAL AzureStorageCredential 
WITH IDENTITY = 'user', 
SECRET = '<azure_storage_account_key>';


-- C: Create External Data Source
-- Specify location and credential to access your Azure blob storage.
CREATE EXTERNAL DATA SOURCE AzureStorage 
WITH (	
		TYPE = Hadoop, 
		LOCATION = 'wasbs://datacontainer@pbdemostorage.blob.core.windows.net',
		CREDENTIAL = AzureStorageCredential
); 


-- D: Create External File format 
-- Specify the layout of data stored in Azure storage blobs. 
CREATE EXTERNAL FILE FORMAT TextFile 
WITH (FORMAT_TYPE = DelimitedText, 
FORMAT_OPTIONS (FIELD_TERMINATOR = ','));


-- E: Create External Table
-- To reference data stored in Azure blob storage.
CREATE EXTERNAL TABLE dbo.DimDate2External (
	DateId INT NOT NULL, 
	CalendarQuarter TINYINT NOT NULL, 
	FiscalQuarter TINYINT NOT NULL
)
WITH (
		LOCATION='datedimension/', 
		DATA_SOURCE=AzureStorage, 
		FILE_FORMAT=TextFile
);


-- Run a query on external table to confirm that the Azure Storage data can be referenced.
SELECT count(*) FROM dbo.DimDate2External;

```



## Étape 4 : Charger des données dans SQL Data Warehouse

- Pour charger les données dans une nouvelle table, exécutez l'instruction CREATE TABLE AS SELECT. La nouvelle table hérite des colonnes désignées dans la requête. Elle hérite des types de données de ces colonnes à partir de la définition de table externe. 
- Pour charger les données dans une table existante, utilisez l'instruction INSERT...SELECT.  


```

-- Load the data from Azure blob storage to SQL Data Warehouse

CREATE TABLE dbo.DimDate2
WITH 
(   
    CLUSTERED COLUMNSTORE INDEX,
		DISTRIBUTION = ROUND_ROBIN
)
AS 
SELECT * 
FROM   [dbo].[DimDate2External];

```
Voir [CREATE TABLE AS SELECT (Transact-SQL)][].


Pour en savoir plus sur PolyBase, consultez [Didacticiel PolyBase dans SQL Data Warehouse][].


<!--Article references-->
[Didacticiel PolyBase dans SQL Data Warehouse]: sql-data-warehouse-load-with-polybase.md


<!-- External Links -->
[dernière version d'AzCopy]: http://aka.ms/downloadazcopy
[Prise en main de l’utilitaire de ligne de commande AzCopy]: https://azure.microsoft.com/documentation/articles/storage-use-azcopy/

[Créer une source de données externe]: https://msdn.microsoft.com/library/dn935022(v=sql.130).aspx
[Créer un format de fichier externe]: https://msdn.microsoft.com/library/dn935026(v=sql.130).aspx
[Créer une table externe]: https://msdn.microsoft.com/library/dn935021(v=sql.130).aspx
[Créer une clé principale]: https://msdn.microsoft.com/fr-FR/library/ms174382.aspx
[Créer des informations d’identification de niveau base de données]: https://msdn.microsoft.com/fr-FR/library/mt270260.aspx
[CREATE TABLE AS SELECT (Transact-SQL)]: https://msdn.microsoft.com/library/mt204041.aspx

<!---HONumber=Nov15_HO1-->