---
title: "SSMS : exportation d’une base de données SQL Server vers un fichier BACPAC (Azure) | Microsoft Docs"
description: "Cet article explique la procédure d’exportation d’une base de données SQL Server vers un fichier BACPAC à l’aide de l’Assistant Exportation d’une application de couche données dans SQL Server Management Studio."
keywords: "Base de données SQL Microsoft Azure, migration de base de données, exportation de base de données, exportation de fichier BACPAC, Assistant d’exportation d’application de couche Données"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 19c2dab4-81a6-411d-b08a-0ef79b90fbce
ms.service: sql-database
ms.custom: migrate and move
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-management
ms.date: 11/08/2016
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 86bc7d89bb5725add8ba05b6f0978467147fd3ca
ms.openlocfilehash: f0238ac34a4a047bd0286a1a87d2427caf0e06fd


---
# <a name="export-a-sql-server-database-to-a-bacpac-file-using-sql-server-management-studio"></a>Exporter une base de données SQL Server vers un fichier BACPAC à l’aide de SQL Server Management Studio
> [!div class="op_single_selector"]
> * [Portail Azure](sql-database-export.md)
> * [SSMS](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md)
> * [SqlPackage](sql-database-cloud-migrate-compatible-export-bacpac-sqlpackage.md)
> * [PowerShell](sql-database-export-powershell.md)
> 

Cet article explique la procédure d’exportation d’une base de données SQL Server vers un fichier [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) à l’aide de l’Assistant Exportation d’une Application de couche données dans SQL Server Management Studio. 

1. Assurez-vous de disposer de la dernière version de SQL Server Management Studio. Les nouvelles versions de Management Studio sont mises à jour tous les mois afin de refléter les mises à jour publiées sur le portail Azure.
   
   > [!IMPORTANT]
   > Nous vous recommandons d’utiliser systématiquement la dernière version de Management Studio afin de rester en cohérence avec les mises à jour de Microsoft Azure et Base de données SQL. [Mettre à jour SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).
   > 
   > 
2. Ouvrez Management Studio et connectez-vous à votre base de données source dans l’Explorateur d’objets.
   
    ![Exporter une application de la couche Données à partir du menu Tâches](./media/sql-database-cloud-migrate/MigrateUsingBACPAC01.png)
3. Dans l’Explorateur d’objets, cliquez avec le bouton droit de la souris sur la base de données source, pointez sur **Tâches**, puis cliquez sur **Exporter une application de la couche Données…**
   
    ![Exporter une application de la couche Données à partir du menu Tâches](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS01.png)
4. Dans l’Assistant Exportation, configurez l’exportation de manière à enregistrer le fichier BACPAC sur le disque local ou dans un objet blob Azure. Le fichier BACPAC exporté inclut toujours le schéma de base de données complet et, par défaut, les données de toutes les tables. Utilisez l’onglet Avancé si vous souhaitez exclure les données de toutes les tables ou de certaines d’entre elles uniquement. Vous pouvez, par exemple, choisir d’exporter uniquement les données des tables de référence au lieu d’exporter toutes les tables.

    ![Paramètres d’exportation](./media/sql-database-cloud-migrate/MigrateUsingBACPAC02.png)

   > [!IMPORTANT]
   > Lorsque vous exportez un fichier BACPAC vers Stockage Blob Azure, utilisez le stockage standard. L’importation d’un fichier BACPAC à partir de Premium Storage n’est pas prise en charge.
   >
   
## <a name="next-steps"></a>Étapes suivantes
* [Version la plus récente de SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
* [Version la plus récente de SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)
* [Importer un fichier BACPAC vers une base de données SQL Azure à l’aide de SSMS](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
* [Importer un fichier BACPAC vers une base de données SQL Azure à l’aide de SqlPackage](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md)
* [Importer un fichier BACPAC vers une base de données SQL Azure à l’aide du Portail Azure](sql-database-import.md)
* [Importer un fichier BACPAC vers une base de données SQL Azure à l’aide de PowerShell](sql-database-import-powershell.md)

## <a name="additional-resources"></a>Ressources supplémentaires
* [Fonctionnalités SQL Database](sql-database-features.md)
* [Fonctions partiellement ou non prises en charge de Transact-SQL](sql-database-transact-sql-information.md)
* [Migration de bases de données non-SQL Server avec l’Assistant Migration SQL Server](http://blogs.msdn.com/b/ssma/)




<!--HONumber=Jan17_HO1-->


