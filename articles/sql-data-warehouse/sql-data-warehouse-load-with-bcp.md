<properties
   pageTitle="Utilisation de bcp pour charger des données dans SQL Data Warehouse | Microsoft Azure"
   description="Découvrez bcp et apprenez comment utiliser cette solution avec les scénarios d’entreposage de données."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="TwoUnder"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="03/23/2016"
   ms.author="mausher;barbkess;sonyama"/>


# Charger des données avec bcp

> [AZURE.SELECTOR]
- [Data Factory](sql-data-warehouse-get-started-load-with-azure-data-factory.md)
- [PolyBase](sql-data-warehouse-get-started-load-with-polybase.md)
- [BCP](sql-data-warehouse-load-with-bcp.md)


**[bcp][]** est un utilitaire de ligne de commande de chargement par lots qui vous permet de charger des données entre SQL Server, des fichiers de données et SQL Data Warehouse. Utilisez bcp pour importer un nombre important de lignes dans les tables SQL Data Warehouse ou pour exporter des données des tables SQL Server dans les fichiers de données. L’utilitaire bcp, sauf lorsqu’il est utilisé avec l’option queryout, ne nécessite aucune connaissance de Transact-SQL.

bcp permet d’importer et d’exporter rapidement et simplement des ensembles de données plus petits de la base de données SQL Data Warehouse. La quantité exacte de données qu’il est recommandé de charger/extraire via bcp dépend de la connexion de votre réseau au centre de données Microsoft Azure. Généralement, des tables de dimension peuvent être chargées et extraites, mais les tables de faits légèrement plus grandes nécessitent des intervalles de chargement ou d’extraction légèrement plus importants.

Avec bcp, vous pouvez :

- Utiliser un utilitaire en ligne de commande simple pour charger des données dans SQL Data Warehouse.
- Utiliser un utilitaire en ligne de commande simple pour extraire des données de SQL Data Warehouse.

Ce didacticiel vous explique comment :

- Importer des données dans une table à l’aide de la commande bcp in
- Exporter des données d’une table à l’aide de la commande bcp out

>[AZURE.VIDEO loading-data-into-azure-sql-data-warehouse-with-bcp]

## Configuration requise

Pour parcourir ce didacticiel, vous avez besoin des éléments suivants :

- Base de données SQL Data Warehouse
- Utilitaire en ligne de commande bcp installé
- Utilitaire en ligne de commande SQLCMD installé

>[AZURE.NOTE] Pour télécharger les utilitaires bcp et sqlcmd, accédez au [Centre de téléchargement Microsoft][].

## Importer des données dans SQL Data Warehouse

Dans ce didacticiel, vous allez créer une table dans Azure SQL Data Warehouse et importer des données dans la table.

### Étape 1 : Créer une table dans Azure SQL Data Warehouse

À partir d’une invite de commande, connectez-vous à votre instance à l’aide de la commande suivante, qui remplace les valeurs de manière appropriée :

```sql
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I
```
Une fois que vous êtes connecté, copiez le script de table suivant sur l’invite sqlcmd, puis appuyez sur la touche Entrée :

```sql
CREATE TABLE DimDate2
(
    DateId INT NOT NULL,
    CalendarQuarter TINYINT NOT NULL,
    FiscalQuarter TINYINT NOT NULL
)
WITH
(
    CLUSTERED COLUMNSTORE INDEX,
    DISTRIBUTION = ROUND_ROBIN
);
GO
```
>[AZURE.NOTE] Consultez la rubrique [Conception de tables][] dans le groupe de rubriques sur le développement pour obtenir plus d'informations sur les options disponibles dans la clause WITH.

### Étape 2 : Créer un fichier de données source

Ouvrez le Bloc-notes et copiez les lignes de données suivantes dans un nouveau fichier.

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

Enregistrez-les dans votre répertoire temporaire local, C:\\Temp\\DimDate2.txt.

> [AZURE.NOTE] Il est important de se souvenir que bcp.exe ne prend pas en charge le codage de fichier UTF-8. Utilisez les fichiers ASCII codés ou le codage UTF-16 pour vos fichiers lors de l’utilisation de bcp.exe.

### Étape 3 : Connecter et importer les données
Grâce à bcp, vous pouvez connecter et importer les données à l’aide de la commande suivante, qui remplace de manière appropriée les valeurs :

```sql
bcp DimDate2 in C:\Temp\DimDate2.txt -S <Server Name> -d <Database Name> -U <Username> -P <password> -q -c -t  ','
```

Pour vérifier que les données ont été chargées, établissez préalablement une connexion avec sqlcmd, puis exécutez la commande TSQL suivante :

```sql
SELECT * FROM DimDate2 ORDER BY 1;
GO
```

Les résultats suivants doivent s’afficher :

DateId |CalendarQuarter |FiscalQuarter
----------- |--------------- |-------------
20150101 |1 |3
20150201 |1 |3
20150301 |1 |3
20150401 |2 |4
20150501 |2 |4
20150601 |2 |4
20150701 |3 |1
20150801 |3 |1
20150801 |3 |1
20151001 |4 |2
20151101 |4 |2
20151201 |4 |2

### Étape 4 : Créer des statistiques sur vos données nouvellement chargées

Azure SQL Data Warehouse ne prend pas encore en charge les statistiques à création ou mise à jour automatique. Pour optimiser les performances de vos requêtes, il est important de créer les statistiques sur toutes les colonnes de toutes les tables après le premier chargement ou après toute modification substantielle dans les données. Pour une explication détaillée des statistiques, consultez la rubrique [Statistiques][] dans le groupe de rubriques sur le développement. Voici un exemple rapide de la création de statistiques sur le tableau chargé dans cet exemple

À partir d'une invite sqlcmd, exécutez les instructions CREATE STATISTICS suivantes :

```sql
create statistics [DateId] on [DimDate2] ([DateId]);
create statistics [CalendarQuarter] on [DimDate2] ([CalendarQuarter]);
create statistics [FiscalQuarter] on [DimDate2] ([FiscalQuarter]);
GO
```

## Exporter des données de SQL Data Warehouse
Dans ce didacticiel, vous allez créer un fichier de données à partir d’une table de SQL Data Warehouse. Nous allons exporter les données créées plus haut vers un nouveau fichier de données, DimDate2\_export.txt.

### Étape 1 : Exporter les données

L’utilitaire bcp vous permet de connecter et d’exporter les données à l’aide de la commande suivante, qui remplace de manière appropriée les valeurs :

```sql
bcp DimDate2 out C:\Temp\DimDate2_export.txt -S <Server Name> -d <Database Name> -U <Username> -P <password> -q -c -t ','
```
Pour vérifier que les données ont été exportées, ouvrez le nouveau fichier. Les données du fichier doivent correspondre au texte ci-dessus :

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

>[AZURE.NOTE] En raison de la nature des systèmes distribués, l’ordre des données peut ne pas être identique entre les différentes bases de données SQL Data Warehouse. Vous pouvez éventuellement utiliser le paramètre queryout pour spécifier la requête Transact-SQL à exécuter.

## Étapes suivantes
Pour consulter une vue d’ensemble sur le chargement, accédez à la rubrique [Chargement de données dans SQL Data Warehouse][]. Pour obtenir des conseils supplémentaires en matière de développement, consultez l’article [Vue d’ensemble sur le développement SQL Data Warehouse][].

<!--Image references-->

<!--Article references-->

[Chargement de données dans SQL Data Warehouse]: ./sql-data-warehouse-overview-load.md
[Vue d’ensemble sur le développement SQL Data Warehouse]: ./sql-data-warehouse-overview-develop.md
[Conception de tables]: ./sql-data-warehouse-develop-table-design.md
[Statistiques]: ./sql-data-warehouse-develop-statistics.md


<!--MSDN references-->
[bcp]: https://msdn.microsoft.com/library/ms162802.aspx


<!--Other Web references-->
[Centre de téléchargement Microsoft]: http://www.microsoft.com/download/details.aspx?id=36433

<!---HONumber=AcomDC_0330_2016-->