<properties
   pageTitle="Gestion de la sécurité SQL Database - sécurité de connexion | Microsoft Azure"
   description="Découvrez la gestion de la sécurité SQL Database, en particulier la façon de gérer la sécurité d’accès et de connexion aux bases de données par le biais du compte du principal au niveau du serveur."
   keywords="sécurité sql database, gestion de la sécurité de base de données, sécurité de connexion,sécurité de base de données, accès aux bases de données"
   services="sql-database"
   documentationCenter=""
   authors="BYHAM"
   manager="jeffreyg"
   editor=""
   tags=""/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="09/25/2015"
   ms.author="rickbyh"/>

# Sécurité SQL Database : gérer la sécurité d’accès et de connexion aux bases de données  

Découvrez la gestion de la sécurité SQL Database, en particulier la façon de gérer la sécurité d’accès et de connexion aux bases de données par le biais du compte du principal au niveau du serveur. Comprenez certaines différences et similitudes dans les options de sécurité de connexion entre SQL Database et un serveur SQL Server local.

## Approvisionnement des bases de données et connexion du principal au niveau du serveur

Dans la base de données SQL Azure de Microsoft, lorsque vous vous inscrivez pour bénéficier du service, le processus d’approvisionnement crée un serveur de base de données SQL Azure, une base de données nommée **master**, et une connexion, qui est le principal au niveau du serveur de votre serveur de base de données SQL Azure. Cette connexion est semblable au principal au niveau du serveur (**sa**), d’une instance locale de SQL Server.

Le compte principal au niveau du serveur de base de données SQL Azure est autorisé à gérer l’ensemble de la sécurité au niveau du serveur et au niveau de la base de données. Cette rubrique explique comment utiliser le principal au niveau du serveur et d’autres comptes pour gérer les connexions et bases de données dans la base de données SQL

> [AZURE.IMPORTANT] La base de données SQL V12 permet aux utilisateurs de s’authentifier pour la base de données à l’aide des utilisateurs de base de données contenu. Les utilisateurs de base de données contenu n’exige pas de connexion. Cela rend les bases de données plus portables, mais réduit la capacité du principal de niveau serveur à contrôler l’accès à la base de données. L’activation des utilisateurs contenus dans la base de données à des impacts de sécurité. Pour plus d’informations, voir [Utilisateurs de base de données à relation contenant-contenu - Rendre votre base de données portable](https://msdn.microsoft.com/library/ff929188.aspx), [Bases de données à relation contenant-contenu](https://technet.microsoft.com/library/ff929071.aspx), [CRÉER UN UTILISATEUR (Transact-SQL)](https://technet.microsoft.com/library/ms173463.aspx), [Connexion à la base de données SQL avec l’authentification Azure Active Directory](sql-database-aad-authentication.md).

## Vue d’ensemble de la gestion de la sécurité SQL Database

La gestion de la sécurité SQL Database est similaire à celle d’une instance locale de SQL Server. La gestion de la sécurité au niveau de la base de données est presque identique, les seules différences concernant les paramètres disponibles. Les bases de données SQL pouvant être adaptées à un ou plusieurs ordinateurs physiques, la base de données SQL Azure utilise une stratégie d’administration de niveau du serveur différente. Le tableau résume les différences entre la gestion de la sécurité de base de données pour un serveur SQL Server local et dans Azure SQL Database.

| Différences | SQL Server local | Base de données SQL Azure |
|------------------------------------------------|-----------------------------------------------------------------------------|--------------------------------------------------|
| Emplacement où se gère la sécurité de niveau serveur | Dossier **Sécurité** dans l’Explorateur d’objets de SQL Server Management Studio | Base de données **MASTER** et via le portail Azure |
| Authentification Windows | Identités Active Directory | Identités Azure Active Directory |
| Rôle de sécurité au niveau du serveur pour la création de connexions | Rôle serveur fixe **securityadmin** | Rôle de base de données **loginmanager** dans la base de données **MASTER** |
| Commandes de gestion des connexions | CRÉER UNE CONNEXION, MODIFIER UNE CONNEXION, ABANDONNER UNE CONNEXION | CRÉER UNE CONNEXION, MODIFIER UNE CONNEXION, ABANDONNER UNE CONNEXION (il existe certaines limitations de paramètres et vous devez être connecté à la base de données **MASTER**.) |
| Vue affichant toutes les connexions | sys.server\_principals | Sys.sql\_logins (vous devez être connecté à la base de données **MASTER**.)|
| Rôle au niveau du serveur pour la création de bases de données | Rôle de base de données fixe **dbcreator** | Rôle de base de données **dbmanager** dans la base de données **MASTER** |
| Aucune de création de base de données | CRÉER UNE BASE DE DONNÉES | CRÉER UNE BASE DE DONNÉES (il existe certaines limitations de paramètres et vous devez être connecté à la base de données **MASTER**.) |
| Vue qui répertorie toutes les bases de données | sys.databases | sys.databases (vous devez être connecté à la base de données **master**.) |

## Administration au niveau du serveur et de la base de données master

Votre serveur de base de données SQL Azure est une abstraction qui définit un groupement de bases de données. Les bases de données associées à votre serveur de base de données SQL Azure peuvent résider sur des ordinateurs physiques distincts dans le datacenter Microsoft. Effectuez l’administration de l’ensemble des bases de données au niveau du serveur en utilisant une base de données unique nommée **MASTER**.

La base de données **MASTER** effectue le suivi des connexions et indique celles qui sont autorisées à créer des bases de données ou d’autres connexions. Vous devez être connecté à la base de données **MASTER** chaque fois que vous créez, modifiez ou annulez des connexions ou des bases de données. La base de données **MASTER** comporte également les vues ``sys.sql_logins`` et ``sys.databases``, que vous pouvez utiliser pour afficher les connexions et les bases de données.

> [AZURE.NOTE] La commande ``USE`` n’est pas prise en charge pour le basculement entre bases de données. Établissement d’une connexion directe à la base de données cible.

Il est possible de gérer la sécurité au niveau de la base de données pour les utilisateurs et les objets de base de données SQL Azure de la même façon que celle d’une instance locale de SQL Server. Il existe uniquement des différences de paramètres disponibles pour les commandes correspondantes. Pour plus d’informations, voir [Instructions et limitations de sécurité dans la base de données SQL Azure](sql-database-security-guidelines.md).

## Gestion des utilisateurs de base de données à relation contenant-contenu

Créez le premier utilisateur de base de données à relation contenant-contenu dans une base de données en vous connectant à la base de données avec le principal au niveau du serveur. Utilisez les instructions ``CREATE USER``, ``ALTER USER`` ou ``DROP USER``. L’exemple qui suit permet de créer un utilisateur nommé user1.

```
CREATE USER user1 WITH password='<Strong_Password>';
```

> [AZURE.NOTE] Vous devez utiliser un mot de passe fort au moment de la création d’un utilisateur de base de données à relation contenant-contenu. Pour plus d'informations, consultez la page [Mots de passe forts](https://msdn.microsoft.com/library/ms161962.aspx).

Des utilisateurs de base de données à relation contenant-contenu supplémentaires peuvent être créés par tout utilisateur disposant de l’autorisation **MODIFIER UN UTILISATEUR**.

La base de données SQL V12 prend en charge les identités Azure Active Directory en tant qu’utilisateurs de base de données à relation contenant-contenu, comme fonctionnalité en version préliminaire. Pour plus d’informations, voir [Connexion à la base de données SQL avec l’authentification Azure Active Directory](sql-database-aad-authentication.md).

Microsoft recommande l’emploi d’utilisateurs de base de données à relation contenant-contenu avec la base de données SQL. Pour plus d’informations, voir [Utilisateurs de base de données à relation contenant-contenu - Rendre votre base de données portable](https://msdn.microsoft.com/library/ff929188.aspx).

## Gestion des connexions

Gérez les connexions avec la connexion au principal au niveau du serveur en se connectant à la base de données master. Vous pouvez utiliser les instructions ``CREATE LOGIN``, ``ALTER LOGIN`` ou ``DROP LOGIN``. L’exemple qui suit crée une connexion nommée **login1** :

```
-- first, connect to the master database
CREATE LOGIN login1 WITH password='<ProvidePassword>';
```

> [AZURE.NOTE] Vous devez utiliser un mot de passe fort au moment de la création d’une connexion. Pour plus d'informations, consultez la page [Mots de passe forts](https://msdn.microsoft.com/library/ms161962.aspx).

#### Utilisation de nouvelles connexions

Pour vous connecter à la base de données SQL Microsoft Azure à l’aide de connexions que vous avez créées, vous devez d’abord accorder à chaque connexion les autorisations de niveau de base de données à l’aide de la commande ``CREATE USER``. Pour plus d'informations, consultez la section **Octroi d’un accès de base de données à une connexion** ci-dessous.

Étant donné que certains outils implémentent les flux de données TDS (Tabular Data Stream) différemment, il se peut que vous deviez ajouter le nom de serveur de base de données SQL Azure à la connexion dans la chaîne de connexion en utilisant la notation ``<login>@<server>``. Dans ces cas-là, insérez le symbole ``@`` entre la connexion et le nom de serveur de base de données SQL Azure. Par exemple, si votre connexion a été nommée **login1** et que le nom complet de votre serveur de base de données SQL Azure est **servername.database.windows.net**, le paramètre de nom d’utilisateur de votre chaîne de connexion doit être : ****login1@servername**. Cette restriction impose des limitations sur le texte que vous pouvez choisir pour le nom de connexion. Pour plus d’informations, consultez [CRÉER UNE CONNEXION (Transact-SQL)](https://msdn.microsoft.com/library/ms189751.aspx).

## Octroi d’autorisations au niveau du serveur à une connexion

Pour que les connexions autres que le principal au niveau du serveur puissent gérer la sécurité au niveau du serveur, la base de données SQL Azure offre deux rôles en matière de sécurité : **loginmanager** pour la création de connexions, et **dbmanager** pour la création de bases de données. Seuls des utilisateurs présents dans la base de données **MASTER** peuvent être ajoutés à ces rôles de base de données.

> [AZURE.NOTE] Pour créer des connexions ou des bases de données, vous devez être connecté à la base de données **master** (qui est une représentation logique de **master**).

### Le rôle loginmanager

À l’instar du rôle de serveur fixe **securityadmin** d’une instance locale de SQL Server, le rôle de base de données **loginmanager** dans la base de données SQL Azure est autorisé à créer des connexions. Seuls la connexion du principal au niveau du serveur (créée par le processus d’approvisionnement) ou les membres du rôle de base de données **loginmanager** peuvent créer des connexions.

### Le rôle dbmanager

Le rôle de base de données **dbmanager** de la base de données SQL Azure est similaire au rôle de serveur fixe **dbcreator** sur une instance locale de SQL Server. Seuls la connexion du principal au niveau du serveur (créée par le processus d’approvisionnement) ou les membres du rôle de base de données **dbmanager** peuvent créer des bases de données. Une fois qu’un utilisateur est membre du rôle de base de données **dbmanager**, il peut créer une base de données avec la commande de base de données SQL Azure ``CREATE DATABASE``, mais cette commande doit être exécutée dans la base de données MASTER. Pour plus d’informations, voir [CRÉER UNE BASE DE DONNÉES (Transact-SQL)](https://msdn.microsoft.com/library/dn268335.aspx).

### Attribuer de rôles au niveau serveur de base de données SQL

Pour créer une connexion et un utilisateur associé en mesure de créer des bases de données ou d’autres connexions, procédez comme suit :

1. Connectez-vous à la base de données **MASTER** en utilisant les informations d’identification de la connexion du principal au niveau du serveur (créée par le processus d’approvisionnement) ou les informations d’identification d’un membre existant du rôle de base de données **loginmanager**.
2. Créez une connexion à l’aide de la commande ``CREATE LOGIN``. Pour plus d’informations, consultez [CRÉER UNE CONNEXION (Transact-SQL)](https://msdn.microsoft.com/library/ms189751.aspx).
3. Créez un utilisateur pour cette connexion dans la base de données MASTER à l’aide de la commande ``CREATE USER``. Pour plus d’informations, voir [CRÉER UN UTILISATEUR (Transact-SQL)](https://msdn.microsoft.com/library/ms173463.aspx).
4. Utilisez la procédure stockée ``sp_addrolememeber`` pour ajouter un nouvel utilisateur au rôle de base de données **dbmanager**, au rôle de base de données loginmanager, ou aux deux.

L’exemple de code qui suit montre comment créer une connexion nommée **login1**, et un utilisateur de base de données correspondant nommé **login1User** qui est en mesure de créer des bases de données ou d’autres connexions lorsqu’il est connecté à la base de données **MASTER** :

```
-- first, connect to the master database
CREATE LOGIN login1 WITH password='<ProvidePassword>';
CREATE USER login1User FROM LOGIN login1;
EXEC sp_addrolemember 'dbmanager', 'login1User';
EXEC sp_addrolemember 'loginmanager', 'login1User';
```

> [AZURE.NOTE] Vous devez utiliser un mot de passe fort au moment de la création d’une connexion. Pour plus d'informations, consultez la page [Mots de passe forts](https://msdn.microsoft.com/library/ms161962.aspx).

## Octroi d’un accès de base de données à une connexion

Toutes les connexions doivent être créées dans la base de données **MASTER**. Un fois la connexion créée, vous avez la possibilité de créer un compte utilisateur dans une autre base de données pour cette connexion. La base de données SQL Azure prend également en charge les rôles de base de données de la même façon qu’une instance locale de SQL Server.

Pour créer un compte utilisateur dans une autre base de données, en supposant que vous n’avez pas créé de connexion ou de base de données, procédez comme suit :

1. Se connecter à la base de données **master** (avec une connexion avec les rôles **loginmanager** et **dbmanager**).
2. Créez une connexion à l’aide de la commande ``CREATE LOGIN``. Pour plus d’informations, voir [CRÉER UNE CONNEXION (Transact-SQL)](https://msdn.microsoft.com/library/ms189751.aspx). L’authentification Windows n’est pas prise en charge.
3. Créez une base de données à l’aide de la commande ``CREATE DATABASE``. Pour plus d’informations, voir [CRÉER UNE BASE DE DONNÉES (Transact-SQL)](https://msdn.microsoft.com/library/dn268335.aspx).
4. Établir une connexion vers la nouvelle base de données (avec la connexion qui a créé la base de données).
5. Créez un utilisateur dans la nouvelle base de données à l’aide de la commande ``CREATE USER``. Pour plus d’informations, voir [CRÉER UN UTILISATEUR (Transact-SQL)](https://msdn.microsoft.com/library/ms173463.aspx).

L’exemple de code suivant montre comment créer une connexion nommée **login1** et une base de données nommée **database1** :

```
-- first, connect to the master database
CREATE LOGIN login1 WITH password='<ProvidePassword>';
CREATE DATABASE database1;
```

> [AZURE.NOTE] Vous devez utiliser un mot de passe fort au moment de la création d’une connexion. Pour plus d'informations, consultez la page [Mots de passe forts](https://msdn.microsoft.com/library/ms161962.aspx).

L’exemple suivant montre comment créer un utilisateur de base de données nommé **login1User** dans la base de données **database1**, qui correspond à la connexion **login1** : Pour exécuter l'exemple suivant, vous devez tout d'abord établir une nouvelle connexion à database1 via une connexion à l’aide de l’autorisation **ALTER ANY USER** dans cette base de données. Tout utilisateur qui se connecte en tant que membre du rôle **db\_owner** aura cette autorisation, tout comme la connexion qui a créé la base de données.

```
-- Establish a new connection to the database1 database
CREATE USER login1User FROM LOGIN login1;
```

Ce modèle d’autorisation au niveau de la base de données dans la base de données SQL Azure est identique à une instance locale de SQL Server. Pour plus d’informations, consultez les rubriques suivantes dans les références de la documentation en ligne de SQL Server.

- [Gestion des connexions, Utilisateurs et Schémas des rubriques de procédures](https://msdn.microsoft.com/library/aa337552.aspx)
- [Leçon 2 : configuration des autorisations sur les objets de base de données](https://msdn.microsoft.com/library/ms365345.aspx)

> [AZURE.NOTE] Les instructions liées à la sécurité Transact-SQL dans la base de données SQL Azure peuvent différer légèrement dans les paramètres disponibles. Pour plus d’informations, consultez Documentation de syntaxe en ligne pour instructions spécifiques.

## Affichage des connexions et bases de données


Pour afficher les connexions et bases de données sur votre serveur de base de données SQL Azure, utilisez respectivement les vues ``sys.sql_logins`` et ``sys.databases`` de la base de données master. L’exemple suivant montre comment afficher une liste de toutes les connexions et bases de données sur votre serveur de base de données SQL Azure.

```
-- first, connect to the master database
SELECT * FROM sys.sql_logins;
SELECT * FROM sys.databases;
```

## Voir aussi

[Instructions et limitations de sécurité dans la base de données SQL Azure](sql-database-security-guidelines.md) [Connexion à la base de données SQL avec l’authentification Azure Active Directory](sql-database-aad-authentication.md)

<!---HONumber=AcomDC_0128_2016-->