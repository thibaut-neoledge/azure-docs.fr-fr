---
title: "Se connecter à SQL Database à l’aide de Ruby | Microsoft Docs"
description: "Offre un exemple de code Ruby que vous pouvez exécuter pour vous connecter à la base de données SQL Azure."
services: sql-database
documentationcenter: 
author: ajlam
manager: jhubbard
editor: 
ms.assetid: 94fec528-58ba-4352-ba0d-25ae4b273e90
ms.service: sql-database
ms.custom: development
ms.workload: drivers
ms.tgt_pltfrm: na
ms.devlang: ruby
ms.topic: article
ms.date: 02/03/2017
ms.author: andrela
translationtype: Human Translation
ms.sourcegitcommit: 1f1c6c89c492d18e0678fa4650b6c5744dc9f7d1
ms.openlocfilehash: cbd13711911b67ace7ef43676b4c52aa93744bcb
ms.lasthandoff: 02/07/2017


---
# <a name="connect-to-sql-database-by-using-ruby"></a>Connexion à SQL Database à l’aide de Ruby
[!INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]

Cette rubrique montre comment se connecter à une base de données Azure SQL Database et l’interroger à l’aide de Ruby. Vous pouvez exécuter cet exemple à partir des plateformes Windows, Ubuntu Linux ou Mac.

## <a name="step-1-configure-development-environment"></a>Étape 1 : configurer l’environnement de développement
[Prerequisites for using the TinyTDS Ruby Driver for SQL Server (Configuration requise pour l’utilisation du pilote Ruby TinyTDS pour SQL Server)](https://docs.microsoft.com/sql/connect/ruby/step-1-configure-development-environment-for-ruby-development/)

## <a name="step-2-create-a-sql-database"></a>Étape 2 : créer une base de données SQL
Consultez la [page de prise en main](sql-database-get-started.md) pour apprendre à créer un exemple de base de données.  Il est important que vous suiviez le guide pour créer un **modèle de base de données AdventureWorks**. Les exemples ci-dessous fonctionnent uniquement avec le **schéma AdventureWorks**.

## <a name="step-3-get-connection-details"></a>Étape 3 : obtenir les informations de connexion
[!INCLUDE [sql-database-include-connection-string-details-20-portalshots](../../includes/sql-database-include-connection-string-details-20-portalshots.md)]

## <a name="step-4-run-sample-code"></a>Étape 4 : Exécuter l’exemple de code
[Preuve de concept sur la connexion à SQL à l’aide de Ruby](https://docs.microsoft.com/sql/connect/ruby/step-3-proof-of-concept-connecting-to-sql-using-ruby/)

## <a name="next-steps"></a>Étapes suivantes
* Consultez la [Vue d’ensemble du développement de base de données SQL](sql-database-develop-overview.md)
* Plus d’informations sur le [pilote Ruby Microsoft pour SQL Server](https://docs.microsoft.com/sql/connect/ruby/ruby-driver-for-sql-server/)

## <a name="additional-resources"></a>Ressources supplémentaires
* [Modèles de conception pour les applications SaaS mutualisées avec Base de données SQL Azure](sql-database-design-patterns-multi-tenancy-saas-applications.md)
* Explorez toutes les [fonctionnalités de la base de données SQL](https://azure.microsoft.com/services/sql-database/)


