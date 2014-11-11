<properties linkid="develop-php-website-with-sql-database-and-git" urlDisplayName="Web w/ SQL + Git" pageTitle="PHP website with SQL Database and Git - Azure tutorial" metaKeywords="" description="A tutorial that demonstrates how to create a PHP website that stores data in SQL Database and use Git deployment to Azure." metaCanonical="" services="web-sites,sql-database" documentationCenter="PHP" title="Create a PHP website with a SQL Database and deploy using Git" authors="robmcm" solutions="" manager="wpickett" editor="mollybos" scriptId="" videoId="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="PHP" ms.topic="article" ms.date="01/01/1900" ms.author="robmcm" />

# Création d'un site web PHP comportant une base de données SQL et déploiement avec Git

Ce didacticiel vous montre comment créer un site web PHP avec une base de données SQL Azure et comment le déployer en utilisant Git. Pour ce didacticiel, [PHP][PHP], [SQL Server Express][SQL Server Express], les [pilotes Microsoft SQL Server pour PHP][pilotes Microsoft SQL Server pour PHP], un serveur Web et [Git][Git] doivent être installés sur votre ordinateur. ﻿Une fois que vous aurez terminé ce guide, vous disposerez d'un site web contenant une base de données PHP-SQL qui s'exécute dans Azure.

> [WACOM.NOTE]
> Vous pouvez installer et configurer PHP, SQL Server Express, les pilotes Microsoft SQL Server pour PHP, ainsi qu'Internet Information Services (IIS) via [Microsoft Web Platform Installer][Microsoft Web Platform Installer].

Vous apprendrez à effectuer les opérations suivantes :

-   création d'un site web Azure et d'une base de données SQL à l'aide du portail de gestion Azure Comme PHP est activé par défaut dans Sites Web Azure, aucune action particulière n'est requise pour exécuter votre code PHP.
-   publication et republication de votre application dans Azure en utilisant Git.

En suivant ce didacticiel, vous allez générer une application Web d'inscription simple dans PHP. L'application est hébergée dans un site web Azure. Voici une capture d'écran de l'application terminée :

![Azure PHP Web Site][Azure PHP Web Site]

[WACOM.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

## Création d'un site web Azure et configuration de la publication Git

Suivez cette procédure pour créer un site web Azure et une base de données SQL :

1.  Connectez-vous au [portail de gestion Azure][portail de gestion Azure].
2.  Cliquez sur l'icône **Nouveau** dans le coin inférieur gauche du portail.
    ![Créer un site web Azure][Créer un site web Azure]

3.  Cliquez sur **Site Web**, puis sur **Création personnalisée**.

    ![Création personnalisée d'un site Web][Création personnalisée d'un site Web]

    Entrez une valeur pour **URL**, dans la liste déroulante **Base de données**, sélectionnez **Créer un serveur de base de données SQL**, puis sélectionnez un centre de données pour votre site web dans la liste déroulante **Région**. Cliquez sur la flèche située en bas de la boîte de dialogue.

    ![Entrer les détails du site Web][Entrer les détails du site Web]

4.  Entrez le **nom** de votre base de données, sélectionnez l'**édition** [(WEB ou BUSINESS)][(WEB ou BUSINESS)] et la **taille maximale** de votre base de données. Choisissez le **classement**, puis sélectionnez **NEW SQL Database server**. Cliquez sur la flèche située en bas de la boîte de dialogue.

    ![Renseigner les paramètres de la base de données SQL][Renseigner les paramètres de la base de données SQL]

5.  Entrez le nom et le mot de passe de l'administrateur (et confirmez ce dernier), choisissez la région dans laquelle sera créé le serveur de bases de données SQL, puis cochez la case `Allow Azure Services to access the server`.

    ![Créer un serveur de base de données SQL][Créer un serveur de base de données SQL]

    Lorsque le site web est créé, le texte **La création du site Web 'nom\_du\_site' s'est terminée correctement** apparaît. Vous pouvez maintenant activer la publication Git.

6.  Cliquez sur le nom du site web affiché dans la liste des sites web pour ouvrir le tableau de bord Démarrage rapide du site web.

    ![Ouvrir le tableau de bord du site Web][Ouvrir le tableau de bord du site Web]

7.  En bas de la page de démarrage rapide, cliquez sur **Set up deployment from source control**.

    ![Configurer la publication Git][Configurer la publication Git]

8.  À la question « Où est votre code source ? », sélectionnez **Référentiel Git local**, puis cliquez sur la flèche.

    ![où est votre code source][où est votre code source]

9.  Pour activer la publication Git, vous devez fournir un nom d'utilisateur et un mot de passe. Notez le nom d'utilisateur et le mot de passe que vous créez (si vous avez déjà configuré un référentiel Git, ignorez cette étape).

    ![Créer les informations d'identification de publication][Créer les informations d'identification de publication]

    La configuration du référentiel prend quelques secondes.

10. Lorsque votre référentiel est prêt, les instructions de publication de vos fichiers d'application s'affichent dans le référentiel. Notez ces instructions : elles vous seront utiles plus tard.

    ![Instructions Git][Instructions Git]

## Obtention des informations de connexion à la base de données SQL

Pour vous connecter à l'instance de base de données SQL exécutée sur Sites Web Azure, vous avez besoin de vos informations de connexion. Procédez comme suit pour les obtenir :

1.  Dans le portail de gestion Azure, cliquez sur **Linked Resources**, puis sur le nom de la base de données.

    ![Ressources liées][Ressources liées]

2.  Cliquez sur **Afficher les chaînes de connexion**.

    ![Chaîne de connexion][Chaîne de connexion]

3.  Dans la section **PHP** de la boîte de dialogue qui s'affiche, notez les valeurs des champs `SERVER`, `DATABASE` et `USERNAME`.

## Génération et test de votre application localement

L'application d'inscription est une simple application PHP qui vous permet de vous inscrire à un événement en entrant votre nom et votre adresse électronique. Les informations relatives aux précédents inscrits sont affichées dans un tableau. Les informations d'inscription sont stockées dans une instance de base de données SQL. L'application se compose de deux fichiers (dont le code est disponible ci-dessous pour un copier/coller) :

-   **index.php** : affiche un formulaire d'inscription et un tableau contenant les informations des inscrits.
-   **createtable.php** : crée la table de base de données SQL pour l'application. Ce fichier sera utilisé une seule fois.

Pour exécuter l'application en local, procédez comme suit : notez que ces étapes partent du principe que PHP, SQL Server Express et un serveur Web sont configurés sur votre machine locale, et que vous avez activé l'[extension PDO pour SQL Server][extension PDO pour SQL Server].

1.  Créez une base de données SQL Server nommée `registration`. Pour cela, utilisez l'invite de commandes `sqlcmd` avec ces commandes :

        >sqlcmd -S localhost\sqlexpress -U <local user name> -P <local password>
        1> create database registration
        2> GO   

2.  Dans le répertoire racine de votre serveur web, créez un dossier nommé `registration`, ainsi que deux fichiers à l'intérieur : un fichier nommé `createtable.php` et un autre nommé `index.php`.

3.  Ouvrez le fichier `createtable.php` dans un éditeur de texte ou un environnement de développement intégré (IDE), puis ajoutez le code suivant. Ce code permet de créer la table `registration_tbl` dans la base de données `registration`.

        <?php
        // DB connection info
        $host = "localhost\sqlexpress";
        $user = "user name";
        $pwd = "password";
        $db = "registration";
        try{
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
        echo "<h3>Table created.</h3>";
        ?>

    Vous devrez mettre à jour les valeurs des champs `$user` et `$pwd` avec votre nom d'utilisateur et votre mot de passe SQL Serveur locaux.

4.  Ouvrez un navigateur Web et accédez à **http://localhost/registration/createtable.php**. La table `registration_tbl` est créée dans la base de données.

5.  Ouvrez le fichier **index.php** dans un éditeur de texte ou un IDE et ajoutez les codes HTML et CSS de base pour la page (le code PHP sera ajouté plus tard).

        <html>
        <head>
        <Title>Registration Form</Title>
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
        <h1>Register here!</h1>
        <p>Fill in your name and email address, then click <strong>Submit</strong> to register.</p>
        <form method="post" action="index.php" enctype="multipart/form-data" >
              Name  <input type="text" name="name" id="name"/></br>
              Email <input type="text" name="email" id="email"/></br>
              <input type="submit" name="submit" value="Submit" />
        </form>
        <?php

        ?>
        </body>
        </html>

6.  Ajoutez le code PHP dans les balises PHP pour la connexion à la base de données.

        // DB connection info
        $host = "localhost\sqlexpress";
        $user = "user name";
        $pwd = "password";
        $db = "registration";
        // Connect to database.
        try {
            $conn = new PDO( "sqlsrv:Server= $host ; Database = $db ", $user, $pwd);
            $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );
        }
        catch(Exception $e){
            die(var_dump($e));
        }

    Là encore, vous devrez mettre à jour les valeurs des champs `$user` et `$pwd` avec votre nom d'utilisateur et votre mot de passe MySQL locaux.

7.  ﻿À la suite du code de connexion à la base de données, ajoutez le code pour l'insertion des informations d'inscription à la base de données.

        if(!empty($_POST)) {
        try {
            $name = $_POST['name'];
            $email = $_POST['email'];
            $date = date("Y-m-d");
            // Insert data
            $sql_insert = "INSERT INTO registration_tbl (name, email, date) 
                           VALUES (?,?,?)";
            $stmt = $conn->prepare($sql_insert);
            $stmt->bindValue(1, $name);
            $stmt->bindValue(2, $email);
            $stmt->bindValue(3, $date);
            $stmt->execute();
        }
        catch(Exception $e) {
            die(var_dump($e));
        }
        echo "<h3>Your're registered!</h3>";
        }

8.  Pour finir, à la suite du code précédent, ajoutez le code de récupération des données de la base de données.

        $sql_select = "SELECT * FROM registration_tbl";
        $stmt = $conn->query($sql_select);
        $registrants = $stmt->fetchAll(); 
        if(count($registrants) > 0) {
            echo "<h2>People who are registered:</h2>";
            echo "<table>";
            echo "<tr><th>Name</th>";
            echo "<th>Email</th>";
            echo "<th>Date</th></tr>";
            foreach($registrants as $registrant) {
                echo "<tr><td>".$registrant['name']."</td>";
                echo "<td>".$registrant['email']."</td>";
                echo "<td>".$registrant['date']."</td></tr>";
            }
            echo "</table>";
        } else {
            echo "<h3>No one is currently registered.</h3>";
        }

Vous pouvez maintenant accéder à **http://localhost/inscription/index.php** pour tester l'application.

## Publication de votre application

Une fois votre application testée localement, vous pouvez la publier vers votre site web Azure en utilisant Git. Cependant, vous devez d'abord mettre à jour les informations de connexion à la base de données dans l'application. En utilisant les informations de connexion à la base de données obtenues précédemment (dans la section **Obtention des informations de connexion à la base de données SQL**), mettez à jour les informations suivantes dans **les deux** fichiers `createdatabase.php` et `index.php` avec les valeurs appropriées :

    // DB connection info
    $host = "tcp:<value of SERVER>";
    $user = "<value of USERNAME>@<server ID>";
    $pwd = "<your password>";
    $db = "<value of DATABASE>";

> [WACOM.NOTE]
> Dans `$host`, la valeur de SERVER doit être précédée de `tcp:`. La valeur de `$user` représente la concaténation de la valeur de USERNAME, de '@' et de votre ID de serveur. Ce dernier est constitué des 10 premiers caractères de la valeur SERVER.

Tout est prêt pour configurer la publication Git et publier l'application.

> [WACOM.NOTE]
> Cette procédure est identique à celle indiquée à la fin de la section « Création d'un site web Azure et configuration de la publication Git » précédente.

1.  Ouvrez GitBash (ou un terminal, si Git est dans votre `PATH`), remplacez les répertoires du répertoire racine de votre application, puis exécutez les commandes suivantes :

        git init
        git add .
        git commit -m "initial commit"
        git remote add azure [URL for remote repository]
        git push azure master

    Vous êtes invité à entrer le mot de passe que vous avez créé précédemment.

2.  Accédez à **http://[nom\_site].azurewebsites.net/createtable.php** pour créer la table MySQL pour l'application.
3.  Accédez à **http://[nom\_site].azurewebsites.net/index.php** pour commencer à utiliser l'application.

Après la publication de votre application, vous pouvez y apporter des modifications, puis utiliser Git pour les publier.

## Publication des modifications apportées à votre application

Pour publier des modifications apportées à votre application, procédez comme suit :

1.  Modifiez votre application en local.
2.  Ouvrez GitBash (ou un terminal, si Git est dans votre `PATH`), remplacez les répertoires du répertoire racine de votre application, puis exécutez les commandes suivantes :

        git add .
        git commit -m "comment describing changes"
        git push azure master

    Vous êtes invité à entrer le mot de passe que vous avez créé précédemment.

3.  Accédez à **http://[nom\_site].azurewebsites.net/index.php** pour afficher vos modifications.

  [PHP]: http://www.php.net/manual/en/install.php
  [SQL Server Express]: http://www.microsoft.com/fr-fr/download/details.aspx?id=29062
  [pilotes Microsoft SQL Server pour PHP]: http://www.microsoft.com/fr-fr/download/details.aspx?id=20098
  [Git]: http://git-scm.com/
  [Microsoft Web Platform Installer]: http://www.microsoft.com/web/downloads/platform.aspx
  [Azure PHP Web Site]: ./media/web-sites-php-sql-database-deploy-use-git/running_app_3.png
  [create-account-and-websites-note]: ../includes/create-account-and-websites-note.md
  [portail de gestion Azure]: https://manage.windowsazure.com/
  [Créer un site web Azure]: ./media/web-sites-php-sql-database-deploy-use-git/new_website.jpg
  [Création personnalisée d'un site Web]: ./media/web-sites-php-sql-database-deploy-use-git/custom_create.png
  [Entrer les détails du site Web]: ./media/web-sites-php-sql-database-deploy-use-git/website_details_sqlazure.jpg
  [Renseigner les paramètres de la base de données SQL]: ./media/web-sites-php-sql-database-deploy-use-git/database_settings.jpg
  [Créer un serveur de base de données SQL]: ./media/web-sites-php-sql-database-deploy-use-git/create_server.jpg
  [Ouvrir le tableau de bord du site Web]: ./media/web-sites-php-sql-database-deploy-use-git/go_to_dashboard.png
  [Configurer la publication Git]: ./media/web-sites-php-sql-database-deploy-use-git/setup_git_publishing.png
  [où est votre code source]: ./media/web-sites-php-sql-database-deploy-use-git/where_is_code.png
  [Créer les informations d'identification de publication]: ./media/web-sites-php-sql-database-deploy-use-git/git-deployment-credentials.png
  [Instructions Git]: ./media/web-sites-php-sql-database-deploy-use-git/git-instructions.png
  [Ressources liées]: ./media/web-sites-php-sql-database-deploy-use-git/linked_resources.jpg
  [Chaîne de connexion]: ./media/web-sites-php-sql-database-deploy-use-git/connection_string.jpg
  [extension PDO pour SQL Server]: http://php.net/pdo_sqlsrv
