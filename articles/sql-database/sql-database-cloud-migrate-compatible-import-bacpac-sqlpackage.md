---
title: "Importation vers Base de données SQL à partir d’un fichier BACPAC à l’aide de SqlPackage"
description: "Base de données SQL Microsoft Azure, migration de base de données, importer une base de données, importer un fichier BACPAC, sqlpackage"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 424afa27-5f13-4ec3-98f6-99a511a6a2df
ms.service: sql-database
ms.custom: migrate and move
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: sqldb-migrate
ms.date: 11/08/2016
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: e8bb9e5a02a7caf95dae0101c720abac1c2deff3
ms.openlocfilehash: d1f701bc5e4028db4d97ac2e7097afedf46b1f1f


---
# <a name="import-to-sql-database-from-a-bacpac-file-using-sqlpackage"></a>Importation vers Base de données SQL à partir d’un fichier BACPAC à l’aide de SqlPackage
> [!div class="op_single_selector"]
> * [SSMS](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
> * [SqlPackage](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md)
> * [Portail Azure](sql-database-import.md)
> * [PowerShell](sql-database-import-powershell.md)
> 
> 

Cet article explique comment effectuer une importation vers SQL Database à partir d’un fichier [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) à l’aide de l’utilitaire de ligne de commande [SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx). Cet utilitaire est fourni avec les dernières versions de [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) et de [SQL Server Data Tools pour Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx). Vous pouvez également télécharger la dernière version de [SqlPackage](https://www.microsoft.com/download/details.aspx?id=53876) directement à partir du Centre de téléchargement Microsoft.

> [!NOTE]
> Les étapes suivantes supposent que vous avez déjà configuré un serveur SQL Database, que vous disposez des informations de connexion et que vous avez vérifié la compatibilité de votre base de données source.
> 
> 

## <a name="import-from-a-bacpac-file-into-azure-sql-database-using-sqlpackage"></a>Importer un fichier BACPAC dans une base de données SQL Azure à l’aide de SqlPackage
Procédez comme suit pour importer une base de données SQL Server (ou Base de données SQL Azure) compatible à partir d’un fichier BACPAC à l’aide de l’utilitaire de ligne de commande [SqlPackage.exe](https://msdn.microsoft.com/library/hh550080.aspx) .

> [!NOTE]
> Les étapes suivantes supposent que vous avez déjà configuré un serveur de base de données SQL Azure et que vous disposez des informations de connexion.
> 
> 

1. Ouvrez une invite de commandes et modifiez un répertoire contenant l’utilitaire de ligne de commande sqlpackage.exe. Cet utilitaire est fourni avec Visual Studio et SQL Server.
2. Exécutez la commande sqlpackage.exe suivante avec les arguments ci-dessous pour votre environnement :
   
    `sqlpackage.exe /Action:Import /tsn:< server_name > /tdn:< database_name > /tu:< user_name > /tp:< password > /sf:< source_file >`
   
   | Argument | Description |
   | --- | --- |
   | < nom_serveur > |nom du serveur cible |
   | < nom_base_de_données > |nom de la base de données cible |
   | < nom_utilisateur > |nom d'utilisateur sur le serveur cible |
   | < mot_de_passe > |mot de passe de l'utilisateur |
   | < fichier_source > |nom de fichier et emplacement du fichier BACPAC en cours d'importation |
   
    ![Exporter une application de la couche Données à partir du menu Tâches](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSQLPackage01c.png)

## <a name="next-steps"></a>Étapes suivantes
* [Version la plus récente de SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
* [Version la plus récente de SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)

## <a name="additional-resources"></a>Ressources supplémentaires
* [Fonctionnalités SQL Database](sql-database-features.md)
* [Fonctions partiellement ou non prises en charge de Transact-SQL](sql-database-transact-sql-information.md)
* [Migration de bases de données non-SQL Server avec l’Assistant Migration SQL Server](http://blogs.msdn.com/b/ssma/)




<!--HONumber=Nov16_HO4-->


