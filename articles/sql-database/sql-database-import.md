---
title: Importer un fichier BACPAC pour créer une base de données SQL Azure | Microsoft Docs
description: Créer une base de données SQL Azure en important un fichier BACPAC existant.
services: sql-database
documentationcenter: ''
author: stevestein
manager: jhubbard
editor: ''

ms.service: sql-database
ms.devlang: NA
ms.date: 08/31/2016
ms.author: sstein
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA

---
# Importer un fichier BACPAC pour créer une base de données SQL Azure
**Base de données unique**

> [!div class="op_single_selector"]
> * [Portail Azure](sql-database-import.md)
> * [PowerShell](sql-database-import-powershell.md)
> * [SSMS](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
> * [SqlPackage](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md)
> 
> 

Cet article fournit des instructions pour créer une base de données SQL Azure à partir d’un fichier BACPAC à l’aide du [portail Azure](https://portal.azure.com).

Un BACPAC est un fichier .bacpac qui contient un schéma de base de données et des données. La base de données est créée à partir d'un fichier BACPAC importé depuis un conteneur d'objets blob de stockage Azure. Si vous n’avez pas de fichier .bacpac dans le stockage Azure, vous pouvez en créer un en suivant la procédure décrite dans [Créer et exporter un fichier BACPAC à partir d’une base de données SQL Azure](sql-database-export.md).

> [!NOTE]
> La base de données SQL Azure crée automatiquement des sauvegardes pour chaque base de données utilisateur que vous pouvez restaurer, et en assure la maintenance. Pour plus d’informations, consultez [Vue d'ensemble de la continuité des activités](sql-database-business-continuity.md).
> 
> 

Pour importer une base de données SQL à partir d'un fichier .bacpac, vous avez besoin des éléments suivants :

* Un abonnement Azure.
* Un serveur V12 de base de données SQL Azure. Si vous n’avez pas de serveur V12, créez-en un en suivant les étapes figurant dans cet article : [Créer votre première base de données SQL Azure](sql-database-get-started.md).
* Un fichier .bacpac de la base de données que vous souhaitez importer dans un conteneur d’objets blob de [compte Azure Storage (standard)](../storage/storage-create-storage-account.md).

> [!IMPORTANT]
> Lorsque vous importez un fichier BACPAC à partir d’un stockage d’objets blob Azure, utilisez le stockage standard. L’importation d’un fichier BACPAC à partir de Premium Storage n’est pas prise en charge.
> 
> 

## Sélectionner le serveur pour héberger la base de données
Ouvrez le panneau SQL Server :

1. Accédez au [portail Azure](https://portal.azure.com).
2. Cliquez sur **Serveurs SQL**.
3. Cliquez sur le serveur dans lequel la base de données doit être restaurée.
4. Dans le panneau SQL Server, cliquez sur **Importer la base de données** pour ouvrir le panneau **Importer la base de données** :
   
   ![importer une base de données][1]
5. Cliquez sur **Stockage** et sélectionnez votre compte de stockage, le conteneur blob et le fichier .bacpac, puis cliquez sur **OK**.
   
   ![configurer les options de stockage][2]
6. Sélectionnez le niveau de tarification de la nouvelle base de données et cliquez sur **Sélectionner**. L’importation d’une base de données directement dans un pool n’est pas prise en charge ; vous pouvez cependant effectuer l’importation dans une base de données unique, puis déplacer la base de données dans un pool.
   
   ![sélectionner un niveau de tarification][3]
7. Entrez un **nom de base de données** pour la base de données que vous créez à partir du fichier BACPAC.
8. Choisissez le type d’authentification, puis saisissez les informations d’authentification pour le serveur.
9. Cliquez sur **Créer** pour créer la base de données à partir du fichier BACPAC.
   
   ![créer une base de données][4]

La commande **Créer** envoie une demande d’importation de la base de données au service. Selon la taille de votre base de données, l'opération d'importation peut prendre plus ou moins longtemps.

## Surveillez la progression de l’opération d’importation
1. Cliquez sur **Serveurs SQL**.
2. Cliquez sur le serveur sur lequel vous effectuez la restauration.
3. Dans le panneau du serveur SQL, dans la zone Opérations, cliquez sur **Historique d’importation/exportation** :
   
   ![historique d’import export][5] ![historique d’import export][6]

## Vérifiez que la base de données est en ligne sur le serveur
1. Cliquez sur **Bases de données SQL** et vérifiez que la nouvelle base de données est **En ligne**.

## Étapes suivantes
* Pour vous connecter et interroger une base de données SQL importée, consultez [Se connecter à la base de données SQL avec SQL Server Management Studio et exécuter un exemple de requête T-SQL](sql-database-connect-query-ssms.md)

<!--Image references-->
[1]: ./media/sql-database-import/import-database.png
[2]: ./media/sql-database-import/storage-options.png
[3]: ./media/sql-database-import/pricing-tier.png
[4]: ./media/sql-database-import/create.png
[5]: ./media/sql-database-import/import-history.png
[6]: ./media/sql-database-import/import-status.png

<!---HONumber=AcomDC_0907_2016-->