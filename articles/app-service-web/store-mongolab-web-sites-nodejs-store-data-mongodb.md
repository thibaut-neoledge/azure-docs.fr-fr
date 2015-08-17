<properties
	pageTitle="Création d'une application web Node.js sur Azure avec MongoDB à l'aide du module complémentaire MongoLab"
	description="Apprenez à créer une application web Node.js dans Azure App Service qui se connecte à une instance MongoDB hébergée sur MongoLab."
	tags="azure-classic-portal"
	services="app-service\web, virtual-machines"
	documentationCenter="nodejs"
	authors="chrisckchang"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="nodejs"
	ms.topic="article"
	ms.date="04/20/2014"
	ms.author="mwasson"/>






# Création d'une application web Node.js sur Azure avec MongoDB à l'aide du module complémentaire MongoLab


<p><em>Par Eric Sedor, MongoLab</em></p>

Bonjour, chers aventuriers ! Bienvenue dans MongoDB-as-a-Service. Ce didacticiel vous apprendra à effectuer les opérations suivantes :

1. [Configuration de la base de données][provision] : le module [MongoLab](http://mongolab.com) d'Azure Marketplace fournit une base de données MongoDB hébergée dans le cloud Azure et gérée par la plateforme de base de données cloud de MongoLab.
2. [Création de l'application][create] : il s'agit d'une simple application Node.js pour conserver une liste de tâches.
3. [Déploiement de l'application][deploy] : en combinant quelques astuces de configuration, nous apporterons un nouveau souffle à notre code [Azure App Service Web Apps](http://go.microsoft.com/fwlink/?LinkId=529714).
4. [Gestion de base de données][manage] : finalement, vous découvrirez le portail de gestion de base de données basé sur le Web où vous pouvez effectuer des recherches, visualiser et modifier les données facilement.

Au cours de ce didacticiel, n'hésitez pas à envoyer un e-mail à tout moment à l'adresse [support@mongolab.com](mailto:support@mongolab.com) si vous avez des questions.

Avant de poursuivre, vérifiez que les éléments suivants sont installés :

* [Node.js] version 0.10.29+

* [Git]

[AZURE.INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]

## Démarrage rapide
Si vous connaissez déjà l'Azure Store, cette section vous permettra de démarrer rapidement. Dans le cas contraire, consultez la section [Configuration de la base de données][provision] ci-dessous.

1. Ouvrez Azure Marketplace en cliquant sur **Nouveau** > **Marketplace**.  
<!-- ![Store][button-store] -->
2. Cliquez sur le module **MongoLab**.  
![MongoLab][entry-mongolab]
3. Cliquez sur le module **MongoLab** dans la liste des modules complémentaires, puis cliquez sur **Connection Info**.  
![ConnectionInfoButton][button-connectioninfo]  
4. Copiez le fichier **MONGOLAB_URI** dans votre Presse-papiers.  
![ÉcranInformationsDeConnexion][screen-connectioninfo]

	>[AZURE.NOTE]Cet URI contient votre nom d’utilisateur et votre mot de passe pour la base de données. Considérez ces informations comme étant sensibles, ne les partagez pas.

5. Ajoutez la valeur à la liste **Connection Strings** dans le menu **Configuration** de votre application web dans Azure App Service :  
![WebAppConnectionStrings][focus-website-connectinfo]
6. Dans **Name**, entrez **MONGOLAB_URI**.
7. Dans **Value**, collez la chaîne de connexion obtenue dans la section précédente.
8. Sélectionnez **Custom** dans la liste déroulante Type (à la place de la valeur par défaut **SQLAzure**).
9. Exécutez `npm install mongoose` pour obtenir Mongoose, un pilote de nœud MongoDB.
10. Configurez un hook dans votre code pour obtenir votre URI de connexion MongoLab à partir d'une variable d'environnement, puis connectez-vous à :

        var mongoose = require('mongoose');  
 		...
 		var connectionString = process.env.CUSTOMCONNSTR_MONGOLAB_URI
 		...
 		mongoose.connect(connectionString);

Remarque : Azure ajoute le préfixe **CUSTOMCONNSTR_** à la chaîne de connexion déclarée à l'origine, ce qui explique pourquoi le code indique **CUSTOMCONNSTR_MONGOLAB_URI.** au lieu de **MONGOLAB_URI**.

Passons maintenant au didacticiel complet...

<a name="provision"></a>
## Configuration de la base de données

[AZURE.INCLUDE [howto-provision-mongolab](../../includes/howto-provision-mongolab.md)]

<a name="create"></a>
## Création de l'application

Dans cette section, vous allez développer votre environnement de développement et définir le code pour une application Web de base de liste de tâches qui utilise Node.js, Express et MongoDB. [Express] offre une infrastructure View Controller pour le nœud, tandis que [Mongoose] est un pilote permettant la communication avec MongoDB en nœud.

### Paramétrage

#### Génération de la structure et installation des modules

1. Dans la ligne de commande, créez le répertoire **tasklist**, puis ouvrez-le. Il s'agit du répertoire de votre projet.
2. Entrez la commande suivante pour installer Express.

		npm install express -g

	`-g` indique le mode global, utilisé pour mettre à disposition le module <strong>express</strong> sans préciser de chemin de répertoire. Si vous recevez le message <strong>Erreur : EPERM, chmod '/usr/local/bin/express'</strong>, utilisez <strong>sudo</strong> pour exécuter npm avec un niveau plus élevé de privilèges.

    Le résultat de cette commande doit ressembler à ceci :

		express@4.9.1 C:\Users\mongolab\AppData\Roaming\npm\node_modules\express
		├── merge-descriptors@0.0.2
		├── utils-merge@1.0.0
		├── fresh@0.2.4
		├── cookie@0.1.2
		├── range-parser@1.0.2
		├── escape-html@1.0.1
		├── cookie-signature@1.0.5
		├── finalhandler@0.2.0
		├── vary@1.0.0
		├── media-typer@0.3.0
		├── parseurl@1.3.0
		├── serve-static@1.6.2
		├── methods@1.1.0
		├── path-to-regexp@0.1.3
		├── depd@0.4.5
		├── qs@2.2.3
		├── on-finished@2.1.0 (ee-first@1.0.5)
		├── debug@2.0.0 (ms@0.6.2)
		├── proxy-addr@1.0.1 (ipaddr.js@0.1.2)
		├── etag@1.3.1 (crc@3.0.0)
		├── send@0.9.2 (destroy@1.0.3, ms@0.6.2, mime@1.2.11)
		├── accepts@1.1.0 (negotiator@0.4.7, mime-types@2.0.1)
		└── type-is@1.5.1 (mime-types@2.0.1)

3. Pour créer la structure qui sera utilisée pour cette application, utilisez la commande **express** :

    	express

    Notez que ce didacticiel utilise Express v4.x.x. Si le Générateur d'application Express 3 est déjà installé sur votre système, vous devez tout d'abord le désinstaller :

    	npm uninstall -g express

    À présent, installez le nouveau générateur pour la version 4.x.x :

    	npm install -g express-generator

	Une fois que la commande **express** s'est exécutée, la sortie doit ressembler à ce qui suit :

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
		create : ./routes/users.js
		create : ./views
		create : ./views/index.jade
		create : ./views/layout.jade
		create : ./views/error.jade
		create : ./bin
		create : ./bin/www

		install dependencies:
		$ cd . && npm install


	Une fois la commande terminée, vous devez disposer de plusieurs nouveaux répertoires et fichiers dans le répertoire **tasklist**.

4. Entrez la commande suivante pour installer les modules décrits dans le fichier **package.json** :

        npm install

    Le résultat de cette commande doit ressembler à ceci :

		cookie-parser@1.3.3 node_modules/cookie-parser
		├── cookie@0.1.2
		└── cookie-signature@1.0.5

		debug@2.0.0 node_modules/debug
		└── ms@0.6.2

		serve-favicon@2.1.4 node_modules/serve-favicon
		├── ms@0.6.2
		├── fresh@0.2.4
		└── etag@1.3.1 (crc@3.0.0)

		morgan@1.3.1 node_modules/morgan
		├── basic-auth@1.0.0
		├── depd@0.4.5
		└── on-finished@2.1.0 (ee-first@1.0.5)

		express@4.9.1 node_modules/express
		├── utils-merge@1.0.0
		├── merge-descriptors@0.0.2
		├── cookie@0.1.2
		├── fresh@0.2.4
		├── escape-html@1.0.1
		├── range-parser@1.0.2
		├── cookie-signature@1.0.5
		├── finalhandler@0.2.0
		├── vary@1.0.0
		├── media-typer@0.3.0
		├── serve-static@1.6.2
		├── parseurl@1.3.0
		├── methods@1.1.0
		├── path-to-regexp@0.1.3
		├── depd@0.4.5
		├── qs@2.2.3
		├── etag@1.3.1 (crc@3.0.0)
		├── on-finished@2.1.0 (ee-first@1.0.5)
		├── proxy-addr@1.0.1 (ipaddr.js@0.1.2)
		├── send@0.9.2 (destroy@1.0.3, ms@0.6.2, mime@1.2.11)
		├── type-is@1.5.1 (mime-types@2.0.1)
		└── accepts@1.1.0 (negotiator@0.4.7, mime-types@2.0.1)

		body-parser@1.8.2 node_modules/body-parser
		├── media-typer@0.3.0
		├── raw-body@1.3.0
		├── bytes@1.0.0
		├── depd@0.4.5
		├── on-finished@2.1.0 (ee-first@1.0.5)
		├── qs@2.2.3
		├── iconv-lite@0.4.4
		└── type-is@1.5.1 (mime-types@2.0.1)

		jade@1.6.0 node_modules/jade
		├── character-parser@1.2.0
		├── commander@2.1.0
		├── void-elements@1.0.0
		├── mkdirp@0.5.0 (minimist@0.0.8)
		├── monocle@1.1.51 (readdirp@0.2.5)
		├── transformers@2.1.0 (promise@2.0.0, css@1.0.8, uglify-js@2.2.5)
		├── constantinople@2.0.1 (uglify-js@2.4.15)
		└── with@3.0.1 (uglify-js@2.4.15)

	Le fichier **package.json** est un des fichiers créés par la commande **express**. Il contient une liste de modules supplémentaires qui sont nécessaires pour les applications Express. Ensuite, quand vous déployez cette application sur Azure App Service Web Apps, ce fichier est utilisé pour déterminer quels modules doivent être installés sur Azure pour prendre en charge votre application.

5. Ensuite, entrez la commande suivante pour installer le module Mongoose en local et pour enregistrer une entrée lui correspondant dans le fichier **package.json** :

		npm install mongoose --save

	Le résultat de cette commande doit ressembler à ceci :

		mongoose@3.8.16 node_modules/mongoose
		├── regexp-clone@0.0.1
		├── muri@0.3.1
		├── sliced@0.0.5
		├── hooks@0.2.1
		├── mpath@0.1.1
		├── mpromise@0.4.3
		├── ms@0.1.0
		├── mquery@0.8.0 (debug@0.7.4)
		└── mongodb@1.4.9 (readable-stream@1.0.31, kerberos@0.0.3, bson@0.2.12)

### Le code

Maintenant que votre environnement et votre structure sont prêts, nous pouvons étendre l'application de base créée par la commande **express** en ajoutant un fichier **task.js** qui contient le modèle de vos tâches. Vous allez également modifier le fichier **app.js** existant et créer un fichier de contrôleur **tasklist.js** pour utiliser le modèle.

#### Création du modèle

1. Dans le répertoire **tasklist**, créez un répertoire appelé **models**.

2. Dans le répertoire **models**, créez un fichier appelé **task.js**. Ce fichier contiendra le modèle des tâches créées par votre application.

3. Ajoutez le code suivant au fichier **task.js** :

        var mongoose = require('mongoose'),
          Schema = mongoose.Schema;

        var TaskSchema = new Schema({
	      itemName      : String,
	      itemCategory  : String,
	      itemCompleted : { type: Boolean, default: false },
	      itemDate      : { type: Date, default: Date.now }
        });

        module.exports = mongoose.model('TaskModel', TaskSchema);

5. Enregistrez, puis fermez le fichier **task.js**.

#### Création du contrôleur

1. Dans le répertoire **tasklist/routes**, créez un nouveau fichier **tasklist.js** et ouvrez-le dans un éditeur de texte.

2. Ajoutez le code suivant dans **tasklist.js**. Ce code charge le module mongoose et le modèle de tâches défini dans **task.js**. La fonction TaskList est utilisée pour créer la connexion entre le serveur MongoDB basé sur la valeur **connection**. Elle fournit également les méthodes **showTasks**, **addTask**, and **completeTasks** :

		var mongoose = require('mongoose'),
 		  task = require('../models/task.js');

		module.exports = TaskList;

		function TaskList(connection) {
		  mongoose.connect(connection);
		}

		TaskList.prototype = {
		  showTasks: function(req, res) {
		    task.find({ itemCompleted : false }, function foundTasks(err, items) {
		    res.render('index', { title: 'My ToDo List', tasks: items })
		    });
		  },

		  addTask: function(req,res) {
		    var item = req.body;
		    var newTask = new task();
		    newTask.itemName = item.itemName;
		    newTask.itemCategory = item.itemCategory;
		    newTask.save(function savedTask(err) {
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

3. Enregistrez le fichier **tasklist.js**.

#### Modification de la vue d'index

1. Passez dans le répertoire **views** et ouvrez le fichier **index.jade** dans un éditeur de texte.

2. Remplacez le contenu du fichier **index.jade** par le code qui suit. Ce code définit la vue pour l'affichage des tâches existantes, ainsi qu'un formulaire pour l'ajout de nouvelles tâches et pour marquer les tâches existantes comme terminées.

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
		        input(name="itemName", type="textbox")
		    tr
		      td Item Category:
		      td
		        input(name="itemCategory", type="textbox")
		  input(type="submit", value="Add item")

3. Fermez et enregistrez le fichier **index.jade**.

#### Remplacement de app.js

1. Dans le répertoire **tasklist**, ouvrez le fichier **app.js** dans un éditeur de texte. Ce fichier a été créé ultérieurement à l'aide de la commande **express**.
2. Ajoutez le code qui suit au début du fichier **app.js**. Ceci permet d'initialiser **TaskList** avec la chaîne de connexion pour le serveur MongoDB :

		var TaskList = require('./routes/tasklist');
		var taskList = new TaskList(process.env.CUSTOMCONNSTR_MONGOLAB_URI);

 	Notez la deuxième ligne. Vous accédez à une variable d'environnement que vous configurerez ultérieurement qui contient les informations de connexion de votre instance Mongo. Si vous disposez d'une instance mongo locale pour le développement, vous pouvez définir cette valeur de façon temporaire sur « localhost » au lieu de `process.env.CUSTOMCONNSTR_MONGOLAB_URI`.

3. Recherchez ces lignes :

		app.use('/', routes);
		app.use('/users', users);

	Et remplacez-les par :

		app.get('/', taskList.showTasks.bind(taskList));
		app.post('/addtask', taskList.addTask.bind(taskList));
		app.post('/completetask', taskList.completeTask.bind(taskList));

	Ce code ajoute les fonctions définies dans **tasklist.js** en tant que routes.

4. Pour initialiser votre application, faites défiler votre fichier **app.js** jusqu'en bas, puis ajoutez la ligne suivante :

		app.listen(3000); // Listen on port 3000
		module.exports = app;

5. Enregistrez le fichier **app.js**.

<a name="deploy"></a>
## Déploiement de l’application

Maintenant que l'application a été développée, il est temps de créer une application web dans Azure App Service pour l'héberger. Configurez cette application web et déployez le code. L'élément essentiel de cette section est l'utilisation de la chaîne de connexion MongoDB (URI). Vous allez configurer une variable d'environnement dans votre application web avec cet URI, afin de séparer l'URI du code. L’URI doit être traitée comme une information sensible car elle contient les identifiants de connexion à votre base de données.

Dans les étapes de cette section, l’interface de ligne de commande Azure pour Mac, Linux et Windows est utilisée afin de créer une application web dans Azure App Service. Le déploiement de l’application s’effectue ensuite à l’aide de Git. Pour effectuer ces étapes, vous devez disposer d’un abonnement Azure.

### Installer l’interface de ligne de commande Microsoft Azure

Pour installer l’interface de ligne de commande Azure, utilisez la commande suivante :

	npm install azure-cli -g

Si vous avez déjà installé le <strong>Kit de développement logiciel (SDK) Azure pour Node.js</strong> à partir du <a href="/develop/nodejs/">Centre de développement Azure</a>, l’interface de ligne de commande Azure doit déjà être installée. Pour plus d’informations, consultez la rubrique <a href="../virtual-machines-command-line-tools.md">Interface de ligne de commande Azure</a>.

Même si l’interface de ligne de commande Azure a été créée en priorité pour les utilisateurs Mac et Linux, elle est basée sur Node.js et peut donc fonctionner sur tout système capable d’exécuter Node.

### Importation de paramètres de publication

Avant d’utiliser l’interface de ligne de commande Azure, vous devez télécharger un fichier comportant des informations relatives à votre abonnement. Procédez comme suit pour télécharger et importer ce fichier.

1. À partir de la ligne de commande, saisissez la commande suivante pour lancer le navigateur et accéder à la page de téléchargement. Si vous y êtes invité, connectez-vous avec le compte associé à votre abonnement.

		azure account download

	![Page de téléchargement][download-publishing-settings]

	Le téléchargement du fichier doit se lancer automatiquement. Si ce n'est pas le cas, cliquez sur le lien situé en haut de la page pour télécharger le fichier manuellement.

2. Une fois le téléchargement terminé, utilisez la commande suivante pour importer les paramètres :

		azure account import <path-to-file>

	Indiquez le chemin et le nom du fichier des paramètres de publication téléchargé à l'étape précédente. Une fois la commande terminée, un texte similaire à celui présenté ci-dessous doit s'afficher :

		info:   Executing command account import
		info:   Found subscription: subscriptionname
		info:   Setting default subscription to: subscriptionname
		warn:   The '/Users/mongolab/.azure/publishSettings.xml' file contains sensitive information.
		warn:   Remember to delete it now that it has been imported.
		info:   Account publish settings imported successfully
		info:   account import command OK


3. Une fois l'importation terminée, il est conseillé de supprimer le fichier de paramètres de publication car il n'est plus nécessaire et il contient des informations sensibles relatives à votre abonnement Azure.

### Création d'une application web et mise à disposition du code

Il est très facile de créer une application web dans Azure App Service. S'il s'agit de votre première application web Azure, vous devez utiliser le portail. Si vous en avez déjà créé un ou plusieurs, passez à l'étape 7.

1. Dans le portail Azure, cliquez sur **Nouveau**.  
![Nouveau][button-new]
2. Sélectionnez **Calculer > Application web > Création rapide**.
<!-- ![Create Web App][screen-mongolab-newwebsite] -->
3. Saisissez un préfixe d’URL. Sélectionnez le nom de votre choix, tout en gardant à l'esprit qu'il doit être unique (« monappmongo » risque fort de ne pas être disponible).
4. Cliquez sur **Créer une application web**.
5. Quand la création de l’application web est terminée, cliquez sur son nom dans la liste des applications web. Le tableau de bord de l’application web s’affiche.
<!-- ![Web App Dashboard][screen-mongolab-websitedashboard] -->
6. Cliquez sur **Configurer le déploiement à partir du contrôle de code source** sous **aperçu rapide**, sélectionnez GitHub, puis entrez votre nom d’utilisateur et votre mot de passe git. Vous utiliserez ce mot de passe pour le transfert vers votre application web (étape 9).  
7. Si vous avez créé votre application web en suivant les étapes ci-dessus, la commande suivante clôt le processus. Toutefois, si vous disposez déjà de plusieurs applications web, vous pouvez ignorer les étapes ci-dessus et créer une application web à l'aide de cette même commande. Dans votre répertoire de projet **tasklist** :

		azure site create myuniquewebappname --git  
	Remplacez « myuniquewebappname » par le nom unique de votre application web. Si l'application web est créée dans le cadre de cette commande, vous êtes invité à indiquer le centre de données dans lequel se trouvera l'application web. Sélectionnez le centre de données géographiquement proche de votre base de données MongoLab.

	Le paramètre `--git` crée : * un référentiel git local dans le dossier **tasklist**, si aucun n'existe déjà ; * un [Git remote] nommé « azure », qui sera utilisé pour publier l'application sur Azure ; * un fichier [iisnode.yml], qui contient les paramètres utilisés par Azure pour héberger des applications nœud ; * un fichier gitignore pour empêcher la publication du dossier node-modules sur .git.

	Une fois cette commande terminée, le résultat doit ressembler à ce qui suit. Notez que la ligne commençant par **Created site at** contient l'URL de l'application web.

		info:   Executing command site create
		info:   Using location southcentraluswebspace
		info:   Executing `git init`
		info:   Creating default web.config file
		info:   Creating a new web site
		info:   Created web site at  mongodbtasklist.azurewebsites.net
		info:   Initializing repository
		info:   Repository initialized
		info:   Executing `git remote add azure http://gitusername@myuniquewebappname.azurewebsites.net/mongodbtasklist.git`
		info:   site create command OK

8. Utilisez la commande suivante pour ajouter, puis valider vos fichiers dans votre référentiel Git local :

		git add .
		git commit -m "adding files"

9. Transfert du code

		git push azure master  

	Quand vous copiez les dernières modifications du référentiel Git vers l'application web Azure App Service, vous devez spécifier que la branche cible est **master**, car elle est utilisée pour le contenu de l'application web. Si un mot de passe vous est demandé, saisissez celui que vous avez créé lors de la configuration de la publication git pour votre application web ci-dessus.

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
Vous vous rappelez de la variable process.env.CUSTOMCONNSTR_MONGOLAB_URI dans le code ? Nous allons remplir cette variable d'environnement avec la valeur fournie à Azure au cours de la configuration de la base de données MongoLab.

#### Accédez à la chaîne de connexion MongoLab

[AZURE.INCLUDE [howto-get-connectioninfo-mongolab](../../includes/howto-get-connectioninfo-mongolab.md)]

#### Ajoutez la chaîne de connexion aux variables d'environnement de l'application web

[AZURE.INCLUDE [howto-save-connectioninfo-mongolab](../../includes/howto-save-connectioninfo-mongolab.md)]

## Vous avez réussi !

Exécutez `azure site browse` depuis le répertoire de votre projet pour ouvrir automatiquement un navigateur (ou ouvrez un navigateur et accédez manuellement à l'URL de votre application web, myuniquewebappname.azurewebsites.net) :

![Une page Web affiche une liste de tâches vide.][node-mongo-finished]

<a name="manage"></a>
## Gestion de la base de données

[AZURE.INCLUDE [howto-access-mongolab-ui](../../includes/howto-access-mongolab-ui.md)]

Félicitations ! Vous venez de lancer une application Node.js utilisant une base de données MongoDB hébergée sur MongoLab ! Maintenant que vous disposez d'une base de données MongoLab, vous pouvez contacter [support@mongolab.com](mailto:support@mongolab.com) pour toute question ou problème relatif à votre base de données ainsi que pour obtenir de l'aide concernant MongoDB ou le pilote de nœud. Bonne continuation !

## Changements apportés
* Pour obtenir un guide présentant les modifications apportées dans le cadre de la transition entre Sites Web et App Service, consultez la page [Azure App Service et les services Azure existants](http://go.microsoft.com/fwlink/?LinkId=529714).
* Pour obtenir un guide présentant les modifications apportées dans le cadre de la transition entre l’ancien et le nouveau portail, consultez la page [Références sur la navigation dans le portail Azure](http://go.microsoft.com/fwlink/?LinkId=529715).

>[AZURE.NOTE]Si vous voulez vous familiariser avec Azure App Service avant d’ouvrir un compte Azure, accédez à la page [Essayer App Service](http://go.microsoft.com/fwlink/?LinkId=523751). Vous pourrez créer immédiatement et gratuitement une application de départ temporaire dans App Service. Aucune carte de crédit n’est requise ; vous ne prenez aucun engagement.


[screen-mongolab-websitedashboard]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/screen-mongolab-websitedashboard.png
[screen-mongolab-newwebsite]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/screen-mongolab-newwebsite.png
[button-new]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/button-new.png
[button-store]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/button-store.png
[entry-mongolab]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/entry-mongolab.png
[button-connectioninfo]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/button-connectioninfo.png
[screen-connectioninfo]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/dialog-mongolab_connectioninfo.png
[focus-website-connectinfo]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/focus-mongolab-websiteconnectionstring.png
[provision]: #provision
[create]: #create
[deploy]: #deploy
[manage]: #manage
[Node.js]: http://nodejs.org
[MongoDB]: http://www.mongodb.org
[Git]: http://git-scm.com
[Express]: http://expressjs.com
[Mongoose]: http://mongoosejs.com
[for free]: /pricing/free-trial
[Git remote]: http://git-scm.com/docs/git-remote
[azure-sdk-for-node]: https://github.com/WindowsAzure/azure-sdk-for-node
[iisnode.yml]: https://github.com/WindowsAzure/iisnode/blob/master/src/samples/configuration/iisnode.yml
[Azure CLI]: ../virtual-machines-command-line-tools.md
[Azure Developer Center]: /develop/nodejs/
[Create and deploy a Node.js application to Azure Web Sites]: /develop/nodejs/tutorials/create-a-website-(mac)/
[Continuous deployment using GIT in Azure App Service]: web-sites-publish-source-control.md
[MongoLab]: http://mongolab.com
[Node.js Web Application with Storage on MongoDB (Virtual Machine)]: /develop/nodejs/tutorials/website-with-mongodb-(mac)/
[node-mongo-finished]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/todo_list_noframe.png
[node-mongo-express-results]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/express_output.png
[download-publishing-settings]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/azure-account-download-cli.png
[import-publishing-settings]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/azureimport.png
[mongolab-create]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/mongolab-create.png
[mongolab-view]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/mongolab-view.png
 

<!---HONumber=August15_HO6-->