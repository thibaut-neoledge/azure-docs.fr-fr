---
title: Exemples DocumentDB pour MongoDB | Microsoft Docs
description: "Accédez à des exemples de prise en charge du protocole MongoDB dans DocumentDB."
keywords: exemples mongodb
services: documentdb
author: AndrewHoh
manager: jhubbard
editor: 
documentationcenter: 
ms.assetid: fb38bc53-3561-487d-9e03-20f232319a87
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2016
ms.author: anhoh
translationtype: Human Translation
ms.sourcegitcommit: 8d4c3aaefee502e79dd1ea3e074937bb9a1d4593
ms.openlocfilehash: 703373c2c0090a2c6ffef3433aee3a05f0f41475


---
# <a name="documentdb-protocol-support-for-mongodb-examples"></a>Exemples de prise en charge du protocole MongoDB dans DocumentDB
Pour utiliser ces exemples, vous devez :

* [Créer](documentdb-create-mongodb-account.md) un compte Azure DocumentDB prenant en charge le protocole MongoDB.
* Récupérer votre compte DocumentDB avec les informations de la [chaîne de connexion](documentdb-connect-mongodb-account.md) pour la prise en charge du protocole MongoDB.

## <a name="get-started-with-a-sample-nodejs-getting-started-app"></a>Prise en main avec un exemple d’application Démarrage rapide Node.js

1. Créez un fichier *app.js* et copiez puis collez le code ci-dessous.

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

2. Modifiez les variables suivantes dans le fichier *app.js* selon les paramètres de votre compte (découvrez comment rechercher votre [chaîne de connexion](documentdb-connect-mongodb-account.md)) :
   
         var url = 'mongodb://<endpoint>:<password>@<endpoint>.documents.azure.com:10250/?ssl=true';
     
3. Ouvrez votre terminal préféré, exécutez **npm install mongodb --save**, puis exécutez votre application avec **node app.js**

## <a name="get-started-with-a-sample-aspnet-mvc-task-list-application"></a>Prise en main d’un exemple d’application de liste de tâches ASP.NET MVC
Vous pouvez utiliser le didacticiel [Création d’une application Web Azure se connectant à MongoDB exécuté sur une machine virtuelle](../app-service-web/web-sites-dotnet-store-data-mongodb-vm.md) sans grande modification pour configurer rapidement une application MongoDB (en local ou publiée dans une application web Azure) qui se connecte à un compte DocumentDB prenant en charge le protocole MongoDB.  

1. Suivez le didacticiel en apportant la modification suivante.  Remplacez le code Dal.cs par :
   
        using System;
        using System.Collections.Generic;
        using System.Linq;
        using System.Web;
        using MyTaskListApp.Models;
        using MongoDB.Driver;
        using MongoDB.Bson;
        using System.Configuration;
        using System.Security.Authentication;
   
        namespace MyTaskListApp
        {
            public class Dal : IDisposable
            {
                //private MongoServer mongoServer = null;
                private bool disposed = false;
   
                // To do: update the connection string with the DNS name
                // or IP address of your server. 
                //For example, "mongodb://testlinux.cloudapp.net
                private string connectionString = "mongodb://localhost:27017";
                private string userName = "<your user name>";
                private string host = "<your host>";
                private string password = "<your password>";
   
                // This sample uses a database named "Tasks" and a 
                //collection named "TasksList".  The database and collection 
                //will be automatically created if they don't already exist.
                private string dbName = "Tasks";
                private string collectionName = "TasksList";
   
                // Default constructor.        
                public Dal()
                {
                }
   
                // Gets all Task items from the MongoDB server.        
                public List<MyTask> GetAllTasks()
                {
                    try
                    {
                        var collection = GetTasksCollection();
                        return collection.Find(new BsonDocument()).ToList();
                    }
                    catch (MongoConnectionException)
                    {
                        return new List<MyTask>();
                    }
                }
   
                // Creates a Task and inserts it into the collection in MongoDB.
                public void CreateTask(MyTask task)
                {
                    var collection = GetTasksCollectionForEdit();
                    try
                    {
                        collection.InsertOne(task);
                    }
                    catch (MongoCommandException ex)
                    {
                        string msg = ex.Message;
                    }
                }
   
                private IMongoCollection<MyTask> GetTasksCollection()
                {
                    MongoClientSettings settings = new MongoClientSettings();
                    settings.Server = new MongoServerAddress(host, 10250);
                    settings.UseSsl = true;
                    settings.SslSettings = new SslSettings();
                    settings.SslSettings.EnabledSslProtocols = SslProtocols.Tls12;
   
                    MongoIdentity identity = new MongoInternalIdentity(dbName, userName);
                    MongoIdentityEvidence evidence = new PasswordEvidence(password);
   
                    settings.Credentials = new List<MongoCredential>()
                    {
                        new MongoCredential("SCRAM-SHA-1", identity, evidence)
                    };
   
                    MongoClient client = new MongoClient(settings);
                    var database = client.GetDatabase(dbName);
                    var todoTaskCollection = database.GetCollection<MyTask>(collectionName);
                    return todoTaskCollection;
                }
   
                private IMongoCollection<MyTask> GetTasksCollectionForEdit()
                {
                    MongoClientSettings settings = new MongoClientSettings();
                    settings.Server = new MongoServerAddress(host, 10250);
                    settings.UseSsl = true;
                    settings.SslSettings = new SslSettings();
                    settings.SslSettings.EnabledSslProtocols = SslProtocols.Tls12;
   
                    MongoIdentity identity = new MongoInternalIdentity(dbName, userName);
                    MongoIdentityEvidence evidence = new PasswordEvidence(password);
   
                    settings.Credentials = new List<MongoCredential>()
                    {
                        new MongoCredential("SCRAM-SHA-1", identity, evidence)
                    };
                    MongoClient client = new MongoClient(settings);
                    var database = client.GetDatabase(dbName);
                    var todoTaskCollection = database.GetCollection<MyTask>(collectionName);
                    return todoTaskCollection;
                }
   
                # region IDisposable
   
                public void Dispose()
                {
                    this.Dispose(true);
                    GC.SuppressFinalize(this);
                }
   
                protected virtual void Dispose(bool disposing)
                {
                    if (!this.disposed)
                    {
                        if (disposing)
                        {
                        }
                    }
   
                    this.disposed = true;
                }
   
                # endregion
            }
        }
2. Modifiez les variables suivantes dans le fichier Dal.cs par les paramètres de votre compte :
   
         private string userName = "<your user name>";
         private string host = "<your host>";
         private string password = "<your password>";
3. Vous pouvez maintenant utiliser l’application.

## <a name="next-steps"></a>Étapes suivantes
* Apprenez à [utiliser MongoChef](documentdb-mongodb-mongochef.md) avec un compte DocumentDB prenant en charge le protocole MongoDB.



<!--HONumber=Nov16_HO4-->


