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
   ms.date="11/04/2015"
   ms.author="sahajs;barbkess"/>


# Charger des données avec PolyBase
La technologie PolyBase vous permet d’interroger et de rassembler des données de sources multiples, à l’aide de commandes Transact-SQL.

Grâce à PolyBase, vous êtes en mesure d’interroger des données stockées dans des objets Blob Microsoft Azure Storage et de les charger dans une base de données SQL Data Warehouse en appliquant la méthode suivante :

- Créez la clé principale et les informations d’identification de la base de données.
- Créez des objets PolyBase : source de données externe, format de fichier externe et table externe. 
- Interrogez les données stockées dans des objets Blob Microsoft Azure Storage.
- Chargez les données conservées dans des objets Blob Microsoft Azure Storage dans SQL Data Warehouse.
- Exportez les données de SQL Data Warehouse vers le stockage d’objets blob Azure.


## Configuration requise
Pour parcourir ce didacticiel, vous avez besoin des éléments suivants :

- Compte Azure Storage
- Données stockées dans les objets Blob Microsoft Azure Storage en tant que fichiers textes délimités

Toute d’abord, vous allez créer les objets dont a besoin PolyBase pour se connecter aux données des objets Blob Microsoft Azure Storage et les interroger.

> [AZURE.IMPORTANT]Les types de compte de stockage Azure pris en charge par PolyBase sont :
> 
> + Stockage localement redondant standard (Standard-LRS)
> + Stockage géo-redondant standard (Standard-GRS)
> + Stockage géo-redondant avec accès en lecture standard (Standard-RAGRS)
>
> Les types de compte de stockage redondant de zone standard (Standard-ZRS) et de stockage localement redondant Premium (Premium-LRS) ne sont PAS pris en charge par PolyBase. Si vous créez un compte de stockage Azure, veillez à sélectionner un type de compte de stockage pris en charge par PolyBase dans le niveau tarifaire.

## Étape 1 : stocker des informations d'identification dans votre base de données
Pour accéder au stockage d’objets Blob Microsoft Azure, vous devez créer un fichier d’informations d’identification de niveau base de données qui conserve les informations d’identification dont vous avez besoin pour accéder à votre compte Microsoft Azure Storage. Procédez comme suit pour stocker des informations d'identification avec votre base de données.

1. Connectez-vous à votre base de données SQL Data Warehouse .
2. Utilisez [CREATE MASTER KEY (Transact-SQL)][] pour créer une clé principale pour votre base de données. Si votre base de données a déjà une clé principale, vous n'avez pas besoin d'en créer une autre. Cette clé est utilisée pour chiffrer vos informations d’identification « secrètes » au cours de l’étape suivante.

    ```
    -- Create a E master key
    CREATE MASTER KEY;
    ```

1. Vérifiez si vous disposez déjà d'informations d'identification de base de données. Pour ce faire, utilisez la vue système sys.database\_credentials, et non les sys.credentials qui affichent uniquement les informations d'identification du serveur.

    ```
    -- Vérifiez les informations d'identification de base de données existantes.
    SELECT * FROM sys.database\_credentials;

3. Utilisez [CREATE CREDENTIAL (Transact-SQL)][] pour créer des informations d'identification de niveau base de données pour chaque compte de stockage Azure auquel vous souhaitez accéder. Dans cet exemple, IDENTITY est un nom convivial pour les informations d'identification. Il n'affecte aucunement l'authentification à Azure Storage. SECRET est votre clé de compte de stockage Azure.

    -- Créer un fichier d'informations d'identification de niveau base de données CREATE DATABASE SCOPED CREDENTIAL ASBSecret WITH IDENTITY = 'joe' , Secret = '<azure_storage_account_key>' ; ```

1. Si vous devez supprimer des informations d’identification de niveau base de données, utilisez [DROP CREDENTIAL (Transact-SQL)][] :

```
-- Dropping credential
DROP DATABASE SCOPED CREDENTIAL ASBSecret
;
```

## Étape 2 : créer une source de données externe
La source de données externe est un objet de base de données qui stocke l’emplacement des données des objets Blob Microsoft Azure Storage et vos informations d’accès. Utilisez [CREATE EXTERNAL DATA SOURCE (Transact-SQL)][] pour définir une source de données externe pour chaque objet blob de stockage Azure auquel vous souhaitez accéder.

    ```
    -- Create an external data source for an Azure storage blob
    CREATE EXTERNAL DATA SOURCE azure_storage 
    WITH
    (
        TYPE = HADOOP,
        LOCATION ='wasbs://mycontainer@test.blob.core.windows.net',
        CREDENTIAL = ASBSecret
    )
    ;
    ```

Si vous devez supprimer la table externe, utilisez [DROP EXTERNAL DATA SOURCE][] :

    ```
    -- Drop an external data source
    DROP EXTERNAL DATA SOURCE azure_storage
    ;
    ```

## Étape 3 : créer un format de fichier externe
Le format de fichier externe est un objet de base de données qui spécifie le format des données externes. PolyBase peut utiliser les données compressées et non compressées dans du texte délimité, les formats Hive RCFILE et HIVE ORC.

Utilisez [CREATE EXTERNAL FILE FORMAT (Transact-SQL)][] pour créer le format de fichier externe. Cet exemple spécifie que les données dans le fichier sont décompressées et que les champs sont séparés par une barre verticale (« | »).

```
-- Create an external file format for a text-delimited file.
-- Data is uncompressed and fields are separated with the
-- pipe character.
CREATE EXTERNAL FILE FORMAT text_file_format 
WITH 
(   
    FORMAT_TYPE = DELIMITEDTEXT, 
    FORMAT_OPTIONS  
    (
        FIELD_TERMINATOR ='|',
        USE_TYPE_DEFAULT = TRUE
    )
)
;
```

Si vous avez besoin de supprimer un format de fichier externe, utilisez [DROP EXTERNAL FILE FORMAT].

```
-- Dropping external file format
DROP EXTERNAL FILE FORMAT text_file_format
;
```

## Créer une table externe

La définition de table externe est similaire à la définition d’une table relationnelle. Les principales différences sont l’emplacement et le format des données.

- La définition de la table externe est stockée comme métadonnée dans la base de données SQL Data Warehouse. 
- Les données sont stockées dans l’emplacement externe spécifié par la source de données.

Utilisez [CREATE EXTERNAL TABLE (Transact-SQL)][] pour définir la table externe.

L’option LOCATION spécifie le chemin d’accès aux données à partir de la racine de la source de données. Dans cet exemple, les données sont conservées à l’emplacement suivant : wasbs://mycontainer@ test.blob.core.windows.net/path/Demo/. L’ensemble des fichiers d’une même table doit être stocké dans le même dossier logique d’Azure Blob Storage.

Si vous le souhaitez, vous pouvez également spécifier des options de rejet (REJECT\_TYPE, REJECT\_VALUE, REJECT\_SAMPLE\_VALUE) qui déterminent la façon dont PolyBase gérera les enregistrements à l’intégrité compromise qu'il reçoit de la source de données externe.

```
-- Creating an external table for data in Azure blob storage.
CREATE EXTERNAL TABLE [ext].[CarSensor_Data] 
(
     [SensorKey]     int    NOT NULL,
     [CustomerKey]   int    NOT NULL,
     [GeographyKey]  int        NULL,
     [Speed]         float  NOT NULL,
     [YearMeasured]  int    NOT NULL,
)
WITH 
(
    LOCATION    = '/Demo/',
    DATA_SOURCE = azure_storage,
    FILE_FORMAT = text_file_format      
)
;
```

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

CREATE TABLE AS SELECT est une instruction Transact-SQL très performante qui charge les données en parallèle sur tous les nœuds de calcul de votre entrepôt SQL Data Warehouse. Initialement développée pour le moteur de traitement massivement parallèle d’Analytics Platform System, elle est désormais disponible dans SQL Data Warehouse.

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

## Créer des statistiques sur des données nouvellement chargées

Azure SQL Data Warehouse ne prend pas encore en charge les statistiques à création ou mise à jour automatique. Pour optimiser les performances de vos requêtes, il est important de créer les statistiques sur toutes les colonnes de toutes les tables après le premier chargement ou après toute modification substantielle dans les données. Pour une explication détaillée des statistiques, consultez la rubrique [Statistiques][] dans le groupe de rubriques sur le développement. Voici un exemple rapide de la création de statistiques sur le tableau chargé dans cet exemple.

```
create statistics [SensorKey] on [Customer_Speed] ([SensorKey]);
create statistics [CustomerKey] on [Customer_Speed] ([CustomerKey]);
create statistics [GeographyKey] on [Customer_Speed] ([GeographyKey]);
create statistics [Speed] on [Customer_Speed] ([Speed]);
create statistics [YearMeasured] on [Customer_Speed] ([YearMeasured]);
```

## Exportation de données vers le stockage d’objets blob Azure
Cette section montre comment exporter les données de SQL Data Warehouse vers le stockage d’objets blob Azure. Cet exemple utilise CREATE EXTERNAL TABLE AS SELECT, une instruction Transact-SQL très performante, pour exporter les données en parallèle à partir de tous les nœuds de calcul.

L’exemple suivant crée une table externe Weblogs2014 à l’aide des définitions de colonne et des données de la table dbo.Weblogs. La définition de la table externe est stockée dans SQL Data Warehouse et les résultats de l’instruction SELECT sont exportés vers le répertoire « /archive/log2014 » sous le conteneur d’objets blob spécifié par la source de données. Les données sont exportées au format de fichier texte spécifié.

```
CREATE EXTERNAL TABLE Weblogs2014 WITH
(
    LOCATION='/archive/log2014/',
    DATA_SOURCE=azure_storage,
    FILE_FORMAT=text_file_format
)
AS
SELECT
    Uri,
    DateRequested
FROM
    dbo.Weblogs
WHERE
    1=1
    AND DateRequested > '12/31/2013'
    AND DateRequested < '01/01/2015';
```


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
[Statistiques]: sql-data-warehouse-develop-statistics.md

<!--MSDN references-->
[supported source/sink]: https://msdn.microsoft.com/library/dn894007.aspx
[copy activity]: https://msdn.microsoft.com/library/dn835035.aspx
[SQL Server destination adapter]: https://msdn.microsoft.com/library/ms141095.aspx
[SSIS]: https://msdn.microsoft.com/library/ms141026.aspx


<!-- External Links -->
[CREATE EXTERNAL DATA SOURCE (Transact-SQL)]: https://msdn.microsoft.com/library/dn935022(v=sql.130).aspx
[CREATE EXTERNAL FILE FORMAT (Transact-SQL)]: https://msdn.microsoft.com/library/dn935026(v=sql.130).aspx
[CREATE EXTERNAL TABLE (Transact-SQL)]: https://msdn.microsoft.com/library/dn935021(v=sql.130).aspx

[DROP EXTERNAL DATA SOURCE (Transact-SQL)]: https://msdn.microsoft.com/library/mt146367.aspx
[DROP EXTERNAL FILE FORMAT (Transact-SQL)]: https://msdn.microsoft.com/library/mt146379.aspx
[DROP EXTERNAL TABLE (Transact-SQL)]: https://msdn.microsoft.com/library/mt130698.aspx

[CREATE TABLE AS SELECT (Transact-SQL)]: https://msdn.microsoft.com/library/mt204041.aspx
[CREATE MASTER KEY (Transact-SQL)]: https://msdn.microsoft.com/library/ms174382.aspx
[CREATE CREDENTIAL (Transact-SQL)]: https://msdn.microsoft.com/library/ms189522.aspx
[DROP CREDENTIAL (Transact-SQL)]: https://msdn.microsoft.com/library/ms189450.aspx

<!---HONumber=AcomDC_1210_2015-->