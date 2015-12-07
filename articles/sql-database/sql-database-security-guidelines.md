<properties
   pageTitle="Instructions de sécurité et limitations d’Azure SQL Database | Microsoft Azure"
   description="Découvrez les instructions et limitations de Microsoft Azure SQL Database relatives à la sécurité."
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
   ms.date="11/24/2015"
   ms.author="rickbyh"/>

# Instructions et limitations de sécurité d’Azure SQL Database

Cette rubrique décrit les instructions et limitations d’Azure SQL Database relatives à la sécurité. Considérez les points suivants lorsque vous gérez la sécurité de vos bases de données SQL Azure.

## Pare-feu

Le service Azure SQL Database Azure n’est disponible que via le port TCP 1433. Pour accéder à une base de données SQL depuis votre ordinateur, vérifiez que votre pare-feu autorise les communications TCP sortantes sur le port 1433. Dans le cadre du processus de connexion, les connexions à partir des machines virtuelles Azure sont redirigées vers une autre adresse IP et un autre port, propres à chaque rôle de travail. Le numéro du port sera compris entre 11000 et 11999.

Lors de votre première connexion au serveur Azure SQL Database, vous devez utiliser le [portail Azure](https://portal.azure.com) ou le [portail de gestion de la plateforme Azure](https://manage.windowsazure.com/microsoft.onmicrosoft.com#Workspaces/All/dashboard) pour configurer le pare-feu Azure SQL Database. Vous devez créer un paramètre de pare-feu au niveau du serveur, qui autorise les tentatives de connexion depuis votre ordinateur vers le serveur Azure SQL Database. De plus, si vous souhaitez contrôler l’accès à certaines bases de données de votre serveur Azure SQL Database, créez des règles de pare-feu de niveau base de données pour les bases de données respectives. Pour en savoir plus, voir [Pare-feu de base de données SQL Azure](sql-database-firewall-configure.md).

## Chiffrement de connexion et validation des certificats

Toutes les communications entre SQL Database et votre application requièrent un chiffrement (SSL) à tout moment. Si votre application cliente ne valide pas les certificats lors de la connexion, votre connexion à SQL Database est vulnérable aux attaques de « l’homme du milieu ».

Pour valider des certificats avec le code ou les outils de l’application, demandez explicitement une connexion chiffrée et ne faites pas confiance aux certificats de serveur. Si le code ou les outils de votre application ne demandent pas une connexion chiffrée, ils reçoivent toujours des connexions chiffrées. Cependant, comme ils ne peuvent pas valider les certificats de serveur, ils restent vulnérables aux attaques de « l’homme du milieu ».

Pour valider des certificats avec le code d’application ADO.NET, définissez ``Encrypt=True`` et ``TrustServerCertificate=False`` dans la chaîne de connexion de base de données. Pour plus d’informations, consultez [Exemple de code : logique de nouvelle tentative pour se connecter à Azure SQL Database à l’aide d’ADO.NET](https://msdn.microsoft.com/library/azure/ee336243.aspx).

SQL Server Management Studio prend également en charge la validation de certificats. Dans la boîte de dialogue **Se connecter au serveur**, cliquez sur **Chiffrer la connexion** dans l’onglet **Propriétés de connexion**.

> [AZURE.NOTE]SQL Server Management Studio ne prend pas en charge les connexions à SQL Database dans les versions antérieures de SQL Server 2008 R2.

Bien que SQLCMD prenne en charge SQL Database à partir de SQL Server 2008, il ne gère pas la validation de certificats dans les versions antérieures à SQL Server 2008 R2. Pour valider des certificats avec SQLCMD à partir de SQL Server 2008 R2, utilisez l’option de ligne de commande ``-N`` et n’utilisez pas l’option ``-C``. Avec l’option -N, SQLCMD demande une connexion chiffrée. Sans l’option ``-C``, SQLCMD n’approuve pas implicitement le certificat de serveur et est forcé de le valider.

Pour plus d’informations techniques, consultez l’article [Azure SQL Database Connection Security](http://social.technet.microsoft.com/wiki/contents/articles/2951.windows-azure-sql-database-connection-security.aspx#comment-4847) sur le site Wiki de TechNet.

## Authentification

L’authentification Active Directory (sécurité intégrée) est disponible en version préliminaire dans la version 12 de la base de données SQL. Pour plus d’informations sur la configuration de l’authentification AD, voir [Connexion à la base de données SQL avec l’authentification Azure Active Directory](sql-database-aad-authentication.md). Quand ils n’utilisent pas la version préliminaire, les utilisateurs doivent fournir leurs informations d’identification (nom d’utilisateur et mot de passe) chaque fois qu’ils se connectent à la base de données SQL. Pour plus d’informations sur l’authentification SQL Server, voir [Choix d’un mode d’authentification](https://msdn.microsoft.com/library/ms144284.aspx) dans la documentation en ligne de SQL Server.

La [base de données SQL V12](sql-database-v12-whats-new.md) permet aux utilisateurs de s’authentifier pour la base de données à l’aide des utilisateurs de base de données contenu. Pour plus d’informations, voir [Utilisateurs de base de données à relation contenant-contenu - Rendre votre base de données portable](https://msdn.microsoft.com/library/ff929188.aspx), [CREATE USER (Transact-SQL)](https://technet.microsoft.com/library/ms173463.aspx) et [Bases de données à relation contenant-contenu](https://technet.microsoft.com/library/ff929071.aspx).

> [AZURE.NOTE]Pour plus d’évolutivité, Microsoft recommande l’emploi d’utilisateurs de base de données à relation contenant-contenu.

Le moteur de base de données ferme les connexions restées inactives pendant plus de 30 minutes. La connexion nécessite une nouvelle identification pour fonctionner à nouveau.

Les connexions perpétuellement actives à SQL Database requièrent une nouvelle autorisation (effectuée par le moteur de base de données) au moins toutes les 10 heures. Le moteur de base de données tente de renouveler l’autorisation à l’aide du mot de passe envoyé à l’origine. L’utilisateur n’a rien à saisir. Pour des raisons de performances, lorsqu’un mot de passe est réinitialisé dans SQL Database, la connexion n’est pas authentifiée à nouveau, même si elle est réinitialisée suite à un regroupement de connexions. Ce comportement est différent de SQL Server local. Si le mot de passe a été modifié depuis l’autorisation initiale de la connexion, celle-ci doit être interrompue et une nouvelle connexion établie à l’aide du nouveau mot de passe. Un utilisateur disposant de l’autorisation KILL DATABASE CONNECTION peut mettre explicitement fin à une connexion à Base de données SQL à l’aide de la commande [KILL](https://msdn.microsoft.com/library/ms173730.aspx).

## Connexions et utilisateurs

La gestion des connexions et des utilisateurs dans SQL Database est soumise à des restrictions.

Les restrictions suivantes s’appliquent à la connexion principale au niveau du serveur :

- L’utilisateur de base de données dans la base de données master, correspondant à la connexion principale au niveau du serveur, ne peut pas être modifié ou supprimé. 
- Bien que la connexion principale au niveau du serveur ne soit pas membre des deux rôles de base de données **dbmanager** et **loginmanager** dans la base de données **MASTER**, elle possède toutes les autorisations accordées à ces deux rôles.

> [AZURE.NOTE]Cette connexion est créée lors de l’approvisionnement du serveur et est similaire à la connexion **sa** dans une instance de SQL Server.

Les restrictions suivantes s’appliquent à toutes les connexions :

- La langue par défaut est l’anglais américain.
- Pour accéder à la base de données **MASTER**, chaque connexion doit être mappée à un compte d’utilisateur de cette base de données **MASTER**. La base de données **MASTER** ne prend pas en charge les utilisateurs de base de données à relation contenant-contenu.
- Si vous ne spécifiez pas une base de données dans la chaîne de connexion, vous êtes connecté à la base de données **MASTER** par défaut.
- Vous devez être connecté à la base de données **MASTER** lors de l’exécution des instructions ``CREATE/ALTER/DROP LOGIN`` et ``CREATE/ALTER/DROP DATABASE``. 
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
- Seule la connexion principale au niveau du serveur et les membres du rôle de base de données **dbmanager** dans la base de données **MASTER** sont autorisés à exécuter les instructions ``CREATE DATABASE`` et ``DROP DATABASE``.
- Seule la connexion principale au niveau du serveur et les membres du rôle de base de données **loginmanager** dans la base de données **MASTER** sont autorisés à exécuter les instructions ``CREATE LOGIN``, ``ALTER LOGIN`` et ``DROP LOGIN``.
- Pour exécuter les instructions ``CREATE/ALTER/DROP``, un utilisateur a besoin de l’autorisation ``ALTER ANY USER`` sur la base de données.
- Lorsque le propriétaire d’un rôle de base de données tente d’ajouter un autre utilisateur de base de données dans ce rôle de base de données (ou de le supprimer de ce dernier), l’erreur suivante peut se produire : **L’utilisateur ou le rôle « Nom » n’existe pas dans cette base de données**. Cette erreur survient, car l’utilisateur n’est pas visible par le propriétaire. Pour résoudre ce problème, accordez au propriétaire du rôle l’autorisation ``VIEW DEFINITION`` sur l’utilisateur. 

Pour plus d’informations sur les connexions et les utilisateurs, voir [Gestion des bases de données et des connexions dans Base de données SQL Azure](sql-database-manage-logins.md).

## Meilleures pratiques en matière de sécurité

Considérez les points suivants pour réduire la vulnérabilité de vos applications Azure SQL Database aux menaces de sécurité :

- Utilisez toujours les dernières mises à jour : lors de la connexion à votre base de données SQL, utilisez systématiquement la version la plus récente des outils et des bibliothèques afin d’éviter les points de vulnérabilité.
- Bloquez les connexions entrantes sur le port TCP 1433 : seules les connexions sortantes sur le port TCP 1433 sont nécessaires aux applications pour communiquer avec SQL Database. Si les communications entrantes ne sont pas requises par d’autres applications sur cet ordinateur, veillez à ce que votre pare-feu continue de bloquer les connexions entrantes sur le port TCP 1433.
- Évitez les vulnérabilités d’injection : pour protéger vos applications contre l’injection SQL, utilisez des requêtes paramétrées dans la mesure du possible. En outre, examinez soigneusement le code et effectuez un test de pénétration avant de déployer votre application.


## Voir aussi

[Pare-feu Azure SQL Database](sql-database-firewall-configure.md)

[Configuration des paramètres du pare-feu (Base de données SQL Azure)](sql-database-configure-firewall-settings.md)

[Gestion des bases de données et des connexions dans Azure SQL Database](sql-database-manage-logins.md)

[Centre de sécurité pour le moteur de base de données SQL Server et la base de données SQL Azure](https://msdn.microsoft.com/library/bb510589)

<!---HONumber=AcomDC_1125_2015-->