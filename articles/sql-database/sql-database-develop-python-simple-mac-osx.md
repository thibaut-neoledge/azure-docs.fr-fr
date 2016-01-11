<properties
	pageTitle="Connexion à SQL Database à l’aide de Python sous Mac OS"
	description="Cette rubrique présente un exemple de code Python que vous pouvez utiliser pour vous connecter à Azure SQL Database à partir d’un ordinateur Mac. L'exemple utilise le pilote pymssql."
	services="sql-database"
	documentationCenter=""
	authors="meet-bhagdev"
	manager="jeffreyg"
	editor=""/>


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="python"
	ms.topic="article"
	ms.date="12/17/2015"
	ms.author="meetb"/>


# Connexion à SQL Database à l’aide de Python sous Mac OS


> [AZURE.SELECTOR]
- [Node.js](sql-database-develop-nodejs-simple-mac.md)
- [Python](sql-database-develop-python-simple-mac-osx.md)
- [Ruby](sql-database-develop-ruby-simple-mac-osx.md)


Cette rubrique présente un exemple de code écrit dans Python. L’exemple s’exécute sur un ordinateur Mac. L’exemple se connecte à une base de données SQL Azure à l’aide du pilote **pymssql**. En outre, veuillez utiliser notre vidéo [Prise en main de Python sur Mac](https://www.youtube.com/watch?v=OMpugPTwnTI) pour compléter cette documentation.


## Composants requis


- [Python 2.7.6](https://www.python.org/download/releases/2.7.6/).
- [FreeTDS](https://github.com/brianb/FreeTDS)
- [Pymssql](https://github.com/pymssql/pymssql)

### Installer les modules requis


Ouvrez votre terminal et procédez aux installations suivantes :

**1) Homebrew** : exécutez la commande suivante à partir de votre terminal. Cette commande entraîne le téléchargement du gestionnaire de package Homebrew sur votre ordinateur.

    ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"

**2) FreeTDS** : exécutez la commande suivante à partir de votre terminal. Cette commande entraîne le téléchargement de FreeTDS sur votre ordinateur. FreeTDS est requis pour que pymmsql fonctionne correctement.

    brew install FreeTDS

**3) Pymmsql** : exécutez la commande suivante à partir de votre terminal. Cette commande entraîne l’installation de pymmsql sur votre ordinateur.

    sudo -H pip install pymssql

### Base de données SQL

Consultez la [page de prise en main](sql-database-get-started.md) pour apprendre à créer un exemple de base de données. Il est important que vous suiviez le guide pour créer un **modèle de base de données AdventureWorks**. Les exemples ci-dessous fonctionnent uniquement avec le **schéma AdventureWorks**.

## Étape 1 : obtenir les informations de connexion

[AZURE.INCLUDE [sql-database-include-connection-string-details-20-portalshots](../../includes/sql-database-include-connection-string-details-20-portalshots.md)]

## Étape 2 : se connecter

La fonction [pymssql.connect](http://pymssql.org/en/latest/ref/pymssql.html) est utilisée pour la connexion à la base de données SQL.

	import pymssql
	conn = pymssql.connect(server='yourserver.database.windows.net', user='yourusername@yourserver', password='yourpassword', database='AdventureWorks')


## Étape 3 : exécuter une requête

La fonction [cursor.execute](http://pymssql.org/en/latest/ref/pymssql.html#pymssql.Cursor.execute) peut être utilisée pour récupérer un jeu de résultats d'une requête à partir d'une base de données SQL. Cette fonction accepte n'importe quelle requête et renvoie un jeu de résultats qui peut être itéré à l'aide de [cursor.fetchone()](http://pymssql.org/en/latest/ref/pymssql.html#pymssql.Cursor.fetchone).


	import pymssql
	conn = pymssql.connect(server='yourserver.database.windows.net', user='yourusername@yourserver', password='yourpassword', database='AdventureWorks')
	cursor = conn.cursor()
	cursor.execute('SELECT c.CustomerID, c.CompanyName,COUNT(soh.SalesOrderID) AS OrderCount FROM SalesLT.Customer AS c LEFT OUTER JOIN SalesLT.SalesOrderHeader AS soh ON c.CustomerID = soh.CustomerID GROUP BY c.CustomerID, c.CompanyName ORDER BY OrderCount DESC;')
	row = cursor.fetchone()
	while row:
	    print str(row[0]) + " " + str(row[1]) + " " + str(row[2]) 	
	    row = cursor.fetchone()


## Étape 4 : insérer une ligne

Dans cet exemple, vous allez découvrir comment exécuter une instruction [INSERT](https://msdn.microsoft.com/library/ms174335.aspx) en toute sécurité, passer des paramètres pour protéger votre application des vulnérabilités découlant de [l’injection de code SQL] (https://technet.microsoft.com/library/ms161953(v=sql.105).aspx) et récupérer la valeur de la [Clé primaire](https://msdn.microsoft.com/library/ms179610.aspx) générée automatiquement.



	import pymssql
	conn = pymssql.connect(server='yourserver.database.windows.net', user='yourusername@yourserver', password='yourpassword', database='AdventureWorks')
	cursor = conn.cursor()
	cursor.execute("INSERT SalesLT.Product (Name, ProductNumber, StandardCost, ListPrice, SellStartDate) OUTPUT INSERTED.ProductID VALUES ('SQL Server Express', 'SQLEXPRESS', 0, 0, CURRENT_TIMESTAMP)")
	row = cursor.fetchone()
	while row:
	    print "Inserted Product ID : " +str(row[0])
	    row = cursor.fetchone()


## Étape 5 : restaurer une transaction


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


## Étapes suivantes

Pour plus d’informations, consultez le [Centre pour développeurs Python](/develop/python/).

<!---HONumber=AcomDC_1223_2015-->