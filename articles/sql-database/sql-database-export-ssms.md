---
title: "SSMS : Exporter une base de données dans un fichier BACPAC (Azure) | Microsoft Docs"
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
ms.workload: sqldb-migrate
ms.date: 02/07/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 3f0af43c103e34535fd114e33c40da010ea69d69
ms.openlocfilehash: 87d9ae3a5c9c8b8edb02e212f652de5f2f796188


---
# <a name="export-an-azure-sql-database-or-a-sql-server-database-to-a-bacpac-file-using-sql-server-management-studio"></a>Exporter une base de données SQL Azure ou une base de données SQL Server dans un fichier BACPAC à l’aide de SQL Server Management Studio        
        
Cet article explique la procédure d’exportation d’une base de données SQL Azure ou d’une base de données SQL Server dans un fichier [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) à l’aide de l’Assistant Exportation d’une Application de couche données dans SQL Server Management Studio. Pour une vue d’ensemble de l’exportation dans un fichier BACPAC, consultez [Export to a BACPAC](sql-database-export.md) (Exporter dans un fichier BACPAC).    
        
1. Assurez-vous de disposer de la dernière version de SQL Server Management Studio. Les nouvelles versions de Management Studio sont mises à jour tous les mois afin de refléter les mises à jour publiées sur le portail Azure.        
           
   > [!IMPORTANT]        
   > Nous vous recommandons d’utiliser systématiquement la dernière version de Management Studio afin de rester en cohérence avec les mises à jour de Microsoft Azure et Base de données SQL. [Version la plus récente de SSMS](https://msdn.microsoft.com/library/mt238290.aspx).        
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
* [Version la plus récente de SSMS](https://msdn.microsoft.com/library/mt238290.aspx)
* Pour en savoir plus sur l’importation d’un fichier BACPAC dans une base de données SQL Server, consultez [Importer un fichier BACPCAC dans une base de données SQL Server](https://msdn.microsoft.com/library/hh710052.aspx)
* Pour une description du processus complet de migration d’une base de données SQL Server, y compris les recommandations relatives aux performances, consultez [Migration de base de données SQL Server vers SQL Database dans le cloud](sql-database-cloud-migrate.md).
* Pour en savoir plus sur la rétention des sauvegardes à long terme d’une sauvegarde de base de données SQL Azure comme alternative à l’exportation d’une base de données à des fins d’archivage, consultez [Rétention à long terme](sql-database-long-term-retention.md)


    



<!--HONumber=Feb17_HO2-->


