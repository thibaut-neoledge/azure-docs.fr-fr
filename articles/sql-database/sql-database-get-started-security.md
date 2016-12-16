---
title: "Didacticiel sur la base de données SQL : prise en main de la sécurité"
description: "Apprenez à créer des comptes d&quot;utilisateurs pour accéder et gérer une base de données."
keywords: 
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 67797b09-f5c3-4ec2-8494-fe18883edf7f
ms.service: sql-database
ms.custom: authentication and authorization
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 08/17/2016
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 09c2332589b1170b411c6f45f4109fb8048887e2
ms.openlocfilehash: e846654b659c1240451e3ac26973fbfce206936d


---
# <a name="sql-database-tutorial-create-sql-database-user-accounts-to-access-and-manage-a-database"></a>Didacticiel sur la base de données SQL : créer des comptes d’utilisateurs de base de données SQL pour accéder et gérer une base de données
> [!div class="op_single_selector"]
> * [Didacticiel de prise en main](sql-database-get-started-security.md)
> * [Accorder l’accès](sql-database-manage-logins.md)
> 
> 

Dans ce didacticiel, vous allez apprendre à utiliser SQL Server Management Studio (SSMS) pour :

* Vous connecter à la base de données SQL à l’aide d’une connexion du principal au niveau du serveur.
* Créer un compte d’utilisateur de base de données SQL.
* Accorder des [autorisations db_owner](https://msdn.microsoft.com/library/ms189121.aspx#Anchor_0) à un utilisateur de bases de données SQL.
* Vous connecter à une base de données SQL avec un compte d’utilisateur qui n’est pas un principal au niveau du serveur.

[!INCLUDE [Login](../../includes/azure-getting-started-portal-login.md)]

[!INCLUDE [Create SQL Database logical server](../../includes/sql-database-sql-server-management-studio-connect-server-principal.md)]

[!INCLUDE [Create SQL Database database](../../includes/sql-database-create-new-database-user.md)]

[!INCLUDE [Create SQL Database database](../../includes/sql-database-grant-database-user-dbo-permissions.md)]

[!INCLUDE [Create SQL Database database](../../includes/sql-database-sql-server-management-studio-connect-user.md)]

## <a name="next-steps"></a>Étapes suivantes
Maintenant que vous avez terminé ce didacticiel concernant le service Base de données SQL, créé un compte d’utilisateur et accordé des autorisations de propriétaire de base de données (dbo) au compte d’utilisateur, vous voici prêt à découvrir la [sécurité de Base de données SQL](sql-database-manage-logins.md).




<!--HONumber=Dec16_HO1-->


