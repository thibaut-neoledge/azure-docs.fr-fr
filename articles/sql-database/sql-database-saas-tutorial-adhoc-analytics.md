---
title: "Exécuter des requêtes d’analyse ad hoc sur plusieurs bases de données SQL Azure | Microsoft Docs"
description: "Exécutez des requêtes d’analyse ad hoc sur plusieurs bases de données SQL dans une application mutualisée SaaS Wingtip."
keywords: "didacticiel sur les bases de données SQL"
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: scale out apps
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/24/2017
ms.author: billgib; sstein
ms.translationtype: Human Translation
ms.sourcegitcommit: 5edc47e03ca9319ba2e3285600703d759963e1f3
ms.openlocfilehash: bf003a3677ed27bc833de59ef61f7637a6899d37
ms.contentlocale: fr-fr
ms.lasthandoff: 05/31/2017


---
# <a name="run-ad-hoc-analytics-queries-across-all-wingtip-saas-tenants"></a>Exécuter des requêtes d’analyse ad hoc sur tous les SaaS Wingtip

Dans ce didacticiel, vous créez une base de données d’analyse ad hoc et exécutez plusieurs requêtes sur tous les clients. Ces requêtes peuvent extraire des analyses enfouies dans les données opérationnelles quotidiennes de l’application SaaS Wingtip.

Pour exécuter des requêtes d’analyse ad hoc (sur plusieurs clients), l’application SaaS Wingtip. utilise une [requête élastique](sql-database-elastic-query-overview.md) ainsi qu’une base de données analytique.


Ce didacticiel vous apprend à effectuer les opérations suivantes :

> [!div class="checklist"]

> * Explorer les vues globales de chacune des bases de données qui activent l’interrogation parmi les clients
> * Déployer une base de données d’analyse ad hoc
> * Exécuter des requêtes distribuées sur toutes les bases de données client



Pour suivre ce tutoriel, vérifiez que les conditions préalables suivantes sont bien satisfaites :

* L’application SaaS Wingtip est déployée. Pour procéder à un déploiement en moins de cinq minutes, consultez la page [Déployer et explorer une application SaaS Wingtip](sql-database-saas-tutorial.md)
* Azure PowerShell est installé. Pour plus d’informations, consultez [Bien démarrer avec Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).


## <a name="ad-hoc-analytics-pattern"></a>Modèle d’analyse ad hoc

Une des grandes opportunités avec les applications SaaS est l’exploitation de la vaste quantité de données partagées stockées de manière centralisée dans le cloud. Utilisez ces données pour obtenir un aperçu du fonctionnement et de l’utilisation de vos applications, vos clients, leurs utilisateurs, leurs préférences et leurs comportements. Ces analyses peuvent guider le développement des fonctionnalités, les améliorations de convivialité et les autres investissements dans vos applications et services.

L’accès à ces données dans une base de données mutualisée est facile, mais pas si simple lors d’une distribution à grande échelle sur des milliers de bases de données. Une approche consiste à utiliser une requête élastique, qui permet d’effectuer des requêtes ad hoc sur un ensemble distribué de bases de données avec un schéma commun. Les requêtes élastiques utilisent une seule base de données *principale* dans laquelle sont définies des tables externes qui reflètent les tables ou les vues dans les bases de données distribuées (client). Les requêtes envoyées à cette base de données principale sont compilées pour produire un plan de requête distribué, avec des parties de la requête transmises aux bases de données client en fonction des besoins. Les requêtes élastiques utilisent la carte de partitions dans la base de données de catalogue pour fournir l’emplacement des bases de données client. La configuration et les requêtes sont simples grâce à l’utilisation de [Transact-SQL](https://docs.microsoft.com/sql/t-sql/language-reference) standard, et les requêtes ad hoc sont prises en charge à partir d’outils tels que Power BI et Excel.

## <a name="get-the-wingtip-application-scripts"></a>Obtenir les scripts d’application Wingtip

Les scripts et le code source de l’application SaaS Wingtip sont disponibles dans le référentiel GitHub [WingtipSaaS](https://github.com/Microsoft/WingtipSaaS). [Étapes de téléchargement des scripts SaaS Wingtip](sql-database-wtp-overview.md#download-the-wingtip-saas-scripts).


## <a name="explore-the-global-views"></a>Explorer les vues globales

L’application SaaS Wingtip est générée à l’aide d’un modèle client par base de données, de sorte que le schéma de base de données client est défini à partir d’une perspective de client unique. Les informations propres au client existent dans une table nommée *Venue*, qui comporte une seule ligne et qui est par ailleurs conçue sous forme de segment sans clé primaire.  Les autres tables du schéma n’ont pas besoin d’être liées à la table *Venue*, car lors d’une utilisation normale, il n’y a jamais de doute sur la question de savoir à quel client les données appartiennent.  Toutefois, lors d’une interrogation portant sur toutes les bases de données, la corrélation des données issues de tables de la base de données avec un client spécifique revêt une certaine importance. Pour simplifier cela, un ensemble de vues offrant une vision globale de chaque client, est ajouté à la base de données du client. Ces vues globales projettent un ID de client dans chaque table interrogée de manière globale. Cela facilite l’identification des données issues de chaque client. Pour des raisons pratiques, ces vues ont été créées au préalable dans toutes les bases de données client (ainsi que la base de données finale, afin de rendre ces vues globales disponibles lors de la configuration de nouveaux clients).

1. Ouvrez SSMS et [connectez-vous au serveur tenants1-&lt;USER&gt;](sql-database-wtp-overview.md#explore-database-schema-and-execute-sql-queries-using-ssms).
1. Développez **Bases de données**, cliquez avec le bouton droit sur **contosoconcerthall**, puis sélectionnez **Nouvelle requête**.
1. Exécutez les requêtes suivantes pour découvrir la différence entre les tables de client unique et les vues globales :

   ```T-SQL
   -- This is the base Venue table, that has no VenueId associated.
   SELECT * FROM Venue

   -- Notice the plural name 'Venues'. This view projects a VenueId column.
   SELECT * FROM Venues

   -- The base Events table which has no VenueId column.
   SELECT * FROM Events

   -- This view projects the VenueId retrieved from the Venues table.
   SELECT * FROM VenueEvents
   ```

Dans l’exemple de base de données, nous avons calculé un ID d’entier à partir d’un hachage du nom de lieu, mais toute autre approche peut être utilisée pour introduire une valeur unique. Cela revient à comment créer la clé de client dans le catalogue, mais il n’est pas nécessaire que la clé de catalogue et l’ID de client de la base de données *adhocanalytics* soient identiques.

Pour examiner une *vue* et voir comment elle est créée :

1. Dans **Explorateur d’objets**, développez **contosoconcethall** > **Vues** :

   ![vues](media/sql-database-saas-tutorial-adhoc-analytics/views.png)

1. Cliquez avec le bouton droit sur **dbo.Venues**.
1. Sélectionnez **Générer un script de la vue en tant que** > **CRÉER vers** > **Nouvelle fenêtre d’éditeur de requête**

Effectuez cette opération pour toutes les *vues* afin d’examiner la façon dont elles ont été créées.

## <a name="deploy-the-database-used-for-ad-hoc-analytics-queries"></a>Déployer la base de données utilisée pour les requêtes d’analyse ad hoc

Cet exercice permet de déployer la base de données *adhocanalytics*. Cette base de données contient le schéma utilisé pour l’interrogation de toutes les bases de données client. La base de données est déployée sur le serveur de catalogue existant, qui est le serveur qui contenant toutes les bases de données liées à la gestion.

1. Ouvrez ...\\Modules d’apprentissage\\Operational Analytics\\Adhoc Analytics\\*Deploy-AdhocAnalyticsDB.ps1*.
1. Faites défiler jusqu’à la section assignant `$commandText` au script SQL. Passez en revue le script et notez les points suivants :

   1. La requête élastique utilise des informations d’identification de niveau base de données pour accéder à chacune des bases de données client. Ces informations d’identification doivent être disponibles dans toutes les bases de données et doivent normalement bénéficier des droits minimaux nécessaires pour activer ces requêtes ad-hoc.
   1. La source de données externe, définie pour utiliser la carte de partitions client dans la base de données de catalogue.  En l’utilisant comme source de données externe, les requêtes sont distribuées sur toutes les bases de données inscrites dans le catalogue au moment de l’émission de la requête.
   1. Les tables externes qui font référence aux vues globales décrites dans la section précédente.
   1. La table locale *VenueTypes* qui est créée et alimentée.  Dans la mesure où cette table de données de référence est commune à toutes les bases de données client, elle peut être représentée ici comme une table locale, qui pour certaines requêtes peut réduire la quantité de données déplacées entre les bases de données client et la base de données *adhocanalytics*.


1. Ouvrez maintenant... \\Modules d’apprentissage\\Operational Analytics\\Adhoc Analytics\\*Demo-AdhocAnalytics.ps1* dans *PowerShell ISE* et définissez les valeurs suivantes :
   * **$DemoScenario** = 2, **Déployer la base de données d’analyse ad hoc**.

1. Appuyez sur **F5** pour exécuter le script et créer la base de données *adhocanalytics*.

   Vous pouvez ignorer ici les avertissements indiquant que *Le serveur RPC n’est pas disponible*.

Vous disposez maintenant d’une base de données *adhocanalytics*, qui peut être utilisée pour exécuter des requêtes distribuées et collecter des informations sur tous les clients !

![base de données adhocanalytics](media/sql-database-saas-tutorial-adhoc-analytics/adhocanalytics.png)

## <a name="run-ad-hoc-analytics-queries"></a>Exécuter des requêtes d’analyse ad hoc

Maintenant que la base de données *adhocanalytics* est configurée, exécutez des requêtes ad hoc :

1. Ouvrir... \\Modules d’apprentissage\\Operational Analytics\\Adhoc Analytics\\*Demo-AdhocAnalyticsQueries.sql* dans SSMS.
1. Assurez-vous que vous êtes connecté à la base de données **adhocanalytics**
1. Sélectionnez la requête individuelle que vous souhaitez exécuter, puis appuyez sur **F5** :

    ![query](media/sql-database-saas-tutorial-adhoc-analytics/query.png)




## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à effectuer les opérations suivantes :

> [!div class="checklist"]

> * Déployer une base de données d’analyse ad hoc
> * Exécuter des requêtes distribuées sur toutes les bases de données client

Essayez maintenant le [Didacticiel sur l’analyse des clients](sql-database-saas-tutorial-tenant-analytics.md)

## <a name="additional-resources"></a>Ressources supplémentaires

* Autres [didacticiels reposant sur l’application SaaS Wingtip](sql-database-wtp-overview.md#sql-database-wingtip-saas-tutorials)
* [Requête élastique](sql-database-elastic-query-overview.md)

