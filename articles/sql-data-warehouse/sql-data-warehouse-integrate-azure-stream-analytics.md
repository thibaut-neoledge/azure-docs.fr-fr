<properties
   pageTitle="Utiliser Azure Stream Analytics avec SQL Data Warehouse | Microsoft Azure"
   description="Conseils sur l’utilisation d’Azure Stream Analytics avec Azure SQL Data Warehouse pour le développement de solutions."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sahaj08"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="09/22/2015"
   ms.author="sahajs;twounder"/>

# Utiliser Azure Stream Analytics avec SQL Data Warehouse

Azure Stream Analytics est un service entièrement géré permettant de traiter des événements avec une latence faible, une haute disponibilité et de façon évolutive via des données de diffusion dans le cloud. Pour découvrir les principes de base de ce service, voir l’article [Présentation d’Azure Stream Analytics][]. Vous pouvez ensuite apprendre à créer une solution de bout en bout avec Stream Analytics en suivant le didacticiel [Prise en main d’Azure Stream Analytics][].

Dans cet article, vous allez apprendre à utiliser votre base de données Azure SQL Data Warehouse à la façon d’un récepteur de sortie pour vos travaux Stream Analytics.

## Composants requis

Tout d’abord, exécutez les étapes suivantes dans le didacticiel [Prise en main d’Azure Stream Analytics][].

1. Création d’une entrée de hub d’événements
2. Configuration et démarrage de l’application de génération d’événements
3. Configuration d'un travail Stream Analytics
4. Spécification d’une entrée de travail et d’une requête

Ensuite, créez une base de données SQL Data Warehouse.

## Spécifier la sortie du travail : base de données Azure SQL Data Warehouse

### Étape 1

En haut de la page de votre travail Stream Analytics, cliquez sur **SORTIE**, puis sur **AJOUTER UNE SORTIE**.

### Étape 2

Sélectionnez Base de données SQL, puis cliquez sur suivant.

![][add-output]

### Étape 3
Entrez les valeurs ci-dessous dans la page suivante :

- *Alias de sortie* : entrez un nom convivial pour cette sortie de travail.
- *Abonnement* :
	- Si votre base de données SQL Data Warehouse se trouve dans le même abonnement que celui de la tâche Stream Analytics, sélectionnez Utiliser la base de données SQL de l’abonnement actuel.
	- Si votre base de données se trouve dans un autre abonnement, sélectionnez Utiliser la base de données SQL d’un autre abonnement.
- *Base de données* : spécifiez le nom d’une base de données de destination.
- *Nom du serveur* : spécifiez le nom du serveur pour la base de données que vous venez d’indiquer. Vous pouvez obtenir cette information dans le portail Azure.

![][server-name]

- *Nom d’utilisateur* : tapez le nom d’utilisateur d’un compte disposant d’autorisations en écriture sur la base de données.
- *Mot de passe* : indiquez le mot de passe du compte d’utilisateur spécifié.
- *Table* : spécifiez le nom de la table cible dans la base de données.

![][add-database]

### Étape 4

Cliquez sur la coche pour ajouter cette sortie du travail et pour vérifier que Stream Analytics peut se connecter à la base de données.

![][test-connection]

Dès que la connexion à la base de données est établie, une notification s’affiche en bas du portail. Vous pouvez tester la connexion à la base de données en cliquant sur Tester la connexion au bas de l’écran.

## Étapes suivantes

Pour consulter une vue d’ensemble de l’intégration, accédez à la rubrique [Vue d’ensemble sur l’intégration de SQL Data Warehouse][].

Pour obtenir des conseils supplémentaires en matière de développement, consultez la rubrique [Vue d’ensemble sur le développement SQL Data Warehouse][].

<!--Image references-->

[add-output]: ./media/sql-data-warehouse-integrate-azure-stream-analytics/add-output.png
[server-name]: ./media/sql-data-warehouse-integrate-azure-stream-analytics/dw-server-name.png
[add-database]: ./media/sql-data-warehouse-integrate-azure-stream-analytics/add-database.png
[test-connection]: ./media/sql-data-warehouse-integrate-azure-stream-analytics/test-connection.png

<!--Article references-->

[Présentation d’Azure Stream Analytics]: stream-analytics-introductiond.md
[Prise en main d’Azure Stream Analytics]: stream-analytics-get-started.md
[Vue d’ensemble sur le développement SQL Data Warehouse]: sql-data-warehouse-overview-develop.md
[Vue d’ensemble sur l’intégration de SQL Data Warehouse]: sql-data-warehouse-overview-integration.md

<!--MSDN references-->

<!--Other Web references-->
[Azure Stream Analytics documentation]: http://azure.microsoft.com/documentation/services/stream-analytics/

<!---HONumber=Sept15_HO4-->