---
title: "Conseils pour le réglage des performances de SQL Database | Microsoft Docs"
description: "Conseils pour le réglage des performances dans Azure SQL Database par le biais de l’évaluation et de l’amélioration."
services: sql-database
documentationcenter: 
author: v-shysun
manager: felixwu
editor: 
keywords: "réglage des performances de sql, réglage des performances de base de données, conseils pour le réglage des performances de sql, réglage des performances de sql database"
ms.assetid: eb7b3f66-3b33-4e1b-84fb-424a928a6672
ms.service: sql-database
ms.custom: monitor and tune
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/13/2016
ms.author: v-shysun
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: df8b72c18dfaf85de07d0cf575fad0a7ef1935bf


---
# <a name="sql-database-performance-tuning-tips"></a>Conseils pour le réglage des performances de SQL Database
Vous pouvez modifier le [niveau de service](sql-database-service-tiers.md) d’une seule base de données ou augmenter le nombre d’eDTU d’un pool de base de données élastique à tout moment pour améliorer les performances, mais il se peut que vous vouliez identifier des opportunités pour améliorer et optimiser d’abord des performances de requêtes. Des index manquants et des requêtes incorrectement optimisées sont souvent à l’origine de performances de base de données limitées. Cet article fournit des conseils pour le réglage des performances dans SQL Database.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="steps-to-evaluate-and-tune-database-performance"></a>Étapes servant à évaluer et à adapter les performances de base de données
1. Dans le [portail Azure](https://portal.azure.com), cliquez sur **Bases de données SQL**, sélectionnez la base de données, puis utilisez le graphique d’analyse pour rechercher des ressources proches de leur maximum. Consommation de DTU est affichée par défaut. Cliquez sur **Modifier** pour modifier l’intervalle de temps et les valeurs affichées.
2. Utilisez [Analyse des performances des requêtes](sql-database-query-performance.md) pour évaluer les requêtes à l’aide de DTU, puis utilisez [SQL Database Advisor](sql-database-advisor.md) pour consulter les recommandations pour la création et la suppression d’index, le paramétrage des requêtes et la résolution des problèmes de schéma.
3. Vous pouvez utiliser des vues de gestion dynamique (DMV), des événements étendus (Xevents) et le magasin de requêtes dans SSMS pour obtenir des paramètres de performances en temps réel. Consultez la [rubrique de conseils de performances](sql-database-performance-guidance.md) pour accéder à des conseils détaillés de réglage et d’analyse.

    > [AZURE.IMPORTANT] Nous vous recommandons d’utiliser systématiquement la dernière version de Management Studio afin de rester en cohérence avec les mises à jour de Microsoft Azure et Base de données SQL. [Mettre à jour SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).


## <a name="steps-to-improve-database-performance-with-more-resources"></a>Opérations servant à améliorer les performances de base de données avec davantage de ressources
1. Pour les bases de données uniques, vous pouvez [Modifier les niveaux de service](sql-database-scale-up.md) à la demande pour améliorer les performances de la base de données.
2. S’il existe plusieurs bases de données, envisagez d’utiliser des [pools de base de données élastiques](sql-database-elastic-pool-guidance.md) pour une mise à l’échelle automatique des ressources.




<!--HONumber=Nov16_HO3-->


