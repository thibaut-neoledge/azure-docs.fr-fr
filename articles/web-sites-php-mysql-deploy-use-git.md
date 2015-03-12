<properties 
	pageTitle="Site Web PHP avec MySQL et Git - Didacticiel Azure" 
	description="Un didacticiel qui vous montre comment créer un site Web PHP qui stocke les données dans MySQL et utiliser un déploiement Git vers Azure." 
	services="web-sites" 
	documentationCenter="php" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="PHP" 
	ms.topic="article" 
	ms.date="11/14/2014" 
	ms.author="tomfitz"/>

#Création et déploiement d'un site Web Azure PHP-MySQL avec Git

Ce didacticiel vous montre comment créer un site Web Azure PHP-MySQL et comment le déployer en utilisant Git. Vous utiliserez [PHP][install-php], l'outil de ligne de commande MySQL (inclus dans [MySQL][install-mysql]), un serveur Web et [Git][install-git] qui sont installés sur votre ordinateur. Les instructions de ce didacticiel s'appliquent à n'importe quel système d'exploitation, notamment Windows, Mac et Linux. À la fin de ce guide, vous disposerez d'un site Web PHP/MySQL exécuté dans Azure.
 
Vous apprendrez à effectuer les opérations suivantes :

* Création d'un site Web Azure et d'une base de données MySQL à l'aide du portail de gestion Azure. Comme PHP est activé par défaut dans Sites Web Azure, aucune action particulière n'est requise pour exécuter votre code PHP.
* Publication et republication de votre application dans Azure en utilisant Git.
 
En suivant ce didacticiel, vous allez générer une application Web d'inscription simple dans PHP. L'application est hébergée dans un site Web Azure. Voici une capture d'écran de l'application terminée :

![Azure PHP web site][running-app]

> [AZURE.NOTE]
> Pour suivre ce didacticiel, vous devez disposer d'un compte Azure pour lequel la fonctionnalité Sites Web Azure est activée. Si vous ne possédez pas de compte, vous pouvez créer un compte d'évaluation gratuit en quelques minutes. Pour plus d'informations, consultez la page <a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A74E0F923" target="_blank">Version d'évaluation gratuite Azure</a>.
> 
> Si vous voulez prendre en main Sites Web Azure avant de créer un compte, accédez à <a href="https://trywebsites.azurewebsites.net/?language=php">https://trywebsites.azurewebsites.net</a>, où vous pouvez immédiatement et gratuitement créer un site de départ ASP.NET de courte durée dans Sites Web Azure. Aucun numéro de carte de crédit, ni engagement de quelque sorte ne sont exigés.

##Configuration de l'environnement de développement

Ce didacticiel part du principe que [PHP][install-php], l'outil de ligne de commande MySQL (inclus dans [MySQL][install-mysql]), un serveur Web et [Git][install-git] sont installés sur votre ordinateur.

> [AZURE.NOTE]
> Si vous suivez ce didacticiel sur Windows, vous pouvez configurer votre machine pour PHP et configurer automatiquement IIS (serveur Web intégré dans Windows) en installant le <a href="http://www.microsoft.com/web/handlers/webpi.ashx/getinstaller/azurephpsdk.appids">Kit de développement logiciel (SDK) Azure pour PHP</a>.

##<a id="create-web-site-and-set-up-git"></a>Création d'un site Web Azure et configuration de la publication Git

Suivez cette procédure pour créer un site Web Azure et une base de données MySQL :

1. Connectez-vous au [Portail de gestion Azure][management-portal].
2. Cliquez sur l'icône **Nouveau** dans le coin inférieur gauche du portail.

	![Create New Azure web site][new-website]

3. Cliquez sur **Site Web**, puis sur **Création personnalisée**.

	![Custom Create a new web site][custom-create]
	
	Entrez une valeur pour **URL** et, dans la liste déroulante **Base de données**, sélectionnez **Créer une base de données MySQL**, puis sélectionnez un centre de données pour votre site Web dans la liste déroulante **Région**. Cliquez sur la flèche située en bas de la boîte de dialogue.

	![Fill in web site details][website-details]

4. Entrez une valeur pour le **Nom** de votre base de données, sélectionnez le centre de données de votre base de données dans la liste déroulante **Région**, puis cochez la case qui indique que vous acceptez les conditions juridiques. Cliquez sur la coche située en bas de la boîte de dialogue.

	![Create new MySQL database][new-mysql-db]

	Lorsque le site Web est créé, le texte **La création du site Web " [NOM DU SITE] " s'est terminée correctement** apparaît. Vous pouvez maintenant activer la publication Git.

6. Cliquez sur le nom du site Web affiché dans la liste pour ouvrir le tableau de bord **Démarrage rapide** correspondant.

	![Open web site dashboard][go-to-dashboard]


7. En bas de la page **Démarrage rapide**, cliquez sur **Configurer la publication Git**. 

	![Set up Git publishing][setup-git-publishing]

8. Pour activer la publication Git, vous devez fournir un nom d'utilisateur et un mot de passe. Notez le nom d'utilisateur et le mot de passe que vous créez. (si vous avez déjà configuré un référentiel Git, ignorez cette étape).

	![Create publishing credentials][credentials]

	La configuration du référentiel prend quelques secondes.

9. Lorsque votre référentiel est prêt, les instructions de publication de vos fichiers d'application s'affichent dans le référentiel. Notez ces instructions : elles vous seront utiles plus tard.

	![Git instructions][git-instructions]

##Obtention des informations de connexion MySQL distantes

Pour vous connecter à la base de données MySQL exécutée sur Sites Web Azure, vous avez besoin de vos informations de connexion. Pour obtenir vos informations de connexion MySQL, procédez comme suit :

1. Depuis le tableau de bord de votre site Web, cliquez sur le lien **Afficher les chaînes de connexion** à droite de la page :

	![Get database connection information][connection-string-info]
	
2. Prenez note des valeurs `Database`, `Data Source`, `User Id` et `Password`.

##Génération et test de votre application localement

Après la création d'un site Web Azure, vous pouvez développer votre application en local, la tester, puis la déployer. 

L'application d'inscription est une simple application PHP qui vous permet de vous inscrire à un événement en entrant votre nom et votre adresse électronique. Les informations relatives aux précédents inscrits sont affichées dans un tableau. Les informations d'inscription sont stockées dans une base de données MySQL. L'application se compose d'un seul fichier (dont le code est disponible ci-dessous pour un copier/coller) :

* **index.php** : affiche un formulaire d'inscription et un tableau contenant les informations des inscrits.

Pour générer et exécuter l'application en local, procédez comme suit : Notez que ces étapes partent du principe que PHP, l'outil en ligne de commande MySQL (inclus dans MySQL) et un serveur Web sont configurés sur votre ordinateur local, et que vous avez activé l'[extension PDO pour MySQL][pdo-mysql].

1. Connectez-vous au serveur MySQL distant en utilisant les valeurs pour `Data Source`, `User Id`, `Password` et `Database` récupérées précédemment :

		mysql -h{Data Source] -u[User Id] -p[Password] -D[Database]

2. L'invite de commandes MySQL s'affiche :

		mysql>

3. Copiez la commande `CREATE TABLE` suivante pour créer la table `registration_tbl` dans votre base de données :

		mysql> CREATE TABLE registration_tbl(id INT NOT NULL AUTO_INCREMENT, PRIMARY KEY(id), name VARCHAR(30), email VARCHAR(30), date DATE);

4. Dans le répertoire racine de votre serveur Web, créez un dossier nommé `registration`, ainsi qu'un fichier nommé `index.php`.

5. Ouvrez le fichier **index.php** dans un éditeur de texte ou un environnement de développement intégré (IDE), puis ajoutez le code suivant en y apportant les modifications nécessaires, indiquées par le commentaire //TODO:.


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
			// DB connection info
			//TODO: Update the values for $host, $user, $pwd, and $db
			//using the values you retrieved earlier from the portal.
			$host = "value of Data Source";
			$user = "value of User Id";
			$pwd = "value of Password";
			$db = "value of Database";
			// Connect to database.
			try {
				$conn = new PDO( "mysql:host=$host;dbname=$db", $user, $pwd);
				$conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );
			}
			catch(Exception $e){
				die(var_dump($e));
			}
			// Insert registration info
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
			// Retrieve data
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
		?>
		</body>
		</html>

Vous pouvez à présent accéder à **http://localhost/registration/index.php** pour tester l'application.


##Publication de votre application

Une fois votre application testée en local, vous pouvez la publier vers votre site Web Azure en utilisant Git. Vous allez initialiser votre référentiel Git, puis publier l'application.

> [AZURE.NOTE]
> Cette procédure est identique à celle affichée dans le portail à la fin de la section " Création d'un site Web Azure et configuration de la publication Git " précédente.

1. (Facultatif) Si vous avez oublié ou mal rédigé l'URL de votre référentiel distant Git, sur le portail, accédez à l'onglet Déploiement.
	
	![Get Git URL][git-instructions]

1. Ouvrez GitBash (ou un terminal, si Git est dans votre `PATH`), remplacez les répertoires du répertoire racine de votre application, puis exécutez les commandes suivantes :

		git init
		git add .
		git commit -m "initial commit"
		git remote add azure [URL for remote repository]
		git push azure master

	Vous êtes invité à entrer le mot de passe que vous avez créé précédemment.

	![Initial Push to Azure via Git][git-initial-push]

2. Accédez à **http://[nom du site].azurewebsites.net/index.php** pour commencer à utiliser l'application (ces informations seront stockées dans le tableau de bord de votre compte) :

	![Azure PHP web site][running-app]

Après la publication de votre application, vous pouvez y apporter des modifications, puis utiliser Git pour les publier. 

##Publication des modifications apportées à votre application

Pour publier des modifications apportées à votre application, procédez comme suit :

1. Modifiez votre application en local.
2. Ouvrez GitBash (ou un terminal, si Git est dans votre `PATH`), remplacez les répertoires du répertoire racine de votre application, puis exécutez les commandes suivantes :

		git add .
		git commit -m "comment describing changes"
		git push azure master

	Vous êtes invité à entrer le mot de passe que vous avez créé précédemment.

	![Pushing site changes to Azure via Git][git-change-push]

3. Accédez à **http://[nom du site].azurewebsites.net/index.php** pour afficher votre application, ainsi que les modifications apportées :

	![Azure PHP web site][running-app]

4. Vous pouvez également afficher le nouveau déploiement sous l'onglet 'Deployments' du portail de gestion Azure :

	![List of web site deployments][deployments-list]

[install-php]: http://www.php.net/manual/en/install.php
[install-SQLExpress]: http://www.microsoft.com/fr-fr/download/details.aspx?id=29062
[install-Drivers]: http://www.microsoft.com/fr-fr/download/details.aspx?id=20098
[install-git]: http://git-scm.com/

[pdo-mysql]: http://www.php.net/manual/en/ref.pdo-mysql.php
[running-app]: ./media/web-sites-php-mysql-deploy-use-git/running_app_2.png
[new-website]: ./media/web-sites-php-mysql-deploy-use-git/new_website.jpg
[custom-create]: ./media/web-sites-php-mysql-deploy-use-git/custom_create.png
[website-details]: ./media/web-sites-php-mysql-deploy-use-git/website_details.jpg
[new-mysql-db]: ./media/web-sites-php-mysql-deploy-use-git/new_mysql_db.jpg
[go-to-dashboard]: ./media/web-sites-php-mysql-deploy-use-git/go_to_dashboard.png
[setup-git-publishing]: ./media/web-sites-php-mysql-deploy-use-git/setup_git_publishing.png
[credentials]: ./media/web-sites-php-mysql-deploy-use-git/git-deployment-credentials.png


[git-instructions]: ./media/web-sites-php-mysql-deploy-use-git/git-instructions.png
[git-change-push]: ./media/web-sites-php-mysql-deploy-use-git/php-git-change-push.png
[git-initial-push]: ./media/web-sites-php-mysql-deploy-use-git/php-git-initial-push.png
[deployments-list]: ./media/web-sites-php-mysql-deploy-use-git/php-deployments-list.png
[connection-string-info]: ./media/web-sites-php-mysql-deploy-use-git/connection_string_info.png
[management-portal]: https://manage.windowsazure.com
[sql-database-editions]: http://msdn.microsoft.com/library/windowsazure/ee621788.aspx


<!--HONumber=42-->
