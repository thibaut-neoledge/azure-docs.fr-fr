<properties 
	pageTitle="Connexion à SQL Database à l'aide de Node.js avec Tedious sous Ubuntu Linux" 
	description="Cette rubrique présente un exemple de code Node.js que vous pouvez utiliser pour vous connecter à Azure SQL Database. L'exemple utilise le pilote Tedious pour se connecter."
	services="sql-database" 
	documentationCenter="" 
	authors="meet-bhagdev" 
	manager="jeffreyg" 
	editor=""/>


<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="na" 
	ms.devlang="nodejs" 
	ms.topic="article" 
	ms.date="07/20/2015" 
	ms.author="mebha"/>


# Connexion à une base de données SQL à l'aide de Node.js avec Tedious sous Ubuntu Linux


[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]


Cette rubrique présente un exemple de code Node.js qui s'exécute sous Ubuntu Linux. L'exemple se connecte à Azure SQL Database à l'aide du pilote Tedious.


## Éléments logiciels requis


Ouvrez votre terminal et installez **node** et **npm**, si ce n'est pas déjà fait.


	sudo apt-get install node
	sudo apt-get install npm


Une fois votre ordinateur configuré avec **node** et **npm**, accédez au répertoire où vous envisagez de créer votre projet Node.js et entrez les commandes suivantes.


	sudo npm init
	sudo npm install tedious


**npm init** crée un projet de nœud. Pour conserver les valeurs par défaut lors de la création de votre projet, appuyez sur Entrée jusqu'à ce que le projet soit créé. Le fichier **package.json** s'affiche dans le répertoire du projet.


### Créer une base de données AdventureWorks


L'exemple de code dans cette rubrique attend une base de données de test **AdventureWorks**. Si vous n'en avez pas déjà une, consultez [Prise en main d'une base de données SQL](sql-database-get-started.md). Il est important que vous suivez le guide pour créer un **modèle de base de données AdventureWorks**. Les exemples ci-dessous fonctionnent uniquement avec le **schéma AdventureWorks**.


## Se connecter à la base de données SQL

La fonction de [nouvelle connexion](http://pekim.github.io/tedious/api-connection.html) est utilisée pour la connexion à la base de données SQL.

	var Connection = require('tedious').Connection;
	var config = {
		userName: 'yourusername',
		password: 'yourpassword',
		server: 'yourserver.database.windows.net',
		// If you are on Microsoft Azure, you need this:
		options: {encrypt: true, database: 'AdventureWorks'}
	};
	var connection = new Connection(config);
	connection.on('connect', function(err) {
	// If no error, then good to proceed.
		console.log("Connected");
	});


## Exécuter une instruction SQL SELECT


Toutes les instructions SQL sont exécutées à l'aide de la fonction [new Request()](http://pekim.github.io/tedious/api-request.html). Si l'instruction renvoie des lignes, par exemple une instruction select, vous pouvez les récupérer à l'aide de la fonction [request.on()](http://pekim.github.io/tedious/api-request.html). S'il n'y a aucune ligne, la fonction [request.on()](http://pekim.github.io/tedious/api-request.html) renvoie des listes vides.


	var Connection = require('tedious').Connection;
	var config = {
		userName: 'yourusername',
		password: 'yourpassword',
		server: 'yourserver.database.windows.net',
		// When you connect to Azure SQL Database, you need these next options.
		options: {encrypt: true, database: 'AdventureWorks'}
	};
	var connection = new Connection(config);
	connection.on('connect', function(err) {
		// If no error, then good to proceed.
		console.log("Connected");
		executeStatement();
	});
	
	var Request = require('tedious').Request;
	var TYPES = require('tedious').TYPES;
	
	function executeStatement() {
		request = new Request("SELECT c.CustomerID, c.CompanyName,COUNT(soh.SalesOrderID) AS OrderCount FROM SalesLT.Customer AS c LEFT OUTER JOIN SalesLT.SalesOrderHeader AS soh ON c.CustomerID = soh.CustomerID GROUP BY c.CustomerID, c.CompanyName ORDER BY OrderCount DESC;", function(err) {
	  	if (err) {
	   		console.log(err);} 
		});
		var result = "";
		request.on('row', function(columns) {
		    columns.forEach(function(column) {
		      if (column.value === null) {
		        console.log('NULL');
		      } else {
		        result+= column.value + " ";
		      }
		    });
		    console.log(result);
		    result ="";
		});
	
		request.on('done', function(rowCount, more) {
		console.log(rowCount + ' rows returned');
		});
		connection.execSql(request);
	}


## Insérer une ligne, appliquer les paramètres et récupérer la clé primaire générée


Dans la base de données SQL, la propriété [IDENTITY](https://msdn.microsoft.com/library/ms186775.aspx) et l’objet [SEQUENCE](https://msdn.microsoft.com/library/ff878058.aspx) peuvent être utilisés pour générer automatiquement des valeurs de [clé primaire](https://msdn.microsoft.com/library/ms179610.aspx). Dans cet exemple, vous verrez comment procéder pour exécuter une instruction insert, transmettre des paramètres en toute sécurité pour une protection contre une injection SQL et récupérer la valeur de la clé primaire générée automatiquement.


L'exemple de code dans cette section applique des paramètres à une instruction SQL INSERT. La valeur de clé primaire qui est générée est récupérée par le programme.


	var Connection = require('tedious').Connection;
	var config = {
		userName: 'yourusername',
		password: 'yourpassword',
		server: 'yourserver.database.windows.net',
		// If you are on Azure SQL Database, you need these next options.
		options: {encrypt: true, database: 'AdventureWorks'}
	};
	var connection = new Connection(config);
	connection.on('connect', function(err) {
		// If no error, then good to proceed.
		console.log("Connected");
		executeStatement1();
	});
	
	var Request = require('tedious').Request
	var TYPES = require('tedious').TYPES;
	
	function executeStatement1() {
		request = new Request("INSERT SalesLT.Product (Name, ProductNumber, StandardCost, ListPrice, SellStartDate) OUTPUT INSERTED.ProductID VALUES (@Name, @Number, @Cost, @Price, CURRENT_TIMESTAMP);", function(err) {
		 if (err) {
		 	console.log(err);} 
		});
		request.addParameter('Name', TYPES.NVarChar,'SQL Server Express 2014');
		request.addParameter('Number', TYPES.NVarChar , 'SQLEXPRESS2014');
		request.addParameter('Cost', TYPES.Int, 11);
		request.addParameter('Price', TYPES.Int,11);
		request.on('row', function(columns) {
		    columns.forEach(function(column) {
		      if (column.value === null) {
		        console.log('NULL');
		      } else {
		        console.log("Product id of inserted item is " + column.value);
		      }
		    });
		});		
		connection.execSql(request);
	}

 

<!---HONumber=Oct15_HO3-->