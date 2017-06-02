---
title: "Connexion à Azure SQL Database avec Node.js | Microsoft Docs"
description: "Cet article présente un exemple de code Node.js que vous pouvez utiliser pour vous connecter à Azure SQL Database et pour l’interroger."
services: sql-database
documentationcenter: 
author: LuisBosquez
manager: jhubbard
editor: 
ms.assetid: 53f70e37-5eb4-400d-972e-dd7ea0caacd4
ms.service: sql-database
ms.custom: quick start connect, mvc
ms.workload: drivers
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: hero-article
ms.date: 05/24/2017
ms.author: lbosq
ms.translationtype: Human Translation
ms.sourcegitcommit: c785ad8dbfa427d69501f5f142ef40a2d3530f9e
ms.openlocfilehash: 4d3c3541749870b09aecc9efb63413f7c045e044
ms.contentlocale: fr-fr
ms.lasthandoff: 05/26/2017


---
# <a name="azure-sql-database-use-nodejs-to-connect-and-query-data"></a>Azure SQL Database : utilisez Node.js pour vous connecter et interroger des données

Ce guide de démarrage rapide indique comment se connecter à une base de données SQL Azure avec [Node.js](https://nodejs.org/en/), puis utiliser les instructions Transact-SQL pour interroger, insérer, mettre à jour et supprimer des données dans la base de données à partir des plateformes Windows, Ubuntu Linux et Mac.

## <a name="prerequisites"></a>Composants requis

Ce guide de démarrage rapide utilise comme point de départ les ressources créées dans l’une de ces instructions de démarrage rapide :

- [Créer une base de données - Portail](sql-database-get-started-portal.md)
- [Créer une base de données - CLI](sql-database-get-started-cli.md)
- [Créer une base de données - PowerShell](sql-database-get-started-powershell.md)

## <a name="install-nodejs"></a>Installer Node.js 

Les étapes de cette section supposent que vous connaissez le développement avec Node.js et que vous ne savez pas utiliser la base de données SQL Azure. Si vous êtes novice en développement avec Node.js, consultez [Créer une application à l’aide de SQL Server](https://www.microsoft.com/en-us/sql-server/developer-get-started/) et sélectionnez **Node.js**, puis sélectionnez votre système d’exploitation.

### <a name="mac-os"></a>**Mac OS**
Entrez les commandes suivantes pour installer **brew**, un gestionnaire de package facile à utiliser pour Mac OS X et **Node.js**.

```bash
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
brew install node
```

### <a name="linux-ubuntu"></a>**Linux (Ubuntu)**
Entrez les commandes suivantes pour installer **Node.js** et **npm**, le gestionnaire de package pour Node.js.

```bash
sudo apt-get install -y nodejs npm
```

### <a name="windows"></a>**Windows**
Visitez la [page des téléchargements Node.js](https://nodejs.org/en/download/) et sélectionnez l’option souhaitée pour le programme d’installation de Windows.


## <a name="install-the-tedious-sql-server-driver-for-nodejs"></a>Installez le pilote Tedious SQL Server pour Node.js
Le pilote recommandé pour Node.js est **[Tedious](https://github.com/tediousjs/tedious)**. Tedious est une initiative open-source à laquelle Microsoft contribue pour les applications Node.js sur toutes les plateformes. Pour ce didacticiel, vous avez besoin d’un répertoire vide pouvant contenir votre code et les dépendances `npm` que vous allez installer.

Pour installer le pilote **Tedious**, exécutez la commande suivante dans votre répertoire :

```cmd
npm install tedious
```

## <a name="get-connection-information"></a>Obtenir des informations de connexion

Obtenez les informations de connexion requises pour la connexion à la base de données SQL Azure. Vous aurez besoin du nom du serveur complet, du nom de la base de données et des informations de connexion dans les procédures suivantes.

1. Connectez-vous au [portail Azure](https://portal.azure.com/).
2. Sélectionnez **Bases de données SQL** dans le menu de gauche, puis cliquez sur votre base de données dans la page **Bases de données SQL**. 
3. Sur la page **Vue d’ensemble** de votre base de données, vérifiez le nom complet du serveur, comme indiqué dans l’image ci-dessous. Vous pouvez pointer sur le nom du serveur pour afficher l’option **Cliquez pour copier**. 

   ![server-name](./media/sql-database-connect-query-dotnet/server-name.png) 

4. Si vous avez oublié vos informations de connexion à votre serveur Azure SQL Database, accédez à la page du serveur SQL Database pour afficher le nom de l’administrateur du serveur et, si nécessaire, réinitialiser le mot de passe.
    
## <a name="select-data"></a>Sélectionner des données

Utilisez le code suivant pour rechercher les 20 principaux produits par catégorie dans votre base de données SQL Azure. Tout d’abord, importez les classes Connexion et Requête de pilote à partir de la bibliothèque de pilote Tedious. Puis, créez l’objet de configuration et remplacez les variables **nom d’utilisateur**, **mot de passe**, **serveur** et **base de données** par les valeurs spécifiées lors de la création de la base de données avec les exemples de données AdventureWorksLT. Créez un objet `Connection` à l’aide de l’objet `config` spécifié. Puis, définissez le rappel pour l’événement `connect` de l’objet `connection` pour exécuter la fonction `queryDatabase()`.

```js
var Connection = require('tedious').Connection;
var Request = require('tedious').Request;


// Create connection to database
var config = {
  userName: 'your_username', // update me
  password: 'your_password', // update me
  server: 'your_server.database.windows.net', // update me
  options: {
      database: 'your_database' //update me
  }
}
var connection = new Connection(config);

// Attempt to connect and execute queries if connection goes through
connection.on('connect', function(err) {
    if (err) {
        console.log(err)
    }
    else{
        queryDatabase()
    }
});

function queryDatabase(){
    console.log('Reading rows from the Table...');

    // Read all rows from table
    request = new Request(
        "SELECT TOP 1 pc.Name as CategoryName, p.name as ProductName FROM [SalesLT].[ProductCategory] pc JOIN [SalesLT].[Product] p ON pc.productcategoryid = p.productcategoryid",
        function(err, rowCount, rows) {
            console.log(rowCount + ' row(s) returned');
        }
    );
    
    request.on('row', function(columns) {
        columns.forEach(function(column) {
            console.log("%s\t%s", column.metadata.colName, column.value);
        });
    });

    connection.execSql(request);
}
```

## <a name="insert-data-into-the-database"></a>Insérer des données dans la base de données
Utilisez le code suivant pour insérer un nouveau produit dans la table SalesLT.Product à l’aide de la fonction `insertIntoDatabase()` et de l’instruction Transact-SQL [INSERT](https://docs.microsoft.com/sql/t-sql/statements/insert-transact-sql). Remplacez les variables **nom d’utilisateur**, **mot de passe**, **serveur** et **base de données** par les valeurs spécifiées lors de la création de la base de données avec les exemples de données AdventureWorksLT. 

```js
var Connection = require('tedious').Connection;
var Request = require('tedious').Request;


// Create connection to database
var config = {
  userName: 'your_username', // update me
  password: 'your_password', // update me
  server: 'your_server.database.windows.net', // update me
  options: {
      database: 'your_database' //update me
  }
}

var connection = new Connection(config);

// Attempt to connect and execute queries if connection goes through
connection.on('connect', function(err) {
    if (err) {
        console.log(err)
    }
    else{
        insertIntoDatabase()
    }
});

function insertIntoDatabase(){
    console.log("Inserting a brand new product into database...");
    request = new Request(
        "INSERT INTO SalesLT.Product (Name, ProductNumber, Color, StandardCost, ListPrice, SellStartDate) OUTPUT INSERTED.ProductID VALUES ('BrandNewProduct', '200989', 'Blue', 75, 80, '7/1/2016')",
        function(err, rowCount, rows) {
            console.log(rowCount + ' row(s) inserted');
        }
    );
    connection.execSql(request);
}
```

## <a name="update-data-in-the-database"></a>Mettre à jour les données dans la base de données
Utilisez le code suivant pour mettre à jour le nouveau produit que vous avez ajouté précédemment à l’aide de la fonction `updateInDatabase()` et de l’instruction Transact-SQL [UPDATE](https://docs.microsoft.com/sql/t-sql/queries/update-transact-sql). Remplacez les variables **nom d’utilisateur**, **mot de passe**, **serveur** et **base de données** par les valeurs spécifiées lors de la création de la base de données avec les exemples de données AdventureWorksLT. Cet exemple utilise le nom de produit inséré dans l’exemple précédent.

```js
var Connection = require('tedious').Connection;
var Request = require('tedious').Request;


// Create connection to database
var config = {
  userName: 'your_username', // update me
  password: 'your_password', // update me
  server: 'your_server.database.windows.net', // update me
  options: {
      database: 'your_database' //update me
  }
}

var connection = new Connection(config);

// Attempt to connect and execute queries if connection goes through
connection.on('connect', function(err) {
    if (err) {
        console.log(err)
    }
    else{
        updateInDatabase()
    }
});

function updateInDatabase(){
    console.log("Updating the price of the brand new product in database...");
    request = new Request(
        "UPDATE SalesLT.Product SET ListPrice = 50 WHERE Name = 'BrandNewProduct'",
        function(err, rowCount, rows) {
            console.log(rowCount + ' row(s) updated');
        }
    );
    connection.execSql(request);
}
```

## <a name="delete-data-from-the-database"></a>Supprimer des données de la base de données
Utilisez le code suivant pour supprimer des données de la base de données. Remplacez les variables **nom d’utilisateur**, **mot de passe**, **serveur** et **base de données** par les valeurs spécifiées lors de la création de la base de données avec les exemples de données AdventureWorksLT. Cette fois, utilisez une **instruction DELETE** dans la fonction `deleteFromDatabase()`. Cet exemple utilise aussi le nom de produit inséré dans l’exemple précédent.

```js
var Connection = require('tedious').Connection;
var Request = require('tedious').Request;


// Create connection to database
var config = {
  userName: 'your_username', // update me
  password: 'your_password', // update me
  server: 'your_server.database.windows.net', // update me
  options: {
      database: 'your_database' //update me
  }
}

var connection = new Connection(config);

// Attempt to connect and execute queries if connection goes through
connection.on('connect', function(err) {
    if (err) {
        console.log(err)
    }
    else{
        deleteFromDatabase()
    }
});

function deleteFromDatabase(){
    console.log("Deleting the brand new product in database...");
    request = new Request(
        "DELETE FROM SalesLT.Product WHERE Name = 'BrandNewProduct'",
        function(err, rowCount, rows) {
            console.log(rowCount + ' row(s) returned');
        }
    );
    connection.execSql(request);
}
```


## <a name="next-steps"></a>Étapes suivantes
- [Concevoir votre première base de données SQL Azure](sql-database-design-first-database.md)
- [Node.js Driver for SQL Server](https://docs.microsoft.com/sql/connect/node-js/node-js-driver-for-sql-server/) (Pilote Node.js pour SQL Server)
- [Se connecter et lancer des requêtes avec SSMS](sql-database-connect-query-ssms.md)
- [Azure SQL Database : utilisez Visual Studio Code pour vous connecter et interroger des données](sql-database-connect-query-vscode.md).



