---
title: "Résoudre les problèmes de performances et optimiser votre base de données | Microsoft Docs"
description: "Appliquer des recommandations en matière de performances à votre SQL Database et apprendre à obtenir des informations sur les performances des requêtes s’exécutant sur votre base de données"
metakeywords: azure sql database performance monitoring recommendation
services: sql-database
documentationcenter: 
manager: jhubbard
author: jan-eng
ms.assetid: 
ms.service: sql-database
ms.custom: mvc,monitor & tune
ms.workload: sql-database
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/07/2017
ms.author: janeng
ms.translationtype: Human Translation
ms.sourcegitcommit: db18dd24a1d10a836d07c3ab1925a8e59371051f
ms.openlocfilehash: f9ae96cdc80c347593f229cb2fce3f2d4d8e7caf
ms.contentlocale: fr-fr
ms.lasthandoff: 06/15/2017


---
<a id="troubleshoot-performance-issues-and-optimize-your-database" class="xliff"></a>

# Résoudre les problèmes de performances et optimiser votre base de données

Des index manquants et des requêtes incorrectement optimisées sont souvent à l’origine de performances de base de données limitées. Ce didacticiel vous apprend à effectuer les opérations suivantes :
> [!div class="checklist"]
> * Examiner, appliquer et annuler des recommandations en matière d’amélioration des performances
> * Rechercher les requêtes utilisant beaucoup de ressources
> * Rechercher les requêtes à exécution longue

> Vous avez besoin d’une charge de travail continue sur une base de données rencontrant des problèmes de performances (p. ex. index manquant) pour obtenir une recommandation.
>

<a id="log-in-to-the-azure-portal" class="xliff"></a>

## Connectez-vous au portail Azure.

Connectez-vous au [portail Azure](https://portal.azure.com/).

<a id="review-and-apply-a-recommendation" class="xliff"></a>

## Examiner et appliquer une recommandation

Suivez les étapes ci-dessous pour appliquer une recommandation du système à votre base de données :

1. Cliquez sur le menu **Recommandations sur les performances** dans le panneau de la base de données.

    ![recommandation sur les performances](./media/sql-database-performance-tutorial/perf_recommendations.png)

2. Sélectionnez une recommandation active dans la liste des recommandations. Dans cet exemple, Créer un index.

    ![sélectionner une recommandation](./media/sql-database-performance-tutorial/create_index.png)

3. Appliquez la recommandation en cliquant sur le bouton **Appliquer**. Si vous le souhaitez, examinez les détails de la recommandation et consultez le script T-SQL à exécuter en cliquant sur le bouton **Afficher le script**.

    ![appliquer une recommandation](./media/sql-database-performance-tutorial/apply.png)

4. [Facultatif] Activez le réglage automatique pour appliquer automatiquement des recommandations.

    ![réglage automatique](./media/sql-database-performance-tutorial/auto_tuning.png)

<a id="revert-a-recommendation" class="xliff"></a>

## Annuler une recommandation

Database Advisor surveille chaque recommandation implémentée. Si une recommandation n’améliore pas la charge de travail, elle est automatiquement annulée. Vous avez la possibilité d’annuler manuellement une recommandation, mais cela n’est pas nécessaire la plupart du temps. Pour annuler une recommandation :

1. Accédez au menu des recommandations sur les performances et sélectionnez l’une des recommandations appliquées.

    ![sélectionner une recommandation](./media/sql-database-performance-tutorial/select.png)

2. Dans la vue Détails, cliquez sur **Revenir**.

    ![annuler une recommandation](./media/sql-database-performance-tutorial/revert.png)

<a id="find-the-query-that-consumes-the-most-resources" class="xliff"></a>

## Rechercher la requête qui consomme le plus de ressources

Suivez les étapes ci-dessous pour rechercher la requête qui consomme le plus de ressources :

1. Cliquez sur le menu **Query Performance Insight** dans le panneau de la base de données.

    ![analyse des requêtes](./media/sql-database-performance-tutorial/query_perf_insights.png)

2. Sélectionnez un type de ressource.

    ![analyse des requêtes](./media/sql-database-performance-tutorial/select_resource_type.png)

3. Sélectionnez la première requête dans la table.

    ![analyse des requêtes](./media/sql-database-performance-tutorial/select_query.png)

4. Examinez les détails de la requête.

    ![analyse des requêtes](./media/sql-database-performance-tutorial/query_details.png)

<a id="find-the-longest-running-query" class="xliff"></a>

## Rechercher la requête dont l’exécution est la plus longue

1. Accédez à Query Performance Insight et sélectionnez l’onglet **Requêtes longues**.

    ![analyse des requêtes](./media/sql-database-performance-tutorial/long_running.png)

3. Sélectionnez la première requête dans la table.

    ![analyse des requêtes](./media/sql-database-performance-tutorial/select_first_query.png)

4. Examinez les détails de la requête.

    ![analyse des requêtes](./media/sql-database-performance-tutorial/review_query_details.png)



<a id="next-steps" class="xliff"></a>

## Étapes suivantes 
Des index manquants et des requêtes incorrectement optimisées sont souvent à l’origine de performances de base de données limitées. Dans ce didacticiel, vous avez appris à effectuer les opérations suivantes :
> [!div class="checklist"]
> * Examiner, appliquer et annuler des recommandations en matière d’amélioration des performances
> * Rechercher les requêtes utilisant beaucoup de ressources
> * Rechercher les requêtes à exécution longue

[Conseils pour le réglage des performances de SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-troubleshoot-performance)

