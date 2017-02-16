---
title: "Vue d’ensemble de la sécurité de la base de données SQL"
description: "Découvrez plus d’informations sur la base de données SQL Microsoft Azure et la sécurité de la base de données SQL, y compris les différences entre le cloud et l’installation SQL Server sur site en termes d’authentification, d’autorisations, de sécurité des connexions, de chiffrement et de conformité."
services: sql-database
documentationcenter: 
author: tmullaney
manager: jhubbard
editor: 
ms.assetid: a012bb85-7fb4-4fde-a2fc-cf426c0a56bb
ms.service: sql-database
ms.custom: authentication and authorization
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: data-management
ms.date: 06/09/2016
ms.author: thmullan;jackr
translationtype: Human Translation
ms.sourcegitcommit: 145cdc5b686692b44d2c3593a128689a56812610
ms.openlocfilehash: 034f774cb7654e3ab8a0ad1e1a4eddf02d8452ed


---
# <a name="securing-your-sql-database"></a>Sécurisation de votre base de données SQL
## <a name="overview"></a>Vue d’ensemble
Cet article présente les principes de la sécurisation de la couche Données d’une application à l’aide de la base de données SQL Microsoft Azure. Cet article vous permet de prendre en main la gestion des ressources de restriction d’accès, de protection des données et de surveillance des activités sur une base de données créée via le [didacticiel sur la prise en main de la base de données SQL](sql-database-get-started.md). Pour obtenir une présentation complète des fonctionnalités de sécurité disponibles sur toutes les versions de SQL, consultez la section [Sécurité et protection (moteur de base de données)](https://msdn.microsoft.com/library/bb510589). Des informations supplémentaires sont également disponibles dans le [livre blanc technique sur la sécurité et la base de données SQL Azure](https://download.microsoft.com/download/A/C/3/AC305059-2B3F-4B08-9952-34CDCA8115A9/Security_and_Azure_SQL_Database_White_paper.pdf) (PDF).

## <a name="connection-security"></a>Sécurité de la connexion
L’expression « sécurité de la connexion » fait référence au mode de restriction et de sécurisation appliqué aux connexions à votre base de données, au moyen de règles de pare-feu et d’une fonction de chiffrement des connexions.

Les règles de pare-feu sont utilisées par le serveur et la base de données pour refuser toute tentative de connexion d’une adresse IP qui ne fait pas partie d’une liste blanche explicite. Pour permettre à l’adresse IP publique de l’ordinateur client ou à votre application de tenter de se connecter à une nouvelle base de données, vous devez d’abord créer une règle de pare-feu au niveau du serveur, via le portail Classic de Microsoft Azure, l’API REST ou PowerShell. Nous vous recommandons, à titre de meilleure pratique, de limiter autant que possible le nombre de plages d’adresses IP autorisées à traverser le pare-feu de votre serveur. Pour en savoir plus, consultez [Pare-feu de la base de données SQL Azure](https://msdn.microsoft.com/library/ee621782).

Toutes les connexions à la base de données SQL Microsoft Azure doivent être chiffrées à tout moment (via SSL/TLS) lorsque les données sont « en transit » depuis et vers la base de données. Dans la chaîne de connexion de votre application, vous devez spécifier des paramètres permettant de chiffrer la connexion et *non* d’approuver le certificat de serveur (le système effectue cette opération pour vous lorsque vous copiez la chaîne de connexion hors du portail Azure Classic). Dans le cas contraire, la connexion ne vérifie pas l’identité du serveur et peut être la cible d’attaques de type MITM (« Man-In-The-Middle »). Pour le pilote ADO.NET, par exemple, ces paramètres de chaîne de connexion sont les suivants : **Encrypt=True** et **TrustServerCertificate=False**. Pour en savoir plus, voir [Instructions de sécurité et limitations de la base de données SQL Azure](https://msdn.microsoft.com/library/azure/ff394108#encryption).

## <a name="authentication"></a>Authentification
Le terme « authentification » fait référence au processus de validation de votre identité lorsque vous vous connectez à la base de données. Une base de données SQL prend en charge deux types d’authentification :

* **l’authentification SQL**, qui utilise un nom d’utilisateur et un mot de passe
* **l’authentification Azure Active Directory**, qui utilise des identités gérées par Azure Active Directory et est prise en charge pour des domaines gérés et intégrés.

Lorsque vous avez créé un serveur logique pour votre base de données, vous avez spécifié un compte de connexion « Admin serveur », associé à un nom d’utilisateur et à un mot de passe. À l’aide de ces informations d’identification, vous pouvez vous authentifier auprès de n’importe quelle base de données sur ce serveur, en tant que propriétaire de la base de données, ou « dbo ». Si vous souhaitez utiliser l’authentification Azure Active Directory, vous devez créer un autre administrateur de serveur appelé « administrateur Azure AD », autorisé à gérer les groupes et utilisateurs Active Directory Azure. Cet administrateur peut également effectuer toutes les opérations d’un administrateur de serveur ordinaire. Pour une procédure pas à pas relative à la création d’un administrateur Azure AD pour activer l’authentification Azure Active Directory, consultez [Connexion à la base de données SQL avec l’authentification Azure Active Directory](sql-database-aad-authentication.md) .

Nous vous recommandons, au titre de meilleure pratique, d’utiliser un autre compte pour l’authentification de votre application. Cela vous permet de limiter les autorisations accordées à cette application et de réduire les risques d’activité malveillante, au cas où le code de votre application serait vulnérable à une attaque par injection de code SQL. La méthode recommandée consiste à créer un [utilisateur de base de données à relation contenant-contenu](https://msdn.microsoft.com/library/ff929188), qui permet à votre application de s’authentifier directement auprès de la base de données. Vous pouvez créer un utilisateur de base de données contenu utilisant une authentification SQL en exécutant la commande T-SQL suivante tout en étant connecté à votre base de données utilisateur en tant qu’administrateur de serveur :

```
CREATE USER ApplicationUser WITH PASSWORD = 'strong_password'; -- SQL Authentication
```

Si vous avez créé un administrateur Azure AD, vous pouvez créer un utilisateur de base de données contenu qui utilise une authentification Azure Active Directory en exécutant la commande T-SQL suivante lorsque vous êtes connecté à votre base de données utilisateur en tant que l’administrateur Azure AD :

```
CREATE USER [Azure_AD_principal_name | Azure_AD_group_display_name] FROM EXTERNAL PROVIDER; -- Azure Active Directory Authentication
```

En tout cas, la chaîne de connexion de votre application doit indiquer les informations d’identification de cet utilisateur, plutôt que l’identifiant de connexion de l’administrateur de serveur, pour se connecter à la base de données.

Pour en savoir plus sur l’authentification auprès d’une base de données SQL, voir [Gestion des bases de données et des connexions dans la base de données SQL Microsoft Azure](sql-database-manage-logins.md).

## <a name="authorization"></a>Autorisation
Le terme « autorisation » fait référence aux actions que vous pouvez exécuter dans une base de données SQL Microsoft Azure, actions contrôlées par les permissions et l’appartenance au rôle de votre compte utilisateur. Nous vous recommandons, à titre de meilleure pratique, d’accorder aux utilisateurs des privilèges aussi réduits que possible. La base de données SQL Microsoft Azure facilite la gestion des rôles dans T-SQL :

```
ALTER ROLE db_datareader ADD MEMBER ApplicationUser; -- allows ApplicationUser to read data
ALTER ROLE db_datawriter ADD MEMBER ApplicationUser; -- allows ApplicationUser to write data
```

Le compte d’administrateur du serveur avec lequel vous vous connectez appartient au groupe « db_owner », qui est autorisé à effectuer tout type d’opérations dans la base de données. Enregistrez ce compte pour l’utiliser lors du déploiement des mises à niveau de schéma et d’autres opérations de gestion. Utilisez le compte « ApplicationUser », doté d’autorisations plus limitées, pour vous connecter à la base de données à partir de votre application, en bénéficiant du niveau de privilèges le moins élevé requis par cette dernière.

Il existe d’autres méthodes pour limiter le nombre d’actions que peut réaliser un utilisateur avec la base de données SQL Microsoft Azure :

* Les [rôles de base de données](https://msdn.microsoft.com/library/ms189121) autres que « db_datareader » et « db_datawriter » peuvent être utilisés pour créer des comptes d’utilisateur plus puissants ou des comptes de gestion moins puissants pour votre application.
* Des [autorisations](https://msdn.microsoft.com/library/ms191291) granulaires vous permettent de contrôler les opérations que vous pouvez exécuter sur des colonnes, des tables, des vues, des procédures et d’autres objets individuels dans la base de données.
* Vous pouvez recourir à l’[emprunt d’identité](https://msdn.microsoft.com/library/vstudio/bb669087) et à la [signature de module](https://msdn.microsoft.com/library/bb669102) pour élever le niveau des autorisations temporairement, en toute sécurité.
* [sécurité au niveau des lignes](https://msdn.microsoft.com/library/dn765131) peut être utilisée pour limiter le nombres de lignes accessibles par l’utilisateur.
* [masquage des données](sql-database-dynamic-data-masking-get-started.md) permet de restreindre l’exposition des données sensibles.
* [procédures stockées](https://msdn.microsoft.com/library/ms190782) , vous pouvez limiter le nombre d’actions susceptibles d’être exécutées sur la base de données.

La gestion des bases de données et serveurs logiques à partir du portail Azure Classic et l’utilisation de l’API Azure Resource Manager sont contrôlées par les affectations associées au rôle de votre compte d’utilisateur sur le portail. Pour en savoir plus à ce sujet, voir [Contrôle d’accès en fonction du rôle dans le portail Azure](../active-directory/role-based-access-control-configure.md).

## <a name="encryption"></a>Chiffrement
La base de données SQL Microsoft Azure peut vous aider à protéger vos données en les chiffrant lorsqu’elles sont « au repos » ou stockées dans des fichiers de base de données et des sauvegardes, à l’aide du [chiffrement transparent des données](http://go.microsoft.com/fwlink/?LinkId=526242). Pour chiffrer votre base de données, connectez-vous en tant que propriétaire de la base de données et exécutez la commande suivante :

```
ALTER DATABASE [AdventureWorks] SET ENCRYPTION ON;
```

Il existe d’autres méthodes de chiffrement des données secrètes :

* [chiffrement au niveau des cellules](https://msdn.microsoft.com/library/ms179331.aspx) permet de chiffrer des colonnes spécifiques, voire des cellules de données, avec des clés de chiffrement différentes.
* S’il vous faut un module de sécurité matériel ou si vous devez gérer la hiérarchie des clés de chiffrement de manière centralisée, consultez l’article de blog relatif au [coffre de clés Microsoft Azure avec SQL Server dans une machine virtuelle Azure](http://blogs.technet.com/b/kv/archive/2015/01/12/using-the-key-vault-for-sql-server-encryption.aspx).
* [Toujours chiffré](https://msdn.microsoft.com/library/mt163865.aspx) (dans la version préliminaire) rend le chiffrement transparent pour les applications et permet aux clients de chiffrer les données sensibles dans les applications clientes sans partager les clés de chiffrement avec SQL Database.

## <a name="auditing"></a>Audit
Les fonctions d’audit et de suivi des événements de la base de données peuvent vous aider à assurer la conformité aux normes et à identifier toute activité suspecte. La fonction d’audit de base de données vous permet d’enregistrer les événements survenus dans votre base de données dans un journal d’audit au sein de votre compte Microsoft Azure Storage. Cette fonction s’intègre également dans Microsoft Power BI, afin de faciliter la création d’analyses et de rapports approfondis. Pour en savoir plus, voir [Prise en main de l’audit de base de données SQL](sql-database-auditing-get-started.md).

Threat Detection pour la base de données SQL fournit une couche de sécurité supplémentaire en plus de l’audit. Cet outil vous permet de répondre aux menaces lorsqu’elles se produisent, par le biais d’alertes de sécurité sur les activités anormales. Pour en savoir plus, consultez [Prise en main de Threat Detection pour la base de données SQL](sql-database-threat-detection-get-started.md).  

## <a name="compliance"></a>Conformité
Non seulement la base de données SQL Microsoft Azure propose les fonctions décrites ci-dessus ainsi que des fonctionnalités permettant à votre application à répondre à différentes exigences en matière de conformité de la sécurité, mais elle participe également à des audits réguliers. Elle est certifiée conforme avec de nombreuses normes actuelles. Pour en savoir plus, accédez au [Centre de gestion de la confidentialité Microsoft Azure](https://azure.microsoft.com/support/trust-center/), qui inclut la liste la plus à jour des [certifications de conformité de la base de données SQL](https://azure.microsoft.com/support/trust-center/services/).




<!--HONumber=Dec16_HO2-->


