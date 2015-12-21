<properties 
   pageTitle="Query Performance Insight pour base de données SQL Azure" 
   description="La surveillance des performances des requêtes identifie les requêtes consommant le plus d’UC pour une base de données SQL Azure." 
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
   ms.date="12/02/2015"
   ms.author="sstein"/>

# Query Performance Insight pour base de données SQL Azure


La gestion et le réglage des performances des bases de données relationnelles est une tâche complexe qui nécessite une réelle expertise et un investissement en temps. Query Performance Insight vous permet de passer moins de temps à résoudre les problèmes de performances des bases de données en fournissant :

- Une meilleure compréhension de la consommation des ressources des bases de données (DTU). 
- Les requêtes principales consommatrices d’UC peuvent être réglées pour améliorer les performances. 
- La possibilité d’examiner les détails d’une requête.

## Composants requis

- Query Performance Insight est uniquement disponible avec la base de données SQL Azure V12.
- Query Performance Insight qui nécessite que le [magasin de requêtes](https://msdn.microsoft.com/library/dn817826.aspx) soit en cours d’exécution sur votre base de données. Le portail vous invite à activer le magasin de requêtes s’il ne fonctionne pas encore.

 
## Autorisations

Les autorisations [de contrôle d’accès en fonction du rôle](role-based-access-control-configure.md) suivantes sont requises pour utiliser Query Performance Insight :

- Les autorisations **Reader**, **Owner**, **Contributor**, **SQL DB Contributor** ou **SQL Server Contributor** sont requises pour afficher les requêtes et graphiques consommant le plus de ressources. 
- Les autorisations **Owner**, **Contributor**, **SQL DB Contributor** ou **SQL Server Contributor** sont requises pour afficher le texte de requête.



## Utilisation de Query Performance Insight

Query Performance Insight est simple d’utilisation :

- Passez en revue la liste des requêtes consommant le plus de ressources. 
- Sélectionnez une requête individuelle pour en afficher les détails.
- Cliquez sur **Paramètres** pour personnaliser l’affichage des données ou pour afficher une autre période.



> [AZURE.NOTE]Quelques heures de données doivent être capturées par Query Store pour que la base de données SQL fournisse des informations sur les performances des requêtes. Si la base de données n’a aucune activité ou que Query Store est resté inactif pendant une certaine période, les graphiques seront vides lors de l’affichage de cette période. Vous pouvez activer Query Store à tout moment s’il n’est pas en cours d’exécution.



## Consultez les requêtes principales consommatrices d’UC

Dans le [portail](https://portal.azure.com), procédez comme suit :

1. Accédez à une base de données SQL et cliquez sur **Analyse des performances de requête**. 

    ![Query Performance Insight][1]

    La vue des principales requêtes s’ouvre, affichant une liste des requêtes consommant le plus d’UC.

1. Cliquez sur le graphique pour plus d’informations.<br>La première ligne affiche le pourcentage de DTU global pour la base de données, tandis que les barres affichent le pourcentage d’UC consommé par les requêtes sélectionnées. Sélectionnez ou effacez des requêtes individuelles pour les inclure ou les exclure du graphique.

    ![principales requêtes][2]

1. Vous pouvez également cliquer sur **Modifier le graphique** pour personnaliser l’affichage des données de consommation d’UC ou pour afficher une autre période.

## Affichage des détails d’une requête individuelle

Pour afficher les détails d’une requête :

1. Cliquez sur n’importe quelle requête dans la liste des principales requêtes.<br>La vue détaillée s’ouvre et la consommation d’UC de requêtes est répartie dans le temps.
3. Cliquez sur le graphique pour plus d’informations.<br>La première ligne affiche le pourcentage de DTU global et les barres affichent le pourcentage d’UC consommé par la requête sélectionnée.
4. Passez en revue les données pour afficher des mesures détaillées, notamment la durée, le nombre d’exécutions et le pourcentage d’utilisation des ressources pour chaque intervalle que la requête exécutait.
    
    ![détails de la requête][3]

1. Vous pouvez également cliquer sur **Paramètres** pour personnaliser l’affichage des données de consommation d’UC ou pour afficher une autre période.




## Résumé

Query Performance Insight vous permet de comprendre l’impact de votre charge de travail de requêtes et la relation de celle-ci avec la consommation des ressources de base de données. Cette fonctionnalité vous permettra d’en savoir plus sur les requêtes les plus gourmandes et d’identifier facilement les requêtes à corriger avant qu’elles ne deviennent un problème. Cliquez sur la vignette **Query Performance Insight** sur un panneau de la base de données pour afficher les requêtes consommant le plus d’unité centrale (UC).




## Étapes suivantes

Les charges de travail d’une base de données sont dynamiques et évoluent en permanence. Surveillez vos requêtes et continuez à les régler pour améliorer les performances.

Découvrez l’[assistant Index](sql-database-index-advisor.md) pour obtenir des recommandations supplémentaires pour améliorer les performances de votre base de données SQL.

<!--Image references-->
[1]: ./media/sql-database-query-performance/tile.png
[2]: ./media/sql-database-query-performance/top-queries.png
[3]: ./media/sql-database-query-performance/query-details.png

<!---HONumber=AcomDC_1210_2015-->