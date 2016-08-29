<properties
   pageTitle="Sécuriser une base de données dans SQL Data Warehouse | Microsoft Azure"
   description="Conseils relatifs à la sécurisation d’une base de données dans Microsoft Azure SQL Data Warehouse, dans le cadre du développement de solutions."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="ronortloff"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="07/22/2016"
   ms.author="rortloff;barbkess;sonyama"/>

# Sécuriser une base de données dans SQL Data Warehouse

> [AZURE.SELECTOR]
- [Présentation de la sécurité](sql-data-warehouse-overview-manage-security.md)
- [Détection de menaces](sql-data-warehouse-security-threat-detection.md)
- [Chiffrement (portail)](sql-data-warehouse-encryption-tde.md)
- [Chiffrement (T-SQL)](sql-data-warehouse-encryption-tde-tsql.md)
- [Vue d’ensemble de l’audit](sql-data-warehouse-auditing-overview.md)
- [Audit des clients de niveau inférieur](sql-data-warehouse-auditing-downlevel-clients.md)



Cet article présente les principes de base de la sécurisation de votre base de données Microsoft Azure SQL Data Warehouse. Plus spécifiquement, cet article vous offre un aperçu sur les ressources dédiées à la limitation de l’accès, à la protection des données et à la surveillance des activités sur une base de données.

## Sécurité de la connexion

L’expression « sécurité de la connexion » fait référence au mode de restriction et de sécurisation appliqué aux connexions à votre base de données, au moyen de règles de pare-feu et d’une fonction de chiffrement des connexions.

Les règles de pare-feu sont utilisées par le serveur et la base de données pour refuser toute tentative de connexion d’une adresse IP qui ne fait pas partie d’une liste blanche explicite. Pour permettre une connexion depuis l’adresse IP publique de l’ordinateur client ou votre application, vous devez d’abord créer une règle de pare-feu au niveau du serveur à l’aide du portail Azure Classic, d’une API REST ou de PowerShell. Nous vous recommandons, à titre de meilleure pratique, de limiter autant que possible le nombre de plages d’adresses IP autorisées à traverser le pare-feu de votre serveur. Pour accéder à SQL Data Warehouse à partir de votre ordinateur local, vérifiez que le pare-feu sur votre réseau et l’ordinateur local autorise les communications sortantes sur le port TCP 1433. Pour plus d’informations, voir [Pare-feu de la base de données Azure SQL][], [sp\_set\_firewall\_rule][], et [sp\_set\_database\_firewall\_rule][].

Les connexions à votre SQL Data Warehouse peuvent être chiffrées en définissant le mode de chiffrement dans votre chaîne de connexion. La syntaxe pour l’activation du chiffrement de la connexion varie en fonction du protocole. Pour obtenir de l’aide pour la configuration de votre chaîne de connexion, accédez à votre base de données dans le portail Azure. Sous *Essentials* cliquez sur *Afficher les chaînes de connexion de la base de données*.


## Authentification

Le terme « authentification » fait référence au processus de validation de votre identité lorsque vous vous connectez à la base de données. SQL Data Warehouse prend actuellement en charge l’authentification SQL Server avec un nom d’utilisateur et un mot de passe, ainsi que la version d’évaluation d’Azure Active Directory.

Lorsque vous avez créé un serveur logique pour votre base de données, vous avez spécifié un compte de connexion « Admin serveur », associé à un nom d’utilisateur et à un mot de passe. À l’aide de ces informations d’identification, vous pouvez vous authentifier auprès de n’importe quelle base de données sur ce serveur, en tant que propriétaire de la base de données, ou « dbo » via l’authentification SQL Server.

Toutefois, au titre de meilleure pratique, il est recommandé que les utilisateurs de votre organisation utilisent un compte différent pour s’authentifier. Cela vous permet de limiter les autorisations accordées à cette application et de réduire les risques d’activité malveillante, au cas où le code de votre application serait vulnérable à une attaque par injection de code SQL.

Pour créer un utilisateur authentifié par SQL Server, connectez-vous à la base de données **master** sur votre serveur avec votre identifiant de connexion d’administrateur du serveur, et créez un nouvel identifiant de connexion au serveur.

```sql
-- Connect to master database and create a login
CREATE LOGIN ApplicationLogin WITH PASSWORD = 'strong_password';

```

Ensuite, connectez-vous à votre **base de données SQL Data Warehouse** avec votre identifiant de connexion d’administrateur du serveur, puis créez un utilisateur de base de données basé sur l’identifiant de connexion au serveur que vous venez de créer.

```sql
-- Connect to SQL DW database and create a database user
CREATE USER ApplicationUser FOR LOGIN ApplicationLogin;

```

Pour en savoir plus sur l’authentification auprès d’une base de données SQL, voir [Gestion des bases de données et des connexions dans la base de données SQL Microsoft Azure][]. Pour plus de détails sur l’utilisation de la version préliminaire d’Azure AD pour SQL Data Warehouse, consultez [Connexion à SQL Data Warehouse avec l’authentification Azure Active Directory][].


## Autorisation

Le terme « autorisation » fait référence aux actions que vous pouvez exécuter dans une base de données Microsoft Azure SQL Data Warehouse, actions contrôlées par les autorisations et l’appartenance au rôle de votre compte utilisateur. Nous vous recommandons, à titre de meilleure pratique, d’accorder aux utilisateurs des privilèges aussi réduits que possible. La base de données Microsoft Azure SQL Data Warehouse facilite la gestion des rôles dans T-SQL :

```sql
EXEC sp_addrolemember 'db_datareader', 'ApplicationUser'; -- allows ApplicationUser to read data
EXEC sp_addrolemember 'db_datawriter', 'ApplicationUser'; -- allows ApplicationUser to write data
```

Le compte d’administrateur du serveur avec lequel vous vous connectez appartient au groupe « db\_owner », qui est autorisé à effectuer tout type d’opérations dans la base de données. Enregistrez ce compte pour l’utiliser lors du déploiement des mises à niveau de schéma et d’autres opérations de gestion. Utilisez le compte « ApplicationUser », doté d’autorisations plus limitées, pour vous connecter à la base de données à partir de votre application, en bénéficiant du niveau de privilèges le moins élevé requis par cette dernière.

Il existe d’autres méthodes pour limiter le nombre d’actions que peut réaliser un utilisateur avec la base de données SQL Microsoft Azure :

- Des [autorisations][] granulaires vous permettent de contrôler les opérations que vous pouvez exécuter sur des colonnes, des tables, des vues, des procédures et d’autres objets individuels dans la base de données. Utilisez les autorisations granulaires pour avoir un contrôle optimal et accordez les autorisations minimales nécessaires. Le système d'autorisation granulaire est quelque peu compliqué et nécessitera un apprentissage avant de pouvoir l’utiliser efficacement.
- Des [rôles de base de données][] autres que « db\_datareader » et « db\_datawriter » peuvent être utilisés afin de créer des comptes d’utilisateur plus puissants ou des comptes de gestion moins puissants pour votre application. Les rôles de base de données fixes intégrés offrent un moyen facile d'accorder des autorisations, mais peuvent entraîner l'octroi d'autorisations excessives.
- Grâce aux [procédures stockées][], vous pouvez limiter le nombre d’actions susceptibles d’être exécutées sur la base de données.

La gestion des bases de données et serveurs logiques à partir du portail Azure Classic et l’utilisation de l’API Azure Resource Manager sont contrôlées par les affectations associées au rôle de votre compte d’utilisateur sur le portail. Pour en savoir plus à ce sujet, voir [Contrôle d’accès en fonction du rôle dans le Portail Azure][].

## Chiffrement

Azure SQL Data Warehouse peut vous aider à protéger vos données en les chiffrant lorsqu’elles sont « au repos » ou stockées dans des fichiers de base de données et des sauvegardes, à l’aide d’un [chiffrement transparent des données][]. Vous devez être un administrateur ou un membre du rôle dbmanager dans la base de données master pour activer le chiffrement transparent des données. Pour chiffrer votre base de données, connectez-vous à la base de données MASTER sur votre serveur et exécutez :


```sql

ALTER DATABASE [AdventureWorks] SET ENCRYPTION ON;

```

Vous pouvez également activer le chiffrement transparent des données à partir des paramètres de base de données dans le [portail Azure][]. Pour plus d’informations, consultez [Prise en main du chiffrement transparent des données (TDE)][].

## Audit

Les fonctions d’audit et de suivi des événements de la base de données peuvent vous aider à assurer la conformité aux normes et à identifier toute activité suspecte. La fonction d’audit de SQL Data Warehouse vous permet d’enregistrer les événements survenus dans votre base de données dans un journal d’audit au sein de votre compte Microsoft Azure Storage. Cette fonction s’intègre également dans Microsoft Power BI, afin de faciliter la création d’analyses et de rapports approfondis. Pour en savoir plus, voir [Prise en main de l’audit de base de données SQL][].

## Étapes suivantes
Pour plus d’informations et des exemples sur la connexion à SQL Data Warehouse avec différents protocoles, consultez [Se connecter à SQL Data Warehouse][].

<!--Image references-->

<!--Article references-->
[Se connecter à SQL Data Warehouse]: ./sql-data-warehouse-connect-overview.md
[Prise en main de l’audit de base de données SQL]: ./sql-data-warehouse-auditing-overview.md
[Prise en main du chiffrement transparent des données (TDE)]: ./sql-data-warehouse-encryption-tde.md
[Connexion à SQL Data Warehouse avec l’authentification Azure Active Directory]: ./sql-data-warehouse-authentication.md

<!--MSDN references-->
[Pare-feu de la base de données Azure SQL]: https://msdn.microsoft.com/library/ee621782.aspx
[sp\_set\_firewall\_rule]: https://msdn.microsoft.com/library/dn270017.aspx
[sp\_set\_database\_firewall\_rule]: https://msdn.microsoft.com/library/dn270010.aspx
[rôles de base de données]: https://msdn.microsoft.com/library/ms189121.aspx
[Gestion des bases de données et des connexions dans la base de données SQL Microsoft Azure]: https://msdn.microsoft.com/library/ee336235.aspx
[autorisations]: https://msdn.microsoft.com/library/ms191291.aspx
[procédures stockées]: https://msdn.microsoft.com/library/ms190782.aspx
[chiffrement transparent des données]: https://go.microsoft.com/fwlink/?LinkId=526242
[portail Azure]: https://portal.azure.com/

<!--Other Web references-->
[Contrôle d’accès en fonction du rôle dans le Portail Azure]: https://azure.microsoft.com/documentation/articles/role-based-access-control-configure

<!---HONumber=AcomDC_0817_2016-->