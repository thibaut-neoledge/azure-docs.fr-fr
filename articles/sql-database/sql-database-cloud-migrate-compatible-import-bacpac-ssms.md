---
title: "SSMS : Importer un fichier BACPAC dans Azure SQL Database | Microsoft Docs"
description: "Cet article explique la procédure d’importation depuis un fichier BACPAC vers Base de données SQL à l’aide de l’Assistant Exportation d’une application de couche données dans SQL Server Management Studio."
keywords: "Microsoft Azure SQL Database, déployer base de données, migration base de données, importer base de données, exporter base de données, assistant de migration"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 24375fc6-c94c-43ef-97ec-fce77343b581
ms.service: sql-database
ms.custom: migrate and move
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: sqldb-migrate
ms.date: 11/08/2016
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 86bc7d89bb5725add8ba05b6f0978467147fd3ca
ms.openlocfilehash: b5a6d90069e87d6115a3b80711f06ccf286b3e88


---
# <a name="import-from-bacpac-to-sql-database-using-ssms"></a>Importation depuis BACPAC vers Base de données SQL avec SSMS
> [!div class="op_single_selector"]
> * [SSMS](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
> * [SqlPackage](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md)
> * [Portail Azure](sql-database-import.md)
> * [PowerShell](sql-database-import-powershell.md)
> 
> 

Cet article explique la procédure d’importation depuis un fichier [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) vers Base de données SQL à l’aide de l’Assistant Exportation d’une Application de couche données dans SQL Server Management Studio.

> [!NOTE]
> Les étapes suivantes supposent que vous avez déjà déployé votre instance logique SQL Azure, et que vous disposez des informations de connexion.
> 
> 

1. Assurez-vous de disposer de la dernière version de SQL Server Management Studio. Les nouvelles versions de Management Studio sont mises à jour tous les mois afin de refléter les mises à jour publiées sur le portail Azure.
   
   > [!IMPORTANT]
   > Nous vous recommandons d’utiliser systématiquement la dernière version de Management Studio afin de rester en cohérence avec les mises à jour de Microsoft Azure et Base de données SQL. [Mettre à jour SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).
   > 
   > 
2. Connectez-vous à votre serveur Azure SQL Database, cliquez avec le bouton droit sur le dossier **Bases de données**, puis cliquez sur **Importer une application de la couche Données…**
   
    ![Importer l’élément de menu de l’application de la couche Données](./media/sql-database-cloud-migrate/MigrateUsingBACPAC03.png)
3. Pour créer la base de données dans Base de données SQL Azure, importez un fichier BACPAC à partir de votre disque local ou sélectionnez le compte de stockage Azure et le conteneur vers lesquels vous avez téléchargé votre fichier BACPAC.
   
   ![Paramètres d’importation](./media/sql-database-cloud-migrate/MigrateUsingBACPAC04.png)
   
   > [!IMPORTANT]
   > Lorsque vous importez un fichier BACPAC à partir d’un stockage d’objets blob Azure, utilisez le stockage standard. L’importation d’un fichier BACPAC à partir de Premium Storage n’est pas prise en charge.
   > 
   > 
4. Fournissez le **Nouveau nom de la base de données** pour la base de données sur Azure SQL DB, définissez **l’Edition de Microsoft Azure SQL Database** (niveau de service), la **Taille maximale de base de données** et **l’Objectif de service** (niveau de performance).
   
   ![Paramètres de base de données](./media/sql-database-cloud-migrate/MigrateUsingBACPAC05.png)
5. Cliquez sur **Suivant**, puis cliquez sur **Terminer** pour importer le fichier BACPAC dans une nouvelle base de données sur le serveur Azure SQL Database.
6. À l’aide de l’Explorateur d’objets, connectez-vous à la base de données que vous venez de déployer sur votre serveur de base de données SQL Azure.
7. Dans le portail Azure, affichez votre base de données et ses propriétés.

## <a name="next-steps"></a>Étapes suivantes
* [Version la plus récente de SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
* [Version la plus récente de SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)

## <a name="additional-resources"></a>Ressources supplémentaires
* [Fonctionnalités SQL Database](sql-database-features.md)
* [Fonctions partiellement ou non prises en charge de Transact-SQL](sql-database-transact-sql-information.md)
* [Migration de bases de données non-SQL Server avec l’Assistant Migration SQL Server](http://blogs.msdn.com/b/ssma/)




<!--HONumber=Jan17_HO1-->


