<properties
	pageTitle="Se connecter à SQL Database à l’aide de Node.js avec Tedious sous Mac OS X"
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
	ms.date="12/17/2015"
	ms.author="meetb"/>


# Se connecter à SQL Database à l’aide de Node.js avec Tedious sous Mac OS X


[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]


Cette rubrique présente un exemple de code Node.js qui s’exécute sur Mac OS X. L’exemple se connecte à Azure SQL Database en utilisant le pilote Tedious.


## Composants requis


Installez le **nœud**, sauf s’il est déjà installé sur votre ordinateur.


Pour installer node.js sous OSX 10.10 Yosemite, vous pouvez télécharger un package binaire précompilé, ce qui permet une installation facile. [Allez sur nodejs.org](http://nodejs.org/) et cliquez sur le bouton Installer pour télécharger le dernier package.

Installez le package à partir du fichier .dmg en suivant les instructions de l’Assistant d’installation qui installera le **nœud** et **npm**. NPM signifie Node Package Manager et facilite l’installation des packages supplémentaires pour node.js.


Une fois votre ordinateur configuré avec **node** et **npm**, accédez au répertoire où vous envisagez de créer votre projet Node.js et entrez les commandes suivantes.


	npm init
	npm install tedious


**npm init** crée un projet de nœud. Pour conserver les valeurs par défaut lors de la création de votre projet, appuyez sur Entrée jusqu'à ce que le projet soit créé. Le fichier **package.json** s'affiche dans le répertoire du projet.

### Base de données SQL

Consultez la [page de prise en main](sql-database-get-started.md) pour apprendre à créer un exemple de base de données. Il est important que vous suiviez le guide pour créer un **modèle de base de données AdventureWorks**. Les exemples ci-dessous fonctionnent uniquement avec le **schéma AdventureWorks**.

## Étape 1 : obtenir les informations de connexion

[AZURE.INCLUDE [sql-database-include-connection-string-details-20-portalshots](../../includes/sql-database-include-connection-string-details-20-portalshots.md)]

## Étape 2 : se connecter

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


## Étape 3 : exécuter une requête


Toutes les instructions SQL sont exécutées à l'aide de la fonction [new Request()](http://pekim.github.io/tedious/api-request.html). Si l'instruction renvoie des lignes, par exemple une instruction select, vous pouvez les récupérer à l'aide de la fonction [request.on()](http://pekim.github.io/tedious/api-request.html). S'il n'y a aucune ligne, la fonction [request.on()](http://pekim.github.io/tedious/api-request.html) renvoie des listes vides.


	var Connection = require('tedious').Connection;
	var Request = require('tedious').Request;
	var TYPES = require('tedious').TYPES;
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


## Étape 4 : insérer une ligne

Dans cet exemple, vous allez découvrir comment exécuter une instruction [INSERT](https://msdn.microsoft.com/library/ms174335.aspx) en toute sécurité, passer des paramètres pour protéger votre application des vulnérabilités découlant de [l’injection de code SQL](https://technet.microsoft.com/library/ms161953(v=sql.105).aspx) et récupérer la valeur de la [Clé primaire](https://msdn.microsoft.com/library/ms179610.aspx) générée automatiquement.


	var Connection = require('tedious').Connection;
	var Request = require('tedious').Request
	var TYPES = require('tedious').TYPES;
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


## Étapes suivantes

Pour plus d’informations, consultez le [Centre pour développeurs Node.js](/develop/nodejs/).

<!---HONumber=AcomDC_0107_2016-->