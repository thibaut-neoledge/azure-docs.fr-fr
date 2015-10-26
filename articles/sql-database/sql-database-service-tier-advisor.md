<properties 
   pageTitle="Recommandations sur les niveaux tarifaires d’Azure SQL Database" 
   description="Lorsque vous modifiez les niveaux tarifaires dans le portail Azure, vous pouvez consulter les recommandations fournies, notamment le niveau le mieux adapté à l’exécution de la charge de travail d’une base de données SQL Azure existante. Les niveaux tarifaires décrivent les niveaux de service et de performances d’une base de données SQL." 
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
   ms.date="10/02/2015"
   ms.author="sstein"/>

# Recommandations relatives aux niveaux tarifaires des bases de données SQL

 Bénéficiez de recommandations sur les niveaux tarifaires, notamment les niveaux de service et de performances les mieux adaptés à l’exécution d’une charge de travail d’une base de données SQL Azure existante.

> [AZURE.NOTE]Ces recommandations sont disponibles uniquement pour les bases de données Web et Business et les pools de bases de données élastiques. Elles sont disponibles uniquement sur le [portail Azure en version préliminaire](https://portal.azure.com/).


Obtenez des recommandations en termes de niveau tarifaire lors des tâches suivantes :

- [Modifier les niveaux de service et de performances (niveau tarifaire) d’une base de données SQL](sql-database-scale-up.md)
- [Mise à niveau d’un serveur SQL Azure vers la version 12](sql-database-v12-upgrade.md)
- Accéder à votre serveur V12 : si vos bases de données peuvent [bénéficier d’un pool de bases de données élastiques](sql-database-elastic-pool-portal.md#recommended-elastic-database-pools), le panneau du serveur affiche un message indiquant un pool recommandé. Cliquez sur le message pour créer le pool recommandé.
- [Créer un pool de bases de données élastiques](sql-database-elastic-pool.md#elastic-database-pool-pricing-tier-recommendations)





## Vue d'ensemble

Le service SQL Database analyse les performances et les fonctionnalités requises en évaluant un historique de l’utilisation des ressources pour une base de données SQL. Par ailleurs, le niveau de service minimum acceptable est déterminé en fonction de la taille de la base de données et des fonctionnalités de [continuité d’activité](sql-database-business-continuity.md) activées.

Ces informations sont analysées ; ensuite, le système recommande le niveau de service et de performances le plus adapté à l’exécution d’une charge de travail classique d’une base de données et la gestion de son ensemble de fonctionnalités actuel.

- Le service examine les données historiques sur la période précédente de 15 à 30 jours (utilisation des ressources, taille des bases de données et activité de ces dernières) et effectue une comparaison entre la quantité de ressources utilisée et les limites réelles des niveaux de service et de performances actuellement disponibles.
- Les données sont analysées toutes les 15 secondes. Le jeu de résultats de chaque intervalle est classé, afin de déterminer le niveau de performances et de service existant le mieux adapté à la gestion de la charge de travail de ce jeu de résultats.
- Ces échantillons de 15 secondes sont ensuite regroupés au sein de l’analyse sur 15-30 jours, plus étendue. Le système recommande ensuite le niveau de service et de performances susceptible de gérer le plus efficacement 95 % de la charge de travail d’historique.

### Recommandations

Selon l’utilisation de votre base de données, vous pouvez actuellement rencontrer 2 catégories de recommandations :


| Recommandation | Description |
| :--- | :--- |
| Mise à niveau | Mise à niveau vers un nouveau niveau. |
| Non disponible | Une base de données requiert une charge de travail minimale, ou environ 14 jours d’activité. Il n’existe pas suffisamment de données pour fournir une recommandation valide. |

## Obtenir des recommandations sur les niveaux tarifaires

Accédez à des recommandations sur les niveaux tarifaires en sélectionnant une base de données « Web ou Business » existante et en cliquant sur la vignette **Niveau tarifaire**. (Des recommandations sur les niveaux tarifaires sont également disponibles quand vous [mettez à niveau Azure SQL Server vers la version 12](sql-database-v12-upgrade.md).)

1. Connectez-vous à la [version préliminaire du portail Azure](https://portal.azure.com/).
2. Cliquez sur **PARCOURIR** > **Bases de données SQL**.
4. Dans le panneau **Bases de données SQL**, cliquez sur la base de données pour laquelle vous voulez afficher une recommandation :

    ![Sélectionner la base de données][1]

5. Dans le panneau de la base de données, sélectionnez la vignette **Niveau tarifaire**.

    ![Niveau tarifaire][2]


7. Lorsque vous cliquez sur **Niveau de tarification**, le panneau **Niveaux de tarification recommandés** s’affiche, dans lequel vous pouvez cliquer sur le niveau suggéré, puis sur le bouton **Sélectionner** pour le modifier.

    ![S’inscrire à la version préliminaire][4]

8. Si vous le voulez, vous pouvez cliquer sur **Afficher les informations sur l’utilisation** pour ouvrir le panneau **Détails des recommandations en matière de niveau de tarification**, dans lequel vous pouvez afficher le niveau recommandé pour la base de données, comparer les fonctionnalités entre le niveau actuel et le niveau recommandé et afficher un graphique d’analyse de l’utilisation des ressources sur une période donnée.

    ![S’inscrire à la version préliminaire][5]



## Résumé

Les recommandations relatives aux niveaux de tarification sont le fruit d’une opération automatisée de rassemblement des données de télémétrie pour chaque base de données SQL, et de recommandation du niveau de performances/de service le plus adapté, en fonction des besoins réels de la base de données en termes de performances et de fonctionnalités. Cliquez sur la vignette **Niveau de tarification** figurant sur le panneau d’une base de données pour afficher les recommandations relatives aux niveaux de tarification.



## Étapes suivantes

Selon les informations de votre base de données spécifique, l’exécution d’une mise à niveau vers un niveau inférieur ou supérieur ne se produit pas de manière instantanée, en général. Le portail de gestion affiche des notifications au moment où la base de données passe au nouveau niveau choisi. Vous pouvez également surveiller l’état de la mise à niveau en affichant la vue [sys.dm\_operation\_status (Azure SQL Database)](https://msdn.microsoft.com/library/dn270022.aspx) dans la base de données principale du serveur de base de données SQL.


<!--Image references-->
[1]: ./media/sql-database-service-tier-advisor/select-database.png
[2]: ./media/sql-database-service-tier-advisor/pricing-tier.png
[3]: ./media/sql-database-service-tier-advisor/preview-sign-up.png
[4]: ./media/sql-database-service-tier-advisor/choose-pricing-tier.png
[5]: ./media/sql-database-service-tier-advisor/usage-details.png


 

<!---HONumber=Oct15_HO3-->