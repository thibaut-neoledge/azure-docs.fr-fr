<properties 
	pageTitle="Connexion à une base de données SQL à l'aide de Python avec pymssql sous Ubuntu" 
	description="Cette rubrique présente un exemple de code Python que vous pouvez utiliser pour vous connecter à la base de données SQL Azure. L'exemple s'exécute sur un ordinateur client Ubuntu Linux."
	services="sql-database" 
	documentationCenter="" 
	authors="meet-bhagdev" 
	manager="jeffreyg" 
	editor="genemi"/>


<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="na" 
	ms.devlang="python" 
	ms.topic="article" 
	ms.date="05/19/2015" 
	ms.author="mebha"/>


# Connexion à une base de données SQL à l'aide de Python sous Ubuntu Linux


[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]


Cette rubrique présente un exemple de code Python qui s'exécute sur un ordinateur client Ubuntu Linux, pour la connexion à une base de données SQL Azure.


## Configuration requise


- [Python 2.7.6](https://www.python.org/download/releases/2.7.6/).


### Installer les modules requis


Ouvrez votre terminal et accédez au répertoire dans lequel vous envisagez de créer votre script python. Entrez les commandes suivantes pour installer **FreeTDS** et **pymssql**. pymssql utilise FreeTDS pour se connecter aux bases de données SQL.

	sudo apt-get --assume-yes update
	sudo apt-get --assume-yes install freetds-dev freetds-bin
	sudo apt-get --assume-yes install python-dev python-pip
	sudo pip install pymssql


### Créer une base de données et récupérer la chaîne de connexion


Consultez la [page de prise en main](sql-database-get-started.md) pour découvrir comment créer un exemple de base de données et obtenir votre chaîne de connexion. Il est important que vous suiviez le guide pour créer un **modèle de base de données AdventureWorks**. Les exemples ci-dessous fonctionnent uniquement avec le **schéma AdventureWorks**.


## Se connecter à la base de données SQL


La fonction [pymssql.connect](http://pymssql.org/en/latest/ref/pymssql.html) est utilisée pour la connexion à la base de données SQL.

	import pymssql
	conn = pymssql.connect(server='yourserver.database.windows.net', user='yourusername@yourserver', password='yourpassword', database='AdventureWorks')


## Exécuter une instruction SQL SELECT

La fonction [cursor.execute](http://pymssql.org/en/latest/ref/pymssql.html#pymssql.Cursor.execute) peut être utilisée pour récupérer un jeu de résultats d'une requête à partir d'une base de données SQL. Cette fonction accepte n'importe quelle requête et renvoie un jeu de résultats qui peut être itéré à l'aide de [cursor.fetchone()](http://pymssql.org/en/latest/ref/pymssql.html#pymssql.Cursor.fetchone).


	import pymssql
	conn = pymssql.connect(server='yourserver.database.windows.net', user='yourusername@yourserver', password='yourpassword', database='AdventureWorks')
	cursor = conn.cursor()
	cursor.execute('SELECT c.CustomerID, c.CompanyName,COUNT(soh.SalesOrderID) AS OrderCount FROM SalesLT.Customer AS c LEFT OUTER JOIN SalesLT.SalesOrderHeader AS soh ON c.CustomerID = soh.CustomerID GROUP BY c.CustomerID, c.CompanyName ORDER BY OrderCount DESC;')
	row = cursor.fetchone()
	while row:
	    print str(row[0]) + " " + str(row[1]) + " " + str(row[2]) 	
	    row = cursor.fetchone()


## Insérer une ligne, transmettre des paramètres et récupérer la clé primaire générée

Dans la base de données SQL, la propriété [IDENTITY](https://msdn.microsoft.com/library/ms186775.aspx) et l'objet [SEQUENCE](https://msdn.microsoft.com/library/ff878058.aspx) peuvent être utilisés pour générer automatiquement des valeurs de [clé primaire](https://msdn.microsoft.com/library/ms179610.aspx).


	import pymssql
	conn = pymssql.connect(server='yourserver.database.windows.net', user='yourusername@yourserver', password='yourpassword', database='AdventureWorks')
	cursor = conn.cursor()
	cursor.execute("INSERT SalesLT.Product (Name, ProductNumber, StandardCost, ListPrice, SellStartDate) OUTPUT INSERTED.ProductID VALUES ('SQL Server Express', 'SQLEXPRESS', 0, 0, CURRENT_TIMESTAMP)")
	row = cursor.fetchone()
	while row:
	    print "Inserted Product ID : " +str(row[0])
	    row = cursor.fetchone()


## Transactions


Cet exemple de code illustre l'utilisation de transactions dans lesquelles vous :


-Commencez une transaction

-Insérez une ligne de données

-Restaurez la transaction pour annuler l'insertion


	import pymssql
	conn = pymssql.connect(server='yourserver.database.windows.net', user='yourusername@yourserver', password='yourpassword', database='AdventureWorks')
	cursor = conn.cursor()
	cursor.execute("BEGIN TRANSACTION")
	cursor.execute("INSERT SalesLT.Product (Name, ProductNumber, StandardCost, ListPrice, SellStartDate) OUTPUT INSERTED.ProductID VALUES ('SQL Server Express New', 'SQLEXPRESS New', 0, 0, CURRENT_TIMESTAMP)")
	cnxn.rollback()

<!---HONumber=58--> 