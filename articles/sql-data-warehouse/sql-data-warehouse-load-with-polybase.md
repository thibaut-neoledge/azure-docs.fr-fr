<properties
   pageTitle="Didacticiel PolyBase dans SQL Data Warehouse | Microsoft Azure"
   description="Découvrez PolyBase et apprenez comment utiliser cette solution avec les scénarios d’entreposage de données."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="barbkess"
   manager="jhubbard"
   editor="jrowlandjones"/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="09/22/2015"
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

> [AZURE.IMPORTANT]Les types de compte de stockage Azure pris en charge par PolyBase sont : + Standard Locally Redundant Storage (Standard-LRS) + Standard Geo-Redundant Storage (Standard-GRS) + Standard Read-Access Geo-Redundant Storage (Standard-RAGRS) Les types de compte Standard Zone Redundant Storage (Standard-ZRS) et Premium Locally Redundant Storage (Premium-LRS) ne sont pas pris en charge par PolyBase. Si vous créez un compte de stockage Azure, veillez à sélectionner un type de compte de stockage pris en charge par PolyBase dans le niveau tarifaire.


## Créer une clé principale de base de données
Connectez-vous à la base de données utilisateur de votre serveur pour créer une clé principale de base de données. Cette clé est utilisée pour chiffrer les informations secrètes d’identification au cours de l’étape suivante.

```
-- Creating master key
CREATE MASTER KEY;
```

Rubrique de référence : [CREATE MASTER KEY (Transact-SQL)][].

## Créer un fichier d’informations d’identification de niveau base de données
Pour accéder au stockage d’objets Blob Microsoft Azure, vous devez créer un fichier d’informations d’identification de niveau base de données qui conserve les informations d’identification dont vous avez besoin pour accéder à votre compte Microsoft Azure Storage. Connectez-vous à votre base de données Data Warehouse et créez un fichier d’informations d’identification de base de données pour chacun des comptes Microsoft Azure Storage pour lesquels vous souhaitez bénéficier d’un accès. Spécifiez un nom d’identité et votre clé secrète de compte Microsoft Azure Storage. Le nom d’identité n’affecte aucunement l’identification à Microsoft Azure Storage.

Pour voir s’il existe déjà des informations d’identification de base de données, utilisez sys.database\_credentials, et non sys.credentials, qui affiche uniquement les informations d’identification de serveur.

```
-- Check for existing database-scoped credentials.
SELECT * FROM sys.database_credentials;

-- Create a database scoped credential
CREATE DATABASE SCOPED CREDENTIAL ASBSecret 
WITH IDENTITY = 'joe'
,    Secret = '<azure_storage_account_key>'
;
```

Rubrique de référence : [CREATE CREDENTIAL (Transact-SQL)][].

Pour supprimer un fichier d’informations d’identification de niveau base de données, utilisez simplement la syntaxe suivante :

```
-- Dropping credential
DROP DATABASE SCOPED CREDENTIAL ASBSecret
;
```

Rubrique de référence : [DROP CREDENTIAL (Transact-SQL)][].

## Créer une source de données externe
La source de données externe est un objet de base de données qui stocke l’emplacement des données des objets Blob Microsoft Azure Storage et vos informations d’accès. Vous devez définir une source de données externes pour chacun des conteneurs Microsoft Azure Storage auquel vous souhaitez accéder.

```
-- Creating external data source (Azure Blob Storage) 
CREATE EXTERNAL DATA SOURCE azure_storage 
WITH
(
    TYPE = HADOOP
,   LOCATION ='wasbs://mycontainer@test.blob.core.windows.net'
,   CREDENTIAL = ASBSecret
)
;
```

Rubrique de référence : [CREATE EXTERNAL DATA SOURCE (Transact-SQL)][].

Pour supprimer la source de données externes, la syntaxe est la suivante :

```
-- Dropping external data source
DROP EXTERNAL DATA SOURCE azure_storage
;
```

Rubrique de référence : [DROP EXTERNAL DATA SOURCE (Transact-SQL)][].

## Créer un format de fichier externe
Le format de fichier externe est un objet de base de données qui spécifie le format des données externes. Dans cet exemple, nous avons décompressé les données dans un fichier texte et les champs sont séparés par une barre verticale (« | »).

```
-- Creating external file format (delimited text file)
CREATE EXTERNAL FILE FORMAT text_file_format 
WITH 
(   
    FORMAT_TYPE = DELIMITEDTEXT 
,	FORMAT_OPTIONS  (
                        FIELD_TERMINATOR ='|'
                    ,   USE_TYPE_DEFAULT = TRUE
                    )
)
;
```

PolyBase peut utiliser les données compressées et non compressées dans du texte délimité, les formats Hive RCFILE et HIVE ORC.

Rubrique de référence : [CREATE EXTERNAL FILE FORMAT (Transact-SQL)][].

Pour supprimer un format de fichier externe, la syntaxe est la suivante :

```
-- Dropping external file format
DROP EXTERNAL FILE FORMAT text_file_format
;
```
Rubrique de référence : [DROP EXTERNAL FILE FORMAT (Transact-SQL)][].

## Créer une table externe

La définition de table externe est similaire à la définition d’une table relationnelle. Les principales différences sont l’emplacement et le format des données. La définition de la table externe est stockée dans la base de données SQL Data Warehouse. Les données sont stockées à l’emplacement spécifié par la source de données.

L’option LOCATION spécifie le chemin d’accès aux données à partir de la racine de la source de données. Dans cet exemple, les données sont conservées à l’emplacement « wasbs://mycontainer@ test.blob.core.windows.net/path/Demo/ ». L’ensemble des fichiers d’une table doivent être stockés dans le même dossier logique du stockage d’objets Blob Microsoft Azure.

Si vous le souhaitez, vous pouvez également spécifier des options de rejet (REJECT\_TYPE, REJECT\_VALUE, REJECT\_SAMPLE\_VALUE) qui déterminent la façon dont PolyBase gérera les enregistrements à l’intégrité compromise qu'il reçoit de la source de données externe.

```
-- Creating external table pointing to file stored in Azure Storage
CREATE EXTERNAL TABLE [ext].[CarSensor_Data] 
(
     [SensorKey]     int    NOT NULL 
,    [CustomerKey]   int    NOT NULL 
,    [GeographyKey]  int        NULL 
,    [Speed]         float  NOT NULL 
,    [YearMeasured]  int    NOT NULL
)
WITH 
(
    LOCATION    = '/Demo/'
,   DATA_SOURCE = azure_storage
,   FILE_FORMAT = text_file_format      
)
;
```

> [AZURE.NOTE]Notez qu’à ce stade il est impossible de générer des statistiques sur une table externe.

Rubrique de référence : [CREATE EXTERNAL TABLE (Transact-SQL)][].

Les objets que vous venez de créer sont stockés dans la base de données SQL Data Warehouse. Vous pouvez les consulter dans l’Explorateur d’objets SQL Server Data Tools (SSDT).

Pour supprimer une table externe, vous devez utiliser la syntaxe suivante :

```
--Dropping external table
DROP EXTERNAL TABLE [ext].[CarSensor_Data]
;
```

> [AZURE.NOTE]Lors de la suppression d’une table externe, vous devez utiliser `DROP EXTERNAL TABLE`. Vous **ne pouvez pas** utiliser `DROP TABLE`.

Rubrique de référence : [DROP EXTERNAL TABLE (Transact-SQL)][].

Il est également important de noter que les tables externes sont visibles dans les affichages catalogue `sys.tables` et plus particulièrement `sys.external_tables`.

## Rotation des clés de stockage

De temps à autre, vous devez modifier la clé d'accès à votre stockage d'objets blob pour des raisons de sécurité.

La façon la plus élégante d’effectuer cette tâche consiste à suivre un processus appelé « rotation des clés ». Vous avez peut-être remarqué que vous disposez de deux clés de stockage pour votre compte de stockage d'objets blob. Cela permet une transition.

La rotation de vos clés de compte de stockage Azure est un processus simple en trois étapes.

1. Créez le deuxième fichier d’informations d’identification de niveau base de données en fonction de la clé d'accès de stockage secondaire.
2. Créez la deuxième source de données externe en fonction de ces nouvelles informations d'identification.
3. Supprimez et créez la ou les tables externes pointant vers la nouvelle source de données externes.

Lorsque vous avez migré toutes vos tables externes vers la nouvelle source de données externes, vous pouvez effectuer les tâches de nettoyage :
 
1. suppression de la première source de données externe ;
2. suppression du premier fichier d’informations d’identification de niveau base de données en fonction de la clé d'accès de stockage secondaire ;
3. connexion à Azure et régénération de la clé d'accès primaire, pour la prochaine fois.

## Interroger des données de l’espace de stockage d’objets Blob Microsoft Azure
Les requêtes dirigées vers les tables externes utilisent le nom de table, comme s’il s’agissait d’une table relationnelle.


```

-- Query Azure storage resident data via external table. 
SELECT * FROM [ext].[CarSensor_Data]
;

```

> [AZURE.NOTE]Une requête sur une table externe peut échouer avec l’erreur *« Requête abandonnée--le seuil de rejet maximal a été atteint lors de la lecture à partir d’une source externe »*. Cela indique que vos données externes contiennent des enregistrements *à l’intégrité compromise*. Un enregistrement de données est considéré comme « compromis » si les types de données / le nombre de colonnes réels ne correspondent pas aux définitions de colonne de la table externe ou si les données ne sont pas conformes au format de fichier externe spécifié. Pour résoudre ce problème, assurez-vous que les définitions de format de votre table externe et de votre fichier externe sont correctes et que vos données externes sont conformes à ces définitions. Dans le cas où un sous-ensemble d'enregistrements de données externes serait compromis, vous pouvez choisir de rejeter ces enregistrements pour vos requêtes en utilisant les options de rejet dans le DDL CREATE EXTERNAL TABLE.


## Charger des données à partir d’objets Blob Microsoft Azure Storage
Dans cet exemple, les données des objets Blob Microsoft Azure Storage sont chargées dans la base de données SQL Data Warehouse.

En stockant directement les données, vous éliminez l’intervalle de transfert de données associé aux requêtes. Le stockage des données avec un index columnstore multiplie jusqu’à dix fois les performances des requêtes d’analyse.

Dans cet exemple, l’instruction CREATE TABLE AS SELECT est utilisée pour charger des données. La nouvelle table hérite des colonnes désignées dans la requête. Elle hérite des types de données de ces colonnes à partir de la définition de table externe.

CREATE TABLE AS SELECT est une instruction Transact-SQL très performante qui remplace INSERT...SELECT. Initialement développée pour le moteur de traitement massivement parallèle d’Analytics Platform System, elle est désormais disponible dans SQL Data Warehouse.

```
-- Load data from Azure blob storage to SQL Data Warehouse 

CREATE TABLE [dbo].[Customer_Speed]
WITH 
(   
    CLUSTERED COLUMNSTORE INDEX
,	DISTRIBUTION = HASH([CarSensor_Data].[CustomerKey])
)
AS 
SELECT * 
FROM   [ext].[CarSensor_Data]
;
```

Voir [CREATE TABLE AS SELECT (Transact-SQL)][].


## Contournement de la nécessité du codage UTF-8 de PolyBase
À présent PolyBase prend en charge le chargement des fichiers de données encodés en UTF-8. Étant donné que UTF-8 utilise le même codage de caractères que ASCII, PolyBase prend également en charge le chargement de données encodées en ASCII. Toutefois, PolyBase ne gère pas d’autre encodage de caractères, comme UTF-16/Unicode ou les caractères ASCII étendus. ASCII étendu inclut les caractères accentués tels que le umlaut, courant en allemand.

Pour contourner cette exigence, la meilleure solution consiste à réécrire au format UTF-8.

Il existe plusieurs façons de le faire. Voici deux méthodes utilisant Powershell :

### Exemple simple pour les petits fichiers

Voici un script Powershell simple d'une ligne qui crée le fichier.
 
```
Get-Content <input_file_name> -Encoding Unicode | Set-Content <output_file_name> -Encoding utf8
```

Toutefois, si c'est un moyen simple de ré-encoder les données, ce n'est en aucun cas le plus efficace. L'exemple de diffusion en continu d'E/S ci-dessous est beaucoup plus rapide et donne le même résultat.

### Exemple de diffusion d’E/S pour les plus gros fichiers

L'exemple de code ci-dessous est plus complexe, mais il est beaucoup plus efficace, car il diffuse les lignes de données de la source à la cible. Utilisez cette approche pour les fichiers plus volumineux.

```
#Static variables
$ascii = [System.Text.Encoding]::ASCII
$utf16le = [System.Text.Encoding]::Unicode
$utf8 = [System.Text.Encoding]::UTF8
$ansi = [System.Text.Encoding]::Default
$append = $False

#Set source file path and file name
$src = [System.IO.Path]::Combine("C:\input_file_path","input_file_name.txt")

#Set source file encoding (using list above)
$src_enc = $ansi

#Set target file path and file name
$tgt = [System.IO.Path]::Combine("C:\output_file_path","output_file_name.txt")

#Set target file encoding (using list above)
$tgt_enc = $utf8

$read = New-Object System.IO.StreamReader($src,$src_enc)
$write = New-Object System.IO.StreamWriter($tgt,$append,$tgt_enc)

while ($read.Peek() -ne -1)
{
    $line = $read.ReadLine();
    $write.WriteLine($line);
}
$read.Close()
$read.Dispose()
$write.Close()
$write.Dispose()
```

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

[DROP EXTERNAL DATA SOURCE (Transact-SQL)]: https://msdn.microsoft.com/fr-FR/library/mt146367.aspx
[DROP EXTERNAL FILE FORMAT (Transact-SQL)]: https://msdn.microsoft.com/fr-FR/library/mt146379.aspx
[DROP EXTERNAL TABLE (Transact-SQL)]: https://msdn.microsoft.com/fr-FR/library/mt130698.aspx

[CREATE TABLE AS SELECT (Transact-SQL)]: https://msdn.microsoft.com/library/mt204041.aspx
[CREATE MASTER KEY (Transact-SQL)]: https://msdn.microsoft.com/fr-FR/library/ms174382.aspx
[CREATE CREDENTIAL (Transact-SQL)]: https://msdn.microsoft.com/fr-FR/library/ms189522.aspx
[DROP CREDENTIAL (Transact-SQL)]: https://msdn.microsoft.com/fr-FR/library/ms189450.aspx

<!---HONumber=Oct15_HO3-->