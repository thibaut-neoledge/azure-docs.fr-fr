<properties
   pageTitle="Didacticiel PolyBase dans SQL Data Warehouse | Microsoft Azure"
   description="Découvrez PolyBase et apprenez comment utiliser cette solution avec les scénarios d’entreposage de données."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="barbkess"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="05/09/2015"
   ms.author="sahajs;barbkess"/>


# Charger des données avec PolyBase
La technologie PolyBase vous permet d’interroger et de rassembler des données de sources multiples, à l’aide de commandes Transact-SQL.

Grâce à PolyBase, vous êtes en mesure d’interroger des données stockées dans des objets Blob Microsoft Azure Storage et de les charger dans une base de données SQL Data Warehouse en appliquant la méthode suivante :

- Créez la clé principale et les informations d’identification de la base de données.
- Créez des objets PolyBase : source de données externe, format de fichier externe et table externe. 
- Interrogez les données stockées dans des objets Blob Microsoft Azure Storage.
- Chargez les données conservées dans des objets Blob Microsoft Azure Storage dans SQL Data Warehouse.


## Composants requis
Pour parcourir ce didacticiel, vous avez besoin des éléments suivants :

- Compte Azure Storage
- Données stockées dans les objets Blob Microsoft Azure Storage en tant que fichiers textes délimités

Toute d’abord, vous allez créer les objets dont a besoin PolyBase pour se connecter aux données des objets Blob Microsoft Azure Storage et les interroger.

## Créer une clé principale de base de données
Connectez-vous à la base de données utilisateur de votre serveur pour créer une clé principale de base de données. Cette clé est utilisée pour chiffrer les informations secrètes d’identification au cours de l’étape suivante.

```
-- Creating master key
CREATE MASTER KEY;
```

Rubrique de référence : [CREATE MASTER KEY (Transact-SQL)][].

## Créer un fichier d’informations d’identification de niveau base de données
Pour accéder au stockage d’objets Blob Microsoft Azure, vous devez créer un fichier d’informations d’identification de niveau base de données qui conserve les informations d’identification dont vous avez besoin pour accéder à votre compte Microsoft Azure Storage. Connectez-vous à votre base de données Data Warehouse et créez un fichier d’informations d’identification de base de données pour chacun des comptes Microsoft Azure Storage pour lesquels vous souhaitez bénéficier d’un accès. Spécifiez un nom d’identité et votre clé secrète de compte Microsoft Azure Storage. Le nom d’identité n’affecte aucunement l’identification à Microsoft Azure Storage.

Pour voir s’il existe déjà des informations d’identification de base de données, utilisez sys.database_credentials, et non sys.credentials, qui affiche uniquement les informations d’identification de serveur.

```
-- Check for existing database-scoped credentials.
SELECT * FROM sys.database_credentials;

-- Create a database scoped credential
CREATE DATABASE SCOPED CREDENTIAL ASBSecret WITH IDENTITY = 'joe', 
	Secret = 'myazurestoragekey==';
```

Rubrique de référence : [CREATE CREDENTIAL (Transact-SQL)][].

Lorsque vous changez de clés de compte Microsoft Azure Storage, vous devez abandonner les informations d’identification et les recréer à l’aide de la nouvelle clé secrète.

```
-- Dropping credential
DROP DATABASE SCOPED CREDENTIAL ASBSecret;
```

Rubrique de référence : [DROP CREDENTIAL (Transact-SQL)][].


## Créer une source de données externe
La source de données externe est un objet de base de données qui stocke l’emplacement des données des objets Blob Microsoft Azure Storage et vos informations d’accès. Vous devez définir une source de données externes pour chacun des conteneurs Microsoft Azure Storage auquel vous souhaitez accéder.

```
-- Creating external data source (Azure Blob Storage) 
CREATE EXTERNAL DATA SOURCE azure_storage 
WITH (
	TYPE = HADOOP, 
       LOCATION ='wasbs://mycontainer@ test.blob.core.windows.net/path’,
      CREDENTIAL = ASBSecret
);
```

Rubrique de référence : [CREATE EXTERNAL DATA SOURCE (Transact-SQL)][].

## Créer un format de fichier externe
Le format de fichier externe est un objet de base de données qui spécifie le format des données externes. Dans cet exemple, nous avons décompressé les données dans un fichier texte et les champs sont séparés par une barre verticale (« | »).

```
-- Creating external file format (delimited text file)
CREATE EXTERNAL FILE FORMAT text_file_format 
WITH (
	FORMAT_TYPE = DELIMITEDTEXT, 
	FORMAT_OPTIONS (
		FIELD_TERMINATOR ='|', 
		USE_TYPE_DEFAULT = TRUE
	)
);
```

PolyBase peut utiliser les données compressées et non compressées dans du texte délimité, les formats Hive RCFILE et HIVE ORC.

Rubrique de référence : [CREATE EXTERNAL FILE FORMAT (Transact-SQL)][].

## Créer une table externe

La définition de table externe est similaire à la définition d’une table relationnelle. Les principales différences sont l’emplacement et le format des données. La définition de la table externe est stockée dans la base de données SQL Data Warehouse. Les données sont stockées à l’emplacement spécifié par la source de données.

L’option LOCATION spécifie le chemin d’accès aux données à partir de la racine de la source de données. Dans cet exemple, les données sont conservées à l’emplacement « wasbs://mycontainer@ test.blob.core.windows.net/path/Demo/ ».

```
-- Creating external table pointing to file stored in Azure Storage
CREATE EXTERNAL TABLE [ext].[CarSensor_Data] (
    [SensorKey] int NOT NULL, 
    [CustomerKey] int NOT NULL, 
    [GeographyKey] int NULL, 
    [Speed] float NOT NULL, 
    [YearMeasured] int NOT NULL
)
WITH (LOCATION='/Demo/',
      DATA_SOURCE = azure_storage,
      FILE_FORMAT = text_file_format,      
);
```

> [AZURE.NOTE]Notez qu’à ce stade il est impossible de générer des statistiques sur une table externe.

L’ensemble des fichiers d’une table doivent être stockés dans le même dossier logique du stockage d’objets Blob Microsoft Azure. Nous vous recommandons, dans la mesure du possible, de répartir vos données Microsoft Azure Storage en fichiers présentant une taille maximale de 1 Go pour le traitement parallèle avec SQL Data Warehouse.

Rubrique de référence : [CREATE EXTERNAL TABLE (Transact-SQL)][].

Les objets que vous venez de créer sont stockés dans la base de données SQL Data Warehouse. Vous pouvez les consulter dans l’Explorateur d’objets SQL Server Data Tools (SSDT).


## Interroger des données de l’espace de stockage d’objets Blob Microsoft Azure
Les requêtes dirigées vers les tables externes utilisent le nom de table, comme s’il s’agissait d’une table relationnelle.

Il s’agit d’une requête ad hoc qui rassemble les données d’assurance du client stockées dans SQL Data Warehouse et les données des capteurs automobiles conservées dans les objets Blob Microsoft Azure Storage. Le résultat indique les conducteurs les plus rapides.

```
-- Join SQL Data Warehouse relational data with Azure storage data. 
SELECT 
    [Insured_Customers].[FirstName],
    [Insured_Customers].[LastName],
    [Insured_Customers].[YearlyIncome],
    [CarSensor_Data].[Speed]
FROM [dbo].[Insured_Customers] INNER JOIN [ext].[CarSensor_Data]
ON [Insured_Customers].[CustomerKey] = [CarSensor_Data].[CustomerKey]
WHERE [CarSensor_Data].[Speed] > 60 
ORDER BY [CarSensor_Data].[Speed] desc;
```

## Charger des données à partir d’objets Blob Microsoft Azure Storage
Dans cet exemple, les données des objets Blob Microsoft Azure Storage sont chargées dans la base de données SQL Data Warehouse.

En stockant directement les données, vous éliminez l’intervalle de transfert de données associé aux requêtes. Le stockage des données avec un index columnstore multiplie jusqu’à dix fois les performances des requêtes d’analyse.

Dans cet exemple, l’instruction CREATE TABLE AS SELECT est utilisée pour charger des données. La nouvelle table hérite des colonnes désignées dans la requête. Elle hérite des types de données de ces colonnes à partir de la définition de table externe.

CREATE TABLE AS SELECT est une instruction Transact-SQL très performante qui remplace INSERT...SELECT. Initialement développée pour le moteur de traitement massivement parallèle d’Analytics Platform System, elle est désormais disponible dans SQL Data Warehouse.

```
-- Load data from Azure blob storage to SQL Data Warehouse 

CREATE TABLE [dbo].[Customer_Speed]
WITH (
	CLUSTERED COLUMNSTORE INDEX
	DISTRIBUTION = HASH([CarSensor_Data].[CustomerKey])
	)
AS SELECT * from [ext].[CarSensor_Data];
```

Voir [CREATE TABLE AS SELECT (Transact-SQL)][].


## Limitations
Le chargement avec PolyBase prend en charge uniquement le style d’encodage UTF-8. Pour les autres styles d’encodage, comme UTF-16, envisagez de recourir à l’utilitaire bcp, à SSIS ou à Azure Data Factory pour charger les données dans la base de données SQL Data Warehouse.

## Étapes suivantes
Pour obtenir des conseils supplémentaires en matière de développement, voir la [vue d’ensemble sur le développement][].

<!--Image references-->

<!--Article references-->
[Load data with bcp]: sql-data-warehouse-load-with-bcp.md
[Load with PolyBase]: sql-data-warehouse-load-with-polybase.md
[solution partners]: sql-data-warehouse-solution-partners.md
[vue d’ensemble sur le développement]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->
[supported source/sink]: https://msdn.microsoft.com/library/dn894007.aspx
[copy activity]: https://msdn.microsoft.com/library/dn835035.aspx
[SQL Server destination adapter]: https://msdn.microsoft.com/library/ms141095.aspx
[SSIS]: https://msdn.microsoft.com/library/ms141026.aspx


<!-- External Links -->
[CREATE EXTERNAL DATA SOURCE (Transact-SQL)]: https://msdn.microsoft.com/library/dn935022(v=sql.130).aspx
[CREATE EXTERNAL FILE FORMAT (Transact-SQL)]: https://msdn.microsoft.com/library/dn935026(v=sql.130).aspx
[CREATE EXTERNAL TABLE (Transact-SQL)]: https://msdn.microsoft.com/library/dn935021(v=sql.130).aspx
[CREATE TABLE AS SELECT (Transact-SQL)]: https://msdn.microsoft.com/library/mt204041.aspx
[CREATE MASTER KEY (Transact-SQL)]: https://msdn.microsoft.com/fr-fr/library/ms174382.aspx
[CREATE CREDENTIAL (Transact-SQL)]: https://msdn.microsoft.com/fr-fr/library/ms189522.aspx
[DROP CREDENTIAL (Transact-SQL)]: https://msdn.microsoft.com/fr-fr/library/ms189450.aspx

<!---HONumber=July15_HO4-->