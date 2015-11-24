<properties
	pageTitle="Didacticiel NoSQL Node.js pour DocumentDB | Microsoft Azure"
	description="Un didacticiel NoSQL Node.js qui crée une application de console et de base de données du nœud à l’aide du Kit de développement logiciel (SDK) DocumentDB Node.js. DocumentDB est une base de données NoSQL pour JSON."
    keywords="didacticiel node.js, base de données du nœud"
	services="documentdb"
	documentationCenter="node.js"
	authors="AndrewHoh"
	manager="jhubbard"
	editor="monicar"/>

<tags
	ms.service="documentdb"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="node"
	ms.topic="hero-article" 
	ms.date="11/18/2015"
	ms.author="anhoh"/>

# Didacticiel NoSQL Node.js : application console Node.js DocumentDB  

> [AZURE.SELECTOR]
- [.NET](documentdb-get-started.md)
- [Node.js](documentdb-nodejs-get-started.md)

Bienvenue dans le didacticiel Node.js pour le Kit de développement logiciel (SDK) de DocumentDB À la fin de ce didacticiel, vous disposerez d’une application console qui crée et interroge des ressources DocumentDB, y compris la base de données du nœud.

Nous allons aborder les points suivants :

- Création et connexion à un compte DocumentDB
- Configuration de votre application
- Création d’une base de données de nœud
- Création d’une collection
- Création de documents JSON
- Interrogation de la collection
- Suppression de la base de données du nœud

Vous n’avez pas le temps ? Ne vous inquiétez pas ! La solution complète est disponible sur [GitHub](https://github.com/Azure-Samples/documentdb-node-getting-started). Pour obtenir des instructions rapides, consultez [Obtenir la solution complète](#GetSolution).

Une fois que vous avez terminé le didacticiel Node.js, utilisez les boutons de vote en haut et en bas de cette page pour nous faire part de vos commentaires. Si vous souhaitez que nous vous contactions directement, n’hésitez pas à inclure votre adresse de messagerie dans vos commentaires.

Commençons dès maintenant !

## Composants requis

Vérifiez que vous disposez des éléments suivants :

- Un compte Azure actif. Si vous n’en avez pas, vous pouvez vous inscrire pour bénéficier d’un [essai gratuit des services Azure](http://azure.microsoft.com/pricing/free-trial/) dès aujourd’hui.
- [Node.js](https://nodejs.org/) version v0.10.29 ou supérieure.

## Étape 1 : créer un compte DocumentDB

Créons un compte DocumentDB. Si vous avez déjà un compte que vous souhaitez utiliser, vous pouvez passer directement à l’étape [Configurer votre application Node.js](#SetupNode).

[AZURE.INCLUDE [documentdb-create-dbaccount](../../includes/documentdb-create-dbaccount.md)]

##<a id="SetupNode"></a> Étape 2 : Configuration de votre application Node.js

1. Ouvrez votre terminal préféré.
2. Recherchez le dossier ou le répertoire où vous souhaitez enregistrer votre application Node.js.
3. Créez deux fichiers JavaScript vides avec les commandes suivantes :
	- Windows :    
	    * **fsutil file createnew app.js 0**
        * **fsutil file createnew config.js 0**
	- Linux/OS X : 
	    * **touch app.js**
        * **touch config.js**
4. Installez le module documentdb via npm. Utilisez la commande suivante :
    * **npm install documentdb --save**

Parfait ! Vous avez terminé l’installation, nous pouvons donc passer à l’écriture du code.

##<a id="Config"></a> Étape 3 : Définition des configurations de votre application

Ouvrez *config.js* dans l’éditeur de texte de votre choix.

Ensuite, créez un objet vide intitulé *config* et définissez les propriétés *config.endpoint* et *config.authKey* pour votre point de terminaison et votre clé d’autorisation DocumentDB. Ces deux configurations se trouvent dans le [portail Azure en version préliminaire](https://portal.azure.com).

![Capture d’écran du portail Azure en version préliminaire, présentant un compte DocumentDB, avec le hub ACTIF et le bouton CLÉS mis en surbrillance dans le panneau du compte DocumentDB, et les valeurs d’URI, de CLÉ PRIMAIRE et de CLÉ SECONDAIRE mises en surbrillance dans le panneau Clés][keys]

    var config = {}

    config.endpoint = "https://YOUR_ENDPOINT_URI.documents.azure.com:443/";
    config.authKey = "oqTveZeWlbtnJQ2yMj23HOAViIr0ya****YOUR_AUTHORIZATION_KEY****ysadfbUV+wUdxwDAZlCfcVzWp0PQg==";

Nous allons maintenant ajouter l’*ID de base de données*, l’*ID de collection*, et les *documents JSON* à votre objet *config*. Sous la section de définition de vos propriétés *config.endpoint* et *config.authKey*, ajoutez le code suivant. Si vous disposez déjà de données que vous souhaitez stocker dans votre base de données, vous pouvez utiliser l’[outil de migration de données](documentdb-import-data.md) de DocumentDB au lieu d’ajouter les définitions de document.

    config.dbDefinition = {"id": "FamilyRegistry"};
    config.collDefinition = {"id": "FamilyCollection"};

    var documents = {
      "Andersen": {
        "id": "AndersenFamily",
        "LastName": "Andersen",
        "Parents": [
          {
            "FirstName": "Thomas"
          },
          {
            "FirstName": "Mary Kay"
          }
        ],
        "Children": [
          {
            "FirstName": "Henriette Thaulow",
            "Gender": "female",
            "Grade": 5,
            "Pets": [
              {
                "GivenName": "Fluffy"
              }
            ]
          }
        ],
        "Address": {
          "State": "WA",
          "County": "King",
          "City": "Seattle"
        }
      },
      "Wakefield":   {
          "id": "WakefieldFamily",
          "Parents": [
            {
              "FamilyName": "Wakefield",
              "FirstName": "Robin"
            },
            {
              "FamilyName": "Miller",
              "FirstName": "Ben"
            }
          ],
          "Children": [
            {
              "FamilyName": "Merriam",
              "FirstName": "Jesse",
              "Gender": "female",
              "Grade": 8,
              "Pets": [
                {
                  "GivenName": "Goofy"
                },
                {
                  "GivenName": "Shadow"
                }
              ]
            },
            {
              "FamilyName": "Miller",
              "FirstName": "Lisa",
              "Gender": "female",
              "Grade": 1
            }
          ],
          "Address": {
            "State": "NY",
            "County": "Manhattan",
            "City": "NY"
          },
          "IsRegistered": false
        }
    };

    config.docsDefinitions = documents;

Les définitions de base de données, de collection et de documents agiront comme vos données d’*ID de base de données*, d’*ID de collection* et de documents DocumentDB.

Enfin, exportez votre objet *config*, afin de pouvoir y faire référence dans le fichier *app.js*.

    module.exports = config;

##<a id="Connect"></a>Étape 4 : Connexion à un compte DocumentDB

Ouvrez votre fichier *app.js* vide dans l’éditeur de texte. Importez le module *documentdb* et votre module *config* nouvellement créé.

    var documentClient = require("documentdb").DocumentClient;
    var config = require("./config");

Ensuite, nous allons utiliser les éléments *config.endpoint* et *config.authKey* enregistrés précédemment pour créer un DocumentClient.

    var client = new documentClient(config.endpoint, {"masterKey": config.authKey});

Maintenant que vous êtes connecté à un compte DocumentDB, jetons un œil à l’utilisation de ressources DocumentDB.

## Étape 5 : Création d’une base de données
Vous pouvez créer une [base de données](documentdb-resources.md#databases) à l’aide de la fonction [createDatabase](https://azure.github.io/azure-documentdb-node/DocumentClient.html) de la classe **DocumentClient**. Une base de données est le conteneur logique de stockage de documents partitionné entre les collections. Ajoutez une fonction pour la création de votre base de données dans le fichier app.js avec l’*ID* spécifié dans l’objet *config*. Tout d’abord, nous allons nous assurer qu’une base de données avec le même ID *FamilyRegistry* n’existe pas déjà. Si elle existe, nous allons retourner cette base de données au lieu d’en créer une.

    var getOrCreateDatabase = function(callback) {
        var querySpec = {
            query: 'SELECT * FROM root r WHERE r.id=@id',
            parameters: [{
                name: '@id',
                value: config.dbDefinition.id
            }]
        };

        client.queryDatabases(querySpec).toArray(function(err, results) {
            if(err) return callback(err);

            if (results.length === 0) {
                client.createDatabase(config.dbDefinition, function(err, created) {
                    if(err) return callback(err);

                    callback(null, created);
                });
            }
            else {
                callback(null, results[0]);
            }
        });
    };

##<a id="CreateColl"></a>Étape 6 : Création d’une collection  

> [AZURE.WARNING]**CreateDocumentCollectionAsync** crée une collection S1, ce qui a des conséquences tarifaires. Pour plus d'informations, visitez notre [page de tarification](https://azure.microsoft.com/pricing/details/documentdb/).

Vous pouvez créer une [collection](documentdb-resources.md#collections) à l’aide de la fonction [createCollection](https://azure.github.io/azure-documentdb-node/DocumentClient.html) de la classe **DocumentClient**. Une collection est un conteneur de documents JSON. Elle est associée à une logique d'application JavaScript. La collection nouvellement créée est mappée à un [niveau de performance S1](documentdb-performance-levels.md). Ajoutez une fonction pour la création de votre collection dans le fichier app.js avec l’*ID* spécifié dans l’objet *config*. Là encore, nous allons nous assurer qu’une collection avec le même ID *FamilyCollection* n’existe pas déjà. Si elle existe, nous allons retourner cette collection au lieu d’en créer une.

    var getOrCreateCollection = function(callback) {

        var querySpec = {
            query: 'SELECT * FROM root r WHERE r.id=@id',
            parameters: [{
                name: '@id',
                value: config.collDefinition.id
            }]
        };

        var dbUri = "dbs/" + config.dbDefinition.id;

        client.queryCollections(dbUri, querySpec).toArray(function(err, results) {
            if(err) return callback(err);

            if (results.length === 0) {
                client.createCollection(dbUri, config.collDefinition, function(err, created) {
                    if(err) return callback(err);
                    callback(null, created);
                });
            }
            else {
                callback(null, results[0]);
            }
        });
    };

##<a id="CreateDoc"></a>Étape 7 : Création d’un document
Vous pouvez créer un [document](documentdb-resources.md#documents) à l’aide de la fonction [createDocument](https://azure.github.io/azure-documentdb-node/DocumentClient.html) de la classe **DocumentClient**. Les documents correspondent à du contenu JSON (arbitraire) défini par l'utilisateur. Vous pouvez maintenant insérer un document dans DocumentDB.

Ensuite, ajoutez une fonction à app.js pour créer les documents contenant les données JSON enregistrées dans l’objet *config*. Là encore, nous allons nous assurer qu’un document avec le même ID n’existe pas déjà.

    var getOrCreateDocument = function(document, callback) {
        var querySpec = {
            query: 'SELECT * FROM root r WHERE r.id=@id',
            parameters: [{
                name: '@id',
                value: document.id
            }]
        };

        var collectionUri = "dbs/" + config.dbDefinition.id + "/colls/" + config.collDefinition.id;

        client.queryDocuments(collectionUri, querySpec).toArray(function(err, results) {
            if(err) return callback(err);

            if(results.length === 0) {
                client.createDocument(collectionUri, document, function(err, created) {
                    if(err) return callback(err);

                    callback(null, created);
                });
            } else {
                callback(null, results[0]);
            }
        });
    };

Félicitations ! Vous disposez désormais des fonctions de création d’une base de données, d’une collection et de documents dans DocumentDB !

![Diagramme illustrant la relation hiérarchique entre le compte, la base de données, la collection et les documents](./media/documentdb-nodejs-get-started/node-js-tutorial-account-database.png)

##<a id="Query"></a>Étape 8 : Interrogation des ressources DocumentDB

DocumentDB prend en charge les [requêtes](documentdb-sql-query.md) enrichies sur les documents JSON stockés dans chaque collection. L’exemple de code suivant illustre une requête que vous pouvez exécuter sur les documents dans votre collection. Ajoutez la fonction suivante au fichier *app.js*. DocumentDB prend en charge des requêtes similaires à SQL comme indiqué ci-dessous. Pour plus d’informations sur la création des requêtes complexes, consultez [Query Playground](https://www.documentdb.com/sql/demo) et la [documentation relative aux requêtes](documentdb-sql-query.md).

    var queryCollection = function(documentId, callback) {
      var querySpec = {
          query: 'SELECT * FROM root r WHERE r.id=@id',
          parameters: [{
              name: '@id',
              value: documentId
          }]
      };

      var collectionUri = "dbs/" + config.dbDefinition.id + "/colls/" + config.collDefinition.id;

      client.queryDocuments(collectionUri, querySpec).toArray(function(err, results) {
          if(err) return callback(err);

          callback(null, results);
      });
    };

Le schéma suivant montre comment la syntaxe de requête SQL de DocumentDB est appelée sur la collection que vous avez créée.

![Diagramme illustrant l'étendue et la signification de la requête](./media/documentdb-nodejs-get-started/node-js-tutorial-collection-documents.png)

Le mot clé [FROM](documentdb-sql-query.md/#from-clause) est facultatif dans la requête, car les requêtes DocumentDB sont déjà étendues à une collection unique. Par conséquent, « FROM Families f » peut être remplacé par «FROM root r » ou par tout autre nom de variable que vous choisissez. DocumentDB déduira que Families, root ou le nom de variable choisi fait par défaut référence à la collection actuelle.

##<a id="DeleteDatabase"></a>Étape 9 : Suppression de la base de données.

Supprimer la base de données créée revient à supprimer la base de données et toutes les ressources enfants (collections, documents, etc.). Vous pouvez supprimer la base de données en ajoutant l’extrait de code suivant.

    // WARNING: this function will delete your database and all its children resources: collections and documents
    function cleanup(callback) {
        client.deleteDatabase("dbs/" + config.dbDefinition.id, function(err) {
            if(err) return callback(err);

            callback(null);
        });
    }

##<a id="Build"></a>Étape 10 : Assemblage

Maintenant que vous avez défini toutes les fonctions nécessaires pour votre application, appelons-les !

L’ordre des appels de fonction sera * *getOrCreateDatabase* * *getOrCreateCollection* * *getOrCreateDocument* * *getOrCreateDocument* * *queryCollection* * *cleanup*

Ajoutez l’extrait de code suivant en bas de votre code dans *app.js*.

    getOrCreateDatabase(function(err, db) {
        if(err) return console.log(err);
        console.log('Read or created db:\n' + db.id + '\n');

        getOrCreateCollection(function(err, coll) {
            if(err) return console.log(err);
            console.log('Read or created collection:\n' + coll.id + '\n');

            getOrCreateDocument(config.docsDefinitions.Andersen, function(err, doc) {
                if(err) return console.log(err);
                console.log('Read or created document:\n' + doc.id + '\n');

                getOrCreateDocument(config.docsDefinitions.Wakefield, function(err, doc) {
                    if(err) return console.log(err);
                    console.log('Read or created document:\n' + doc.id + '\n');

                    queryCollection("AndersenFamily", function(err, results) {
                        if(err) return console.log(err);
                        console.log('Query results:\n' + JSON.stringify(results, null, '\t') + '\n');

                        cleanup(function(err) {
                            if(err) return console.log(err);
                            console.log('Done.');
                        });
                    });
                });
            });
        });
    });

##<a id="Run"></a> Étape 11 : Exécuter votre application Node.js

Vous êtes maintenant en mesure d’exécuter votre application Node.js.

Sur votre terminal, recherchez votre fichier *app.js* et exécutez la commande **node app.js**.

La sortie de votre application de prise en main doit s’afficher. La sortie doit correspondre au texte en exemple ci-dessous.

    Read or created db:
    FamilyRegistry

    Read or created collection:
    FamilyCollection

    Read or created document:
    AndersenFamily

    Read or created document:
    WakefieldFamily

    Query results:
    [
            {
                    "id": "AndersenFamily",
                    "LastName": "Andersen",
                    "Parents": [
                            {
                                    "FirstName": "Thomas"
                            },
                            {
                                    "FirstName": "Mary Kay"
                            }
                    ],
                    "Children": [
                            {
                                    "FirstName": "Henriette Thaulow",
                                    "Gender": "female",
                                    "Grade": 5,
                                    "Pets": [
                                            {
                                                    "GivenName": "Fluffy"
                                            }
                                    ]
                            }
                    ],
                    "Address": {
                            "State": "WA",
                            "County": "King",
                            "City": "Seattle"
                    },
                    "_rid": "tOErANjwoQcBAAAAAAAAAA==",
                    "_ts": 1444327141,
                    "_self": "dbs/tOErAA==/colls/tOErANjwoQc=/docs/tOErANjwoQcBAAAAAAAAAA==/",
                    "_etag": ""00001200-0000-0000-0000-5616aee50000"",
                    "_attachments": "attachments/"
            }
    ]

    Done.

Félicitations ! Vous avez créé et terminé le didacticiel Node.js et disposez à présent de votre première application de console DocumentDB !

##<a id="GetSolution"></a> Obtenir la solution complète
Pour générer la solution GetStarted qui contient tous les exemples de cet article, vous devez avoir les éléments suivants :

-   [Un compte DocumentDB][documentdb-create-account].
-   La solution [GetStarted](https://github.com/Azure-Samples/documentdb-node-getting-started) disponible sur GitHub.

Installez le module **documentdb** via npm. Utilisez la commande suivante : * **npm install documentdb --save**

Ensuite, dans le fichier *config.js*, mettez à jour les valeurs config.endpoint et config.authKey, comme décrit à l’[étape 3 : Définir les configurations de votre application](#Config).

## Étapes suivantes

-   Vous voulez un exemple de Node.js plus complexe ? Consultez [Création d’une application web Node.js avec DocumentDB](documentdb-nodejs-application.md).
-	Apprenez à [surveiller un compte DocumentDB](documentdb-monitor-accounts.md).
-	Exécutez des requêtes sur notre exemple de dataset dans le [Query Playground](https://www.documentdb.com/sql/demo).
-	Consultez la section Développer de la [page de documentation DocumentDB](../../services/documentdb/) pour découvrir plus en détail le modèle de programmation.

[doc-landing-page]: ../../services/documentdb/
[documentdb-create-account]: documentdb-create-account.md
[documentdb-manage]: documentdb-manage.md

[keys]: media/documentdb-nodejs-get-started/node-js-tutorial-keys.png

<!---HONumber=Nov15_HO4-->