---
title: "Portail Azure : Exporter une base de données SQL Azure dans un fichier BACPAC | Microsoft Docs"
description: "Exporter une base de données SQL Azure dans un fichier BACPAC à l’aide du Portail Azure"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: migrate and move
ms.devlang: NA
ms.date: 02/07/2017
ms.author: carlrab
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA
translationtype: Human Translation
ms.sourcegitcommit: 3d04be3d2427bc59d24bfaad227730991b61265b
ms.openlocfilehash: ed983134e468a69c0e89387a3211479630a278e3


---
# <a name="export-an-azure-sql-database-to-a-bacpac-file-using-the-azure-portal"></a>Exporter une base de données SQL Azure dans un fichier BACPAC à l’aide du Portail Azure

Cet article fournit des instructions pour exporter votre base de données SQL Azure dans un fichier BACPAC (stocké dans le Stockage Blob Azure) à l’aide du [Portail Azure](https://portal.azure.com). Pour une vue d’ensemble de l’exportation dans un fichier BACPAC, consultez [Export to a BACPAC](sql-database-export.md) (Exporter dans un fichier BACPAC).

> [!NOTE]
> Vous pouvez également exporter votre fichier de base de données SQL Azure dans un fichier BACPAC à l’aide de [SQL Server Management Studio](sql-database-export-ssms.md), [PowerShell](sql-database-export-powershell.md) ou [SQLPackage](sql-database-export-sqlpackage.md).
>

## <a name="prerequisites"></a>Composants requis

Pour effectuer ce qui est décrit dans cet article, vous avez besoin des éléments suivants :

* Un abonnement Azure.
* Une base de données SQL Azure. 
* Un [compte Azure Storage standard](../storage/storage-create-storage-account.md) avec un conteneur d’objets blob pour stocker le fichier BACPAC dans le stockage standard.

## <a name="export-your-database"></a>Exporter votre base de données

1. Accédez au [portail Azure](https://portal.azure.com).
2. Ouvrez le panneau Base de données SQL de la base de données que vous voulez exporter :
3. Assurez-vous qu’aucune transaction ne se produira lors de l’exportation. 

   > [!IMPORTANT]
   > Pour garantir un fichier BACPAC cohérent au niveau transactionnel, nous recommandons de [créer une copie de votre base de données](sql-database-copy.md), puis d’exporter la copie de base de données. 
   > 

4. Cliquez sur **Bases de données SQL**.
5. Cliquez sur la base de données à archiver.
6. Dans le panneau SQL Database, cliquez sur **Exporter** pour ouvrir le panneau **Exporter la base de données** :
   
   ![bouton exporter][1]
7. Cliquez sur **Stockage** et sélectionnez votre compte de stockage et le conteneur d’objets blob où sera stocké le fichier BACPAC :
   
   ![Exporter la base de données][2]
8. Sélectionnez le type d’authentification. 
9. Entrez les informations d’identification appropriées pour le serveur SQL Azure qui contient la base de données que vous exportez.
10. Cliquez sur **OK** pour exporter la base de données. En cliquant sur **OK** , une demande d’exportation de la base de données est créée et envoyée au service. La durée de l’exportation dépend de la taille et de la complexité de votre base de données, ainsi que de votre niveau de service. 
11. Consultez la notification que vous recevez.
   
   ![exporter la notification][3]

## <a name="monitor-the-progress-of-the-export-operation"></a>Surveillez la progression de l’opération d’exportation
1. Cliquez sur **Serveurs SQL**.
2. Cliquez sur le serveur contenant la base de données (source) d’origine que vous avez archivée.
3. Faites défiler vers Opérations.
4. Dans le panneau du serveur SQL, cliquez sur **Historique d'Import/Export**:
   
   ![historique d’import export][4]

## <a name="verify-the-bacpac-is-in-your-storage-container"></a>Vérifiez que le fichier BACPAC se trouve dans votre conteneur de stockage
1. Cliquez sur **Comptes de stockage**.
2. Cliquez sur le compte de stockage où vous avez stocké l’archive BACPAC.
3. Cliquez sur **Conteneurs** et sélectionnez le conteneur dans lequel vous avez exporté la base de données pour obtenir les détails (vous pouvez télécharger et enregistrer le fichier BACPAC ici).
   
   ![détails du fichier .bacpac][5]    

## <a name="next-steps"></a>Étapes suivantes

* Pour en savoir plus sur la rétention des sauvegardes à long terme d’une sauvegarde de base de données SQL Azure comme alternative à l’exportation d’une base de données à des fins d’archivage, consultez [Rétention à long terme](sql-database-long-term-retention.md)
* Pour en savoir plus sur l’importation d’un fichier BACPAC dans une base de données SQL Server, consultez [Importer un fichier BACPCAC dans une base de données SQL Server](https://msdn.microsoft.com/library/hh710052.aspx)

<!--Image references-->
[1]: ./media/sql-database-export/export.png
[2]: ./media/sql-database-export/export-blade.png
[3]: ./media/sql-database-export/export-notification.png
[4]: ./media/sql-database-export/export-history.png
[5]: ./media/sql-database-export/bacpac-archive.png




<!--HONumber=Feb17_HO2-->


