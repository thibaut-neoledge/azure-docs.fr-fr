<properties linkid="develop-nodejs-tutorials-web-site-with-mongodb-mongolab" urlDisplayName="Web site with MongoDB" pageTitle="Node.js web site with MongoDB on MongoLab - Azure" metaKeywords="" description="Learn how to create a Node.js Azure Web Site that connects to a MongoDB instance hosted on MongoLab." metaCanonical="" services="web-sites,virtual-machines" documentationCenter="Node.js" title="Create a Node.js Application on Azure with MongoDB using the MongoLab Add-On" authors="" solutions="" manager="" editor="" />

Création d'une application Node.js sur Azure avec MongoDB à l'aide du module MongoLab
=====================================================================================

*Par Eric Sedor, MongoLab*

Bonjour, chers aventuriers ! Bienvenue dans MongoDB-as-a-Service. Ce didacticiel vous apprendra à effectuer les opérations suivantes :

1.  [Configuration de la base de données](#provision) : le module [MongoLab](http://mongolab.com) de l'Azure Store fournit une base de données MongoDB hébergée dans le cloud Azure et gérée par la plateforme de base de données cloud de MongoLab.
2.  [Création de l'application](#create) : il s'agit d'une simple application Node.js pour conserver une liste de tâches.
3.  [Déploiement de l'application](#deploy) : en combinant quelques astuces de configuration, nous apporterons un nouveau souffle à notre code.
4.  [Gestion de base de données](#manage) : finalement, vous découvrirez le portail de gestion de base de données basé sur le Web où vous pouvez effectuer des recherches, visualiser et modifier les données facilement.

Au cours de ce didacticiel, n'hésitez pas à envoyer un e-mail à tout moment à l'adresse [support@mongolab.com](mailto:support@mongolab.com) si vous avez des questions.

Avant de poursuivre, vérifiez que les éléments suivants sont installés :

* [Node.js](http://nodejs.org) version 0.8.14+

* [Git](http://git-scm.com)

[WACOM.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

## D&eacute;marrage rapide
Si vous connaissez d&eacute;j&agrave; l'Azure Store, cette section vous permettra de d&eacute;marrer rapidement. Dans le cas contraire, consultez la section <a href="#provision">Configuration de la base de donn&eacute;es</a> ci-dessous.

<ol>
  <li>Ouvrez l'Azure Store.<br /> <img src="./media/store-mongolab-web-sites-nodejs-store-data-mongodb/button-store.png" alt="Store" /></li>
  <li>Cliquez sur le module MongoLab.<br /> <img src="./media/store-mongolab-web-sites-nodejs-store-data-mongodb/entry-mongolab.png" alt="MongoLab" /></li>
  <li>Cliquez sur le module MongoLab dans la liste des modules compl&eacute;mentaires, puis cliquez sur <strong>Connection Info</strong>.<br /> <img src="./media/store-mongolab-web-sites-nodejs-store-data-mongodb/button-connectioninfo.png" alt="ConnectionInfoButton" />  </li>
  <li>Copiez le fichier MONGOLAB_URI dans votre presse-papiers.<br /> <img src="./media/store-mongolab-web-sites-nodejs-store-data-mongodb/dialog-mongolab_connectioninfo.png" alt="ConnectionInfoScreen" /><br /> <strong>Cet URI contient votre nom d&rsquo;utilisateur et votre mot de passe.  Consid&eacute;rez ces informations comme &eacute;tant sensibles, ne les partagez pas.</strong></li>
  <li>Ajoutez la valeur &agrave; la liste Connection Strings dans le menu Configuration de votre application Azure Web&nbsp;:<br /> <img src="./media/store-mongolab-web-sites-nodejs-store-data-mongodb/focus-mongolab-websiteconnectionstring.png" alt="Cha&icirc;nesConnexionSiteWeb" /></li>
  <li>Dans <strong>Name</strong>, entrez MONGOLAB_URI.</li>
  <li>Dans <strong>Value</strong>, collez la cha&icirc;ne de connexion obtenue dans la section pr&eacute;c&eacute;dente.</li>
  <li>S&eacute;lectionnez <strong>Custom</strong> dans la liste d&eacute;roulante Type (&agrave; la place de la valeur par d&eacute;faut <strong>SQLAzure</strong>).</li>
  <li>Ex&eacute;cutez <code>npm install mongoose</code> pour obtenir M		ongoose, un pilote de n&oelig;ud MongoDB.</li>
  <li>
    <p>Configurez un hook dans votre code pour obtenir votre URI de connexion MongoLab &agrave; partir d'une variable d'environnement, puis connectez-vous &agrave;&nbsp;:</p>

    <pre><code> var mongoose = require('mongoose');  
 ... 
 var connectionString = process.env.CUSTOMCONNSTR_MONGOLAB_URI 
 ... 
 mongoose.connect(connectionString);
</code></pre>
  </li>
</ol>

<p>Remarque&nbsp;: Azure ajoute le pr&eacute;fixe <strong>CUSTOMCONNSTR_</strong> &agrave; la cha&icirc;ne de connexion d&eacute;clar&eacute;e &agrave; l'origine, ce qui explique pourquoi le code indique <strong>CUSTOMCONNSTR_MONGOLAB_URI.</strong> au lieu de <strong>MONGOLAB_URI</strong>.</p>

<p>Passons maintenant au didacticiel complet...</p>

Configuration de la base de données
-----------------------------------

[WACOM.INCLUDE [howto-provision-mongolab](../includes/howto-provision-mongolab.md)]

Création de l'application
-------------------------

Dans cette section, vous allez développer votre environnement de développement et définir le code pour une application Web de base de liste de tâches qui utilise Node.js, Express et MongoDB. [Express](http://expressjs.com) offre une infrastructure View Controller pour le nœud, tandis que [Mongoose](http://mongoosejs.com) est un pilote permettant la communication avec MongoDB en nœud.

### Paramétrage

#### Génération de la structure et installation des modules

1.  Dans la ligne de commande, créez le répertoire **tasklist**, puis ouvrez-le. Il s'agit du répertoire de votre projet.
2.  Entrez la commande suivante pour installer Express.

		npm install express -g
 
	`-g` indicates global mode, which we use to make the <strong>express</strong> module available without specifying a directory path. If you receive <strong>Error: EPERM, chmod '/usr/local/bin/express'</strong>, use <strong>sudo</strong> to run npm at a higher privilege level.

    Le résultat de cette commande doit ressembler à ceci :

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

    Le résultat de cette commande doit ressembler à ceci :

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

    Le fichier **package.json** est un des fichiers créés par la commande **express**. Il contient une liste de modules supplémentaires qui sont nécessaires pour les applications Express. Ensuite, lorsque vous déployez cette application sur un site Web Azure, ce fichier est utilisé pour déterminer quels modules doivent être installés sur Azure pour prendre en charge votre application.

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

1.  Enregistrez le fichier **tasklist.js**.

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

 	Notez la deuxième ligne. Vous accédez à une variable d'environnement que vous configurerez ultérieurement qui contient les informations de connexion de votre instance Mongo. Si une instance mongo locale est exécutée dans un but de développement, il peut être préférable de définir temporairement cette valeur sur « localhost » plutôt que sur `process.env.CUSTOMCONNSTR_MONGOLAB_URI`.

1.  Recherchez les lignes qui commencent par `app.get` et remplacez-les par les lignes suivantes :

         app.get('/', taskList.showTasks.bind(taskList));
         app.post('/addtask', taskList.addTask.bind(taskList));
         app.post('/completetask', taskList.completeTask.bind(taskList));

    Ce code ajoute les fonctions définies dans **tasklist.js** en tant que routes.

2.  Enregistrez le fichier **app.js**.

Déploiement de l’application
----------------------------

Maintenant que l'application a été développée, il est temps de créer un site Web Azure pour l'héberger. Configurez le site Web et déployez le code. L'élément essentiel de cette section est l'utilisation de la chaîne de connexion MongoDB (URI). Vous allez configurer une variable d'environnement dans votre site Web avec cette URI, afin de conserver l'URI séparée du code. L'URI doit être traitée comme une information sensible car elle contient les identifiants de connexion à votre base de données.

Les étapes de cette section utilisent les outils en ligne de commande Azure pour créer un nouveau site Web Azure, puis déploient votre application à l'aide de Git. Pour effectuer ces étapes, vous devez disposer d'un abonnement Azure.

### Installation de l'outil en ligne de commande Azure pour Mac et Linux

Pour installer les outils en ligne de commande, utilisez la commande suivante :

    npm install azure-cli -g

Si vous avez déjà installé le fichier **Azure SDK for Node.js** depuis le [Centre de développement Azure](/en-us/develop/nodejs/), les outils en ligne de commande devraient déjà être installés. Pour plus d'informations, consultez la page [Outil en ligne de commande Azure pour Mac et Linux](/en-us/develop/nodejs/how-to-guides/command-line-tools/).

Alors que les outils en ligne de commande Azure ont été créés en priorité pour les utilisateurs Mac et Linux, ils sont basés sur Node.js et peuvent fonctionner sur tout système capable d'exécuter Node.

### Importation de paramètres de publication

Avant d'utiliser les outils en ligne de commande avec Azure, vous devez télécharger un fichier comportant des informations relatives à votre abonnement. Procédez comme suit pour télécharger et importer ce fichier.

1.  À partir de la ligne de commande, saisissez la commande suivante pour lancer le navigateur et accéder à la page de téléchargement. Si vous y êtes invité, connectez-vous avec le compte associé à votre abonnement.

         azure account download

    ![Page de téléchargement](./media/store-mongolab-web-sites-nodejs-store-data-mongodb/azure-account-download-cli.png)

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

<h3>Cr&eacute;ation d'un nouveau site&nbsp;Web et mise &agrave; disposition du code</h3>

<p>Il est tr&egrave;s facile de cr&eacute;er un site&nbsp;Web dans Azure. S'il s'agit de votre premier site&nbsp;Web Azure, vous devez utiliser le portail. Si vous en avez d&eacute;j&agrave; cr&eacute;&eacute; un ou plusieurs, passez &agrave; l'&eacute;tape&nbsp;7.</p>

<ol>
  <li>Dans le portail Azure, cliquez sur <strong>New</strong>.  <br /> <img src="./media/store-mongolab-web-sites-nodejs-store-data-mongodb/button-new.png" alt="Nouveau" /></li>
  <li>S&eacute;lectionnez <strong>Compute &gt; Web Site &gt; Quick Create</strong>. <img src="./media/store-mongolab-web-sites-nodejs-store-data-mongodb/screen-mongolab-newwebsite.png" alt="CreateSite" /></li>
  <li>Saisissez un pr&eacute;fixe d'URL. S&eacute;lectionnez le nom de votre choix, tout en gardant &agrave; l'esprit qu'il doit &ecirc;tre unique (&laquo;&nbsp;monappmongo&nbsp;&raquo; risque fort de ne pas &ecirc;tre disponible).</li>
  <li>Cliquez sur <strong>Create Web Site</strong>.</li>
  <li>Une fois la cr&eacute;ation du site&nbsp;Web termin&eacute;e, cliquez sur le nom du site&nbsp;Web dans la liste des sites&nbsp;Web. Le tableau de bord du site&nbsp;Web s'affiche.<br /> <img src="./media/store-mongolab-web-sites-nodejs-store-data-mongodb/screen-mongolab-websitedashboard.png" alt="WebSiteDashboard" /></li>
  <li>Cliquez <strong>Configurer la publication Git</strong> sous <strong>quick glance</strong>, puis entrez votre nom d'utilisateur et votre mot de passe git. Vous utiliserez ce mot de passe pour le transfert vers votre site&nbsp;Web (&eacute;tape&nbsp;9).  </li>
  <li>
    <p>Si vous avez cr&eacute;&eacute; votre site&nbsp;Web en suivant les &eacute;tapes ci-dessus, la commande suivante cl&ocirc;t le processus. Toutefois, si vous disposez d&eacute;j&agrave; de plus d'un site&nbsp;Web Azure, vous pouvez ignorer les &eacute;tapes ci-dessus et cr&eacute;er un nouveau site&nbsp;Web &agrave; l'aide de cette m&ecirc;me commande. Dans votre r&eacute;pertoire de projet <strong>tasklist</strong>&nbsp;: </p>

    <pre><code> azure site create nomuniquedusite --git  
</code></pre>
    <p>Remplacez &laquo;&nbsp;nom uniquedusite&nbsp;&raquo; par le nom unique donn&eacute; &agrave; votre site&nbsp;Web. Si le site&nbsp;Web est cr&eacute;&eacute; &agrave; partir de cette commande, il vous est demand&eacute; d'indiquer le centre de donn&eacute;es sur lequel sera conserv&eacute; le site. S&eacute;lectionnez le centre de donn&eacute;es le plus proche de l'emplacement de votre base de donn&eacute;es MongoLab.</p>

    <p>Le param&egrave;tre <code>--git</code> cr&eacute;e&nbsp;: 
    <p>Un r&eacute;f&eacute;rentiel git local dans le dossier <strong>tasklist</strong>, si aucun n'existe d&eacute;j&agrave;&nbsp;
    <p>Un <a href="http://git-scm.com/docs/git-remote">Git remote</a> nomm&eacute; &laquo;&nbsp;azure&nbsp;&raquo;, qui sera utilis&eacute; pour publier l'application sur Azure&nbsp;
    <p>Un fichier<a href="https://github.com/WindowsAzure/iisnode/blob/master/src/samples/configuration/iisnode.yml">iisnode.yml</a>, qui contient les param&egrave;tres utilis&eacute;s par Azure pour g&eacute;rer des applications n&oelig;ud&nbsp;; 
    <p>Un fichier gitignore pour emp&ecirc;cher la publication du dossier modules-n&oelig;uds sur .git.  </p>

    <p>Une fois cette commande termin&eacute;e, le r&eacute;sultat doit ressembler &agrave; ce qui suit. Notez que la ligne commen&ccedil;ant par <strong>Created web site at</strong> contient l'URL du site Web.</p>

    <pre><code> info:   Executing command site create
 info:   Using location southcentraluswebspace
 info:   Executing `git init`
 info:   Creating default web.config file
 info:   Creating a new web site
 info:   Created web site at  mongodbtasklist.azurewebsites.net
 info:   Initializing repository
 info:   Repository initialized
 info:   Executing `git remote add azure http://gitusername@myuniquesitename.azurewebsites.net/mongodbtasklist.git`
 info:   site create command OK
</code></pre>
  </li>
  <li>
    <p>Utilisez la commande suivante pour ajouter, puis valider vos fichiers dans votre r&eacute;f&eacute;rentiel Git local&nbsp;:</p>

    <pre><code> git add .
 git commit -m &quot;adding files&quot;
</code></pre>
  </li>
  <li>
    <p>Transfert du code</p>

    <pre><code> git push azure master  
</code></pre>
    <p>Lorsque vous copiez les derni&egrave;res modifications du r&eacute;f&eacute;rentiel Git vers le site Web Azure, vous devez sp&eacute;cifier que la branche cible est <strong>master</strong>, car elle est utilis&eacute;e pour le contenu du site Web. Si un mot de passe vous est demand&eacute;, saisissez le mot de passe cr&eacute;&eacute; lors de la configuration de la publication git pour votre site&nbsp;Web ci-dessus.</p>

    <p>Vous devez voir des informations similaires &agrave; ce qui suit. &Agrave; mesure que le d&eacute;ploiement s'effectue, Azure t&eacute;l&eacute;charge tous les modules npm. </p>

    <pre><code> Counting objects: 17, done.
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
* [new branch]      master -&gt; master
</code></pre>
  </li>
</ol>


<p>Vous avez presque termin&eacute;&nbsp;!</p>

### Configuration de votre environnement

Vous vous rappelez de la variable process.env.CUSTOMCONNSTR\_MONGOLAB\_URI dans le code ? Nous allons remplir cette variable d'environnement avec la valeur fournie à Azure au cours de la configuration de la base de données MongoLab.

#### Accédez à la chaîne de connexion MongoLab

[WACOM.INCLUDE [howto-get-connectioninfo-mongolab](../includes/howto-get-connectioninfo-mongolab.md)]

#### Ajoutez la chaîne de connexion aux variables d'environnement du site Web

[WACOM.INCLUDE [howto-save-connectioninfo-mongolab](../includes/howto-save-connectioninfo-mongolab.md)]

Vous avez réussi !
------------------

Exécutez `azure site browse` depuis le répertoire de votre projet pour ouvrir automatiquement un navigateur (ou ouvrez un navigateur et accédez manuellement à l'URL de votre site Web, monsiteunique.azurewebsites.net):

![Une page Web affiche une liste de tâches vide.](./media/store-mongolab-web-sites-nodejs-store-data-mongodb/todo_list_noframe.png)

Gestion de la base de données
-----------------------------

[WACOM.INCLUDE [howto-access-mongolab-ui](../includes/howto-access-mongolab-ui.md)]

Félicitations ! Vous venez de lancer une application Node.js utilisant une base de données MongoDB hébergée sur MongoLab ! Maintenant que vous disposez d'une base de données MongoLab, vous pouvez contacter <support@mongolab.com> pour toute question ou problème relatif à votre base de données ainsi que pour obtenir de l'aide concernant MongoDB ou le pilote de nœud. Bonne continuation !



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
[for free]: /en-us/pricing/free-trial
[Git remote]: http://git-scm.com/docs/git-remote
[azure-sdk-for-node]: https://github.com/WindowsAzure/azure-sdk-for-node
[iisnode.yml]: https://github.com/WindowsAzure/iisnode/blob/master/src/samples/configuration/iisnode.yml
[Azure command-line tool for Mac and Linux]: /en-us/develop/nodejs/how-to-guides/command-line-tools/
[Azure Developer Center]: /en-us/develop/nodejs/
[Create and deploy a Node.js application to Azure Web Sites]: /en-us/develop/nodejs/tutorials/create-a-website-(mac)/
[Publishing to Azure Web Sites with Git]: /en-us/develop/nodejs/common-tasks/publishing-with-git/
[MongoLab]: http://mongolab.com
[Node.js Web Application with Storage on MongoDB (Virtual Machine)]: /en-us/develop/nodejs/tutorials/website-with-mongodb-(mac)/
[node-mongo-finished]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/todo_list_noframe.png
[node-mongo-express-results]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/express_output.png
[download-publishing-settings]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/azure-account-download-cli.png
[import-publishing-settings]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/azureimport.png
[mongolab-create]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/mongolab-create.png
[mongolab-view]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/mongolab-view.png