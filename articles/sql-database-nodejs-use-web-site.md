<properties linkid="develop-nodejs-tutorials-web-site-with-sql-database" urlDisplayName="Website with SQL Database" pageTitle="Node.js website with SQL Database - Azure tutorial" metaKeywords="" description="Learn how to create a Node.js website that accesses a SQL Database and is deployed to Azure" metaCanonical="" services="web-sites,sql-database" documentationCenter="nodejs" title="Node.js Web Application using the Azure SQL Database" authors="larryfr" solutions="" manager="" editor="" />

<tags ms.service="sql-database" ms.workload="data-management" ms.tgt_pltfrm="na" ms.devlang="nodejs" ms.topic="article" ms.date="01/01/1900" ms.author="larryfr"></tags>

# Application Web Node.js avec la base de données SQL Azure

Ce didacticiel vous indique comment utiliser la base de données SQL fournie par Azure Data Management pour stocker vos données et y accéder à partir d'une application [node][] hébergée sur Azure. Ce didacticiel part du principe que vous avez déjà une certaine expérience en tant qu'utilisateur des applications node et de [Git][]

Vous apprendrez à effectuer les opérations suivantes :

-   utiliser le portail de gestion Azure pour créer un site web Azure et une base de données SQL ;

-   utiliser le gestionnaire de package node (ou npm) pour installer les modules node ;

-   utiliser une base de données SQL à l'aide du module node-sqlserver ;

-   utiliser les paramètres d'application pour spécifier les valeurs runtime pour une application.

Dans ce didacticiel, vous allez concevoir une application Web simple pour la gestion des tâches qui vous permet de créer et de récupérer des tâches et de les marquer comme terminées. Les tâches sont stockées dans la base de données SQL.

Les fichiers de projets de ce didacticiel sont stockés dans un répertoire appelé **tasklist** et l'application terminée ressemble à ce qui suit :

![Une page Web avec une liste de tâches vide][]

<div class="dev-callout">
<b>Remarque</b>
<p>Le pilote Microsoft SQL Server pour Node.JS utilis&eacute; dans ce didacticiel est actuellement disponible dans sa version pr&eacute;liminaire. Il repose sur des composants d'ex&eacute;cution qui sont disponibles uniquement sur les syst&egrave;mes d'exploitation Microsoft Windows et Azure.</p>
</div>

<div class="dev-callout">
<strong>Remarque</strong>
<p>Ce didacticiel fait r&eacute;f&eacute;rence au dossier <strong>tasklist</strong>. Le chemin complet de ce dossier n'est pas mentionn&eacute;, en raison des diff&eacute;rences entre les syst&egrave;mes d'exploitation. Cr&eacute;ez ce dossier dans un emplacement facilement accessible dans votre syst&egrave;me de fichiers local, par exemple <strong>~/node/tasklist</strong> ou <strong>c:\node\tasklist</strong></p>
</div>

<div class="dev-callout">
<strong>Remarque</strong>
<p>Plusieurs des &eacute;tapes ci-dessous vous demandent d'utiliser la ligne de commande. Pour ce faire, utilisez la ligne de commande de votre syst&egrave;me d'exploitation, par exemple <strong>cmd.exe</strong> (Windows) ou <strong>Bash</strong> (Unix Shell). Sur les syst&egrave;mes OS&nbsp;X, vous pouvez acc&eacute;der &agrave; la ligne de commande via l'application Terminal.</p>
</div>

## Conditions préalables

Avant de suivre les instructions de cet article, vérifiez que les éléments suivants sont installés :

-   [node][] version 0.6.14 ou supérieure

-   [Git][]

-   Bibliothèques clientes natives Microsoft SQL Server - disponibles avec le [Microsoft SQL Server 2012 Feature Pack][]

-   Un éditeur de texte

-   Un navigateur Web

<!--div chunk="../../Shared/Chunks/create-account-and-websites-note.md" /-->

## Créer un site web avec une base de données

Suivez cette procédure pour créer un site web Azure et une base de données SQL :

1.  Connectez-vous au [portail de gestion Azure][].
2.  Cliquez sur l'icône **+ New** dans le coin inférieur gauche du portail.

    ![Créer un site Web Azure][]

3.  Cliquez sur **SITE WEB**, puis sur **CRÉATION PERSONNALISÉE**.

    ![Création personnalisée d'un site Web][]

    Entrez une valeur pour **URL** et, dans la liste déroulante **BASE DE DONNÉES**, sélectionnez **Créer une base de données SQL**, puis sélectionnez un centre de données pour votre site web dans la liste déroulante **﻿RÉGION**. Cliquez sur la flèche située en bas de la boîte de dialogue.

    ![Indiquer les détails du site Web][]

4.  Entrez le **nom** de votre base de données, sélectionnez l'**édition** [(WEB ou BUSINESS)][] et la **taille maximale** de votre base de données. Choisissez le **classement**, puis sélectionnez **NEW SQL Database server**. Cliquez sur la flèche située en bas de la boîte de dialogue.

    ![Renseigner les paramètres de la base de données SQL][]

5.  Entrez le nom et le mot de passe de l'administrateur (et confirmez ce dernier), choisissez la région dans laquelle sera créé le serveur de base de données SQL, puis cochez la case **Allow Azure Services to access the server**

    ![Créer un serveur de base de données SQL][]

    Lorsque le site web est créé, le texte **La création du site Web 'nom\_du\_site' s'est terminée correctement** apparaît. Vous pouvez maintenant activer la publication Git.

6.  Cliquez sur le nom du site web affiché dans la liste des sites web pour ouvrir le tableau de bord Démarrage rapide du site web.

    ![Ouvrir le tableau de bord du site Web][]

7.  En bas de la page de démarrage rapide, cliquez sur **Configurer la publication Git**.

    ![Configurer la publication Git][]

8.  Pour activer la publication Git, vous devez fournir un nom d'utilisateur et un mot de passe. Notez le nom d'utilisateur et le mot de passe que vous créez (si vous avez déjà configuré un référentiel Git, ignorez cette étape).

    ![Créer les informations d'identification de publication][]

    La configuration du référentiel prend quelques secondes.

9.  Lorsque votre référentiel est prêt, les instructions de publication de vos fichiers d'application s'affichent dans le référentiel. Notez ces instructions : elles vous seront utiles plus tard.

    ![Instructions Git][]

## Obtention des informations de connexion à la base de données SQL

Pour vous connecter à l'instance de base de données SQL exécutée sur Sites Web Azure, vous avez besoin des informations de connexion. Procédez comme suit pour les obtenir :

1.  Dans le portail de gestion Azure, cliquez sur **LINKED RESOURCES**, puis sur le nom de la base de données.

    ![Ressources liées][]

2.  Cliquez sur **Afficher les chaînes de connexion**.

    ![Chaîne de connexion][]

3.  Dans la boîte de dialogue qui s'affiche, sous la section **ODBC**, notez la chaîne de connexion. Elle vous sera utile par la suite.

## Conception de la table de tâches

Pour créer la table de base de données utilisée pour stocker les éléments pour l'application de liste de tâches, procédez comme suit :

1.  Dans le portail de gestion Azure, sélectionnez votre base de données SQL, puis cliquez sur **MANAGE** en bas de la page. Si un message s'affiche pour indiquer que l'adresse IP actuelle n'est pas incluse dans les règles de pare-feu, sélectionnez **OK** pour ajouter l'adresse IP.

    ![bouton de gestion][]

2.  Connectez-vous avec l'identifiant et le mot de passe utilisés précédemment lors de la création du serveur de base de données.

    ![connexion pour la gestion de base de données][]

3.  En bas à gauche de la page, sélectionnez **Design**, puis **New Table**.

    ![Nouvelle table][]

4.  Indiquez « tasks » pour le **nom de table**, puis activez la case **Is Identity?** pour la colonne **ID**.

    ![nom de table défini sur tasks avec case identity activée][]

5.  Remplacez **Column1** par **name** et **Column2** par **category**. Ajoutez deux nouvelles colonnes en cliquant sur le bouton **Add column**. La première nouvelle colonne doit être nommée **created** et contenir une **date**. La seconde doit être nommée **completed** et contenir un **bit**. Pour ces deux colonnes, la case **Is Required?** doit être activée.

    ![conception de table terminée][]

6.  Cliquez sur le bouton **Enregistrer** pour enregistrer les modifications apportées à la table. Vous pouvez maintenant fermer la page de gestion de base de données SQL.

## Installation des modules et création de la structure

Dans cette section, vous allez créer une application Node et utiliser npm pour ajouter des packages de module. Pour l'application de liste de tâches, vous devez utiliser les modules [express][] and [node-sqlserver][]. Le module Express fournit une infrastructure Model View Controller pour node, tandis que le serveur node-sqlserver assure la connexion vers la base de données SQL Azure.

### Installation express et création de la structure

1.  Dans la ligne de commande, accédez au répertoire **tasklist**. Si le répertoire **tasklist** n'existe pas, créez-le.

2.  Entrez la commande suivante pour installer Express.

        npm install express -g

    <div class="dev-callout">
<strong>Remarque</strong>
<p>L'emploi du param&egrave;tre '-g' sur certains syst&egrave;mes d'exploitation peut entra&icirc;ner l'erreur suivante&nbsp;: <strong>Error: EPERM, chmod '/usr/local/bin/express'</strong> et une instruction demandant que le compte soit ex&eacute;cut&eacute; en tant qu'administrateur. Si cette erreur survient, utilisez la commande <strong>sudo</strong> pour ex&eacute;cuter npm avec des privil&egrave;ges plus &eacute;lev&eacute;s.</p>
</div>

    Le résultat de cette commande doit ressembler à ceci :

        express@2.5.9 /usr/local/lib/node_modules/express
        ├── mime@1.2.4
        ├── mkdirp@0.3.0
        ├── qs@0.4.2
        └── connect@1.8.7

    <div class="dev-callout">
<strong>Remarque</strong>
<p>Avec le param&egrave;tre '-g', le module express est install&eacute; dans sa globalit&eacute;. Cela permet d'acc&eacute;der &agrave; la commande <strong>express</strong> pour g&eacute;n&eacute;rer un mod&egrave;le automatique de site web sans devoir taper d'autres informations de chemin d'acc&egrave;s.</p>
</div>

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

### Installation de modules supplémentaires

1.  Dans la ligne de commande, remplacez le répertoire par le dossier **tasklist**, puis entrez les commandes suivantes pour installer les modules décrits dans le fichier **package.json** :

        npm install

    Le résultat de cette commande doit ressembler à ceci :

        express@2.5.8 ./node_modules/express
        ├── mime@1.2.4
        ├── qs@0.4.2
        ├── mkdirp@0.3.0
        └── connect@1.8.7
        jade@0.26.0 ./node_modules/jade
        ├── commander@0.5.2
        └── mkdirp@0.3.0

    Ceci permet d'installer tous les modules requis par Express.

2.  Utilisez ensuite la commande suivante pour ajouter le module nconf. Ce module permet à l'application de lire la chaîne de connexion à la base de données à partir d'un fichier de configuration.

    npm install nconf -save

3.  Téléchargez ensuite la version binaire du pilote Microsoft SQL Server pour Node.JS dans le [centre de téléchargement][].

4.  Procédez à l'extraction de l'archive dans le répertoire **tasklist\\node\_modules**.

5.  Exécutez le fichier **msnodesql-install.cmd** dans le répertoire **tasklist\\node\_modules**. Cette opération permet de créer un sous-répertoire **msnodesql** sous **node\_modules** et de déplacer les fichiers du pilote dans la nouvelle structure de répertoires.

6.  Supprimez le fichier **msnodesql-install.cmd** ; il ne vous sera plus utile.

## Utilisation de la base de données SQL dans une application node

Cette section indique comment enrichir l'application de base créée via la commande **express** en modifiant l'élément **app.js** existant et en créant un fichier **index.js** pour utiliser la base de données créée précédemment.

### Modification du contrôleur

1.  Dans le répertoire **tasklist/routes**, ouvrez le fichier **index.js** dans un éditeur de texte.

2.  Remplacez le code présent dans le fichier **index.js** par le code suivant. Les modules msnodesql et nconf sont alors chargés. nconf est ensuite utilisé pour charger la chaîne de connexion depuis une variable d'environnement nommée **SQL\_CONN** ou une valeur **SQL\_CONN** dans le fichier **config.json**.

        var sql = require('msnodesql')
            , nconf = require('nconf');

        nconf.env()
             .file({ file: 'config.json' });
        var conn = nconf.get("SQL_CONN");

3.  Complétez le fichier **index.js** en ajoutant les méthodes **index** et **updateItem**. La méthode **index** renvoie toutes les tâches non terminées de la base de données, tandis que **updateItem** marque toutes les tâches sélectionnées comme terminées.

        exports.index = function(req, res) {
            var select = "select * from tasks where completed = 0";
            sql.query(conn, select, function(err, items) {
                if(err)
                    throw err;
                res.render('index', { title: 'My ToDo List ', tasks: items });
            });
        };

        exports.updateItem = function(req, res) {
            var item = req.body.item;
            if(item) {
                var insert = "insert into tasks (name, category, created, completed) values (?, ?, GETDATE(), 0)";
                sql.query(conn, insert, [item.name, item.category], function(err) {
                    if(err)
                        throw err;
                    res.redirect('/');
                });
            } else {
                var completed = req.body.completed;
                if(!completed.forEach)
                    completed = [completed];
                var update = "update tasks set completed = 1 where id in (" + completed.join(",") + ")";
                sql.query(conn, update, function(err) {
                    if(err)
                        throw err;
                    res.redirect('/');
                });
            }
        }

4.  Enregistrez le fichier **index.js**.

### Modification de app.js

1.  Dans le répertoire **tasklist**, ouvrez le fichier **app.js** dans un éditeur de texte. Ce fichier a été créé ultérieurement à l'aide de la commande **express**.

2.  Dans le fichier app.js, faites défiler le contenu jusqu'au code suivant.

        app.configure('development', function(){
        app.use(express.errorHandler());
        });

3.  Insérez le code suivant :

        app.get('/', routes.index);
        app.post('/', routes.updateItem);

 Une nouvelle route est ajoutée vers la méthode **updateItem** ajoutée précédemment dans le fichier **index.js**.

       		
3.  Enregistrez le fichier **app.js**.

### Modification de la vue d'index

1.  Passez dans le répertoire **views** et ouvrez le fichier **index.jade** dans un éditeur de texte.

2.  Remplacez le contenu du fichier **index.jade** par le code qui suit. Ce code définit la vue pour l'affichage des tâches existantes, ainsi qu'un formulaire pour l'ajout de nouvelles tâches et pour marquer les tâches existantes comme terminées.

        h1= title
        br

        form(action="/", method="post")
          table(class="table table-striped table-bordered")
            thead
              tr
                td Name
                td Category
                td Date
                td Complete
            tbody
            each task in tasks
              tr
                td #{task.name}
                td #{task.category}
                td #{task.created}
                td
                  input(type="checkbox", name="completed", value="#{task.ID}", checked=task.completed == 1)
          button(type="submit", class="btn") Update tasks
        hr

        form(action="/", method="post", class="well")
          label Item Name:
          input(name="item[name]", type="textbox")
          label Item Category:
          input(name="item[category]", type="textbox")
          br
          button(type="submit", class="btn") Add Item

3.  Fermez et enregistrez le fichier **index.jade**.

### Modification de la disposition générale

Le fichier **layout.jade** du répertoire **views** sert de modèle global aux autres fichiers **.jade**. Dans cette étape, vous allez le modifier pour utiliser [Twitter Bootstrap][], qui est un kit de ressources qui facilite la conception d'un site web bien présenté.

1.  Téléchargez les fichiers du [Twitter Bootstrap][1], puis procédez à l'extraction. Copiez le fichier **bootstrap.min.css** du dossier **bootstrap\\css** vers le répertoire **public\\stylesheets** de votre application de liste de tâches.

2.  Dans le dossier **views**, ouvrez **layout.jade** dans votre éditeur de texte et remplacez son contenu par le code suivant :

        !!!html
        html
          head
            title= title
            meta(http-equiv='X-UA-Compatible', content='IE=10')
            link(rel='stylesheet', href='/stylesheets/style.css')
            link(rel='stylesheet', href='/stylesheets/bootstrap.min.css')
          body(class='app')
            div(class='navbar navbar-fixed-top')
              .navbar-inner
                .container
                  a(class='brand', href='/') My Tasks
            .container!= body

3.  Enregistrez le fichier **layout.jade**.

### Création du fichier de configuration

Le fichier **config.json** contient la chaîne de connexion utilisée pour établir la connexion vers la base de données SQL. Il est consulté par le fichier **index.js** lors de l'exécution. Procédez comme suit pour créer ce fichier :

1.  Dans le répertoire **tasklist**, créez un fichier nommé **config.json**, puis ouvrez-le dans un éditeur de texte.

2.  Le contenu du fichier **config.json** doit ressembler au suivant :

        {
          "SQL_CONN" : "connection_string"
        }

    Remplacez **connection\_string** par la valeur de chaîne de connexion ODBC renvoyée précédemment.

3.  Enregistrez le fichier.

## Exécution locale de l'application

Pour tester l'application sur votre machine locale, procédez comme suit :

1.  Dans la ligne de commande, accédez au répertoire **tasklist**.

2.  Utilisez la commande suivante pour lancer l'application en local :

        node app.js

3.  Ouvrez un navigateur web et accédez à l'adresse <http://127.0.0.1:3000>. Une page web semblable à la suivante doit s'afficher :

    ![Une page Web affiche une liste de tâches vide.][]

4.  Utilisez les champs **Item Name** et **Item Category** pour entrer les informations, puis cliquez sur **Add item**.

5.  La page doit se mettre à jour et afficher l'élément dans la liste ToDo.

    ![Image du nouvel élément dans la liste de tâches][]

6.  Pour terminer une tâche, activez simplement la case à cocher dans la colonne Complete, puis cliquez sur **Update tasks**.

7.  Pour interrompre le processus node, accédez à la ligne de commande, puis appuyez sur les touches **CTRL** et **C**.

## Déploiement de votre application dans Azure

Dans cette section, vous allez utiliser les étapes de déploiement reçues après la création du site web pour publier votre application sur Azure.

### Publication de l'application

1.  Dans la ligne de commande, remplacez les répertoires par le répertoire **tasklist** si vous ne vous y trouvez pas déjà.

2.  Utilisez la commande suivante pour initialiser un référentiel git local pour votre application. Ajoutez-y les fichiers d'application, puis publiez les fichiers sur Azure.

        git init
        git add .
        git commit -m "adding files"
        git remote add azure [URL for remote repository]
        git push azure master

    À la fin du déploiement, une déclaration similaire à la suivante doit s'afficher :

        To https://username@tabletasklist.azurewebsites.net/TableTasklist.git
         * [new branch]      master -> master

3.  Une fois la publication terminée, accédez à **http://[nom du site].azurewebsites.net/** pour afficher votre application.

### Passage à une variable d'environnement

Précédemment, nous avons implémenté un code qui recherche une variable d'environnement **SQL\_CONN** pour la chaîne de connexion ou charge la valeur du fichier **config.json**. Dans les étapes suivantes, vous allez créer une paire clé/valeur dans la configuration de votre site web, auxquelles l'application accède réellement via une variable d'environnement.

1.  Dans le portail de gestion Azure, cliquez sur **Sites Web**, puis sélectionnez votre site web.

    ![Ouvrir le tableau de bord du site Web][]

2.  Cliquez sur **CONFIGURE**, puis recherchez la section **app settings** de la page.

    ![configurer le lien][]

3.  Dans la section **app settings**, entrez **SQL\_CONN** dans le champ **KEY**. Entrez également la chaîne de connexion ODBC dans le champ **VALUE**. Cliquez ensuite sur la coche.

    ![paramètres d'application][]

4.  Pour finir, cliquez sur l'icône **SAVE** en bas de la page pour appliquer cette modification à l'environnement d'exécution.

    ![enregistrer les paramètres d'application][]

5.  Dans la ligne de commande, remplacez les répertoires par le répertoire **tasklist**, puis entrez la commande suivante pour supprimer le fichier **config.json** :

        git rm config.json
        git commit -m "Removing config file"

6.  Exécutez les commandes suivantes pour déployer les modifications sur Azure :

        git push azure master

Une fois les modifications déployées sur Azure, votre application Web doit continuer à fonctionner, car la chaîne de connexion est lue depuis l'entrée **app settings**. Pour vous en assurer, remplacez la valeur de l'entrée **SQL\_CONN** dans **app settings** par une valeur non valide. Une fois la valeur enregistrée, le site web doit échouer en raison de la chaîne de connexion non valide.

## Étapes suivantes

* [Application Web Node.js avec MongoDB]

* [Application Web Node.js avec stockage de tables]

## Ressources supplémentaires

[Outil en ligne de commande Azure pour Mac et Linux]

[Création et déploiement d'une application Node.js vers Sites Web Azure]: /fr-fr/develop/nodejs/tutorials/create-a-website-(mac)/
[Publication sur les sites Web Azure avec Git]: /fr-fr/develop/nodejs/common-tasks/publishing-with-git/
[Centre de développement Azure]: /fr-fr/develop/nodejs/
[Application web Node.js avec stockage de tables]: /fr-fr/develop/nodejs/tutorials/web-site-with-storage/
[node]: http://nodejs.org
[Git]: http://git-scm.com
[Une page Web avec une liste de tâches vide]: ./media/sql-database-nodejs-use-web-site/sql_todo_final.png
[Microsoft SQL Server 2012 Feature Pack]: http://www.microsoft.com/fr-fr/download/details.aspx?id=29065
[portail de gestion Azure]: https://manage.windowsazure.com/
[Créer un site Web Azure]: ./media/sql-database-nodejs-use-web-site/new_website.jpg
[Création personnalisée d'un site Web]: ./media/sql-database-nodejs-use-web-site/custom_create.png
[Indiquer les détails du site Web]: ./media/sql-database-nodejs-use-web-site/website_details_sqlazure.jpg
[(WEB ou BUSINESS)]: http://msdn.microsoft.com/fr-fr/library/windowsazure/ee621788.aspx
[Renseigner les paramètres de la base de données SQL]: ./media/sql-database-nodejs-use-web-site/database_settings.jpg
[Créer un serveur de base de données SQL]: ./media/sql-database-nodejs-use-web-site/create_server.jpg
[Ouvrir le tableau de bord du site Web]: ./media/sql-database-nodejs-use-web-site/go_to_dashboard.png
[Configurer la publication Git]: ./media/sql-database-nodejs-use-web-site/setup_git_publishing.png
[Créer les informations d'identification de publication]: ./media/sql-database-nodejs-use-web-site/git-deployment-credentials.png
[Instructions Git]: ./media/sql-database-nodejs-use-web-site/git-instructions.png
[Ressources liées]: ./media/sql-database-nodejs-use-web-site/linked_resources.jpg
[Chaîne de connexion]: ./media/sql-database-nodejs-use-web-site/connection_string.jpg
[bouton de gestion]: ./media/sql-database-nodejs-use-web-site/sql-manage.png
[connexion pour la gestion de base de données]: ./media/sql-database-nodejs-use-web-site/sqlazurelogin.png
[Nouvelle table]: ./media/sql-database-nodejs-use-web-site/new-table.png
[nom de table défini sur tasks avec case identity activée]: ./media/sql-database-nodejs-use-web-site/table-name-identity.png
[conception de table terminée]: ./media/sql-database-nodejs-use-web-site/table-columns.png
[express]: http://expressjs.com
[node-sqlserver]: https://github.com/WindowsAzure/node-sqlserver
[centre de téléchargement]: http://www.microsoft.com/fr-fr/download/details.aspx?id=29995
[Twitter Bootstrap]: https://github.com/twbs/bootstrap
[1]: http://getbootstrap.com/
[Une page Web affiche une liste de tâches vide.]: ./media/sql-database-nodejs-use-web-site/sql_todo_empty.png
[Image du nouvel élément dans la liste de tâches]: ./media/sql-database-nodejs-use-web-site/sql_todo_list.png
[configurer le lien]: ./media/sql-database-nodejs-use-web-site/sql-task-configure.png
[paramètres d'application]: ./media/sql-database-nodejs-use-web-site/appsettings.png
[enregistrer les paramètres d'application]: ./media/sql-database-nodejs-use-web-site/savebutton.png
[Application Web Node.js avec MongoDB]: ../store-mongolab-web-sites-nodejs-store-data-mongodb/
[Publication sur les sites Web Azure avec Git]: ../CommonTasks/publishing-with-git
[Outil en ligne de commande Azure pour Mac et Linux]: /fr-fr/develop/nodejs/how-to-guides/command-line-tools/