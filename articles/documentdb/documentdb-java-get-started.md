---
title: "Didacticiel NoSQL : kit de développement logiciel (SDK) Java de DocumentDB | Microsoft Docs"
description: "Didacticiel NoSQL qui crée une base de données en ligne et une application console Java à l’aide du Kit de développement logiciel (SDK) Java de DocumentDB. Azure DocumentDB est une base de données NoSQL pour JSON."
keywords: "didacticiel nosql, base de données en ligne, application console java"
services: documentdb
documentationcenter: Java
author: arramac
manager: jhubbard
editor: monicar
ms.assetid: 75a9efa1-7edd-4fed-9882-c0177274cbb2
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: hero-article
ms.date: 01/05/2017
ms.author: arramac
translationtype: Human Translation
ms.sourcegitcommit: ddd676df429c20d1c07cfe64abc9ab69ef11bd8c
ms.openlocfilehash: 845858c3df6456293a2552f55ffb35254024931b


---
# <a name="nosql-tutorial-build-a-documentdb-java-console-application"></a>Didacticiel NoSQL : générer une application console Java de DocumentDB
> [!div class="op_single_selector"]
> * [.NET](documentdb-get-started.md)
> * [.NET Core](documentdb-dotnetcore-get-started.md)
> * [Java](documentdb-java-get-started.md)
> * [Node.JS](documentdb-nodejs-get-started.md)
> * [C++](documentdb-cpp-get-started.md)
>  
> 

Bienvenue dans le didacticiel NoSQL pour le Kit de développement logiciel (SDK) Java d’Azure DocumentDB ! À la fin de ce didacticiel, vous disposerez d’une application console qui crée et interroge des ressources DocumentDB.

Le programme est le suivant :

* Création et connexion à un compte DocumentDB
* Configuration de votre solution Visual Studio
* Création d’une base de données en ligne
* Création d’une collection
* Création de documents JSON
* Interrogation de la collection
* Création de documents JSON
* Interrogation de la collection
* Remplacement d'un document
* Suppression d’un document
* Suppression de la base de données

Commençons dès maintenant !

## <a name="prerequisites"></a>Composants requis
Vérifiez que vous disposez des éléments suivants :

* Un compte Azure actif. Si vous n’en avez pas, vous pouvez vous inscrire pour bénéficier d’un [compte gratuit](https://azure.microsoft.com/free/). Vous pouvez également utiliser [l’émulateur Azure DocumentDB](documentdb-nosql-local-emulator.md) pour ce didacticiel.
* [Git](https://git-scm.com/downloads)
* [Kit de développement logiciel Java (JDK) 7+](http://www.oracle.com/technetwork/java/javase/downloads/index.html)
* [Maven](http://maven.apache.org/download.cgi).

## <a name="step-1-create-a-documentdb-account"></a>Étape 1 : créer un compte DocumentDB
Créons un compte DocumentDB. Si vous avez déjà un compte que vous souhaitez utiliser, vous pouvez passer directement à l’étape [Cloner le projet Github](#GitClone). Si vous utilisez l’émulateur DocumentDB, suivez les étapes de la section [Émulateur Azure DocumentDB](documentdb-nosql-local-emulator.md) pour configurer l’émulateur et passez directement à l’étape [Cloner le projet Github](#GitClone).

[!INCLUDE [documentdb-create-dbaccount](../../includes/documentdb-create-dbaccount.md)]

## <a name="a-idgitcloneastep-2-clone-the-github-project"></a><a id="GitClone"></a>Étape 2 : cloner le projet Github
Vous pouvez commencer par cloner le référentiel Github pour une bonne [prise en main de DocumentDB et de Java](https://github.com/Azure-Samples/documentdb-java-getting-started). Par exemple, à partir d’un répertoire local, exécutez la commande suivante pour récupérer l’exemple de projet localement.

    git clone git@github.com:Azure-Samples/documentdb-java-getting-started.git

    cd documentdb-java-getting-started

Le répertoire contient un fichier `pom.xml` pour le projet et un dossier `src` contenant le code source Java `Program.java` qui montre comment effectuer des opérations simples avec Azure DocumentDB, comme créer des documents et interroger des données dans une collection. Le fichier `pom.xml` inclut une dépendance au [Kit de développement logiciel (SDK) Java de DocumentDB sur Maven](https://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb).

    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-documentdb</artifactId>
        <version>LATEST</version>
    </dependency>

## <a name="a-idconnectastep-3-connect-to-a-documentdb-account"></a><a id="Connect"></a>Étape 3 : se connecter à un compte DocumentDB
Ensuite, revenez au [portail Azure](https://portal.azure.com) pour récupérer votre point de terminaison et votre clé primaire. Le point d’extrémité et la clé primaire de DocumentDB sont nécessaires pour que votre application sache où se connecter et que DocumentDB approuve la connexion de votre application.

Dans le portail Azure, accédez à votre compte DocumentDB, puis cliquez sur **Clés**. Copiez l’URI à partir du portail et collez-le dans `<your endpoint URI>` dans le fichier Program.java. Ensuite, copiez la clé primaire à partir du portail, puis collez-la dans `<your key>`.

    this.client = new DocumentClient(
        "<your endpoint URI>",
        "<your key>"
        , new ConnectionPolicy(),
        ConsistencyLevel.Session);

![Capture d’écran du portail Azure utilisé par le didacticiel NoSQL pour créer une application console Java. Présente un compte DocumentDB, avec le hub ACTIF et le bouton CLÉS mis en surbrillance dans le panneau du compte DocumentDB, et les valeurs d’URI, de CLÉ PRIMAIRE et de CLÉ SECONDAIRE mises en surbrillance dans le panneau Clés][keys]

## <a name="step-4-create-a-database"></a>Étape 4 : créer une base de données
Pour créer votre [base de données](documentdb-resources.md#databases) DocumentDB, utilisez la méthode [CreateDatabaseAsync](http://azure.github.io/azure-documentdb-java/com/microsoft/azure/documentdb/DocumentClient.html#createDatabase-com.microsoft.azure.documentdb.Database-com.microsoft.azure.documentdb.RequestOptions-) de la classe **DocumentClient**. Une base de données est le conteneur logique de stockage de documents JSON partitionné entre les collections.

    Database database = new Database();
    database.setId("familydb");
    this.client.createDatabase(database, null);

## <a name="a-idcreatecollastep-5-create-a-collection"></a><a id="CreateColl"></a>Étape 5 : créer une collection
> [!WARNING]
> **createCollection** crée une collection avec un débit réservé, ce qui a des conséquences sur la tarification. Pour plus d’informations, consultez notre [page de tarification](https://azure.microsoft.com/pricing/details/documentdb/).
> 
> 

Pour créer une [collection](documentdb-resources.md#collections), utilisez la méthode [createCollection](http://azure.github.io/azure-documentdb-java/com/microsoft/azure/documentdb/DocumentClient.html#createCollection-java.lang.String-com.microsoft.azure.documentdb.DocumentCollection-com.microsoft.azure.documentdb.RequestOptions-) de la classe **DocumentClient**. Une collection est un conteneur de documents JSON. Elle est associée à une logique d'application JavaScript.


    DocumentCollection collectionInfo = new DocumentCollection();
    collectionInfo.setId("familycoll");

    // DocumentDB collections can be reserved with throughput specified in request units/second. 
    // Here we create a collection with 400 RU/s.
    RequestOptions requestOptions = new RequestOptions();
    requestOptions.setOfferThroughput(400);

    this.client.createCollection("/dbs/familydb", collectionInfo, requestOptions);

## <a name="a-idcreatedocastep-6-create-json-documents"></a><a id="CreateDoc"></a>Étape 6 : Création de documents JSON
Pour créer un [document](documentdb-resources.md#documents), utilisez la méthode [createDocument](http://azure.github.io/azure-documentdb-java/com/microsoft/azure/documentdb/DocumentClient.html#createDocument-java.lang.String-java.lang.Object-com.microsoft.azure.documentdb.RequestOptions-boolean-) de la classe **DocumentClient**. Les documents sont du contenu JSON (arbitraire) défini par l'utilisateur. Nous pouvons maintenant insérer un ou plusieurs documents. Si vous disposez déjà de données que vous souhaitez stocker dans votre base de données, vous pouvez utiliser [l’outil de migration de données](documentdb-import-data.md) de DocumentDB pour importer les données dans une base de données.

    // Insert your Java objects as documents 
    Family andersenFamily = new Family();
    andersenFamily.setId("Andersen.1");
    andersenFamily.setLastName("Andersen")

    // More initialization skipped for brevity. You can have nested references
    andersenFamily.setParents(new Parent[] { parent1, parent2 });
    andersenFamily.setDistrict("WA5");
    Address address = new Address();
    address.setCity("Seattle");
    address.setCounty("King");
    address.setState("WA");

    andersenFamily.setAddress(address);
    andersenFamily.setRegistered(true);

    this.client.createDocument("/dbs/familydb/colls/familycoll", family, new RequestOptions(), true);

![Diagramme illustrant la relation hiérarchique existant entre le compte, la base de données en ligne, la collection et les documents utilisés par le didacticiel NoSQL pour créer une application console C#](./media/documentdb-get-started/nosql-tutorial-account-database.png)

## <a name="a-idqueryastep-7-query-documentdb-resources"></a><a id="Query"></a>Étape 7 : interroger les ressources DocumentDB
DocumentDB prend en charge les [requêtes](documentdb-sql-query.md) enrichies sur les documents JSON stockés dans chaque collection.  L’exemple de code suivant montre comment interroger des documents dans DocumentDB à l’aide de la syntaxe SQL avec la méthode [queryDocuments](http://azure.github.io/azure-documentdb-java/com/microsoft/azure/documentdb/DocumentClient.html#queryDocuments-java.lang.String-com.microsoft.azure.documentdb.SqlQuerySpec-com.microsoft.azure.documentdb.FeedOptions-).

    FeedResponse<Document> queryResults = this.client.queryDocuments(
        "/dbs/familydb/colls/familycoll",
        "SELECT * FROM Family WHERE Family.lastName = 'Andersen'", 
        null);

    System.out.println("Running SQL query...");
    for (Document family : queryResults.getQueryIterable()) {
        System.out.println(String.format("\tRead %s", family));
    }

## <a name="a-idreplacedocumentastep-8-replace-json-document"></a><a id="ReplaceDocument"></a>Étape 8 : remplacer le document JSON
DocumentDB prend en charge la mise à jour des documents JSON à l’aide de la méthode [replaceDocument](http://azure.github.io/azure-documentdb-java/com/microsoft/azure/documentdb/DocumentClient.html#replaceDocument-com.microsoft.azure.documentdb.Document-com.microsoft.azure.documentdb.RequestOptions-).

    // Update a property
    andersenFamily.Children[0].Grade = 6;

    this.client.replaceDocument(
        "/dbs/familydb/colls/familycoll/docs/Andersen.1", 
        andersenFamily,
        null);

## <a name="a-iddeletedocumentastep-9-delete-json-document"></a><a id="DeleteDocument"></a>Étape 9 : supprimer le document JSON
De même, DocumentDB prend en charge la suppression de documents JSON à l’aide de la méthode [deleteDocument](http://azure.github.io/azure-documentdb-java/com/microsoft/azure/documentdb/DocumentClient.html#deleteDocument-java.lang.String-com.microsoft.azure.documentdb.RequestOptions-).  

    this.client.delete("/dbs/familydb/colls/familycoll/docs/Andersen.1", null);

## <a name="a-iddeletedatabaseastep-10-delete-the-database"></a><a id="DeleteDatabase"></a>Étape 10 : supprimer la base de données
Supprimer la base de données créée revient à supprimer la base de données et toutes les ressources enfants (collections, documents, etc.).

    this.client.deleteDatabase("/dbs/familydb", null);

## <a name="a-idrunastep-11-run-your-java-console-application-all-together"></a><a id="Run"></a>Étape 11 : exécuter votre application console C#
Pour exécuter l’application à partir de la console, vous devez d’abord la compiler à l’aide de Maven :
    
    mvn package

L’exécution de `mvn package` télécharge la dernière bibliothèque DocumentDB à partir de Maven et génère `GetStarted-0.0.1-SNAPSHOT.jar`. Ensuite, exécutez l’application comme suit :

    mvn exec:java -D exec.mainClass=GetStarted.Program

Félicitations ! Vous avez terminé ce didacticiel NoSQL et vous disposez d’une application console Java opérationnelle !

## <a name="next-steps"></a>Étapes suivantes
* Vous recherchez un didacticiel sur les applications web Java ? Consultez [Créer une application web Java avec DocumentDB](documentdb-java-application.md).
* Apprenez à [surveiller un compte DocumentDB](documentdb-monitor-accounts.md).
* Exécutez des requêtes sur notre exemple de dataset dans le [Query Playground](https://www.documentdb.com/sql/demo).
* Consultez la section Développer de la [page de documentation DocumentDB](https://azure.microsoft.com/documentation/services/documentdb/)pour découvrir plus en détail le modèle de programmation.

[documentdb-create-account]: documentdb-create-account.md
[keys]: media/documentdb-get-started/nosql-tutorial-keys.png



<!--HONumber=Jan17_HO1-->


