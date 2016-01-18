<properties
   pageTitle="Sécuriser une base de données dans SQL Data Warehouse | Microsoft Azure"
   description="Conseils relatifs à la sécurisation d’une base de données dans Microsoft Azure SQL Data Warehouse, dans le cadre du développement de solutions."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sahaj08"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="01/04/2016"
   ms.author="sahajs"/>

# Sécuriser une base de données dans SQL Data Warehouse

Cet article présente les principes de base de la sécurisation de votre base de données Microsoft Azure SQL Data Warehouse. Plus spécifiquement, cet article vous offre un aperçu sur les ressources dédiées à la limitation de l’accès, à la protection des données et à la surveillance des activités sur une base de données.

## Sécurité de la connexion

L’expression « sécurité de la connexion » fait référence au mode de restriction et de sécurisation appliqué aux connexions à votre base de données, au moyen de règles de pare-feu et d’une fonction de chiffrement des connexions.

Les règles de pare-feu sont utilisées par le serveur et la base de données pour refuser toute tentative de connexion d’une adresse IP qui ne fait pas partie d’une liste blanche explicite. Pour permettre à l’adresse IP publique de l’ordinateur client ou à votre application de tenter de se connecter à une nouvelle base de données, vous devez d’abord créer une règle de pare-feu au niveau du serveur, via le portail Classic de Microsoft Azure, l’API REST ou PowerShell. Nous vous recommandons, à titre de meilleure pratique, de limiter autant que possible le nombre de plages d’adresses IP autorisées à traverser le pare-feu de votre serveur. Pour en savoir plus, voir [Pare-feu de base de données SQL Azure][].


## Authentification

Le terme « authentification » fait référence au processus de validation de votre identité lorsque vous vous connectez à la base de données. SQL Data Warehouse prend actuellement en charge l’authentification SQL avec un nom d’utilisateur et un mot de passe.

Lorsque vous avez créé un serveur logique pour votre base de données, vous avez spécifié un compte de connexion « Admin serveur », associé à un nom d’utilisateur et à un mot de passe. À l’aide de ces informations d’identification, vous pouvez vous authentifier auprès de n’importe quelle base de données sur ce serveur, en tant que propriétaire de la base de données, ou « dbo ».

Toutefois, au titre de meilleure pratique, il est recommandé que les utilisateurs de votre organisation utilisent un compte différent pour s’authentifier. Cela vous permet de limiter les autorisations accordées à cette application et de réduire les risques d’activité malveillante, au cas où le code de votre application serait vulnérable à une attaque par injection de code SQL. Pour créer un utilisateur de base de données basé sur la connexion au serveur :

Tout d’abord, connectez-vous à la base de données MASTER sur votre serveur avec votre identifiant de connexion d'administrateur du serveur, et créez un nouvel identifiant de connexion au serveur.

```
-- Connect to master database and create a login
CREATE LOGIN ApplicationLogin WITH PASSWORD = 'strong_password';

```

Ensuite, connectez-vous à votre base de données SQL Data Warehouse avec votre avec votre identifiant de connexion d'administrateur du serveur, et créez un utilisateur de base de données basé sur l’identifiant de connexion au serveur que vous venez de créer.

```

-- Connect to SQL DW database and create a database user
CREATE USER ApplicationUser FOR LOGIN ApplicationLogin;

```

Pour en savoir plus sur l’authentification auprès d’une base de données SQL, voir [Gestion des bases de données et des connexions dans la base de données SQL Microsoft Azure][].


## Autorisation

Le terme « autorisation » fait référence aux actions que vous pouvez exécuter dans une base de données Microsoft Azure SQL Data Warehouse, actions contrôlées par les autorisations et l’appartenance au rôle de votre compte utilisateur. Nous vous recommandons, à titre de meilleure pratique, d’accorder aux utilisateurs des privilèges aussi réduits que possible. La base de données Microsoft Azure SQL Data Warehouse facilite la gestion des rôles dans T-SQL :

```
EXEC sp_addrolemember 'db_datareader', 'ApplicationUser'; -- allows ApplicationUser to read data
EXEC sp_addrolemember 'db_datawriter', 'ApplicationUser'; -- allows ApplicationUser to write data
```

Le compte d’administrateur du serveur avec lequel vous vous connectez appartient au groupe « db\_owner », qui est autorisé à effectuer tout type d’opérations dans la base de données. Enregistrez ce compte pour l’utiliser lors du déploiement des mises à niveau de schéma et d’autres opérations de gestion. Utilisez le compte « ApplicationUser », doté d’autorisations plus limitées, pour vous connecter à la base de données à partir de votre application, en bénéficiant du niveau de privilèges le moins élevé requis par cette dernière.

Il existe d’autres méthodes pour limiter le nombre d’actions que peut réaliser un utilisateur avec la base de données SQL Microsoft Azure :

- Des [rôles de base de données][] autres que « db\_datareader » et « db\_datawriter » peuvent être utilisés afin de créer des comptes d’utilisateur plus puissants ou des comptes de gestion moins puissants pour votre application.
- Des [autorisations][] granulaires vous permettent de contrôler les opérations que vous pouvez exécuter sur des colonnes, des tables, des vues, des procédures et d’autres objets individuels dans la base de données.
- Grâce aux [procédures stockées][], vous pouvez limiter le nombre d’actions susceptibles d’être exécutées sur la base de données.

La gestion des bases de données et serveurs logiques à partir du portail Azure Classic et l’utilisation de l’API Azure Resource Manager sont contrôlées par les affectations associées au rôle de votre compte d’utilisateur sur le portail. Pour en savoir plus à ce sujet, voir [Contrôle d’accès en fonction du rôle dans le portail Azure][].



## Chiffrement

Azure SQL Data Warehouse peut vous aider à protéger vos données en les chiffrant lorsqu’elles sont « au repos » ou stockées dans des fichiers de base de données et des sauvegardes, à l’aide d’un [chiffrement transparent des données][]. Pour chiffrer votre base de données, connectez-vous à la base de données MASTER sur votre serveur et exécutez :


```

ALTER DATABASE [AdventureWorks] SET ENCRYPTION ON;

```

Vous pouvez également activer le chiffrement transparent des données à partir des paramètres de base de données dans le [portail Azure Classic][].



## Audit

Les fonctions d’audit et de suivi des événements de la base de données peuvent vous aider à assurer la conformité aux normes et à identifier toute activité suspecte. La fonction d’audit de SQL Data Warehouse vous permet d’enregistrer les événements survenus dans votre base de données dans un journal d’audit au sein de votre compte Microsoft Azure Storage. Cette fonction s’intègre également dans Microsoft Power BI, afin de faciliter la création d’analyses et de rapports approfondis. Pour en savoir plus, voir [Prise en main de l’audit de base de données SQL][].



## Étapes suivantes
Pour obtenir des conseils supplémentaires en matière de développement, voir la [vue d’ensemble sur le développement][].

<!--Image references-->

<!--Article references-->
[vue d’ensemble sur le développement]: sql-data-warehouse-overview-develop.md


<!--MSDN references-->
[Pare-feu de base de données SQL Azure]: https://msdn.microsoft.com/library/ee621782.aspx
[rôles de base de données]: https://msdn.microsoft.com/library/ms189121.aspx
[Gestion des bases de données et des connexions dans la base de données SQL Microsoft Azure]: https://msdn.microsoft.com/library/ee336235.aspx
[autorisations]: https://msdn.microsoft.com/library/ms191291.aspx
[procédures stockées]: https://msdn.microsoft.com/library/ms190782.aspx
[chiffrement transparent des données]: http://go.microsoft.com/fwlink/?LinkId=526242
[Prise en main de l’audit de base de données SQL]: sql-database-auditing-get-started.md
[portail Azure Classic]: https://portal.azure.com/

<!--Other Web references-->
[Contrôle d’accès en fonction du rôle dans le portail Azure]: http://azure.microsoft.com/documentation/articles/role-based-access-control-configure.aspx

<!---HONumber=AcomDC_0107_2016-->