<properties 
	pageTitle="Connexion à la base de données SQL à l'aide de Node.js sous Windows" 
	description="Cette rubrique présente un exemple de code Node.js que vous pouvez utiliser pour vous connecter à la base de données SQL Azure. L'exemple s'exécute sur un ordinateur client Windows."
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
	ms.date="04/27/2015" 
	ms.author="mebha"/>


# Connexion à la base de données SQL à l'aide de Node.js sous Windows


[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]


Cette rubrique présente un exemple de code Node.js que vous pouvez utiliser pour vous connecter à la base de données SQL Azure. Le programme Node.js s'exécute sur un ordinateur client Windows. Pour gérer la connexion, le pilote msnodesql est utilisé.


## Configuration requise


Les éléments logiciels suivants doivent être présents sur votre ordinateur de développement client.


-  Node.js – [Version 0.8.9 (version 32 bits)](http://blog.nodejs.org/2012/09/11/node-v0-8-9-stable/). Faites défiler et cliquez sur le téléchargement pour Windows Installer pour 32 bits x86, et non pour Windows x64 Installer 64 bits.
- [Python 2.7.6](https://www.python.org/download/releases/2.7.6/), le programme d'installation pour x86 ou x64. 
- [Visual C++ 2010](https://app.vssps.visualstudio.com/profile/review?download=true&family=VisualStudioCExpress&release=VisualStudio2010&type=web&slcid=0x409&context=eyJwZSI6MSwicGMiOjEsImljIjoxLCJhbyI6MCwiYW0iOjEsIm9wIjpudWxsLCJhZCI6bnVsbCwiZmEiOjAsImF1IjpudWxsLCJjdiI6OTY4OTg2MzU1LCJmcyI6MCwic3UiOjAsImVyIjoxfQ2), l'édition Express est disponible gratuitement auprès de Microsoft.
- SQL Server Native Client 11.0 : disponible en tant que Microsoft SQL Server 2012 Native Client situé dans [SQL Server 2012 Feature Pack](http://www.microsoft.com/download/details.aspx?id=29065).


### Installer les modules requis


Dans une fenêtre de ligne de commande **cmd.exe**, accédez au répertoire contenant msnodesql. Entrez les commandes suivantes dans l'ordre indiqué.


	npm install msnodesql
	npm install -g node-gyp


À présent que vous avez installé node-gyp, accédez au répertoire *YourProjectDirectory*, puis à **node_modules\msnodesql**. Ensuite, exécutez les commandes suivantes dans la fenêtre **cmd.exe**.


	node-gyp configure 
	node-gyp build


Puis, accédez au répertoire **build\release**. Copiez le fichier **sqlserver.node** et collez-le dans le répertoire **msnodesql\lib**. Remplacez l'ancien fichier si nécessaire.

Consultez notre [blog de l'équipe](http://blogs.msdn.com/b/sqlphp/archive/2015/05/12/getting-started-with-node-js-and-microsoft-sql-server-and-azure-sql-database.aspx) et notre [vidéo](https://www.youtube.com/watch?v=kQo_L-D_zk8) pour découvrir comment installer et configurer les exigences mentionnées précédemment.


### Créer une base de données et récupérer la chaîne de connexion
 
Consultez la [rubrique Prise en main](sql-database-get-started.md) pour découvrir comment créer un exemple de base de données et obtenir votre chaîne de connexion. Il est important que vous suiviez le guide pour créer un **modèle de base de données AdventureWorks**. Les exemples ci-dessous fonctionnent uniquement avec le **schéma AdventureWorks**.


## Se connecter à la base de données SQL


- Copiez le code suivant dans un fichier .js situé dans le répertoire de projet.


		var http = require('http');
		var sql = require('msnodesql');
		var http = require('http');
		var fs = require('fs');
		var useTrustedConnection = false;
		var conn_str = "Driver={SQL Server Native Client 11.0};Server=tcp:yourserver.database.windows.net;" + 
		(useTrustedConnection == true ? "Trusted_Connection={Yes};" : "UID=yourusername;PWD=yourpassword;") + 
		"Database={AdventureWorks};"
		sql.open(conn_str, function (err, conn) {
		    if (err) {
		        console.log("Error opening the connection!");
		        return;
		    }
		    else
		        console.log("Successfuly connected");
		});	


- Exécutez le fichier .js en émettant la commande suivante.


		node index.js


## Exécuter une instruction SQL SELECT


	var http = require('http');
	var sql = require('msnodesql');
	var http = require('http');
	var fs = require('fs');
	var useTrustedConnection = false;
	var conn_str = "Driver={SQL Server Native Client 11.0};Server=tcp:yourserver.database.windows.net;" + 
	(useTrustedConnection == true ? "Trusted_Connection={Yes};" : "UID=yourusername;PWD=yourpassword;") + 
	"Database={AdventureWorks};"
	sql.open(conn_str, function (err, conn) {
	    if (err) {
	        console.log("Error opening the connection!");
	        return;
	    }
	    else
	        console.log("Successfuly connected");
	
	
	    conn.queryRaw("SELECT c.CustomerID, c.CompanyName,COUNT(soh.SalesOrderID) AS OrderCount FROM SalesLT.Customer AS c LEFT OUTER JOIN SalesLT.SalesOrderHeader AS soh ON c.CustomerID = soh.CustomerID GROUP BY c.CustomerID, c.CompanyName ORDER BY OrderCount DESC;", function (err, results) {
	        if (err) {
	            console.log("Error running query1!");
	            return;
	        }
	        for (var i = 0; i < results.rows.length; i++) {
	            console.log(results.rows[i]);
	        }
	    });
	});


## Insérer une ligne, transmettre des paramètres et récupérer la clé primaire générée


	var http = require('http');
	var sql = require('msnodesql');
	var http = require('http');
	var fs = require('fs');
	var useTrustedConnection = false;
	var conn_str = "Driver={SQL Server Native Client 11.0};Server=tcp:yourserver.database.windows.net;" + 
	(useTrustedConnection == true ? "Trusted_Connection={Yes};" : "UID=yourusername;PWD=yourpassword;") + 
	"Database={AdventureWorks};"
	sql.open(conn_str, function (err, conn) {
	    if (err) {
	        console.log("Error opening the connection!");
	        return;
	    }
	    else
	        console.log("Successfuly connected");
	
	
	    conn.queryRaw("INSERT SalesLT.Product (Name, ProductNumber, StandardCost, ListPrice, SellStartDate) OUTPUT INSERTED.ProductID VALUES ('SQL Server Express', 'SQLEXPRESS', 0, 0, CURRENT_TIMESTAMP)", function (err, results) {
	        if (err) {
	            console.log("Error running query!");
	            return;
	        }
	        for (var i = 0; i < results.rows.length; i++) {
	            console.log("Product ID Inserted : "+results.rows[i]);
	        }
	    });
	});


## Transactions


La méthode **conn.beginTransactions** ne fonctionne pas dans la base de données SQL Azure. À la place, suivez l'exemple de code pour effectuer des transactions dans la base de données SQL.


	var http = require('http');
	var sql = require('msnodesql');
	var http = require('http');
	var fs = require('fs');
	var useTrustedConnection = false;
	var conn_str = "Driver={SQL Server Native Client 11.0};Server=tcp:yourserver.database.windows.net;" + 
	(useTrustedConnection == true ? "Trusted_Connection={Yes};" : "UID=yourusername;PWD=yourpassword;") + 
	"Database={AdventureWorks};"
	sql.open(conn_str, function (err, conn) {
	    if (err) {
	        console.log("Error opening the connection!");
	        return;
	    }
	    else
	        console.log("Successfuly connected");
	
	
	    conn.queryRaw("INSERT SalesLT.Product (Name, ProductNumber, StandardCost, ListPrice, SellStartDate) OUTPUT INSERTED.ProductID VALUES ('SQL Server Express New ', 'SQLEXPRESS New', 1, 1, CURRENT_TIMESTAMP)", function (err, results) {
	        if (err) {
	            console.log("Error running query!");
	            return;
	        }
	        for (var i = 0; i < results.rows.length; i++) {
	            console.log("Product ID Inserted : "+results.rows[i]);
	        }
	    });
	    
	    conn.queryRaw("ROLLBACK TRANSACTION; ", function (err, results) {
            	if (err) {
        		console.log("Rollback failed");
        		return;
        	}
    	    });
	});


## Procédures stockées


Pour que cet exemple de code fonctionne, vous devez tout d'abord avoir ou créer une procédure stockée qui ne saisit aucun paramètre. Vous pouvez créer une procédure stockée avec un outil tel que SQL Server Management Studio (SSMS.exe).


	var http = require('http');
	var sql = require('msnodesql');
	var http = require('http');
	var fs = require('fs');
	var useTrustedConnection = false;
	var conn_str = "Driver={SQL Server Native Client 11.0};Server=tcp:yourserver.database.windows.net;" + 
	(useTrustedConnection == true ? "Trusted_Connection={Yes};" : "UID=yourusername;PWD=yourpassword;") + 
	"Database={AdventureWorks};"
	sql.open(conn_str, function (err, conn) {
	    if (err) {
	        console.log("Error opening the connection!");
	        return;
	    }
	    else
	        console.log("Successfuly connected");
		
	    conn.query("exec NameOfStoredProcedure", function (err, results) {
	    	if (err) {
			console.log("Error running query8!");
			return;
		}
	    });
	});

 

<!---HONumber=July15_HO4-->