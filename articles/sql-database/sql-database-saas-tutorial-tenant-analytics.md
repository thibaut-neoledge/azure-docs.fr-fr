---
title: "Exécuter des requêtes d’analyse sur plusieurs bases de données SQL Azure | Microsoft Docs"
description: "Extraire des données à partir de bases de données du locataire dans une base de données d’analyse pour une analyse hors connexion"
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
ms.date: 06/16/2017
ms.author: billgib; sstein
ms.openlocfilehash: 4e32407d5f321198358e07980907c3420aaf56c6
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2017
---
# <a name="extract-data-from-tenant-databases-into-an-analytics-database-for-offline-analysis"></a>Extraire des données à partir de bases de données du locataire dans une base de données d’analyse pour une analyse hors connexion

Dans ce didacticiel, vous utilisez un travail élastique pour exécuter des requêtes sur chaque base de données du locataire. Le travail extrait les données de ventes de ticket et les charge dans une base de données d’analyse (ou entrepôt de données) pour analyse. Cette base de données d’analyse peut être interrogée pour extraire les informations dans les données opérationnelles quotidiennes de tous les locataires.


Ce didacticiel vous explique comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Créer la base de données d’analyse du locataire
> * Créer un travail planifié pour récupérer les données et remplir la base de données d’analyse

Pour suivre ce didacticiel, vérifiez que les conditions préalables ci-dessous sont bien satisfaites :

* L’application SaaS Wingtip est déployée. Pour procéder à un déploiement en moins de cinq minutes, consultez la page [Déployer et explorer une application SaaS Wingtip](sql-database-saas-tutorial.md)
* Azure PowerShell est installé. Pour plus d’informations, consultez [Bien démarrer avec Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).
* La dernière version de SQL Server Management Studio (SSMS) est installée. [Télécharger et installer SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)

## <a name="tenant-operational-analytics-pattern"></a>Modèle d’analyse opérationnelle du locataire

Une des grandes opportunités avec les applications SaaS est l’exploitation des nombreuses données de locataire qui sont stockées dans le cloud. Utilisez ces données pour obtenir des informations sur le fonctionnement et l’utilisation de votre application, et sur vos locataires. Ces données peuvent guider le développement des fonctionnalités, les améliorations de convivialité et autres investissements dans l’application et la plateforme. Lorsqu’il se fait dans une seule base de données mutualisée, l’accès à ces données est facile, mais pas si simple lors d’une distribution à grande échelle sur des milliers de bases de données. Pour accéder à ces données, une approche consiste à utiliser des travaux élastiques, qui permettent la capture des résultats des requêtes lors de l’exécution du travail dans une base de données de sortie et dans une table.

## <a name="get-the-wingtip-application-scripts"></a>Obtenir les scripts d’application Wingtip

Les scripts et le code source de l’application SaaS Wingtip sont disponibles dans le référentiel GitHub [WingtipSaaS](https://github.com/Microsoft/WingtipSaaS). [Étapes de téléchargement des scripts SaaS Wingtip](sql-database-wtp-overview.md#download-and-unblock-the-wingtip-saas-scripts).

## <a name="deploy-a-database-for-tenant-analytics-results"></a>Déployer une base de données pour les résultats d’analyse du locataire

Ce didacticiel nécessite que vous ayez déployé une base de données, afin de capturer les résultats de l’exécution du travail de scripts, qui contiennent les requêtes qui renvoient des résultats. À cet effet, nous allons créer une base de données appelée tenantanalytics.

1. Ouvrez ...\\Modules d’apprentissage\\Operational Analytics\\Tenants Analytics\\*Demo-TenantAnalyticsDB.ps1* dans *PowerShell ISE* et définissez la valeur suivante :
   * **$DemoScenario** = **2** *Déployer une base de données d’analyse opérationnelle*
1. Appuyez sur **F5** pour exécuter le script de démonstration (qui appelle le script *Deploy-TenantAnalyticsDB.ps1*), qui crée la base de données d’analyse du locataire.

## <a name="create-some-data-for-the-demo"></a>Créer des données pour la démonstration

1. Ouvrez ...\\Modules d’apprentissage\\Operational Analytics\\Tenants Analytics\\*Demo-TenantAnalyticsDB.ps1* dans *PowerShell ISE* et définissez la valeur suivante :
   * **$DemoScenario** = **1** *Acheter des tickets pour des événements dans tous les lieux*
1. Appuyez sur **F5** pour exécuter le script et créez un historique d’achat de tickets.


## <a name="create-a-scheduled-job-to-retrieve-tenant-analytics-about-ticket-purchases"></a>Créer un travail planifié pour récupérer l’analyse du locataire concernant l’achat de tickets

Ce script crée un travail permettant de récupérer les informations d’achat de tickets par tous les locataires. Une fois ces données regroupées dans une seule table, vous pouvez obtenir des mesures pertinentes sur l’achat de tickets par les locataires.

1. Ouvrez SSMS et connectez-vous au serveur catalog-&lt;user&gt;.database.windows.net.
1. Ouvrez ...\\Modules d’apprentissage\\Operational Analytics\\Tenant Analytics\\*TicketPurchasesfromAllTenants.sql*.
1. Modifiez &lt;User&gt; (Utilisateur), servez-vous du nom d’utilisateur utilisé lors du déploiement de l’application SaaS Wingtip au début du script, **sp\_add\_target\_group\_member** et **sp\_add\_jobstep**
1. Si vous n’êtes pas déjà connecté, cliquez avec le bouton droit, sélectionnez **Connexion** et connectez-vous au serveur catalog-&lt;User&gt;.database.windows.net
1. Assurez-vous que vous êtes connecté à la base de données **jobaccount**, puis appuyez sur **F5** pour exécuter le script.

* **sp\_add\_target\_group** crée le nom du groupe cible *TenantGroup*. Nous devons à présent ajouter des membres cibles.
* **sp\_add\_target\_group\_member** ajoute un type de membre cible *server*, qui considère qu’au moment de l’exécution du travail, toutes les bases de données dans ce serveur (notez qu’il s’agit du serveur customer1-&lt;User&gt; contenant les bases de données des locataires) doivent être incluses dans le travail.
* **sp\_add\_job** crée un nouveau travail hebdomadaire planifié appelé « Achat de tickets par tous les locataires ».
* **sp\_add\_jobstep** crée l’étape du travail contenant le texte de la commande T-SQL pour récupérer toutes les informations liées aux achats de tickets par tous les locataires, et pour copier le résultat renvoyé défini dans une table appelée *AllTicketsPurchasesfromAllTenants*.
* Les autres vues dans le script indiquent l’existence des objets et contrôlent l’exécution du travail. Surveillez la valeur de l’état dans la colonne **lifecycle**. Une fois, Réussi, le travail a réussi sur toutes les bases de données locataires et les deux autres bases de données contenant la table de référence.

Si le script s’exécute correctement, vous devez obtenir des résultats similaires :

![results](media/sql-database-saas-tutorial-tenant-analytics/ticket-purchases-job.png)

## <a name="create-a-job-to-retrieve-a-summary-count-of-ticket-purchases-from-all-tenants"></a>Créer un travail permettant de récupérer un résumé du nombre d’achats de tickets par tous les locataires

Ce script crée un travail permettant de récupérer la somme des achats de tickets par tous les locataires.

1. Ouvrez SSMS et connectez-vous au serveur *catalog-&lt;User&gt;.database.windows.net*.
1. Ouvrez le fichier... \\Modules d’apprentissage\\Provision and Catalog\\Operational Analytics\\Tenant Analytics\\*Results-TicketPurchasesfromAllTenants.sql*.
1. Modifiez &lt;User&gt; (Utilisateur), servez-vous du nom d’utilisateur utilisé lors du déploiement de l’application SaaS Wingtip dans le script, dans la procédure stockée **sp\_add\_jobstep**
1. Si vous n’êtes pas déjà connecté, cliquez avec le bouton droit, sélectionnez **Connexion** et connectez-vous au serveur catalog-&lt;User&gt;.database.windows.net
1. Assurez-vous que vous êtes connecté à la base de données **tenantanalytics**, puis appuyez sur **F5** pour exécuter le script.

Si le script s’exécute correctement, vous devez obtenir des résultats similaires :

![results](media/sql-database-saas-tutorial-tenant-analytics/total-sales.png)



* **sp\_add\_job** crée un travail hebdomadaire planifié appelé « ResultsTicketsOrders ».

* **sp\_add\_jobstep** crée l’étape du travail contenant le texte de la commande T-SQL pour récupérer toutes les informations liées aux achats de tickets par tous les locataires, et pour copier le résultat renvoyé défini dans une table appelée CountofTicketOrders.

* Les autres vues dans le script indiquent l’existence des objets et contrôlent l’exécution du travail. Surveillez la valeur de l’état dans la colonne **lifecycle**. Une fois, Réussi, le travail a réussi sur toutes les bases de données locataires et les deux autres bases de données contenant la table de référence.


## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à :

> [!div class="checklist"]
> * Déployez une base de données d’analyse du locataire
> * Créer un travail planifié permettant de récupérer des données d’analyse sur les locataires

Félicitations !

## <a name="additional-resources"></a>Ressources supplémentaires

* Autres [didacticiels reposant sur l’application SaaS Wingtip](sql-database-wtp-overview.md#sql-database-wingtip-saas-tutorials)
* [Tâches élastiques](sql-database-elastic-jobs-overview.md)
