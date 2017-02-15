---
title: "Connexion à une base de données SQL avec PHP sous Windows | Microsoft Docs"
description: "Présente un exemple de programme PHP qui se connecte à la base de données SQL Azure à partir d&quot;un client Windows et fournit des liens vers les composants logiciels nécessaires requis par le client."
services: sql-database
documentationcenter: 
author: meet-bhagdev
manager: jhubbard
editor: 
ms.assetid: 4e71db4a-a22f-4f1c-83e5-4a34a036ecf3
ms.service: sql-database
ms.custom: development
ms.workload: drivers
ms.tgt_pltfrm: na
ms.devlang: php
ms.topic: article
ms.date: 10/03/2016
ms.author: meetb
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 162c89b07a0bc690600f0cc59e3f4c70e02eac91


---
# <a name="connect-to-sql-database-by-using-php-on-windows"></a>Connexion à SQL Database à l'aide de PHP sur Windows
[!INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]

Cette rubrique montre comment vous connecter à Azure SQL Database à partir d'une application cliente écrite en PHP qui s'exécute sur Windows.

## <a name="step-1--configure-development-environment"></a>Étape 1 : Configurer l’environnement de développement
[Configurer l’environnement de développement pour le développement PHP](https://msdn.microsoft.com/library/mt720663.aspx)

## <a name="step-2-create-a-sql-database"></a>Étape 2 : créer une base de données SQL
Consultez la [page de prise en main](sql-database-get-started.md) pour apprendre à créer un exemple de base de données.  Il est important que vous suiviez le guide pour créer un **modèle de base de données AdventureWorks**. Les exemples ci-dessous fonctionnent uniquement avec le **schéma AdventureWorks**.

## <a name="step-3-get-connection-details"></a>Étape 3 : obtenir les informations de connexion
[!INCLUDE [sql-database-include-connection-string-details-20-portalshots](../../includes/sql-database-include-connection-string-details-20-portalshots.md)]

## <a name="step-4-run-sample-code"></a>Étape 4 : Exécuter l’exemple de code
* [Preuve de concept sur la connexion à SQL avec PHP](https://msdn.microsoft.com/library/mt720665.aspx)
* [Se connecter de façon robuste à SQL avec PHP](https://msdn.microsoft.com/library/mt720667.aspx)

## <a name="next-steps"></a>Étapes suivantes
* Consultez la [Vue d’ensemble du développement de base de données SQL](sql-database-develop-overview.md)
* Plus d’informations sur le [pilote PHP Microsoft pour SQL Server](https://msdn.microsoft.com/library/dn865013.aspx)
* Pour plus d'informations sur l'installation et l'utilisation de PHP, consultez [Accès aux bases de données du serveur SQL avec PHP](http://social.technet.microsoft.com/wiki/contents/articles/1258.accessing-sql-server-databases-from-php.aspx).

## <a name="additional-resources"></a>Ressources supplémentaires
* [Modèles de conception pour les applications SaaS mutualisées avec Base de données SQL Azure](sql-database-design-patterns-multi-tenancy-saas-applications.md)
* Explorez toutes les [fonctionnalités de la base de données SQL](https://azure.microsoft.com/services/sql-database/)




<!--HONumber=Nov16_HO3-->


