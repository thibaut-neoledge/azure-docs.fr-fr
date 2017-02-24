---
title: 'SqlPackage : importer un fichier BACPAC dans Azure SQL Database | Microsoft Docs'
description: "Cet article explique comment effectuer une importation dans SQL Database à partir d’un fichier BACPAC à l’aide de l’utilitaire de ligne de commande SqlPackage."
keywords: "Base de données SQL Microsoft Azure, migration de base de données, importer une base de données, importer un fichier BACPAC, sqlpackage"
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
ms.date: 02/08/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: e6f0d661465c813ec310b8c69ab1ee06e4f95401
ms.openlocfilehash: 23c5da7b9e171312274aec02262b139ee701deb1


---
# <a name="import-a-database-from-a-bacpac-file-to-azure-sql-database-using-sqlpackage"></a>Importer une base de données à partir d’un fichier BACPAC dans Azure SQL Database à l’aide de SqlPackage

Cet article explique comment effectuer une importation vers SQL Database à partir d’un fichier [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) à l’aide de l’utilitaire de ligne de commande [SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx). Cet utilitaire est fourni avec les dernières versions de [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) et de [SQL Server Data Tools pour Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx). Vous pouvez également télécharger la dernière version de [SqlPackage](https://www.microsoft.com/download/details.aspx?id=53876) directement à partir du Centre de téléchargement Microsoft.

> [!NOTE]
> Les étapes suivantes supposent que vous avez déjà configuré un serveur SQL Database, que vous disposez des informations de connexion et que vous avez vérifié la compatibilité de votre base de données source.
> 
> 

## <a name="import-the-database"></a>Importer la base de données
Utilisez l’utilitaire de ligne de commande [SqlPackage.exe](https://msdn.microsoft.com/library/hh550080.aspx) pour importer une base de données SQL Server (ou une base de données SQL Azure) compatible à partir d’un fichier BACPAC.

> [!NOTE]
> Les étapes suivantes supposent que vous avez déjà configuré un serveur de base de données SQL Azure et que vous disposez des informations de connexion.
>  

À une invite de commandes dans le répertoire contenant la version la plus récente de l’utilitaire de ligne de commande sqlpackage.exe, exécutez une commande semblable à l’exemple de commande suivant qui importe un fichier BACPAC dans Azure SQL Database en tant que Premium P11.

```
SqlPackage.exe /a:import /tcs:"Data Source=SERVER;Initial Catalog=DBNAME;User Id=USER;Password=PASSWORD" /sf:C:\db.bacpac /p:DatabaseEdition=Premium /p:DatabaseServiceObjective=P11
```   

## <a name="next-steps"></a>Étapes suivantes

* Pour une description du processus complet de migration d’une base de données SQL Server, y compris les recommandations relatives aux performances, consultez [Migration de base de données SQL Server vers SQL Database dans le cloud](sql-database-cloud-migrate.md).
* Pour le contenu de référence sur SQLPackage, consultez [SqlPackage.exe](https://msdn.microsoft.com/library/hh550080.aspx).
* Pour télécharger la dernière version de SQLPackage, consultez [SqlPackage](https://www.microsoft.com/download/details.aspx?id=53876).
* Pour consulter le billet du blog SQL Server Customer Advisory Team sur la migration de SQL Server vers Azure SQL Database à l’aide de fichiers BACPAC (en anglais), rendez-vous [ici](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/).




<!--HONumber=Feb17_HO2-->


