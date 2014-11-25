<properties linkid="dev-nodejs-tutorials-web-site-with-storage" urlDisplayName="Website with Storage" pageTitle="Node.js website with table storage | Microsoft Azure" metaKeywords="Azure table storage Node.js, Azure Node.js application, Azure Node.js tutorial, Azure Node.js example" description="A tutorial that teaches you how to use the Azure Table service to store data from a Node application hosted on an Azure website." metaCanonical="" services="web-sites,storage" documentationCenter="Node.js" title="Node.js Web Application using the Azure Table Service" authors="larryfr" solutions="" manager="" editor="" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="nodejs" ms.topic="article" ms.date="01/01/1900" ms.author="larryfr" />

# Application Web Node.js avec le service de Table Azure

Ce didacticiel vous indique comment utiliser le service de Table fourni par la gestion des données Azure pour stocker vos données et y accéder à partir d'une application [node][node] hébergée sur Azure. Ce didacticiel part du principe que vous avez déjà une certaine expérience en tant qu'utilisateur des applications node et de [Git][Git]

Vous apprendrez à effectuer les opérations suivantes :

-   utiliser le gestionnaire de package node (ou npm) pour installer les modules node ;

-   utiliser le service de Table Azure ;

-   utiliser l'outil en ligne de commande Azure pour Mac et Linux pour créer un site web Azure.

Dans ce didacticiel, vous allez concevoir une application Web simple pour la gestion des tâches qui vous permet de créer et de récupérer des tâches et de les marquer comme terminées. Les tâches sont stockées dans le service de Table.

Les fichiers de projets de ce didacticiel sont stockés dans un répertoire appelé **tasklist** et l'application terminée ressemble à ce qui suit :

![Une page Web avec une liste de tâches vide][Une page Web avec une liste de tâches vide]

> [WACOM.NOTE] Ce didacticiel fait référence au dossier **tasklist**. Le chemin complet de ce dossier n'est pas mentionné, en raison des différences entre les systèmes d'exploitation. Créez ce dossier dans un emplacement facile d'accès sur votre système de fichiers local, par exemple **~/node/tasklist** ou **c:\\node\\tasklist**.

> [WACOM.NOTE] De nombreuses étapes ci-dessous mentionnent l'utilisation de la ligne de commande. Pour ce faire, utilisez la ligne de commande de votre système d'exploitation, par exemple **cmd.exe** (Windows) ou **Bash** (Unix Shell). Sur les systèmes OS X, vous pouvez accéder à la ligne de commande via l'application Terminal.

## Configuration requise

Avant de suivre les instructions de cet article, vérifiez que les éléments suivants sont installés :

-   [node][node] version 0.10.24 ou supérieure

-   [Git][Git]

-   Un éditeur de texte

-   Un navigateur Web

[WACOM.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

## Créer un compte de stockage

Procédez comme suit pour créer un compte de stockage. Ce compte est utilisé pour les instructions suivantes de ce didacticiel.

1.  Ouvrez votre navigateur Web et accédez au [portail Azure][portail Azure]. Si vous y êtes invité, connectez-vous avec vos informations d'abonnement Azure.

2.  En bas du portail, cliquez sur **+ NEW**, puis sélectionnez **Storage Account**.

    ![+nouveau][+nouveau]

    ![compte de stockage][compte de stockage]

3.  Sélectionnez **Quick Create**, puis entrez l'URL et la région ou le groupe d'affinités pour ce compte de stockage. Comme il s'agit d'un didacticiel ne nécessitant pas d'être répliqué globalement, désactivez **Enable Geo-Replication**. Enfin, cliquez sur « Create Storage Account ».

    ![création rapide][création rapide]

    Notez l'URL saisie, car elle sera référencée en tant que nom de compte lors des étapes suivantes.

4.  Une fois le compte de stockage créé, cliquez sur **Manage Keys** en bas de la page. Cela affiche la clé d'accès primaire et la clé d'accès secondaire de ce compte de stockage. Copiez et enregistrez la clé d'accès primaire, puis cliquez sur la coche.

    ![clés d'accès][clés d'accès]

## Installation des modules et création de la structure

Dans cette section, vous allez créer une application Node et utiliser npm pour ajouter des packages de module. Pour l'application de liste de tâches, vous allez utiliser les modules [Express][Express] et [Azure][Azure]. Le module Express fournit une infrastructure Model View Controller pour node, tandis que les modules Azure assurent la connectivité vers le service de Table.

### Installation express et création de la structure

1.  Dans la ligne de commande, accédez au répertoire **tasklist**. Si le répertoire **tasklist** n'existe pas, créez-le.

2.  Entrez la commande suivante pour installer la commande express.

        npm install express-generator@4.2.0 -g

    > [WACOM.NOTE] Lorsque vous utilisez le paramètre « -g » sur certains systèmes d'exploitation, vous pouvez obtenir une erreur **Error: EPERM, chmod '/usr/local/bin/express'** et une instruction demandant que le compte soit exécuté en tant qu'administrateur. Si cette erreur survient, utilisez la commande **sudo** pour exécuter npm avec des privilèges plus élevés.

    Le résultat de cette commande doit ressembler à ceci :

        express-generator@4.2.0 C:\Users\username\AppData\Roaming\npm\node_modules\express-generator
        ├── mkdirp@0.3.5
        └── commander@1.3.2 (keypress@0.1.0)

    > [WACOM.NOTE] Le paramètre « -g » utilisé lors de l'installation du module express l'installe de façon globale. Cela permet d'accéder à la commande **express** pour générer un modèle automatique de site web sans devoir taper d'autres informations de chemin d'accès.\*\*

3.  Pour créer la structure qui sera utilisée pour cette application, utilisez la commande **express** :

        express

    Le résultat de cette commande doit ressembler à ceci :

           create : .
           create : ./package.json
           create : ./app.js
           create : ./public
           create : ./public/images
           create : ./routes
           create : ./routes/index.js
           create : ./routes/users.js
           create : ./public/stylesheets
           create : ./public/stylesheets/style.css
           create : ./views
           create : ./views/index.jade
           create : ./views/layout.jade
           create : ./views/error.jade
           create : ./public/javascripts
           create : ./bin
           create : ./bin/www

           install dependencies:
             $ cd . && npm install

           run the app:
             $ DEBUG=my-application ./bin/www

    Une fois la commande terminée, vous devez disposer de plusieurs nouveaux répertoires et fichiers dans le répertoire **tasklist**.

### Installation de modules supplémentaires

Le fichier **package.json** est un des fichiers créés par la commande **express**. Il contient une liste de modules supplémentaires qui sont nécessaires pour les applications Express. Ensuite, lorsque vous déployez cette application vers un site web Azure, ce fichier permet de déterminer quels modules doivent être installés sur Azure pour prendre en charge votre application.

1.  Dans la ligne de commande, remplacez le répertoire par le dossier **tasklist**, puis entrez les commandes suivantes pour installer les modules décrits dans le fichier **package.json** :

        npm install

    Le résultat de cette commande doit ressembler à ceci :

        debug@0.7.4 node_modules\debug

        static-favicon@1.0.2 node_modules\static-favicon

        morgan@1.0.1 node_modules\morgan
        └── bytes@0.3.0

        cookie-parser@1.0.1 node_modules\cookie-parser
        ├── cookie-signature@1.0.3
        └── cookie@0.1.0

        body-parser@1.0.2 node_modules\body-parser
        ├── qs@0.6.6
        ├── raw-body@1.1.7 (string_decoder@0.10.25-1, bytes@1.0.0)
        └── type-is@1.1.0 (mime@1.2.11)

        express@4.2.0 node_modules\express
        ├── parseurl@1.0.1
        ├── merge-descriptors@0.0.2
        ├── utils-merge@1.0.0
        ├── cookie@0.1.2
        ├── escape-html@1.0.1
        ├── cookie-signature@1.0.3
        ├── debug@0.8.1
        ├── fresh@0.2.2
        ├── qs@0.6.6
        ├── range-parser@1.0.0
        ├── methods@1.0.0
        ├── buffer-crc32@0.2.1
        ├── serve-static@1.1.0
        ├── path-to-regexp@0.1.2
        ├── send@0.3.0 (debug@0.8.0, mime@1.2.11)
        ├── type-is@1.1.0 (mime@1.2.11)
        └── accepts@1.0.1 (negotiator@0.4.7, mime@1.2.11)

        jade@1.3.1 node_modules\jade
        ├── commander@2.1.0
        ├── character-parser@1.2.0
        ├── mkdirp@0.3.5
        ├── monocle@1.1.51 (readdirp@0.2.5)
        ├── constantinople@2.0.1 (uglify-js@2.4.15)
        ├── transformers@2.1.0 (promise@2.0.0, css@1.0.8, uglify-js@2.2.5)
        └── with@3.0.0 (uglify-js@2.4.15)

    Ceci permet d'installer tous les modules requis par Express.

2.  Ensuite, entrez la commande suivante pour installer les modules [azure][Azure], [node-uuid], [nconf] et [async] en local et pour enregistrer une entrée leur correspondant dans le fichier **package.json** :

        npm install azure-storage node-uuid async nconf --save

    Le résultat de cette commande doit ressembler à ceci :

        async@0.9.0 node_modules\async

        node-uuid@1.4.1 node_modules\node-uuid

        nconf@0.6.9 node_modules\nconf
        ├── ini@1.2.1
        ├── async@0.2.9
        └── optimist@0.6.0 (wordwrap@0.0.2, minimist@0.0.10)

        azure-storage@0.3.0 node_modules\azure-storage
        ├── extend@1.2.1
        ├── xmlbuilder@0.4.3
        ├── mime@1.2.11
        ├── validator@3.1.0
        ├── underscore@1.4.4
        ├── xml2js@0.2.7 (sax@0.5.2)
        └── request@2.27.0 (forever-agent@0.5.2, aws-sign@0.3.0, json-stringify-safe@5.0.0, tunnel-agent@0.3.0, qs@0.6.6, oauth-sign@0.3.0, cookie-jar@0.3.0, form-data@0.1.4, hawk@1.0.0, http-signature@0.10.0)

## Utilisation du service de Table dans une application Node

Dans cette section, vous allez étendre l'application de base créée par la commande **express** en ajoutant un fichier **task.js** qui contient le modèle de vos tâches. Vous allez également modifier le fichier **app.js** existant et créer un fichier **tasklist.js** qui utilise le modèle.

### Création du modèle

1.  Dans le répertoire **tasklist**, créez un répertoire appelé **models**.

2.  Dans le répertoire **models**, créez un fichier appelé **task.js**. Ce fichier contiendra le modèle des tâches créées par votre application.

3.  Au début du fichier **task.js**, ajoutez le code suivant pour référencer les bibliothèques nécessaires :

        var azure = require('azure-storage');
        var uuid = require('node-uuid');
        var entityGen = azure.TableUtilities.entityGenerator;

4.  Ensuite, vous allez ajouter du code pour définir et exporter l'objet Task. Cet objet est responsable de la connexion vers la table.

        module.exports = Task;

        function Task(storageClient, tableName, partitionKey) {
          this.storageClient = storageClient;
          this.tableName = tableName;
          this.partitionKey = partitionKey;
          this.storageClient.createTableIfNotExists(tableName, function tableCreated(error) {
            if(error) {
              throw error;
            }
          });
        };

5.  Ensuite, ajoutez le code suivant pour définir des méthodes supplémentaires sur l'objet Task permettant d'interagir avec les données stockées dans la table :

        Task.prototype = {
          find: function(query, callback) {
            self = this;
            self.storageClient.queryEntities(this.tableName, query, null, function entitiesQueried(error, result) {
              if(error) {
                callback(error);
              } else {
                callback(null, result.entries);
              }
            });
          },

          addItem: function(item, callback) {
            self = this;
            // use entityGenerator to set types
            // NOTE: RowKey must be a string type, even though
            // it contains a GUID in this example.
            var itemDescriptor = {
              PartitionKey: entityGen.String(self.partitionKey),
              RowKey: entityGen.String(uuid()),
              name: entityGen.String(item.name),
              category: entityGen.String(item.category),
              completed: entityGen.Boolean(false)
            };
            self.storageClient.insertEntity(self.tableName, itemDescriptor, function entityInserted(error) {
              if(error){  
                callback(error);
              }
              callback(null);
            });
          },

          updateItem: function(rKey, callback) {
            self = this;
            self.storageClient.retrieveEntity(self.tableName, self.partitionKey, rKey, function entityQueried(error, entity) {
              if(error) {
                callback(error);
              }
              entity.completed._ = true;
              self.storageClient.updateEntity(self.tableName, entity, function entityUpdated(error) {
                if(error) {
                  callback(error);
                }
                callback(null);
              });
            });
          }
        }

6.  Enregistrez, puis fermez le fichier **task.js**.

### Création du contrôleur

1.  Dans le répertoire **tasklist/routes**, créez un nouveau fichier **tasklist.js** et ouvrez-le dans un éditeur de texte.

2.  Ajoutez le code suivant dans **tasklist.js**. Cela charge les modules azure et async, qui sont utilisés par **tasklist.js**. Cela définit également la fonction **TaskList** à qui est transmise une instance de l'objet **Task** défini précédemment :

        var azure = require('azure-storage');
        var async = require('async');

        module.exports = TaskList;

        function TaskList(task) {
          this.task = task;
        }

3.  Continuez à modifier le fichier **tasklist.js** en ajoutant les méthodes utilisées pour afficher les tâches (**showTasks**), ajouter les tâches (**addTask**) et marquer les tâches comme terminées (**completeTasks**) :

        TaskList.prototype = {
          showTasks: function(req, res) {
            self = this;
            var query = new azure.TableQuery()
              .where('completed eq ?', false);
            self.task.find(query, function itemsFound(error, items) {
              res.render('index',{title: 'My ToDo List ', tasks: items});
            });
          },

          addTask: function(req,res) {
            var self = this      
            var item = req.body.item;
            self.task.addItem(item, function itemAdded(error) {
              if(error) {
                throw error;
              }
              res.redirect('/');
            });
          },

          completeTask: function(req,res) {
            var self = this;
            var completedTasks = Object.keys(req.body);
            async.forEach(completedTasks, function taskIterator(completedTask, callback) {
              self.task.updateItem(completedTask, function itemsUpdated(error) {
                if(error){
                  callback(error);
                } else {
                  callback(null);
                }
              });
            }, function goHome(error){
              if(error) {
                throw error;
              } else {
               res.redirect('/');
              }
            });
          }
        }

4.  Enregistrez le fichier **tasklist.js**.

### Modification de app.js

1.  Dans le répertoire **tasklist**, ouvrez le fichier **app.js** dans un éditeur de texte. Ce fichier a été créé ultérieurement à l'aide de la commande **express**.

2.  Au début du fichier, ajoutez ce qui suit pour charger le module azure, définir le nom de la table, la clé de partition et les informations d'identification de stockage utilisés par cet exemple :

        var azure = require('azure-storage');
        var nconf = require('nconf');
        nconf.env()
             .file({ file: 'config.json'});
        var tableName = nconf.get("TABLE_NAME");
        var partitionKey = nconf.get("PARTITION_KEY");
        var accountName = nconf.get("STORAGE_NAME");
        var accountKey = nconf.get("STORAGE_KEY");

    > [WACOM.NOTE] nconf charge les valeurs de configuration à partir des variables d'environnement ou du fichier **config.json**, qui sera créé plus tard.

3.  Dans le fichier app.js, faites défiler le contenu jusqu'à la ligne suivante :

        app.use('/', routes);
        app.use('/users', users);

    Remplacez les lignes ci-dessus par le code affiché ci-dessous. Cela initialise une instance de **Task** avec une connexion vers votre compte de stockage. Elle est transmise à **TaskList**, qui l'utilise pour communiquer avec le service de Table :

        var TaskList = require('./routes/tasklist');
        var Task = require('./models/task');
        var task = new Task(azure.createTableService(accountName, accountKey), tableName, partitionKey);
        var taskList = new TaskList(task);

        app.get('/', taskList.showTasks.bind(taskList));
        app.post('/addtask', taskList.addTask.bind(taskList));
        app.post('/completetask', taskList.completeTask.bind(taskList));

4.  Enregistrez le fichier **app.js**.

### Modification de la vue d'index

1.  Passez dans le répertoire **views** et ouvrez le fichier **index.jade** dans un éditeur de texte.

2.  Remplacez le contenu du fichier **index.jade** par le code qui suit. Ce code définit la vue pour l'affichage des tâches existantes, ainsi qu'un formulaire pour l'ajout de nouvelles tâches et pour marquer les tâches existantes comme terminées.

        extends layout

        block content
          h1= title
          br

          form(action="/completetask", method="post")
            table.table.table-striped.table-bordered
              tr
                td Name
                td Category
                td Date
                td Complete
              if (typeof tasks === "undefined")
                tr
                  td 
              else
                each task in tasks
                  tr
                    td #{task.name._}
                    td #{task.category._}
                    - var day   = task.Timestamp._.getDate();
                    - var month = task.Timestamp._.getMonth() + 1;
                    - var year  = task.Timestamp._.getFullYear();
                    td #{month + "/" + day + "/" + year}
                    td
                      input(type="checkbox", name="#{task.RowKey._}", value="#{!task.completed._}", checked=task.completed._)
            button.btn(type="submit") Update tasks
          hr
          form.well(action="/addtask", method="post")
            label Item Name: 
            input(name="item[name]", type="textbox")
            label Item Category: 
            input(name="item[category]", type="textbox")
            br
            button.btn(type="submit") Add item

3.  Fermez et enregistrez le fichier **index.jade**.

### Modification de la disposition générale

Le fichier **layout.jade** du répertoire **views** sert de modèle global aux autres fichiers **.jade**. Dans cette étape, vous allez le modifier pour utiliser [Twitter Bootstrap][Twitter Bootstrap], qui est un kit de ressources qui facilite la conception d'un site web bien présenté.

1.  Téléchargez les fichiers du [Twitter Bootstrap][1], puis procédez à l'extraction. Copiez le fichier **bootstrap.min.css** du dossier **bootstrap\\dist\\css** vers le répertoire **public\\stylesheets** de votre application de liste de tâches.

2.  Dans le dossier **views**, ouvrez **layout.jade** dans votre éditeur de texte et remplacez son contenu par le code suivant :

        doctype html
        html
          head
            title= title
            link(rel='stylesheet', href='/stylesheets/bootstrap.min.css')
            link(rel='stylesheet', href='/stylesheets/style.css')
          body.app
            nav.navbar.navbar-default
              div.navbar-header
                a.navbar-brand(href='/') My Tasks
            block content

3.  Enregistrez le fichier **layout.jade**.

### Création du fichier de configuration

Le fichier **config.json** contient la chaîne de connexion utilisée pour établir la connexion vers la base de données SQL. Il est consulté par l'application au moment de l'exécution. Procédez comme suit pour créer ce fichier :

1.  Dans le répertoire **tasklist**, créez un fichier nommé **config.json**, puis ouvrez-le dans un éditeur de texte.

2.  Le contenu du fichier **config.json** doit ressembler au suivant :

        {
            "STORAGE_NAME": "storage account name",
            "STORAGE_KEY": "storage access key",
            "PARTITION_KEY": "mytasks",
            "TABLE_NAME": "tasks"
        }

    Remplacez le **nom du compte de stockage** par celui créé précédemment. Remplacez la **clé d'accès de stockage** par la clé d'accès primaire de votre compte de stockage.

3.  Enregistrez le fichier.

## Exécution locale de l'application

Pour tester l'application sur votre machine locale, procédez comme suit :

1.  Dans la ligne de commande, accédez au répertoire **tasklist**.

2.  Utilisez la commande suivante pour lancer l'application en local :

        npm start

3.  Ouvrez un navigateur web et accédez à l'adresse <http://127.0.0.1:3000>. Une page web semblable à la suivante doit s'afficher :

    ![Une page Web affiche une liste de tâches vide.][Une page Web avec une liste de tâches vide]

4.  Utilisez les champs **Item Name** et **Item Category** pour entrer les informations, puis cliquez sur **Add item**.

5.  La page se met à jour et affiche l'élément dans la table ToDo List.

    ![Image du nouvel élément dans la liste de tâches][Image du nouvel élément dans la liste de tâches]

6.  Pour terminer une tâche, activez simplement la case à cocher dans la colonne Complete, puis cliquez sur **Update tasks**.

7.  Pour interrompre le processus node, accédez à la ligne de commande, puis appuyez sur les touches **CTRL** et **C**.

## Déploiement de votre application dans Azure

Les étapes de cette section utilisent les outils en ligne de commande Azure pour créer un site web Azure, puis Git pour déployer votre application. Pour effectuer ces étapes, vous devez disposer d'un abonnement Azure.

> [WACOM.NOTE] Ces étapes peuvent également être effectuées depuis le portail Azure. Pour savoir comment utiliser le portail Azure pour déployer une application Node.js, consultez la page [Création et déploiement d'une application Node.js sur des sites Web Azure][Création et déploiement d'une application Node.js sur des sites Web Azure].

> [WACOM.NOTE] S'il s'agit du premier site web Azure que vous avez créé, utilisez le portail Azure pour déployer cette application.

### Création d'un abonnement Azure

Si vous n'avez pas encore d'abonnement Azure, vous pouvez vous enregistrer [gratuitement][portail Azure]. Lorsque vous êtes inscrit, procédez aux étapes suivantes du didacticiel.

[WACOM.INCLUDE [antares-iaas-signup](../includes/antares-iaas-signup.md)]

### Installation de l'outil en ligne de commande Azure pour Mac et Linux

Pour installer les outils en ligne de commande, utilisez la commande suivante :

    npm install azure-cli -g

> [WACOM.NOTE] Pour plus d'informations, consultez la rubrique [Installation et configuration de l'interface de ligne de commande multiplateforme Azure][Installation et configuration de l'interface de ligne de commande multiplateforme Azure].

> [WACOM.NOTE] Les outils en ligne de commande ont été créés à l'origine pour les utilisateurs sous Mac et Linux, mais ils sont basés sur Node.js et doivent fonctionner sur tout système capable d'exécuter Node.

### Importation de paramètres de publication

Avant d'utiliser les outils en ligne de commande avec Azure, vous devez télécharger un fichier comportant des informations relatives à votre abonnement. Procédez comme suit pour télécharger et importer ce fichier.

1.  Dans la ligne de commande, accédez au répertoire **tasklist**.

2.  Entrez la commande suivante pour lancer le navigateur et accéder à la page de téléchargement. Si vous y êtes invité, connectez-vous avec le compte associé à votre abonnement.

        azure account download

    ![Page de téléchargement][Page de téléchargement]

    Le téléchargement du fichier doit se lancer automatiquement. Si ce n'est pas le cas, cliquez sur le lien situé en haut de la page pour télécharger le fichier manuellement.

3.  Une fois le téléchargement terminé, utilisez la commande suivante pour importer les paramètres :

        azure account import <path-to-file>

    Indiquez le chemin et le nom du fichier des paramètres de publication téléchargé à l'étape précédente. Une fois la commande terminée, un texte similaire à celui présenté ci-dessous doit s'afficher :

        info:   Executing command account import
        info:   Setting service endpoint to: management.core.windows.net
        info:   Setting service port to: 443
        info:   Found subscription: YourSubscription
        info:   Setting default subscription to: YourSubscription
        warn:   The 'C:\users\username\downloads\YourSubscription-6-7-2012-credentials.publishsettings' file contains sensitive information.
        warn:   Remember to delete it now that it has been imported.
        info:   Account publish settings imported successfully
        info:   account import command OK

4.  Une fois l'importation terminée, il est conseillé de supprimer le fichier de paramètres de publication car il n'est plus nécessaire et il contient des informations sensibles relatives à votre abonnement Azure.

### Création d'un site web Azure

1.  Dans la ligne de commande, accédez au répertoire **tasklist**.

2.  Pour créer un site web Azure, utilisez la commande suivante.

        azure site create --git

    Vous êtes invité à entrer le nom du site web et le centre de données dans lequel il sera placé. Indiquez un nom unique et sélectionnez le centre de données géographiquement proche de votre emplacement.

    L'option `--git` crée un référentiel Git sur Azure pour ce site web. Il initialise également un référentiel Git dans le répertoire en cours si aucun n'existe déjà. Il crée également une télécommande [Git remote][Git remote] appelée « azure », qui sera utilisée pour publier l'application sur Azure. Enfin, il crée un fichier **web.config**, qui contient les paramètres utilisés par Azure pour héberger les applications Node.

    > [WACOM.NOTE] Si cette commande est exécutée depuis un répertoire qui contient déjà un référentiel Git, le répertoire n'est pas réinitialisé.

    > [WACOM.NOTE] Si le paramètre `--git` est omis, le répertoire contient un référentiel Git et le site « azure » distant est tout de même créé.

    Une fois cette commande terminée, le résultat doit ressembler à ce qui suit. Notez que la ligne qui commence par **Website created at** contient l'URL du site web.

        info:   Executing command site create
        help:   Need a site name
        Name: TableTasklist
        info:   Using location southcentraluswebspace
        info:   Executing `git init`
        info:   Creating default .gitignore file
        info:   Creating a new web site
        info:   Created web site at  tabletasklist.azurewebsites.net
        info:   Initializing repository
        info:   Repository initialized
        info:   Executing `git remote add azure https://username@tabletasklist.azurewebsites.net/TableTasklist.git`
        info:   site create command OK

    > [WACOM.NOTE] S'il s'agit du premier site web Azure pour votre abonnement, vous êtes invité à utiliser le portail pour créer le site web. Pour plus d'informations, consultez [Création et déploiement d'une application Node.js dans un site web Azure].

### Publication de l'application

1.  Dans la fenêtre Terminal, passez dans le répertoire **tasklist** si vous ne vous y trouvez pas déjà.

2.  Utilisez les commandes suivantes pour ajouter, puis valider les fichiers dans le référentiel :

        git add .
        git commit -m "adding files"

3.  Lorsque vous envoyez les modifications récentes du référentiel Git sur le site web Azure, vous devez spécifier que la branche cible est **master**, car elle est utilisée pour le contenu du site web.

        git push azure master

    À la fin du déploiement, une déclaration similaire à la suivante doit s'afficher :

        To https://username@tabletasklist.azurewebsites.net/TableTasklist.git
         * [new branch]      master -> master

4.  À la fin de l'opération d'envoi, accédez à l'URL du site web renvoyée précédemment par la commande `azure create site` pour afficher votre application.

### Passage à une variable d'environnement

Précédemment, nous avons implémenté du code qui cherche des variables d'environnement ou charge les valeurs du fichier **config.json**. Dans les étapes suivantes, vous allez créer des paires clé/valeur dans la configuration de votre site web auxquelles l'application accède réellement via une variable d'environnement.

1.  Dans le portail de gestion, cliquez sur **Sites Web** et sélectionnez votre site web.

    ![Ouvrir le tableau de bord du site Web][Ouvrir le tableau de bord du site Web]

2.  Cliquez sur **CONFIGURE**, puis recherchez la section **app settings** de la page.

    ![configurer le lien][configurer le lien]

3.  Dans la section **app settings**, entrez **STORAGE\_NAME** dans le champ **KEY**, puis le nom de votre compte de stockage dans le champ **VALUE**. Cliquez sur la coche pour passer au champ suivant. Répétez ce processus pour les clés et valeurs suivantes :

    -   **STORAGE\_KEY** : clé d'accès de votre compte de stockage

    -   **PARTITION\_KEY** : « mytasks »

    -   **TABLE\_NAME** : « tasks »

    ![paramètres d'application][paramètres d'application]

4.  Pour finir, cliquez sur l'icône **SAVE** en bas de la page pour appliquer cette modification à l'environnement d'exécution.

    ![enregistrer les paramètres d'application][enregistrer les paramètres d'application]

5.  Dans la ligne de commande, remplacez les répertoires par le répertoire **tasklist**, puis entrez la commande suivante pour supprimer le fichier **config.json** :

        git rm config.json
        git commit -m "Removing config file"

6.  Exécutez les commandes suivantes pour déployer les modifications sur Azure :

        git push azure master

Une fois les modifications déployées sur Azure, votre application Web doit continuer à fonctionner, car la chaîne de connexion est lue depuis l'entrée **app settings**. Pour vérifier, remplacez la valeur de l'entrée **STORAGE\_KEY** dans **app settings** par une valeur non valide. Après avoir enregistré cette valeur, le site web doit échouer en raison du paramètre non valide de clé d'accès de stockage.

## Étapes suivantes

Bien que les étapes de cet article décrivent l'utilisation du service de Table pour stocker des informations, vous pouvez aussi utiliser MongoDB. Pour plus d'informations, consultez la page [Application Web Node.js avec MongoDB][Application Web Node.js avec MongoDB].

## Ressources supplémentaires

[Outil en ligne de commande Azure pour Mac et Linux](/fr-fr/documentation/articles/xplat-cli/)

  [node]: http://nodejs.org
  [Git]: http://git-scm.com
  [Une page Web avec une liste de tâches vide]: ./media/storage-nodejs-use-table-storage-web-site/table_todo_empty.png
  [portail Azure]: http://windowsazure.com
  [compte de stockage]: ./media/storage-nodejs-use-table-storage-web-site/new-storage.png
  [création rapide]: ./media/storage-nodejs-use-table-storage-web-site/quick-storage.png
  [clés d'accès]: ./media/storage-nodejs-use-table-storage-web-site/manage-access-keys.png
  [Express]: http://expressjs.com
  [Azure]: https://github.com/Azure/azure-sdk-for-node
  [Twitter Bootstrap]: https://github.com/twbs/bootstrap
  [1]: http://getbootstrap.com/
  [Image du nouvel élément dans la liste de tâches]: ./media/storage-nodejs-use-table-storage-web-site/table_todo_list.png
  [Création et déploiement d'une application Node.js sur des sites Web Azure]: /fr-fr/documentation/articles/web-sites-nodejs-develop-deploy-mac/
  [Installation et configuration de l'interface de ligne de commande multiplateforme Azure]: /fr-fr/documentation/articles/xplat-cli/
  [Page de téléchargement]: ./media/storage-nodejs-use-table-storage-web-site/azure-account-download-cli.png
  [Git remote]: http://git-scm.com/docs/git-remote
  [Ouvrir le tableau de bord du site Web]: ./media/storage-nodejs-use-table-storage-web-site/go_to_dashboard.png
  [configurer le lien]: ./media/storage-nodejs-use-table-storage-web-site/sql-task-configure.png
  [paramètres d'application]: ./media/storage-nodejs-use-table-storage-web-site/storage-tasks-appsettings.png
  [enregistrer les paramètres d'application]: ./media/storage-nodejs-use-table-storage-web-site/savebutton.png
  [Application Web Node.js avec MongoDB]: /fr-fr/documentation/articles/web-sites-nodejs-store-data-mongodb/
