---
title: 'Didacticiel sur la base de données SQL : prise en main de la sécurité'
description: Apprenez à créer des comptes d'utilisateurs pour accéder et gérer une base de données.
keywords: ''
services: sql-database
documentationcenter: ''
author: CarlRabeler
manager: jhubbard
editor: ''

ms.service: sql-database
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 08/17/2016
ms.author: carlrab

---
# Didacticiel sur la base de données SQL : créer des comptes d’utilisateurs de base de données SQL pour accéder et gérer une base de données
> [!div class="op_single_selector"]
> * [Didacticiel de prise en main](sql-database-get-started-security.md)
> * [Accorder l’accès](sql-database-manage-logins.md)
> 
> 

Dans ce didacticiel, vous allez apprendre à utiliser SQL Server Management Studio (SSMS) pour :

* Vous connecter à la base de données SQL à l’aide d’une connexion du principal au niveau du serveur.
* Créer un compte d’utilisateur de base de données SQL.
* Accorder une [autorisation db\_owner](https://msdn.microsoft.com/library/ms189121.aspx#Anchor_0) à un utilisateur de bases de données SQL.
* Vous connecter à une base de données SQL avec un compte d’utilisateur qui n’est pas un principal au niveau du serveur.

[!INCLUDE [Connexion](../../includes/azure-getting-started-portal-login.md)]

[!INCLUDE [Création d’un serveur logique de base de données SQL](../../includes/sql-database-sql-server-management-studio-connect-server-principal.md)]

[!INCLUDE [Création d’une base de données SQL Database](../../includes/sql-database-create-new-database-user.md)]

[!INCLUDE [Création d’une base de données SQL Database](../../includes/sql-database-grant-database-user-dbo-permissions.md)]

[!INCLUDE [Création d’une base de données SQL Database](../../includes/sql-database-sql-server-management-studio-connect-user.md)]

## Étapes suivantes
Maintenant que vous avez terminé ce didacticiel concernant le service Base de données SQL, créé un compte d’utilisateur et accordé des autorisations de propriétaire de base de données (dbo) au compte d’utilisateur, vous voici prêt à découvrir la [sécurité de Base de données SQL](sql-database-manage-logins.md).

<!---HONumber=AcomDC_0824_2016-->