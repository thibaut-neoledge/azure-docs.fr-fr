<properties title="Build a Node.js web application using DocumentDB" pageTitle="Build a Node.js web application using DocumentDB | Azure" description="Learn how to use Azure DocumentDB to store and access data from a Node.js application hosted on Azure." metaKeywords="NoSQL, DocumentDB,  database, document-orientated database, JSON, getting started"   services="documentdb" solutions="data-management" documentationCenter=""  authors="ryancraw" manager="jhubbard" editor="cgronlun" scriptId="" />

<tags ms.service="documentdb" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/20/2014" ms.author="ryancraw" />

# <a name="_Toc395783175">Création d'une application web Node.js avec DocumentDB</a>

<a name="_Toc395783175">

Ce didacticiel vous montre comment utiliser le service Azure DocumentDB pour stocker des
données et accéder à ces dernières à partir d'une application Express Node.js hébergée sur Sites Web Azure.

Ce didacticiel part du principe que vous avez une certaine expérience de l'utilisation de Node.js.

Vous apprendrez à effectuer les opérations suivantes :

· utiliser Node.js Tools pour Visual Studio ;

· utiliser le service Azure DocumentDB avec le module NPM de DocumentDB ;

· déployer l'application web vers Sites Web Azure.

Dans ce didacticiel, vous allez concevoir une simple application
web de gestion des tâches qui vous permet de créer,
de récupérer et de terminer des tâches. Ces dernières sont stockées en tant que documents JSON dans Azure
DocumentDB.

![Alt text](./media/documentdb-nodejs-application/image1.png)

## <a name="_Toc395783176">Configuration requise</a>

Avant de suivre les instructions de cet article, vérifiez que les éléments
suivants sont installés :

[Node.js][Node.js] version v0.10.29 ou supérieure

[Git][Git]

[Visual Studio 2013][Visual Studio 2013] Update 3

[Outils Node.js pour Visual Studio][Outils Node.js pour Visual Studio]

**Remarque :** bien que nous utilisions Visual Studio pour créer, déboguer et déployer notre
projet Node.js dans ce didacticiel, vous pouvez utiliser votre
éditeur préféré et exécuter Node.js directement sur la plateforme de votre
choix en procédant comme vous le feriez normalement pour exécuter un projet Node.js. Vous pouvez ensuite utiliser les outils de l'[interface de ligne de commande Azure][interface de ligne de commande Azure] pour
déployer votre application vers Sites Web Azure

# <a name="_Toc395637761">Création d'un compte de base de données DocumentDB</a>

Pour configurer un compte de base de données DocumentDB dans Azure, ouvrez le [portail de gestion Azure][portail de gestion Azure],
puis cliquez sur la vignette Galerie Azure sur la page d'accueil ou sur « + » dans le coin inférieur gauche de l'écran.

![Alt text](./media/documentdb-nodejs-application/image2.png)

Cette opération ouvre la Galerie Azure, dans laquelle vous pouvez sélectionner
l'un des nombreux services Azure disponibles. Dans la Galerie, sélectionnez « Données, stockage et
sauvegarde » dans la liste des catégories.

![Alt text](./media/documentdb-nodejs-application/image3.png)

Sélectionnez l'option pour Azure DocumentDB.

![Alt text](./media/documentdb-nodejs-application/image4.png)

Sélectionnez ensuite « Créer » en bas de l'écran.

![Alt text](./media/documentdb-nodejs-application/image5.png)

Le volet « Nouveau DocumentDB » s'ouvre. Vous pouvez y spécifier
le nom, la région, l'échelle, le groupe de ressources et d'autres paramètres pour votre
nouveau compte.

![Alt text](./media/documentdb-nodejs-application/image6.png)

Une fois que vous avez fourni les valeurs pour votre compte, cliquez sur
« Créer » : le processus de configuration commence alors la création de
votre compte de base de données. Lorsque ce processus est terminé,
une notification apparaît dans la zone des notifications du portail
et la vignette sur l'écran d'accueil (si vous avez choisi d'en créer une)
affiche l'action terminée.

![Alt text](./media/documentdb-nodejs-application/image7.png)

Une fois la configuration terminée, un clic sur la vignette DocumentDB
dans l'écran d'accueil affiche le volet principal correspondant au
nouveau compte DocumentDB.

![Alt text](./media/documentdb-nodejs-application/image8.png) 
![Alt text](./media/documentdb-nodejs-application/image9.png)

À l'aide du bouton « Clés », accédez à votre URL de point de terminaison
et à la clé primaire. Copiez-les dans le Presse-papiers et gardez-les à portée
de main. Nous les utiliserons dans l'application web que nous allons ensuite créer.

## <a name="_Toc395783178">Création d'une application Node.js</a>

Dans Visual Studio, sélectionnez Fichier – Nouveau projet et choisissez de créer une « Application Microsoft Azure Express de base ».

![Alt text](./media/documentdb-nodejs-application/image10.png)

Une application Express de base est créée pour vous. Si vous êtes
invité à « installer les dépendances », sélectionnez « Oui ». Tous les packages
npm requis pour une nouvelle application Express sont installés.

Lorsque l'opération est terminée, l'Explorateur de solutions a
normalement l'aspect suivant :

![Alt text](./media/documentdb-nodejs-application/image11.png)

Comme le montre cet écran, Express, Jade et Stylus sont installés.

Appuyez sur F5 dans Visual Studio pour créer le projet, démarrer
Node.js et afficher un navigateur avec l'équivalent Express de « Hello
World ».

![Alt text](./media/documentdb-nodejs-application/image12.png)

## <a name="_Toc395783179">Installation de modules supplémentaires</a>

Le fichier **package.json** est l'un des fichiers créés à la racine du
projet. Il contient une liste de modules supplémentaires qui
sont indispensables pour les applications Express. Ensuite, lorsque vous déployez
cette application vers un site web Azure, ce fichier permet de
déterminer quels modules doivent être installés sur Azure pour prendre en charge votre application.

![Alt text](./media/documentdb-nodejs-application/image13.png)

Nous avons besoin d'installer deux autres packages pour ce didacticiel.

Cliquez avec le bouton droit sur « npm » dans l'Explorateur de solutions et sélectionnez Installer les
packages npm.

![Alt text](./media/documentdb-nodejs-application/image14.png)

Dans la boîte de dialogue « Installer les nouveaux packages npm », tapez **nconf** afin de rechercher
le module. Celui-ci permet à l'application de lire les valeurs de
configuration de la base de données à partir d'un fichier de configuration.

![Alt text](./media/documentdb-nodejs-application/image15.png)

Enfin, installez le module Azure DocumentDB de la même façon en recherchant
**documentdb**. Il s'agit du module où se produit toute la magie de
DocumentDB.

Une fois que vous avez installé ces deux modules supplémentaires et les
dépendances, ils apparaissent normalement dans l'Explorateur de solutions sous les modules
**npm**.

![Alt text](./media/documentdb-nodejs-application/image16.png)

Un contrôle rapide du fichier **package.json** de l'application doit
permettre de voir les modules supplémentaires. Ce fichier indiquera
ultérieurement à Azure quels packages doivent être téléchargés et
installés lors de l'exécution de votre application.

Modifiez le fichier package.json, si besoin est, pour qu'il ait le même aspect que l'exemple ci-dessous.

![Alt text](./media/documentdb-nodejs-application/image17.png)

Ce code indique à Node (et à Azure ultérieurement) que votre application
dépend de ces modules supplémentaires.

# <a name="_Toc395783180">Utilisation du service DocumentDB dans une application Node</a>

Ceci concerne l'ensemble de l'installation et de la configuration initiales. Venons-en à présent à la raison de notre présence ici, à savoir écrire du code avec Azure DocumentDB. Pour commencer, modifiez le fichier **app.js** situé à la racine de l'application Express que nous venons de créer. Recherchez les lignes suivantes dans le fichier :

    app.get('/', routes.index);
    app.get('/users', user.list);

Remplacez-les par les deux lignes suivantes :

    app.get('/', routes.index);
    app.post('/', routes.createOrUpdateItem);

Ce code indique à l'application ce qu'il faut faire avec les méthodes GET et POST par défaut sur un formulaire que nous allons créer ensuite.

Recherchez à présent le fichier **index.js** qui se trouve dans le dossier **routes**. Ouvrez-le dans votre éditeur et supprimez tout le code qui s'y trouve.

Ajoutez le code suivant au début du fichier :

    // import the modules we will use
    var DocumentDBClient = require('documentdb').DocumentClient;
    var nconf = require('nconf');

    // tell nconf which config file to use
    nconf.env();
    nconf.file({ file: 'config.json' });

Ce code définit les modules dont nous allons nous servir : **documentdb** et **nconf**.

**nconf** est un module qui vous permet de charger des valeurs de configuration telles que des chaînes de connexion de base de données à partir de fichiers externes plutôt que d'incorporer ces valeurs directement dans le code. nconf recherche par défaut un fichier **config.json** pour sa configuration.

Poursuivons et créons un fichier texte vide intitulé **config.json** à la racine du projet (même emplacement que le fichier app.js)

Ouvrez ce nouveau fichier **config.json** et entrez les valeurs suivantes en fonction de votre point de terminaison DocumentDB. Veillez à définir correctement les valeurs HOST et MASTER\_KEY.

    {
        "HOST"       : "<insert your DocDB endpoint here>",
        "AUTH_KEY"   : "<insert either primary or secondary key here>",
        "DATABASE"   : "ToDoList",
        "COLLECTION" : "Items"
    }

Revenez ensuite au fichier **index.js**. Ajoutez les lignes suivantes après les dernières lignes pour lire le fichier de configuration et stocker les valeurs dans des variables de niveau page.

    var host = nconf.get("HOST");
    var authKey = nconf.get("AUTH_KEY");
    var databaseId = nconf.get("DATABASE");
    var collectionId = nconf.get("COLLECTION");

Une fois ceci effectué, ajoutez le code suivant à **index.js**.

    // create some global variables which we will use later to hold instances of the DocumentDBClient, Database and Collection

    // create an instance of the DocumentDB client
    var client = new DocumentDBClient(host, { masterKey: authKey });

    exports.index = function (req, res) {       
        // before we can query for Items in the document store, we need to ensure we 
        // have a database with a collection then use the collection to read the documents
        readOrCreateDatabase(function (database) {
            readOrCreateCollection(database, function (collection) {
                listItems(collection, function (items) {
                    res.render('index', { title: 'My ToDo List', tasks: items });
                });    
            });
        });
    };

    exports.createOrUpdateItem = function (req, res) {
        //first have to set the database & collection context so that we have the self links   
        readOrCreateDatabase(function (database) {
            readOrCreateCollection(database, function (collection) {
                        
                //if we find an item on the form, we'll create it in the database
                var item = req.body.item;
                if (item) {
                    createItem(collection, item, function () {
                    res.redirect('/');
                });
                
                //else let's look for items marked as completed, 
                //and update that item in the database
                } else {
                    var completed = req.body.completed;
                    
                    //check if completed is actually an Array, if not make it one. 
                    //this happens when you select only one item            
                    if (!completed.forEach)
                        completed = [completed];
                    
                    //use a recursive function to loop through items in 
                    //array calling updateItem each time through                                    
                    function updater(i) {
                        if (i < completed.length) {
                            updateItem(collection, completed[i], function () {
                                updater(i + 1);
                            });
                        } else {
                            res.redirect('/');
                        }
                    }
                    
                    //kick off the recursion
                    updater(0);
                }
            });
        });
    }

Il s'agit des deux fonctions que nous avons demandé à l'application d'utiliser précédemment dans le fichier **app.js**, lors de la définition des itinéraires. Lorsqu'une opération GET parvient à la vue d'index, la fonction **exports.index** est exécutée ; de même, lorsqu'une opération POST est reçue par la vue d'index, la fonction **exports.createOfUpdateItem** est exécutée.

Ces deux fonctions effectuent tout le travail de l'application que nous sommes en train de créer ; elles font cependant appel à d'autres fonctions, tout simplement pour rendre le code plus lisible et facile à suivre. Ajoutez ensuite le code suivant au fichier **index.js**. Il contient toutes les méthodes utilisées par les deux fonctions ci-dessus et tous les appels à DocumentDB. Nous examinerons chaque fonction de manière plus détaillée ultérieurement.

    // update item
    var updateItem = function (collection, itemId, callback) {
        //first fetch the document based on the id
        getItem(collection, itemId, function (doc) {
            //now replace the document with the updated one
            doc.completed = true;
            client.replaceDocument(doc._self, doc, function (err, replacedDoc) {
                if (err) {
                    throw (err);
                }
                
                callback();
            });
        });
    }

    // get item
    var getItem = function (collection, itemId, callback) {      
        client.queryDocuments(collection._self, 'SELECT * FROM root r WHERE r.id="' + itemId + '"').toArray(function (err, results) {
            if (err) {
                throw (err);
            }

            callback(results[0]);
        });
    }

    // create new item
    var createItem = function (collection, documentDefinition, callback) {
        documentDefinition.completed = false;
        client.createDocument(collection._self, documentDefinition, function (err, doc) {
            if (err) {
                throw (err);
            }
            
            callback();
        });
    }

    // query the provided collection for all non-complete items
    var listItems = function (collection, callback) {
        client.queryDocuments(collection._self, 'SELECT * FROM root r WHERE r.completed=false').toArray(function (err, docs) {
            if (err) {
                throw (err);
            }
            
            callback(docs);
        });
    }

    // if the database does not exist, then create it, else return the database object
    var readOrCreateDatabase = function (callback) {
        client.queryDatabases('SELECT * FROM root r WHERE r.id="' + databaseId + '"').toArray(function (err, results) {
            if (err) {
                // some error occured, rethrow up
                throw (err);
            }
            if (!err && results.length === 0) {
                // no error occured, but there were no results returned 
                // indicating no database exists matching the query            
                client.createDatabase({ id: databaseId }, function (err, createdDatabase) {
                    callback(createdDatabase);
                });
            } else {
                // we found a database
                callback(results[0]);
            }
        });
    };

    // if the collection does not exist for the database provided, create it, else return the collection object
    var readOrCreateCollection = function (database, callback) {
        client.queryCollections(database._self, 'SELECT * FROM root r WHERE r.id="' + collectionId + '"').toArray(function (err, results) {
            if (err) {
                // some error occured, rethrow up
                throw (err);
            }           
            if (!err && results.length === 0) {
                // no error occured, but there were no results returned 
                //indicating no collection exists in the provided database matching the query
                client.createCollection(database._self, { id: collectionId }, function (err, createdCollection) {
                    callback(createdCollection);
                });
            } else {
                // we found a collection
                callback(results[0]);
            }
        });
    };

**updateItem** : met à jour un document dans la base de données en fonction de l'ID d'élément transmis à partir du formulaire. La fonction utilise cet ID pour exécuter une méthode ***readDocument*** sur DocumentDB afin de lire le document particulier que nous avons stocké. Elle définit ensuite l'attribut « completed » du document sur true, indiquant que ce dernier est à présent terminé, puis procède au remplacement du document dans la base de données.

**getItem** : utilise ***queryDocuments*** pour obtenir un élément unique de la base de données avec la propriété id de l'élément.

**createItem** : utilise la méthode ***createDocument*** pour créer un document dans la base de données, sur la base du nom d'élément et de la catégorie d'élément entrés sur le formulaire. Cette fonction définit également l'indicateur completed sur false pour indiquer que cet élément n'est pas encore terminé.

**listItems** : utilise ***queryDocuments*** pour rechercher dans la collection tous les documents qui ne sont pas encore terminés ou pour lesquels completed=false. La fonction utilise la grammaire de requête DocumentDB basée sur ANSI-SQL pour illustrer cette fonctionnalité d'interrogation familière, mais puissante.

**readOrCreateDatabase** : utilise ***queryDatabases*** pour vérifier s'il existe déjà une base de données avec ce nom. Si nous n'en trouvons aucune, nous continuons et utilisons ***createDatabase*** pour créer une base de données avec l'identificateur fourni (à partir de notre fichier de configuration) sur le point de terminaison spécifié (lui aussi dans notre fichier de configuration).

**readOrCreateCollection** : comme avec readOrCreateDatabase, cette méthode a d'abord essayé de trouver une collection avec l'identificateur fourni. S'il en existe une, elle est renvoyée ; sinon, elle est créée pour vous.

Nous venons de passer en revue tout le code qui est requis pour interagir avec DocumentDB dans cet exemple d'application.

Intéressons-nous à présent à la création de l'interface utilisateur pour permettre à un utilisateur d'interagir réellement avec notre application. L'application Express que nous avons créée utilise **Jade** comme moteur de vue. Pour plus d'informations sur Jade, consultez la page <http://jade-lang.com/>

Ouvrez le fichier **layout.jade** qui se trouve dans le dossier **views** et remplacez le contenu par ce qui suit :

    doctype html
    html
      head
        title= title
        meta(http-equiv='X-UA-Compatible', content='IE=10')
        link(rel='stylesheet', href='/stylesheets/style.css')
        link(rel='stylesheet', href='/stylesheets/bootstrap.min.css')
      body.app
        nav.navbar.navbar-fixed-top
          div.navbar-inner
            container
                a.brand(href='/') My Tasks
        block content

Ce code demande au moteur **Jade** de générer un rendu HTML pour notre application et crée un « **bloc** » intitulé « **content** » dans lequel nous pouvons fournir la mise en page de nos pages de contenu.

Ouvrez maintenant le fichier **index.jade**, la vue associée à index.js, puis remplacez le contenu par ce qui suit :

    extends layout

    block content
      h1= title
      br

      form(action="/", method="post")
        table.table.table-striped.table-bordered
          tr
            td Name
            td Category
            td Complete
          each task in tasks
            tr
                td #{task.name}
                td #{task.category}
                td 
                    input(type="checkbox", name="completed", value="#{task.id}", checked=(task.completed == true))
        button.btn(type="submit") Update tasks

      hr

      form.well(action="/", method="post")
        label Item Name:  
        input(name="item[name]", type="textbox")
        label Item Category: 
        input(name="item[category]", type="textbox")
        br
        button.btn(type="submit") Add item

Ce code étend la mise en page et fournit du contenu pour l'espace
réservé « block content » que nous avons vu plus haut dans le fichier layout.jade.

Dans cette mise en page, nous avons créé deux fichiers HTML. Le premier contient une table
pour nos données et un bouton qui nous permet de mettre à jour les éléments. Le second
formulaire contient quelques champs de saisie et un bouton qui nous permet de créer un élément.

Lorsqu'un clic est effectué sur l'un ou l'autre de ces boutons, la fonction **createOrUpdateItem**
 (c'est-à-dire lorsqu'une opération POST est effectuée) que nous avons créée dans le fichier **index.js**
 est appelée. Si cette page est simplement chargée directement
(c'est-à-dire si une opération GET est effectuée), la fonction **index** est exécutée.

Ceci devrait être suffisant pour que notre application puisse fonctionner.

</h1>
# <a name="_Toc395783181">Exécution locale de l'application</a>

Pour tester l'application sur votre ordinateur local, appuyez sur F5 dans
Visual Studio. Ceci a pour effet de créer l'application, de démarrer Node.js et de lancer un navigateur avec
une page similaire à celle illustrée ci-dessous :

![Alt text](./media/documentdb-nodejs-application/image18.png)

1\. Utilisez les champs Nom de l'élément et Catégorie de l'élément pour
entrer les informations, puis cliquez sur **Ajouter un élément**.

2\. La page doit se mettre à jour et afficher le nouvel élément dans la liste
des tâches.

![Alt text](./media/documentdb-nodejs-application/image19.png)

3\. Pour terminer une tâche, activez simplement la case à cocher dans la colonne
Terminé, puis cliquez sur **Mettre à jour les tâches**.

</h1>
# <a name="_Toc395783182">Déploiement de votre application vers Sites Web Azure</a>

Si Node.js Tools pour Visual Studio est installé, le déploiement vers
Sites Web Azure s'effectue facilement en quelques étapes rapides.

Cliquez avec le bouton droit sur votre projet et sélectionnez « Publier ».

![Alt text](./media/documentdb-nodejs-application/image20.png)

Parcourez ensuite les étapes de l'Assistant Publication afin de fournir la
configuration requise pour votre site web Azure. L'Assistant vous permet de choisir un site web
existant pour le mettre à jour ou d'en créer un.

Après avoir fourni la configuration nécessaire, appuyez simplement sur « Publier ».

![Alt text](./media/documentdb-nodejs-application/image21.png)

Parcourez ensuite les étapes de l'Assistant Publication afin de fournir la
configuration requise pour votre site web Azure. L'Assistant vous permet de choisir un site web
existant pour le mettre à jour ou d'en créer un.

Après avoir fourni la configuration nécessaire, appuyez simplement sur « Publier ».

Visual Studio se connecte alors à votre abonnement Azure et publie
cette application Node.js.

En quelques secondes, Visual Studio achève la publication de votre
application web et lance un navigateur dans lequel vous pouvez voir
votre réalisation exécutée dans Azure !

# <a name="_Toc395783183"></a> <a name="_Toc395637775">Conclusion</a>

Félicitations ! Vous venez de créer votre première application web
Express Node.js avec Azure DocumentDB et de la publier sur Sites Web Azure.

Vous pouvez télécharger le code source de l'application de référence complète [ici][ici].

</h1>

  [Alt text]: ./media/documentdb-nodejs-application/image1.png
  [Node.js]: http://nodejs.org/
  [Git]: http://git-scm.com/
  [Visual Studio 2013]: http://msdn.microsoft.com/fr-fr/vstudio/cc136611.aspx
  [Outils Node.js pour Visual Studio]: https://nodejstools.codeplex.com/
  [interface de ligne de commande Azure]: http://azure.microsoft.com/fr-fr/documentation/articles/xplat-cli/
  [portail de gestion Azure]: http://portal.azure.com
  [ici]: http://go.microsoft.com/fwlink/?LinkID=509839&clcid=0x409
