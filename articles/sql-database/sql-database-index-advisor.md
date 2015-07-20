<properties 
   pageTitle="Assistant Index de base de données SQL Azure" 
   description="Les recommandations d’index fournies vous permettent de créer facilement des index parfaitement adaptés à l’exécution de la charge de travail de la base de données SQL Azure." 
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
   ms.date="06/30/2015"
   ms.author="sstein"/>

# Assistant Index de base de données SQL

L’assistant Index de base de données SQL Azure recommande des nouveaux index pour vos bases de données SQL existantes afin d’améliorer les performances actuelles des requêtes.

Le service de base de données SQL évalue les performances de l’index en analysant l’historique d’utilisation des ressources d’une base de données SQL. Il recommande ensuite les index les plus adaptés pour exécuter la charge de travail standard de la base de données.

L’assistant Index simplifie la gestion des index en fournissant des recommandations concernant les index à créer. Pour les serveurs V12, l’assistant Index peut également créer et valider des index en quelques clics dans le [portail Azure](https://portal.azure.com/). Une fois l’index créé, le service de base de données SQL analyse les performances de la charge de travail de la base de données et fournit des informations détaillées sur l’impact du nouvel index. Si l’analyse détermine qu’un index recommandé a un impact négatif sur les performances, cet index est automatiquement annulé.

L’assistant Index vous permet de consacrer moins de temps au réglage des performances de votre base de données.


> [AZURE.NOTE]L’assistant Index est actuellement disponible en version préliminaire et uniquement dans le [portail Azure](https://portal.azure.com/).


## Considérations relatives à la version préliminaire

L’assistant Index est actuellement disponible en version préliminaire. Il présente les limitations suivantes :

- Les recommandations d’index ne peuvent être créées et validées que pour des serveurs V12 (les recommandations et les scripts de création d’index sont fournis pour les serveurs V12).
- Les recommandations et les outils de gestion sont disponibles pour les index non cluster uniquement.

## Conditions préalables

Pour afficher et créer des recommandations d’index, vous devez disposer des autorisations de [contrôle d’accès basé sur les rôles](role-based-access-control-configure.md) adéquates dans Azure.

- Les autorisations **Lecteur**, **Collaborateur de base de données SQL** sont obligatoires pour afficher les recommandations.
- Les autorisations **Propriétaire**, **Collaborateur de base de données SQL** autorisations sont obligatoires pour exécuter toutes les actions, créer ou supprimer des index et annuler la création d’index.


## Utilisation de l’assistant Index

L’assistant Index est facile à utiliser. Pour simplifier la gestion des index de votre base de données, tentez compte des conseils suivants :

- D’abord, examinez la liste des recommandations d’index et déterminez lesquels créer ou ignorer. Dans la liste, les recommandations sont triées et libellées en fonction de leur impact estimé sur les performances (détails ci-dessous). 
- Créez ou ignorez les index recommandés. Créez automatiquement l’index en cliquant sur **Créer l’index** dans le portail, ou créez manuellement l’index en exécutant le script de création d’index.
- Pour les index créés manuellement, vous devez surveiller le processus de création et mesurer l’impact sur les performances. Pour les index créés automatiquement, la surveillance et l’analyse de l’impact sur les performances sont effectuées automatiquement par le service de base de données SQL Azure. 



## Examen des index recommandés

L’assistant Index fournit une liste de recommandations d’index dans le volet Base de données sur le [portail Azure](https://portal.azure.com/). Les principales recommandations sélectionnées sont affichées pour chaque table de la base de données choisie lorsque la création d’un nouvel index peut améliorer les performances.

### Pour examiner les recommandations d’index actuellement disponibles :

1. Connectez-vous au [portail Azure](https://portal.azure.com/).
2. Cliquez sur l’option **PARCOURIR** dans le menu de gauche.
3. Cliquez sur **Bases de données SQL** dans le panneau **Parcourir**.
4. Dans le volet **Bases de données SQL**, cliquez sur la base de données dont vous voulez examiner les recommandations d’index.
5. Cliquez sur **Assistant Index** pour ouvrir et afficher les **recommandations d’index** disponibles pour la base de données choisie.

> [AZURE.NOTE]Pour obtenir des recommandations d’index, une base de données doit avoir été utilisée pendant environ une semaine et avoir fait l’objet d’activités au cours de cette semaine. Les activités doivent également avoir été cohérentes. L’assistant Index peut plus facilement optimiser des modèles de requête cohérents que des pics d’activité aléatoires.

![Index recommandés][3]

Les recommandations sont triées en fonction de leur impact potentiel sur les performances dans les 4 catégories suivantes :

| Impact | Description |
| :--- | :--- |
| Élevé | Les recommandations ayant un impact élevé fournissent l’impact le plus important sur les performances. |
| Substantiel | Les recommandations ayant un impact substantiel améliorent sensiblement les performances. |
| Modéré | Les recommandations ayant un impact modéré améliorent les performances, mais pas de manière substantielle. |
| Faible | Les recommandations ayant un faible impact fournissent de meilleures performances qu’en l’absence de cet index, mais les améliorations ne sont toutefois pas significatives. 
Utilisez l’indicateur d’impact pour identifier les meilleurs candidates à la création d’index.

### Gestion de la liste d’index recommandés

Si votre liste d’index recommandés contient des index qui ne vous semblent pas bénéfiques, l’assistant Index vous permet d’en rejeter (vous pourrez rétablir ultérieurement les **index recommandés** rejetés si besoin).

#### Rejet d’une recommandation d’index

1. Sélectionnez l’index dans la liste des **index recommandés**.
1. Cliquez sur **Rejeter l’index** dans le volet **Détails sur l’index**.

#### Affichage des index rejetés et rétablissement de ces index dans la liste principale

1. Dans le volet **Recommandations d’index**, cliquez sur **Afficher les recommandations d’index rejetées**.
1. Sélectionnez un index rejeté dans la liste pour afficher les détails le concernant.
1. Éventuellement, cliquez sur **Annuler le rejet** pour ajouter l’index à la liste principale de **recommandations d’index**.



## Création d’index

L’assistant Index vous permet de contrôler entièrement la création d’index. Chaque recommandation fournit un script de création d’index T-SQL. Avant toute action dans une base de données, vous pouvez consulter des informations précises sur la manière dont l’index sera créé.

Les recommandations d’index sont disponibles pour tous les serveurs de base de données SQL Azure, mais seuls les serveurs V12 fournissent la création automatique d’index. Il est également possible de tirer parti de l’assistant Index sur les serveurs non-V12, mais vous devez créer les index manuellement comme expliqué ci-dessous.

Pour la création d’index automatique et manuelle, sélectionnez un index recommandé dans le volet **Recommandations d’index** et procédez de la manière suivante :

### Création automatique d’index (serveurs V12 uniquement)

Si la base de données se trouve sur un serveur V12, vous pouvez facilement créer un index recommandé en sélectionnant l’index souhaité sur le portail et en cliquant sur **Créer l’index**.

La base de données reste en ligne pendant la création d’index. le fait d’utiliser l’assistant Index ne met pas la base de données hors connexion.

En outre, les index créés avec **Créer l’index** ne nécessitent aucune surveillance des performances supplémentaire. Si l’index a un impact négatif sur les performances, il est alors automatiquement annulé. Après l’utilisation de Création d’index, les mesures relatives à l’impact du nouvel index sont disponibles dans le portail.


### Création manuelle d’index (tous les serveurs)

Sélectionnez un index recommandé dans le portail, puis cliquez sur **Afficher le script**. Exécutez ce script sur votre base de données pour créer l’index recommandé. Les index qui sont créés manuellement ne sont pas surveillés ni validés concernant leur impact réel sur les performances. Nous vous suggérons donc de surveiller ces index après la création pour vérifier s’ils améliorent les performances et les ajuster ou les supprimer, si nécessaire. Pour plus d’informations sur la création d’index, consultez [CREATE INDEX (Transact-SQL)](https://msdn.microsoft.com/library/ms188783.aspx).


### Annulation de la création d’index

Vous pouvez annuler les index dont l’état est **En attente**. Vous ne pouvez pas annuler les index qui sont en cours de création (état **En cours d’exécution**).

1. Sélectionnez un index **En attente** dans la zone **Opérations d’index** pour ouvrir le volet **Détails sur l’Index**.
1. Cliquez sur **Annuler** pour abandonner le processus de création d’index.

## Surveillance des opérations d’index après la création d’index

La création d’un index ne se produit pas instantanément. Le portail fournit des informations concernant l’état des opérations d’index. Lorsque vous gérez les index, voici les états possibles de ces index :

| Statut | Description |
| :--- | :--- |
| Pending | La commande de création d’index a été reçue et la création de l’index est planifiée. |
| Executing | La commande de création d’index est en cours d’exécution et l’index est en cours de création. |
| Succès | L’index a été créé correctement. |
| Échec | L’index n’a pas été créé. Il peut s’agir d’un problème temporaire ou éventuellement d’un changement de schéma dans la table auquel cas le script n’est plus valide. |
| Annulation | Le processus de création d’index a été annulé ou a été jugé non performant et est automatiquement annulé. |



![Index recommandés][4]



## Suppression d’un index
Vous pouvez supprimer des index qui ont été créés avec l’assistant Index.


1. Sélectionnez un index créé dans la liste **Opérations d’index**.
1. Cliquez sur **Supprimer l’index** dans le volet **Détails sur l’index** ou cliquez sur **Afficher le script** pour un script DROP INDEX.



## Résumé

Les recommandations d’index fournissent une solution automatisée pour gérer la création et l’analyse des index de chaque base de données SQL et la recommandation des meilleurs index. Cliquez sur la vignette **Assistant Index** dans un volet de base de données pour voir les recommandations d’index.



## Étapes suivantes

Surveillez vos recommandations d’index et continuez à les appliquer pour affiner les performances. Les charges de travail d’une base de données sont dynamiques et évoluent en permanence. L’assistant Index va continuer à surveiller et à recommander des index pouvant potentiellement améliorer les performances de votre base de données.


<!--Image references-->
[1]: ./media/sql-database-index-advisor/index-recommendations.png
[2]: ./media/sql-database-index-advisor/index-details.png
[3]: ./media/sql-database-index-advisor/recommended-indexes.png
[4]: ./media/sql-database-index-advisor/index-operations.png

<!---HONumber=July15_HO2-->