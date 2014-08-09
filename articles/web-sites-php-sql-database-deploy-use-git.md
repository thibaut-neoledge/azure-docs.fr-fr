<properties linkid="develop-php-website-with-sql-database-and-git" urlDisplayName="Web w/ SQL + Git" pageTitle="PHP web site with SQL Database and Git - Azure tutorial" metaKeywords="" description="A tutorial that demonstrates how to create a PHP web site that stores data in SQL Database and use Git deployment to Azure." metaCanonical="" services="web-sites,sql-database" documentationCenter="PHP" title="Create a PHP web site with a SQL Database and deploy using Git" authors="waltpo" solutions="" manager="" editor="mollybos" />

Création d'un site Web PHP comportant une base de données SQL et déploiement avec Git
=====================================================================================

Ce didacticiel vous montre comment créer un site Web PHP avec une base de données SQL Azure et comment le déployer en utilisant Git. Pour ce didacticiel, [PHP](http://www.php.net/manual/en/install.php), [SQL Server Express](http://www.microsoft.com/en-us/download/details.aspx?id=29062), les [pilotes Microsoft SQL Server pour PHP](http://www.microsoft.com/en-us/download/details.aspx?id=20098), un serveur Web et [Git](http://git-scm.com/) doivent être installés sur votre ordinateur. ?Une fois que vous aurez terminé ce guide, vous disposerez d'un site Web contenant une base de données PHP-SQL qui s'exécute dans Azure.

> [WACOM.NOTE] Vous pouvez installer et configurer PHP, SQL Server Express, les pilotes Microsoft SQL Server pour PHP, ainsi qu'Internet Information Services (IIS) via le [Microsoft Web Platform Installer](http://www.microsoft.com/web/downloads/platform.aspx).

Vous apprendrez à effectuer les opérations suivantes :

-   création d'un site Web Azure et d'une base de données SQL à l'aide du portail de gestion Azure (comme PHP est activé par défaut dans Sites Web Azure, aucune action particulière n'est requise pour exécuter votre code PHP) ;
-   publication et republication de votre application dans Azure en utilisant Git.

En suivant ce didacticiel, vous allez générer une application Web d'inscription simple dans PHP. L'application est hébergée dans un site Web Azure. Voici une capture d'écran de l'application terminée :

![Site Web PHP Azure](./media/web-sites-php-sql-database-deploy-use-git/running_app_3.png)

[WACOM.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

Création d'un site Web Azure et configuration de la publication Git
-------------------------------------------------------------------

Suivez cette procédure pour créer un site Web Azure et une base de données SQL :

1.  Connectez-vous au [portail de gestion Azure](https://manage.windowsazure.com/).
2.  Cliquez sur l'icône **New** dans le coin inférieur gauche du portail.

	![Créer un site Web Azure](./media/web-sites-php-sql-database-deploy-use-git/new_website.jpg)

3.  Cliquez sur **Site Web**, puis sur **Custom Create**.

    ![Création personnalisée d'un site Web](./media/web-sites-php-sql-database-deploy-use-git/custom_create.png)

    Entrez une valeur pour **URL**, dans la liste déroulante **Database**, sélectionnez **Create a New SQL Database**, puis sélectionnez un centre de données pour votre site Web dans la liste déroulante **Region**. Cliquez sur la flèche située en bas de la boîte de dialogue.

    ![Entrer les détails du site Web](./media/web-sites-php-sql-database-deploy-use-git/website_details_sqlazure.jpg)

4.  Entrez le **nom** de votre base de données, sélectionnez l'**édition** [(WEB ou BUSINESS)](http://msdn.microsoft.com/fr-fr/library/windowsazure/ee621788.aspx) et la **taille maximale** de votre base de données. Choisissez le **classement**, puis sélectionnez **NEW SQL Database server**. Cliquez sur la flèche située en bas de la boîte de dialogue.

    ![Renseigner les paramètres de la base de données SQL](./media/web-sites-php-sql-database-deploy-use-git/database_settings.jpg)

5.  Entrez le nom et le mot de passe de l'administrateur (et confirmez ce dernier), choisissez la région dans laquelle sera créé le serveur de base de données SQL, puis activez la case `Allow Azure Services to access the server`

    ![Créer un serveur de base de données SQL](./media/web-sites-php-sql-database-deploy-use-git/create_server.jpg)

    Lorsque le site Web est créé, le texte **Creation of Web Site "[SITENAME]" completed successfully** apparaît. Vous pouvez maintenant activer la publication Git.

6.  Cliquez sur le nom du site Web affiché dans la liste de sites Web pour ouvrir le tableau de bord Démarrage rapide du site Web.

    ![Ouvrir le tableau de bord du site Web](./media/web-sites-php-sql-database-deploy-use-git/go_to_dashboard.png)

7.  En bas de la page de démarrage rapide, cliquez sur **Set up deployment from source control**.

    ![Configurer la publication Git](./media/web-sites-php-sql-database-deploy-use-git/setup_git_publishing.png)

8.  À la question « Où est votre code source ? », sélectionnez **Local Git repository**, puis cliquez sur la flèche.

    ![où est votre code source](./media/web-sites-php-sql-database-deploy-use-git/where_is_code.png)

9.  Pour activer la publication Git, vous devez fournir un nom d'utilisateur et un mot de passe. Notez le nom d'utilisateur et le mot de passe que vous créez (si vous avez déjà configuré un référentiel Git, ignorez cette étape).

    ![Créer les informations d'identification de publication](./media/web-sites-php-sql-database-deploy-use-git/git-deployment-credentials.png)

    La configuration du référentiel prend quelques secondes.

10. Lorsque votre référentiel est prêt, les instructions de publication de vos fichiers d'application s'affichent dans le référentiel. Notez ces instructions : elles vous seront utiles plus tard.

    ![Instructions Git](./media/web-sites-php-sql-database-deploy-use-git/git-instructions.png)

Obtention des informations de connexion à la base de données SQL
----------------------------------------------------------------

Pour vous connecter à l'instance de base de données SQL exécutée sur Sites Web Azure, vous avez besoin de vos informations de connexion. Procédez comme suit pour les obtenir :

1.  Dans le portail de gestion Azure, cliquez sur **Linked Resources**, puis sur le nom de la base de données.

    ![Ressources liées](./media/web-sites-php-sql-database-deploy-use-git/linked_resources.jpg)

2.  Cliquez sur **Afficher les chaînes de connexion**.

    ![Chaîne de connexion](./media/web-sites-php-sql-database-deploy-use-git/connection_string.jpg)

3.  Dans la section **PHP** de la boîte de dialogue qui s'affiche, notez les valeurs des champs `SERVER`, `DATABASE` et `USERNAME`.

Génération et test de votre application localement
--------------------------------------------------

L'application d'inscription est une simple application PHP qui vous permet de vous inscrire à un événement en entrant votre nom et votre adresse électronique. Les informations relatives aux précédents inscrits sont affichées dans un tableau. Les informations d'inscription sont stockées dans une instance de base de données SQL. L'application se compose de deux fichiers (dont le code est disponible ci-dessous pour un copier/coller) :

-   **index.php** : affiche un formulaire d'inscription et un tableau contenant les informations des inscrits.
-   **createtable.php** : crée la table de base de données SQL pour l'application. Ce fichier sera utilisé une seule fois.

Pour exécuter l'application en local, procédez comme suit : notez que ces étapes partent du principe que PHP, SQL Server Express et un serveur Web sont configurés sur votre machine locale, et que vous avez activé l'[extension PDO pour SQL Server](http://php.net/pdo_sqlsrv).

1.  Créez une base de données SQL Server nommée `registration`. Pour cela, utilisez l'invite de commandes `sqlcmd` avec ces commandes :

         >sqlcmd -S localhost\sqlexpress -U <local user name> -P <local password>
         1> create database registration
         2> GO   

2.  Dans le répertoire racine de votre serveur Web, créez un dossier nommé `registration`, ainsi que deux fichiers à l'intérieur : un fichier nommé `createtable.php` et un autre nommé `index.php`.

3.  Ouvrez le fichier `createtable.php` dans un éditeur de texte ou un environnement de développement intégré (IDE), puis ajoutez le code suivant. Ce code permet de créer la table `registration_tbl` dans la base de données `registration`.

         <
         php
         // Informations de connexion de base de données
         $host = "localhost\sqlexpress";
         $user = "nom d'utilisateur";
         $pwd = "mot de passe";
         $db = "registration";
         try {
             $conn = new PDO( "sqlsrv:Server= $host ; Database = $db ", $user, $pwd);
             $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );
             $sql = "CREATE TABLE registration_tbl(
             id INT NOT NULL IDENTITY(1,1) 
             PRIMARY KEY(id),
             name VARCHAR(30),
             email VARCHAR(30),
             date DATE)";
             $conn->query($sql);
         }
         catch(Exception $e){
             die(print_r($e));
         }
         echo "<h3>Vous avez créé la table.</h3>";
         
         >

    Vous devrez mettre à jour les valeurs des champs `$user` et `$pwd` avec votre nom d'utilisateur et votre mot de passe SQL Serveur locaux.

4.  Ouvrez un navigateur Web et accédez à **http://localhost/registration/createtable.php**. La table `registration_tbl` est créée dans la base de données.

5.  Ouvrez le fichier **index.php** dans un éditeur de texte ou un IDE et ajoutez les codes HTML et CSS de base pour la page (le code PHP sera ajouté plus tard).

         <html>
         <head>
         <Title>Formulaire d'inscription</Title>
         <style type="text/css">
             body { background-color: #fff; border-top: solid 10px #000;
                 color: #333; font-size: .85em; margin: 20; padding: 20;
                 font-family: "Segoe UI", Verdana, Helvetica, Sans-Serif;
             }
             h1, h2, h3,{ color: #000; margin-bottom: 0; padding-bottom: 0; }
             h1 { font-size: 2em; }
             h2 { font-size: 1.75em; }
             h3 { font-size: 1.2em; }
             table { margin-top: 0.75em; }
             th { font-size: 1.2em; text-align: left; border: none; padding-left: 0; }
             td { padding: 0.25em 2em 0.25em 0em; border: 0 none; }
         </style>
         </head>
         <body>
         <h1>Inscrivez-vous ici</h1>
         <p>Entrez votre nom et votre adresse électronique, puis cliquez sur <strong>Envoyer</strong> pour vous inscrire.</p>
         <form method="post" action="index.php" enctype="multipart/form-data" >
               Nom  <input type="text" name="nom" id="nom"/></br>
               Adresse électronique <input type="text" name="adresse électronique" id="adresse électronique"/></br>
               <input type="submit" name="envoyer" value="Envoyer" />
         </form>
         <
         php

         
         >
         </body>
         </html>

6.  Ajoutez le code PHP dans les balises PHP pour la connexion à la base de données.

         // Informations de connexion à la base de données
         $host = "localhost\sqlexpress";
         $user = "nom d'utilisateur";
         $pwd = "mot de passe";
         $db = "registration";
         // Connectez-vous à la base de données.
         try {
             $conn = new PDO( "sqlsrv:Server= $host ; Database = $db ", $user, $pwd);
             $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );
         }
         catch(Exception $e){
             die(var_dump($e));
         }

    Vous devrez encore mettre à jour les valeurs des champs `$user` et `$pwd` avec votre nom d'utilisateur et votre mot de passe MySQL locaux.

7.  À la suite du code de connexion à la base de données, ajoutez le code pour l'insertion des informations d'inscription à la base de données.

         if(!empty($_POST)) {
         try {
             $name = $_POST['nom'];
             $email = $_POST['adresse électronique'];
             $date = date("Y-m-d");
             // Insertion des données
             $sql_insert = "INSERT INTO registration_tbl (nom, adresse électronique, date)  
                            VALUES (
         ,
         ,
         )";
             $stmt = $conn->prepare($sql_insert);
             $stmt->bindValue(1, $name);
             $stmt->bindValue(2, $email);
             $stmt->bindValue(3, $date);
             $stmt->execute();
         }
         catch(Exception $e){
             die(var_dump($e));
         }
         echo "<h3>Vous êtes inscrit !</h3>";
         }

8.  Pour finir, à la suite du code précédent, ajoutez le code de récupération des données de la base de données.

         $sql_select = "SELECT * FROM registration_tbl";
         $stmt = $conn->query($sql_select);
         $registrants = $stmt->fetchAll();  
         if(count($registrants) > 0) {
             echo "<h2>Personnes inscrites :</h2>";
             echo "<table>";
             echo "<tr><th>Nom</th>";
             echo "<th>Adresse électronique</th>";
             echo "<th>Date</th></tr>";
             foreach($registrants as $registrant) {
                 echo "<tr><td>".$registrant['nom']."</td>";
                 echo "<td>".$registrant['adresse électronique']."</td>";
                 echo "<td>".$registrant['date']."</td></tr>";
             }
            echo "</table>";
         } else {
             echo "<h3>Personne n'est inscrit pour le moment.</h3>";
         }

Vous pouvez maintenant accéder à **http://localhost/inscription/index.php** pour tester l'application.

Publication de votre application
--------------------------------

Une fois votre application testée en local, vous pouvez la publier vers votre site Web Azure en utilisant Git. Cependant, vous devez d'abord mettre à jour les informations de connexion à la base de données dans l'application. En utilisant les informations de connexion à la base de données obtenues précédemment (dans la section **Obtention des informations de connexion à la base de données SQL**), mettez à jour les informations suivantes dans **les deux** fichiers `createdatabase.php` et `index.php` avec les valeurs appropriées :

     // Informations de connexion à la base de données
    $host = "tcp:<value of SERVER>";
    $user = "<value of USERNAME>@<server ID>";
    $pwd = "<your password>";
    $db = "<value of DATABASE>";

> [WACOM.NOTE] Dans `$host`, la valeur de SERVER doit être précédée de `tcp:`. La valeur de `$user` représente la concaténation de la valeur de USERNAME, '@' et de votre ID de serveur. Ce dernier est constitué des 10 premiers caractères de la valeur SERVER.

Tout est prêt pour configurer la publication Git et publier l'application.

> [WACOM.NOTE] Cette procédure est identique à celle indiquée à la fin de la section « Création d'un site Web Azure et configuration de la publication Git » précédente.

1.  Ouvrez GitBash (ou un terminal, si Git est dans votre `PATH`), remplacez les répertoires du répertoire racine de votre application, puis exécutez les commandes suivantes :

         git init
         git add .
         git commit -m "initial commit"
         git remote add azure [URL de votre référentiel distant]
         git push azure master

    Vous êtes invité à entrer le mot de passe que vous avez créé précédemment.

2.  Accédez à **http://[nom du site].azurewebsites.net/createtable.php** pour créer la table MySQL pour l'application.
3.  Accédez à **http://[nom du site].azurewebsites.net/index.php** pour commencer à utiliser l'application.

Après la publication de votre application, vous pouvez y apporter des modifications, puis utiliser Git pour les publier.

Publication des modifications apportées à votre application
-----------------------------------------------------------

Pour publier des modifications apportées à votre application, procédez comme suit :

1.  Modifiez votre application en local.
2.  Ouvrez GitBash (ou un terminal, si Git est dans votre `PATH`), remplacez les répertoires du répertoire racine de votre application, puis exécutez les commandes suivantes :

         git add .
         git commit -m "commentaire décrivant les modifications"
         git push azure master

    Vous êtes invité à entrer le mot de passe que vous avez créé précédemment.

3.  Accédez à **http://[nom du site].azurewebsites.net/index.php** pour afficher vos modifications.

