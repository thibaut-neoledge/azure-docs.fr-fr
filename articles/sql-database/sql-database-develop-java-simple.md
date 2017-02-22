---
title: "Connexion à une base de données SQL à l’aide de Java avec JDBC sous Windows | Documents Microsoft"
description: "Cette rubrique présente un exemple de code Java que vous pouvez utiliser pour vous connecter à la base de données SQL Azure. L&quot;exemple utilise JDBC et s&quot;exécute sur un ordinateur client Windows."
services: sql-database
documentationcenter: 
author: LuisBosquez
manager: jhubbard
editor: genemi
ms.assetid: 08fc49b1-cd48-4dcc-a293-ff22a4d2d62c
ms.service: sql-database
ms.custom: development
ms.workload: drivers
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: article
ms.date: 02/03/2017
ms.author: lbosq
translationtype: Human Translation
ms.sourcegitcommit: 1f1c6c89c492d18e0678fa4650b6c5744dc9f7d1
ms.openlocfilehash: aafc89ca958e4bdc2ba52bf1a7d379e3a4cddf1c


---
# <a name="connect-to-sql-database-by-using-java-with-jdbc-on-windows"></a>Connexion à la base de données SQL à l’aide de Java avec JDBC sous Windows
[!INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]

Cette rubrique présente un exemple de code Java que vous pouvez utiliser pour vous connecter à la base de données SQL Azure. L'exemple Java s'appuie sur le Kit de développement Java (JDK) version 1.8. L'exemple se connecte à une base de données SQL Azure en utilisant le pilote JDBC.

## <a name="step-1--configure-development-environment"></a>Étape 1 : Configurer l’environnement de développement
[Configurer l’environnement de développement pour le développement Java](https://docs.microsoft.com/sql/connect/jdbc/step-1-configure-development-environment-for-java-development/)

## <a name="step-2-create-a-sql-database"></a>Étape 2 : créer une base de données SQL
Consultez la [page de prise en main](sql-database-get-started.md) pour apprendre à créer une base de données.  

## <a name="step-3-get-connection-string"></a>Étape 3 : obtenir la chaîne de connexion
[!INCLUDE [sql-database-include-connection-string-jdbc-20-portalshots](../../includes/sql-database-include-connection-string-jdbc-20-portalshots.md)]

> [!NOTE]
> Si vous utilisez le pilote JTDS JDBC, vous devez ajouter « ssl=require » à l’URL de la chaîne de connexion et vous devez définir l’option suivante pour l’environnement JVM : « -Djsse.enableCBCProtection=false ». Cette option JVM désactive un correctif pour un problème de sécurité. Par conséquent, assurez-vous que vous savez quels risques vous prenez avant de définir cette option.
> 
> 

## <a name="step-4-run-sample-code"></a>Étape 4 : Exécuter l’exemple de code
* [Preuve de concept sur la connexion à SQL avec Java](https://docs.microsoft.com/sql/connect/jdbc/step-3-proof-of-concept-connecting-to-sql-using-java/)

## <a name="next-steps"></a>Étapes suivantes
* Visitez le [Centre de développement Java](/develop/java/).
* Consultez la [Vue d’ensemble du développement de base de données SQL](sql-database-develop-overview.md)
* Plus d’informations sur le [pilote JDBC Microsoft pour SQL Server](https://docs.microsoft.com/sql/connect/jdbc/microsoft-jdbc-driver-for-sql-server/)

## <a name="additional-resources"></a>Ressources supplémentaires
* [Modèles de conception pour les applications SaaS mutualisées avec Base de données SQL Azure](sql-database-design-patterns-multi-tenancy-saas-applications.md)
* Explorez toutes les [fonctionnalités de la base de données SQL](https://azure.microsoft.com/services/sql-database/)




<!--HONumber=Feb17_HO1-->


