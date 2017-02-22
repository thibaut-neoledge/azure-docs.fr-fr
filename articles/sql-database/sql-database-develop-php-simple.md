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
ms.date: 02/03/2017
ms.author: meetb
translationtype: Human Translation
ms.sourcegitcommit: 1f1c6c89c492d18e0678fa4650b6c5744dc9f7d1
ms.openlocfilehash: e1c3e7e0f6ca097e3ee41995defe5c1df666d39e


---
# <a name="connect-to-sql-database-by-using-php-on-windows"></a>Connexion à SQL Database à l'aide de PHP sur Windows
[!INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]

Cette rubrique montre comment vous connecter à Azure SQL Database à partir d'une application cliente écrite en PHP qui s'exécute sur Windows.

## <a name="step-1--configure-development-environment"></a>Étape 1 : Configurer l’environnement de développement
[Configurer l’environnement de développement pour le développement PHP](https://docs.microsoft.com/sql/connect/php/step-1-configure-development-environment-for-php-development/)

## <a name="step-2-create-a-sql-database"></a>Étape 2 : créer une base de données SQL
Consultez la [page de prise en main](sql-database-get-started.md) pour apprendre à créer un exemple de base de données.  Il est important que vous suiviez le guide pour créer un **modèle de base de données AdventureWorks**. Les exemples ci-dessous fonctionnent uniquement avec le **schéma AdventureWorks**.

## <a name="step-3-get-connection-details"></a>Étape 3 : obtenir les informations de connexion
[!INCLUDE [sql-database-include-connection-string-details-20-portalshots](../../includes/sql-database-include-connection-string-details-20-portalshots.md)]

## <a name="step-4-run-sample-code"></a>Étape 4 : Exécuter l’exemple de code
* [Preuve de concept sur la connexion à SQL avec PHP](https://docs.microsoft.com/sql/connect/php/step-3-proof-of-concept-connecting-to-sql-using-php/)
* [Se connecter de façon robuste à SQL avec PHP](https://docs.microsoft.com/sql/connect/php/step-4-connect-resiliently-to-sql-with-php/)

## <a name="next-steps"></a>Étapes suivantes
* Consultez la [Vue d’ensemble du développement de base de données SQL](sql-database-develop-overview.md)
* Plus d’informations sur le [pilote PHP Microsoft pour SQL Server](https://docs.microsoft.com/sql/connect/php/microsoft-php-driver-for-sql-server/)
* Pour plus d'informations sur l'installation et l'utilisation de PHP, consultez [Accès aux bases de données du serveur SQL avec PHP](http://social.technet.microsoft.com/wiki/contents/articles/1258.accessing-sql-server-databases-from-php.aspx).

## <a name="additional-resources"></a>Ressources supplémentaires
* [Modèles de conception pour les applications SaaS mutualisées avec Base de données SQL Azure](sql-database-design-patterns-multi-tenancy-saas-applications.md)
* Explorez toutes les [fonctionnalités de la base de données SQL](https://azure.microsoft.com/services/sql-database/)




<!--HONumber=Feb17_HO1-->


