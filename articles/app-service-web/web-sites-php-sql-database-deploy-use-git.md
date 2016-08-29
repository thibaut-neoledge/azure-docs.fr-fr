<properties 
	pageTitle="Création d’une application web PHP-SQL dans Azure App Service et déploiement à l’aide de Git" 
	description="Didacticiel expliquant comment créer une application web PHP stockant les données dans Base de données SQL Azure et comment utiliser un déploiement Git dans Azure App Service." 
	services="app-service\web, sql-database" 
	documentationCenter="php" 
	authors="rmcmurray" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="PHP" 
	ms.topic="article" 
	ms.date="08/11/2016" 
	ms.author="robmcm"/>

# Création d’une application web PHP-SQL dans Azure App Service et déploiement à l’aide de Git

Ce didacticiel vous explique comment créer une application web PHP dans [Azure App Service](http://go.azure.microsoft.com/.com/fwlink/?LinkId=529714) se connectant à la base de données SQL Azure et comment la déployer à l’aide de Git. Pour ce didacticiel, [PHP][install-php], [SQL Server Express][install-SQLExpress], les [pilotes azure.microsoft.com/ SQL Server pour PHP](http://www.azure.microsoft.com/.com/download/en/details.aspx?id=20098) et [Git][install-git] doivent être installés sur votre ordinateur. À la fin de ce guide, vous disposerez d’une application web PHP-SQL s’exécutant dans Azure.

> [AZURE.NOTE]
Vous pouvez installer et configurer PHP, SQL Server Express et les pilotes azure.microsoft.com/ SQL Server pour PHP via [azure.microsoft.com/ Web Platform Installer](http://www.azure.microsoft.com/.com/web/downloads/platform.aspx).

Vous apprendrez à effectuer les opérations suivantes :

* Création d’une application web Azure et d’une base de données SQL à l’aide du [portail Azure](http://go.azure.microsoft.com/.com/fwlink/?LinkId=529715). (PHP étant activé par défaut dans App Service Web Apps, l’exécution de votre code PHP ne requiert aucune action particulière) ;
* publication et republication de votre application dans Azure en utilisant Git.
 
En suivant ce didacticiel, vous allez générer une application Web d'inscription simple dans PHP. L'application est hébergée dans un site web Azure. Voici une capture d’écran de l’application terminée :

![Site Web PHP Azure](./media/web-sites-php-sql-database-deploy-use-git/running_app_3.png)

[AZURE.INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]

>[AZURE.NOTE] Si vous voulez vous familiariser avec Azure App Service avant d’ouvrir un compte Azure, accédez à la page [Essayer App Service](http://go.azure.microsoft.com/.com/fwlink/?LinkId=523751). Vous pourrez créer immédiatement une application web temporaire dans App Service. Aucune carte de crédit n’est requise ; vous ne prenez aucun engagement.

##Création d’une application web Azure et configuration de la publication Git

Suivez cette procédure pour créer une application web Azure et une base de données SQL :

1. Connectez-vous au [portail Azure](https://portal.azure.com/).

2. Ouvrez Azure Marketplace en cliquant sur l'icône **Nouveau** dans le coin supérieur gauche du tableau de bord, cliquez sur **Sélectionner tout** en regard de Marketplace et sélectionnez **Web + Mobile**.
	
3. Dans Marketplace, sélectionnez **Web + Mobile**.

4. Cliquez sur l’icône **Application web et SQL**.

5. Après avoir lu la description de l’application web et de l’application SQL, sélectionnez **Créer**.

6. Cliquez sur chaque partie (**Groupe de ressources**, **Application web**, **Base de données** et **Abonnement**), puis saisissez ou sélectionnez des valeurs pour les champs obligatoires :
	
	- Entrez le nom d’URL de votre choix.
	- Configuration des informations d’identification du serveur de bases de données
	- Sélectionnez la région la plus proche de vous.

	![Configurer une application](./media/web-sites-php-sql-database-deploy-use-git/configure-db-settings.png)

7. Une fois l’application web définie, cliquez sur **Créer**.

	Une fois l’application web créée, le bouton **Notifications** affiche la mention **RÉUSSITE** en vert clignotant, et le panneau du groupe de ressources affiche l’application web et la base de données SQL dans le groupe.

4. Cliquez sur l’icône de l’application web dans le panneau du groupe de ressources pour ouvrir le panneau de l’application web.

	![Groupe de ressources de l’application web](./media/web-sites-php-sql-database-deploy-use-git/resource-group-blade.png)

5. Dans les **Paramètres**, cliquez sur **Déploiement continu** > **Configurer les paramètres requis**. Sélectionnez **Référentiel Git local**, puis cliquez sur **OK**.

	![où est votre code source](./media/web-sites-php-sql-database-deploy-use-git/setup-local-git.png)

	Si vous n’avez pas encore configuré de référentiel Git, vous devez fournir un nom d’utilisateur et un mot de passe. Pour cela, cliquez sur **Paramètres** > **Informations d'identification de déploiement** dans le panneau de l'application web.

	![](./media/web-sites-php-sql-database-deploy-use-git/deployment-credentials.png)

6. Dans les **Paramètres**, cliquez sur **Propriétés** pour afficher l'URL distante Git nécessaire pour déployer votre application PHP par la suite.

##Obtention des informations de connexion à la base de données SQL

Pour établir la connexion à l’instance de base de données SQL liée à votre application web, vous avez besoin des informations de connexion que vous avez spécifiées lors de la création de la base de données. Procédez comme suit pour les obtenir :

1. Revenez au panneau du groupe de ressources, puis cliquez sur l’icône de base de données SQL.

2. Dans le panneau de la base de données SQL, cliquez sur **Paramètres** > **Propriétés**, puis sur **Afficher les chaînes de connexion à la base de données**.

	![Afficher les propriétés de base de données](./media/web-sites-php-sql-database-deploy-use-git/view-database-properties.png)
	
3. Dans la section **PHP** de la boîte de dialogue qui s’affiche, notez les valeurs de `Server`, `SQL Database` et `User Name`. Vous utiliserez ces valeurs ultérieurement lors de la publication de votre application web PHP sur Azure App Service.

##Génération et test de votre application localement

L'application d'inscription est une simple application PHP qui vous permet de vous inscrire à un événement en entrant votre nom et votre adresse électronique. Les informations relatives aux précédents inscrits sont affichées dans un tableau. Les informations d'inscription sont stockées dans une instance de base de données SQL. L’application se compose de deux fichiers (dont le code est disponible ci-dessous pour un copier/coller) :

* **index.php** : affiche un formulaire d’inscription et un tableau contenant les informations des inscrits.
* **createtable.php** : crée la table de base de données SQL pour l’application. Ce fichier sera utilisé une seule fois.

Pour exécuter l'application en local, procédez comme suit : notez que ces étapes partent du principe que PHP et SQL Server Express sont configurés sur votre machine locale, et que vous avez activé l'[extension PDO pour SQL Server][pdo-sqlsrv].

1. Créez une base de données SQL Server nommée `registration`. Pour cela, utilisez l’invite de commandes `sqlcmd` avec ces commandes :

		>sqlcmd -S localhost\sqlexpress -U <local user name> -P <local password>
		1> create database registration
		2> GO	


2. Dans le répertoire racine de votre application, créez deux fichiers : un fichier nommé `createtable.php` et un autre nommé `index.php`.

3. Ouvrez le fichier `createtable.php` dans un éditeur de texte ou un environnement de développement intégré (IDE), puis ajoutez le code suivant. Ce code permet de créer la table `registration_tbl` dans la base de données `registration`.

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

	Vous devrez mettre à jour les valeurs de <code>$user</code> et de <code>$pwd</code> avec votre nom d’utilisateur et votre mot de passe SQL Server locaux.

4. Sur un terminal dans le répertoire racine de l'application, tapez la commande suivante :

		php -S localhost:8000

4. Ouvrez un navigateur web et accédez à **http://localhost:8000/createtable.php**. La table `registration_tbl` est créée dans la base de données.

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

    Là encore, vous devez mettre à jour les valeurs de <code>$user</code> et <code>$pwd</code> avec votre nom d’utilisateur et votre mot de passe MySQL locaux.

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

Vous pouvez à présent accéder à **http://localhost:8000/index.php** pour tester l'application.

##Publication de votre application

Une fois votre application testée en local, vous pouvez la publier dans App Service Web Apps à l’aide de Git. Cependant, vous devez d'abord mettre à jour les informations de connexion à la base de données dans l'application. En utilisant les informations de connexion à la base de données obtenues précédemment (dans la section **Obtention des informations de connexion à la base de données SQL**), mettez à jour les informations suivantes dans les **deux** fichiers `createdatabase.php` et `index.php` avec les valeurs appropriées :

	// DB connection info
	$host = "tcp:<value of Server>";
	$user = "<value of User Name>";
	$pwd = "<your password>";
	$db = "<value of SQL Database>";

> [AZURE.NOTE]
Dans <code>$host</code>, la valeur du serveur doit présenter le préfixe <code>tcp:</code>.


Tout est prêt pour configurer la publication Git et publier l’application.

> [AZURE.NOTE]
Cette procédure est identique à celle indiquée à la fin de la section **Création d’une application web Azure et configuration de la publication Git** précédente.


1. Ouvrez GitBash (ou un terminal, si Git figure dans votre `PATH`), remplacez les répertoires par le répertoire racine de votre application (le répertoire **d’enregistrement**, puis exécutez les commandes suivantes :

		git init
		git add .
		git commit -m "initial commit"
		git remote add azure [URL for remote repository]
		git push azure master

	Vous êtes invité à entrer le mot de passe que vous avez créé précédemment.

2. Accédez à **http://[web app name].azurewebsites.net/createtable.php** pour créer la table de la base de données SQL de l’application.
3. Accédez à **http://[web app name].azurewebsites.net/index.php** pour commencer à utiliser l’application.

Après la publication de votre application, vous pouvez y apporter des modifications, puis utiliser Git pour les publier.

##Publication des modifications apportées à votre application

Pour publier des modifications apportées à votre application, procédez comme suit :

1. Modifiez votre application en local.
2. Ouvrez GitBash (ou un terminal, si Git est dans votre `PATH`), remplacez les répertoires du répertoire racine de votre application, puis exécutez les commandes suivantes :

		git add .
		git commit -m "comment describing changes"
		git push azure master

	Vous êtes invité à entrer le mot de passe que vous avez créé précédemment.

3. Accédez à **http://[web app name].azurewebsites.net/index.php** pour voir vos modifications.

## Changements apportés
* Pour obtenir un guide présentant les modifications apportées dans le cadre de la transition entre Sites Web et App Service, consultez la page [Azure App Service et les services Azure existants](http://go.azure.microsoft.com/.com/fwlink/?LinkId=529714).




[install-php]: http://www.php.net/manual/en/install.php
[install-SQLExpress]: http://www.azure.microsoft.com/.com/download/details.aspx?id=29062
[install-Drivers]: http://www.azure.microsoft.com/.com/download/details.aspx?id=20098
[install-git]: http://git-scm.com/
[pdo-sqlsrv]: http://php.net/pdo_sqlsrv
 

<!---HONumber=AcomDC_0817_2016-->