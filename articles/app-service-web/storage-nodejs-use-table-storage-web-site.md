---
title: Application Web Node.js avec le service de Table Azure
description: "Ce didacticiel vous explique comment utiliser le service Azure Table pour stocker les données d’une application Node.hs hébergée dans Azure App Service Web Apps."
tags: azure-portal
services: app-service\web, storage
documentationcenter: nodejs
author: rmcmurray
manager: erikre
editor: 
ms.assetid: 029e6f46-f586-4309-adbf-71c7b8d537d4
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 11/01/2016
ms.author: robmcm
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: a5ff81a2b693c8a7b97783d6a43cab204c7ec3eb


---
# <a name="nodejs-web-app-using-the-azure-table-service"></a>Application Web Node.js avec le service de Table Azure
## <a name="overview"></a>Vue d'ensemble
Ce didacticiel vous indique comment utiliser le service de Table fourni par la gestion des données Azure pour stocker et consulter les données provenant d’une application [node] hébergée dans [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714) Web Apps. Ce didacticiel part du principe que vous avez déjà une certaine expérience en tant qu'utilisateur des applications node et de [Git]

Vous apprendrez à effectuer les opérations suivantes :

* utiliser le gestionnaire de package node (ou npm) pour installer les modules node ;
* utiliser le service de Table Azure ;
* Utilisation de l’interface de ligne de commande Azure pour créer une application web

Dans ce didacticiel, vous allez concevoir une application web simple qui permet de créer, récupérer et finaliser des tâches. Les tâches sont stockées dans le service de Table.

Voici l’application finalisée :

![Une page Web avec une liste de tâches vide][node-table-finished]

> [!NOTE]
> Si vous voulez vous familiariser avec Azure App Service avant d’ouvrir un compte Azure, accédez à la page [Essayer App Service](http://go.microsoft.com/fwlink/?LinkId=523751), où vous pourrez créer immédiatement une application web temporaire dans App Service. Aucune carte de crédit n’est requise ; vous ne prenez aucun engagement.
> 
> 

## <a name="prerequisites"></a>Composants requis
Avant de suivre les instructions de cet article, vérifiez que les éléments suivants sont installés :

* [node] version 0.10.24 ou supérieure
* [Git]

[!INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]

## <a name="create-a-storage-account"></a>Créez un compte de stockage.
Créez un compte de stockage Azure. L'application utilisera ce compte pour stocker les tâches à effectuer.

1. Connectez-vous au [portail Azure](https://portal.azure.com/).
2. Cliquez sur l’icône **Nouveau** en bas à gauche du portail, puis cliquez sur **Données + stockage** > **Stockage**. Attribuez un nom unique au compte de stockage et créez un [groupe de ressources](../azure-resource-manager/resource-group-overview.md).
   
      ![Bouton Nouveau](./media/storage-nodejs-use-table-storage-web-site/configure-storage.png)
   
    Une fois le compte de stockage créé, le bouton **Notifications** affiche la mention **RÉUSSITE** en vert clignotant et le panneau du compte de stockage s’ouvre pour indiquer qu’il appartient au groupe de ressources créé.
3. Dans le panneau du compte de stockage, cliquez sur la section **Paramètres** > **Clés**. Copiez la clé d'accès primaire dans le Presse-papiers.
   
    ![Clé d’accès][portal-storage-access-keys]

## <a name="install-modules-and-generate-scaffolding"></a>Installation des modules et création de la structure
Dans cette section, vous allez créer une application Node et utiliser npm pour ajouter des packages de module. Pour cette application, vous allez utiliser les modules [Express] et [Azure]. Le module Express fournit une infrastructure Model View Controller pour node, tandis que les modules Azure assurent la connectivité vers le service de Table.

### <a name="install-express-and-generate-scaffolding"></a>Installation express et création de la structure
1. À l’aide de la ligne de commande, créez un répertoire nommé **tasklist** et accédez-y.  
2. Entrez la commande suivante pour installer le module Express.
   
        npm install express-generator@4.2.0 -g
   
    Selon le système d'exploitation, il se peut que vous deviez placer « sudo » avant la commande :
   
        sudo npm install express-generator@4.2.0 -g
   
    Le résultat ressemble à l’exemple suivant :
   
        express-generator@4.2.0 /usr/local/lib/node_modules/express-generator
        ├── mkdirp@0.3.5
        └── commander@1.3.2 (keypress@0.1.0)
   
   > [!NOTE]
   > Le paramètre « -g » installe le module globalement. Ainsi, nous pouvons utiliser la commande **express** pour générer la structure de l’application web sans avoir à saisir d’informations supplémentaires concernant le chemin d’accès.
   > 
   > 
3. Pour créer la structure de l'application, entrez la commande **express** :
   
        express
   
    Le résultat de cette commande ressemble à l’exemple suivant :
   
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
   
    Maintenant, le répertoire **tasklist** comporte plusieurs nouveaux répertoires et fichiers.

### <a name="install-additional-modules"></a>Installation de modules supplémentaires
L’un des fichiers créés par **express** est **package.json**. Ce fichier contient une liste de dépendances de module. Ensuite, quand vous déployez l’application dans App Service Web Apps, ce fichier détermine les modules à installer sur Azure.

Sur la ligne de commande, entrez la commande suivante pour installer les modules décrits dans le fichier **package.json** . Il se peut que vous deviez « sudo ».

    npm install

Le résultat de cette commande ressemble à l’exemple suivant :

    debug@0.7.4 node_modules\debug

    cookie-parser@1.0.1 node_modules\cookie-parser
    ├── cookie-signature@1.0.3
    └── cookie@0.1.0

    [...]


Ensuite, entrez la commande suivante pour installer les modules [azure], [node-uuid], [nconf] et [async] :

    npm install azure-storage node-uuid async nconf --save

L’indicateur **--save** ajoute des entrées pour ces modules dans le fichier **package.json**.

Le résultat de cette commande ressemble à l’exemple suivant :

    async@0.9.0 node_modules\async

    node-uuid@1.4.1 node_modules\node-uuid

    nconf@0.6.9 node_modules\nconf
    ├── ini@1.2.1
    ├── async@0.2.9
    └── optimist@0.6.0 (wordwrap@0.0.2, minimist@0.0.10)

    [...]


## <a name="create-the-application"></a>Création de l'application
Maintenant, nous sommes prêts à générer l'application.

### <a name="create-a-model"></a>Création d’un modèle
Un *modèle* est un objet qui représente les données dans votre application. Pour l'application, le seul modèle est un objet tâche qui représente un élément de la liste des tâches. Les tâches ont les champs suivants :

* PartitionKey
* RowKey
* nom (chaîne)
* catégorie (chaîne)
* terminé (booléen)

**PartitionKey** et **RowKey** sont utilisés par le service de Table comme clés de table. Pour plus d'informations, consultez la rubrique [Présentation du modèle de données du service de Table](https://msdn.microsoft.com/library/azure/dd179338.aspx).

1. Dans le répertoire **tasklist**, créez un répertoire appelé **models**.
2. Dans le répertoire **models**, créez un fichier appelé **task.js**. Ce fichier contiendra le modèle des tâches créées par votre application.
3. Au début du fichier **task.js** , ajoutez le code suivant pour référencer les bibliothèques nécessaires :
   
        var azure = require('azure-storage');
          var uuid = require('node-uuid');
        var entityGen = azure.TableUtilities.entityGenerator;
4. Ajoutez le code suivant pour définir et exporter l'objet Task. Cet objet est responsable de la connexion vers la table.
   
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
5. Ajoutez le code suivant pour définir des méthodes supplémentaires sur l'objet Task permettant d'interagir avec les données stockées dans la table :
   
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
6. Enregistrez, puis fermez le fichier **task.js** .

### <a name="create-a-controller"></a>Création d’un contrôleur
Un *contrôleur* gère les requêtes HTTP et génère la réponse HTML.

1. Dans le répertoire **tasklist/routes**, créez un nouveau fichier **tasklist.js** et ouvrez-le dans un éditeur de texte.
2. Ajoutez le code suivant dans **tasklist.js**. Cela charge les modules azure et async, qui sont utilisés par **tasklist.js**. Cela définit également la fonction **TaskList** à qui est transmise une instance de l’objet **Task** défini précédemment :
   
        var azure = require('azure-storage');
        var async = require('async');
   
        module.exports = TaskList;
3. Définissez un objet **TaskList** .
   
        function TaskList(task) {
          this.task = task;
        }
4. Ajoutez les méthodes suivantes à **TaskList**:
   
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
            var self = this;
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

### <a name="modify-appjs"></a>Modification de app.js
1. Dans le répertoire **tasklist**, ouvrez le fichier **app.js**. Ce fichier a été créé ultérieurement à l'aide de la commande **express** .
2. Au début du fichier, ajoutez ce qui suit pour charger le module Azure, définir le nom de la table, la clé de partition et les informations d'identification de stockage utilisés par cet exemple :
   
        var azure = require('azure-storage');
        var nconf = require('nconf');
        nconf.env()
             .file({ file: 'config.json', search: true });
        var tableName = nconf.get("TABLE_NAME");
        var partitionKey = nconf.get("PARTITION_KEY");
        var accountName = nconf.get("STORAGE_NAME");
        var accountKey = nconf.get("STORAGE_KEY");
   
   > [!NOTE]
   > nconf charge les valeurs de configuration de l’une ou l’autre des variables d'environnement ou du fichier **config.json** , que nous allons créer ensuite.
   > 
   > 
3. Dans le fichier app.js, faites défiler le contenu jusqu'à la ligne suivante :
   
        app.use('/', routes);
        app.use('/users', users);
   
    Remplacez les lignes ci-dessus par le code affiché ci-dessous. Cela initialise une instance de <strong>Task</strong> avec une connexion à votre compte de stockage. Elle est transmise à <strong>TaskList</strong>qui l'utilise pour communiquer avec le service de Table :
   
        var TaskList = require('./routes/tasklist');
        var Task = require('./models/task');
        var task = new Task(azure.createTableService(accountName, accountKey), tableName, partitionKey);
        var taskList = new TaskList(task);
   
        app.get('/', taskList.showTasks.bind(taskList));
        app.post('/addtask', taskList.addTask.bind(taskList));
        app.post('/completetask', taskList.completeTask.bind(taskList));
4. Enregistrez le fichier **app.js** .

### <a name="modify-the-index-view"></a>Modification de la vue d'index
1. Ouvrez la **tasklist/views/index.jade** fichier dans un éditeur de texte.
2. Remplacez tout le contenu du fichier par le code suivant. Ceci définit une vue qui affiche les tâches et comprend un formulaire permettant d’ajouter des tâches et de les marquer comme terminées.
   
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
3. Fermez et enregistrez le fichier **index.jade** .

### <a name="modify-the-global-layout"></a>Modification de la disposition générale
Le fichier **layout.jade** du répertoire **views** sert de modèle global aux autres fichiers **.jade**. Dans cette étape, vous allez modifier ce fichier pour utiliser [Twitter Bootstrap](https://github.com/twbs/bootstrap), un kit de ressources qui facilite la création d’une application Web attrayante.

Téléchargez les fichiers du [Twitter Bootstrap](http://getbootstrap.com/), puis procédez à l'extraction. Copiez le fichier **bootstrap.min.css** du dossier **css** de Bootstrap dans le répertoire **public/stylesheets** de votre application.

Dans le dossier **views**, ouvrez le fichier **layout.jade** et remplacez son contenu par ce qui suit :

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

### <a name="create-a-config-file"></a>Création d’un fichier de configuration
Pour exécuter l'application localement, nous allons copier les informations d'identification d’Azure Storage dans un fichier de configuration. Créez un fichier nommé **config.json* * à l’aide du code JSON suivant :

    {
        "STORAGE_NAME": "<storage account name>",
        "STORAGE_KEY": "<storage access key>",
        "PARTITION_KEY": "mytasks",
        "TABLE_NAME": "tasks"
    }

Remplacez **storage account name** par le nom du compte de stockage créé, puis remplacez **storage access key** par la clé d’accès primaire de votre compte de stockage. Par exemple :

    {
        "STORAGE_NAME": "nodejsappstorage",
        "STORAGE_KEY": "KG0oDd..."
        "PARTITION_KEY": "mytasks",
        "TABLE_NAME": "tasks"
    }

Enregistrez ce fichier *dans le répertoire de niveau supérieur* à celui de **tasklist** , comme celui-ci :

    parent/
      |-- config.json
      |-- tasklist/

Cette opération permet de ne pas vérifier le fichier de configuration dans le contrôle source, où il risque de devenir public. Lors du déploiement de l'application dans Azure, nous allons utiliser des variables d'environnement au lieu d'un fichier de configuration.

## <a name="run-the-application-locally"></a>Exécuter l’application localement
Pour tester l'application sur votre machine locale, procédez comme suit :

1. Dans la ligne de commande, modifiez les répertoires vers le répertoire **tasklist** .
2. Utilisez la commande suivante pour lancer l'application en local :
   
        npm start
3. Ouvrez un navigateur web et accédez à l'adresse http://127.0.0.1:3000.
   
    Une page web qui ressemble à l’exemple suivant s’affiche.
   
    ![Une page Web affiche une liste de tâches vide.][node-table-finished]
4. Pour créer un élément de tâche, entrez un nom et une catégorie, puis cliquez sur **Ajouter un élément**. 
5. Pour marquer une tâche comme terminée, cochez **Terminée** puis cliquez sur **Mettre à jour les tâches**.
   
    ![Image du nouvel élément dans la liste de tâches][node-table-list-items]

Bien que l'application s'exécute localement, elle stocke les données du service de Table Azure.

## <a name="deploy-your-application-to-azure"></a>Déploiement de votre application dans Azure
Les étapes de cette section utilisent les outils en ligne de commande Azure pour créer une application web dans Azure App Service et la déployer à l’aide de Git. Pour effectuer ces étapes, vous devez disposer d’un abonnement Azure.

> [!NOTE]
> Ces étapes peuvent également être effectuées depuis le [portail Azure](https://portal.azure.com/). Consultez [Créer et déployer une application web Node.js dans Azure App Service].
> 
> S’il s’agit de la première application web que vous créez, vous devez utiliser le portail Azure pour la déployer.
> 
> 

Pour commencer, installez l’ [interface de ligne de commande Azure] en entrant la commande suivante dans la ligne de commande :

    npm install azure-cli -g

### <a name="import-publishing-settings"></a>Importation de paramètres de publication
Dans cette étape, vous allez télécharger un fichier contenant des informations sur votre abonnement.

1. Entrez la commande suivante :
   
        azure account download
   
    Cette commande lance un navigateur et accède à la page de téléchargement. Si vous y êtes invité, connectez-vous avec le compte associé à votre abonnement Azure.
   
    <!-- ![The download page][download-publishing-settings] -->
   
    Le téléchargement du fichier se lance automatiquement. Si ce n'est pas le cas, cliquez sur le lien situé en haut de la page pour télécharger le fichier manuellement. Enregistrez le fichier et notez le chemin d'accès.
2. Entrez la commande suivante pour importer les paramètres :
   
        azure account import <path-to-file>
   
    Indiquez le chemin et le nom du fichier des paramètres de publication téléchargé à l'étape précédente.
3. Une fois les paramètres importés, supprimez le fichier des paramètres de publication. Ce fichier n’est plus nécessaire, et il contient des informations sensibles concernant votre abonnement Azure.

### <a name="create-an-app-service-web-app"></a>Créer une application web App Service
1. Dans la ligne de commande, modifiez les répertoires vers le répertoire **tasklist** .
2. Pour créer une application web, utilisez la commande suivante.
   
        azure site create --git
   
    Vous êtes invité à spécifier le nom et l’emplacement de l’application web. Indiquez un nom unique et sélectionnez le même emplacement géographique que votre compte de stockage Azure.
   
    Le paramètre `--git` crée un référentiel Git sur Azure pour cette application web. Il initialise également un référentiel Git dans le répertoire actif s’il n’en existe aucun, et ajoute un référentiel [Git distant] nommé « azure » qui est utilisé pour publier l'application dans Azure. Enfin, il crée un fichier **web.config** , qui contient les paramètres utilisés par Azure pour héberger les applications Node. Si le paramètre `--git` est omis, mais que le répertoire contient un référentiel Git, la commande crée quand même le référentiel « azure » distant.
   
    Une fois cette commande terminée, le résultat doit ressembler à ce qui suit. Notez que la ligne qui commence par **Website created at** contient l'URL de l’application web.
   
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
   
   > [!NOTE]
   > S’il s’agit de la première application web App Service dans votre abonnement, vous devez utiliser le portail Azure pour la créer. Pour plus d’informations, consultez la page [Créer et déployer une application web Node.js dans Azure App Service].
   > 
   > 

### <a name="set-environment-variables"></a>Définition des variables d'environnement
Dans cette étape, vous allez ajouter des variables d'environnement à la configuration de votre application web sur Azure.
Sur la ligne de commande, entrez les informations suivantes :

    azure site appsetting add
        STORAGE_NAME=<storage account name>;STORAGE_KEY=<storage access key>;PARTITION_KEY=mytasks;TABLE_NAME=tasks


Remplacez **<storage account name>** par le nom du compte de stockage créé, puis remplacez **<storage access key>** par la clé d’accès primaire de votre compte de stockage. (Utilisez les mêmes valeurs que le fichier config.json que vous avez créé précédemment).

L’autre possibilité consiste à définir les variables d'environnement dans le [Portail Azure](https://portal.azure.com/):

1. Ouvrez le volet de l’application web en cliquant sur **Parcourir** > **Web Apps** > nom de votre application web.
2. Dans le volet de votre application web, cliquez sur **Tous les paramètres** > **Paramètres de l’application**.
   
     <!-- ![Top Menu](./media/storage-nodejs-use-table-storage-web-site/PollsCommonWebSiteTopMenu.png) -->
3. Faites défiler l’écran jusqu'à la section **Paramètres de l'application** et ajoutez des paires clé/valeur.
   
     ![Paramètres de l'application](./media/storage-nodejs-use-table-storage-web-site/storage-tasks-appsettings.png)
4. Cliquez sur **ENREGISTRER**.

### <a name="publish-the-application"></a>Publication de l'application
Pour publier l'application, validez les fichiers de code dans Git puis transférez-les vers azure/master.

1. Définissez vos informations d'identification de déploiement.
   
        azure site deployment user set <name> <password>
2. Ajoutez et validez vos fichiers d'application.
   
        git add .
        git commit -m "adding files"
3. Envoyez la validation à l'application web App Service :
   
        git push azure master
   
    Utilisez **master** comme branche cible. À la fin du déploiement, une déclaration similaire à l’exemple suivant s'affiche :
   
        To https://username@tabletasklist.azurewebsites.net/TableTasklist.git
          * [new branch]      master -> master
4. Une fois l'opération de transmission terminée, accédez à l'URL de l’application web renvoyée précédemment par la commande `azure create site` pour voir votre application.

## <a name="next-steps"></a>Étapes suivantes
Bien que les étapes de cet article décrivent l’utilisation du service de Table pour stocker des informations, vous pouvez aussi utiliser [MongoDB](https://mlab.com/azure/). 

## <a name="additional-resources"></a>Ressources supplémentaires
[interface de ligne de commande Azure]

## <a name="whats-changed"></a>Changements apportés
* Pour obtenir un guide présentant les modifications apportées dans le cadre de la transition entre Sites Web et App Service, consultez la page [Azure App Service et les services Azure existants](http://go.microsoft.com/fwlink/?LinkId=529714)

<!-- URLs -->

[Créer et déployer une application web Node.js dans Azure App Service]: web-sites-nodejs-develop-deploy-mac.md
[Centre de développement Azure]: /develop/nodejs/

[node]: http://nodejs.org
[Git]: http://git-scm.com
[Express]: http://expressjs.com
[gratuitement]: http://windowsazure.com
[Git distant]: http://git-scm.com/docs/git-remote

[interface de ligne de commande Azure]: ../xplat-cli-install.md

[azure]: https://github.com/Azure/azure-sdk-for-node
[node-uuid]: https://www.npmjs.com/package/node-uuid
[nconf]: https://www.npmjs.com/package/nconf
[async]: https://www.npmjs.com/package/async

[Portail Azure]: https://portal.azure.com

[Création et déploiement d’une application Node.js sur un site Web Azure]: web-sites-nodejs-develop-deploy-mac.md

<!-- Image References -->

[node-table-finished]: ./media/storage-nodejs-use-table-storage-web-site/table_todo_empty.png
[node-table-list-items]: ./media/storage-nodejs-use-table-storage-web-site/table_todo_list.png
[download-publishing-settings]: ./media/storage-nodejs-use-table-storage-web-site/azure-account-download-cli.png
[portal-new]: ./media/storage-nodejs-use-table-storage-web-site/plus-new.png
[portal-storage-account]: ./media/storage-nodejs-use-table-storage-web-site/new-storage.png
[portal-quick-create-storage]: ./media/storage-nodejs-use-table-storage-web-site/quick-storage.png
[portal-storage-access-keys]: ./media/storage-nodejs-use-table-storage-web-site/manage-access-keys.png
[go-to-dashboard]: ./media/storage-nodejs-use-table-storage-web-site/go_to_dashboard.png
[web-configure]: ./media/storage-nodejs-use-table-storage-web-site/sql-task-configure.png
[app-settings-save]: ./media/storage-nodejs-use-table-storage-web-site/savebutton.png
[app-settings]: ./media/storage-nodejs-use-table-storage-web-site/storage-tasks-appsettings.png



<!--HONumber=Nov16_HO3-->


