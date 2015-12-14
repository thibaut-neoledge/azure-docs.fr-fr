<properties 
	pageTitle="Créer une application web PHP-MySQL dans Azure App Service et la déployer à l’aide de FTP" 
	description="Didacticiel expliquant comment créer une application web PHP stockant les données dans MySQL et comment utiliser un déploiement FTP dans Azure." 
	services="app-service\web" 
	documentationCenter="php" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="PHP" 
	ms.topic="article" 
	ms.date="10/01/2015" 
	ms.author="tomfitz"/>


#Créer une application web PHP-MySQL dans Azure App Service et la déployer à l’aide de FTP

> [AZURE.SELECTOR]
- [.Net](web-sites-dotnet-get-started.md)
- [Node.js](web-sites-nodejs-develop-deploy-mac.md)
- [Java](web-sites-java-get-started.md)
- [PHP - Git](web-sites-php-mysql-deploy-use-git.md)
- [PHP - FTP](web-sites-php-mysql-deploy-use-ftp.md)
- [Python](web-sites-python-ptvs-django-mysql.md)

Ce didacticiel vous indique comment créer une application web PHP-MySQL et comment la déployer à l’aide de FTP. Il part du principe que vous avez installé [PHP][install-php], [MySQL][install-mysql], un serveur Web et un client FTP sur votre ordinateur. Les instructions de ce didacticiel s'appliquent à n'importe quel système d'exploitation, notamment Windows, Mac et Linux. À la fin de ce guide, vous disposerez d’une application web PHP/MySQL s’exécutant dans Azure.
 
Vous apprendrez à effectuer les opérations suivantes :

* création d’une application web et d’une base de données MySQL à l’aide du portail Azure (PHP étant activé par défaut dans Web Apps, l’exécution de votre code PHP ne requiert aucune action particulière) ;
* publier votre application sur Azure avec FTP.
 
En suivant ce didacticiel, vous allez générer une application web d’inscription simple dans PHP. Cette application sera hébergée dans une application web. Voici une capture d'écran de l'application terminée :

![Site Web PHP Azure][running-app]

>[AZURE.NOTE]Si vous souhaitez commencer à utiliser Azure App Service avant d’ouvrir un compte, accédez au site [Essayer App Service](http://go.microsoft.com/fwlink/?LinkId=523751), où vous pouvez créer immédiatement une application web temporaire dans App Service. Aucune carte de crédit n’est requise, et vous ne prenez aucun engagement.


##Créer une application web et configurer la publication FTP

Pour créer une application web et une base de données MySQL, suivez la procédure ci-après :

1. Connectez-vous au [portail Azure][management-portal].
2. Cliquez sur l’icône **+ Nouveau** en bas à gauche du portail Azure.

	![Créer un site Web Azure][new-website]

3. Cliquez sur **Web et mobilité**, puis sur **Application web et MySQL**.

	![Création personnalisée d'un site Web][custom-create]

4. Entrez un nom valide pour votre groupe de ressources.

    ![Définir le nom du groupe de ressources][resource-group]

5. Entrez des valeurs pour votre nouvelle application web.

     ![Créer une application web][new-web-app]

6. Entrez des valeurs pour votre nouvelle base de données, notamment l’acceptation des conditions juridiques.

	![Créer une base de données MySQL][new-mysql-db]
	
7. Une fois que l’application web a été créée, vous voyez apparaître le nouveau groupe de ressources. Cliquez sur le nom de l’application web pour en configurer les paramètres.

	![Ouvrir l’application web][go-to-webapp]

6. Faites défiler l’écran jusqu’à la section **Définir les informations d’identification de déploiement**.

	![Définir les informations d’identification de déploiement][set-deployment-credentials]

7. Pour activer la publication FTP, vous devez fournir un nom d'utilisateur et un mot de passe. Enregistrez les informations d’identification et notez le nom d’utilisateur et le mot de passe que vous créez.

	![Créer les informations d'identification de publication][portal-ftp-username-password]

##Générer et tester votre application localement

L'application d'inscription est une simple application PHP qui vous permet de vous inscrire à un événement en entrant votre nom et votre adresse électronique. Les informations relatives aux précédents inscrits sont affichées dans un tableau. Les informations d'inscription sont stockées dans une base de données MySQL. L’application se compose de deux fichiers :

* **index.php** : affiche un formulaire d’inscription et un tableau contenant les informations des inscrits.
* **createtable.php** : crée la table MySQL de l’application. Ce fichier sera utilisé une seule fois.

Pour générer et exécuter l’application localement, suivez la procédure ci-après. notez que ces étapes partent du principe que PHP, MySQL et un serveur Web sont configurés sur votre machine locale, et que vous avez activé l'[extension PDO pour MySQL][pdo-mysql].

1. Créez une base de données MySQL nommée `registration`. Pour cela, utilisez l'invite de commandes MySQL avec cette commande :

		mysql> create database registration;

2. Dans le répertoire racine de votre serveur web, créez un dossier nommé `registration` et deux fichiers à l’intérieur : un fichier nommé `createtable.php` et un autre nommé `index.php`.

3. Ouvrez le fichier `createtable.php` dans un éditeur de texte ou un environnement de développement intégré (IDE), puis ajoutez le code suivant. Ce code permet de créer la table `registration_tbl` dans la base de données `registration`.

		<?php
		// DB connection info
		$host = "localhost";
		$user = "user name";
		$pwd = "password";
		$db = "registration";
		try{
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
		echo "<h3>Table created.</h3>";
		?>

	> [AZURE.NOTE]Vous devrez encore mettre à jour les valeurs de <code>$user</code> et <code>$pwd</code> avec votre nom d’utilisateur et votre mot de passe MySQL locaux.

4. Ouvrez un navigateur Web et accédez à [http://localhost/registration/createtable.php][localhost-createtable]. La table `registration_tbl` est créée dans la base de données.

5. Ouvrez le fichier **index.php** dans un éditeur de texte ou un IDE et ajoutez les codes HTML et CSS de base pour la page (le code PHP sera ajouté plus tard).

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

6. Ajoutez le code PHP dans les balises PHP pour la connexion à la base de données.

		// DB connection info
		$host = "localhost";
		$user = "user name";
		$pwd = "password";
		$db = "registration";
		// Connect to database.
		try {
			$conn = new PDO( "mysql:host=$host;dbname=$db", $user, $pwd);
			$conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );
		}
		catch(Exception $e){
			die(var_dump($e));
		}

	> [AZURE.NOTE]Là encore, vous devez mettre à jour les valeurs de <code>$user</code> et <code>$pwd</code> avec votre nom d’utilisateur et votre mot de passe MySQL locaux.

7. À la suite du code de connexion à la base de données, ajoutez le code pour l'insertion des informations d'inscription à la base de données.

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

8. Pour finir, à la suite du code précédent, ajoutez le code de récupération des données de la base de données.

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

Vous pouvez maintenant accéder à [http://localhost/inscription/index.php][localhost-index] pour tester l’application.

##Obtention des informations de connexion MySQL et FTP

Pour vous connecter à la base de données MySQL qui s’exécute dans Web Apps, vous devez disposer des informations de connexion. Pour obtenir vos informations de connexion MySQL, procédez comme suit :

1. À partir de votre groupe de ressources, cliquez sur la base de données :

	![Sélectionner la base de données][select-database]

2. Dans le résumé de la base de données, sélectionnez **Propriétés**.

    ![Sélectionner les propriétés][select-properties]
	
2. Notez les valeurs de `Database`, `Host`, `User Id` et `Password`.

    ![Noter les propriétés][note-properties]

3. Dans votre application web, cliquez sur le lien **Télécharger le profil de publication** dans le coin inférieur droit de la page :

	![Télécharger le profil de publication][download-publish-profile]

4. Ouvrez le fichier `.publishsettings` dans un éditeur XML.

3. Recherchez l’élément `<publishProfile >` dont la structure de publication `publishMethod="FTP"` est semblable à celle-ci :

		<publishProfile publishMethod="FTP" publishUrl="ftp://[mysite].azurewebsites.net/site/wwwroot" ftpPassiveMode="True" userName="[username]" userPWD="[password]" destinationAppUrl="http://[name].antdf0.antares-test.windows-int.net" 
			...
		</publishProfile>
	
Notez les attributs `publishUrl`, `userName` et `userPWD`.

##Publier votre application

Après avoir testé votre application localement, vous pouvez la publier dans votre application web à l’aide de FTP. Cependant, vous devez d'abord mettre à jour les informations de connexion à la base de données dans l'application. En utilisant les informations de connexion à la base de données obtenues précédemment (dans la section **Obtention des informations de connexion MySQL et FTP**), mettez à jour les informations suivantes dans **les deux** fichiers `createdatabase.php` et `index.php` avec les valeurs appropriées :

	// DB connection info
	$host = "value of Data Source";
	$user = "value of User Id";
	$pwd = "value of Password";
	$db = "value of Database";

Vous pouvez à présent publier votre application à l’aide de FTP.

1. Ouvrez le client FTP de votre choix.

2. Dans votre client FTP, entrez la *partie du nom d’hôte* de l’attribut `publishUrl`, que vous avez notée précédemment.

3. Entrez également les attributs `userName` et `userPWD` tels que vous les avez notés précédemment.

4. Établissez une connexion.

Une fois connecté, vous pouvez télécharger les fichiers. Veillez à charger les fichiers dans le répertoire racine `/site/wwwroot`.

Après avoir chargé les fichiers `index.php` et `createtable.php`, accédez à **http://[site[nom\_site].azurewebsites.net/createtable.php** pour créer la table MySQL de l’application, puis accédez à **http://[site[nom\_site].azurewebsites.net/index.php** pour commencer à utiliser l’application.
 
## Étapes suivantes

Pour plus d’informations, consultez le [Centre pour développeurs PHP](/develop/php/).

[install-php]: http://www.php.net/manual/en/install.php
[install-mysql]: http://dev.mysql.com/doc/refman/5.6/en/installing.html
[pdo-mysql]: http://www.php.net/manual/en/ref.pdo-mysql.php
[localhost-createtable]: http://localhost/tasklist/createtable.php
[localhost-index]: http://localhost/tasklist/index.php
[running-app]: ./media/web-sites-php-mysql-deploy-use-ftp/running_app_2.png
[new-website]: ./media/web-sites-php-mysql-deploy-use-ftp/new_website2.png
[custom-create]: ./media/web-sites-php-mysql-deploy-use-ftp/create_web_mysql.png
[website-details]: ./media/web-sites-php-web-site-mysql-deploy-use-ftp/website_details.jpg
[new-mysql-db]: ./media/web-sites-php-mysql-deploy-use-ftp/create_db.png
[go-to-webapp]: ./media/web-sites-php-mysql-deploy-use-ftp/select_webapp.png
[set-deployment-credentials]: ./media/web-sites-php-mysql-deploy-use-ftp/set_credentials.png
[portal-ftp-username-password]: ./media/web-sites-php-mysql-deploy-use-ftp/save_credentials.png
[resource-group]: ./media/web-sites-php-mysql-deploy-use-ftp/set_group.png
[new-web-app]: ./media/web-sites-php-mysql-deploy-use-ftp/create_wa.png
[select-database]: ./media/web-sites-php-mysql-deploy-use-ftp/select_database.png
[select-properties]: ./media/web-sites-php-mysql-deploy-use-ftp/select_properties.png
[note-properties]: ./media/web-sites-php-mysql-deploy-use-ftp/note-properties.png

[connection-string-info]: ./media/web-sites-php-web-site-mysql-deploy-use-ftp/connection_string_info.png
[management-portal]: https://portal.azure.com
[download-publish-profile]: ./media/web-sites-php-mysql-deploy-use-ftp/download_publish_profile_3.png
 

<!---HONumber=AcomDC_1203_2015-->