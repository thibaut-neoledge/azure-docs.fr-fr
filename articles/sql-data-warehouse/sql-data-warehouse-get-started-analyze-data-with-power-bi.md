<properties
    pageTitle="Analyse des données SQL Data Warehouse avec Power BI | Microsoft Azure"
    description="Analyse des données SQL Data Warehouse avec Power BI"
    services="sql-data-warehouse"
    documentationCenter="NA"
    authors="lodipalm"
    manager="barbkess"
    editor=""/>

<tags
    ms.service="sql-data-warehouse"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="NA"
    ms.workload="data-services"
    ms.date="10/07/2015"
    ms.author="lodipalm"/>

# Analyse des données avec Power BI
Ce didacticiel vous montre comment utiliser Power BI pour vous connecter SQL Data Warehouse et créer quelques visualisations de base.

> [AZURE.NOTE]Pour suivre ce didacticiel, vous avez besoin d'une base de données SQL Data Warehouse préchargée avec l’exemple de base de données AdventureWorksDW. La section [Création d’une base de données SQL Data Warehouse](sql-data-warehouse-get-started-provision.md) vous montre comment en créer une.
> 
> Si vous avez déjà une base de données SQL Data Warehouse mais que vous ne disposez pas d'exemples de données, vous pouvez [charger manuellement des exemples de données][].


## Connexion à la base de données SQL Data Warehouse

Pour ouvrir Power BI et vous connecter à votre base de données AdventureWorksDW :

1. Connectez-vous au [portail Azure en version préliminaire][].
2. Cliquez sur **Bases de données SQL** et choisissez votre base de données SQL Data Warehouse AdventureWorks. 

    ![Recherche de votre base de données][1]

3. Cliquez sur le bouton Open in Power BI.

    ![Bouton Power BI][2]

4. La page de connexion de la base de données SQL Data Warehouse devrait afficher les informations concernant votre base de données. Entrez votre mot de passe pour vous connecter à votre base de données SQL Data Warehouse.


## Analyse des données avec Power BI

Vous êtes maintenant prêt à utiliser Power BI pour analyser vos exemples de données AdventureWorksDW. Pour effectuer l'analyse, AdventureWorksDW dispose d'une vue appelée AggregateSales. Cette vue contient quelques-unes des métriques essentielles à l'analyse des ventes de l'entreprise.

1. Pour créer un mappage du montant des ventes par code postal, cliquez sur la vue AggregateSales afin de l’agrandir, puis cliquez sur les colonnes PostalCode et SalesAmount. Power BI reconnaît automatiquement ces données comme des données géographiques et les intègre dans une carte.

    ![Carte Power BI][3]

2. Pour créer un histogramme des ventes, cliquez sur la colonne SalesAmount. Pour ajouter plus de détails, faites glisser le tableau CustomerIncome sur le champ Axis à gauche de AggregateSales afin d’afficher les ventes par revenus clients.

    ![Barre Power BI][4]

3. Pour créer une chronologie des ventes, cliquez sur SalesAmount, OrderDate et Line Chart, la première icône dans la deuxième ligne sous Visualizations.

    ![Ligne Power BI][5]

Vous pouvez enregistrer votre progression à tout moment en cliquant sur le bouton **ENREGISTRER** dans le coin supérieur gauche et en enregistrant vos visualisations sous forme de rapports.

## Étapes suivantes
Après vous être familiarisé avec les exemples de données, vous pouvez découvrir les opérations de [développement][], de [chargement][] ou de [migration][].

<!--Image references-->
[1]: ./media/sql-data-warehouse-get-started-analyze-data-with-power-bi/pbi-find-database.png
[2]: ./media/sql-data-warehouse-get-started-analyze-data-with-power-bi/pbi-button.png
[3]: ./media/sql-data-warehouse-get-started-analyze-data-with-power-bi/pbi-map.png
[4]: ./media/sql-data-warehouse-get-started-analyze-data-with-power-bi/pbi-bar.png
[5]: ./media/sql-data-warehouse-get-started-analyze-data-with-power-bi/pbi-line.png

<!--Article references-->
[migration]: sql-data-warehouse-overview-migrate.md
[développement]: sql-data-warehouse-overview-develop.md
[chargement]: sql-data-warehouse-overview-load.md
[charger manuellement des exemples de données]: sql-data-warehouse-get-started-manually-load-samples.md
[portail Azure en version préliminaire]: https://portal.azure.com/
[Power BI]: http://www.powerbi.com/
[connecting to SQL Data Warehouse]: sql-data-warehouse-integrate-power-bi.md
[Create a SQL Data Warehouse]: sql-data-warehouse-get-started-provision.md

<!---HONumber=Oct15_HO2-->