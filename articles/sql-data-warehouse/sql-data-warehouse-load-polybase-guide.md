<properties
   pageTitle="Guide d'utilisation de PolyBase dans SQL Data Warehouse | Microsoft Azure"
   description="Instructions et recommandations relatives à l'utilisation de PolyBase dans les scénarios SQL Data Warehouse."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sahaj08"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="03/03/2016"
   ms.author="sahajs;barbkess;sonyama"/>


# Guide d'utilisation de PolyBase dans SQL Data Warehouse

Ce guide fournit des informations pratiques pour l'utilisation de PolyBase dans SQL Data Warehouse.

Pour commencer, suivez le didacticiel [Charger des données avec PolyBase][].


## Rotation des clés de stockage

De temps à autre, vous devez modifier la clé d'accès à votre stockage d'objets blob pour des raisons de sécurité.

La façon la plus élégante d’effectuer cette tâche consiste à suivre un processus appelé « rotation des clés ». Vous avez peut-être remarqué que vous disposez de deux clés de stockage pour votre compte de stockage d'objets blob. Cela permet une transition.

La rotation de vos clés de compte de stockage Azure est un processus simple en trois étapes.

1. Créez le deuxième fichier d’informations d’identification de niveau base de données en fonction de la clé d'accès de stockage secondaire.
2. Créez la deuxième source de données externe en fonction de ces nouvelles informations d'identification.
3. Supprimez et créez la ou les tables externes pointant vers la nouvelle source de données externes.

Lorsque vous avez migré toutes vos tables externes vers la nouvelle source de données externes, vous pouvez effectuer les tâches de nettoyage :

1. suppression de la première source de données externe ;
2. suppression du premier fichier d’informations d’identification de niveau base de données en fonction de la clé d'accès de stockage secondaire ;
3. connexion à Azure et régénération de la clé d'accès primaire, pour la prochaine fois.

## Interroger des données de l’espace de stockage d’objets Blob Microsoft Azure
Les requêtes dirigées vers les tables externes utilisent le nom de table, comme s’il s’agissait d’une table relationnelle.

```

-- Query Azure storage resident data via external table.
SELECT * FROM [ext].[CarSensor_Data]
;

```

> [AZURE.NOTE] Une requête sur une table externe peut échouer avec l’erreur *« Requête abandonnée--le seuil de rejet maximal a été atteint lors de la lecture à partir d’une source externe »*. Cela indique que vos données externes contiennent des enregistrements *à l’intégrité compromise*. Un enregistrement de données est considéré comme « compromis » si les types de données / le nombre de colonnes réels ne correspondent pas aux définitions de colonne de la table externe ou si les données ne sont pas conformes au format de fichier externe spécifié. Pour résoudre ce problème, assurez-vous que les définitions de format de votre table externe et de votre fichier externe sont correctes et que vos données externes sont conformes à ces définitions. Dans le cas où un sous-ensemble d'enregistrements de données externes serait compromis, vous pouvez choisir de rejeter ces enregistrements pour vos requêtes en utilisant les options de rejet dans le DDL CREATE EXTERNAL TABLE.


## Charger des données à partir d’objets Blob Microsoft Azure Storage
Dans cet exemple, les données des objets Blob Microsoft Azure Storage sont chargées dans la base de données SQL Data Warehouse.

En stockant directement les données, vous éliminez l’intervalle de transfert de données associé aux requêtes. Le stockage des données avec un index columnstore multiplie jusqu’à dix fois les performances des requêtes d’analyse.

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

L’exemple suivant crée une table externe Weblogs2014 à l’aide des définitions de colonne et des données de la table dbo.Weblogs. La définition de la table externe est stockée dans SQL Data Warehouse et les résultats de l’instruction SELECT sont exportés vers le répertoire « /archive/log2014 » sous le conteneur d’objets blob spécifié par la source de données. Les données sont exportées au format de fichier texte spécifié.

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


## Contournement de la nécessité du codage UTF-8 de PolyBase
À présent PolyBase prend en charge le chargement des fichiers de données encodés en UTF-8. Étant donné que UTF-8 utilise le même codage de caractères que ASCII, PolyBase prend également en charge le chargement de données encodées en ASCII. Toutefois, PolyBase ne gère pas d’autre encodage de caractères, comme UTF-16/Unicode ou les caractères ASCII étendus. ASCII étendu inclut les caractères accentués tels que le umlaut, courant en allemand.

Pour contourner cette exigence, la meilleure solution consiste à réécrire au format UTF-8.

Il existe plusieurs façons de le faire. Voici deux méthodes utilisant Powershell :

### Exemple simple pour les petits fichiers

Voici un script Powershell simple d'une ligne qui crée le fichier.

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
Pour plus d'informations sur le déplacement de données vers SQL Data Warehouse, consultez la [vue d'ensemble sur la migration des données][].

<!--Image references-->

<!--Article references-->
[Load data with bcp]: sql-data-warehouse-load-with-bcp.md
[Charger des données avec PolyBase]: sql-data-warehouse-get-started-load-with-polybase.md
[solution partners]: sql-data-warehouse-solution-partners.md
[development overview]: sql-data-warehouse-overview-develop.md
[Statistiques]: sql-data-warehouse-develop-statistics.md
[vue d'ensemble sur la migration des données]: sql-data-warehouse-overview-migrate.md

<!--MSDN references-->
[supported source/sink]: https://msdn.microsoft.com/library/dn894007.aspx
[copy activity]: https://msdn.microsoft.com/library/dn835035.aspx
[SQL Server destination adapter]: https://msdn.microsoft.com/library/ms141095.aspx
[SSIS]: https://msdn.microsoft.com/library/ms141026.aspx


<!-- External Links -->
[CREATE EXTERNAL DATA SOURCE (Transact-SQL)]: https://msdn.microsoft.com/library/dn935022.aspx
[CREATE EXTERNAL FILE FORMAT (Transact-SQL)]: https://msdn.microsoft.com/library/dn935026).aspx
[CREATE EXTERNAL TABLE (Transact-SQL)]: https://msdn.microsoft.com/library/dn935021.aspx

[DROP EXTERNAL DATA SOURCE (Transact-SQL)]: https://msdn.microsoft.com/library/mt146367.aspx
[DROP EXTERNAL FILE FORMAT (Transact-SQL)]: https://msdn.microsoft.com/library/mt146379.aspx
[DROP EXTERNAL TABLE (Transact-SQL)]: https://msdn.microsoft.com/library/mt130698.aspx

[CREATE TABLE AS SELECT (Transact-SQL)]: https://msdn.microsoft.com/library/mt204041.aspx
[INSERT...SELECT (Transact-SQL)]: https://msdn.microsoft.com/library/ms174335.aspx
[CREATE MASTER KEY (Transact-SQL)]: https://msdn.microsoft.com/library/ms174382.aspx
[CREATE CREDENTIAL (Transact-SQL)]: https://msdn.microsoft.com/library/ms189522.aspx
[CREATE DATABASE SCOPED CREDENTIAL (Transact-SQL)]: https://msdn.microsoft.com/library/mt270260.aspx
[DROP CREDENTIAL (Transact-SQL)]: https://msdn.microsoft.com/library/ms189450.aspx

<!---------HONumber=AcomDC_0309_2016-->