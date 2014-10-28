<properties linkid="develop-node-website-with-mongodb-mac" urlDisplayName="Website with MongoDB" pageTitle="Node.js website with MongoDB on a VM - Azure tutorial" metaKeywords="Azure tutorial MongoDB, MongoDB store data, access data MongoDB Node, Azure Node app" description="A tutorial that teaches you how to use MongoDB to store and access data from a Node application hosted on Azure." metaCanonical="http://www.windowsazure.com/fr-fr/develop/nodejs/tutorials/website-with-mongodb-mongolab/" services="web-sites,virtual-machines" documentationCenter="nodejs" title="Node.js Web Application with Storage on MongoDB (Virtual Machine)" authors="larryfr"  solutions="" writer="" manager="" editor=""  />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="nodejs" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr"></tags>

# Création d'une application Node.js sur Azure avec MongoDB dans une machine virtuelle

Ce didacticiel vous présente comment utiliser [MongoDB][MongoDB] sur une machine virtuelle Azure pour stocker des données et accéder à ces données depuis une application [Node][Node] hébergée dans un site web Azure. [MongoDB][MongoDB] est une base de données NoSQL libre qui offre des performances élevées.

Vous apprendrez à effectuer les opérations suivantes :

-   Configuration d'une machine virtuelle avec Ubuntu et MongoDB depuis le VM Depot.
-   Accès à MongoDB depuis une application Node
-   Utilisation des outils interplateforme pour Azure pour créer un site web Azure

Dans ce didacticiel, vous allez concevoir une application Web simple pour la gestion des tâches qui vous permet de créer et de récupérer des tâches et de les marquer comme terminées. Les tâches sont stockées dans MongoDB.

> [WACOM.NOTE] Ce didacticiel utilise une instance MongoDB installée sur une machine virtuelle. Si vous souhaitez utiliser une instance MongoDB hébergée fournie par MongoLabs, consultez la page [Création d'une application Node.js sur Azure avec MongoDB et le module MongoLab][Création d'une application Node.js sur Azure avec MongoDB et le module MongoLab].

Les fichiers de projets de ce didacticiel sont stockés dans un répertoire appelé **tasklist** et l'application terminée ressemble à ce qui suit :

![Une page Web avec une liste de tâches vide][Une page Web avec une liste de tâches vide]

> [WACOM.NOTE] De nombreuses étapes ci-dessous mentionnent l'utilisation de la ligne de commande. Pour ces étapes, utilisez la ligne de commande de votre système d'exploitation, par exemple **Windows PowerShell** (Windows) ou **Bash** (Shell Unix). Sur les systèmes OS X, vous pouvez accéder à la ligne de commande via l'application Terminal.

## Conditions préalables

Les étapes de ce didacticiel font référence à Node.js. Vous devez donc disposer d'une version récente de [Node.js][Node] dans votre environnement de développement.

De plus, [Git][Git] doit être accessible depuis la ligne de commande de votre environnement de développement, car il est utilisé pour le déploiement de l'application sur le site web Azure.

[WACOM.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

## Création d'une machine virtuelle

<!--This tutorial assumes you have created a virtual machine in Azure. After creating the virtual machine you need to install MongoDB on the virtual machine:  * To create a Linux virtual machine and install MongoDB, see [Installing MongoDB on a Linux Virtual machine].  After you have created the virtual machine in Azure and installed MongoDB, be sure to remember the DNS name of the virtual machine ("testlinuxvm.cloudapp.net", for example) and the external port for MongoDB that you specified in the endpoint.  You will need this information later in the tutorial.-->

Bien qu'il soit possible de créer une nouvelle machine virtuelle puis d'y installer MongoDB selon les [guides d'installation MongoDB][guides d'installation MongoDB], la plus grande partie de ce travail a déjà été effectuée par la communauté. Il est disponible dans le VM Depot. Les étapes qui suivent présentent l'utilisation d'une image VM Depot sur laquelle Mongo DB est déjà installé et configuré.

> [WACOM.NOTE] L'image de communauté utilisée par ce didacticiel stocke les données MongoDB sur le disque du système d'exploitation. Bien que cela soit suffisant pour les besoins du didacticiel, le stockage de données MongoDB sur un disque de données offrira de meilleures performances. Pour obtenir les étapes nécessaires à la création d'une nouvelle machine virtuelle, y compris un disque de données, et au stockage de données MongoDB sur le disque de données, consultez [Installer MongoDB sur Linux sur Azure][Installer MongoDB sur Linux sur Azure].

1.  Connectez-vous au [portail de gestion Azure][portail de gestion Azure], sélectionnez **Virtual Machines**, sélectionnez **Images**, puis sélectionnez **VM Depot**.

    ![capture d'écran de la sélection de VM Depot][capture d'écran de la sélection de VM Depot]

2.  Sélectionnez une image qui comprend MongoDB. Dans ce cas, je sélectionne Ubuntu pour réduire la liste aux images basées sur la distribution Linux Ubuntu. Enfin, je sélectionne MongoDB v2.2.3 sur l'image Hardened Ubuntu.

    ![capture d'écran de MongoDB v2.2.3 sur l'image Hardened Ubuntu][capture d'écran de MongoDB v2.2.3 sur l'image Hardened Ubuntu]

    > [WACOM.NOTE] Sélectionnez bien **More** pour voir toutes les informations sur l'image. Certaines images comportent des éléments de configuration supplémentaire qui sont nécessaires une fois la machine virtuelle créée à partir de l'image.

    Cliquez sur la flèche située en bas pour passer à l'écran suivant.

3.  Sélectionnez la région et le compte de stockage qui seront utilisés pour stocker le VHD de cette image. Cliquez sur la coche pour continuer.

    ![capture d'écran de la sélection d'un compte de stockage][capture d'écran de la sélection d'un compte de stockage]

    > [WACOM.NOTE] Cette opération lance un processus de copie qui copie l'image de VM Depot vers le compte de stockage spécifié. Elle peut prendre un peu de temps, 15 minutes ou plus.

4.  Une fois que l'état de l'image passe à **Pending registration**, sélectionnez **Register** et entrez un nom convivial pour la nouvelle image. Cliquez sur la coche pour continuer.

    ![capture d'écran de l'inscription d'une image][capture d'écran de l'inscription d'une image]

5.  Une fois l'état de l'image sur **Available**, sélectionnez **+ New**, **Virtual Machine**, **From Gallery**. Dans l'étape **Choose an Image**, sélectionnez **My Images**, puis sélectionnez l'image créée dans les étapes précédentes. Cliquez sur la flèche pour continuer.

    ![capture d'écran de l'image][capture d'écran de l'image]

6.  Indiquez le nom de la machine virtuelle, sa taille, le nom d'utilisateur. Cliquez sur la flèche pour continuer.

    ![Capture d'écran du nom de la machine virtuelle, du nom d'utilisateur, etc.][Capture d'écran du nom de la machine virtuelle, du nom d'utilisateur, etc.]

    > [WACOM.NOTE] Dans ce didacticiel, vous n'allez pas utiliser SSH pour vous connecter à distance à la machine virtuelle. Sélectionnez **Use a password** et indiquez un mot de passe si vous ne maîtrisez pas l'utilisation des certificats avec SSH.
    >
    > Pour plus d'informations sur l'utilisation de SSH avec une machine virtuelle Linux sur Azure, consultez la page [Utilisation de SSH avec Linux sur Azure][Utilisation de SSH avec Linux sur Azure].

7.  Sélectionnez un nouveau service cloud ou un service existant, puis la région dans laquelle la machine virtuelle sera créée. Cliquez sur la flèche pour continuer.

    ![capture d'écran de la configuration de la machine virtuelle][capture d'écran de la configuration de la machine virtuelle]

8.  Création de points de terminaison supplémentaires pour la machine virtuelle. Comme nous allons accéder à MongoDB sur cette machine virtuelle, ajoutez un nouveau point de terminaison à l'aide des informations suivantes :

    -   Nom : MongoDB
    -   Protocole : TCP
    -   Port public : 27017
    -   Port privé : 27017

    Pour exposer le portail Web de MongoDB, ajoutez un autre point de terminaison à l'aide des informations suivantes :

    -   Nom : MongoDBWeb
    -   Protocole : TCP
    -   Port public : 28017
    -   Port privé : 28017

    Cliquez sur la coche pour créer la machine virtuelle.

    ![capture d'écran de la configuration du point de terminaison][capture d'écran de la configuration du point de terminaison]

9.  Une fois que l'état de la machine virtuelle est passé à **Running**, vous pouvez ouvrir un navigateur Web et accéder à __http://\<NomDNSVM\>.cloudapp.net:28017/__ pour vérifier que MongoDB fonctionne. En bas de la page, un journal contient des informations sur le service, qui ressemblent à celles-ci :

        Fri Mar  7 18:57:16 [initandlisten] MongoDB starting : pid=1019 port=27017 dbpath=/var/lib/mongodb 64-bit host=localhost.localdomain
           18:57:16 [initandlisten] db version v2.2.3, pdfile version 4.5
           18:57:16 [initandlisten] git version: f570771a5d8a3846eb7586eaffcf4c2f4a96bf08
           18:57:16 [initandlisten] build info: Linux ip-10-2-29-40 2.6.21.7-2.ec2.v1.2.fc8xen #1 SMP Fri Nov 20 17:48:28 EST 2009 x86_64 BOOST_LIB_VERSION=1_49
           18:57:16 [initandlisten] options: { config: "/etc/mongodb.conf", dbpath: "/var/lib/mongodb", logappend: "true", logpath: "/var/log/mongodb/mongodb.log" }
           18:57:16 [initandlisten] journal dir=/var/lib/mongodb/journal
           18:57:16 [initandlisten] recover : no journal files present, no recovery needed
           18:57:17 [initandlisten] waiting for connections on port 27017

    Si le journal contient des erreurs, consultez la [documentation MongoDB][documentation MongoDB] pour obtenir des instructions sur la résolution des problèmes.

## Installation des modules et création de la structure

Dans cette section, vous allez créer une nouvelle application Node dans votre environnement de développement et utiliser npm pour ajouter des packages de module. Pour l'application de liste de tâches, vous allez utiliser les modules [Express][Express] et [Mongoose][Mongoose]. Le module Express contient une infrastructure de modèle de contrôleur d'affichage pour Node, alors que Mongoose est un pilote pour la communication avec MongoDB.

### Installation express et création de la structure

1.  Dans la ligne de commande, accédez au répertoire **tasklist**. Si le répertoire **tasklist** n'existe pas, créez-le.

    > [WACOM.NOTE] Ce didacticiel fait référence au dossier **tasklist**. Le chemin complet de ce dossier n'est pas mentionné, en raison des différences entre les systèmes d'exploitation. Créez ce dossier dans un emplacement facilement accessible dans votre système de fichiers local, par exemple **~/node/tasklist** ou **c:\\node\\tasklist**

2.  Entrez la commande suivante pour installer la commande express.

    npm install express-generator -g

    > [WACOM.NOTE] Lorsque vous utilisez le paramètre « -g » sur certains systèmes d'exploitation, vous pouvez obtenir une erreur ***Error: EPERM, chmod '/usr/local/bin/express'*** et une instruction demandant que le compte soit exécuté en tant qu'administrateur. Si cette erreur survient, utilisez la commande `sudo` pour exécuter npm avec des privilèges plus élevés.

    Le résultat de cette commande doit ressembler à ceci :

        express-generator@4.0.0 C:\Users\username\AppData\Roaming\npm\node_modules\express-generator
        ├── mkdirp@0.3.5
        └── commander@1.3.2 (keypress@0.1.0)                                                                         

    > [WACOM.NOTE] Le paramètre « -g » utilisé lors de l'installation du module express l'installe de façon globale. Cela permet d'accéder à la commande ***express*** pour générer un modèle automatique de site web sans devoir taper d'autres informations de chemin d'accès.

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

4.  Copiez le fichier **tasklist/bin/www** dans un fichier nommé **server.js** dans le dossier **tasklist**. Sites Web Azure s'attend à ce que le point d'entrée pour une application Node.js soit **server.js** ou **app.js**. Comme **app.js** existe déjà mais qu'il ne s'agit pas d'un point d'entrée, nous devons utiliser **server.js**.

5.  Modifiez le fichier **server.js** pour supprimer l'un des caractères « . » de la ligne suivante.

        var app = require('../app');

    La ligne modifiée devrait ressembler à ce qui suit.

        var app = require('./app');

    Cela est nécessaire car **server.js** (anciennement **bin/www**,) se trouve maintenant dans le même dossier que le fichier **app.js** nécessaire.

### Installation de modules supplémentaires

Le fichier **package.json** est un des fichiers créés par la commande **express**. Il contient une liste de modules supplémentaires qui sont nécessaires pour les applications Express. Ensuite, lorsque vous déployez cette application vers un site web Azure, ce fichier permet de déterminer quels modules doivent être installés sur Azure pour prendre en charge votre application.

1.  Dans le dossier **tasklist**, utilisez la commande suivante pour installer les modules décrits dans le fichier **package.json** :

        npm install

    Le résultat de cette commande doit ressembler à ceci :

        debug@0.7.4 node_modules\debug

        cookie-parser@1.0.1 node_modules\cookie-parser
        ├── cookie-signature@1.0.3
        └── cookie@0.1.0

        morgan@1.0.0 node_modules\morgan
        └── bytes@0.2.1

        body-parser@1.0.2 node_modules\body-parser
        ├── qs@0.6.6
        ├── raw-body@1.1.4 (bytes@0.3.0)
        └── type-is@1.1.0 (mime@1.2.11)

        express@4.0.0 node_modules\express
        ├── methods@0.1.0
        ├── parseurl@1.0.1
        ├── merge-descriptors@0.0.2
        ├── utils-merge@1.0.0
        ├── escape-html@1.0.1
        ├── cookie-signature@1.0.3
        ├── fresh@0.2.2
        ├── range-parser@1.0.0
        ├── buffer-crc32@0.2.1
        ├── qs@0.6.6
        ├── cookie@0.1.0
        ├── path-to-regexp@0.1.2
        ├── send@0.2.0 (mime@1.2.11)
        ├── type-is@1.0.0 (mime@1.2.11)
        ├── accepts@1.0.0 (negotiator@0.3.0, mime@1.2.11)
        └── serve-static@1.0.1 (send@0.1.4)

        jade@1.3.1 node_modules\jade
        ├── character-parser@1.2.0
        ├── commander@2.1.0
        ├── mkdirp@0.3.5
        ├── monocle@1.1.51 (readdirp@0.2.5)
        ├── constantinople@2.0.0 (uglify-js@2.4.13)
        ├── with@3.0.0 (uglify-js@2.4.13)
        └── transformers@2.1.0 (promise@2.0.0, css@1.0.8, uglify-js@2.2.5)                                                                

    Ceci installe tous les modules utilisés par défaut par les applications Express.

2.  Ensuite, entrez la commande suivante pour installer le module Mongoose en local et pour enregistrer une entrée lui correspondant dans le fichier **package.json** :

        npm install mongoose --save

    Le résultat de cette commande doit ressembler à ceci :

        mongoose@3.8.8 node_modules\mongoose
        ├── regexp-clone@0.0.1
        ├── sliced@0.0.5
        ├── muri@0.3.1
        ├── hooks@0.2.1
        ├── mpath@0.1.1
        ├── mpromise@0.4.3
        ├── ms@0.1.0
        ├── mquery@0.5.3
        └── mongodb@1.3.23 (kerberos@0.0.3, bson@0.2.5)         

    > [WACOM.NOTE] Vous pouvez ignorer tous les messages concernant l'installation de l'analyseur bson C++.

## Utilisation de MongoDB dans une application Node

Dans cette section, vous allez étendre l'application de base créée par la commande **express** en ajoutant un fichier **task.js** qui contient le modèle de vos tâches. Vous allez également modifier le fichier **app.js** existant et créer un fichier de contrôleur **tasklist.js** pour utiliser le modèle.

### Création du modèle

1.  Dans le répertoire **tasklist**, créez un répertoire appelé **models**.

2.  Dans le répertoire **models**, créez un fichier appelé **task.js**. Ce fichier contiendra le modèle des tâches créées par votre application.

3.  Au début du fichier **task.js**, ajoutez le code suivant pour référencer les bibliothèques nécessaires :

        var mongoose = require('mongoose'),
            Schema = mongoose.Schema;

4.  Ensuite, vous allez ajouter du code pour définir et exporter le modèle. Ce modèle sera utilisé pour assurer les interactions avec la base de données MongoDB.

        var TaskSchema = new Schema({
            itemName      : String,
            itemCategory  : String,
            itemCompleted : { type: Boolean, default: false },
            itemDate      : { type: Date, default: Date.now }
        });

        module.exports = mongoose.model('TaskModel', TaskSchema);

5.  Enregistrez, puis fermez le fichier **task.js**.

### Création du contrôleur

1.  Dans le répertoire **tasklist/routes**, créez un nouveau fichier **tasklist.js** et ouvrez-le dans un éditeur de texte.

2.  Ajoutez le code suivant à **tasklist.js**. Ce code charge le module mongoose et le modèle de tâches défini dans **task.js**. La fonction TaskList est utilisée pour créer la connexion au serveur MongoDB basée sur la valeur **connection** :

        var mongoose = require('mongoose'),
            task = require('../models/task.js');

        module.exports = TaskList;

        function TaskList(connection) {
          mongoose.connect(connection);
        }

3.  Continuez à modifier le fichier **tasklist.js** en ajoutant les méthodes utilisées pour afficher les tâches (**showTasks**), ajouter les tâches (**addTask**) et marquer les tâches comme terminées (**completeTasks**) :

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

4.  Enregistrez le fichier **tasklist.js**.

### Modification de app.js

1.  Dans le répertoire **tasklist**, ouvrez le fichier **app.js** dans un éditeur de texte. Ce fichier a été créé ultérieurement à l'aide de la commande **express**.

2.  Ajoutez le code qui suit au début du fichier **app.js**. Il initialise **TaskList** avec la chaîne de connexion du serveur MongoDB :

        var TaskList = require('./routes/tasklist');
        var taskList = new TaskList(process.env.MONGODB_URI);

    Notez la deuxième ligne. Vous accédez à une variable d'environnement que vous configurerez ultérieurement qui contient les informations de connexion de votre instance Mongo. Si vous disposez d'une instance Mongo locale pour le développement, vous pouvez définir cette valeur de façon temporaire sur « localhost » au lieu de process.env.MONGODB\_URI.

3.  Recherchez les lignes suivantes :

        app.use('/', routes);
        app.use('/users', users);

    Remplacez les deux lignes ci-dessus par ce qui suit :

        app.get('/', taskList.showTasks.bind(taskList));
        app.post('/addtask', taskList.addTask.bind(taskList));
        app.post('/completetask', taskList.completeTask.bind(taskList));

    Ce code ajoute les fonctions définies dans **tasklist.js** en tant que routes.

4.  Enregistrez le fichier **app.js**.

### Modification de la vue d'index

1.  Passez dans le répertoire **views** et ouvrez le fichier **index.jade** dans un éditeur de texte.

2.  Remplacez le contenu du fichier **index.jade** par le code qui suit. Ce code définit la vue pour l'affichage des tâches existantes, ainsi qu'un formulaire pour l'ajout de nouvelles tâches et pour marquer les tâches existantes comme terminées.

        h1= title
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

<!-- ##Run your application locally  To test the application on your local machine, perform the following steps:  1. From the command-line, change directories to the **tasklist** directory.  2. Set the MONGODB_URI environment variable on your development environment to point to the virtual machine hosting MongoDB. In the examples below, replace __mymongodb__ with your virtual machine name.      On a Windows system, use the following to set the environment variable.          set MONGODB_URI=mongodb://mymongodb.cloudapp.net/tasks      On an OS X or Linux-based system, use the following to set the environment variable.          set MONGODB_URI=mongodb://mymongodb.cloudapp.net/tasks         export MONGODB_URI      This will instruct the application to connect to MongoDB on the __mymongodb.cloudapp.net__ virtual machine created earlier, and to use a DB named 'tasks'.  2. Use the following command to launch the application locally:          node app.js  3. Open a web browser and navigate to http://localhost:3000. This should display a web page similar to the following:      ![A webpage displaying an empty tasklist][node-mongo-finished]  4. Use the provided fields for **Item Name** and **Item Category** to enter information, and then click **Add item**.      ![An image of the add item field with populated values.][node-mongo-add-item]  5. The page should update to display the item in the ToDo List table.      ![An image of the new item in the list of tasks][node-mongo-list-items]  6. To complete a task, simply check the checkbox in the Complete column, and then click **Update tasks**. While there is no visual change after clicking **Update tasks**, the document entry in MongoDB has now been marked as completed.  7. To stop the node process, go to the command-line and press the **CTRL** and **C** keys. -->

## Déploiement de votre application dans Azure

Les étapes de cette section utilisent les outils en ligne de commande Azure pour créer un site web Azure, puis Git pour déployer votre application. Pour effectuer ces étapes, vous devez disposer d'un abonnement Azure.

> [WACOM.NOTE] Ces étapes peuvent également être effectuées depuis le portail Azure. Pour savoir comment utiliser le portail Azure pour déployer une application Node.js, consultez la page [Création et déploiement d'une application Node.js sur des sites web Azure][Création et déploiement d'une application Node.js sur des sites web Azure].

> [WACOM.NOTE] S'il s'agit du premier site web Azure que vous avez créé, utilisez le portail Azure pour déployer cette application.

### Installation de l'interface de ligne de commande interplateforme Azure

L'interface de ligne de commande interplateforme Azure (xplat-cli) vous permet d'effectuer des opérations de gestion sur les services Azure. Si vous n'avez pas déjà installé et configuré xplat-cli dans votre environnement de développement, consultez les instructions dans [Installation de l'interface de ligne de commande interplateforme Azure][Installation de l'interface de ligne de commande interplateforme Azure].

### Création d'un site web Azure

1.  Dans la ligne de commande, accédez au répertoire **tasklist**.

2.  Pour créer un site web Azure, utilisez la commande suivante. Remplacez « myuniquesitename » par le nom de site unique de votre site web. Cette valeur est utilisée comme une partie de l'URL du site web final.

        azure site create myuniquesitename --git

    Vous êtes invité à préciser le centre de données dans lequel sera situé le site. Sélectionnez le centre de données le plus proche de vous du point de vue géographique.

    Le paramètre `--git`= crée un référentiel Git local dans le dossier **tasklist** s'il n'en existe pas. Il crée également une télécommande [Git remote][Git remote] appelée « azure », qui sera utilisée pour publier l'application sur Azure. Il crée un fichier [iisnode.yml][iisnode.yml], qui contient les paramètres utilisés par Azure pour héberger les applications Node. Enfin, il crée également un fichier .gitignore afin d'exclure le dossier node-modules de la publication sur .git.

    > [WACOM.NOTE] Si cette commande est exécutée depuis un répertoire qui contient déjà un référentiel Git, le répertoire n'est pas réinitialisé.

    > [WACOM.NOTE] Si le paramètre --git est omis mais que le répertoire contient un référentiel Git, la télécommande « azure » est quand même créée.

    Une fois cette commande terminée, le résultat doit ressembler à ce qui suit. Notez que la ligne qui commence par **Created website at** contient l'URL du site web.

        info:   Executing command site create
        info:   Using location southcentraluswebspace
        info:   Executing `git init`
        info:   Creating default web.config file
        info:   Creating a new web site
        info:   Created web site at  mongodbtasklist.azurewebsites.net
        info:   Initializing repository
        info:   Repository initialized
        info:   Executing `git remote add azure http://username@mongodbtasklist.azurewebsites.net/mongodbtasklist.git`
        info:   site create command OK

    > [WACOM.NOTE\> S'il s'agit du premier site web Azure pour votre abonnement, vous êtes invité à utiliser le portail pour créer le site web. Pour plus d'informations, consultez la page [Création et déploiement d'une application Node.js sur des sites web Azure][Création et déploiement d'une application Node.js sur des sites web Azure].

### Définir la variable d'environnement MONGODB\_URI

L'application s'attend à ce que la chaîne de connexion de l'instance MongoDB soit disponible dans la variable d'environnement MONGODB\_URI. Pour définir cette valeur pour le site web, utilisez la commande suivante :

    azure site config add MONGODB_URI=mongodb://mymongodb.cloudapp.net/tasks

Ceci crée un paramètre d'application pour le site web, utilisé pour remplir la variable d'environnement MONGODB\_URI lue par le site web. Remplacez la valeur de « mymongodb.cloudapp.net » par le nom de la machine virtuelle sur laquelle MongoDB a été installé.

### Publication de l'application

1.  Dans la fenêtre Terminal, passez dans le répertoire **tasklist** si vous ne vous y trouvez pas déjà.

2.  Utilisez les commandes suivantes pour ajouter, puis valider les fichiers dans le référentiel :

        git add .
        git commit -m "adding files"

3.  Lorsque vous envoyez les modifications récentes du référentiel Git sur le site web Azure, vous devez spécifier que la branche cible est **master**, car elle est utilisée pour le contenu du site web.

        git push azure master

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

4.  Une fois l'opération push terminée, accédez au site web à l'aide de la commande `azure site browse` pour voir votre application.

## Étapes suivantes

Bien que les étapes de cet article décrivent l'utilisation de MongoDB pour stocker des informations, vous pouvez aussi utiliser Azure Table Service. Pour plus d'informations, consultez la page [Application Web Node.js avec Azure Table Service][Application Web Node.js avec Azure Table Service].

Pour savoir comment utiliser une instance hébergée de MongoDB fournie par MongoLabs, consultez la page [Création d'une application Node.js sur Azure avec MongoDB et le module MongoLab][Création d'une application Node.js sur Azure avec MongoDB et le module MongoLab].

Pour plus d'informations sur la sécurisation de MongoDB, consultez la page [Sécurité de MongoDB][Sécurité de MongoDB].

## Ressources supplémentaires

[Outil en ligne de commande Azure pour Mac et Linux][Création et déploiement d'une application Node.js sur des sites web Azure]
[Publication sur les Sites Web Azure avec Git][Publication sur les Sites Web Azure avec Git]

  [MongoDB]: http://www.mongodb.org
  [Node]: http://nodejs.org
  [Création d'une application Node.js sur Azure avec MongoDB et le module MongoLab]: /fr-fr/develop/nodejs/tutorials/website-with-mongodb-mongolab/
  [Une page Web avec une liste de tâches vide]: ./media/store-mongodb-web-sites-nodejs-use-mac/todo_list_empty.png
  [Git]: http://git-scm.com
  [create-account-and-websites-note]: ../includes/create-account-and-websites-note.md
  [guides d'installation MongoDB]: http://docs.mongodb.org/manual/installation/
  [Installer MongoDB sur Linux sur Azure]: http://docs.mongodb.org/ecosystem/tutorial/install-mongodb-on-linux-in-azure/
  [portail de gestion Azure]: https://manage.windowsazure.com/
  [capture d'écran de la sélection de VM Depot]: ./media/web-sites-nodejs-store-data-mongodb/browsedepot.png
  [capture d'écran de MongoDB v2.2.3 sur l'image Hardened Ubuntu]: ./media/web-sites-nodejs-store-data-mongodb/selectimage.png
  [capture d'écran de la sélection d'un compte de stockage]: ./media/web-sites-nodejs-store-data-mongodb/storageaccount.png
  [capture d'écran de l'inscription d'une image]: ./media/web-sites-nodejs-store-data-mongodb/register.png
  [capture d'écran de l'image]: ./media/web-sites-nodejs-store-data-mongodb/myimages.png
  [Capture d'écran du nom de la machine virtuelle, du nom d'utilisateur, etc.]: ./media/web-sites-nodejs-store-data-mongodb/vmname.png
  [Utilisation de SSH avec Linux sur Azure]: http://www.windowsazure.com/fr-fr/documentation/articles/linux-use-ssh-key/
  [capture d'écran de la configuration de la machine virtuelle]: ./media/web-sites-nodejs-store-data-mongodb/vmconfig.png
  [capture d'écran de la configuration du point de terminaison]: ./media/web-sites-nodejs-store-data-mongodb/endpoints.png
  [documentation MongoDB]: http://docs.mongodb.org/manual/
  [Express]: http://expressjs.com
  [Mongoose]: http://mongoosejs.com
  [Création et déploiement d'une application Node.js sur des sites web Azure]: /fr-fr/develop/nodejs/tutorials/create-a-website-(mac)/
  [Installation de l'interface de ligne de commande interplateforme Azure]: /fr-fr/documentation/articles/xplat-cli/
  [Git remote]: http://git-scm.com/docs/git-remote
  [iisnode.yml]: https://github.com/WindowsAzure/iisnode/blob/master/src/samples/configuration/iisnode.yml
  [Application Web Node.js avec Azure Table Service]: /fr-fr/develop/nodejs/tutorials/web-site-with-storage/
  [Sécurité de MongoDB]: http://docs.mongodb.org/manual/security/
  [Publication sur les Sites Web Azure avec Git]: /fr-fr/develop/nodejs/common-tasks/publishing-with-git/
