---
title: Didacticiel NoSQL C++ pour DocumentDB | Microsoft Docs
description: "Un didacticiel NoSQL C++ qui crée une application de console et de base de données C++ à l’aide d’un Kit de développement logiciel (SDK) approuvé par DocumentDB pour C++. DocumentDB est un service de base de données NoSQL à l’échelle de la planète."
services: documentdb
documentationcenter: cpp
author: asthana86
manager: jhubbard
editor: 
ms.assetid: b8756b60-8d41-4231-ba4f-6cfcfe3b4bab
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: cpp
ms.topic: hero-article
ms.date: 11/02/2016
ms.author: aasthan
translationtype: Human Translation
ms.sourcegitcommit: 9f4105d1ab366994add0f75d634917ab9a063733
ms.openlocfilehash: 15719afed2435bd42e732a7d610e7b516ba07ad8


---
# <a name="nosql-c-tutorial-documentdb-c-console-application"></a>Didacticiel C++ NoSQL : application console C++ DocumentDB
> [!div class="op_single_selector"]
> * [.NET](documentdb-get-started.md)
> * [.NET Core](documentdb-dotnetcore-get-started.md)
> * [Node.JS](documentdb-nodejs-get-started.md)
> * [C++](documentdb-cpp-get-started.md)
> 
> 

Bienvenue dans le didacticiel C++ pour le Kit de développement logiciel (SDK) approuvé par Azure DocumentDB pour C++ ! À la fin de ce didacticiel, vous disposerez d’une application console qui crée et interroge des ressources DocumentDB, y compris une base de données C++.

Nous allons aborder les points suivants :

* Création et connexion à un compte DocumentDB
* Configuration de votre application
* Création d’une base de données DocumentDB C++
* Création d’une collection
* Création de documents JSON
* Interrogation de la collection
* Remplacement d'un document
* Suppression d’un document
* Suppression de la base de données DocumentDB C++

Vous n’avez pas le temps ? Ne vous inquiétez pas ! La solution complète est disponible sur [GitHub](https://github.com/stalker314314/DocumentDBCpp). Pour obtenir des instructions rapides, consultez [Obtenir la solution complète](#GetSolution) .

Une fois que vous avez terminé le didacticiel C++, utilisez les boutons de vote en bas de cette page pour nous faire part de vos commentaires. 

Si vous souhaitez que nous vous contactions directement, n’hésitez pas à inclure votre adresse de messagerie dans vos commentaires ou à [nous contacter](https://www.research.net/r/8BKRJ3Z). 

Commençons dès maintenant !

## <a name="prerequisites-for-the-c-tutorial"></a>Configuration requise pour le didacticiel C++
Vérifiez que vous disposez des éléments suivants :

* Un compte Azure actif. Si vous n’en avez pas, vous pouvez vous inscrire pour bénéficier d’un [essai gratuit des services Azure](https://azure.microsoft.com/pricing/free-trial/)dès aujourd’hui.
* [Visual Studio](https://www.visualstudio.com/downloads/), avec les composants du langage C++ installés.

## <a name="step-1-create-a-documentdb-account"></a>Étape 1 : créer un compte DocumentDB
Créons un compte DocumentDB. Si vous avez déjà un compte que vous souhaitez utiliser, vous pouvez passer directement à l’étape [Configurer votre application C++](#SetupNode).

[!INCLUDE [documentdb-create-dbaccount](../../includes/documentdb-create-dbaccount.md)]

## <a name="a-idsetupcastep-2-set-up-your-c-application"></a><a id="SetupC++"></a>Étape 2 : configurer votre application C++
1. Après avoir ouvert Visual Studio, dans le menu **Fichier**, cliquez sur **Nouveau**, puis sur **Projet**. 
2. Dans le volet **Installé** de la fenêtre **Nouveau projet**, développez **Visual C++**, cliquez sur **Win32** et cliquez sur **Application console Win32**. Nommez le projet hellodocumentdb, puis cliquez sur **OK**. 
   
    ![Capture d’écran de l’Assistant Nouveau projet](media/documentdb-cpp-get-started/hellodocumentdb.png)
3. Au démarrage de l’Assistant Application Win32, cliquez sur **Terminer**.
4. Une fois le projet créé, ouvrez le Gestionnaire de package NuGet en cliquant avec le bouton droit sur le projet **hellodocumentdb** dans **l’Explorateur de solutions** et en cliquant sur **Gérer les packages NuGet**. 
   
    ![Capture d’écran montrant Gérer le package NuGet dans le menu du projet](media/documentdb-cpp-get-started/nuget.png)
5. Dans l’onglet **NuGet : hellodocumentdb** cliquez sur **Parcourir**, puis recherchez *documentdbcpp*. Dans les résultats, sélectionnez DocumentDbCpp, comme l’illustre la capture d’écran suivante. Ce package installe les références au Kit de développement logiciel (SDK) C++ REST, une dépendance de DocumentDbCpp.  
   
    ![Capture d’écran du package DocumentDbCpp en surbrillance](media/documentdb-cpp-get-started/documentdbcpp.png)
   
    Une fois les packages ajoutés à votre projet, nous sommes prêts à commencer à écrire du code.   

## <a name="a-idconfigastep-3-copy-connection-details-from-azure-portal-for-your-documentdb-database"></a><a id="Config"></a>Étape 3 : copier les détails de la connexion à partir du Portail Azure pour votre base de données DocumentDB
Affichez [Portail Azure](https://portal.azure.com) et accédez au compte de base de données NoSQL (DocumentDB) créé. Nous aurons besoin de l’URI et de la clé primaire du Portail Azure à l’étape suivante pour établir une connexion à partir de notre extrait de code C++. 

![URI et clés DocumentDB dans le Portail Azure](media/documentdb-cpp-get-started/nosql-tutorial-keys.png)

## <a name="a-idconnectastep-4-connect-to-a-documentdb-account"></a><a id="Connect"></a>Étape 4 : se connecter à un compte DocumentDB
1. Ajoutez les en-têtes et espaces de noms suivants à votre code source, après `#include "stdafx.h"`.
   
        #include <cpprest/json.h>
        #include <documentdbcpp\DocumentClient.h>
        #include <documentdbcpp\exceptions.h>
        #include <documentdbcpp\TriggerOperation.h>
        #include <documentdbcpp\TriggerType.h>
        using namespace documentdb;
        using namespace std;
        using namespace web::json;
2. Ensuite, ajoutez le code suivant à votre fonction main et remplacez la configuration du compte et la clé primaire par vos paramètres DocumentDB de l’étape 3. 
   
        DocumentDBConfiguration conf (L"<account_configuration_uri>", L"<primary_key>");
        DocumentClient client (conf);
   
    Maintenant que vous avez le code permettant d’initialiser le client DocumentDB, voyons comment utiliser les ressources DocumentDB.

## <a name="a-idcreatedbcollastep-5-create-a-c-database-and-collection"></a><a id="CreateDBColl"></a>Étape 5 : créer une base de données C++ et une collection
Avant d’effectuer cette étape, expliquons comment fonctionnent les interactions entre une base de données, une collection et des documents à ceux d’entre vous qui débutent avec DocumentDB. Une [base de données](documentdb-resources.md#databases) est un conteneur logique de stockage de documents réparti entre des collections. Une [collection](documentdb-resources.md#collections) est un conteneur de documents JSON. Elle est associée à une logique d’application JavaScript. Pour en savoir plus sur les concepts et le modèle de ressources hiérarchique de DocumentDB, consultez [Concepts et modèle de ressources hiérarchique de DocumentDB](documentdb-resources.md).

Pour créer une base de données et la collection correspondante, ajoutez le code suivant à la fin de votre fonction main. Cela crée une base de données appelée « FamilyRegistry » et une collection appelée « FamilyCollection » selon la configuration du client déclarée à l’étape précédente.

    try {
      shared_ptr<Database> db = client.CreateDatabase(L"FamilyRegistry");
      shared_ptr<Collection> coll = db->CreateCollection(L"FamilyCollection");
    } catch (DocumentDBRuntimeException ex) {
      wcout << ex.message();
    }


## <a name="a-idcreatedocastep-6-create-a-document"></a><a id="CreateDoc"></a>Étape 6 : créer un document
Les [documents](documentdb-resources.md#documents) correspondent à du contenu JSON (arbitraire) défini par l’utilisateur. Vous pouvez maintenant insérer un document dans DocumentDB. Vous pouvez créer un document en copiant le code suivant à la fin de la fonction main. 

    try {
      value document_family;
      document_family[L"id"] = value::string(L"AndersenFamily");
      document_family[L"FirstName"] = value::string(L"Thomas");
      document_family[L"LastName"] = value::string(L"Andersen");
      shared_ptr<Document> doc = coll->CreateDocumentAsync(document_family).get();

      document_family[L"id"] = value::string(L"WakefieldFamily");
      document_family[L"FirstName"] = value::string(L"Lucy");
      document_family[L"LastName"] = value::string(L"Wakefield");
      doc = coll->CreateDocumentAsync(document_family).get();
    } catch (ResourceAlreadyExistsException ex) {
      wcout << ex.message();
    }

En bref, ce code crée une base de données DocumentDB, une collection et des documents, que vous pouvez interroger dans l’Explorateur de documents dans le Portail Azure. 

![Tutoriel C++ : diagramme illustrant la relation hiérarchique entre le compte, la base de données, la collection et les documents](media/documentdb-cpp-get-started/documentdbdocs.png)

## <a name="a-idquerydbastep-7-query-documentdb-resources"></a><a id="QueryDB"></a>Étape 7 : interroger les ressources DocumentDB
DocumentDB prend en charge les [requêtes](documentdb-sql-query.md) enrichies sur les documents JSON stockés dans chaque collection. L’exemple de code suivant affiche une requête utilisant la syntaxe SQL de DocumentDB que vous pouvez exécuter sur les documents créés à l’étape précédente.

La fonction prend comme arguments l’ID de ressource ou l’identificateur unique de la base de données et de la collection, ainsi que le client du document. Ajoutez ce code avant la fonction main.

    void executesimplequery(const DocumentClient &client,
                            const wstring dbresourceid,
                            const wstring collresourceid) {
      try {
        client.GetDatabase(dbresourceid).get();
        shared_ptr<Database> db = client.GetDatabase(dbresourceid);
        shared_ptr<Collection> coll = db->GetCollection(collresourceid);
        wstring coll_name = coll->id();
        shared_ptr<DocumentIterator> iter =
            coll->QueryDocumentsAsync(wstring(L"SELECT * FROM " + coll_name)).get();
        wcout << "\n\nQuerying collection:";
        while (iter->HasMore()) {
          shared_ptr<Document> doc = iter->Next();
          wstring doc_name = doc->id();
          wcout << "\n\t" << doc_name << "\n";
          wcout << "\t"
                << "[{\"FirstName\":"
                << doc->payload().at(U("FirstName")).as_string()
                << ",\"LastName\":" << doc->payload().at(U("LastName")).as_string()
                << "}]";
        }
      } catch (DocumentDBRuntimeException ex) {
        wcout << ex.message();
      }
    }

## <a name="a-idreplaceastep-8-replace-a-document"></a><a id="Replace"></a>Étape 8 : remplacer un document
DocumentDB prend en charge le remplacement de documents JSON, comme l’illustre le code suivant. Ajoutez ce code après la fonction executesimplequery.

    void replacedocument(const DocumentClient &client, const wstring dbresourceid,
                         const wstring collresourceid,
                         const wstring docresourceid) {
      try {
        client.GetDatabase(dbresourceid).get();
        shared_ptr<Database> db = client.GetDatabase(dbresourceid);
        shared_ptr<Collection> coll = db->GetCollection(collresourceid);
        value newdoc;
        newdoc[L"id"] = value::string(L"WakefieldFamily");
        newdoc[L"FirstName"] = value::string(L"Lucy");
        newdoc[L"LastName"] = value::string(L"Smith Wakefield");
        coll->ReplaceDocument(docresourceid, newdoc);
      } catch (DocumentDBRuntimeException ex) {
        throw;
      }
    }

## <a name="a-iddeleteastep-9-delete-a-document"></a><a id="Delete"></a>Étape 9 : supprimer un document
DocumentDB prend en charge la suppression de documents JSON. Pour ce faire, copiez et collez le code suivant après la fonction replacedocument. 

    void deletedocument(const DocumentClient &client, const wstring dbresourceid,
                        const wstring collresourceid, const wstring docresourceid) {
      try {
        client.GetDatabase(dbresourceid).get();
        shared_ptr<Database> db = client.GetDatabase(dbresourceid);
        shared_ptr<Collection> coll = db->GetCollection(collresourceid);
        coll->DeleteDocumentAsync(docresourceid).get();
      } catch (DocumentDBRuntimeException ex) {
        wcout << ex.message();
      }
    }

## <a name="a-iddeletedbastep-10-delete-a-database"></a><a id="DeleteDB"></a>Étape 10 : supprimer une base de données
Supprimer la base de données créée revient à supprimer la base de données et toutes les ressources enfants (collections, documents, etc.).

Copiez et collez l’extrait de code suivant (fonction cleanup) après la fonction deletedocument pour supprimer la base de données et toutes ses ressources enfants.

    void deletedb(const DocumentClient &client, const wstring dbresourceid) {
      try {
        client.DeleteDatabase(dbresourceid);
      } catch (DocumentDBRuntimeException ex) {
        wcout << ex.message();
      }
    }

## <a name="a-idrunastep-11-run-your-c-application-all-together"></a><a id="Run"></a>Étape 11 : exécuter l’ensemble votre application C++ !
Nous avons maintenant ajouté le code permettant de créer, d’interroger, de modifier et de supprimer différentes ressources DocumentDB.  Nous allons maintenant lier ces éléments en ajoutant des appels à ces différentes fonctions à notre fonction main dans hellodocumentdb.cpp, ainsi que des messages de diagnostic.

Pour cela, remplacez la fonction main de votre application par le code suivant. Cela remplace account_configuration_uri et primary_key que vous avez copiés dans le code à l’étape 3 : par conséquent, enregistrez cette ligne ou copiez à nouveau les valeurs sur le portail. 

    int main() {
        try {
            // Start by defining your account's configuration
            DocumentDBConfiguration conf (L"<account_configuration_uri>", L"<primary_key>");
            // Create your client
            DocumentClient client(conf);
            // Create a new database
            try {
                shared_ptr<Database> db = client.CreateDatabase(L"FamilyDB");
                wcout << "\nCreating database:\n" << db->id();
                // Create a collection inside database
                shared_ptr<Collection> coll = db->CreateCollection(L"FamilyColl");
                wcout << "\n\nCreating collection:\n" << coll->id();
                value document_family;
                document_family[L"id"] = value::string(L"AndersenFamily");
                document_family[L"FirstName"] = value::string(L"Thomas");
                document_family[L"LastName"] = value::string(L"Andersen");
                shared_ptr<Document> doc =
                    coll->CreateDocumentAsync(document_family).get();
                wcout << "\n\nCreating document:\n" << doc->id();
                document_family[L"id"] = value::string(L"WakefieldFamily");
                document_family[L"FirstName"] = value::string(L"Lucy");
                document_family[L"LastName"] = value::string(L"Wakefield");
                doc = coll->CreateDocumentAsync(document_family).get();
                wcout << "\n\nCreating document:\n" << doc->id();
                executesimplequery(client, db->resource_id(), coll->resource_id());
                replacedocument(client, db->resource_id(), coll->resource_id(),
                    doc->resource_id());
                wcout << "\n\nReplaced document:\n" << doc->id();
                executesimplequery(client, db->resource_id(), coll->resource_id());
                deletedocument(client, db->resource_id(), coll->resource_id(),
                    doc->resource_id());
                wcout << "\n\nDeleted document:\n" << doc->id();
                deletedb(client, db->resource_id());
                wcout << "\n\nDeleted db:\n" << db->id();
                cin.get();
            }
            catch (ResourceAlreadyExistsException ex) {
                wcout << ex.message();
            }
        }
        catch (DocumentDBRuntimeException ex) {
            wcout << ex.message();
        }
        cin.get();
    }

Vous pouvez maintenant générer et exécuter votre code dans Visual Studio en appuyant sur F5 ou dans la fenêtre du terminal en localisant l’application et en exécutant le fichier exécutable. 

La sortie de votre application de prise en main doit s’afficher. La sortie devrait ressembler à la capture d’écran qui suit.

![Sortie de l’application C++ DocumentDB](media/documentdb-cpp-get-started/docdbconsole.png)

Félicitations ! Vous avez terminé le didacticiel C++ et disposez à présent de votre première application de console DocumentDB !

## <a name="a-idgetsolutionaget-the-complete-c-tutorial-solution"></a><a id="GetSolution"></a>Obtenir la solution complète du didacticiel C++
Pour générer la solution GetStarted qui contient tous les exemples de cet article, vous devez disposer des éléments suivants :

* [Compte DocumentDB][documentdb-create-account].
* La solution [GetStarted](https://github.com/stalker314314/DocumentDBCpp) disponible sur GitHub.

## <a name="next-steps"></a>Étapes suivantes
* Apprenez à [surveiller un compte DocumentDB](documentdb-monitor-accounts.md).
* Exécutez des requêtes sur notre exemple de dataset dans le [Query Playground](https://www.documentdb.com/sql/demo).
* Consultez la section Développer de la [page de documentation DocumentDB](https://azure.microsoft.com/documentation/services/documentdb/)pour découvrir plus en détail le modèle de programmation.

[documentdb-create-account]: documentdb-create-account.md
[documentdb-manage]: documentdb-manage.md






<!--HONumber=Nov16_HO3-->


