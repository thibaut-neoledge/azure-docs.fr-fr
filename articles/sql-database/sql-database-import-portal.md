---
title: "Importer un fichier BACPAC pour créer une base de données SQL Azure | Microsoft Docs"
description: "Créer une base de données SQL Azure en important un fichier BACPAC existant."
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: cf9a9631-56aa-4985-a565-1cacc297871d
ms.service: sql-database
ms.custom: migrate and move
ms.devlang: NA
ms.date: 02/07/2017
ms.author: carlrab
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA
translationtype: Human Translation
ms.sourcegitcommit: c1cd1450d5921cf51f720017b746ff9498e85537
ms.openlocfilehash: ca15248d7ac5232e6308d8188332975f1d21c09b
ms.lasthandoff: 03/14/2017


---
# <a name="import-a-bacpac-file-to-create-an-azure-sql-database-using-the-azure-portal"></a>Importer un fichier BACPAC pour créer une base de données SQL Azure à l’aide du portail Azure

Cet article fournit des instructions pour créer une base de données SQL Azure à partir d’un fichier BACPAC à l’aide du [portail Azure](https://portal.azure.com).

## <a name="prerequisites"></a>Composants requis

Pour importer une base de données SQL à partir d’un fichier BACPAC, vous avez besoin des éléments suivants :

* Un abonnement Azure. 
* Un serveur V12 de base de données SQL Azure. Si vous n’avez pas de serveur V12, créez-en un en suivant les étapes figurant dans cet article : [Créer votre première base de données SQL Azure](sql-database-get-started.md).
* Un fichier .bacpac de la base de données que vous souhaitez importer dans un conteneur d’objets blob de [compte Azure Storage (standard)](../storage/storage-create-storage-account.md) .

> [!IMPORTANT]
> Lorsque vous importez un fichier BACPAC à partir d’un stockage d’objets blob Azure, utilisez le stockage standard. L’importation d’un fichier BACPAC à partir de Premium Storage n’est pas prise en charge.
> 

## <a name="import-the-database"></a>Importer la base de données
Ouvrez le panneau SQL Server :

1. Accédez au [portail Azure](https://portal.azure.com).
2. Cliquez sur **Serveurs SQL**.
3. Cliquez sur le serveur dans lequel la base de données doit être restaurée.
4. Dans le panneau SQL Server, cliquez sur **Importer la base de données** pour ouvrir le panneau **Importer la base de données** :
   
   ![Importer la base de données][1]
5. Cliquez sur **Stockage** et sélectionnez votre compte de stockage, le conteneur blob et le fichier .bacpac, puis cliquez sur **OK**.
   
   ![configurer les options de stockage][2]
6. Sélectionnez le niveau de tarification de la nouvelle base de données et cliquez sur **Sélectionner**. L’importation d’une base de données directement dans un pool élastique n’est pas prise en charge ; vous pouvez cependant effectuer l’importation dans une base de données autonome, puis déplacer la base de données dans un pool.
   
   ![sélectionner un niveau de tarification][3]
7. Entrez un **nom de base de données** pour la base de données que vous créez à partir du fichier BACPAC.
8. Choisissez le type d’authentification, puis saisissez les informations d’authentification pour le serveur. 
9. Cliquez sur **Créer** pour créer la base de données à partir du fichier BACPAC.
   
   ![créer une base de données][4]

La commande **Créer** envoie une demande d’importation de la base de données au service. Selon la taille de votre base de données, l'opération d'importation peut prendre plus ou moins longtemps.

## <a name="monitor-the-progress-of-the-import-operation"></a>Surveillez la progression de l’opération d’importation
1. Cliquez sur **Serveurs SQL**.
2. Cliquez sur le serveur sur lequel vous effectuez la restauration.
3. Dans le panneau du serveur SQL, dans la zone Opérations, cliquez sur **Historique d’importation/exportation**:
   
   ![historique d’importation exportation][5]
   ![historique d’importation exportation][6]

4. Pour vérifier que la base de données est en ligne sur le serveur, cliquez sur **Bases de données SQL** et assurez-vous que la nouvelle base de données est définie sur **En ligne**.

## <a name="next-steps"></a>Étapes suivantes
* Pour vous connecter et interroger une base de données SQL importée, consultez [Se connecter à la base de données SQL avec SQL Server Management Studio et exécuter un exemple de requête T-SQL](sql-database-connect-query-ssms.md).
* Pour consulter le billet du blog SQL Server Customer Advisory Team sur la migration de SQL Server vers Azure SQL Database à l’aide de fichiers BACPAC (en anglais), rendez-vous [ici](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/).
* Pour une description du processus complet de migration d’une base de données SQL Server, y compris les recommandations relatives aux performances, consultez [Migration de base de données SQL Server vers SQL Database dans le cloud](sql-database-cloud-migrate.md).


<!--Image references-->
[1]: ./media/sql-database-import/import-database.png
[2]: ./media/sql-database-import/storage-options.png
[3]: ./media/sql-database-import/pricing-tier.png
[4]: ./media/sql-database-import/create.png
[5]: ./media/sql-database-import/import-history.png
[6]: ./media/sql-database-import/import-status.png

