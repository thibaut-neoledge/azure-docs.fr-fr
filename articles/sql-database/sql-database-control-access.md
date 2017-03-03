---
title: "Accorder l’accès à Azure SQL Database | Microsoft Docs"
description: "Découvrez comment accorder l’accès à Microsoft Azure SQL Database."
services: sql-database
documentationcenter: 
author: BYHAM
manager: jhubbard
editor: 
tags: 
ms.assetid: 8e71b04c-bc38-4153-8f83-f2b14faa31d9
ms.service: sql-database
ms.custom: overview
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 02/06/2017
ms.author: rickbyh
translationtype: Human Translation
ms.sourcegitcommit: ae230c012a17eb73c8993a32197c844c6abaa2a4
ms.openlocfilehash: 8b9b9b70cc7b6a9b89a52523f713b43b74713042
ms.lasthandoff: 02/17/2017

---
# <a name="azure-sql-database-access-control"></a>Contrôle d’accès à Azure SQL Database
Pour assurer la sécurité, SQL Database contrôle l’accès avec des règles de pare-feu qui limitent la connectivité par adresse IP, des mécanismes d’authentification qui obligent les utilisateurs à prouver leur identité, et des mécanismes d’autorisation qui les restreignent à certaines actions et données. 

> [!IMPORTANT]
> Pour une vue d’ensemble des fonctionnalités de sécurité de SQL Database, consultez [Securing your SQL Database (Sécurisation de SQL Database)](sql-database-security-overview.md). Pour obtenir un didacticiel sur l’utilisation de l’authentification SQL Server, consultez [Authentification et autorisation SQL](sql-database-control-access-sql-authentication-get-started.md). Pour un didacticiel sur l’authentification Azure Active Directory, consultez [Authentification et autorisation Azure AD](sql-database-control-access-aad-authentication-get-started.md).

## <a name="firewall-and-firewall-rules"></a>Pare-feu et règles de pare-feu
Microsoft Azure SQL Database fournit un service de base de données relationnelle pour Azure et d’autres applications basées sur Internet. Pour aider à protéger vos données, le pare-feu empêche tout accès à votre serveur de base de données jusqu’à ce que vous spécifiiez les ordinateurs qui disposent d’autorisations. Le pare-feu octroie l’accès à la base de données en fonction de l’adresse IP d’origine de chaque demande. Pour en savoir plus, consultez [Vue d’ensemble des règles de pare-feu d’Azure SQL Database](sql-database-firewall-configure.md).

Le service Azure SQL Database Azure n’est disponible que via le port TCP 1433. Pour accéder à une base de données SQL depuis votre ordinateur, vérifiez que le pare-feu de votre ordinateur client autorise les communications TCP sortantes sur le port 1433. Si elles ne sont pas nécessaire pour les autres applications, bloquez les connexions entrantes sur le port TCP 1433. 

Dans le cadre du processus de connexion, les connexions à partir des machines virtuelles Azure sont redirigées vers une autre adresse IP et un autre port, propres à chaque rôle de travail. Le numéro du port est compris entre 11000 et 11999. Pour plus d’informations sur les ports TCP, consultez la page [Ports au-delà de 1433 pour ADO.NET 4.5 et SQL Database V12](sql-database-develop-direct-route-ports-adonet-v12.md).

## <a name="authentication"></a>Authentification

Une base de données SQL prend en charge deux types d’authentification :

* **L’authentification SQL**, qui utilise un nom d’utilisateur et un mot de passe. Lorsque vous avez créé un serveur logique pour votre base de données, vous avez spécifié un compte de connexion « Admin serveur », associé à un nom d’utilisateur et à un mot de passe. À l’aide de ces informations d’identification, vous pouvez vous authentifier auprès de n’importe quelle base de données sur ce serveur, en tant que propriétaire de la base de données, ou « dbo ». 
* **L’authentification Azure Active Directory**, qui utilise des identités gérées par Azure Active Directory et qui est prise en charge pour les domaines gérés et intégrés. Utilisez l’authentification Active Directory (sécurité intégrée) [dans la mesure du possible](https://msdn.microsoft.com/library/ms144284.aspx). Si vous souhaitez utiliser l’authentification Azure Active Directory, vous devez créer un autre administrateur de serveur appelé « administrateur Azure AD », autorisé à gérer les groupes et utilisateurs Active Directory Azure. Cet administrateur peut également effectuer toutes les opérations d’un administrateur de serveur ordinaire. Pour une procédure pas à pas relative à la création d’un administrateur Azure AD pour activer l’authentification Azure Active Directory, consultez [Connexion à la base de données SQL avec l’authentification Azure Active Directory](sql-database-aad-authentication.md) .

Le moteur de base de données ferme les connexions restées inactives pendant plus de 30 minutes. La connexion nécessite une nouvelle identification pour fonctionner à nouveau. Les connexions perpétuelles à SQL Database requièrent une nouvelle autorisation (effectuée par le moteur de base de données) au moins toutes les 10 heures. Le moteur de base de données tente de renouveler l’autorisation à l’aide du mot de passe envoyé à l’origine. L’utilisateur n’a rien à saisir. Pour des raisons de performances, lorsqu’un mot de passe est réinitialisé dans SQL Database, la connexion n’est pas authentifiée à nouveau, même si elle est réinitialisée suite à un regroupement de connexions. Ce comportement est différent de SQL Server local. Si le mot de passe a été modifié depuis l’autorisation initiale de la connexion, celle-ci doit être interrompue et une nouvelle connexion établie à l’aide du nouveau mot de passe. Un utilisateur disposant de l’autorisation `KILL DATABASE CONNECTION` peut mettre explicitement fin à une connexion à SQL Database à l’aide de la commande [KILL](https://msdn.microsoft.com/library/ms173730.aspx).

Les comptes d’utilisateur peuvent être soit créés dans la base de données principale et autorisés à accéder à toutes les bases de données sur le serveur, soit créés dans la base de données elle-même (appelés utilisateurs à relation contenant-contenu). Pour plus d’informations sur la création et la gestion des connexions, consultez [Gérer les connexions](sql-database-manage-logins.md). Pour plus de portabilité et d’évolutivité, utilisez des utilisateurs de base de données à relation contenant-contenu. Pour plus d’informations sur les utilisateurs à relation contenant-contenu, consultez [Utilisateurs de base de données à relation contenant-contenu - Rendre votre base de données portable](https://msdn.microsoft.com/library/ff929188.aspx), [CREATE USER (Transact-SQL)](https://technet.microsoft.com/library/ms173463.aspx) et [Bases de données à relation contenant-contenu](https://technet.microsoft.com/library/ff929071.aspx).

Nous vous recommandons, au titre de meilleure pratique, d’utiliser un compte dédié pour l’authentification de votre application. Cela vous permet de limiter les autorisations accordées à cette application et de réduire les risques d’activité malveillante, au cas où le code de votre application serait vulnérable à une attaque par injection de code SQL. La méthode recommandée consiste à créer un [utilisateur de base de données à relation contenant-contenu](https://msdn.microsoft.com/library/ff929188), qui permet à votre application de s’authentifier directement auprès de la base de données. 

## <a name="authorization"></a>Autorisation

Le terme « autorisation » fait référence aux actions qu’un utilisateur peut exécuter dans une base de données Azure SQL Database et qui sont contrôlées par les [rôles détenus](https://msdn.microsoft.com/library/ms189121) et les [autorisations sur les objets](https://msdn.microsoft.com/library/ms191291.aspx). Nous vous recommandons, à titre de meilleure pratique, d’accorder aux utilisateurs des privilèges aussi réduits que possible. Le compte d’administrateur du serveur avec lequel vous vous connectez appartient au groupe « db_owner », qui est autorisé à effectuer tout type d’opérations dans la base de données. Enregistrez ce compte pour l’utiliser lors du déploiement des mises à niveau de schéma et d’autres opérations de gestion. Utilisez le compte « ApplicationUser », doté d’autorisations plus limitées, pour vous connecter à la base de données à partir de votre application, en bénéficiant du niveau de privilèges le moins élevé requis par cette dernière. Pour plus d’informations, consultez [Gérer les connexions](sql-database-manage-logins.md).

En règle générale, seuls les administrateurs ont besoin d’accéder à la base de données `master`. L’accès normal à chaque base de données utilisateur doit se faire par les utilisateurs non administrateurs de base de données à relation contenant-contenu créés dans chaque base de données. Lorsque vous utilisez des utilisateurs de base de données à relation contenant-contenu, vous n’avez pas besoin de créer des connexions à la base de données `master`. Pour plus d’informations, voir [Utilisateurs de base de données à relation contenant-contenu - Rendre votre base de données portable](https://msdn.microsoft.com/library/ff929188.aspx).

Vous devez vous familiariser avec les fonctionnalités suivantes qui peuvent être utilisées pour limiter ou élever les autorisations :   
* Vous pouvez recourir à l’[emprunt d’identité](https://msdn.microsoft.com/library/vstudio/bb669087) et à la [signature de module](https://msdn.microsoft.com/library/bb669102) pour élever le niveau des autorisations temporairement, en toute sécurité.
* [sécurité au niveau des lignes](https://msdn.microsoft.com/library/dn765131) peut être utilisée pour limiter le nombres de lignes accessibles par l’utilisateur.
* [masquage des données](sql-database-dynamic-data-masking-get-started.md) permet de restreindre l’exposition des données sensibles.
* [procédures stockées](https://msdn.microsoft.com/library/ms190782) , vous pouvez limiter le nombre d’actions susceptibles d’être exécutées sur la base de données.

## <a name="next-steps"></a>Étapes suivantes

- Pour une vue d’ensemble des fonctionnalités de sécurité de SQL Database, consultez [Securing your SQL Database (Sécurisation de SQL Database)](sql-database-security-overview.md).
- Pour en savoir plus sur les règles de pare-feu, consultez [Règles de pare-feu](sql-database-firewall-configure.md).
- Pour en savoir plus sur les utilisateurs et les connexions, consultez [Gérer les connexions](sql-database-manage-logins.md). 
- Pour une discussion sur la surveillance proactive, consultez [Audit de base de données](sql-database-auditing.md) et [Détection des menaces pour SQL Database](sql-database-threat-detection.md).
- Pour obtenir un didacticiel sur l’utilisation de l’authentification SQL Server, consultez [Authentification et autorisation SQL](sql-database-control-access-sql-authentication-get-started.md).
- Pour un didacticiel sur l’authentification Azure Active Directory, consultez [Authentification et autorisation Azure AD](sql-database-control-access-aad-authentication-get-started.md).

