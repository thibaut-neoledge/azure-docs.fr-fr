<properties linkid="dev-nodejs-tutorials-web-site-with-storage" urlDisplayName="Web Site with Storage" pageTitle="Node.js web site with table storage | Microsoft Azure" metaKeywords="Azure table storage Node.js, Azure Node.js application, Azure Node.js tutorial, Azure Node.js example" description="A tutorial that teaches you how to use the Azure Table service to store data from a Node application hosted on an Azure web site." metaCanonical="" services="web-sites,storage" documentationCenter="Node.js" title="Node.js Web Application using the Azure Table Service" authors="" solutions="" manager="" editor="" />

Application Web Node.js avec le service de Table Azure
======================================================

Ce didacticiel vous indique comment utiliser le service de Table fourni par la gestion des données Azure pour stocker vos données et y accéder à partir d'une application [node](http://nodejs.org) hébergée sur Azure. Ce didacticiel part du principe que vous avez déjà une certaine expérience en tant qu'utilisateur des applications node et de [Git](http://git-scm.com)

Vous apprendrez à effectuer les opérations suivantes :

-   utiliser le gestionnaire de package node (ou npm) pour installer les modules node ;

-   utiliser le service de Table Azure ;

-   utiliser l'outil en ligne de commande Azure pour Mac et Linux afin de créer un site Web Azure.

Ce didacticiel va vous permettre de créer une application simple de gestion des tâchés basée sur le Web, permettant ainsi la création, la récupération et la finalisation de tâches. Les tâches sont stockées dans le service de Table.

Les fichiers de projet pour ce didacticiel doivent être stockés dans un répertoire nommé **tasklist** et l'application terminée ressemblera à celle ci-dessous :

![Page Web affichant une liste de tâches vide](./media/storage-nodejs-use-table-storage-web-site/table_todo_empty.png)

**Remarque**

Ce didacticiel fait référence au dossier **tasklist**. Le chemin complet vers ce dossier n'est pas précisé, car il peut varier en fonction du système d'exploitation. Créez ce dossier dans un emplacement facilement accessible dans votre système de fichiers local, par exemple **\~/node/tasklist** ou **c:\\node\\tasklist**

**Remarque**

Plusieurs des étapes ci-dessous vous demandent d'utiliser la ligne de commande. Pour ce faire, utilisez la ligne de commande de votre système d'exploitation, par exemple **cmd.exe** (Windows) ou **Bash** (Unix Shell). Sur les systèmes OS X, vous pouvez accéder à la ligne de commande via l'application Terminal.

Configuration requise
---------------------

Avant de suivre les instructions de cet article, vérifiez que les éléments suivants sont installés :

-   [node](http://nodejs.org) version 0.6.14 ou supérieure

-   [Git](http://git-scm.com)

-   Un éditeur de texte

-   Un navigateur Web

[WACOM.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

Création d'un compte de stockage
--------------------------------

Procédez comme suit pour créer un compte de stockage. Ce compte est utilisé pour les instructions suivantes de ce didacticiel.

1.  Ouvrez votre navigateur Web et accédez au [portail Azure](http://windowsazure.com). Si vous y êtes invité, connectez-vous avec vos informations d'abonnement Azure.

2.  En bas du portail, cliquez sur **+ NEW**, puis sélectionnez **Storage Account**.

    ![+nouveau](./media/storage-nodejs-use-table-storage-web-site/plus-new.png)

    ![compte de stockage](./media/storage-nodejs-use-table-storage-web-site/new-storage.png)

3.  Sélectionnez **Quick Create**, puis entrez l'URL et la région ou le groupe d'affinités pour ce compte de stockage. Comme il s'agit d'un didacticiel ne nécessitant pas d'être répliqué globalement, désactivez **Enable Geo-Replication**. Enfin, cliquez sur « Create Storage Account ».

    ![création rapide](./media/storage-nodejs-use-table-storage-web-site/quick-storage.png)

    Notez l'URL saisie, car elle sera référencée en tant que nom de compte lors des étapes suivantes.

4.  Une fois le compte de stockage créé, cliquez sur **Manage Keys** en bas de la page. Cela affiche la clé d'accès primaire et la clé d'accès secondaire de ce compte de stockage. Copiez et enregistrez la clé d'accès primaire, puis cliquez sur la coche.

    ![clés d'accès](./media/storage-nodejs-use-table-storage-web-site/manage-access-keys.png)

Installation des modules et création de la structure
----------------------------------------------------

Dans cette section, vous allez créer une application Node et utiliser npm pour ajouter des packages de module. Pour l'application de liste de tâches, vous allez utiliser les modules [Express](http://expressjs.com) et [Azure](https://github.com/WindowsAzure/azure-sdk-for-node). Le module Express fournit une infrastructure Model View Controller pour node, tandis que les modules Azure assurent la connectivité vers le service de Table.

### Installation du module Express et génération de la structure

1.  Dans la ligne de commande, modifiez les répertoires vers le répertoire **tasklist**. Si le répertoire **tasklist** n'existe pas, créez-le.

2.  Entrez la commande suivante pour installer Express.

         npm install express -g

    **Remarque**

    L'emploi du paramètre '-g' sur certains systèmes d'exploitation peut entraîner l'erreur suivante : **Error: EPERM, chmod '/usr/local/bin/express'**. Il peut vous être demandé d'exécuter le compte en tant qu'administrateur. Si c'est le cas, utilisez la commande **sudo** pour exécuter npm à un niveau supérieur de privilèges.

    Le résultat de cette commande devrait ressembler à l'exemple ci-dessous :

		express@3.4.0 C:\Users\larryfr\AppData\Roaming\npm\node_modules\express
		├── methods@0.0.1
		├── fresh@0.2.0
		├── cookie-signature@1.0.1
		├── range-parser@0.0.4
		├── buffer-crc32@0.2.1
		├── cookie@0.1.0
		├── debug@0.7.2
		├── mkdirp@0.3.5
		├── commander@1.2.0 (keypress@0.1.0)
		├── send@0.1.4 (mime@1.2.11)
		└── connect@2.9.0 (uid2@0.0.2, pause@0.0.1, qs@0.6.5, bytes@0.2.0, multiparty@2.1.8)

    **Remarque**

    Avec le paramètre '-g', le module express est installé dans sa globalité. Ceci permet ensuite d'accéder à la commande **express** pour générer la structure d'un site Web sans avoir à entrer d'autres informations de chemin ou d'emplacement.

3.  Pour créer la structure qui sera utilisée pour cette application, utilisez la commande **express** :

         express

    Le résultat de cette commande devrait ressembler à l'exemple ci-dessous :

         create : .
         create : ./package.json
         create : ./app.js
         create : ./public/javascripts
         create : ./public
         create : ./public/stylesheets
         create : ./public/stylesheets/style.css
         create : ./views
         create : ./views/layout.jade
         create : ./views/index.jade
         create : ./routes
         create : ./routes/index.js
         create : ./routes/user.js
         create : ./public/images

         install dependencies:
           $ cd . && npm install

         run the app:
           $ node app

Une fois la commande terminée, vous devez disposer de plusieurs nouveaux répertoires et fichiers dans le répertoire **tasklist**.

### Installation de modules supplémentaires

Le fichier **package.json** est un des fichiers créés par la commande **express**. Il contient une liste de modules supplémentaires qui sont nécessaires pour les applications Express. Ensuite, lorsque vous déployez cette application sur un site Web Azure, ce fichier est utilisé pour déterminer quels modules doivent être installés sur Azure pour prendre en charge votre application.

1.  Dans la ligne de commande, remplacez le répertoire par le dossier **tasklist**, puis entrez les commandes suivantes pour installer les modules décrits dans le fichier **package.json** :

         npm install

    Le résultat de cette commande devrait ressembler à l'exemple ci-dessous :

		express@3.4.0 node_modules\express
		├── methods@0.0.1
		├── range-parser@0.0.4
		├── cookie-signature@1.0.1
		├── fresh@0.2.0
		├── buffer-crc32@0.2.1
		├── cookie@0.1.0
		├── debug@0.7.2
		├── mkdirp@0.3.5
		├── commander@1.2.0 (keypress@0.1.0)
		├── send@0.1.4 (mime@1.2.11)
		└── connect@2.9.0 (uid2@0.0.2, pause@0.0.1, bytes@0.2.0, qs@0.6.5, multiparty@2.1.8)

		jade@0.35.0 node_modules\jade
		├── character-parser@1.2.0
		├── commander@2.0.0
		├── mkdirp@0.3.5
		├── monocle@1.1.50 (readdirp@0.2.5)
		├── transformers@2.1.0 (promise@2.0.0, css@1.0.8, uglify-js@2.2.5)
		├── with@1.1.1 (uglify-js@2.4.0)
		└── constantinople@1.0.2 (uglify-js@2.4.0)

    Ceci permet d'installer tous les modules requis par Express.

2.  Ensuite, entrez la commande suivante pour installer les modules [azure](https://github.com/WindowsAzure/azure-sdk-for-node), [node-uuid], [nconf] et [async] en local et pour enregistrer une entrée leur correspondant dans le fichier **package.json** :

         npm install azure node-uuid async nconf --save

    Le résultat de cette commande devrait ressembler à l'exemple ci-dessous :

         async@0.2.9 node_modules\async

         node-uuid@1.4.1 node_modules\node-uuid

		nconf@0.6.7 node_modules\nconf
		├── ini@1.1.0
		├── async@0.1.22
		├── pkginfo@0.2.3
		└── optimist@0.3.7 (wordwrap@0.0.2)

		azure@0.7.15 node_modules\azure
		├── dateformat@1.0.2-1.2.3
		├── xmlbuilder@0.4.2
		├── envconf@0.0.4
		├── node-uuid@1.2.0
		├── mpns@2.0.1
		├── underscore@1.5.2
		├── mime@1.2.11
		├── validator@1.5.1
		├── tunnel@0.0.2
		├── wns@0.5.3
		├── xml2js@0.2.8 (sax@0.5.5)
		└── request@2.25.0 (json-stringify-safe@5.0.0, aws-sign@0.3.0, forever-agent@0.5.0, tunnel-agent@0.3.0, qs@0.6.5, oauth-sign@0.3.0, cookie-jar@0.3.0, node-uuid@1.4.1, http-signature@0.10.0, form-data@0.1.1, hawk@1.0.0)

Utilisation du service de Table dans une application Node
---------------------------------------------------------

Dans cette section, vous allez étendre l'application de base créée par la commande **express** en ajoutant un fichier **task.js** qui contient le modèle de vos tâches. Vous allez également modifier le fichier **app.js** existant et créer un fichier **tasklist.js** qui utilise le modèle.

### Création du modèle

1.  Dans le répertoire **tasklist**, créez un répertoire appelé **models**.

2.  Dans le répertoire **models**, créez un fichier appelé **task.js**. Ce fichier contiendra le modèle des tâches créées par votre application.

3.  Au début du fichier **task.js**, ajoutez le code suivant pour référencer les bibliothèques nécessaires :

         var azure = require('azure');

    var uuid = require('node-uuid');

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

1.  Ensuite, ajoutez le code suivant pour définir des méthodes supplémentaires sur l'objet Task permettant d'interagir avec les données stockées dans la table :

         Task.prototype = {
           find: function(query, callback) {
             self = this;
             self.storageClient.queryEntities(query, function entitiesQueried(error, entities) {
               if(error) {
                 callback(error);
               } else {
                 callback(null, entities);
               }
             });
           },

           addItem: function(item, callback) {
             self = this;
             item.RowKey = uuid();
             item.PartitionKey = self.partitionKey;
             item.completed = false;
             self.storageClient.insertEntity(self.tableName, item, function entityInserted(error) {
               if(error) {  
                 callback(error);
               }
               callback(null);
             });
           },

           updateItem: function(item, callback) {
             self = this;
             self.storageClient.queryEntity(self.tableName, self.partitionKey, item, function entityQueried(error, entity) {
               if(error) {
                 callback(error);
               }
               entity.completed = true;
               self.storageClient.updateEntity(self.tableName, entity, function entityUpdated(error) {
                 if(error) {
                   callback(error);
                 }
                 callback(null);
               });
             });
           }
         }

2.  Enregistrez, puis fermez le fichier **task.js**.

### Création du contrôleur

1.  Dans le répertoire **tasklist/routes**, créez un nouveau fichier **tasklist.js** et ouvrez-le dans un éditeur de texte.

2.  Ajoutez le code suivant à **tasklist.js**. Cela charge les modules azure et async, qui sont utilisés par **tasklist.js**. Cela définit également la fonction **TaskList** à qui est transmise une instance de l'objet **Task** défini précédemment :

         var azure = require('azure');
         var async = require('async');

         module.exports = TaskList;

         function TaskList(task) {
           this.task = task;
         }

3.  Continuez à modifier le fichier **tasklist.js** en ajoutant les méthodes utilisées pour afficher les tâches (**showTasks**), ajouter les tâches (**addTask**) et marquer les tâches comme terminées (**completeTasks**) :

         TaskList.prototype = {
           showTasks: function(req, res) {
             self = this;
             var query = azure.TableQuery
               .select()
               .from(self.task.tableName)
               .where('completed eq 
         ', false);
             self.task.find(query, function itemsFound(error, items) {
               res.render('index', { title: 'My ToDo List ', tasks: items });
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
                 if(error) {
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

1.  Dans le répertoire **tasklist**, ouvrez le fichier **app.js** dans un éditeur de texte. Le fichier a été créé au préalable lors de l'exécution de la commande **express**.

2.  Au début du fichier, ajoutez ce qui suit pour charger le module azure, définir le nom de la table, la clé de partition et les informations d'identification de stockage utilisés par cet exemple :

         var azure = require('azure');
         var nconf = require('nconf');
         nconf.env()
              .file({ file: 'config.json' });
         var tableName = nconf.get("TABLE_NAME")
         var partitionKey = nconf.get("PARTITION_KEY")
         var accountName = nconf.get("STORAGE_NAME")
         var accountKey = nconf.get("STORAGE_KEY");

    **Remarque**

    nconf charge les valeurs de configuration à partir des variables d'environnement ou du fichier **config.json**, que nous allons créer ultérieurement.

3.  Dans le fichier app.js, faites défiler le contenu jusqu'à la ligne suivante :

         app.get('/', routes.index);
         app.get('/users', user.list);

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

1.  Remplacez les répertoires par le répertoire **views** et ouvrez le fichier **index.jade** dans un éditeur de texte.

2.  Remplacez le contenu du fichier **index.jade** par le code suivant. Ceci permet de définir l'affichage des tâches existantes, ainsi qu'un formulaire d'ajout de nouvelles tâches et de signalement des tâches existantes comme terminées.

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
               each task in tasks
                 tr
                   td #{task.name}
                   td #{task.category}
                   - var day   = task.Timestamp.getDate();
                   - var month = task.Timestamp.getMonth() + 1;
                   - var year  = task.Timestamp.getFullYear();
                   td #{month + "/" + day + "/" + year}
                   td
                     input(type="checkbox", name="#{task.RowKey}", value="#{!task.itemCompleted}", checked=task.itemCompleted)
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

Le fichier **layout.jade** du répertoire **views** sert de modèle global aux autres fichiers **.jade**. Dans cette étape, il s'agit de modifier ce fichier afin d'utiliser [Twitter Bootstrap](https://github.com/twbs/bootstrap), un kit de ressources (ou toolkit) qui permet de concevoir facilement une belle présentation pour un site Web.

1.  Téléchargez les fichiers du [Twitter Bootstrap](http://getbootstrap.com/), puis procédez à l'extraction. Copiez le fichier **bootstrap.min.css** du dossier **bootstrap\\dist\\css** vers le répertoire **public\\stylesheets** de votre application de liste de tâches.

2.  Dans le dossier **views**, ouvrez **layout.jade** dans votre éditeur de texte et remplacez son contenu par le code suivant :

         doctype 5
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

Exécution locale de l'application
---------------------------------

Pour tester l'application sur votre machine locale, procédez comme suit :

1.  Dans la ligne de commande, modifiez les répertoires vers le répertoire **tasklist**.

2.  Utilisez la commande suivante pour lancer l'application en local :

         node app.js

3.  Ouvrez un navigateur Web et accédez à l'adresse http://127.0.0.1:3000. Cette opération doit entraîner l'affichage d'une page Web similaire à la suivante :

    ![Une page Web affiche une liste de tâches vide.](./media/storage-nodejs-use-table-storage-web-site/table_todo_empty.png)

4.  Utilisez les champs **Item Name** et **Item Category** pour entrer les informations, puis cliquez sur **Add item**.

5.  La page se met à jour et affiche l'élément dans la table ToDo List.

    ![Image du nouvel élément dans la liste de tâches](./media/storage-nodejs-use-table-storage-web-site/table_todo_list.png)

6.  Pour terminer une tâche, activez simplement la case à cocher dans la colonne Complete, puis cliquez sur **Update tasks**.

7.  Pour interrompre le processus node, accédez à la ligne de commande, puis appuyez sur les touches **CTRL** et **C**.

Déploiement de votre application dans Azure
-------------------------------------------

Les étapes de cette section utilisent les outils en ligne de commande Azure pour créer un nouveau site Web Azure, puis déploient votre application à l'aide de Git. Pour effectuer ces étapes, vous devez disposer d'un abonnement Azure.

**Remarque**

Ces étapes peuvent également être effectuées depuis le portail Azure. Pour savoir comment utiliser le portail Azure pour déployer une application Node.js, consultez la page [Création et déploiement d'une application Node.js sur des sites Web Azure](http://content-ppe.windowsazure.com/en-us/develop/nodejs/tutorials/create-a-website-(mac)/).

**Remarque**

S'il s'agit de votre premier site Web Azure, vous devez utiliser le portail Azure pour déployer cette application.

### Activation de la fonctionnalité de site Web Azure

Si vous n'avez pas encore d'abonnement Azure, vous pouvez vous enregistrer [gratuitement](http://windowsazure.com). Une fois que vous êtes inscrit, suivez cette procédure pour activer la fonctionnalité de site Web Azure.

[WACOM.INCLUDE [antares-iaas-signup](../includes/antares-iaas-signup.md)]

### Installation de l'outil en ligne de commande Azure pour Mac et Linux

Pour installer les outils en ligne de commande, utilisez la commande suivante :

    npm install azure-cli -g

**Remarque**

Si vous avez déjà installé le **Kit de développement logiciel (SDK) Azure pour Node.js** à partir du [Centre de développement Azure](/en-us/develop/nodejs/), les outils en ligne de commande doivent déjà être installés. Pour plus d'informations, consultez la page [Outil en ligne de commande Azure pour Mac et Linux](/en-us/develop/nodejs/how-to-guides/command-line-tools/).

**Remarque**

Alors que les outils en ligne de commande ont été créés en priorité pour les utilisateurs Mac et Linux, ils sont basés sur Node.js et peuvent fonctionner sur tout système capable d'exécuter Node.

### Importation de paramètres de publication

Avant d'utiliser les outils en ligne de commande avec Azure, vous devez télécharger un fichier comportant des informations relatives à votre abonnement. Procédez comme suit pour télécharger et importer ce fichier.

1.  Dans la ligne de commande, modifiez les répertoires vers le répertoire **tasklist**.

2.  Entrez la commande suivante pour lancer le navigateur et accéder à la page de téléchargement. Si vous y êtes invité, connectez-vous avec le compte associé à votre abonnement.

         azure account download

    ![Page de téléchargement](./media/storage-nodejs-use-table-storage-web-site/azure-account-download-cli.png)

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

### Création d'un site Web Azure

1.  Dans la ligne de commande, modifiez les répertoires vers le répertoire **tasklist**.

2.  Utilisez la commande suivante pour créer un site Web Azure.

         azure site create --git

    Vous êtes invité à indiquer le nom du site Web et le centre de données sur lequel il sera situé. Fournissez un nom unique et sélectionnez le centre de données le plus proche de vous du point de vue géographique.

    Le paramètre `--git` crée un référentiel Git sur Azure pour ce site Web. Il initialise également un référentiel Git dans le répertoire en cours si aucun n'existe déjà. Il crée également une télécommande [Git remote](http://git-scm.com/docs/git-remote) appelée « azure », qui sera utilisée pour publier l'application sur Azure. Enfin, il crée un fichier **web.config**, qui contient les paramètres utilisés par Azure pour héberger les applications Node.

    **Remarque**

    Si cette commande est exécutée depuis un répertoire qui contient déjà un référentiel Git, le répertoire n'est pas réinitialisé.

    **Remarque**

    Si le paramètre « --git » est omis, mais que le répertoire contient un référentiel Git, la commande distante « azure » est quand même créée.

    Une fois cette commande terminée, le résultat doit ressembler à ce qui suit. Notez que la ligne commençant par **Web site created at** contient l'URL du site Web.

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

    **Remarque**

    S'il s'agit du premier site Web Azure de votre abonnement, vous devrez utiliser le portail pour le créer. Pour plus d'informations, consultez la page [Création et déploiement d'une application Node.js dans un site Web Azure](/en-us/develop/nodejs/tutorials/create-a-website-(mac)/).

### Publication de l'application

1.  Dans la fenêtre Terminal, passez dans le répertoire **tasklist** si vous ne vous y trouvez pas déjà.

2.  Utilisez les commandes suivantes pour ajouter, puis valider les fichiers dans le référentiel :

         git add .
         git commit -m "adding files"

3.  Lorsque vous copiez les dernières modifications du référentiel Git vers le site Web Azure, vous devez spécifier que la branche cible est **master**, car elle est utilisée pour le contenu du site Web.

         git push azure master

    À la fin du déploiement, une déclaration similaire à la suivante doit s'afficher :

         To https://username@tabletasklist.azurewebsites.net/TableTasklist.git

    \* [new branch] master -> master

4.  Une fois l'opération Push terminée, accédez à l'URL du site Web renvoyée précédemment par la commande `azure create site` pour voir votre application.

### Passage à une variable d'environnement

Précédemment, nous avons implémenté un code qui recherche une variable d'environnement **SQL\_CONN** pour la chaîne de connexion ou charge la valeur du fichier **config.json**. Les étapes suivantes vous indiquent comment créer une paire clé/valeur dans la configuration de votre site Web. L'application y accède via une variable d'environnement.

1.  Dans le portail de gestion, cliquez sur **Web Sites**, puis sélectionnez votre site Web.

    ![Ouvrir le tableau de bord du site Web](./media/storage-nodejs-use-table-storage-web-site/go_to_dashboard.png)

2.  Cliquez sur **CONFIGURE**, puis recherchez la section **app settings** de la page.

    ![configurer le lien](./media/storage-nodejs-use-table-storage-web-site/sql-task-configure.png)

3.  Dans la section **app settings**, entrez **STORAGE\_NAME** dans le champ **KEY**, puis le nom de votre compte de stockage dans le champ **VALUE**. Cliquez sur la coche pour passer au champ suivant. Répétez ce processus pour les clés et valeurs suivantes :

    -   **STORAGE\_KEY** : clé d'accès de votre compte de stockage

    -   **PARTITION\_KEY** : « mytasks »

    -   **TABLE\_NAME** : « tasks »

    ![paramètres d'application](./media/storage-nodejs-use-table-storage-web-site/storage-tasks-appsettings.png)

4.  Pour finir, cliquez sur l'icône **SAVE** en bas de la page pour appliquer cette modification à l'environnement d'exécution.

    ![enregistrer les paramètres d'application](./media/storage-nodejs-use-table-storage-web-site/savebutton.png)

5.  Dans la ligne de commande, remplacez les répertoires par le répertoire **tasklist**, puis entrez la commande suivante pour supprimer le fichier **config.json** :

         git rm config.json
         git commit -m "Removing config file"

6.  Exécutez les commandes suivantes pour déployer les modifications sur Azure :

         git push azure master

Une fois les modifications déployées sur Azure, votre application Web doit continuer à fonctionner, car la chaîne de connexion est lue depuis l'entrée **app settings**. Pour vérifier, remplacez la valeur de l'entrée **STORAGE\_KEY** dans **app settings** par une valeur non valide. Une fois la valeur enregistrée, le site Web doit rencontrer une erreur en raison du paramètre de clé d'accès de stockage non valide.

Étapes suivantes
----------------

Bien que les étapes de cet article décrivent l'utilisation du service de Table pour stocker des informations, vous pouvez aussi utiliser MongoDB. Pour plus d'informations, consultez la page [Application Web Node.js avec MongoDB](/en-us/develop/nodejs/tutorials/website-with-mongodb-(Mac)/).

Ressources supplémentaires
--------------------------

[Outil en ligne de commande Azure pour Mac et Linux]
 [Création et déploiement d'une application Node.js dans un site Web Azure] : /en-us/develop/nodejs/tutorials/create-a-website-(mac)/ [Publication sur les sites Web Azure avec Git](../CommonTasks/publishing-with-git) : /en-us/develop/nodejs/common-tasks/publishing-with-git/ [Centre de développement Azure] : /en-us/develop/nodejs/

