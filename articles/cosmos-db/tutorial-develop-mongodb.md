---
title: "Utiliser l’API d’Azure Cosmos DB pour MongoDB afin de créer une application web | Microsoft Docs"
description: "Ce didacticiel Azure Cosmos DB permet de créer une application web de base de données en ligne à l’aide de l’API pour MongoDB."
keywords: exemples mongodb
services: cosmos-db
author: AndrewHoh
manager: jhubbard
editor: 
documentationcenter: 
ms.assetid: 61a2ab3a-2fc3-4d49-a263-ed87c66628f6
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/10/2017
ms.author: anhoh
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: 2c6cf0eff812b12ad852e1434e7adf42c5eb7422
ms.openlocfilehash: a5f34e487a6b2a99f5fcd5c4639ceb6da36806d4
ms.contentlocale: fr-fr
ms.lasthandoff: 09/13/2017

---
# <a name="azure-cosmos-db-connect-to-a-mongodb-app-using-net"></a>Azure Cosmos DB : se connecter à une application MongoDB à l’aide de .NET

Azure Cosmos DB est le service de base de données multi-modèle de Microsoft distribué à l’échelle mondiale. Vous pouvez rapidement créer et interroger des documents, des paires clé/valeur et des bases de données de graphiques, tous profitant de la distribution à l’échelle mondiale et des capacités de mise à l’échelle horizontale au cœur d’Azure Cosmos DB. 

Ce didacticiel montre comment créer un compte Azure Cosmos DB à l’aide du portail Azure, puis une base de données et une collection pour stocker des données avec [l’API MongoDB](mongodb-introduction.md). 

Ce didacticiel décrit les tâches suivantes :

> [!div class="checklist"]
> * Créer un compte Azure Cosmos DB 
> * Mettre à jour votre chaîne de connexion
> * Créer une application MongoDB sur une machine virtuelle 


## <a name="create-a-database-account"></a>Création d'un compte de base de données

Commençons par créer un compte Azure Cosmos DB dans le portail Azure.  

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer. 
[!INCLUDE [cosmos-db-emulator-mongodb](../../includes/cosmos-db-emulator-mongodb.md)]

> [!TIP]
> * Possédez-vous un compte Azure Cosmos DB ? Si tel est le cas, passez directement à l’étape [Configurer votre solution Visual Studio](#SetupVS).
> * Possédiez-vous un compte Azure DocumentDB ? Si c’est le cas, votre compte a été converti en compte Azure Cosmos DB et vous pouvez passer directement à l’étape [Configurer votre solution Visual Studio](#SetupVS).  
> * Si vous utilisez l’émulateur Azure Cosmos DB, suivez les étapes de la section [Émulateur Azure Cosmos DB](local-emulator.md) pour le configurer, puis passez directement à l’étape [Configurer votre solution Visual Studio](#SetupVS). 
>
>

[!INCLUDE [cosmos-db-create-dbaccount-mongodb](../../includes/cosmos-db-create-dbaccount-mongodb.md)]

## <a name="update-your-connection-string"></a>Mettre à jour votre chaîne de connexion

1. Dans le portail Azure, dans la page **Azure Cosmos DB**, sélectionnez le compte API pour MongoDB. 
2. Dans la barre de gauche du panneau Compte, cliquez sur **Démarrage rapide**. 
3. Choisissez votre plateforme (*Pilote .NET*, *Pilote Node.js*, *Pilote MongoDB*, *Pilote Java*, *Pilote Python*). Si votre pilote ou outil n'est pas répertorié, ne vous inquiétez pas, nous développons en permanence d'autres extraits de code de connexion. 
4. Copiez et collez l’extrait de code dans votre application MongoDB, et vous voilà prêt à démarrer.

## <a name="set-up-your-mongodb-app"></a>Configurer votre application MongoDB

Vous pouvez utiliser le didacticiel [Création d’une application Web Azure se connectant à MongoDB exécuté sur une machine virtuelle](../app-service-web/web-sites-dotnet-store-data-mongodb-vm.md) sans grande modification. Il explique comment configurer rapidement une application MongoDB (en local ou publiée dans une application web Azure) qui se connecte à un compte API pour MongoDB.  

1. Suivez le didacticiel en apportant la modification suivante.  Remplacez le code Dal.cs par :

    ```csharp   
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
                settings.Server = new MongoServerAddress(host, 10255);
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
                settings.Server = new MongoServerAddress(host, 10255);
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
    ```

2. Modifiez les variables suivantes dans le fichier Dal.cs et remplacez-les par les paramètres de votre compte provenant de la page Clés du portail Azure :

    ```csharp   
    private string userName = "<your user name>";
    private string host = "<your host>";
    private string password = "<your password>";
    ```

3. Vous pouvez maintenant utiliser l’application.

## <a name="clean-up-resources"></a>Supprimer des ressources

Si vous ne prévoyez pas de continuer à utiliser cette application, utilisez les étapes suivantes pour supprimer toutes les ressources créées par ce didacticiel dans le portail Azure. 

1. Dans le menu de gauche du portail Azure, cliquez sur **Groupes de ressources**, puis sur le nom de la ressource que vous avez créée. 
2. Dans la page de votre groupe de ressources, cliquez sur **Supprimer**, tapez le nom de la ressource à supprimer dans la zone de texte, puis cliquez sur **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à :

> [!div class="checklist"]
> * Créer un compte Azure Cosmos DB 
> * Mettre à jour votre chaîne de connexion
> * Créer une application MongoDB sur une machine virtuelle

Vous pouvez passer à l’étape suivante du didacticiel et importer vos données MongoDB dans Azure Cosmos DB.  

> [!div class="nextstepaction"]
> [Importer des données MongoDB dans Azure Cosmos DB](mongodb-migrate.md)


