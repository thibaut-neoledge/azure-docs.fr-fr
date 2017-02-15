---
title: "Charger des exemples de données dans SQL Data Warehouse | Documents Microsoft"
description: "Charger des exemples de données dans SQL Data Warehouse"
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jhubbard
editor: 
ms.assetid: e338ecf8-cfee-419b-b7b6-98108d381c62
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 10/31/2016
ms.author: barbkess
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 9edad2037787a7a2da4e2a6fcce15ace51d41032


---
# <a name="load-sample-data-into-sql-data-warehouse"></a>Charger des exemples de données dans SQL Data Warehouse
Suivez ces étapes simples pour charger et interroger l’exemple de base de données Adventure Works. Ces scripts utilisent d’abord sqlcmd pour exécuter SQL qui crée des tables et des vues. Une fois que les tables ont été créées, les scripts utilisent bcp pour charger les données.  Si sqlcmd et bcp ne sont pas encore installés, suivez ces liens pour [installer bcp][installer bcp] et pour [installer sqlcmd][installer sqlcmd].

## <a name="load-sample-data"></a>Charger les exemples de données
1. Téléchargez le fichier ZIP [Exemples de scripts Adventure Works pour SQL Data Warehouse][Exemples de scripts Adventure Works pour SQL Data Warehouse].
2. Extrayez les fichiers du fichier zip téléchargé dans un répertoire sur votre ordinateur local.
3. Modifiez le fichier extrait aw_create.bat et définissez les variables suivantes au début du fichier.  Veillez à ne laisser aucun espace entre le « = » et le paramètre.  Vous trouverez ci-dessous des exemples de ce à quoi vos modifications peuvent ressembler.
   
    ```
    server=mylogicalserver.database.windows.net
    user=mydwuser
    password=Mydwpassw0rd
    database=mydwdatabase
    ```
4. À partir d'une invite de commande Windows, exécutez le fichier aw_create.bat modifié.  Assurez-vous que vous êtes dans le répertoire où vous avez enregistré votre version modifiée du fichier aw_create.bat.
   Ce script va...
   
   * Supprimer les tables ou vues Adventure Works qui existent déjà dans votre base de données.
   * Créer les tables et les vues Adventure Works.
   * Charger chaque table Adventure Works à l'aide de bcp.
   * Valider le nombre de lignes pour chaque table Adventure Works.
   * Collecter les statistiques sur chaque colonne pour chaque table Adventure Works.

## <a name="query-sample-data"></a>Interrogation des données de l'exemple
Lorsque vous avez chargé des exemples de données dans SQL Data Warehouse, vous pouvez exécuter rapidement quelques requêtes.  Pour exécuter une requête, connectez-vous à la base de données Adventure Works que vous venez de créer dans Azure SQL DW avec Visual Studio et SSDT, comme le décrit le document [Effectuer des requêtes avec Visual Studio][Effectuer des requêtes avec Visual Studio].

Exemple d'instruction simple select pour obtenir toutes les informations des employés :

```sql
SELECT * FROM DimEmployee;
```

Exemple de requête plus complexe à l'aide de constructions telles que GROUP BY pour examiner le montant total de toutes les ventes chaque jour :

```sql
SELECT OrderDateKey, SUM(SalesAmount) AS TotalSales
FROM FactInternetSales
GROUP BY OrderDateKey
ORDER BY OrderDateKey;
```

Exemple d'instruction SELECT avec une clause WHERE pour filtrer les commandes antérieures à une date donnée :

```
SELECT OrderDateKey, SUM(SalesAmount) AS TotalSales
FROM FactInternetSales
WHERE OrderDateKey > '20020801'
GROUP BY OrderDateKey
ORDER BY OrderDateKey;
```

SQL Data Warehouse prend en charge presque toutes les constructions T-SQL prises en charge par SQL Server.  Toutes les différences sont documentées dans notre documentation sur la [migration du code][migration du code].

## <a name="next-steps"></a>Étapes suivantes
Maintenant que vous avez pu tester certaines requêtes avec des exemples de données, découvrez comment effectuer un [développer][développer], un [charger][charger] ou une [migrer][migrer] dans SQL Data Warehouse.

<!--Image references-->

<!--Article references-->
[migrer]: sql-data-warehouse-overview-migrate.md
[développer]: sql-data-warehouse-overview-develop.md
[charger]: sql-data-warehouse-overview-load.md
[Effectuer des requêtes avec Visual Studio]: sql-data-warehouse-query-visual-studio.md
[migration du code]: sql-data-warehouse-migrate-code.md
[installer bcp]: sql-data-warehouse-load-with-bcp.md
[installer sqlcmd]: sql-data-warehouse-get-started-connect-sqlcmd.md

<!--Other Web references-->
[Exemples de scripts Adventure Works pour SQL Data Warehouse]: https://migrhoststorage.blob.core.windows.net/sqldwsample/AdventureWorksSQLDW2012.zip



<!--HONumber=Nov16_HO3-->


