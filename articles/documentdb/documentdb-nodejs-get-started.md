---
title: Didacticiel NoSQL Node.js pour DocumentDB | Microsoft Docs
description: "Didacticiel NoSQL Node.js qui crée une application de console et de base de données NoSQL à l’aide du Kit de développement logiciel (SDK) DocumentDB Node.js. DocumentDB est une base de données NoSQL pour JSON."
keywords: "didacticiel node.js, base de données du nœud"
services: documentdb
documentationcenter: node.js
author: AndrewHoh
manager: jhubbard
editor: monicar
ms.assetid: 14d52110-1dce-4ac0-9dd9-f936afccd550
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: node
ms.topic: hero-article
ms.date: 12/25/2016
ms.author: anhoh
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: 2b8ac838e9387b04467f03d0608da05b3edfdd26
ms.lasthandoff: 03/29/2017


---
# <a name="nosql-nodejs-tutorial-documentdb-nodejs-console-application"></a>Didacticiel NoSQL Node.js : application console Node.js DocumentDB
> [!div class="op_single_selector"]
> * [.NET](documentdb-get-started.md)
> * [.NET Core](documentdb-dotnetcore-get-started.md)
> * [Node.js pour MongoDB](documentdb-mongodb-samples.md)
> * [Node.JS](documentdb-nodejs-get-started.md)
> * [Java](documentdb-java-get-started.md)
> * [C++](documentdb-cpp-get-started.md)
>  
> 

Bienvenue dans le didacticiel Node.js pour le Kit de développement logiciel (SDK) d’Azure DocumentDB ! À la fin de ce didacticiel, vous disposerez d’une application console qui crée et interroge des ressources DocumentDB.

Nous allons aborder les points suivants :

* Création et connexion à un compte DocumentDB
* Configuration de votre application
* Création d’une base de données de nœud
* Création d’une collection
* Création de documents JSON
* Interrogation de la collection
* Remplacement d'un document
* Suppression d’un document
* Suppression de la base de données du nœud

Vous n’avez pas le temps ? Ne vous inquiétez pas ! La solution complète est disponible sur [GitHub](https://github.com/Azure-Samples/documentdb-node-getting-started). Pour obtenir des instructions rapides, consultez [Obtenir la solution complète](#GetSolution) .

Une fois que vous avez terminé le didacticiel Node.js, utilisez les boutons de vote en haut et en bas de cette page pour nous faire part de vos commentaires. Si vous souhaitez que nous vous contactions directement, n’hésitez pas à inclure votre adresse de messagerie dans vos commentaires.

Commençons dès maintenant !

## <a name="prerequisites-for-the-nodejs-tutorial"></a>Configuration requise pour le didacticiel Node.js
Vérifiez que vous disposez des éléments suivants :

* Un compte Azure actif. Si vous n’en avez pas, vous pouvez vous inscrire pour bénéficier d’un [essai gratuit des services Azure](https://azure.microsoft.com/pricing/free-trial/)dès aujourd’hui.
    * Vous pouvez également utiliser [l’émulateur Azure DocumentDB](documentdb-nosql-local-emulator.md) pour ce didacticiel.
* [Node.js](https://nodejs.org/) version v0.10.29 ou supérieure.

## <a name="step-1-create-a-documentdb-account"></a>Étape 1 : créer un compte DocumentDB
Créons un compte DocumentDB. Si vous avez déjà un compte que vous souhaitez utiliser, vous pouvez passer directement à l’étape [Configurer votre application Node.js](#SetupNode). Si vous utilisez l’émulateur DocumentDB, suivez les étapes de la section [Émulateur Azure DocumentDB](documentdb-nosql-local-emulator.md) pour configurer l’émulateur et passez directement à l’étape [Configurer votre application Node.js](#SetupNode).

[!INCLUDE [documentdb-create-dbaccount](../../includes/documentdb-create-dbaccount.md)]

## <a id="SetupNode"></a>Étape 2 : configurer votre application Node.js
1. Ouvrez votre terminal préféré.
2. Recherchez le dossier ou le répertoire où vous souhaitez enregistrer votre application Node.js.
3. Créez deux fichiers JavaScript vides avec les commandes suivantes :
   * Windows :
     * ```fsutil file createnew app.js 0```
     * ```fsutil file createnew config.js 0```
   * Linux/OS X :
     * ```touch app.js```
     * ```touch config.js```
4. Installez le module documentdb via npm. Utilisez la commande suivante :
   * ```npm install documentdb --save```

Parfait ! Vous avez terminé l’installation, nous pouvons donc passer à l’écriture du code.

## <a id="Config"></a>Étape 3 : définir les configurations de votre application
Ouvrez ```config.js``` dans l’éditeur de texte de votre choix.

Puis, copiez et collez l’extrait de code ci-dessous et attribuez aux propriétés ```config.endpoint``` et ```config.primaryKey``` l’URI de votre point de terminaison DocumentDB et votre clé primaire. Ces deux configurations se trouvent dans le [portail Azure](https://portal.azure.com).

![Didacticiel Node.js : capture d’écran du portail Azure, présentant un compte DocumentDB, avec le hub ACTIF et le bouton CLÉS mis en surbrillance dans le panneau du compte DocumentDB, et les valeurs d’URI, de CLÉ PRIMAIRE et de CLÉ SECONDAIRE mises en surbrillance dans le panneau Clés - Base de données de nœud][keys]

    // ADD THIS PART TO YOUR CODE
    var config = {}

    config.endpoint = "~your DocumentDB endpoint uri here~";
    config.primaryKey = "~your primary key here~";

Copiez et collez ```database id```, ```collection id``` et ```JSON documents``` dans votre objet ```config``` ci-dessous, où vous avez défini vos propriétés ```config.endpoint``` et ```config.authKey```. Si vous disposez déjà de données que vous souhaitez stocker dans votre base de données, vous pouvez utiliser l’ [outil de migration de données](documentdb-import-data.md) de DocumentDB au lieu d’ajouter les définitions de document.

    config.endpoint = "~your DocumentDB endpoint uri here~";
    config.primaryKey = "~your primary key here~";

    // ADD THIS PART TO YOUR CODE
    config.database = {
        "id": "FamilyDB"
    };

    config.collection = {
        "id": "FamilyColl"
    };

    config.documents = {
        "Andersen": {
            "id": "Anderson.1",
            "lastName": "Andersen",
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


Les définitions de base de données, de collection et de document vous serviront de DocumentDB ```database id```, de ```collection id``` et de données de documents.

Enfin, exportez votre objet ```config``` pour pouvoir y faire référence dans le fichier ```app.js```.

            },
            "isRegistered": false
        }
    };

    // ADD THIS PART TO YOUR CODE
    module.exports = config;

## <a id="Connect"></a> Étape 4 : Se connecter à un compte DocumentDB
Ouvrez votre fichier ```app.js``` vide dans l’éditeur de texte. Copiez et collez le code ci-dessous pour importer le module ```documentdb``` et le module ```config``` que vous venez de créer.

    // ADD THIS PART TO YOUR CODE
    "use strict";

    var documentClient = require("documentdb").DocumentClient;
    var config = require("./config");
    var url = require('url');

Copiez et collez le code pour utiliser l’élément ```config.endpoint``` déjà enregistré et ```config.primaryKey``` pour créer un DocumentClient.

    var config = require("./config");
    var url = require('url');

    // ADD THIS PART TO YOUR CODE
    var client = new documentClient(config.endpoint, { "masterKey": config.primaryKey });

Maintenant que vous avez le code permettant d’initialiser le client DocumentDB, voyons comment utiliser les ressources DocumentDB.

## <a name="step-5-create-a-node-database"></a>Étape 5 : Création d’une base de données de nœud
Copiez et collez le code ci-dessous pour définir l’état HTTP correspondant à Non trouvé, l’URL de la base de données et l’URL de la collection. Le client DocumentDB utilise ces URL pour trouver la base de données et la collection appropriées.

    var client = new documentClient(config.endpoint, { "masterKey": config.primaryKey });

    // ADD THIS PART TO YOUR CODE
    var HttpStatusCodes = { NOTFOUND: 404 };
    var databaseUrl = `dbs/${config.database.id}`;
    var collectionUrl = `${databaseUrl}/colls/${config.collection.id}`;

Vous pouvez créer une [base de données](documentdb-resources.md#databases) à l’aide de la fonction [createDatabase](https://azure.github.io/azure-documentdb-node/DocumentClient.html) de la classe **DocumentClient**. Une base de données est le conteneur logique de stockage de documents partitionné entre les collections.

Copiez et collez la fonction **getDatabase** pour créer votre base de données dans le fichier app.js avec l’élément ```config``` spécifié dans l’objet ```id```. La fonction vérifie qu’il existe aucune base de données ayant l’ID ```FamilyRegistry``` . Si elle existe, nous allons retourner cette base de données au lieu d’en créer une.

    var collectionUrl = `${databaseUrl}/colls/${config.collection.id}`;

    // ADD THIS PART TO YOUR CODE
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

Copiez et collez le code suivant où vous avez défini la fonction **getDatabase** pour ajouter la fonction d’assistance **exit** qui imprime le message de sortie et l’appel à la fonction **getDatabase**.

                } else {
                    resolve(result);
                }
            });
        });
    }

    // ADD THIS PART TO YOUR CODE
    function exit(message) {
        console.log(message);
        console.log('Press any key to exit');
        process.stdin.setRawMode(true);
        process.stdin.resume();
        process.stdin.on('data', process.exit.bind(process, 0));
    }

    getDatabase()
    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });

Sur votre terminal, recherchez votre fichier ```app.js``` et exécutez la commande : ```node app.js```

Félicitations ! Vous avez créé une base de données DocumentDB.

## <a id="CreateColl"></a>Étape 6 : Création d’une collection
> [!WARNING]
> **CreateDocumentCollectionAsync** crée une collection, ce qui a des conséquences tarifaires. Pour plus d'informations, visitez notre [page de tarification](https://azure.microsoft.com/pricing/details/documentdb/).
> 
> 

Vous pouvez créer une [collection](documentdb-resources.md#collections) à l’aide de la fonction [createCollection](https://azure.github.io/azure-documentdb-node/DocumentClient.html) de la classe **DocumentClient**. Une collection est un conteneur de documents JSON. Elle est associée à une logique d'application JavaScript.

Dans le fichier app.js, copiez et collez la fonction **getCollection** sous la fonction **getDatabase** pour créer votre collection avec l’élément ```id``` spécifié dans l’objet ```config```. Là encore, nous allons nous assurer qu’il n’existe aucune collection avec l’ID ```FamilyCollection``` . Si elle existe, nous allons retourner cette collection au lieu d’en créer une.

                } else {
                    resolve(result);
                }
            });
        });
    }

    // ADD THIS PART TO YOUR CODE
    function getCollection() {
        console.log(`Getting collection:\n${config.collection.id}\n`);

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

Copiez et collez le code sous l’appel à **getDatabase** pour exécuter la fonction **getCollection**.

    getDatabase()

    // ADD THIS PART TO YOUR CODE
    .then(() => getCollection())
    // ENDS HERE

    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });

Sur votre terminal, recherchez votre fichier ```app.js``` et exécutez la commande : ```node app.js```

Félicitations ! Vous avez créé une base de données DocumentDB.

## <a id="CreateDoc"></a>Étape 7 : Création d’un document
Vous pouvez créer un [document](documentdb-resources.md#documents) à l’aide de la fonction [createDocument](https://azure.github.io/azure-documentdb-node/DocumentClient.html) de la classe **DocumentClient**. Les documents correspondent à du contenu JSON (arbitraire) défini par l'utilisateur. Vous pouvez maintenant insérer un document dans DocumentDB.

Copiez et collez la fonction **getFamilyDocument** sous la fonction **getCollection** pour créer les documents contenant les données JSON enregistrées dans l’objet ```config```. Là encore, nous allons nous assurer qu’un document avec le même ID n’existe pas déjà.

                } else {
                    resolve(result);
                }
            });
        });
    }

    // ADD THIS PART TO YOUR CODE
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

Copiez et collez le code situé sous l’appel à **getCollection** pour exécuter la fonction **getFamilyDocument**.

    getDatabase()
    .then(() => getCollection())

    // ADD THIS PART TO YOUR CODE
    .then(() => getFamilyDocument(config.documents.Andersen))
    .then(() => getFamilyDocument(config.documents.Wakefield))
    // ENDS HERE

    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });

Sur votre terminal, recherchez votre fichier ```app.js``` et exécutez la commande : ```node app.js```

Félicitations ! Vous avez créé un document DocumentDB.

![Didacticiel Node.js : diagramme illustrant la relation hiérarchique existant entre le compte, la base de données, la collection et les documents - Base de données de nœud](./media/documentdb-nodejs-get-started/node-js-tutorial-account-database.png)

## <a id="Query"></a>Étape 8 : Interrogation des ressources DocumentDB
DocumentDB prend en charge les [requêtes](documentdb-sql-query.md) enrichies sur les documents JSON stockés dans chaque collection. L’exemple de code suivant illustre une requête que vous pouvez exécuter sur les documents dans votre collection.

Copiez et collez la fonction **queryCollection** sous la fonction **getFamilyDocument** dans le fichier app.js. DocumentDB prend en charge des requêtes similaires à SQL comme indiqué ci-dessous. Pour plus d’informations sur la création de requêtes complexes, consultez [Query Playground](https://www.documentdb.com/sql/demo) et la [documentation relative aux requêtes](documentdb-sql-query.md).

                } else {
                    resolve(result);
                }
            });
        });
    }

    // ADD THIS PART TO YOUR CODE
    function queryCollection() {
        console.log(`Querying collection through index:\n${config.collection.id}`);

        return new Promise((resolve, reject) => {
            client.queryDocuments(
                collectionUrl,
                'SELECT VALUE r.children FROM root r WHERE r.lastName = "Andersen"'
            ).toArray((err, results) => {
                if (err) reject(err)
                else {
                    for (var queryResult of results) {
                        let resultString = JSON.stringify(queryResult);
                        console.log(`\tQuery returned ${resultString}`);
                    }
                    console.log();
                    resolve(results);
                }
            });
        });
    };


Le schéma suivant montre comment la syntaxe de requête SQL de DocumentDB est appelée sur la collection que vous avez créée.

![Didacticiel Node.js : diagramme illustrant l’étendue et la signification de la requête - Base de données de nœud](./media/documentdb-nodejs-get-started/node-js-tutorial-collection-documents.png)

Le mot clé [FROM](documentdb-sql-query.md#FromClause) est facultatif dans la requête, car les requêtes DocumentDB sont déjà étendues à une collection unique. Par conséquent, « FROM Families f » peut être remplacé par «FROM root r » ou par tout autre nom de variable que vous choisissez. DocumentDB déduira que Families, root ou le nom de variable choisi fait par défaut référence à la collection actuelle.

Copiez et collez le code sous l’appel à **getFamilyDocument** pour exécuter la fonction **queryCollection**.

    .then(() => getFamilyDocument(config.documents.Andersen))
    .then(() => getFamilyDocument(config.documents.Wakefield))

    // ADD THIS PART TO YOUR CODE
    .then(() => queryCollection())
    // ENDS HERE

    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });

Sur votre terminal, recherchez votre fichier ```app.js``` et exécutez la commande : ```node app.js```

Félicitations ! Vous avez interrogé des documents DocumentDB.

## <a id="ReplaceDocument"></a>Étape 9 : Remplacement d’un document
DocumentDB prend en charge le remplacement des documents JSON.

Copiez et collez la fonction **replaceFamilyDocument** sous la fonction **queryCollection** dans le fichier app.js.

                    }
                    console.log();
                    resolve(result);
                }
            });
        });
    }

    // ADD THIS PART TO YOUR CODE
    function replaceFamilyDocument(document) {
        let documentUrl = `${collectionUrl}/docs/${document.id}`;
        console.log(`Replacing document:\n${document.id}\n`);
        document.children[0].grade = 6;

        return new Promise((resolve, reject) => {
            client.replaceDocument(documentUrl, document, (err, result) => {
                if (err) reject(err);
                else {
                    resolve(result);
                }
            });
        });
    };

Copiez et collez le code sous l’appel à **queryCollection** pour exécuter la fonction **replaceDocument**. De plus, ajoutez le code permettant d’appeler **queryCollection** à nouveau pour vérifier que le document a bien été modifié.

    .then(() => getFamilyDocument(config.documents.Andersen))
    .then(() => getFamilyDocument(config.documents.Wakefield))
    .then(() => queryCollection())

    // ADD THIS PART TO YOUR CODE
    .then(() => replaceFamilyDocument(config.documents.Andersen))
    .then(() => queryCollection())
    // ENDS HERE

    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });

Sur votre terminal, recherchez votre fichier ```app.js``` et exécutez la commande : ```node app.js```

Félicitations ! Vous avez remplacé un document DocumentDB.

## <a id="DeleteDocument"></a>Étape 10 : Suppression d’un document
DocumentDB prend en charge la suppression des documents JSON.

Copiez et collez la fonction **deleteFamilyDocument** sous la fonction **replaceFamilyDocument**.

                else {
                    resolve(result);
                }
            });
        });
    };

    // ADD THIS PART TO YOUR CODE
    function deleteFamilyDocument(document) {
        let documentUrl = `${collectionUrl}/docs/${document.id}`;
        console.log(`Deleting document:\n${document.id}\n`);

        return new Promise((resolve, reject) => {
            client.deleteDocument(documentUrl, (err, result) => {
                if (err) reject(err);
                else {
                    resolve(result);
                }
            });
        });
    };

Copiez et collez le code sous l’appel à la deuxième fonction **queryCollection** pour exécuter la fonction **deleteDocument**.

    .then(() => queryCollection())
    .then(() => replaceFamilyDocument(config.documents.Andersen))
    .then(() => queryCollection())

    // ADD THIS PART TO YOUR CODE
    .then(() => deleteFamilyDocument(config.documents.Andersen))
    // ENDS HERE

    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });

Sur votre terminal, recherchez votre fichier ```app.js``` et exécutez la commande : ```node app.js```

Félicitations ! Vous avez supprimé un document DocumentDB.

## <a id="DeleteDatabase"></a>Étape 11 : Suppression de la base de données de nœud
Supprimer la base de données créée revient à supprimer la base de données et toutes les ressources enfants (collections, documents, etc.).

Copiez et collez la fonction **cleanup** sous la fonction **deleteFamilyDocument** pour supprimer la base de données et toutes ses ressources enfants.

                else {
                    resolve(result);
                }
            });
        });
    };

    // ADD THIS PART TO YOUR CODE
    function cleanup() {
        console.log(`Cleaning up by deleting database ${config.database.id}`);

        return new Promise((resolve, reject) => {
            client.deleteDatabase(databaseUrl, (err) => {
                if (err) reject(err)
                else resolve(null);
            });
        });
    }

Copiez et collez le code sous l’appel à la fonction **deleteFamilyDocument** pour exécuter la fonction **cleanup**.

    .then(() => deleteFamilyDocument(config.documents.Andersen))

    // ADD THIS PART TO YOUR CODE
    .then(() => cleanup())
    // ENDS HERE

    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });

## <a id="Run"></a>Étape 12 : Exécution de l’application Node.js
La séquence permettant d’appeler vos fonctions doit être similaire à celle-ci :

    getDatabase()
    .then(() => getCollection())
    .then(() => getFamilyDocument(config.documents.Andersen))
    .then(() => getFamilyDocument(config.documents.Wakefield))
    .then(() => queryCollection())
    .then(() => replaceFamilyDocument(config.documents.Andersen))
    .then(() => queryCollection())
    .then(() => deleteFamilyDocument(config.documents.Andersen))
    .then(() => cleanup())
    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });

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
        Query returned [{"firstName":"Henriette Thaulow","gender":"female","grade":5,"pets":[{"givenName":"Fluffy"}]}]

    Replacing document:
    Anderson.1

    Querying collection through index:
    FamilyColl
        Query returned [{"firstName":"Henriette Thaulow","gender":"female","grade":6,"pets":[{"givenName":"Fluffy"}]}]

    Deleting document:
    Anderson.1

    Cleaning up by deleting database FamilyDB
    Completed successfully
    Press any key to exit

Félicitations ! Vous avez créé et terminé le didacticiel Node.js et disposez à présent de votre première application de console DocumentDB !

## <a id="GetSolution"></a>Obtenir la solution complète du didacticiel Node.js
Si vous n’avez pas le temps de suivre les étapes de ce didacticiel, ou que vous voulez simplement télécharger le code, vous pouvez l’obtenir à partir de [GitHub](https://github.com/Azure-Samples/documentdb-node-getting-started).

Pour exécuter la solution GetStarted qui contient tous les exemples de cet article, vous devez disposer des éléments suivants :

* [Un compte DocumentDB][documentdb-create-account].
* La solution [GetStarted](https://github.com/Azure-Samples/documentdb-node-getting-started) disponible sur GitHub.

Installez le module **documentdb** via npm. Utilisez la commande suivante :

* ```npm install documentdb --save```

Ensuite, dans le fichier ```config.js```, mettez à jour les valeurs de config.endpoint et de config.authKey, comme indiqué à l’[étape 3 : Définition des configurations de votre application](#Config). 

Sur votre terminal, recherchez votre fichier ```app.js``` et exécutez la commande suivante : ```node app.js```.

Voilà, vous n’avez plus qu’à générer l’élément pour être sur la bonne voie ! 

## <a name="next-steps"></a>Étapes suivantes
* Vous voulez un exemple de Node.js plus complexe ? Consultez [Création d’une application web Node.js avec DocumentDB](documentdb-nodejs-application.md).
* Apprenez à [surveiller un compte DocumentDB](documentdb-monitor-accounts.md).
* Exécutez des requêtes sur notre exemple de dataset dans le [Query Playground](https://www.documentdb.com/sql/demo).
* Consultez la section Développer de la [page de documentation DocumentDB](https://azure.microsoft.com/documentation/services/documentdb/)pour découvrir plus en détail le modèle de programmation.

[documentdb-create-account]: documentdb-create-account.md
[keys]: media/documentdb-nodejs-get-started/node-js-tutorial-keys.png

