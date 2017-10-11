---
title: "Charger des exemples de données dans SQL Data Warehouse | Documents Microsoft"
description: "Charger des exemples de données dans SQL Data Warehouse"
services: sql-data-warehouse
documentationcenter: NA
author: ckarst
manager: jhubbard
editor: 
ms.assetid: e338ecf8-cfee-419b-b7b6-98108d381c62
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: loading
ms.date: 10/31/2016
ms.author: cakarst;barbkess
ms.openlocfilehash: 1e0df958a2f18fe1e988168918e5cfd293f84e64
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2017
---
# <a name="load-sample-data-into-sql-data-warehouse"></a>Charger des exemples de données dans SQL Data Warehouse
Suivez ces étapes simples pour charger et interroger l’exemple de base de données Adventure Works. Ces scripts utilisent d’abord sqlcmd pour exécuter SQL qui crée des tables et des vues. Une fois que les tables ont été créées, les scripts utilisent bcp pour charger les données.  Si sqlcmd et bcp ne sont pas encore installés, suivez ces liens pour [installer bcp][install bcp] et [installer sqlcmd][install sqlcmd].

## <a name="load-sample-data"></a>Charger les exemples de données
1. Téléchargez le fichier zip [Exemples de scripts Adventure Works pour SQL Data Warehouse][Adventure Works Sample Scripts for SQL Data Warehouse].
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
Lorsque vous avez chargé des exemples de données dans SQL Data Warehouse, vous pouvez exécuter rapidement quelques requêtes.  Pour exécuter une requête, connectez-vous à votre base de données Adventure Works nouvellement créée dans Azure SQL DW avec Visual Studio et SSDT, comme le décrit le document [Effectuer des requêtes avec Visual Studio][query with Visual Studio].

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

SQL Data Warehouse prend en charge presque toutes les constructions T-SQL prises en charge par SQL Server.  Toutes les différences sont documentées dans notre documentation sur la [migration du code][migrate code].

## <a name="next-steps"></a>Étapes suivantes
Maintenant que vous avez eu l’occasion d’essayer certaines requêtes avec des exemples de données, découvrez comment [développer][develop], [charger][load] ou [migrer][migrate] vers SQL Data Warehouse.

<!--Image references-->

<!--Article references-->
[migrate]: sql-data-warehouse-overview-migrate.md
[develop]: sql-data-warehouse-overview-develop.md
[load]: sql-data-warehouse-overview-load.md
[query with Visual Studio]: sql-data-warehouse-query-visual-studio.md
[migrate code]: sql-data-warehouse-migrate-code.md
[install bcp]: sql-data-warehouse-load-with-bcp.md
[install sqlcmd]: sql-data-warehouse-get-started-connect-sqlcmd.md

<!--Other Web references-->
[Adventure Works Sample Scripts for SQL Data Warehouse]: https://migrhoststorage.blob.core.windows.net/sqldwsample/AdventureWorksSQLDW2012.zip
