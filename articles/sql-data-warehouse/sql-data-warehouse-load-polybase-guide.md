---
title: "Guide d’utilisation de PolyBase dans SQL Data Warehouse | Microsoft Docs"
description: "Instructions et recommandations relatives à l&quot;utilisation de PolyBase dans les scénarios SQL Data Warehouse."
services: sql-data-warehouse
documentationcenter: NA
author: ckarst
manager: barbkess
editor: 
ms.assetid: 4757fce1-96b3-48ea-8a51-be1385705f9f
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 6/5/2016
ms.custom: loading
ms.author: cakarst;barbkess
ms.translationtype: Human Translation
ms.sourcegitcommit: 80be19618bd02895d953f80e5236d1a69d0811af
ms.openlocfilehash: 6938b92d8e5b46d908dc5b2155bdfdc89bb1dc8c
ms.contentlocale: fr-fr
ms.lasthandoff: 06/07/2017



---
# <a name="guide-for-using-polybase-in-sql-data-warehouse"></a>Guide d'utilisation de PolyBase dans SQL Data Warehouse
Ce guide fournit des informations pratiques pour l'utilisation de PolyBase dans SQL Data Warehouse.

Pour commencer, suivez le didacticiel [Charger des données avec PolyBase][Load data with PolyBase].

## <a name="rotating-storage-keys"></a>Rotation des clés de stockage
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

## <a name="query-azure-blob-storage-data"></a>Interroger des données de l’espace de stockage d’objets Blob Microsoft Azure
Les requêtes dirigées vers les tables externes utilisent le nom de table, comme s’il s’agissait d’une table relationnelle.

```sql
-- Query Azure storage resident data via external table.
SELECT * FROM [ext].[CarSensor_Data]
;
```

> [!NOTE]
> Une requête sur une table externe peut échouer avec l’erreur *« Requête abandonnée : le seuil de rejet maximal a été atteint lors de la lecture à partir d’une source externe »*. Cela indique que vos données externes contiennent des enregistrements *à l’intégrité compromise* . Un enregistrement de données est considéré comme « compromis » si les types de données / le nombre de colonnes réels ne correspondent pas aux définitions de colonne de la table externe ou si les données ne sont pas conformes au format de fichier externe spécifié. Pour résoudre ce problème, assurez-vous que les définitions de format de votre table externe et de votre fichier externe sont correctes et que vos données externes sont conformes à ces définitions. Dans le cas où un sous-ensemble d'enregistrements de données externes serait compromis, vous pouvez choisir de rejeter ces enregistrements pour vos requêtes en utilisant les options de rejet dans le DDL CREATE EXTERNAL TABLE.
> 
> 

## <a name="load-data-from-azure-blob-storage"></a>Charger des données à partir d’objets Blob Microsoft Azure Storage
Dans cet exemple, les données des objets Blob Microsoft Azure Storage sont chargées dans la base de données SQL Data Warehouse.

En stockant directement les données, vous éliminez l’intervalle de transfert de données associé aux requêtes. Le stockage des données avec un index columnstore multiplie jusqu’à dix fois les performances des requêtes d’analyse.

Dans cet exemple, l’instruction CREATE TABLE AS SELECT est utilisée pour charger des données. La nouvelle table hérite des colonnes désignées dans la requête. Elle hérite des types de données de ces colonnes à partir de la définition de table externe.

CREATE TABLE AS SELECT est une instruction Transact-SQL très performante qui charge les données en parallèle sur tous les nœuds de calcul de votre entrepôt SQL Data Warehouse.  Initialement développée pour le moteur MPP (Massively Parallel Processing) d’Analytics Platform System, elle est désormais disponible dans SQL Data Warehouse.

```sql
-- Load data from Azure blob storage to SQL Data Warehouse

CREATE TABLE [dbo].[Customer_Speed]
WITH
(   
    CLUSTERED COLUMNSTORE INDEX
,    DISTRIBUTION = HASH([CarSensor_Data].[CustomerKey])
)
AS
SELECT *
FROM   [ext].[CarSensor_Data]
;
```

Consultez [CREATE TABLE AS SELECT (Transact-SQL)][CREATE TABLE AS SELECT (Transact-SQL)].

## <a name="create-statistics-on-newly-loaded-data"></a>Créer des statistiques sur des données nouvellement chargées
Azure SQL Data Warehouse ne prend pas encore en charge les statistiques à création ou mise à jour automatique.  Pour optimiser les performances de vos requêtes, il est important de créer les statistiques sur toutes les colonnes de toutes les tables après le premier chargement ou après toute modification substantielle dans les données.  Pour obtenir une explication détaillée des statistiques, consultez la rubrique [Statistiques][Statistics] dans le groupe de rubriques sur le développement.  Voici un exemple rapide de la création de statistiques sur le tableau chargé dans cet exemple.

```sql
create statistics [SensorKey] on [Customer_Speed] ([SensorKey]);
create statistics [CustomerKey] on [Customer_Speed] ([CustomerKey]);
create statistics [GeographyKey] on [Customer_Speed] ([GeographyKey]);
create statistics [Speed] on [Customer_Speed] ([Speed]);
create statistics [YearMeasured] on [Customer_Speed] ([YearMeasured]);
```

## <a name="export-data-to-azure-blob-storage"></a>Exportation de données vers le stockage d’objets blob Azure
Cette section montre comment exporter les données de SQL Data Warehouse vers le stockage d’objets blob Azure. Cet exemple utilise CREATE EXTERNAL TABLE AS SELECT, une instruction Transact-SQL très performante, pour exporter les données en parallèle à partir de tous les nœuds de calcul.

L’exemple suivant crée une table externe Weblogs2014 à l’aide des définitions de colonne et des données de la table dbo.Weblogs. La définition de la table externe est stockée dans SQL Data Warehouse et les résultats de l’instruction SELECT sont exportés vers le répertoire « /archive/log2014 » sous le conteneur d’objets blob spécifié par la source de données. Les données sont exportées au format de fichier texte spécifié.

```sql
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
## <a name="isolate-loading-users"></a>Isoler les utilisateurs du chargement
Il est souvent nécessaire d’avoir plusieurs utilisateurs qui peuvent charger des données dans un entrepôt de données SQL. Étant donné que [CREATE TABLE AS SELECT (Transact-SQL)][CREATE TABLE AS SELECT (Transact-SQL)] nécessite des autorisations CONTROL de la base de données, vous obtenez plusieurs utilisateurs avec un accès au contrôle sur tous les schémas. Pour limiter ce problème, vous pouvez utiliser l’instruction DENY CONTROL.

Par exemple, les schémas de base de données schema_A pour le service A et schema_B pour le service B laissent les utilisateurs de base de données user_A et user_B être utilisateurs du chargement PolyBase dans les services A et B, respectivement. Ils ont tous deux reçu des autorisations de base de données CONTROL.
Les créateurs des schémas A et B verrouillent maintenant leurs schémas à l’aide de DENY :

```sql
   DENY CONTROL ON SCHEMA :: schema_A TO user_B;
   DENY CONTROL ON SCHEMA :: schema_B TO user_A;
```   
 Suite à cette opération, user_A et user_B ne doivent maintenant plus avoir accès au schéma de l’autre service.
 


## <a name="next-steps"></a>Étapes suivantes
Pour en savoir plus sur le déplacement de données dans SQL Data Warehouse, consultez la [vue d’ensemble de la migration des données][data migration overview].

<!--Image references-->

<!--Article references-->
[Load data with bcp]: ./sql-data-warehouse-load-with-bcp.md
[Load data with PolyBase]: ./sql-data-warehouse-get-started-load-with-polybase.md
[Statistics]: ./sql-data-warehouse-tables-statistics.md
[data migration overview]: ./sql-data-warehouse-overview-migrate.md

<!--MSDN references-->
[supported source/sink]: https://msdn.microsoft.com/library/dn894007.aspx
[copy activity]: https://msdn.microsoft.com/library/dn835035.aspx
[SQL Server destination adapter]: https://msdn.microsoft.com/library/ms141095.aspx
[SSIS]: https://msdn.microsoft.com/library/ms141026.aspx

[CREATE EXTERNAL DATA SOURCE (Transact-SQL)]: https://msdn.microsoft.com/library/dn935022.aspx
[CREATE EXTERNAL FILE FORMAT (Transact-SQL)]: https://msdn.microsoft.com/library/dn935026.aspx
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

<!-- External Links -->

