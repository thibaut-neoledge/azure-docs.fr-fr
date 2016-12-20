---
title: "Analyse des performances d’une base de données SQL Azure | Microsoft Docs"
description: "La base de données SQL Azure fournit des outils de performances pour vous aider à identifier les zones susceptibles d’améliorer les performances actuelles des requêtes."
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: monicar
ms.assetid: a60b75ac-cf27-4d73-8322-ee4d4c448aa2
ms.service: sql-database
ms.custom: monitor and tune
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 07/19/2016
ms.author: sstein
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 38416deeb583c78bca9ded2e514bf3ddff716c52


---
# <a name="sql-database-performance-insight"></a>Informations sur les performances des bases de données SQL
La base de données SQL Azure fournit des outils de performances pour vous aider à identifier et à améliorer les performances de vos bases de données en fournissant des recommandations et des actions de paramétrage intelligentes. 

1. Accédez à votre base de données dans le [portail Azure](http://portal.azure.com) et cliquez sur **Tous les paramètres** > **Performances** > **Vue d’ensemble** pour ouvrir la page **Performances**. 
2. Cliquez sur **Recommandations** pour ouvrir [SQL Database Advisor](#sql-database-advisor), puis cliquez sur **Requêtes** pour ouvrir [Analyse des performances des requêtes](#query-performance-insight).
   
    ![Afficher les performances](./media/sql-database-performance/entries.png)

## <a name="performance-overview"></a>Vue d'ensemble des performances
En cliquant sur **Vue d’ensemble** ou sur la vignette **Performances**, vous accédez au tableau de bord des performances de votre base de données. Cette vue fournit un résumé des performances de votre base de données et vous aide à optimiser les performances et à résoudre les problèmes. 

![Performances](./media/sql-database-performance/performance.png)

* La vignette **Recommandations** fournit une décomposition des recommandations de paramétrage de votre base de données (les 3 premières recommandations sont affichées s’il en existe plus). Cette vignette vous amène à **SQL Database Advisor**. 
* La vignette **Activité de paramétrage** fournit un résumé des actions de paramétrage en cours et terminées pour votre base de données, ce qui vous donne un aperçu rapide de l’historique des activités de paramétrage. Cliquez sur cette vignette pour afficher l'historique de paramétrage complet de votre base de données.
* La vignette **Paramétrage automatique** affiche la configuration de paramétrage automatique de votre base de données (les actions de paramétrage sont configurées pour être automatiquement appliquées à votre base de données). Cliquez sur cette vignette pour ouvrir la boîte de dialogue de configuration de l'automatisation.
* La vignette **Requêtes de base de données** affiche le résumé des performances de requête de votre base de données (utilisation DTU globale et requêtes les plus consommatrices de ressources). Cette vignette vous amène à **Query Performance Insight**.

## <a name="sql-database-advisor"></a>SQL Database Advisor
[SQL Database Advisor](sql-database-advisor.md) fournit des recommandations de paramétrage intelligent qui peuvent aider à améliorer les performances de votre base de données. 

* Recommandations sur les index à créer ou supprimer (et une option pour appliquer automatiquement les recommandations d'index, sans intervention de l'utilisateur, et annuler automatiquement les recommandations qui ont un impact négatif sur les performances).
* Recommandations en cas de problèmes de schéma dans la base de données.
* Recommandations quand les requêtes peuvent tirer parti des requêtes paramétrables.

## <a name="query-performance-insight"></a>Query Performance Insight
[Query Performance Insight](sql-database-query-performance.md) vous permet de passer moins de temps à résoudre les problèmes de performances des bases de données en fournissant :

* Une meilleure compréhension de la consommation des ressources des bases de données (DTU). 
* Les requêtes principales consommatrices d’UC peuvent être réglées pour améliorer les performances. 
* La possibilité d’examiner les détails d’une requête. 

## <a name="additional-resources"></a>Ressources supplémentaires
* [Guide des performances de base de données SQL Azure pour les bases de données uniques](sql-database-performance-guidance.md)
* [Quand utiliser un pool de base de données élastique ?](sql-database-elastic-pool-guidance.md)




<!--HONumber=Nov16_HO3-->


