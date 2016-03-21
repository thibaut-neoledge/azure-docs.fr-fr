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
   ms.date="03/03/2016"
   ms.author="lodipalm;barbkess;sonyama"/>

#Charger des exemples de données dans SQL Data Warehouse

Une fois que vous avez [créé une instance de base de données SQL Data Warehouse][create a SQL Data Warehouse database instance] l'étape suivante consiste à créer et charger des tables. Vous pouvez utiliser les exemples de scripts Adventure Works que nous avons créés pour SQL Data Warehouse pour créer et charger des tables pour la société fictive appelée Adventure Works. Ces scripts utilisent sqlcmd pour exécuter SQL et bcp pour charger les données. Si vous ne disposez pas encore de ces outils, suivez ces liens pour [installer bcp][] et [installer sqlcmd][].

Suivez ces étapes simples pour charger l'exemple de base de données Adventure Works dans SQL DW...

1. Téléchargez [Exemples de scripts Adventure Works pour SQL Data Warehouse][].

2. Extrayez les fichiers du fichier zip téléchargé dans un répertoire sur votre ordinateur local.

3. Modifiez le fichier extrait aw\_create.bat et définissez les variables suivantes au début du fichier. Veillez à ne laisser aucun espace entre le « = » et le paramètre. Vous trouverez ci-dessous des exemples de ce à quoi vos modifications peuvent ressembler.

    	server=mylogicalserver.database.windows.net
    	user=mydwuser
    	password=Mydwpassw0rd
    	database=mydwdatabase

4. À partir d'une invite de commande Windows, exécutez le fichier aw\_create.bat modifié. Assurez-vous que vous êtes dans le répertoire où vous avez enregistré votre version modifiée du fichier aw\_create.bat. Ce script va...
	* Supprimer les tables ou vues Adventure Works qui existent déjà dans votre base de données.
	* Créer les tables et les vues Adventure Works.
	* Charger chaque table Adventure Works à l'aide de bcp.
	* Valider le nombre de lignes pour chaque table Adventure Works.
	* Collecter les statistiques sur chaque colonne pour chaque table Adventure Works.


##Interroger vos exemples de données.

Lorsque vous avez chargé des exemples de données dans SQL Data Warehouse, vous pouvez exécuter rapidement quelques requêtes. Pour exécuter une requête, connectez-vous à votre base de données Adventure Works nouvellement créée Azure SQL DW avec Visual Studio et SSDT, comme décrit dans le document [de connexion][].

Exemple d'instruction simple select pour obtenir toutes les informations des employés :

	SELECT * FROM DimEmployee;

Exemple de requête plus complexe à l'aide de constructions telles que GROUP BY pour examiner le montant total de toutes les ventes chaque jour :

	SELECT OrderDateKey, SUM(SalesAmount) AS TotalSales
	FROM FactInternetSales
	GROUP BY OrderDateKey
	ORDER BY OrderDateKey;

Exemple d'instruction SELECT avec une clause WHERE pour filtrer les commandes antérieures à une date donnée :

	SELECT OrderDateKey, SUM(SalesAmount) AS TotalSales
	FROM FactInternetSales
	WHERE OrderDateKey > '20020801'
	GROUP BY OrderDateKey
	ORDER BY OrderDateKey;

SQL Data Warehouse prend en charge presque toutes les constructions T-SQL prises en charge par SQL Server. Toutes les différences sont documentées dans notre documentation sur la [migration du code][].

## Étapes suivantes
Maintenant que vous avez eu l'occasion d'essayer certaines requêtes avec des exemples de données, découvrez comment [développer][], [charger][] ou [migrer][] vers SQL Data Warehouse.

<!--Image references-->

<!--Article references-->
[migrer]: ./sql-data-warehouse-overview-migrate.md
[développer]: ./sql-data-warehouse-overview-develop.md
[charger]: ./sql-data-warehouse-overview-load.md
[de connexion]: ./sql-data-warehouse-get-started-connect.md
[migration du code]: ./sql-data-warehouse-migrate-code.md
[create a SQL Data Warehouse database instance]: ./sql-data-warehouse-get-started-provision.md
[installer bcp]: ./sql-data-warehouse-load-with-bcp.md
[installer sqlcmd]: ./sql-data-warehouse-get-started-connect-query-sqlcmd.md

<!--Other Web references-->
[Exemples de scripts Adventure Works pour SQL Data Warehouse]: https://migrhoststorage.blob.core.windows.net/sqldwsample/AdventureWorksSQLDW2012.zip

<!---HONumber=AcomDC_0309_2016-->