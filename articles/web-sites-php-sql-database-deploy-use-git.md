<properties urlDisplayName="Web w/ SQL + Git" pageTitle="Site web PHP avec une base de données SQL et Git - Didacticiel Azure" metaKeywords="" description="A tutorial that demonstrates how to create a PHP website that stores data in SQL Database and use Git deployment to Azure." metaCanonical="" services="web-sites,sql-database" documentationCenter="PHP" title="Create a PHP website with a SQL Database and deploy using Git" authors="tomfitz" solutions="" manager="wpickett" editor="mollybos" scriptId="" videoId="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="PHP" ms.topic="article" ms.date="11/18/2014" ms.author="tomfitz" />

#Création d'un site web PHP comportant une base de données SQL et déploiement avec Git

Ce didacticiel vous montre comment créer un site web PHP avec une base de données SQL Azure et comment le déployer en utilisant Git. Ce didacticiel part du principe que vous avez installé [PHP][install-php], [SQL Server Express][install-SQLExpress], les [pilotes Microsoft pour SQL Server pour PHP][install-drivers], un serveur web et [Git][install-git] sur votre ordinateur. Une fois que vous aurez terminé ce guide, vous disposerez d'un site web contenant une base de données PHP-SQL qui s'exécute dans Azure.

> [WACOM.NOTE]
> Vous pouvez installer et configurer PHP, SQL Server Express, les pilotes Microsoft SQL Server pour PHP, ainsi qu'Internet Information Services (IIS) via <a href="http://www.microsoft.com/web/downloads/platform.aspx">Microsoft Web Platform Installer</a>.

Vous apprendrez à effectuer les opérations suivantes :

* création d'un site web Azure et d'une base de données SQL à l'aide du portail de gestion Azure Comme PHP est activé par défaut dans Sites Web Azure, aucune action particulière n'est requise pour exécuter votre code PHP.
* publication et republication de votre application dans Azure en utilisant Git.
 
En suivant ce didacticiel, vous allez générer une application Web d'inscription simple dans PHP. L'application est hébergée dans un site web Azure. Voici une capture d'écran de l'application terminée :

![Azure PHP Web Site][running-app]

> [WACOM.NOTE]
> Pour effectuer ce didacticiel, vous avez besoin d'un compte Azure. Vous pouvez <a href="http://azure.microsoft.com/fr-fr/pricing/member-offers/msdn-benefits-details/">activer les avantages de votre abonnement MSDN</a> ou <a href="http://azure.microsoft.com/fr-fr/pricing/free-trial/">vous inscrire à une version d'évaluation gratuite</a>.
> 
> Si vous voulez prendre en main Azure Web Sites avant de créer un compte, accédez à <a href="https://trywebsites.azurewebsites.net/?language=php">https://trywebsites.azurewebsites.net</a>, où vous pouvez immédiatement et gratuitement créer un site de départ ASP.NET de courte durée dans Azure Web Sites. Aucune carte de crédit n'est requise, vous ne prenez aucun engagement.

##Création d'un site web Azure et configuration de la publication Git

Suivez cette procédure pour créer un site web Azure et une base de données SQL :

1. Connectez-vous au [portail de gestion Azure][management-portal].
2. Cliquez sur l'icône **Nouveau** en bas à gauche du portail.
![Create New Azure Web Site][new-website]

3. Cliquez sur **Site web**, puis sur **Création personnalisée**.

	![Custom Create a new Web Site][custom-create]

	Entrez une valeur dans le champ **URL**, sélectionnez **Créer une base de données SQL** dans la liste déroulante **Base de données**, puis **Publier à partir du contrôle de code source**. Cliquez sur la flèche située en bas de la boîte de dialogue.

	![Fill in web site details][website-details-sqlazure]

4. Entrez une valeur dans le champ **Nom** de votre base de données, sélectionnez **Nouveau serveur de base de données SQL**, indiquez vos informations de connexion et sélectionnez un région. Cliquez sur la flèche située en bas de la boîte de dialogue.

	![Fill in SQL Database settings][database-settings]

5. Sélectionnez **Référentiel Git local** pour votre code source.

	![where is your source code][where-is-code]

	Si vous n'avez pas encore configuré de référentiel Git, vous devez fournir un nom d'utilisateur et un mot de passe.

6. Une fois le site web créé, ouvrez le tableau de bord du site et sélectionnez **Afficher les déploiements**.

	![Web site dashboard][go-to-dashboard]

9. Des instructions pour envoyer vos fichiers d'application dans le référentiel s'affichent. Notez ces instructions : elles vous seront utiles plus tard.

	![Git instructions][git-instructions]

##Obtention des informations de connexion à la base de données SQL

Pour vous connecter à l'instance de base de données SQL exécutée sur Sites Web Azure, vous avez besoin de vos informations de connexion. Procédez comme suit pour les obtenir :

1. Dans le portail de gestion Azure, cliquez sur **Ressources liées**, puis sur le nom de la base de données.

	![Linked Resources][linked-resources]

2. Cliquez sur **Afficher les chaînes de connexion**.

	![Connection string][connection-string]
	
3. Dans la section **PHP** de la boîte de dialogue qui s'affiche, notez les valeurs des champs SERVEUR, BASE DE DONNÉES et NOM D'UTILISATEUR.

##Génération et test de votre application localement

L'application d'inscription est une simple application PHP qui vous permet de vous inscrire à un événement en entrant votre nom et votre adresse électronique. Les informations relatives aux précédents inscrits sont affichées dans un tableau. Les informations d'inscription sont stockées dans une instance de base de données SQL. L'application se compose de deux fichiers (dont le code est disponible ci-dessous pour un copier/coller) :

* **index.php** : affiche un formulaire d'inscription et un tableau contenant les informations des inscrits.
* **createtable.php** : crée la table de base de données SQL pour l'application. Ce fichier sera utilisé une seule fois.

Pour exécuter l'application en local, procédez comme suit : notez que ces étapes partent du principe que PHP, SQL Server Express et un serveur web sont configurés sur votre ordinateur local, et que vous avez activé l'[extension PDO pour SQL Server][pdo-sqlsrv].

1. Créez une base de données SQL Server nommée registration. Pour cela, utilisez l'invite de commandes sqlcmd avec ces commandes :

		>sqlcmd -S localhost\sqlexpress -U <local user name> -P <local password>
		1> create database registration
		2> GO	


2. Dans le répertoire racine de votre serveur web, créez un dossier nommé registration ainsi que deux fichiers à l'intérieur : createtable.php et index.php.

3. Ouvrez le fichier createtable.php dans un éditeur de texte ou un environnement de développement intégré (IDE), puis ajoutez le code suivant. Ce code permet de créer la table registration_tbl dans la base de données registration.

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

	Notez que vous devez mettre à jour les valeurs des champs <code>$user</code> et <code>$pwd</code> avec les nom d'utilisateur et mot de passe de votre SQL Server local.

4. Ouvrez un navigateur web et accédez à **http://localhost/registration/createtable.php**. La table registration_tbl est créée dans la base de données.

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

    Une fois de plus, mettez à jour les valeurs des champs <code>$user</code> et <code>$pwd</code> avec les nom d'utilisateur et mot de passe de votre MySQL local.

7. À la suite du code de connexion de la base de données, ajoutez le code pour insérer les informations d'inscription dans la base de données.

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

Vous pouvez à présent accéder à **http://localhost/registration/index.php** pour tester l'application.

##Publication de votre application

Une fois votre application testée localement, vous pouvez la publier vers votre site web Azure en utilisant Git. Cependant, vous devez d'abord mettre à jour les informations de connexion à la base de données dans l'application. En utilisant les informations de connexion à la base de données obtenues précédemment (dans la section **Obtention des informations de connexion à la base de données SQL**), mettez à jour les informations suivantes dans **les deux** fichiers createdatabase.php et index.php avec les valeurs appropriées :

	// DB connection info
	$host = "tcp:<value of SERVER>";
	$user = "<value of USERNAME>@<server ID>";
	$pwd = "<your password>";
	$db = "<value of DATABASE>";

> [WACOM.NOTE]
> Dans le champ <code>$host</code>, la valeur de SERVEUR doit être ajoutée à <code>tcp:</code>et la valeur de <code>$user</code> est la concaténation de la valeur NOM D'UTILISATEUR, '@', et de votre ID de serveur Votre ID de serveur est constitué des 10 premiers caractères de la valeur de SERVEUR.


Tout est prêt pour configurer la publication Git et publier l'application.

> [WACOM.NOTE]
> Cette procédure est identique à celle indiquée à la fin de la section " Création d'un site web Azure et configuration de la publication Git " précédente.


1. Ouvrez GitBash (ou un terminal, si Git est dans votre PATH), remplacez les répertoires du répertoire racine de votre application, puis exécutez les commandes suivantes :

		git init
		git add .
		git commit -m "initial commit"
		git remote add azure [URL for remote repository]
		git push azure master

	Vous êtes invité à entrer le mot de passe que vous avez créé précédemment.

2. Accédez à **http://[nom du site].azurewebsites.net/createtable.php** pour créer la table MySQL pour l'application.
3. Accédez à **http://[nom du site].azurewebsites.net/index.php** pour commencer à utiliser l'application.

Après la publication de votre application, vous pouvez y apporter des modifications, puis utiliser Git pour les publier. 

##Publication des modifications apportées à votre application

Pour publier des modifications apportées à votre application, procédez comme suit :

1. Modifiez votre application en local.
2. Ouvrez GitBash (ou un terminal, si Git est dans votre PATH), remplacez les répertoires du répertoire racine de votre application, puis exécutez les commandes suivantes :

		git add .
		git commit -m "comment describing changes"
		git push azure master

	Vous êtes invité à entrer le mot de passe que vous avez créé précédemment.

3. Accédez à **http://[nom du site].azurewebsites.net/index.php** pour afficher vos modifications.

[install-php]: http://www.php.net/manual/en/install.php
[install-SQLExpress]: http://www.microsoft.com/fr-fr/download/details.aspx?id=29062
[install-Drivers]: http://www.microsoft.com/fr-fr/download/details.aspx?id=20098
[install-git]: http://git-scm.com/
[pdo-sqlsrv]: http://php.net/pdo_sqlsrv
[running-app]: ./media/web-sites-php-sql-database-deploy-use-git/running_app_3.png
[new-website]: ./media/web-sites-php-sql-database-deploy-use-git/new_website.jpg
[custom-create]: ./media/web-sites-php-sql-database-deploy-use-git/custom_create.png
[website-details-sqlazure]: ./media/web-sites-php-sql-database-deploy-use-git/createphpgitsite.png
[database-settings]: ./media/web-sites-php-sql-database-deploy-use-git/setupdb.png
[create-server]: ./media/web-sites-php-sql-database-deploy-use-git/create_server.jpg
[go-to-dashboard]: ./media/web-sites-php-sql-database-deploy-use-git/viewdeploy.png
[setup-git-publishing]: ./media/web-sites-php-sql-database-deploy-use-git/setup_git_publishing.png
[credentials]: ./media/web-sites-php-sql-database-deploy-use-git/git-deployment-credentials.png


[git-instructions]: ./media/web-sites-php-sql-database-deploy-use-git/gitsettings.png
[linked-resources]: ./media/web-sites-php-sql-database-deploy-use-git/linked_resources.jpg
[connection-string]: ./media/web-sites-php-sql-database-deploy-use-git/connection_string.jpg
[management-portal]: https://manage.windowsazure.com/
[sql-database-editions]: http://msdn.microsoft.com/fr-fr/library/windowsazure/ee621788.aspx
[where-is-code]: ./media/web-sites-php-sql-database-deploy-use-git/setupgit.png

<!--HONumber=35_1-->
