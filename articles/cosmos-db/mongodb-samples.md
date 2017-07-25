---
title: "Utiliser les API MongoDB pour générer une application Azure Cosmos DB | Microsoft Docs"
description: "Didacticiel qui crée une base de données en ligne à l’aide des API DocumentDB pour MongoDB."
keywords: exemples mongodb
services: cosmos-db
author: AndrewHoh
manager: jhubbard
editor: 
documentationcenter: 
ms.assetid: fb38bc53-3561-487d-9e03-20f232319a87
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/22/2017
ms.author: anhoh
ms.translationtype: Human Translation
ms.sourcegitcommit: a643f139be40b9b11f865d528622bafbe7dec939
ms.openlocfilehash: 5afe7e716bc33e2d6d07edf15ab1686466c05bb8
ms.contentlocale: fr-fr
ms.lasthandoff: 05/31/2017


---
# <a name="build-an-azure-cosmos-db-api-for-mongodb-app-using-nodejs"></a>Générer une application Azure Cosmos DB : API pour MongoDB à l’aide de Node.js
> [!div class="op_single_selector"]
> * [.NET](documentdb-get-started.md)
> * [.NET Core](documentdb-dotnetcore-get-started.md)
> * [Java](documentdb-java-get-started.md)
> * [Node.js pour MongoDB](mongodb-samples.md)
> * [Node.JS](documentdb-nodejs-get-started.md)
> * [C++](documentdb-cpp-get-started.md)
>  
>

Cet exemple vous montre comment générer une application console Azure Cosmos DB : API pour MongoDB à l’aide de Node.js.

Pour utiliser cet exemple, vous devez :

* [Créez](create-mongodb-dotnet.md#create-account) un compte Azure Cosmos DB : API pour MongoDB.
* Récupérer les informations de [chaîne de connexion](connect-mongodb-account.md) MongoDB.

## <a name="create-the-app"></a>Création de l'application

1. Créez un fichier *app.js* et copiez puis collez le code ci-dessous.

    ```nodejs
    var MongoClient = require('mongodb').MongoClient;
    var assert = require('assert');
    var ObjectId = require('mongodb').ObjectID;
    var url = 'mongodb://<endpoint>:<password>@<endpoint>.documents.azure.com:10250/?ssl=true';

    var insertDocument = function(db, callback) {
    db.collection('families').insertOne( {
            "id": "AndersenFamily",
            "lastName": "Andersen",
            "parents": [
                { "firstName": "Thomas" },
                { "firstName": "Mary Kay" }
            ],
            "children": [
                { "firstName": "John", "gender": "male", "grade": 7 }
            ],
            "pets": [
                { "givenName": "Fluffy" }
            ],
            "address": { "country": "USA", "state": "WA", "city": "Seattle" }
        }, function(err, result) {
        assert.equal(err, null);
        console.log("Inserted a document into the families collection.");
        callback();
    });
    };
    
    var findFamilies = function(db, callback) {
    var cursor =db.collection('families').find( );
    cursor.each(function(err, doc) {
        assert.equal(err, null);
        if (doc != null) {
            console.dir(doc);
        } else {
            callback();
        }
    });
    };
    
    var updateFamilies = function(db, callback) {
    db.collection('families').updateOne(
        { "lastName" : "Andersen" },
        {
            $set: { "pets": [
                { "givenName": "Fluffy" },
                { "givenName": "Rocky"}
            ] },
            $currentDate: { "lastModified": true }
        }, function(err, results) {
        console.log(results);
        callback();
    });
    };
    
    var removeFamilies = function(db, callback) {
    db.collection('families').deleteMany(
        { "lastName": "Andersen" },
        function(err, results) {
            console.log(results);
            callback();
        }
    );
    };
    
    MongoClient.connect(url, function(err, db) {
    assert.equal(null, err);
    insertDocument(db, function() {
        findFamilies(db, function() {
        updateFamilies(db, function() {
            removeFamilies(db, function() {
                db.close();
            });
        });
        });
    });
    });
    ```

2. Modifiez les variables suivantes dans le fichier *app.js* selon les paramètres de votre compte (découvrez comment rechercher votre [chaîne de connexion](connect-mongodb-account.md)) :
   
    ```nodejs
    var url = 'mongodb://<endpoint>:<password>@<endpoint>.documents.azure.com:10250/?ssl=true';
    ```
     
3. Ouvrez votre terminal préféré, exécutez **npm install mongodb --save**, puis exécutez votre application avec **node app.js**

## <a name="next-steps"></a>Étapes suivantes
* Découvrez comment [utiliser MongoChef](mongodb-mongochef.md) avec votre compte Azure Cosmos DB : API pour MongoDB.

