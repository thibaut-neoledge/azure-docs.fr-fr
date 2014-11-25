<properties linkid="develop-nodejs-tutorials-web-site-with-mongodb-mongolab" urlDisplayName="Website with MongoDB" pageTitle="Node.js website with MongoDB on MongoLab - Azure" metaKeywords="" description="Learn how to create a Node.js Azure Website that connects to a MongoDB instance hosted on MongoLab." metaCanonical="" services="web-sites,virtual-machines" documentationCenter="nodejs" title="Create a Node.js Application on Azure with MongoDB using the MongoLab Add-On" authors="eric@mongolab.com" solutions="" manager="" editor="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="nodejs" ms.topic="article" ms.date="09/17/2014" ms.author="eric@mongolab.com" />

# Création d'une application Node.js sur Azure avec MongoDB à l'aide du module MongoLab

*Par Eric Sedor, MongoLab*

Bonjour, chers aventuriers ! Bienvenue dans MongoDB-as-a-Service. Ce didacticiel vous apprendra à effectuer les opérations suivantes :

1.  [Configuration de la base de données][Configuration de la base de données] : le module [MongoLab][MongoLab] de l'Azure Store fournit une base de données MongoDB hébergée dans le cloud Azure et gérée par la plateforme de base de données cloud de MongoLab.
2.  [Création de l'application][Création de l'application] : il s'agit d'une simple application Node.js pour conserver une liste de tâches.
3.  [Déploiement de l'application][Déploiement de l'application] : en combinant quelques astuces de configuration, nous apporterons un nouveau souffle à notre code.
4.  [Gestion de base de données][Gestion de base de données] : finalement, vous découvrirez le portail de gestion de base de données basé sur le Web où vous pouvez effectuer des recherches, visualiser et modifier les données facilement.

Au cours de ce didacticiel, n'hésitez pas à envoyer un e-mail à tout moment à l'adresse [\<a href="mailto:support@mongolab.com"\>support@mongolab.com\</a\>][\<a href="mailto:support@mongolab.com"\>support@mongolab.com\</a\>] si vous avez des questions.

Avant de poursuivre, vérifiez que les éléments suivants sont installés :

-   [Node.js][Node.js] version 0.8.14+

-   [Git][Git]

[WACOM.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

## Démarrage rapide

Si vous connaissez déjà l'Azure Store, cette section vous permettra de démarrer rapidement. Dans le cas contraire, consultez la section [Configuration de la base de données][Configuration de la base de données] ci-dessous.

1.  Ouvrez l'Azure Store.<br />
    ![Store][Store]
2.  Cliquez sur le module MongoLab.<br />
    ![MongoLab][1]
3.  Cliquez sur le module MongoLab dans la liste des modules complémentaires, puis cliquez sur **Connection Info**.<br />
    ![ConnectionInfoButton][ConnectionInfoButton]
4.  Copiez le fichier MONGOLAB\_URI dans votre presse-papiers.<br />
    ![ConnectionInfoScreen][ConnectionInfoScreen]
    **Cet URI contient votre nom d’utilisateur et votre mot de passe. Considérez ces informations comme étant sensibles, ne les partagez pas.**
5.  Ajoutez la valeur à la liste Connection Strings dans le menu Configuration de votre application Azure Web :<br />
    ![WebSiteConnectionStrings][WebSiteConnectionStrings]
6.  Dans **Name**, entrez MONGOLAB\_URI.
7.  Dans **Value**, collez la chaîne de connexion obtenue dans la section précédente.
8.  Sélectionnez **Custom** dans la liste déroulante Type (à la place de la valeur par défaut **SQLAzure**).
9.  Exécutez `npm install mongoose` pour obtenir Mongoose, un pilote de nœud MongoDB.
10. Configurez un hook dans votre code pour obtenir votre URI de connexion MongoLab à partir d'une variable d'environnement, puis connectez-vous à :

        var mongoose = require('mongoose');  
        ...
        var connectionString = process.env.CUSTOMCONNSTR_MONGOLAB_URI
        ...
        mongoose.connect(connectionString);

Remarque : Azure ajoute le préfixe **CUSTOMCONNSTR\_** à la chaîne de connexion déclarée à l'origine, ce qui explique pourquoi le code indique **CUSTOMCONNSTR\_MONGOLAB\_URI.** au lieu de **MONGOLAB\_URI**.

Passons maintenant au didacticiel complet...

## <a name="provision"></a>Configuration de la base de données

[WACOM.INCLUDE [howto-provision-mongolab](../includes/howto-provision-mongolab.md)]

## <a name="create"></a>Création de l'application

Dans cette section, vous allez développer votre environnement de développement et définir le code pour une application Web de base de liste de tâches qui utilise Node.js, Express et MongoDB. [Express][Express] offre une infrastructure View Controller pour le nœud, tandis que [Mongoose][Mongoose] est un pilote permettant la communication avec MongoDB en nœud.

### Paramétrage

#### Génération de la structure et installation des modules

1.  Dans la ligne de commande, créez le répertoire **tasklist**, puis ouvrez-le. Il s'agit du répertoire de votre projet.
2.  Entrez la commande suivante pour installer Express.

        npm install express -g

    `-g` indique le mode global, utilisé pour mettre à disposition le module **express** sans préciser de chemin de répertoire. Si vous recevez le message **Error: EPERM, chmod '/usr/local/bin/express'**, utilisez **sudo** pour exécuter npm avec un niveau plus élevé de privilèges.

    Le résultat de cette commande doit ressembler à ceci :

        express@3.3.4 C:\Users\larryfr\AppData\Roaming\npm\node_modules\express
        ├── methods@0.0.1
        ├── fresh@0.1.0
        ├── cookie-signature@1.0.1
        ├── range-parser@0.0.4
        ├── buffer-crc32@0.2.1
        ├── cookie@0.1.0
        ├── debug@0.7.2
        ├── mkdirp@0.3.5
        ├── commander@1.2.0 (keypress@0.1.0)
        ├── send@0.1.3 (mime@1.2.9)
        └── connect@2.8.4 (uid2@0.0.2, pause@0.0.1, qs@0.6.5, bytes@0.2.0, formidable@1.0.14)

3.  Pour créer la structure qui sera utilisée pour cette application, utilisez la commande **express** :

    express

    Le résultat de cette commande doit ressembler à ceci :

        create : .
        create : ./package.json
        create : ./app.js
        create : ./public
        create : ./public/javascripts
        create : ./public/images
        create : ./public/stylesheets
        create : ./public/stylesheets/style.css
        create : ./routes
        create : ./routes/index.js
        create : ./views
        create : ./views/layout.jade
        create : ./views/index.jade

        dont forget to install dependencies:
        $ cd . && npm install

    Une fois la commande terminée, vous devez disposer de plusieurs nouveaux répertoires et fichiers dans le répertoire **tasklist**.

4.  Entrez la commande suivante pour installer les modules décrits dans le fichier **package.json** :

        npm install

    Le résultat de cette commande doit ressembler à ceci :

        express@3.3.4 node_modules\express
        ├── methods@0.0.1
        ├── fresh@0.1.0
        ├── range-parser@0.0.4
        ├── cookie-signature@1.0.1
        ├── buffer-crc32@0.2.1
        ├── cookie@0.1.0
        ├── debug@0.7.2
        ├── mkdirp@0.3.5
        ├── commander@1.2.0 (keypress@0.1.0)
        ├── send@0.1.3 (mime@1.2.9)
        └── connect@2.8.4 (uid2@0.0.2, pause@0.0.1, qs@0.6.5, bytes@0.2.0, formidable@1.0.14)

        jade@0.33.0 node_modules\jade
        ├── character-parser@1.0.2
        ├── mkdirp@0.3.5
        ├── commander@1.2.0 (keypress@0.1.0)
        ├── with@1.1.0 (uglify-js@2.3.6)
        ├── constantinople@1.0.1 (uglify-js@2.3.6)
        ├── transformers@2.0.1 (promise@2.0.0, css@1.0.8, uglify-js@2.2.5)
        └── monocle@0.1.48 (readdirp@0.2.5)

    Le fichier **package.json** est un des fichiers créés par la commande **express**. Il contient une liste de modules supplémentaires qui sont nécessaires pour les applications Express. Ensuite, lorsque vous déployez cette application vers un site web Azure, ce fichier permet de déterminer quels modules doivent être installés sur Azure pour prendre en charge votre application.

5.  Ensuite, entrez la commande suivante pour installer le module Mongoose en local et pour enregistrer une entrée lui correspondant dans le fichier **package.json** :

        npm install mongoose --save

    Le résultat de cette commande doit ressembler à ceci :

        mongoose@3.6.15 node_modules\mongoose
        ├── regexp-clone@0.0.1
        ├── sliced@0.0.3
        ├── muri@0.3.1
        ├── hooks@0.2.1
        ├── mpath@0.1.1
        ├── ms@0.1.0
        ├── mpromise@0.2.1 (sliced@0.0.4)
        └── mongodb@1.3.11 (bson@0.1.9, kerberos@0.0.3)

    Vous pouvez ignorer tout message concernant l'installation de l'analyseur C++ bson.

### Le code

Maintenant que votre environnement et votre structure sont prêts, nous pouvons étendre l'application de base créée par la commande **express** en ajoutant un fichier **task.js** qui contient le modèle de vos tâches. Vous allez également modifier le fichier **app.js** existant et créer un fichier de contrôleur **tasklist.js** pour utiliser le modèle.

#### Création du modèle

1.  Dans le répertoire **tasklist**, créez un répertoire appelé **models**.

2.  Dans le répertoire **models**, créez un fichier appelé **task.js**. Ce fichier contiendra le modèle des tâches créées par votre application.

3.  Ajoutez le code suivant au fichier **task.js** :

        var mongoose = require('mongoose')
          , Schema = mongoose.Schema;

        var TaskSchema = new Schema({
            itemName      : String
          , itemCategory  : String
          , itemCompleted : { type: Boolean, default: false }
          , itemDate      : { type: Date, default: Date.now }
        });

        module.exports = mongoose.model('TaskModel', TaskSchema);

4.  Enregistrez, puis fermez le fichier **task.js**.

#### Création du contrôleur

1.  Dans le répertoire **tasklist/routes**, créez un nouveau fichier **tasklist.js** et ouvrez-le dans un éditeur de texte.

2.  Ajoutez le code suivant dans **tasklist.js**. Ce code charge le module mongoose et le modèle de tâches défini dans **task.js**. La fonction TaskList est utilisée pour créer la connexion entre le serveur MongoDB basé sur la valeur **connection**. Elle fournit également les méthodes **showTasks**, **addTask**, and **completeTasks** :

        var mongoose = require('mongoose')
          , task = require('../models/task.js');

        module.exports = TaskList;

        function TaskList(connection) {
          mongoose.connect(connection);
        }

        TaskList.prototype = {
          showTasks: function(req, res) {
            task.find({itemCompleted: false}, function foundTasks(err, items) {
              res.render('index',{title: 'My ToDo List ', tasks: items})
            });
          },

          addTask: function(req,res) {
            var item = req.body.item;
            newTask = new task();
            newTask.itemName = item.name;
            newTask.itemCategory = item.category;
            newTask.save(function savedTask(err){
              if(err) {
                throw err;
              }
            });
            res.redirect('/');
          },


          completeTask: function(req,res) {
            var completedTasks = req.body;
            for(taskId in completedTasks) {
              if(completedTasks[taskId]=='true') {
                var conditions = { _id: taskId };
                var updates = { itemCompleted: completedTasks[taskId] };
                task.update(conditions, updates, function updatedTask(err) {
                  if(err) {
                    throw err;
                  }
                });
              }
            }
            res.redirect('/');
          }
        }

3.  Enregistrez le fichier **tasklist.js**.

#### Modification de la vue d'index

1.  Passez dans le répertoire **views** et ouvrez le fichier **index.jade** dans un éditeur de texte.

2.  Remplacez le contenu du fichier **index.jade** par le code qui suit. Ce code définit la vue pour l'affichage des tâches existantes, ainsi qu'un formulaire pour l'ajout de nouvelles tâches et pour marquer les tâches existantes comme terminées.

        h1 #{title}
        form(action="/completetask", method="post")
          table(border="1")
            tr
              td Name
              td Category
              td Date
              td Complete
            each task in tasks
              tr
                td #{task.itemName}
                td #{task.itemCategory}
                - var day   = task.itemDate.getDate();
                - var month = task.itemDate.getMonth() + 1;
                - var year  = task.itemDate.getFullYear();
                td #{month + "/" + day + "/" + year}
                td
                  input(type="checkbox", name="#{task._id}", value="#{!task.itemCompleted}", checked=task.itemCompleted)
          input(type="submit", value="Update tasks")
        hr
        form(action="/addtask", method="post")
          table(border="1") 
            tr
              td Item Name: 
              td 
                input(name="item[name]", type="textbox")
            tr
              td Item Category: 
              td 
                input(name="item[category]", type="textbox")
          input(type="submit", value="Add item")

3.  Fermez et enregistrez le fichier **index.jade**.

#### Remplacement de app.js

1.  Dans le répertoire **tasklist**, ouvrez le fichier **app.js** dans un éditeur de texte. Ce fichier a été créé ultérieurement à l'aide de la commande **express**.
2.  Ajoutez le code qui suit au début du fichier **app.js**. Ceci permet d'initialiser **TaskList** avec la chaîne de connexion pour le serveur MongoDB :

        var TaskList = require('./routes/tasklist');
        var taskList = new TaskList(process.env.CUSTOMCONNSTR_MONGOLAB_URI);

    Notez la deuxième ligne. Vous accédez à une variable d'environnement que vous configurerez ultérieurement qui contient les informations de connexion de votre instance Mongo. Si vous disposez d'une instance Mongo locale pour le développement, vous pouvez définir cette valeur de façon temporaire sur « localhost » au lieu de `process.env.CUSTOMCONNSTR_MONGOLAB_URI`.

3.  Recherchez les lignes qui commencent par `app.get` et remplacez-les par les lignes suivantes :

        app.get('/', taskList.showTasks.bind(taskList));
        app.post('/addtask', taskList.addTask.bind(taskList));
        app.post('/completetask', taskList.completeTask.bind(taskList));

    Ce code ajoute les fonctions définies dans **tasklist.js** en tant que routes.

4.  Enregistrez le fichier **app.js**.

## <a name="deploy"></a>Déploiement de l’application

Maintenant que l'application a été développée, il est temps de créer un site Web Azure pour l'héberger. Configurez le site Web et déployez le code. L'élément essentiel de cette section est l'utilisation de la chaîne de connexion MongoDB (URI). Vous allez configurer une variable d'environnement dans votre site Web avec cet URI, afin de conserver l'URI séparé du code. L'URI doit être traitée comme une information sensible car elle contient les identifiants de connexion à votre base de données.

Les étapes de cette section utilisent les outils en ligne de commande Azure pour créer un site web Azure, puis Git pour déployer votre application. Pour effectuer ces étapes, vous devez disposer d'un abonnement Azure.

### Installation de l'outil en ligne de commande Azure pour Mac et Linux

Pour installer les outils en ligne de commande, utilisez la commande suivante :

    npm install azure-cli -g

Si vous avez déjà installé le fichier **Azure SDK for Node.js** depuis le [Centre de développement Azure][Centre de développement Azure], les outils en ligne de commande devraient déjà être installés. Pour plus d'informations, consultez la page [Outil en ligne de commande Azure pour Mac et Linux][Outil en ligne de commande Azure pour Mac et Linux].

Alors que les outils en ligne de commande Azure ont été créés en priorité pour les utilisateurs Mac et Linux, ils sont basés sur Node.js et peuvent fonctionner sur tout système capable d'exécuter Node.

### Importation de paramètres de publication

Avant d'utiliser les outils en ligne de commande avec Azure, vous devez télécharger un fichier comportant des informations relatives à votre abonnement. Procédez comme suit pour télécharger et importer ce fichier.

1.  À partir de la ligne de commande, saisissez la commande suivante pour lancer le navigateur et accéder à la page de téléchargement. Si vous y êtes invité, connectez-vous avec le compte associé à votre abonnement.

        azure account download

    ![Page de téléchargement][Page de téléchargement]

    Le téléchargement du fichier doit se lancer automatiquement. Si ce n'est pas le cas, cliquez sur le lien situé en haut de la page pour télécharger le fichier manuellement.

2.  Une fois le téléchargement terminé, utilisez la commande suivante pour importer les paramètres :

        azure account import <path-to-file>

    Indiquez le chemin et le nom du fichier des paramètres de publication téléchargé à l'étape précédente. Une fois la commande terminée, un texte similaire à celui présenté ci-dessous doit s'afficher :

        info:   Executing command account import
        info:   Found subscription: subscriptionname
        info:   Setting default subscription to: subscriptionname
        warn:   The '/Users/user1/.azure/publishSettings.xml' file contains sensitive information.
        warn:   Remember to delete it now that it has been imported.
        info:   Account publish settings imported successfully
        info:   account import command OK

3.  Une fois l'importation terminée, il est conseillé de supprimer le fichier de paramètres de publication car il n'est plus nécessaire et il contient des informations sensibles relatives à votre abonnement Azure.

### Création d'un nouveau site Web et mise à disposition du code

Il est très facile de créer un site Web dans Azure. S'il s'agit de votre premier site Web Azure, vous devez utiliser le portail. Si vous en avez déjà créé un ou plusieurs, passez à l'étape 7.

1.  Dans le portail Azure, cliquez sur **New**.<br />
    ![New][New]
2.  Sélectionnez **Compute \> Web Site \> Quick Create**.<br />
    ![CreateSite][CreateSite]
3.  Saisissez un préfixe d'URL. Sélectionnez le nom de votre choix, tout en gardant à l'esprit qu'il doit être unique (« monappmongo » risque fort de ne pas être disponible).
4.  Cliquez sur **Create Website**.
5.  Une fois le site Web créé, cliquez sur le nom du site Web dans la liste des sites Web. Le tableau de bord du site Web s'affiche.<br />
    ![WebSiteDashboard][WebSiteDashboard]
6.  Cliquez **Configurer la publication Git** sous **quick glance**, puis entrez votre nom d'utilisateur et votre mot de passe git. Vous utiliserez ce mot de passe pour le transfert vers votre site Web (étape 9).
7.  Si vous avez créé votre site Web en suivant les étapes ci-dessus, la commande suivante clôt le processus. Toutefois, si vous disposez déjà de plus d'un site Web Azure, vous pouvez ignorer les étapes ci-dessus et créer un nouveau site Web à l'aide de cette même commande. Dans votre répertoire de projet **tasklist** :

        azure site create myuniquesitename --git  

    Remplacez « nom uniquedusite » par le nom unique donné à votre site Web. Si le site Web est créé à partir de cette commande, il vous est demandé d'indiquer le centre de données sur lequel sera conservé le site. Sélectionnez le centre de données le plus proche de l'emplacement de votre base de données MongoLab.

    Le paramètre `--git` crée :
    Un référentiel git local dans le dossier **tasklist**, si aucun n'existe déjà ;
    un [Git remote][Git remote] nommé « azure », qui sera utilisé pour publier l'application sur Azure ;
    un fichier[iisnode.yml][iisnode.yml], qui contient les paramètres utilisés par Azure pour gérer des applications nœud ;
    un fichier gitignore pour empêcher la publication du dossier modules-nœuds sur .git.

    Une fois cette commande terminée, le résultat doit ressembler à ce qui suit. Notez que la ligne qui commence par **Created website at** contient l'URL du site Web.

        info:   Executing command site create
        info:   Using location southcentraluswebspace
        info:   Executing `git init`
        info:   Creating default web.config file
        info:   Creating a new web site
        info:   Created web site at  mongodbtasklist.azurewebsites.net
        info:   Initializing repository
        info:   Repository initialized
        info:   Executing `git remote add azure http://gitusername@myuniquesitename.azurewebsites.net/mongodbtasklist.git`
        info:   site create command OK

8.  Utilisez la commande suivante pour ajouter, puis valider vos fichiers dans votre référentiel Git local :

        git add .
        git commit -m "adding files"

9.  Transfert du code

        git push azure master  

    Lorsque vous envoyez les modifications récentes du référentiel Git sur le site web Azure, vous devez spécifier que la branche cible est **master**, car elle est utilisée pour le contenu du site web. Si un mot de passe vous est demandé, saisissez le mot de passe créé lors de la configuration de la publication git pour votre site Web ci-dessus.

    Vous devez voir des informations similaires à ce qui suit. À mesure que le déploiement s'effectue, Azure télécharge tous les modules npm.

        Counting objects: 17, done.
        Delta compression using up to 8 threads.
        Compressing objects: 100% (13/13), done.
        Writing objects: 100% (17/17), 3.21 KiB, done.
        Total 17 (delta 0), reused 0 (delta 0)
        remote: New deployment received.
        remote: Updating branch 'master'.
        remote: Preparing deployment for commit id 'ef276f3042'.
        remote: Preparing files for deployment.
        remote: Running NPM.
        ...
        remote: Deploying Web.config to enable Node.js activation.
        remote: Deployment successful.
        To https://username@mongodbtasklist.azurewebsites.net/MongoDBTasklist.git
         * [new branch]      master -> master

Vous avez presque terminé !

### Configuration de votre environnement

Vous vous rappelez de la variable process.env.CUSTOMCONNSTR\_MONGOLAB\_URI dans le code ? Nous allons remplir cette variable d'environnement avec la valeur fournie à Azure au cours de la configuration de la base de données MongoLab.

#### Accédez à la chaîne de connexion MongoLab

[WACOM.INCLUDE [howto-get-connectioninfo-mongolab](../includes/howto-get-connectioninfo-mongolab.md)]

#### Ajoutez la chaîne de connexion aux variables d'environnement du site Web

[WACOM.INCLUDE [howto-save-connectioninfo-mongolab](../includes/howto-save-connectioninfo-mongolab.md)]

## Vous avez réussi !

Exécutez `azure site browse` depuis le répertoire de votre projet pour ouvrir automatiquement un navigateur (ou ouvrez un navigateur et accédez manuellement à l'URL de votre site Web, monsiteunique.azurewebsites.net) :

![Une page Web affiche une liste de tâches vide.][Une page Web affiche une liste de tâches vide.]

## <a name="manage"></a>Gestion de la base de données

[WACOM.INCLUDE [howto-access-mongolab-ui](../includes/howto-access-mongolab-ui.md)]

Félicitations ! Vous venez de lancer une application Node.js utilisant une base de données MongoDB hébergée sur MongoLab ! Maintenant que vous disposez d'une base de données MongoLab, vous pouvez contacter [\<a href="mailto:support@mongolab.com"\>support@mongolab.com\</a\>][\<a href="mailto:support@mongolab.com"\>support@mongolab.com\</a\>] pour toute question ou problème relatif à votre base de données ainsi que pour obtenir de l'aide concernant MongoDB ou le pilote de nœud. Bonne continuation !

  [Configuration de la base de données]: #provision
  [MongoLab]: http://mongolab.com
  [Création de l'application]: #create
  [Déploiement de l'application]: #deploy
  [Gestion de base de données]: #manage
  [Node.js]: http://nodejs.org
  [Git]: http://git-scm.com
  [Store]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/button-store.png
  [1]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/entry-mongolab.png
  [ConnectionInfoButton]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/button-connectioninfo.png
  [ConnectionInfoScreen]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/dialog-mongolab_connectioninfo.png
  [WebSiteConnectionStrings]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/focus-mongolab-websiteconnectionstring.png
  [Express]: http://expressjs.com
  [Mongoose]: http://mongoosejs.com
  [Centre de développement Azure]: /fr-fr/develop/nodejs/
  [Outil en ligne de commande Azure pour Mac et Linux]: /fr-fr/develop/nodejs/how-to-guides/command-line-tools/
  [Page de téléchargement]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/azure-account-download-cli.png
  [New]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/button-new.png
  [CreateSite]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/screen-mongolab-newwebsite.png
  [WebSiteDashboard]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/screen-mongolab-websitedashboard.png
  [Git remote]: http://git-scm.com/docs/git-remote
  [iisnode.yml]: https://github.com/WindowsAzure/iisnode/blob/master/src/samples/configuration/iisnode.yml
  [Une page Web affiche une liste de tâches vide.]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/todo_list_noframe.png
