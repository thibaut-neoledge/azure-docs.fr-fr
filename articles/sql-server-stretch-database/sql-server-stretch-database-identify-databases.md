---
title: "Identifier des bases de données et des tables pour Stretch Database en exécutant Stretch Database Advisor | Microsoft Docs"
description: "Découvrez comment identifier les bases de données et les tables candidates pour Stretch Database."
services: sql-server-stretch-database
documentationcenter: 
author: douglaslMS
manager: jhubbard
editor: 
ms.assetid: 9253c5a2-f135-4782-95ec-8ff39454c2c0
ms.service: sql-server-stretch-database
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/05/2017
ms.author: douglasl
translationtype: Human Translation
ms.sourcegitcommit: 47b666a7f2178daebc1b2b442fd1bb8cf31e40bb
ms.openlocfilehash: 3aef66c7b80ee356faedcaaadacd15aea3438714


---
# <a name="identify-databases-and-tables-for-stretch-database-by-running-stretch-database-advisor"></a>Identifier des bases de données et des tables pour Stretch Database en exécutant Stretch Database Advisor
Pour identifier les bases de données candidates pour Stretch Database Advisor, téléchargez SQL Server 2016 Upgrade Advisor et exécutez Stretch Database Advisor. Stretch Database Advisor identifie également les problèmes de blocage.

## <a name="download-and-install-upgrade-advisor"></a>Télécharger et installer le Conseiller de mise à niveau
Téléchargez et installez le Conseiller de mise à niveau [ici](http://go.microsoft.com/fwlink/?LinkID=613421). Cet outil n’est pas inclus sur le support d’installation de SQL Server.

## <a name="run-the-stretch-database-advisor"></a>Exécuter Stretch Database Advisor
1. Exécutez le Conseiller de mise à niveau
2. Sélectionnez **Scenarios** (Scénarios), puis **RUN STRETCH DATABASE ADVISOR** (Exécuter Stretch Database Advisor).
3. Dans le panneau **Run Stretch Database Advisor** (Exécuter Stretch Database Advisor), cliquez sur **SELECT DATABASES TO ANALYZE** (Sélectionner les bases de données à analyser).
4. Dans le panneau **Sélectionner des bases de données** , entrez ou sélectionnez le nom du serveur et les informations d’authentification. Cliquez sur **Connecter**.
5. Une liste des bases de données sur le serveur sélectionné s’affiche. Sélectionnez les bases de données que vous souhaitez analyser. Cliquez sur **Sélectionner**.
6. Dans le panneau **Run Stretch Database Advisor** (Exécuter Stretch Database Advisor), cliquez sur **Run** (Exécuter).  L’analyse s’exécute.

## <a name="review-the-results"></a>Passer en revue les résultats.
1. Une fois l’analyse terminée, dans le panneau **Analyzed databases** (Bases de données analysées), sélectionnez l’une des bases de données analysées pour afficher le panneau **Analysis results** (Résultats de l’analyse).
   
   Le panneau **Analysis results** (Résultats de l’analyse) répertorie les tables recommandées de la base de données qui correspondent aux critères de recommandation par défaut.
2. Dans la liste des tables du panneau **Analysis results** (Résultats de l’analyse), sélectionnez l’une des tables recommandées pour afficher le panneau **Table results** (Résultats de la table).
   
   En cas de problèmes de blocage, ceux-ci sont répertoriés dans le panneau **Table results** (Résultats de la table) de la table sélectionnée. Pour plus d’informations sur les problèmes de blocage détectés par Stretch Database Advisor, consultez [Limites de Stretch Database](sql-server-stretch-database-limitations.md).
3. Dans la liste des problèmes de blocage dans le panneau **Table results** (Résultats de la table), sélectionnez l’un des problèmes pour afficher plus d’informations sur celui-ci et les mesures d’atténuation proposées. Implémentez la solution suggérée si vous souhaitez configurer la table sélectionnée pour Stretch Database.

## <a name="next-step"></a>Étape suivante
Activer Stretch Database

* Pour activer Stretch Database sur une **base de données**, consultez [Activer Stretch Database pour une base de données](sql-server-stretch-database-enable-database.md).
* Pour activer Stretch Database sur une autre **table**lorsque Stretch est déjà activé sur la base de données, consultez [Activer Stretch Database pour une table](sql-server-stretch-database-enable-table.md).

## <a name="see-also"></a>Voir aussi
[Limites de Stretch Database](sql-server-stretch-database-limitations.md)

[Activer Stretch Database pour une base de données](sql-server-stretch-database-enable-database.md)

[Activer Stretch Database pour une table](sql-server-stretch-database-enable-table.md)




<!--HONumber=Jan17_HO1-->


