<properties
   pageTitle="Visualiser les données SQL Data Warehouse avec Power BI"
   description="Visualiser les données SQL Data Warehouse avec Power BI"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor="" />

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/16/2016"
   ms.author="lodipalm;barbkess;sonyama" />

# Visualiser des données avec Power BI

> [AZURE.SELECTOR]
- [Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
- [Azure Machine Learning](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
- [Visual Studio](sql-data-warehouse-query-visual-studio.md)
- [sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md) 

Ce didacticiel vous montre comment utiliser Power BI pour vous connecter SQL Data Warehouse et créer quelques visualisations de base.

> [AZURE.VIDEO azure-sql-data-warehouse-sample-data-and-powerbi]

## Configuration requise

Pour parcourir ce didacticiel, vous avez besoin des éléments suivants :

- Un SQL Data Warehouse préchargé avec la base de données AdventureWorksDW. Pour le configurer, consultez [Créer un Azure SQL Data Warehouse][] et chargez les données d’exemple. Si vous disposez déjà d’un entrepôt de données, mais sans disposer d’exemples de données, vous pouvez [charger manuellement des exemples de données][].


## 1\. Connectez-vous à votre base de données

Pour ouvrir Power BI et vous connecter à votre base de données AdventureWorksDW :

1. Connectez-vous au [portail Azure][].
2. Cliquez sur **Bases de données SQL** et choisissez votre base de données SQL Data Warehouse AdventureWorks.

    ![Recherche de votre base de données][1]

3. Cliquez sur le bouton Open in Power BI.

    ![Bouton Power BI][2]

4. La page de connexion de la base de données SQL Data Warehouse devrait afficher l’adresse web de votre base de données. Cliquez sur Suivant.

    ![Connexion Power BI][3]

6. Saisissez votre mot de passe Azure SQL Server pour vous connecter à votre base de données SQL Data Warehouse.

    ![Se connecter à Power BI][4]

7. Une fois que vous vous êtes connecté à Power BI, cliquez sur le jeu de données AdventureWorksDW sur le panneau de gauche. Cette action ouvrira la base de données.

    ![Ouvrir AdventureWorksDW Power BI][5]



## 2\. Créer un rapport

Vous êtes maintenant prêt à utiliser Power BI pour analyser vos exemples de données AdventureWorksDW. Pour effectuer l'analyse, AdventureWorksDW dispose d'une vue appelée AggregateSales. Cette vue contient quelques-unes des métriques essentielles à l'analyse des ventes de l'entreprise.

1. Pour créer une correspondance entre montant des ventes et code postal, dans le panneau de champs de droite, cliquez sur la vue AggregateSales afin de l’agrandir. Cliquez sur les colonnes PostalCode et SalesAmount pour les sélectionner.

    ![Sélection d’AggregateSales Power BI][6]

    Power BI reconnaît automatiquement ces données comme des données géographiques et les intègre dans une carte.

    ![Carte Power BI][7]

2. Cette étape crée un graphique à barres qui affiche le montant des ventes en fonction du revenu client. Pour créer ce dernier, accédez à la vue développée d’AggregateSales. Cliquez sur le champ SalesAmount. Faites glisser le champ Revenu du client sur la gauche et déposez-le dans Axe.

    ![Axe de sélection Power BI][8]

    Nous avons déplacé le graphique à barres sur le côté gauche.

    ![Barre Power BI][9]

3. Cette étape crée un graphique en courbes qui indique le montant des ventes par date de commande. Pour créer ce dernier, accédez à la vue développée d’AggregateSales. Cliquez sur SalesAmount et OrderDate. Dans la colonne Visualisations, cliquez sur l’icône de graphique en courbes ; il s’agit de la première icône de la deuxième ligne sous Visualisations.

	![Sélection de graphique en courbes Power BI][10]

    Vous disposez maintenant d’un rapport qui affiche trois présentations différentes des données.

    ![Ligne Power BI][11]

Vous pouvez enregistrer votre progression à tout moment en cliquant sur **Fichier**, puis en sélectionnant **Enregistrer**.

## Étapes suivantes
Une fois que vous êtes familiarisé avec les exemples de données, vous pouvez découvrir les opérations de [développement][], de [chargement][] ou de [migration][]. Vous pouvez également consulter le [site web de Power BI][].

<!--Image references-->
[1]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-find-database.png
[2]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-button.png
[3]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-connect-to-azure.png
[4]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-sign-in.png
[5]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-open-adventureworks.png
[6]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-aggregatesales.png
[7]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-map.png
[8]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-chooseaxis.png
[9]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-bar.png
[10]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-prepare-line.png
[11]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-line.png
[12]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-save.png

<!--Article references-->
[migration]: sql-data-warehouse-overview-migrate.md
[développement]: sql-data-warehouse-overview-develop.md
[chargement]: sql-data-warehouse-overview-load.md
[charger manuellement des exemples de données]: sql-data-warehouse-load-sample-databases.md
[connecting to SQL Data Warehouse]: sql-data-warehouse-integrate-power-bi.md
[Créer un Azure SQL Data Warehouse]: sql-data-warehouse-get-started-provision.md

<!--Other-->
[portail Azure]: https://portal.azure.com/
[site web de Power BI]: http://www.powerbi.com/

<!---HONumber=AcomDC_0622_2016-->