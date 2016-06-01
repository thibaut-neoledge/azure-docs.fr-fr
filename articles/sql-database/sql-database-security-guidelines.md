<properties
   pageTitle="Instructions de sécurité et limitations d’Azure SQL Database | Microsoft Azure"
   description="Découvrez les instructions et limitations de Microsoft Azure SQL Database relatives à la sécurité."
   services="sql-database"
   documentationCenter=""
   authors="BYHAM"
   manager="jhubbard"
   editor=""
   tags=""/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="05/13/2016"
   ms.author="rickbyh"/>

# Instructions et limitations de sécurité d’Azure SQL Database

Cette rubrique décrit les instructions et limitations d’Azure SQL Database relatives à la sécurité. Considérez les points suivants lorsque vous gérez la sécurité de vos bases de données SQL Azure.

## Accès à la base de données master virtuelle

En règle générale, seuls les administrateurs ont besoin d’accéder à la base de données master. L’accès normal à chaque base de données utilisateur doit se faire par les utilisateurs non administrateurs de base de données à relation contenant-contenu créés dans chaque base de données. Lorsque vous utilisez des utilisateurs de base de données à relation contenant-contenu, vous n’avez pas besoin de créer des connexions à la base de données master. Pour plus d’informations, voir [Utilisateurs de base de données à relation contenant-contenu - Rendre votre base de données portable](https://msdn.microsoft.com/library/ff929188.aspx).


## Pare-feu

Le pare-feu SQL Server étendu pour l’ensemble du serveur SQL Azure est généralement configuré via le portail et doit accepter uniquement les adresses IP utilisées par les administrateurs. Connectez-vous à une base de données utilisateur, puis utilisez l’instruction Transact-SQL [sp\_set\_database\_firewall\_rule](https://msdn.microsoft.com/library/dn270010.aspx) pour créer une règle de pare-feu de base de données qui ouvrira la plage d’adresses IP nécessaire pour chaque base de données.

Le service Azure SQL Database Azure n’est disponible que via le port TCP 1433. Pour accéder à une base de données SQL depuis votre ordinateur, vérifiez que le pare-feu de votre ordinateur client autorise les communications TCP sortantes sur le port 1433. Si elles ne sont pas nécessaire pour les autres applications, bloquez les connexions entrantes sur le port TCP 1433.

Dans le cadre du processus de connexion, les connexions à partir des machines virtuelles Azure sont redirigées vers une autre adresse IP et un autre port, propres à chaque rôle de travail. Le numéro du port sera compris entre 11000 et 11999. Pour plus d’informations sur les ports TCP, consultez la page [Ports au-delà de 1433 pour ADO.NET 4.5 et SQL Database V12](sql-database-develop-direct-route-ports-adonet-v12.md).


## Authentification

Utilisez autant que possible l’authentification Active Directory (sécurité intégrée). Pour plus d’informations sur la configuration de l’authentification AD, voir [Connexion à la base de données SQL avec l’authentification Azure Active Directory](sql-database-aad-authentication.md) et [Choisir un mode d’authentification](https://msdn.microsoft.com/library/ms144284.aspx) dans la documentation en ligne de SQL Server.

Pour plus d’évolutivité, utilisez des utilisateurs de base de données à relation contenant-contenu. Pour plus d’informations, consultez [Utilisateurs de base de données contenu - rendre votre base de données portable](https://msdn.microsoft.com/library/ff929188.aspx), [CREATE USER (Transact-SQL)](https://technet.microsoft.com/library/ms173463.aspx), et [bases de données contenu](https://technet.microsoft.com/library/ff929071.aspx).

Le moteur de base de données ferme les connexions restées inactives pendant plus de 30 minutes. La connexion nécessite une nouvelle identification pour fonctionner à nouveau.

Les connexions perpétuellement actives à SQL Database requièrent une nouvelle autorisation (effectuée par le moteur de base de données) au moins toutes les 10 heures. Le moteur de base de données tente de renouveler l’autorisation à l’aide du mot de passe envoyé à l’origine. L’utilisateur n’a rien à saisir. Pour des raisons de performances, lorsqu’un mot de passe est réinitialisé dans SQL Database, la connexion n’est pas authentifiée à nouveau, même si elle est réinitialisée suite à un regroupement de connexions. Ce comportement est différent de SQL Server local. Si le mot de passe a été modifié depuis l’autorisation initiale de la connexion, celle-ci doit être interrompue et une nouvelle connexion établie à l’aide du nouveau mot de passe. Un utilisateur disposant de l’autorisation KILL DATABASE CONNECTION peut mettre explicitement fin à une connexion à Base de données SQL à l’aide de la commande [KILL](https://msdn.microsoft.com/library/ms173730.aspx).

## Connexions et utilisateurs

La gestion des connexions et des utilisateurs dans SQL Database est soumise à des restrictions.


- Vous devez être connecté à la base de données **master** lors de l’exécution des instructions ``CREATE/ALTER/DROP DATABASE``. - L’utilisateur de base de données dans la base de données master correspondant à la connexion principale au niveau du serveur ne peut pas être modifié ou supprimé. 
- L’anglais américain est la langue par défaut de la connexion principale au niveau du serveur.
- Pour accéder à la base de données **master**, chaque connexion doit être mappée à un compte d’utilisateur de cette base de données **master**. La base de données **MASTER** ne prend pas en charge les utilisateurs de base de données à relation contenant-contenu.
- Seule la connexion principale au niveau du serveur et les membres du rôle de base de données **dbmanager** dans la base de données **MASTER** sont autorisés à exécuter les instructions ``CREATE DATABASE`` et ``DROP DATABASE``.
- Vous devez être connecté à la base de données master lors de l’exécution des instructions ``CREATE/ALTER/DROP LOGIN``. L’utilisation de connexions est toutefois déconseillée. Préférez l’emploi d’utilisateurs de base de données à relation contenant-contenu.
- Pour vous connecter à une base de données utilisateur, vous devez renseigner le nom de la base de données dans la chaîne de connexion.
- Seule la connexion principale au niveau du serveur et les membres du rôle de base de données **loginmanager** dans la base de données **MASTER** sont autorisés à exécuter les instructions ``CREATE LOGIN``, ``ALTER LOGIN`` et ``DROP LOGIN``.
- Lors de l’exécution des instructions ``CREATE/ALTER/DROP LOGIN`` et ``CREATE/ALTER/DROP DATABASE`` dans une application ADO.NET, l’utilisation de commandes paramétrées est interdite. Pour plus d’informations, voir [Commandes et paramètres](https://msdn.microsoft.com/library/ms254953.aspx).
- Lors de l’exécution des instructions ``CREATE/ALTER/DROP DATABASE`` et ``CREATE/ALTER/DROP LOGIN``, chacune d’entre elles doit être la seule instruction dans un batch Transact-SQL. Sinon, une erreur se produit. Par exemple, l’instruction Transact-SQL suivant vérifie l’existence de la base de données. Si elle existe, une instruction ``DROP DATABASE`` est appelée pour supprimer la base de données. Comme l’instruction ``DROP DATABASE`` n’est pas la seule instruction du batch, l’exécution de l’instruction Transact-SQL suivante génère une erreur.

```
IF EXISTS (SELECT [name]
           FROM   [sys].[databases]
           WHERE  [name] = N'database_name')
     DROP DATABASE [database_name];
GO
```

- Lors de l’exécution de l’instruction ``CREATE USER`` avec l’option ``FOR/FROM LOGIN``, elle doit être la seule instruction du batch Transact-SQL.
- Lors de l’exécution de l’instruction ``ALTER USER`` avec l’option ``WITH LOGIN``, elle doit être la seule instruction du batch Transact-SQL.
- Pour exécuter les instructions ``CREATE/ALTER/DROP``, un utilisateur a besoin de l’autorisation ``ALTER ANY USER`` sur la base de données.
- Lorsque le propriétaire d’un rôle de base de données tente d’ajouter un autre utilisateur de base de données dans ce rôle de base de données (ou de le supprimer de ce dernier), l’erreur suivante peut se produire : **L’utilisateur ou le rôle « Nom » n’existe pas dans cette base de données**. Cette erreur survient, car l’utilisateur n’est pas visible par le propriétaire. Pour résoudre ce problème, accordez au propriétaire du rôle l’autorisation ``VIEW DEFINITION`` sur l’utilisateur. 

Pour plus d’informations sur les connexions et les utilisateurs, voir [Gestion des bases de données et des connexions dans Base de données SQL Azure](sql-database-manage-logins.md).


## Voir aussi

[Pare-feu Azure SQL Database](sql-database-firewall-configure.md)

[Configuration des paramètres du pare-feu (Base de données SQL Azure)](sql-database-configure-firewall-settings.md)

[Gestion des bases de données et des connexions dans Azure SQL Database](sql-database-manage-logins.md)

[Centre de sécurité pour le moteur de base de données SQL Server et la base de données SQL Azure](https://msdn.microsoft.com/library/bb510589)

<!---HONumber=AcomDC_0518_2016-->