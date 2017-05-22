---
title: "Exécuter des requêtes d’analyse ad hoc sur plusieurs bases de données SQL Azure | Microsoft Docs"
description: "Exécuter des requêtes d’analyse ad hoc sur plusieurs bases de données dans une application mutualisée"
keywords: "didacticiel sur les bases de données SQL"
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: tutorial
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 05/10/2017
ms.author: billgib; sstein
ms.translationtype: Human Translation
ms.sourcegitcommit: fc4172b27b93a49c613eb915252895e845b96892
ms.openlocfilehash: dd41e7f1f131f6c18e03d2434982c3d681342b8b
ms.contentlocale: fr-fr
ms.lasthandoff: 05/12/2017


---
# <a name="run-ad-hoc-analytics-queries-across-all-wtp-tenants"></a>Exécuter des requêtes d’analyse ad hoc sur tous les clients WTP

Dans ce didacticiel, vous créez une base de données d’analyse ad hoc et exécutez plusieurs requêtes sur tous les clients. Ces requêtes peuvent extraire des analyses enfouies dans les données opérationnelles quotidiennes de l’application WTP.

Pour exécuter des requêtes d’analyse ad hoc (sur plusieurs clients), l’application WTP utilise une [requête élastique](sql-database-elastic-query-overview.md) ainsi qu’une base de données analytique.


Ce didacticiel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]

> * Déployer une base de données d’analyse ad hoc
> * Exécuter des requêtes distribuées sur toutes les bases de données client



Pour suivre ce didacticiel, vérifiez que les conditions préalables suivantes sont bien satisfaites :

* L’application WTP est déployée. Pour la déployer en moins de cinq minutes, voir [Déployer et découvrir l’application SaaS WTP](sql-database-saas-tutorial.md).
* Azure PowerShell est installé. Pour plus d’informations, voir [Bien démarrer avec Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)


## <a name="ad-hoc-analytics-pattern"></a>Modèle d’analyse ad hoc

Une des grandes opportunités avec les applications SaaS est l’exploitation de la vaste quantité de données partagées que vous avez stockées de manière centralisée dans le cloud. Utilisez ces données pour obtenir un aperçu du fonctionnement et de l’utilisation de vos applications, vos clients, leurs utilisateurs, leurs préférences et leurs comportements. Les analyses que vous obtenez peuvent guider le développement des fonctionnalités, les améliorations de convivialité et autres investissements dans vos applications et services.

L’accès à ces données dans une base de données mutualisée est facile, mais pas si simple lors d’une distribution à grande échelle sur des milliers de bases de données. Une approche consiste à utiliser une requête élastique, qui permet d’effectuer des requêtes ad hoc sur un ensemble distribué de bases de données avec un schéma commun. Les requêtes élastiques utilisent une seule base de données *principale* dans laquelle sont définies des tables externes qui reflètent les tables dans les bases de données distribuées (client). Les requêtes envoyées à cette base de données principale sont compilées pour produire un plan de requête distribué, avec des parties de la requête transmises aux bases de données client en fonction des besoins. Les requêtes élastiques utilisent la carte de partitions dans la base de données de catalogue pour fournir l’emplacement des bases de données client. La configuration et les requêtes sont simples grâce à l’utilisation de T-SQL normal, et les requêtes ad hoc sont prises en charge à partir d’outils tels que Power BI et Excel.

## <a name="get-the-wingtip-application-scripts"></a>Obtenir les scripts d’application Wingtip

Les scripts Wingtip Tickets et le code source de l’application sont disponibles dans le référentiel github [WingtipSaaS](https://github.com/Microsoft/WingtipSaaS). Les fichiers de script se trouvent dans le [dossier Learning Modules](https://github.com/Microsoft/WingtipSaaS/tree/master/Learning%20Modules). Téléchargez le dossier **Learning Modules** en local sur votre ordinateur, tout en conservant l’arborescence.

## <a name="deploy-the-database-used-for-ad-hoc-analytics-queries"></a>Déployer la base de données utilisée pour les requêtes d’analyse ad hoc

Cet exercice déploie la base de données d’analyse ad hoc qui contient le schéma utilisé pour émettre des requêtes ad hoc qui couvrent toutes les bases de données client.

1. Ouvrir... \\Modules d’apprentissage\\Operational Analytics\\Adhoc Analytics\\*Demo-AdhocAnalytics.ps1* dans *PowerShell ISE* et définissez les valeurs suivantes :
   * **$DemoScenario** = 2, **Déployer la base de données d’analyse ad hoc**.

1. Appuyez sur **F5** pour exécuter le script et créer une nouvelle base de données SQL pour l’analyse ad hoc et ajoutez-la au catalogue WTP. Les tables TicketPurchases, Tickets et Venues sont ajoutées en tant que tables externes qui peuvent être interrogées.
   Ne vous inquiétez pas si vous rencontrez des avertissements indiquant sur *Le serveur RPC n’est pas disponible*.


Vous disposez maintenant d’une base de données *adhocanalytics*, qui peut être utilisée pour exécuter des requêtes distribuées et collecter des informations sur tous les clients !

## <a name="run-ad-hoc-analytics-queries"></a>Exécuter des requêtes d’analyse ad hoc

Cet exercice exécute des requêtes d’analyse ad hoc pour découvrir des informations sur le client à partir de l’application WTP.

1. Ouvrir... \\Modules d’apprentissage\\Operational Analytics\\Adhoc Analytics\\*Demo-AdhocAnalyticsQueries.sql* dans SSMS.
1. Assurez-vous que vous êtes connecté à la base de données **adhocanalytics**
1. Sélectionnez la requête individuelle que vous souhaitez exécuter, puis appuyez sur **F5** :

    ![query](media/sql-database-saas-tutorial-adhoc-analytics/query.png)




## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à effectuer les opérations suivantes :

> [!div class="checklist"]

> * Déployer une base de données d’analyse ad hoc
> * Exécuter des requêtes distribuées sur toutes les bases de données client

[Didacticiel Log Analytics (OMS)](sql-database-saas-tutorial-log-analytics.md)

## <a name="additional-resources"></a>Ressources supplémentaires

* [Autres didacticiels reposant sur le déploiement initial d’applications sur Wingtip Tickets Platform (WTP)](sql-database-wtp-overview.md#sql-database-wtp-saas-tutorials)
* [Requête élastique](sql-database-elastic-query-overview.md)

