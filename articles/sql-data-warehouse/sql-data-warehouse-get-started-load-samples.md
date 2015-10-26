<properties
   pageTitle="Charger des exemples de données dans SQL Data Warehouse | Microsoft Azure"
   description="Charger des exemples de données dans SQL Data Warehouse"
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
   ms.date="09/23/2015"
   ms.author="lodipalm;barbkess"/>

#Charger des exemples de données dans SQL Data Warehouse

Pendant que vous créez votre instance de SQL Data Warehouse, vous pouvez facilement y charger des exemples de données. Si vous avez manqué cette étape lors du déploiement, vous pouvez également [charger manuellement les exemples de données][].

Les éléments suivants vous donnent un bref aperçu de la façon dont AdventureWorksDW peut être chargé dans votre base de données. Ce jeu de données modélise un exemple de structure d’entrepôt de données pour une société fictive nommée AdventureWorks avec des données représentant les ventes et les clients de la société.

## Ajout d’exemples de données lors de la création
Pour garantir que les exemples de données sont chargés dans votre SQL Data Warehouse lors du déploiement, procédez comme suit :

1. Démarrez le processus de création en recherchant SQL Data Warehouse dans le [portail Azure][] en cliquant sur « + Nouveau », puis « Données et stockage » ou sur le marché en recherchant « SQL Data Warehouse ». 
 
2. Une fois que le processus est démarré, cliquez sur l’option « Sélectionner la source » et affectez-lui la valeur « Exemple ». Si vous ne créez pas de nouveau serveur, vous serez également invité à indiquer le mot de passe d’accès au serveur utilisé pour la création.


> [AZURE.NOTE]Pour charger les exemples de données dans votre instance, vous devrez permettre aux services Azure d’accéder à votre serveur (cette option devrait être activée par défaut lors de la création d’un nouveau serveur). Dans le cas contraire, le chargement échoue, mais vous pourrez toujours [charger manuellement des exemples de données][].


##Utilisation de Power BI pour analyser Adventureworks

Utiliser le jeu d’exemples de données peut être un excellent moyen de prendre en main Power BI. Après avoir chargé les exemples de données, vous pouvez ouvrir une connexion à SQL Data Warehouse en cliquant sur le bouton « Ouvrir dans Power BI » dans le portail Azure ou en accédant à [Power BI][] et en vous [connectant à SQL Data Warehouse][]. Une fois que vous êtes connecté, un nouveau jeu de données doit être créé avec le même nom que celui de votre entrepôt de données. Pour faciliter l’analyse, nous avons créé une vue appelée « AggregateSales » avec quelques-unes des mesures essentielles pour analyser les ventes de la société. Vous pouvez cliquer sur le nom de cette vue pour la développer et afficher les colonnes qu’elle contient et vous pouvez créer des visualisations rapides en suivant ces étapes :

1. Pour commencer, nous pouvons facilement créer une carte de toutes nos ventes en cliquant sur les colonnes « PostalCode » et « SalesAmount ». Power BI reconnaît même automatiquement ces données comme des données géographiques et les intègre dans une carte. 

2. À présent, si vous souhaitez créer un histogramme des ventes, il vous suffit de cliquer sur la colonne « SalesAmount » et Power BI le crée automatiquement pour vous. Vous pouvez ajouter une profondeur supplémentaire en faisant glisser le tableau « CustomerIncome » sur le champ « Axis » à gauche de « AggregateSales » pour afficher les ventes par tranches de revenus clients.

3. Enfin, si vous souhaitez créer une chronologie des ventes, il vous suffit de cliquer sur « SalesAmount », « OrderDate » et « Line Chart » (la première icône dans la deuxième ligne sous « Visualizations »).

Vous pouvez enregistrer votre progression à tout moment en cliquant sur le bouton « ENREGISTRER » dans le coin supérieur gauche et en enregistrant vos visualisations sous forme de rapports.

## Connexion à votre exemple et interrogation de ce dernier

Vous pouvez également analyser les exemples de données avec des moyens traditionnels. Comme l’indique la documentation sur [la connexion et la requête][], vous pouvez vous connecter à cette base de données à l’aide de SQL Server Data Tools dans Visual Studio. Des exemples de données étant chargés dans SQL Data Warehouse, vous pouvez exécuter rapidement quelques requêtes pour commencer.

Nous pouvons exécuter une instruction select simple pour obtenir toutes les informations des employés :

	SELECT * FROM DimEmployee;

Nous pouvons également exécuter une requête plus complexe à l’aide de constructions telles que GROUP BY pour examiner le montant total de toutes les ventes chaque jour :

	SELECT OrderDateKey, SUM(SalesAmount) AS TotalSales
	FROM FactInternetSales
	GROUP BY OrderDateKey
	ORDER BY OrderDateKey;

Nous pouvons même utiliser la clause WHERE pour filtrer les commandes antérieures à une date donnée :

	SELECT OrderDateKey, SUM(SalesAmount) AS TotalSales
	FROM FactInternetSales
	WHERE OrderDateKey > '20020801'
	GROUP BY OrderDateKey
	ORDER BY OrderDateKey;

De fait, SQL Data Warehouse prend parfaitement en charge presque toutes les constructions T-SQL de SQL Server. Certaines des différences sont indiquées dans notre documentation sur la [code de migration][].



## Étapes suivantes
Après vous être familiarisé avec les exemples de données, vous pouvez découvrir les opérations de [développement][], de [chargement][] ou de [migration][].

<!--Image references-->

<!--Article references-->
[migration]: https://azure.microsoft.com/fr-FR/documentation/articles/sql-data-warehouse-overview-migrate/
[développement]: https://azure.microsoft.com/fr-FR/documentation/articles/sql-data-warehouse-overview-develop/
[chargement]: https://azure.microsoft.com/fr-FR/documentation/articles/sql-data-warehouse-overview-load/
[la connexion et la requête]: https://azure.microsoft.com/fr-FR/documentation/articles/sql-data-warehouse-get-started-connect-query/
[code de migration]: https://azure.microsoft.com/fr-FR/documentation/articles/sql-data-warehouse-migrate-code/
[charger manuellement des exemples de données]: https://azure.microsoft.com/fr-FR/documentation/articles/sql-data-warehouse-get-started-manually-load-samples/
[charger manuellement les exemples de données]: https://azure.microsoft.com/fr-FR/documentation/articles/sql-data-warehouse-get-started-manually-load-samples/
[portail Azure]: https://portal.azure.com
[Power BI]: http://www.powerbi.com
[connectant à SQL Data Warehouse]: https://azure.microsoft.com/fr-FR/documentation/articles/sql-data-warehouse-integrate-power-bi/

<!--MSDN references-->
[Microsoft Command Line Utilities for SQL Server]: http://www.microsoft.com/fr-FR/download/details.aspx?id=36433

<!--Other Web references-->
[Sample Data Scripts]: https://migrhoststorage.blob.core.windows.net/sqldwsample/AdventureWorksPDW2012.zip

<!---HONumber=Oct15_HO3-->