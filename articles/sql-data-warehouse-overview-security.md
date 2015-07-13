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
   ms.date="06/22/2015"
   ms.author="sahajs"/>

# Sécuriser une base de données dans SQL Data Warehouse

Cet article présente les principes de base de la sécurisation de votre base de données Microsoft Azure SQL Data Warehouse. Plus spécifiquement, cet article vous offre un aperçu sur les ressources dédiées à la limitation de l’accès, à la protection des données et à la surveillance des activités sur une base de données.

## Sécurité de la connexion

L’expression « sécurité de la connexion » fait référence au mode de restriction et de sécurisation appliqué aux connexions à votre base de données, au moyen de règles de pare-feu et d’une fonction de chiffrement des connexions.

Les règles de pare-feu sont utilisées par le serveur et la base de données pour refuser toute tentative de connexion d’une adresse IP qui ne fait pas partie d’une liste blanche explicite. Pour permettre à l’adresse IP publique de l’ordinateur client ou à votre application de tenter de se connecter à une nouvelle base de données, vous devez d’abord créer une règle de pare-feu au niveau du serveur, via le portail de gestion de Microsoft Azure, l’API REST ou PowerShell. Nous vous recommandons, à titre de meilleure pratique, de limiter autant que possible le nombre de plages d’adresses IP autorisées à traverser le pare-feu de votre serveur. Pour en savoir plus, voir [Pare-feu de base de données SQL Azure][].


## Authentification

Le terme « authentification » fait référence au processus de validation de votre identité lorsque vous vous connectez à la base de données. SQL Data Warehouse prend actuellement en charge l’authentification SQL avec un nom d’utilisateur et un mot de passe.

Lorsque vous avez créé un serveur logique pour votre base de données, vous avez spécifié un compte de connexion « Admin serveur », associé à un nom d’utilisateur et à un mot de passe. À l’aide de ces informations d’identification, vous pouvez vous authentifier auprès de n’importe quelle base de données sur ce serveur, en tant que propriétaire de la base de données, ou « dbo ».

Toutefois, nous recommandons à vos utilisateurs, à titre de meilleure pratique, d’utiliser un autre compte pour l’authentification de votre application. Cela vous permet de limiter les autorisations accordées à cette application et de réduire les risques d’activité malveillante, au cas où le code de votre application serait vulnérable à une attaque par injection de code SQL. La méthode recommandée consiste à créer un utilisateur contenu, afin de permettre à votre application de se connecter directement à une base de données, via un nom d’utilisateur et un mot de passe. Vous pouvez créer un utilisateur contenu en exécutant le code T-SQL suivant, tout en étant connecté à votre base de données utilisateur avec votre connexion d’administrateur de serveur :

```
CREATE USER ApplicationUser WITH PASSWORD = 'strong_password';
```

Pour en savoir plus sur l’authentification auprès d’une base de données SQL, voir [Gestion des bases de données et des connexions dans la base de données SQL Microsoft Azure][].


## Autorisation

Le terme « autorisation » fait référence aux actions que vous pouvez exécuter dans une base de données Microsoft Azure SQL Data Warehouse, actions contrôlées par les autorisations et l’appartenance au rôle de votre compte utilisateur. Nous vous recommandons, à titre de meilleure pratique, d’accorder aux utilisateurs des privilèges aussi réduits que possible. La base de données Microsoft Azure SQL Data Warehouse facilite la gestion des rôles dans T-SQL :

```
ALTER ROLE db_datareader ADD MEMBER ApplicationUser; -- allows ApplicationUser to read data
ALTER ROLE db_datawriter ADD MEMBER ApplicationUser; -- allows ApplicationUser to write data
```

Le compte d’administrateur du serveur avec lequel vous vous connectez appartient au groupe « db_owner », qui est autorisé à effectuer tout type d’opérations dans la base de données. Enregistrez ce compte pour l’utiliser lors du déploiement des mises à niveau de schéma et d’autres opérations de gestion. Utilisez le compte « ApplicationUser », doté d’autorisations plus limitées, pour vous connecter à la base de données à partir de votre application, en bénéficiant du niveau de privilèges le moins élevé requis par cette dernière.

Il existe des moyens supplémentaires permettant de limiter les actions de l’utilisateur dans une base de données SQL Microsoft Azure : - Les [rôles de base de données][] différents de db_datareader et de db_datawriter peuvent être utilisés pour créer des comptes plus puissants d’utilisateur de l’application ou des comptes de gestion moins puissants. - Les [autorisations][] granulaires sont utilisées pour contrôler les opérations pouvant être exécutées sur les différentes colonnes, tables, vues, procédures et les autres objets de la base de données. - Les [procédures stockées][] servent à restreindre les actions pouvant être exécutées sur la base de données.

La gestion des bases de données et serveurs logiques à partir du portail de gestion Microsoft Azure et l’utilisation de l’API Azure Resource Manager sont contrôlées par les affectations associées au rôle de votre compte d’utilisateur sur le portail. Pour en savoir plus à ce sujet, voir [Contrôle d’accès en fonction du rôle dans la version préliminaire du portail Azure][].


## Étapes suivantes
Pour obtenir des conseils supplémentaires sur le développement, consultez la [vue d’ensemble sur le développement][].

<!--Image references-->

<!--Article references-->
[vue d’ensemble sur le développement]: sql-data-warehouse-overview-develop.md


<!--MSDN references-->
[Pare-feu de base de données SQL Azure]: https://msdn.microsoft.com/library/ee621782.aspx
[rôles de base de données]: https://msdn.microsoft.com/library/ms189121.aspx
[Gestion des bases de données et des connexions dans la base de données SQL Microsoft Azure]: https://msdn.microsoft.com/library/ee336235.aspx
[autorisations]: https://msdn.microsoft.com/library/ms191291.aspx
[procédures stockées]: https://msdn.microsoft.com/library/ms190782.aspx
[Transparent Data Encryption]: http://go.microsoft.com/fwlink/?linkid=526242&clcid=0x409

<!--Other Web references-->
[Contrôle d’accès en fonction du rôle dans la version préliminaire du portail Azure]: http://azure.microsoft.com/documentation/articles/role-based-access-control-configure.aspx

<!---HONumber=July15_HO1-->