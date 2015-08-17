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
   ms.date="08/05/2015"
   ms.author="lodipalm;barbkess"/>


#Charger des exemples de données dans SQL Data Warehouse

Une instance de SQL Data Warehouse étant configurée, vous pouvez facilement y charger des exemples de données. Les informations ci-après vous aident à créer un jeu de données appelé AdventureWorksPDW2012 dans votre base de données. Ce jeu de données modélise un exemple de structure d’entrepôt de données pour une société fictive nommée AdventureWorks. Notez que BCP doit être installé pour les étapes suivantes. Si ce n’est pas le cas, vous devez installer les [utilitaires de ligne de commande Microsoft pour SQL Server][].

1. Pour commencer, cliquez pour télécharger nos [exemples de scripts de données][].

2. Une fois le fichier téléchargé, extrayez le contenu du fichier AdventureWorksPDW2012.zip et ouvrez le nouveau dossier AdventureWorksPDW2012.

3. Modifiez le fichier aw\_create.bat et définissez les valeurs suivantes au début du fichier : a. **Server** : nom complet du serveur où réside SQL Data Warehouse ; b. **User** : utilisateur associé au serveur ci-dessus ; c. **Password** : mot de passe d’accès au serveur ; d. **Database** : nom de l’instance de SQL Data Warehouse sur laquelle charger des données.

4. Exécutez aw\_create.bat à partir du répertoire dans lequel il se trouve. Cela crée le schéma et charge les données dans toutes les tables à l’aide de BCP.


## Connexion à votre exemple et interrogation de ce dernier

Comme décrit dans la documentation sur [la connexion et l’interrogation][], vous pouvez vous connecter à cette base de données à l’aide de Visual Studio et de SSDT. Des exemples de données étant chargés dans SQL Data Warehouse, vous pouvez exécuter rapidement quelques requêtes pour commencer.

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

De fait, SQL Data Warehouse prend en charge pratiquement les mêmes constructions T-SQL que SQL Server ; certaines des différences sont indiquées dans notre documentation sur la [migration du code][].

## Étapes suivantes
Après vous être familiarisé avec les exemples de données, vous pouvez découvrir les opérations de [développement][], de [chargement][] ou de [migration][].

<!--Image references-->

<!--Article references-->
[migration]: https://azure.microsoft.com/fr-fr/documentation/articles/sql-data-warehouse-overview-migrate/
[développement]: https://azure.microsoft.com/fr-fr/documentation/articles/sql-data-warehouse-overview-develop/
[chargement]: https://azure.microsoft.com/fr-fr/documentation/articles/sql-data-warehouse-overview-load/
[la connexion et l’interrogation]: https://azure.microsoft.com/fr-fr/documentation/articles/sql-data-warehouse-get-started-connect-query/
[migration du code]: https://azure.microsoft.com/fr-fr/documentation/articles/sql-data-warehouse-migrate-code/

<!--MSDN references-->
[utilitaires de ligne de commande Microsoft pour SQL Server]: http://www.microsoft.com/fr-fr/download/details.aspx?id=36433

<!--Other Web references-->
[exemples de scripts de données]: https://migrhoststorage.blob.core.windows.net/sqldwsample/AdventureWorksPDW2012.zip

<!---HONumber=August15_HO6-->