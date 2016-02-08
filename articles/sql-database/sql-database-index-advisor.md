<properties 
   pageTitle="Assistant Index de base de données SQL Azure" 
   description="L’assistant Index de base de données SQL Azure recommande des nouveaux index pour vos bases de données SQL existantes afin d’améliorer les performances actuelles des requêtes." 
   services="sql-database" 
   documentationCenter="" 
   authors="stevestein" 
   manager="jeffreyg" 
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management" 
   ms.date="01/23/2015"
   ms.author="sstein"/>

# Assistant Index de base de données SQL

Azure SQL Database Index Advisor fournit des recommandations d’index pour vos bases de données SQL existantes afin d’améliorer les performances actuelles des requêtes. Le service SQL Database évalue les performances de l’index en analysant l’historique d’utilisation de votre base de données SQL. Les index les mieux adaptés pour exécuter la charge de travail standard de votre base de données sont recommandés.

Index advisor vous aide à régler les performances de votre base de données en :

- fournissant des recommandations sur les index à créer (les recommandations ne sont disponibles que pour les index non cluster) ;
- fournissant des recommandations sur les index à supprimer (les recommandations de suppression d’index sont disponibles en version préliminaire et ne s’appliquent actuellement qu’aux index en double) ;
- vous permettant de choisir d’appliquer les recommandations d’index automatiquement sans aucune interaction utilisateur (les recommandations automatisées nécessitent que le [magasin de requêtes](https://msdn.microsoft.com/library/dn817826.aspx) soit activé et en cours d’exécution) ;
- restaurant automatiquement les recommandations qui ont un impact négatif sur les performances. 


Cet article décrit Index Advisor pour les serveurs V12. Les recommandations d’index sont disponibles pour les serveurs V11, mais vous devez exécuter le script Transact-SQL (T-SQL) fourni pour les mettre en œuvre. Le conseiller n’annule pas les opérations d’index sur les serveurs V11 ; vous devez donc surveiller l’impact sur les performances et l’annuler si nécessaire.


### Autorisations

Pour afficher et créer des recommandations d’index, vous devez disposer des autorisations de [contrôle d’accès basé sur les rôles](role-based-access-control-configure.md) adéquates dans Azure.

- Les autorisations **Lecteur**, **Collaborateur de base de données SQL** sont obligatoires pour afficher les recommandations.
- Les autorisations **Propriétaire**, **Collaborateur de base de données SQL** autorisations sont obligatoires pour exécuter toutes les actions, créer ou supprimer des index et annuler la création d’index.


## Affichage des recommandations d’index

La page Recommandations d’index présente les principaux index suggérés en fonction de leur impact potentiel sur l’amélioration des performances. Vous pouvez également afficher l’état des dernières opérations d’index. Sélectionnez une recommandation ou l’état pour afficher ses détails.

Pour afficher les recommandations d’index :

1. Connectez-vous au [portail Azure](https://portal.azure.com/).
2. Cliquez sur **PARCOURIR** > **Bases de données SQL**, puis sélectionnez votre base de données.
5. Cliquez sur **Tous les paramètres** > **Conseiller d’indexation** pour afficher les **recommandations d’index** disponibles pour la base de données choisie.

> [AZURE.NOTE] Pour obtenir des recommandations d’index, une base de données doit avoir été utilisée pendant environ une semaine et avoir fait l’objet d’activités au cours de cette semaine. Les activités doivent également avoir été cohérentes. L’assistant Index peut plus facilement optimiser des modèles de requête cohérents que des pics d’activité aléatoires. Si les recommandations ne sont pas disponibles, la page **Recommandations d’index** doit fournir un message explicatif.

![Index recommandés](./media/sql-database-index-advisor/recommendations.png)

Les recommandations sont triées en fonction de leur impact potentiel sur les performances dans les 4 catégories suivantes :

| Impact | Description |
| :--- | :--- |
| Élevé | Les recommandations ayant un impact élevé fournissent l’impact le plus important sur les performances. |
| Substantiel | Les recommandations ayant un impact substantiel améliorent sensiblement les performances. |
| Modéré | Les recommandations ayant un impact modéré améliorent les performances, mais pas de manière substantielle. |
| Faible | Les recommandations ayant un faible impact fournissent de meilleures performances qu’en l’absence de cet index, mais les améliorations ne sont toutefois pas significatives. 
Utilisez l’indicateur d’impact pour identifier les meilleurs candidates à la création d’index.


### Suppression de recommandations d’index de la liste

Si votre liste d’index recommandés contient des index que vous souhaitez supprimer de la liste, vous pouvez ignorer la recommandation :

1. Sélectionnez une recommandation dans la liste des **index recommandés**.
2. Cliquez sur **Rejeter l’index** dans le volet **Détails sur l’index**.


Si vous le souhaitez, vous pouvez rajouter des index rejetés à la liste **Index recommandés** :

1. Dans le volet **Recommandations d’index**, cliquez sur **Afficher les recommandations d’index rejetées**.
1. Sélectionnez un index rejeté dans la liste pour afficher les détails le concernant.
1. Éventuellement, cliquez sur **Annuler le rejet** pour ajouter l’index à la liste principale de **recommandations d’index**.



## Application des recommandations d’index

Grâce à Index Advisor, vous pouvez contrôler totalement la façon dont les recommandations d’index sont activées à l’aide d’une des 3 options suivantes.

- Appliquer les recommandations individuelles une à la fois.
- Permettre à Index Advisor d’appliquer automatiquement les recommandations d’index.
- Exécuter manuellement le script T-SQL recommandé sur votre base de données pour implémenter une recommandation.

Sélectionnez toute recommandation pour afficher ses détails, puis cliquez sur **Afficher le script** pour savoir exactement comment la recommandation doit être créée.

La base de données demeure en ligne pendant que le conseiller applique la recommandation ; l’utilisation d’Index Advisor n’entraîne jamais la mise hors connexion d’une base de données.

### Appliquer une recommandation individuelle

Vous pouvez consulter et accepter les recommandations une à la fois.

1. Dans le panneau **Recommandations d’index**, cliquez sur une recommandation.
2. Dans le panneau **Détails de l’index**, cliquez sur **Appliquer**.

    ![Appliquer une recommandation](./media/sql-database-index-advisor/apply.png)


### Activer la gestion automatique des index

Vous pouvez configurer Index Advisor de manière à implémenter automatiquement les recommandations. Dès qu’une recommandation est disponible, elle est automatiquement appliquée. Comme avec toutes les opérations d’index gérées par le service, si l’impact sur les performances est négatif, la recommandation est annulée.

1. Dans le panneau **Recommandations d’index**, cliquez sur **Paramètres du conseiller** :

    ![Paramètres du conseiller](./media/sql-database-index-advisor/settings.png)

2. Définissez le conseiller de manière à **Créer** ou **Supprimer** les index automatiquement :

    ![Index recommandés](./media/sql-database-index-advisor/automation.png)




### Exécuter manuellement le script T-SQL recommandé

Sélectionnez toute recommandation, puis cliquez sur **Afficher le script**. Exécutez ce script sur votre base de données pour appliquer la recommandation manuellement.

*Les index qui sont exécutés manuellement ne sont pas surveillés ni validés concernant l’impact du service sur les performances*. Nous vous suggérons donc de surveiller ces index après la création pour vérifier s’ils améliorent les performances et les ajuster ou les supprimer, si nécessaire. Pour plus d’informations sur la création d’index, consultez [CREATE INDEX (Transact-SQL)](https://msdn.microsoft.com/library/ms188783.aspx).


### Annulation de la création d’index

Vous pouvez annuler les index dont l’état est **En attente**. Vous ne pouvez pas annuler les index en cours de création (état **En cours d’exécution**).

1. Sélectionnez un index **En attente** dans la zone **Opérations d’index** pour ouvrir le volet **Détails sur l’index**.
2. Cliquez sur **Annuler** pour abandonner le processus de création d’index.



## Surveillance des opérations d’index

L’application d’une recommandation ne se produit pas toujours instantanément. Le portail fournit des informations concernant l’état des opérations d’index. Lorsque vous gérez les index, voici les états possibles de ces index :

| Statut | Description |
| :--- | :--- |
| Pending | La commande de création d’index a été reçue et la création de l’index est planifiée. |
| Executing | La commande de création d’index est en cours d’exécution et l’index est en cours de création. |
| Succès | L’index a été créé correctement. |
| Échec | L’index n’a pas été créé. Il peut s’agir d’un problème temporaire ou éventuellement d’un changement de schéma dans la table auquel cas le script n’est plus valide. |
| Annulation | Le processus de création d’index a été annulé ou a été jugé non performant et est automatiquement annulé. |

Cliquez sur une recommandation en cours dans la liste pour afficher ses détails :

![Index recommandés](./media/sql-database-index-advisor/operations.png)



### Annulation d’un index

Si vous avez utilisé le conseiller pour créer un index (situation dans laquelle vous n’avez pas exécuté manuellement le script T-SQL), il annule automatiquement l’index si l’impact sur les performances est négatif. Si vous souhaitez simplement annuler une opération du conseiller d’indexation, vous pouvez effectuer ce qui suit.


1. Sélectionnez un index créé dans la liste **Opérations d’index**.
2. Cliquez sur **Annuler** dans le panneau **Détails de l’index** ou cliquez sur **Afficher le script** pour un script DROP INDEX exécutable.

![Index recommandés](./media/sql-database-index-advisor/details.png)


## Analyse de l’impact des recommandations d’index sur les performances

Une fois les recommandations correctement implémentées, vous pouvez cliquer sur **Informations sur la requête** dans le panneau Détails de l’index pour ouvrir [Query Performance Insight](sql-database-query-performance.md) et voir l’impact de vos principales requêtes sur les performances.

![Surveiller l’impact sur les performances](./media/sql-database-index-advisor/query-insights.png)


## Résumé

Index Advisor procure des recommandations d’index et une expérience automatisée pour la gestion des index de base de données SQL. En fournissant des scripts T-SQL, ainsi que des options de gestion d’index individuelles et entièrement automatiques, Index Advisor facilite l’optimisation des index de vos bases de données, avec à la clé une amélioration des performances des requêtes.



## Étapes suivantes

Surveillez vos recommandations d’index et continuez à les appliquer pour affiner les performances. Les charges de travail d’une base de données sont dynamiques et évoluent en permanence. L’assistant Index va continuer à surveiller et à recommander des index pouvant potentiellement améliorer les performances de votre base de données.

<!---HONumber=AcomDC_0128_2016-->