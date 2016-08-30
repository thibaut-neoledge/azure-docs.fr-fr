<properties 
   pageTitle="Query Performance Insight pour base de données SQL Azure" 
   description="La surveillance des performances des requêtes identifie les requêtes consommant le plus d’UC pour une base de données SQL Azure." 
   services="sql-database" 
   documentationCenter="" 
   authors="stevestein" 
   manager="jhubbard" 
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management" 
   ms.date="08/09/2016"
   ms.author="sstein"/>

# Query Performance Insight pour base de données SQL Azure


La gestion et le réglage des performances des bases de données relationnelles est une tâche complexe qui nécessite une réelle expertise et un investissement en temps. Query Performance Insight vous permet de passer moins de temps à résoudre les problèmes de performances des bases de données en fournissant :

- Une meilleure compréhension de la consommation des ressources des bases de données (DTU).
- Les requêtes principales consommatrices d’UC peuvent être réglées pour améliorer les performances.
  - UC
  - Durée
  - Nombre d’exécutions
- La possibilité de connaître les détails d’une requête, d’afficher son texte et l’historique d’utilisation des ressources.
- Des annotations de réglage des performances qui indiquent les actions effectuées par [SQL Azure Database Advisor](sql-database-advisor.md)

​

## Composants requis

- Query Performance Insight est uniquement disponible avec la base de données SQL Azure V12.
- Query Performance Insight nécessite que le [magasin de requêtes](https://msdn.microsoft.com/library/dn817826.aspx) soit actif sur votre base de données. Si le magasin de requêtes ne fonctionne pas, le portail vous invite à l’activer.

 
## Autorisations

Les autorisations [de contrôle d’accès en fonction du rôle](../active-directory/role-based-access-control-configure.md) suivantes sont requises pour utiliser Query Performance Insight :

- Les autorisations **Reader**, **Owner**, **Contributor**, **SQL DB Contributor** ou **SQL Server Contributor** sont requises pour afficher les requêtes et graphiques consommant le plus de ressources.
- Les autorisations **Owner**, **Contributor**, **SQL DB Contributor** ou **SQL Server Contributor** sont requises pour afficher le texte de requête.



## Utilisation de Query Performance Insight

Query Performance Insight est simple d’utilisation :

- Ouvrez le [portail Azure](https://portal.azure.com/) et recherchez la base de données que vous souhaitez examiner.
  - Dans le menu de gauche, sous Support et dépannage, sélectionnez « Query Performance Insight ».
- Dans le premier onglet, passez en revue la liste des requêtes consommant le plus de ressources.
- Sélectionnez une requête pour en afficher les détails.
- Ouvrez [SQL Azure Database Advisor](sql-database-advisor.md), puis regardez si des recommandations sont disponibles.
- Utilisez les icônes de curseurs ou de zoom pour modifier l’intervalle observé.

    ![tableau de bord des performances](./media/sql-database-query-performance/performance.png)

> [AZURE.NOTE] Quelques heures de données doivent être capturées par le magasin de requêtes pour que la base de données SQL fournisse des informations sur les performances des requêtes. Si la base de données n’a aucune activité ou que Query Store est resté inactif pendant une certaine période, les graphiques seront vides lors de l’affichage de cette période. Vous pouvez activer Query Store à tout moment s’il n’est pas en cours d’exécution.



## Consultez les requêtes principales consommatrices d’UC

Dans le [portail](http://portal.azure.com), procédez comme suit :

1. Accédez à une base de données SQL, puis cliquez sur **Tous les paramètres** > **Support et dépannage** > **Query performance insight**.

    ![Query Performance Insight][1]

    La vue des principales requêtes s’ouvre, affichant une liste des requêtes consommant le plus d’UC.

1. Cliquez sur le graphique pour plus d’informations.<br>La première ligne affiche le pourcentage de DTU global de la base de données, tandis que les barres affichent le pourcentage d’UC consommé par les requêtes sélectionnées pendant l’intervalle sélectionné (par exemple, si **Semaine dernière** est sélectionné, chaque barre représente un jour).

    ![principales requêtes][2]

    Le tableau du bas contient des informations agrégées concernant les requêtes visibles.

  -	ID de requête – identificateur unique de la requête dans la base de données.
  -	UC par requête pendant l’intervalle observable (dépend de la fonction d’agrégation).
  -	Durée par requête (dépend de la fonction d’agrégation).
  -	Nombre total d’exécutions pour une requête particulière.

    Sélectionnez ou supprimez des requêtes individuelles pour les inclure ou les exclure du graphique à l’aide des cases à cocher.

1. Si vos données deviennent obsolètes, cliquez sur le bouton **Actualiser**.
1. Vous pouvez utiliser les boutons de curseur et de zoom pour modifier l’intervalle d’observation et examiner les pics d’activité : ![paramètres](./media/sql-database-query-performance/zoom.png)
1. Si vous souhaitez un affichage différent, vous pouvez également sélectionner l’onglet **Personnalisé** et définir :
  
  - la mesure (UC, Durée, Nombre d’exécutions) ;
  - l’intervalle de temps (Dernières 24 heures, Semaine dernière, Mois dernier) ;
  - le nombre de requêtes ;
  - la fonction d’agrégation.

    ![paramètres](./media/sql-database-query-performance/custom-tab.png)

## Affichage des détails d’une requête individuelle

Pour afficher les détails d’une requête :

1. Cliquez sur n’importe quelle requête dans la liste des principales requêtes.

    ![détails](./media/sql-database-query-performance/details.png)

1. La vue détaillée s’ouvre et la consommation d’UC/la durée/le nombre d’exécutions des requêtes sont répartie dans le temps.
1. Cliquez sur le graphique pour plus d’informations.
  - Le graphique supérieur affiche la ligne avec le pourcentage de DTU de base de données global, et les barres indiquent le pourcentage de l’UC consommé par la requête sélectionnée.
  - Le deuxième graphique affiche la durée totale pour la requête sélectionnée.
  - Le graphique du bas affiche le nombre total d’exécutions pour la requête sélectionnée.
    
    ![détails de la requête][3]

1. Vous pouvez également utiliser les curseurs ou les boutons de zoom ou cliquer sur **Paramètres** pour personnaliser l’affichage des données ou pour afficher une autre période.

## Révision des requêtes principales par durée

Dans la mise à jour récente de Query Performance Insight, nous avons introduit deux nouvelles mesures qui peuvent vous aider à identifier les goulots d’étranglement potentiels : la durée et le nombre d’exécutions.<br>

Les requêtes de longue durée ont le plus grand risque de verrouiller des ressources le plus longtemps, de bloquer d’autres utilisateurs et de limiter l’évolutivité. Mais elles peuvent être également facilement optimisées.<br>

Pour identifier les requêtes de longue durée :

1. Ouvrez l’onglet **Personnalisé** dans Query Performance Insight pour la base de données sélectionnée.
1. Sélectionnez la mesure **durée**.
1. Sélectionner le nombre de requêtes et l’intervalle d’observation.
1. Sélectionnez la fonction d’agrégation.
  - **Somme** calcule le temps d’exécution total de toutes les requêtes pendant tout l’intervalle d’observation.
  - **Max** recherche le temps d’exécution maximum pendant l’intervalle d’observation.
  - **Moy** calcule la durée d’exécution moyenne de toutes les exécutions de requête et affiche ces moyennes.

    ![durée de la requête][4]

## Révision des requêtes principales par nombre d’exécutions

Un nombre élevé d’exécutions peut ne pas affecter la base de données elle-même et l’utilisation de ressources peut être faible, mais l’application globale peut devenir lente.

Dans certains cas, un nombre d’exécutions très élevé peut augmenter les allers-retours réseau. Les allers-retours affectent considérablement les performances. Ils peuvent entraîner une latence du réseau et une latence du serveur en aval.

Par exemple, de nombreux sites web centrés sur les données accèdent à la base de données à chaque requête de l’utilisateur. Alors que le regroupement de connexions a un effet positif, l’augmentation du trafic réseau et la charge de traitement dans la base de données peuvent nuire aux performances. Il est généralement conseillé de limiter les allers-retours au strict minimum.

Pour identifier les requêtes fréquemment exécutées (« bavardes ») :

1. Ouvrez l’onglet **Personnalisé** dans Query Performance Insight pour la base de données sélectionnée.
1. Sélectionnez la mesure **nombre d’exécutions**.
1. Sélectionner le nombre de requêtes et l’intervalle d’observation.

    ![nombre d’exécutions de la requête][5]

## Présentation des annotations de réglage des performances 

Lors de l’analyse de votre charge de travail dans Query Performance Insight, vous pouvez remarquer des icônes avec une ligne verticale en haut du graphique.<br>

Ces icônes sont des annotations. Elles représentent les performances qui affectent les actions effectuées par [SQL Azure Database Advisor](sql-database-advisor.md). En pointant sur une annotation, vous obtenez des informations de base sur l’action :

![annotation de requête][6]

Si vous souhaitez en savoir plus ou appliquer des recommandations de SQL Azure Database Advisor, cliquez sur l’icône. Les détails de l’action s’ouvrent. S’il s’agit d’une recommandation active, vous pouvez l’appliquer directement à l’aide de la commande.

![détails d’annotation de requête][7]

### Annotations multiples. ###

Il est possible qu’en raison du niveau de zoom, les annotations qui sont proches les unes des autres soient réduites en une seule. Une icône spéciale s’affiche alors. Si vous cliquez dessus, un panneau s’ouvre, qui contient une liste d’annotations groupées. La corrélation de requêtes et des actions de réglage des performances peut vous aider à mieux comprendre votre charge de travail.


## 	Optimisation de la configuration du magasin de requêtes pour Query Performance Insight

Pendant l’utilisation de Query Performance Insight, vous pouvez rencontrer les messages suivants du magasin de requêtes :

- « Le magasin de requête n’est pas correctement configuré dans cette base de données. Cliquez ici pour en savoir plus. »
- « Le magasin de requête n’est pas correctement configuré dans cette base de données. Cliquez ici pour modifier les paramètres. »

Ces messages s’affichent généralement lorsque le magasin de requêtes ne peut plus collecter de données supplémentaires.

Le premier cas se produit lorsque le magasin de requêtes est en mode Lecture seule et si les paramètres sont définis de façon optimale. Vous pouvez résoudre ce problème en augmentant la taille du magasin de requêtes ou en le nettoyant.

![bouton qds][8]

Le deuxième cas se produit lorsque le magasin de requêtes est désactivé ou si les paramètres ne sont pas définis de façon optimale. <br>Vous pouvez modifier la stratégie de rétention et de capture et activer le magasin de requêtes en exécutant les commandes ci-dessous ou directement à partir du portail :

![bouton qds][9]

### Stratégie de rétention et de capture recommandée

Il existe deux types de stratégies de rétention :

- Basée sur la taille : si la valeur AUTO est définie, les données sont automatiquement supprimées quand la taille maximale est proche.
- Basée sur le temps : la période par défaut est de 30 jours. Ainsi, si le magasin de requêtes manque d’espace, il supprimera les informations liées aux requêtes âgées de plus de 30 jours.

La stratégie de capture peut avoir les valeurs suivantes :

- **Tout** : toutes les requêtes sont capturées.
- **Auto** : les requêtes peu fréquentes et les requêtes avec une durée de compilation et d’exécution insignifiante sont ignorées. Les seuils concernant le nombre d’exécutions, et la durée de compilation et d’exécution, sont déterminés en interne. Il s'agit de l'option par défaut.
- **Aucune** : le magasin de requêtes arrête la capture de nouvelles requêtes, mais les statistiques d’exécution pour les requêtes déjà capturées sont toujours collectées.
	
Nous vous recommandons de définir toutes les stratégies sur AUTO, et de définir la stratégie de suppression des anciennes requêtes sur 30 jours :

    ALTER DATABASE [YourDB] 
    SET QUERY_STORE (SIZE_BASED_CLEANUP_MODE = AUTO);
    	
    ALTER DATABASE [YourDB] 
    SET QUERY_STORE (CLEANUP_POLICY = (STALE_QUERY_THRESHOLD_DAYS = 30));
    
    ALTER DATABASE [YourDB] 
    SET QUERY_STORE (QUERY_CAPTURE_MODE = AUTO);

Augmentez la taille du magasin de requêtes. Pour cela, connectez-vous à une base de données et exécutez la requête suivante :

    ALTER DATABASE [YourDB]
    SET QUERY_STORE (MAX_STORAGE_SIZE_MB = 1024);

Lorsque ces paramètres sont appliqués, le magasin de requêtes collecte finalement les nouvelles requêtes, mais vous pouvez effacer les données du magasin de requêtes si vous ne souhaitez pas attendre.
> [AZURE.NOTE] L’exécution de la requête suivante supprime l’ensemble des informations du magasin de requêtes.


    ALTER DATABASE [YourDB] SET QUERY_STORE CLEAR;


## Résumé

Query Performance Insight vous permet de comprendre l’impact de votre charge de travail de requêtes et la relation de celle-ci avec la consommation des ressources de base de données. Cette fonctionnalité vous permettra d’en savoir plus sur les requêtes les plus gourmandes et d’identifier facilement les requêtes à corriger avant qu’elles ne deviennent un problème.




## Étapes suivantes

Pour obtenir d’autres recommandations concernant l’amélioration des performances de votre base de données SQL, cliquez sur [Recommandations](sql-database-advisor.md) dans le panneau **Query Performance Insight**.

![Performance Advisor](./media/sql-database-query-performance/ia.png)


<!--Image references-->
[1]: ./media/sql-database-query-performance/tile.png
[2]: ./media/sql-database-query-performance/top-queries.png
[3]: ./media/sql-database-query-performance/query-details.png
[4]: ./media/sql-database-query-performance/top-duration.png
[5]: ./media/sql-database-query-performance/top-execution.png
[6]: ./media/sql-database-query-performance/annotation.png
[7]: ./media/sql-database-query-performance/annotation-details.png
[8]: ./media/sql-database-query-performance/qds-off.png
[9]: ./media/sql-database-query-performance/qds-button.png

<!---HONumber=AcomDC_0817_2016-->