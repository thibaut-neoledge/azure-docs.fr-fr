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
	ms.date="03/30/2016"
	ms.author="anhoh"/>

# Didacticiel NoSQL Node.js : application console Node.js DocumentDB  

> [AZURE.SELECTOR]
- [.NET](documentdb-get-started.md)
- [Node.JS](documentdb-nodejs-get-started.md)

Bienvenue dans le didacticiel Node.js pour le Kit de développement logiciel (SDK) de DocumentDB À la fin de ce didacticiel, vous disposerez d’une application console qui crée et interroge des ressources DocumentDB, y compris la base de données du nœud.

Nous allons aborder les points suivants :

- Création et connexion à un compte DocumentDB
- Configuration de votre application
- Création d’une base de données de nœud
- Création d’une collection
- Création de documents JSON
- Interrogation de la collection
- Suppression de la base de données du nœud

Vous n’avez pas le temps ? Ne vous inquiétez pas ! La solution complète est disponible sur [GitHub](https://github.com/Azure-Samples/documentdb-node-getting-started). Pour obtenir des instructions rapides, consultez [Obtenir la solution complète](#GetSolution).

Une fois que vous avez terminé le didacticiel Node.js, utilisez les boutons de vote en haut et en bas de cette page pour nous faire part de vos commentaires. Si vous souhaitez que nous vous contactions directement, n’hésitez pas à inclure votre adresse de messagerie dans vos commentaires.

Commençons dès maintenant !

## Configuration requise pour le didacticiel Node.js

Vérifiez que vous disposez des éléments suivants :

- Un compte Azure actif. Si vous n’en avez pas, vous pouvez vous inscrire pour bénéficier d’un [essai gratuit des services Azure](https://azure.microsoft.com/pricing/free-trial/) dès aujourd’hui.
- [Node.js](https://nodejs.org/) version v0.10.29 ou supérieure.

## Étape 1 : créer un compte DocumentDB

Créons un compte DocumentDB. Si vous avez déjà un compte que vous souhaitez utiliser, vous pouvez passer directement à l’étape [Configurer votre application Node.js](#SetupNode).

[AZURE.INCLUDE [documentdb-create-dbaccount](../../includes/documentdb-create-dbaccount.md)]

##<a id="SetupNode"></a> Étape 2 : Configuration de votre application Node.js

1. Ouvrez votre terminal préféré.
2. Recherchez le dossier ou le répertoire où vous souhaitez enregistrer votre application Node.js.
3. Créez deux fichiers JavaScript vides avec les commandes suivantes :
	- Windows :    
	    * ```fsutil file createnew app.js 0```
        * ```fsutil file createnew config.js 0```
	- Linux/OS X : 
	    * ```touch app.js```
        * ```touch config.js```
4. Installez le module documentdb via npm. Utilisez la commande suivante :
    * ```npm install documentdb --save```

Parfait ! Vous avez terminé l’installation, nous pouvons donc passer à l’écriture du code.

##<a id="Config"></a> Étape 3 : Définition des configurations de votre application

Ouvrez ```config.js``` dans l’éditeur de texte de votre choix.

Ensuite, créez un objet vide intitulé ```config```, puis définissez les propriétés ```config.endpoint``` et ```config.authKey``` pour votre point de terminaison et votre clé d’autorisation DocumentDB. Ces deux configurations se trouvent dans le [portail Azure](https://portal.azure.com).

![Didacticiel Node.js : capture d’écran du portail Azure, présentant un compte DocumentDB, avec le hub ACTIF et le bouton CLÉS mis en surbrillance dans le panneau du compte DocumentDB, et les valeurs d’URI, de CLÉ PRIMAIRE et de CLÉ SECONDAIRE mises en surbrillance dans le panneau Clés - Base de données de nœud][keys]

    var config = {}

    config.endpoint = "https://YOUR_ENDPOINT_URI.documents.azure.com:443/";
    config.authKey = "oqTveZeWlbtnJQ2yMj23HOAViIr0ya****YOUR_AUTHORIZATION_KEY****ysadfbUV+wUdxwDAZlCfcVzWp0PQg==";

Nous allons à présent ajouter les éléments ```database id```, ```collection id``` et ```JSON documents``` à votre objet ```config```. Sous la section où vous avez défini vos propriétés ```config.endpoint``` et ```config.authKey```, ajoutez le code suivant. Si vous disposez déjà de données que vous souhaitez stocker dans votre base de données, vous pouvez utiliser l’[outil de migration de données](documentdb-import-data.md) de DocumentDB au lieu d’ajouter les définitions de document.

    config.database = {
        "id": "FamilyDB"
    };

    config.collection = {
        "id": "FamilyColl",
        "partitionKey": { "paths": ["/district"], "kind": "Hash" }
    };

    config.documents = {
        "Andersen": {
            "id": "Anderson.1",
            "lastName": "Andersen",
            "district": "WA5",
            "parents": [{
                "firstName": "Thomas"
            }, {
                    "firstName": "Mary Kay"
                }],
            "children": [{
                "firstName": "Henriette Thaulow",
                "gender": "female",
                "grade": 5,
                "pets": [{
                    "givenName": "Fluffy"
                }]
            }],
            "address": {
                "state": "WA",
                "county": "King",
                "city": "Seattle"
            }
        },
        "Wakefield": {
            "id": "Wakefield.7",
            "district": "NY23",
            "parents": [{
                "familyName": "Wakefield",
                "firstName": "Robin"
            }, {
                    "familyName": "Miller",
                    "firstName": "Ben"
                }],
            "children": [{
                "familyName": "Merriam",
                "firstName": "Jesse",
                "gender": "female",
                "grade": 8,
                "pets": [{
                    "givenName": "Goofy"
                }, {
                        "givenName": "Shadow"
                    }]
            }, {
                    "familyName": "Miller",
                    "firstName": "Lisa",
                    "gender": "female",
                    "grade": 1
                }],
            "address": {
                "state": "NY",
                "county": "Manhattan",
                "city": "NY"
            },
            "isRegistered": false
        }
    };


Les définitions de base de données, de collection et de documents vous serviront de propriétés ```database id```, ```collection id``` de DocumentDB et de données de documents.

Enfin, exportez votre objet ```config``` afin de pouvoir y faire référence dans le fichier ```app.js```.

    module.exports = config;

##<a id="Connect"></a> Étape 4 : Se connecter à un compte DocumentDB

Ouvrez votre fichier ```app.js``` vide dans l’éditeur de texte. Importez le module ```documentdb``` et le module ```config``` que vous venez de créer.

    "use strict";

    var documentClient = require("documentdb").DocumentClient;
    var config = require("./config");
    var url = require('url');

Ensuite, nous allons utiliser les éléments ```config.endpoint``` et ```config.authKey``` enregistrés précédemment pour créer un DocumentClient.

    var client = new documentClient(config.endpoint, { "masterKey": config.authKey });

Maintenant que vous êtes connecté à un compte DocumentDB, jetons un œil à l’utilisation de ressources DocumentDB.

## Étape 5 : Création d’une base de données de nœud
Vous pouvez créer une [base de données](documentdb-resources.md#databases) à l’aide de la fonction [createDatabase](https://azure.github.io/azure-documentdb-node/DocumentClient.html) de la classe **DocumentClient**. Une base de données est le conteneur logique de stockage de documents partitionné entre les collections. Ajoutez une fonction pour la création de votre base de données dans le fichier app.js avec l’élément ```id``` spécifié dans l’objet ```config```. Nous allons commencer par nous assurer qu’une base de données avec le même ID ```FamilyRegistry``` n’existe pas déjà. Si elle existe, nous allons retourner cette base de données au lieu d’en créer une.

    var HttpStatusCodes = { NOTFOUND: 404 };
    var databaseUrl = `dbs/${config.database.id}`;
    var collectionUrl = `${databaseUrl}/colls/${config.collection.id}`;

    /**
     * Get the database by ID, or create if it doesn't exist.
     * @param {string} database - The database to get or create
     */
    function getDatabase() {
        console.log(`Getting database:\n${config.database.id}\n`);

        return new Promise((resolve, reject) => {
            client.readDatabase(databaseUrl, (err, result) => {
                if (err) {
                    if (err.code == HttpStatusCodes.NOTFOUND) {
                        client.createDatabase(config.database, (err, created) => {
                            if (err) reject(err)
                            else resolve(created);
                        });
                    } else {
                        reject(err);
                    }
                } else {
                    resolve(result);
                }
            });
        });
    }
##<a id="CreateColl"></a>Étape 6 : Création d’une collection  

> [AZURE.WARNING] **CreateDocumentCollectionAsync** crée une nouvelle collection, ce qui a des conséquences tarifaires. Pour plus d'informations, visitez notre [page de tarification](https://azure.microsoft.com/pricing/details/documentdb/).

Vous pouvez créer une [collection](documentdb-resources.md#collections) à l’aide de la fonction [createCollection](https://azure.github.io/azure-documentdb-node/DocumentClient.html) de la classe **DocumentClient**. Une collection est un conteneur de documents JSON et de la logique d’application JavaScript associée. Ajoutez une fonction permettant de créer votre nouvelle collection dans le fichier app.js avec l’élément ```id``` spécifié dans l’objet ```config```. Là encore, nous allons nous assurer qu’une collection avec le même ID ```FamilyCollection``` n’existe pas déjà. Si elle existe, nous allons retourner cette collection au lieu d’en créer une.

    /**
     * Get the collection by ID, or create if it doesn't exist.
     */
    function getCollection() {
        console.log(`Getting collection:\n${collection.id}\n`);

        return new Promise((resolve, reject) => {
            client.readCollection(collectionUrl, (err, result) => {
                if (err) {
                    if (err.code == HttpStatusCodes.NOTFOUND) {
                        client.createCollection(databaseUrl, config.collection, { offerThroughput: 400 }, (err, created) => {
                            if (err) reject(err)
                            else resolve(created);
                        });
                    } else {
                        reject(err);
                    }
                } else {
                    resolve(result);
                }
            });
        });
    }

##<a id="CreateDoc"></a>Étape 7 : Création d’un document
Vous pouvez créer un [document](documentdb-resources.md#documents) à l’aide de la fonction [createDocument](https://azure.github.io/azure-documentdb-node/DocumentClient.html) de la classe **DocumentClient**. Les documents correspondent à du contenu JSON (arbitraire) défini par l'utilisateur. Vous pouvez maintenant insérer un document dans DocumentDB.

Ensuite, ajoutez une fonction à app.js pour créer les documents contenant les données JSON enregistrées dans l’objet ```config```. Là encore, nous allons nous assurer qu’un document avec le même ID n’existe pas déjà.

    /**
     * Get the document by ID, or create if it doesn't exist.
     * @param {function} callback - The callback function on completion
     */
    function getFamilyDocument(document) {
        let documentUrl = `${collectionUrl}/docs/${document.id}`;
        console.log(`Getting document:\n${document.id}\n`);

        return new Promise((resolve, reject) => {
            client.readDocument(documentUrl, { partitionKey: document.district }, (err, result) => {
                if (err) {
                    if (err.code == HttpStatusCodes.NOTFOUND) {
                        client.createDocument(collectionUrl, document, (err, created) => {
                            if (err) reject(err)
                            else resolve(created);
                        });
                    } else {
                        reject(err);
                    }
                } else {
                    resolve(result);
                }
            });
        });
    };

Félicitations ! Vous disposez désormais des fonctions de création d’une base de données, d’une collection et de documents dans DocumentDB !

![Didacticiel Node.js : diagramme illustrant la relation hiérarchique existant entre le compte, la base de données, la collection et les documents - Base de données de nœud](./media/documentdb-nodejs-get-started/node-js-tutorial-account-database.png)

##<a id="Query"></a>Étape 8 : Interrogation des ressources DocumentDB

DocumentDB prend en charge les [requêtes](documentdb-sql-query.md) enrichies sur les documents JSON stockés dans chaque collection. L’exemple de code suivant illustre une requête que vous pouvez exécuter sur les documents dans votre collection. Ajoutez la fonction suivante au fichier ```app.js```. DocumentDB prend en charge des requêtes similaires à SQL comme indiqué ci-dessous. Pour plus d’informations sur la création des requêtes complexes, consultez [Query Playground](https://www.documentdb.com/sql/demo) et la [documentation relative aux requêtes](documentdb-sql-query.md).

    /**
     * Query the collection using SQL
     */
    function queryCollection() {
        console.log(`Querying collection through index:\n${config.collection.id}\n`);

        return new Promise((resolve, reject) => {
            client.queryDocuments(
                collectionUrl,
                'SELECT VALUE r.address.city FROM root r WHERE r.lastName = "Andersen"',
                { enableCrossPartitionQuery: true }
            ).toArray((err, results) => {
                if (err) reject(err)
                else {
                    for (var queryResult of results) {
                        console.log(`Query returned ${queryResult}`);
                    }
                    resolve(results);
                }
            });
        });
    };


Le schéma suivant montre comment la syntaxe de requête SQL de DocumentDB est appelée sur la collection que vous avez créée.

![Didacticiel Node.js : diagramme illustrant l’étendue et la signification de la requête - Base de données de nœud](./media/documentdb-nodejs-get-started/node-js-tutorial-collection-documents.png)

Le mot clé [FROM](documentdb-sql-query.md/#from-clause) est facultatif dans la requête, car les requêtes DocumentDB sont déjà étendues à une collection unique. Par conséquent, « FROM Families f » peut être remplacé par «FROM root r » ou par tout autre nom de variable que vous choisissez. DocumentDB déduira que Families, root ou le nom de variable choisi fait par défaut référence à la collection actuelle.

##<a id="DeleteDatabase"></a>Étape 9 : Suppression de la base de données de nœuds

Supprimer la base de données créée revient à supprimer la base de données et toutes les ressources enfants (collections, documents, etc.). Vous pouvez supprimer la base de données en ajoutant l’extrait de code suivant.

    /**
     * Cleanup the database and collection on completion
     */
    function cleanup() {
        console.log(`Cleaning up by deleting database ${config.database.id}`);

        return new Promise((resolve, reject) => {
            client.deleteDatabase(databaseUrl, (err) => {
                if (err) reject(err)
                else resolve(null);
            });
        });
    }

##<a id="Build"></a>Étape 10 : Assemblage

Maintenant que vous avez défini toutes les fonctions nécessaires pour votre application, appelons-les !

Ajoutez l’extrait de code suivant en bas de votre code dans ```app.js```.

    /**
     * Exit the app with a prompt
     * @param {message} message - The message to display
     */
    function exit(message) {
        console.log(message);
        console.log('Press any key to exit');
        process.stdin.setRawMode(true);
        process.stdin.resume();
        process.stdin.on('data', process.exit.bind(process, 0));
    }

    getDatabase()
        .then(() => getCollection())
        .then(() => getFamilyDocument(config.documents.Andersen))
        .then(() => getFamilyDocument(config.documents.Wakefield))
        .then(() => queryCollection())
        .then(() => cleanup())
        .then(() => { exit(`Completed successfully`); })
        .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });

##<a id="Run"></a> Étape 11 : Exécuter votre application Node.js

Vous êtes maintenant en mesure d’exécuter votre application Node.js.

Sur votre terminal, recherchez votre fichier ```app.js``` et exécutez la commande : ```node app.js```

La sortie de votre application de prise en main doit s’afficher. La sortie doit correspondre au texte en exemple ci-dessous.

    Getting database:
    FamilyDB

    Getting collection:
    FamilyColl

    Getting document:
    Anderson.1

    Getting document:
    Wakefield.7

    Querying collection through index:
    FamilyColl
      Query returned Seattle

    Cleaning up by deleting database FamilyDB

    Completed successfully
    Press any key to exit

Félicitations ! Vous avez créé et terminé le didacticiel Node.js et disposez à présent de votre première application de console DocumentDB !

##<a id="GetSolution"></a> Obtenir la solution du didacticiel Node.js complète
Pour générer la solution GetStarted qui contient tous les exemples de cet article, vous devez avoir les éléments suivants :

-   [Un compte DocumentDB][documentdb-create-account].
-   La solution [GetStarted](https://github.com/Azure-Samples/documentdb-node-getting-started) disponible sur GitHub.

Installez le module **documentdb** via npm. Utilisez la commande suivante :
* ```npm install documentdb --save```

Ensuite, dans le fichier ```config.js```, mettez à jour les valeurs config.endpoint et config.authKey, comme décrit à l’[Étape 3 : Définition des configurations de votre application](#Config).

## Étapes suivantes

-   Vous voulez un exemple de Node.js plus complexe ? Consultez [Création d’une application web Node.js avec DocumentDB](documentdb-nodejs-application.md).
-	Apprenez à [surveiller un compte DocumentDB](documentdb-monitor-accounts.md).
-	Exécutez des requêtes sur notre exemple de dataset dans le [Query Playground](https://www.documentdb.com/sql/demo).
-	Consultez la section Développer de la [page de documentation DocumentDB](../../services/documentdb/) pour découvrir plus en détail le modèle de programmation.

[doc-landing-page]: ../../services/documentdb/
[documentdb-create-account]: documentdb-create-account.md
[documentdb-manage]: documentdb-manage.md

[keys]: media/documentdb-nodejs-get-started/node-js-tutorial-keys.png

<!---HONumber=AcomDC_0330_2016-->