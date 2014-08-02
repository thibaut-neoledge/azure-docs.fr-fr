<properties linkid="develop-php-website-with-mysql-and-ftp" urlDisplayName="Web w/ MySQL + FTP" pageTitle="PHP web site with MySQL and FTP - Azure tutorial" metaKeywords="" description="A tutorial that demonstrates how to create a PHP web site that stores data in MySQL and use FTP deployment to Azure." metaCanonical="" services="web-sites" documentationCenter="PHP" title="Create a PHP-MySQL Azure Web Site and Deploy Using FTP" authors="" solutions="" manager="" editor="" />

Création et déploiement d'un site Web Azure PHP-MySQL avec FTP
==============================================================

Ce didacticiel vous montre comment créer un site Web Azure PHP-MySQL et comment le déployer en utilisant FTP. Il part du principe que vous avez installé [PHP](http://www.php.net/manual/en/install.php), [MySQL](http://dev.mysql.com/doc/refman/5.6/en/installing.html), un serveur Web et un client FTP sur votre ordinateur. Les instructions de ce didacticiel s'appliquent à n'importe quel système d'exploitation, notamment Windows, Mac et Linux. À la fin de ce guide, vous disposerez d'un site Web PHP/MySQL exécuté dans Azure.

Vous apprendrez à effectuer les opérations suivantes :

-   Créer un site Web Azure et une base de données MySQL à l'aide du portail de gestion Azure (comme PHP est activé par défaut dans Sites Web Azure, aucune action particulière n'est requise pour exécuter votre code PHP) ;
-   publier votre application sur Azure avec FTP.

En suivant ce didacticiel, vous allez générer une application Web d'inscription simple dans PHP. L'application est hébergée dans un site Web Azure. Voici une capture d'écran de l'application terminée :

![Site Web PHP Azure](./media/web-sites-php-web-site-mysql-deploy-use-ftp/running_app_2.png)

[WACOM.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

Création d'un site Web Azure et configuration de la publication FTP
-------------------------------------------------------------------

Suivez cette procédure pour créer un site Web Azure et une base de données MySQL :

1.  Connectez-vous au [portail de gestion Azure](https://manage.windowsazure.com).
2.  Cliquez sur l'icône **+ New** dans le coin inférieur gauche du portail.

    ![Créer un site Web Azure](./media/web-sites-php-web-site-mysql-deploy-use-ftp/new_website.jpg)

3.  Cliquez sur **SITE WEB**, puis sur **CUSTOM CREATE**.

    ![Création personnalisée d'un site Web](./media/web-sites-php-web-site-mysql-deploy-use-ftp/custom_create.png)

    Entrez une valeur pour **URL**, dans la liste déroulante **BASE DE DONNÉES**, sélectionnez **Create a New MySQL Database**, puis sélectionnez un centre de données pour votre site Web dans la liste déroulante **RÉGION**. Cliquez sur la flèche située en bas de la boîte de dialogue.

    ![Entrer les détails du site Web](./media/web-sites-php-web-site-mysql-deploy-use-ftp/website_details.jpg)

4.  Entrez un **nom** pour votre base de données, sélectionnez un centre de données dans la liste déroulante **RÉGION**, puis activez la case à cocher qui indique que vous acceptez les conditions juridiques. Cliquez sur la coche située en bas de la boîte de dialogue.

    ![Créer une base de données MySQL](./media/web-sites-php-web-site-mysql-deploy-use-ftp/new_mysql_db.jpg)

    Lorsque le site Web est créé, le texte **Creation of Web Site ‘[SITENAME]’ completed successfully** apparaît. Vous pouvez maintenant activer la publication FTP.

5.  Cliquez sur le nom du site Web affiché dans la liste de sites Web pour ouvrir le tableau de bord **DÉMARRAGE RAPIDE** du site Web.

    ![Ouvrir le tableau de bord du site Web](./media/web-sites-php-web-site-mysql-deploy-use-ftp/go_to_dashboard.png)

6.  En bas de la page **DÉMARRAGE RAPIDE**, cliquez sur **Reset deployment credentials**.

    ![Réinitialiser les informations d'identification de déploiement](./media/web-sites-php-web-site-mysql-deploy-use-ftp/reset-deployment-credentials.png)

7.  Pour activer la publication FTP, vous devez fournir un nom d'utilisateur et un mot de passe. Notez le nom d'utilisateur et le mot de passe que vous créez

    ![Créer les informations d'identification de publication](./media/web-sites-php-web-site-mysql-deploy-use-ftp/git-deployment-credentials.png)

Génération et test de votre application localement
--------------------------------------------------

L'application d'inscription est une simple application PHP qui vous permet de vous inscrire à un événement en entrant votre nom et votre adresse électronique. Les informations relatives aux précédents inscrits sont affichées dans un tableau. Les informations d'inscription sont stockées dans une base de données MySQL. L'application se compose de deux fichiers :

-   **index.php** : affiche un formulaire d'inscription et un tableau contenant les informations des inscrits.
-   **createtable.php** : crée la table MySQL pour l'application. Ce fichier sera utilisé une seule fois.

Pour générer et exécuter l'application en local, procédez comme suit : notez que ces étapes partent du principe que PHP, MySQL et un serveur Web sont configurés sur votre machine locale, et que vous avez activé l'[extension PDO pour MySQL](http://www.php.net/manual/en/ref.pdo-mysql.php).

1.  Créez une base de données MySQL nommée `registration`. Pour cela, utilisez l'invite de commandes MySQL avec cette commande :

         mysql> create database registration;

2.  Dans le répertoire racine de votre serveur Web, créez un dossier nommé `registration`, ainsi que deux fichier à l'intérieur : un fichier nommé `createtable.php` et un autre nommé `index.php`.

3.  Ouvrez le fichier `createtable.php` dans un éditeur de texte ou un environnement de développement intégré (IDE), puis ajoutez le code suivant. Ce code permet de créer la table `registration_tbl` dans la base de données `registration`.

         <
         php
         // Informations de connexion de base de données
         $host = "localhost";
         $user = "nom d'utilisateur";
         $pwd = "mot de passe";
         $db = "registration";
         try {
             $conn = new PDO( "mysql:host=$host;dbname=$db", $user, $pwd);
             $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );
             $sql = "CREATE TABLE registration_tbl(
                         id INT NOT NULL AUTO_INCREMENT, 
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

    > [WACOM.NOTE] Vous devrez mettre à jour les valeurs des champs `$user` et `$pwd` avec votre nom d'utilisateur et votre mot de passe MySQL locaux.

4.  Ouvrez un navigateur Web et accédez à [http://localhost/registration/createtable.php](http://localhost/tasklist/createtable.php). La table `registration_tbl` est créée dans la base de données.

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
         $host = "localhost";
         $user = "nom d'utilisateur";
         $pwd = "mot de passe";
         $db = "registration";
         // Connectez-vous à la base de données.
         try {
             $conn = new PDO( "mysql:host=$host;dbname=$db", $user, $pwd);
             $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );
         }
         catch(Exception $e){
             die(var_dump($e));
         }

    > [WACOM.NOTE] Vous devrez encore mettre à jour les valeurs des champs `$user` et `$pwd` avec votre nom d'utilisateur et votre mot de passe MySQL locaux.

7.  À la suite du code de connexion à la base de données, ajoutez le code pour l'insertion des informations d'inscription à la base de données.

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

Vous pouvez maintenant accéder à [http://localhost/inscription/index.php](http://localhost/tasklist/index.php) pour tester l'application.

Obtention des informations de connexion MySQL et FTP
----------------------------------------------------

Pour vous connecter à la base de données MySQL exécutée sur Sites Web Azure, vous avez besoin de vos informations de connexion. Pour obtenir vos informations de connexion MySQL, procédez comme suit :

1.  Depuis le tableau de bord de votre site Web, cliquez sur le lien **View connection strings** à droite de la page.

    ![Obtenir les informations de connexion à la base de données](./media/web-sites-php-web-site-mysql-deploy-use-ftp/connection_string_info.png)

2.  Notez les valeurs pour `Base de données`, `Source de données`, `ID utilisateur` et `Mot de passe`.

3.  Depuis le tableau de bord de votre site Web, cliquez sur le lien **Download publish profile** en bas à droite de la page :

    ![Télécharger le profil de publication](./media/web-sites-php-web-site-mysql-deploy-use-ftp/download_publish_profile_2.png)

4.  Ouvrez le fichier `.publishsettings` dans un éditeur XML.

5.  Recherchez l'élément `<publishProfile >` dont la méthode de publication `publishMethod="FTP"` est semblable à celle-ci :

         <publishProfile publishMethod="FTP" publishUrl="ftp://[monsite].azurewebsites.net/site/wwwroot" ftpPassiveMode="True" userName="[nomutilisateur]" userPWD="[motdepasse]" destinationAppUrl="http://[nom].antdf0.antares-test.windows-int.net" 
             ...
         </publishProfile>

Notez les attributs `publishUrl`, `userName`, et `userPWD`.

Publication de votre application
--------------------------------

Une fois votre application testée en local, vous pouvez la publier dans votre site Web Azure en utilisant FTP. Cependant, vous devez d'abord mettre à jour les informations de connexion à la base de données dans l'application. En utilisant les informations de connexion à la base de données obtenues précédemment (dans la section **Obtention des informations de connexion MySQL et FTP**), mettez à jour les informations suivantes dans **les deux** fichiers `createdatabase.php` et `index.php` avec les valeurs appropriées :

     // Informations de connexion à la base de données
    $host = "valeur de Source de données";
    $user = "valeur de ID utilisateur";
    $pwd = "valeur de Mot de passe";
    $db = "valeur de Base de données";

Vous pouvez à présent publier votre application avec FTP.

1.  Ouvrez le client FTP de votre choix.

2.  Entrez dans votre client FTP la *partie représentant le nom d'hôte* dans l'attribut `publishUrl` que vous avez noté précédemment.

3.  Entrez également les attributs `userName` et `userPWD` tels que vous les avez notés précédemment.

4.  Établissez une connexion.

Une fois connecté, vous pouvez télécharger les fichiers. Assurez-vous de bien télécharger les fichiers dans le répertoire racine, qui est `/site/wwwroot`.

Après avoir téléchargé les deux fichiers `index.php` et `createtable.php`, accédez à **http://[nom du site].azurewebsites.net/createtable.php** pour créer la table MySQL pour l'application, puis accédez à **http://[nom du site].azurewebsites.net/index.php** pour commencer à utiliser l'application.

