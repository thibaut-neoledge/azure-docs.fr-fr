---
title: "Recommandations sur les niveaux tarifaires d’Azure SQL Database"
description: "Lorsque vous modifiez les niveaux tarifaires dans le portail Azure, vous pouvez consulter les recommandations fournies, notamment le niveau le mieux adapté à l’exécution de la charge de travail d’une base de données SQL Azure existante. Les niveaux tarifaires décrivent les niveaux de service et de performances d’une base de données SQL."
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: monicar
ms.assetid: 265beec9-7478-4abd-9ab5-4094e2e75fe4
ms.service: sql-database
ms.custom: v11
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 08/08/2016
ms.author: sstein
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 7fcbdd70d4145dfc603567163bee7b904a724614


---
# <a name="sql-database-pricing-tier-recommendations"></a>Recommandations relatives aux niveaux tarifaires des bases de données SQL
 Les recommandations sur les niveaux tarifaires proposent le niveau de service et de performances le mieux adapté à l’exécution de la charge de travail d’une base de données SQL Azure existante.

> [!NOTE]
> Ces recommandations sont disponibles uniquement pour les bases de données Web et Business et les pools de bases de données élastiques. Elles sont disponibles uniquement sur le [portail Azure](https://portal.azure.com/).
>
>

Obtenez des recommandations en termes de niveau tarifaire lors des tâches suivantes :

* [Modifier les niveaux de service et de performances (niveau tarifaire) d’une base de données SQL](sql-database-scale-up.md)
* [Mise à niveau d’un serveur SQL Azure vers la version 12](sql-database-upgrade-server-portal.md)
* Accédez à votre serveur V12. Voir [Recommandations relatives aux niveaux tarifaires des bases de données SQL](sql-database-service-tier-advisor.md).
* [Créer un pool de bases de données élastiques](sql-database-elastic-pool.md#which-databases-go-in-a-pool)

## <a name="overview"></a>Vue d'ensemble
Le service SQL Database analyse les performances et les fonctionnalités requises en évaluant un historique de l’utilisation des ressources pour une base de données SQL. Par ailleurs, le niveau de service minimum acceptable est déterminé en fonction de la taille de la base de données et des fonctionnalités de [continuité d’activité](sql-database-business-continuity.md) activées.

Ces informations sont analysées ; ensuite, le système recommande le niveau de service et de performances le plus adapté à l’exécution d’une charge de travail classique d’une base de données et la gestion de son ensemble de fonctionnalités actuel.

* Le service examine les données historiques sur la période précédente de 15 à 30 jours (utilisation des ressources, taille des bases de données et activité de ces dernières) et effectue une comparaison entre la quantité de ressources utilisée et les limites réelles des niveaux de service et de performances actuellement disponibles.
* Les données sont analysées toutes les 15 secondes. Le jeu de résultats de chaque intervalle est classé, afin de déterminer le niveau de performances et de service existant le mieux adapté à la gestion de la charge de travail de ce jeu de résultats.
* Ces échantillons de 15 secondes sont ensuite regroupés au sein de l’analyse sur 15-30 jours, plus étendue. Le système recommande ensuite le niveau de service et de performances susceptible de gérer le plus efficacement 95 % de la charge de travail d’historique.

### <a name="recommendations"></a>Recommandations
Selon l’utilisation de votre base de données, vous pouvez actuellement rencontrer 2 catégories de recommandations :

| Recommandation | Description |
|:--- |:--- |
| Mise à niveau |Mise à niveau vers un nouveau niveau. |
| Non disponible |Une base de données requiert une charge de travail minimale, ou environ 35 jours d’activité. Il n’existe pas suffisamment de données pour fournir une recommandation valide. |

## <a name="getting-pricing-tier-recommendations"></a>Obtenir des recommandations sur les niveaux tarifaires
Pour accéder à des recommandations sur les niveaux tarifaires, sélectionnez une base de données « Web ou Business » existante, cliquez sur **Tous les paramètres**, puis cliquez sur **Niveau tarifaire (mise à l’échelle de DTU)**. (Des recommandations sur les niveaux tarifaires sont également disponibles quand vous [mettez à niveau Azure SQL Server vers la version 12](sql-database-upgrade-server-portal.md).)

1. Connectez-vous au [portail Azure](https://portal.azure.com/).
2. Cliquez sur **PARCOURIR** > **Bases de données SQL**.
3. Dans le panneau **Bases de données SQL** , cliquez sur la base de données pour laquelle vous voulez afficher une recommandation :

    ![Sélectionner la base de données][1]
4. Dans le panneau de la base de données, sélectionnez **Tous les paramètres**, puis sélectionnez **Niveau tarifaire (mise à l’échelle de DTU)**.
5. Dans la fenêtre **Niveaux tarifaires recommandés** qui s’ouvre, cliquez sur le niveau recommandé, puis cliquez sur le bouton **Sélectionner** pour changer le niveau.

    ![S’inscrire à la version préliminaire][4]
6. Si vous le voulez, vous pouvez cliquer sur **Afficher les informations sur l’utilisation** pour ouvrir le panneau **Détails des recommandations en matière de niveau tarifaire**, dans lequel vous pouvez afficher le niveau recommandé pour la base de données, comparer les fonctionnalités entre le niveau actuel et le niveau recommandé et afficher un graphique d’analyse de l’utilisation des ressources sur une période donnée.

    ![S’inscrire à la version préliminaire][5]

## <a name="summary"></a>Résumé
Les recommandations relatives aux niveaux tarifaires sont le fruit d’une opération automatisée de collecte des données de télémétrie pour chaque base de données SQL, et de recommandation du niveau de performances/de service le plus adapté, en fonction des besoins réels de la base de données en termes de performances et de fonctionnalités. Dans le panneau Paramètres, cliquez sur **Niveau tarifaire (mise à l’échelle de DTU)** pour afficher les recommandations concernant les niveaux tarifaires des bases de données Web et Business.

## <a name="next-steps"></a>Étapes suivantes
Selon les informations de votre base de données spécifique, l’exécution d’une mise à niveau vers un niveau inférieur ou supérieur ne se produit pas de manière instantanée, en général. Le portail affiche des notifications au moment où la base de données passe au nouveau niveau choisi. Vous pouvez également surveiller l’état de la mise à niveau en affichant la vue [sys.dm_operation_status (Azure SQL Database)](https://msdn.microsoft.com/library/dn270022.aspx) dans la base de données principale du serveur de base de données SQL.

<!--Image references-->
[1]: ./media/sql-database-service-tier-advisor/select-database.png
[4]: ./media/sql-database-service-tier-advisor/choose-pricing-tier.png
[5]: ./media/sql-database-service-tier-advisor/usage-details.png



<!--HONumber=Dec16_HO2-->


