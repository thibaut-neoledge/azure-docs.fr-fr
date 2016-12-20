---
title: Azure SQL Database Advisor avec le portail Azure | Microsoft Docs
description: "Vous pouvez utiliser Azure SQL Database Advisor dans le portail Azure afin d’examiner et d’implémenter des recommandations pour vos bases de données SQL existantes et améliorer ainsi les performances actuelles des requêtes."
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: monicar
ms.assetid: cda8a646-0584-4368-b28a-85cdd9b54fcd
ms.service: sql-database
ms.custom: monitor and tune
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 09/30/2016
ms.author: sstein
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: e16587c3eb7366c92297455e8a175ab118174526


---
# <a name="sql-database-advisor-using-the-azure-portal"></a>SQL Database Advisor avec le portail Azure
> [!div class="op_single_selector"]
> * [Présentation de SQL Database Advisor](sql-database-advisor.md)
> * [Portail](sql-database-advisor-portal.md)
> 
> 

Vous pouvez utiliser Azure SQL Database Advisor dans le portail Azure afin d’examiner et d’implémenter des recommandations pour vos bases de données SQL existantes et améliorer ainsi les performances actuelles des requêtes.

## <a name="viewing-recommendations"></a>Affichage des recommandations
La page de recommandations présente les principales recommandations en fonction de leur impact potentiel sur l’amélioration des performances. Vous pouvez également afficher l’état des opérations historiques. Sélectionnez une recommandation ou un état pour afficher ses détails.

Pour afficher et appliquer des recommandations, vous devez disposer des autorisations de [contrôle d’accès basé sur les rôles](../active-directory/role-based-access-control-configure.md) adéquates dans Azure. Les autorisations **Lecteur** et **Collaborateur de base de données SQL** sont obligatoires pour afficher les recommandations, et les autorisations **Propriétaire** et **Collaborateur de base de données SQL** sont obligatoires pour exécuter toutes les actions, créer ou supprimer des index et annuler la création d’index.

1. Connectez-vous au [portail Azure](https://portal.azure.com/).
2. Cliquez sur **More services (Plus de services)** > **Bases de données SQL**, puis sélectionnez votre base de données.
3. Cliquez sur **Recommandation sur les performances** pour afficher les recommandations disponibles pour la base de données choisie.

> [!NOTE]
> Pour obtenir des recommandations, une base de données doit avoir été utilisée pendant environ une journée et avoir fait l’objet d’activités au cours de cette journée. Les activités doivent également avoir été cohérentes. SQL Database Advisor peut plus facilement optimiser des modèles de requête cohérents que des pics d’activité aléatoires. Si les recommandations ne sont pas disponibles, la page **Recommandation sur les performances** doit fournir un message explicatif.
> 
> 

![Recommandations](./media/sql-database-advisor-portal/recommendations.png)

Voici un exemple de recommandation « Résoudre les problèmes de schéma » dans le portail Azure.

![Résoudre les problèmes de schéma](./media/sql-database-advisor-portal/sql-database-advisor-schema-issue.png)

Les recommandations sont triées en fonction de leur impact potentiel sur les performances dans les quatre catégories suivantes :

| Impact | Description |
|:--- |:--- |
| Élevé |Les recommandations ayant un impact élevé fournissent l’impact le plus important sur les performances. |
| Moyenne |Les recommandations ayant un impact moyen améliorent les performances, mais pas de manière substantielle. |
| Faible |Les recommandations ayant un faible impact fournissent de meilleures performances, mais les améliorations ne sont toutefois pas significatives. |

### <a name="removing-recommendations-from-the-list"></a>Suppression de recommandations de la liste
Si votre liste de recommandations contient des éléments que vous souhaitez supprimer de la liste, vous pouvez ignorer la recommandation :

1. Sélectionnez une recommandation dans la liste des **Recommandations**.
2. Cliquez sur **Ignorer** dans le panneau **Détails**.

Si vous le souhaitez, vous pouvez rajouter des éléments rejetés à la liste **Recommandations** :

1. Dans le panneau **Recommandations**, cliquez sur **Afficher les éléments ignorés**.
2. Sélectionnez un élément rejeté dans la liste pour afficher les détails le concernant.
3. Si vous le souhaitez, vous pouvez cliquer sur **Annuler le rejet** pour ajouter l’index à la liste principale de **Recommandations**.

## <a name="applying-recommendations"></a>Application des recommandations
Grâce à SQL Database Advisor, vous pouvez contrôler totalement la façon dont les recommandations sont activées à l’aide de l’une des trois options suivantes : 

* Appliquer les recommandations individuelles une à la fois.
* Activer Advisor pour appliquer automatiquement les recommandations (s’applique uniquement aux recommandations d'index actuellement).
* Pour implémenter une recommandation manuellement, exécutez le script T-SQL recommandé sur votre base de données.

Sélectionnez une recommandation pour afficher ses détails, puis cliquez sur **Afficher le script** pour savoir exactement comment la recommandation doit être créée.

La base de données demeure en ligne pendant qu’Advisor applique la recommandation. L’utilisation de SQL Database Advisor n’entraîne jamais la mise hors connexion d’une base de données.

### <a name="apply-an-individual-recommendation"></a>Appliquer une recommandation individuelle
Vous pouvez consulter et accepter les recommandations une à la fois.

1. Dans le panneau **Recommandations**, cliquez sur une recommandation.
2. Dans le panneau **Détails**, cliquez sur **Appliquer**.
   
    ![Appliquer une recommandation](./media/sql-database-advisor-portal/apply.png)

### <a name="enable-automatic-index-management"></a>Activer la gestion automatique des index
Vous pouvez configurer SQL Database Advisor de manière à implémenter automatiquement les recommandations. Dès qu’une recommandation est disponible, elle est automatiquement appliquée. Comme avec toutes les opérations d’index gérées par le service, si l’impact sur les performances est négatif, la recommandation est annulée.

1. Dans le panneau **Recommandations**, cliquez sur **Automatiser** :
   
    ![Paramètres du conseiller](./media/sql-database-advisor-portal/settings.png)
2. Définissez Advisor de manière à **Créer** ou **Supprimer** les index automatiquement :
   
    ![Index recommandés](./media/sql-database-advisor-portal/automation.png)

### <a name="manually-run-the-recommended-t-sql-script"></a>Exécuter manuellement le script T-SQL recommandé
Sélectionnez une recommandation, puis cliquez sur **Afficher le script**. Exécutez ce script sur votre base de données pour appliquer la recommandation manuellement.

*Les index qui sont exécutés manuellement ne sont pas surveillés ni validés en ce qui concerne l’impact du service sur les performances*. Nous vous suggérons donc de surveiller ces index après leur création pour vérifier s’ils améliorent les performances et de les ajuster ou de les supprimer, si nécessaire. Pour plus d’informations sur la création d’index, consultez [CREATE INDEX (Transact-SQL)](https://msdn.microsoft.com/library/ms188783.aspx).

### <a name="canceling-recommendations"></a>Annulation de recommandations
Les recommandations ayant l’état **En attente**, **En cours de vérification** ou **Réussite** peuvent être annulées. Les recommandations avec l'état **En cours d'exécution** ne peuvent pas être annulées.

1. Sélectionnez une recommandation dans la zone **Historique de paramétrage** pour ouvrir le panneau **Détails des recommandations**.
2. Cliquez sur **Annuler** pour abandonner le processus d'application de la recommandation.

## <a name="monitoring-operations"></a>Surveillance des opérations
L’application d’une recommandation ne se produit pas toujours instantanément. Le portail fournit des informations concernant l’état des opérations de recommandation. Voici les états possibles des index :

| Statut | Description |
|:--- |:--- |
| Pending |La commande Appliquer la recommandation a été reçue et son exécution est planifiée. |
| En cours d'exécution |La recommandation est en cours d’application. |
| Réussite |La recommandation a été appliquée avec succès. |
| Erreur |Une erreur s’est produite au cours du processus d'application de la recommandation. Il peut s’agir d’un problème temporaire ou éventuellement d’un changement de schéma dans la table auquel cas le script n’est plus valide. |
| Annulation |La recommandation a été appliquée, mais a été jugée non performante et est automatiquement annulée. |
| Annulée |La recommandation a été annulée. |

Cliquez sur une recommandation en cours dans la liste pour afficher ses détails :

![Index recommandés](./media/sql-database-advisor-portal/operations.png)

### <a name="reverting-a-recommendation"></a>Annulation d'une recommandation
Si vous avez utilisé Advisor pour appliquer la recommandation (situation dans laquelle vous n’avez pas exécuté manuellement le script T-SQL), il l’annule automatiquement si l’impact sur les performances est négatif. Si vous souhaitez simplement annuler une recommandation, vous pouvez effectuer ce qui suit :

1. Sélectionnez une recommandation appliquée avec succès dans la zone **Historique de paramétrage** .
2. Cliquez sur **Annuler** dans le panneau **Détails de la recommandation**.

![Index recommandés](./media/sql-database-advisor-portal/details.png)

## <a name="monitoring-performance-impact-of-index-recommendations"></a>Analyse de l’impact des recommandations d’index sur les performances
Une fois les recommandations correctement implémentées (actuellement, seulement les opérations d’index et les recommandations de paramétrage des requêtes), vous pouvez cliquer sur **Informations sur la requête** dans le panneau Détails de la recommandation pour ouvrir [Query Performance Insight](sql-database-query-performance.md) et voir l’impact de vos principales requêtes sur les performances.

![Surveiller l’impact sur les performances](./media/sql-database-advisor-portal/query-insights.png)

## <a name="summary"></a>Résumé
SQL Database Advisor fournit des recommandations pour améliorer les performances des bases de données SQL. En fournissant des scripts T-SQL, ainsi que des options individuelles et entièrement automatiques (actuellement, seulement les index), Advisor facilite l’optimisation de votre base de données, avec à la clé une amélioration des performances des requêtes.

## <a name="next-steps"></a>Étapes suivantes
Surveillez vos recommandations et continuez à les appliquer pour affiner les performances. Les charges de travail d’une base de données sont dynamiques et évoluent en permanence. SQL Database Advisor va continuer à surveiller et à fournir des recommandations pouvant potentiellement améliorer les performances de votre base de données. 

* Consultez la page [SQL Database Advisor](sql-database-advisor.md) pour obtenir une vue d’ensemble de SQL Database Advisor.
* Pour connaître l’impact de vos principales requêtes sur les performances, consultez [Query Performance Insights](sql-database-query-performance.md).

## <a name="additional-resources"></a>Ressources supplémentaires
* [Magasin de requêtes](https://msdn.microsoft.com/library/dn817826.aspx)
* [CREATE INDEX](https://msdn.microsoft.com/library/ms188783.aspx)
* [Contrôle d’accès en fonction du rôle](../active-directory/role-based-access-control-configure.md)




<!--HONumber=Nov16_HO3-->


