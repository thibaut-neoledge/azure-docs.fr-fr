---
title: "Vue d’ensemble du développement de base de données SQL | Microsoft Docs"
description: "En savoir plus sur les bibliothèques de connectivité disponibles et les meilleures pratiques pour les applications qui utilisent une connexion à la base de données SQL."
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: genemi
ms.assetid: 67c02204-d1bd-4622-acce-92115a7cde03
ms.service: sql-database
ms.custom: development
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/17/2016
ms.author: sstein
translationtype: Human Translation
ms.sourcegitcommit: 72a20c51416cd3022e2a749c51f25c4b3d9268df
ms.openlocfilehash: 88b8076e740f80154e410050cd55cd2fa4783b38


---
# <a name="sql-database-application-development-overview"></a>Vue d’ensemble du développement de base de données SQL
Cet article explique les aspects de base qu’un développeur doit prendre en compte lors de l’écriture de code permettant d’établir une connexion à la base de données SQL Azure.

> [!TIP]
> Pour savoir comment créer un serveur ou un pare-feu basé sur un serveur, afficher les propriétés d’un serveur, se connecter à l’aide de SQL Server Management Studio, interroger la base de données master, créer un exemple de base de données et une base de données vide, demander les propriétés d’une base de données, se connecter à l’aide de SQL Server Management Studio et interroger l’exemple de base de données, consultez le didacticiel [Prise en main des serveurs Azure SQL Database, des bases de données et des règles de pare-feu à l’aide du portail Azure et de SQL Server Management Studio](sql-database-get-started.md).
>

## <a name="language-and-platform"></a>Langage et plateforme
Plusieurs exemples de code sont à votre disposition pour divers langages et plateformes de programmation. Vous trouverez des liens vers des exemples de code dans les articles suivants : 

* Pour en savoir plus, voir [Bibliothèques de connexions pour SQL Database et SQL Server](sql-database-libraries.md)

## <a name="resource-limitations"></a>Limitations des ressources
La base de données SQL Azure gère les ressources disponibles pour une base de données à l’aide de deux mécanismes différents : la gouvernance des ressources et l’application de limites.

* Pour en savoir plus, voir [Limites de ressources de base de données SQL Azure](sql-database-resource-limits.md)

## <a name="security"></a>Sécurité
La base de données SQL Azure fournit des ressources permettant de limiter l’accès, de protéger les données et de surveiller les activités sur une base de données SQL.

* Pour en savoir plus, voir [Sécurisation de votre base de données SQL](sql-database-security.md)

## <a name="authentication"></a>Authentification
* La base de données SQL Azure prend en charge les utilisateurs et les connexions de l’authentification SQL Server, ainsi que les utilisateurs et les connexions de [l’authentification Azure Active Directory](sql-database-aad-authentication.md) .
* Vous devez définir une base de données spécifique, au lieu de la base de données *MASTER* par défaut.
* Vous ne pouvez pas utiliser l’instruction **USE myDatabaseName;** Transact-SQL sur une base de données SQL pour basculer vers une autre base de données.
* Pour en savoir plus, consultez [Sécurité SQL Database : gérer la sécurité d’accès et de connexion aux bases de données](sql-database-manage-logins.md)

## <a name="resiliency"></a>Résilience
Lorsqu’une erreur temporaire se produit au moment de la connexion à la base de données SQL, votre code doit exécuter une nouvelle tentative d’appel.  Nous vous recommandons d’utiliser une logique de nouvelle tentative basée sur une logique d’interruption, afin d’éviter que la base de données SQL ne soit inondée de tentatives simultanées de plusieurs clients.

* Exemples de code : pour obtenir des exemples de code qui illustrent la logique de nouvelle tentative, consultez les exemples disponibles dans le langage de votre choix sur la page [Bibliothèques de connexions pour SQL Database et SQL Server](sql-database-libraries.md)
* Pour en savoir plus, consultez [Messages d’erreur pour les programmes clients SQL Database](sql-database-develop-error-messages.md)

## <a name="managing-connections"></a>Gestion des connexions
* Dans votre logique de connexion client, définissez le délai d’expiration sur 30 secondes.  La valeur par défaut de 15 secondes est trop courte pour les connexions qui reposent sur Internet.
* Si vous utilisez un [pool de connexions](http://msdn.microsoft.com/library/8xx3tyca.aspx), veillez à fermer la connexion dès que votre programme ne l’utilise plus activement et qu’il ne se prépare pas à le réutiliser.

## <a name="network-considerations"></a>Remarques relatives au réseau
* Assurez-vous que le pare-feu de l’ordinateur qui héberge votre programme client autorise les communications TCP sortantes sur le port 1433.  Pour en savoir plus, consultez [Configurer un pare-feu sur une base de données SQL Azure à l’aide du portail Azure](sql-database-configure-firewall-settings.md)
* Si votre programme client se connecte à la base de données SQL V12 pendant que votre client s’exécute sur une machine virtuelle Azure, vous devez ouvrir certaines plages de ports sur la machine virtuelle. Pour en savoir plus, consultez [Ports au-delà de 1433 pour ADO.NET 4.5 et SQL Database V12](sql-database-develop-direct-route-ports-adonet-v12.md)
* Parfois, les connexions clientes à Azure SQL Database V12 ignorent le proxy et interagissent directement avec la base de données. Les ports autres que le port 1433 deviennent importants. Pour plus d’informations, consultez [Ports au-delà de 1433 pour ADO.NET 4.5 et SQL Database V12](sql-database-develop-direct-route-ports-adonet-v12.md)

## <a name="data-sharding-with-elastic-scale"></a>Partitionnement de données avec la mise à l’échelle élastique
La mise à l’échelle élastique simplifie le processus d’évolutivité horizontale. 

* [Modèles de conception pour les applications SaaS mutualisées avec Base de données SQL Azure](sql-database-design-patterns-multi-tenancy-saas-applications.md)
* [Routage dépendant des données](sql-database-elastic-scale-data-dependent-routing.md)
* [Prise en main de l'infrastructure élastique d’Azure SQL Database (version préliminaire)](sql-database-elastic-scale-get-started.md)

## <a name="next-steps"></a>Étapes suivantes
Explorez toutes les [fonctionnalités de la base de données SQL](https://azure.microsoft.com/services/sql-database/).




<!--HONumber=Dec16_HO3-->


