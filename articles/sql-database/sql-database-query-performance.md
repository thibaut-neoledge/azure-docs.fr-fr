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
- La possibilité d’examiner les détails d’une requête.

## Composants requis

- Query Performance Insight est uniquement disponible avec la base de données SQL Azure V12.
- Query Performance Insight qui nécessite que le [magasin de requêtes](https://msdn.microsoft.com/library/dn817826.aspx) soit en cours d’exécution sur votre base de données. Si le magasin de requêtes ne fonctionne pas, le portail vous invite à l’activer.

 
## Autorisations

Les autorisations [de contrôle d’accès en fonction du rôle](../active-directory/role-based-access-control-configure.md) suivantes sont requises pour utiliser Query Performance Insight :

- Les autorisations **Reader**, **Owner**, **Contributor**, **SQL DB Contributor** ou **SQL Server Contributor** sont requises pour afficher les requêtes et graphiques consommant le plus de ressources.
- Les autorisations **Owner**, **Contributor**, **SQL DB Contributor** ou **SQL Server Contributor** sont requises pour afficher le texte de requête.



## Utilisation de Query Performance Insight

Query Performance Insight est simple d’utilisation :

- Passez en revue la liste des requêtes consommant le plus de ressources.
- Sélectionnez une requête pour en afficher les détails.
- Ouvrez [SQL Database Advisor](sql-database-advisor.md), puis regardez si des recommandations sont disponibles.
- Zoomez pour obtenir des informations détaillées.

    ![tableau de bord des performances](./media/sql-database-query-performance/performance.png)

> [AZURE.NOTE] Quelques heures de données doivent être capturées par Query Store pour que la base de données SQL fournisse des informations sur les performances des requêtes. Si la base de données n’a aucune activité ou que Query Store est resté inactif pendant une certaine période, les graphiques sont vides lors de l’affichage de cette période. Si Query Store n’est pas en cours d’exécution, vous pouvez l’activer à tout moment.



## Consultez les requêtes principales consommatrices d’UC

Dans le [portail](http://portal.azure.com), procédez comme suit :

1. Accédez à une base de données SQL et cliquez sur **Paramètres** > **Performances** > **Requêtes**.

    ![Query Performance Insight][1]

    La vue des principales requêtes s’ouvre, affichant une liste des requêtes consommant le plus d’UC.

1. Cliquez sur le graphique pour plus d’informations.<br>La première ligne affiche le pourcentage global de DTU pour la base de données. Les barres affichent le pourcentage d’UC consommée par les requêtes sélectionnées pendant l’intervalle sélectionné (par exemple, si **Semaine dernière** est sélectionné, chaque barre représente un jour).

    ![principales requêtes][2]

    Le tableau du bas contient des informations agrégées concernant les requêtes visibles.

    -	ID de requête – identificateur unique de la requête dans la base de données.
    -	UC par requête pendant l’intervalle observable (dépend de la fonction d’agrégation).
    -	Durée par requête (dépend de la fonction d’agrégation).
    -	Nombre total d’exécutions pour une requête particulière.


	Sélectionnez ou effacez des requêtes individuelles pour les inclure ou les exclure du graphique.


1. Si vos données deviennent obsolètes, cliquez sur le bouton **Actualiser**.
1. Vous pouvez également cliquer sur **Paramètres** pour personnaliser l’affichage des données de consommation d’UC ou pour afficher une autre période.

    ![paramètres](./media/sql-database-query-performance/settings.png)

## Affichage des détails d’une requête individuelle

Pour afficher les détails d’une requête :

1. Cliquez sur n’importe quelle requête dans la liste des principales requêtes.

    ![détails](./media/sql-database-query-performance/details.png)

4. La vue détaillée s’ouvre et la consommation d’UC de requêtes est répartie dans le temps.
3. Cliquez sur le graphique pour plus d’informations.<br>La première ligne affiche le pourcentage de DTU global et les barres affichent le pourcentage d’UC consommé par la requête sélectionnée.
4. Passez en revue les données pour afficher des mesures détaillées, notamment la durée, le nombre d’exécutions et le pourcentage d’utilisation des ressources pour chaque intervalle que la requête exécutait.
    
    ![détails de la requête][3]

1. Vous pouvez également cliquer sur **Paramètres** pour personnaliser l’affichage des données de consommation d’UC ou pour afficher une autre période.


## 	Optimisation de la configuration du magasin de requêtes pour Query Performance Insight

Pendant l’utilisation de Query Performance Insight, vous pouvez rencontrer les messages suivants du magasin de requêtes :

- « Le magasin de requêtes a atteint sa capacité maximale et ne peut plus collecter de données. »
- « Le magasin de requêtes de cette base de données est en lecture seule et ne peut pas collecter de données d’analyse de performances ».
- « Les paramètres du magasin de requêtes ne sont pas définis de manière optimale pour Query Performance Insight. »

Ces messages s’affichent généralement lorsque le magasin de requêtes ne peut plus collecter de données supplémentaires. Pour résoudre ces problèmes, vous avez plusieurs possibilités :

-	Modifier la stratégie de rétention et de capture du magasin de requêtes
-	Augmenter la taille du magasin de requêtes
-	Supprimer le contenu du magasin de requêtes

### Stratégie de rétention et de capture recommandée

Il existe deux types de stratégies de rétention :

- Basée sur la taille : si la valeur AUTO est définie, les données sont automatiquement supprimées quand la taille maximale est proche.
- Temporel : si le magasin de requêtes manque d’espace, il supprime les informations de requête de plus de 30 jours (paramètre par défaut).

La stratégie de capture peut avoir les valeurs suivantes :

- **Tout** : toutes les requêtes sont capturées. **Tout** est l’option par défaut.
- **Auto** : les requêtes peu fréquentes et les requêtes avec une durée de compilation et d’exécution insignifiante sont ignorées. Les seuils concernant le nombre d’exécutions et la durée de compilation et d’exécution sont déterminés en interne.
- **Aucun** : le magasin de requêtes capture de nouvelles requêtes.
	
Nous vous recommandons de définir toutes les stratégies sur AUTO, et de définir la stratégie de suppression des anciennes requêtes sur 30 jours :

    ALTER DATABASE [YourDB] 
    SET QUERY_STORE (SIZE_BASED_CLEANUP_MODE = AUTO);
    	
    ALTER DATABASE [YourDB] 
    SET QUERY_STORE (CLEANUP_POLICY = (STALE_QUERY_THRESHOLD_DAYS = 30));
    
    ALTER DATABASE [YourDB] 
    SET QUERY_STORE (QUERY_CAPTURE_MODE = AUTO);

Augmentez la taille du magasin de requêtes en vous connectant à une base de données et en exécutant la requête suivante :

    ALTER DATABASE [YourDB]
    SET QUERY_STORE (MAX_STORAGE_SIZE_MB = 1024);

Supprimez le contenu du magasin de requêtes. Supprime toutes les informations actuelles dans le magasin de requête :

    ALTER DATABASE [YourDB] SET QUERY_STORE CLEAR;


## Résumé

Query Performance Insight vous permet de comprendre l’impact de votre charge de travail de requêtes et la relation de celle-ci avec la consommation des ressources de base de données. Cette fonctionnalité vous permet d’en savoir plus sur les requêtes les plus gourmandes et d’identifier facilement les requêtes à corriger avant qu’elles ne deviennent un problème.




## Étapes suivantes

Pour obtenir d’autres recommandations concernant l’amélioration des performances de votre base de données SQL, cliquez sur [Database Advisor](sql-database-advisor.md) dans le panneau **Query Performance Insight**.

![Performance Advisor](./media/sql-database-query-performance/ia.png)


<!--Image references-->
[1]: ./media/sql-database-query-performance/tile.png
[2]: ./media/sql-database-query-performance/top-queries.png
[3]: ./media/sql-database-query-performance/query-details.png

<!---HONumber=AcomDC_0810_2016-->