<properties 
	pageTitle="Site Web PHP avec une base de données SQL et WebMatrix - Didacticiel Azure" 
	description="Un didacticiel qui vous montre comment utiliser l'IDE WebMatrix gratuite pour créer et déployer un site Web PHP qui stocke les données dans une base de données SQL." 
	services="web-sites" 
	documentationCenter="" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="PHP" 
	ms.topic="article" 
	ms.date="11/17/2014" 
	ms.author="tomfitz"/>





#Création et déploiement d'un site Web PHP et d'une base de données SQL avec WebMatrix

Ce didacticiel vous explique comment utiliser WebMatrix pour développer et déployer dans un site Web Azure une application PHP qui utilise une base de données SQL Azure. WebMatrix est un outil de développement Web gratuit de Microsoft doté de tous les éléments nécessaires pour développer un site Web. WebMatrix prend en charge le PHP et inclut Intellisence pour le développement PHP. 

Ce didacticiel part du principe que vous avez installé [SQL Server Express][install-SQLExpress] sur votre ordinateur pour pouvoir tester une application en local. Cependant, cette installation n'est pas obligatoire pour suivre ce didacticiel. Vous pouvez en effet déployer votre application directement sur Sites Web Azure.

Une fois que vous aurez terminé ce guide, vous disposerez d'un site Web contenant une base de données PHP-SQL qui s'exécute dans Azure.
 
Vous apprendrez à effectuer les opérations suivantes :

* création d'un site Web Azure et d'une base de données SQL à l'aide du portail de gestion Azure. Comme PHP est activé par défaut dans Sites Web Azure, aucune action particulière n'est requise pour exécuter votre code PHP.
* développement d'une application PHP avec WebMatrix ;
* publication et republication de votre application dans Azure avec WebMatrix.
 
En suivant ce didacticiel, vous allez créer une application Web Tasklist simple dans PHP. L'application est hébergée dans un site Web Azure. Voici une capture d'écran de l'application en cours d'exécution :

![Azure PHP Web Site][running-app]

> [AZURE.NOTE]
> Pour effectuer ce didacticiel, vous avez besoin d'un compte Azure. Vous pouvez <a href="http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/">activer les avantages de votre abonnement MSDN</a> ou <a href="http://azure.microsoft.com/pricing/free-trial/">obtenir une version d'évaluation gratuite</a>.
> 
> Si vous voulez prendre en main Sites Web Azure avant de créer un compte, accédez à <a href="https://trywebsites.azurewebsites.net/?language=php">https://trywebsites.azurewebsites.net</a>, où vous pouvez immédiatement et gratuitement créer un site de départ ASP.NET de courte durée dans Sites Web Azure. Aucun numéro de carte de crédit, ni engagement de quelque sorte n'est exigé.

##Configuration requise

1. [Téléchargez][tasklist-sqlazure-download] les fichiers de l'application Tasklist. Il s'agit d'une application PHP simple qui vous permet d'ajouter, de marquer comme terminés et de supprimer des éléments d'une liste de tâches. Ces derniers sont stockés dans une base de données SQL (SQL Server Express pour le test local). L'application se compose des fichiers suivants :

	* **index.php** : affiche les tâches et fournit un formulaire pour ajouter un élément à la liste.
	* **additem.php** : ajoute un élément à la liste.
	* **getitems.php** : obtient tous les éléments de la base de données.
	* **markitemcomplete.php** : définit l'état d'un élément sur Terminé.
	* **deleteitem.php** : supprime un élément.
	* **taskmodel.php** : contient les fonctionnalités permettant d'ajouter, d'obtenir, de mettre à jour et de supprimer des éléments de la base de données.
	* **createtable.php** : crée la table de base de données SQL pour l'application. Ce fichier sera appelé une seule fois.

2. Créez une base de données SQL Server nommée `tasklist`. Pour cela, utilisez l'invite de commandes `sqlcmd` avec ces commandes :

		>sqlcmd -S <server name>\sqlexpress -U <user name> -P <password>
		1> create database tasklist
		2> GO

	Suivez cette procédure seulement si vous voulez tester votre application en local.

## Création d'un site Web et d'une base de données SQL

1. Connectez-vous au [portail de gestion][preview-portal].
2. Cliquez sur l'icône **+ Nouveau** dans le coin inférieur gauche du portail.

	![Create New Azure Web Site][NewWebSite1]

3. Cliquez sur **SITE WEB**, puis sur **CRÉATION PERSONNALISÉE**.

	![Custom Create a new Web Site][NewWebSite2]

	Entrez une valeur pour **URL**, dans la liste déroulante **BASE DE DONNÉES**, sélectionnez **Créer une base de données SQL**, puis sélectionnez un centre de données pour votre site Web dans la liste déroulante **RÉGION**. Cliquez sur la flèche située en bas de la boîte de dialogue.

	![Fill in web site details][NewWebSite3_SQL]

4. Entrez une valeur pour le **NOM** de votre base de données, puis sélectionnez **NOUVEAU serveur de base de données SQL**. Entrez un nom et un mot de passe de connexion au serveur (et confirmez le mot de passe). Sélectionnez la région dans laquelle votre serveur de base de données SQL sera créé.

	![Fill in SQL Database settings][NewWebSite4_SQL]

	Lorsque le site Web est créé, le texte **La création du site Web " [NOM DU SITE] " s'est terminée correctement** apparaît. Ensuite, vous obtenez les informations de connexion à la base de données.

5. Cliquez sur **RESSOURCES LIÉES**, puis sélectionnez le nom de la base de données.

	![Linked Resources][NewWebSite6_SQL]

6. Cliquez sur **Afficher les chaînes de connexion**.

	![Connection string][NewWebSite7]
	
Dans la section **PHP** de la boîte de dialogue qui s'affiche, notez les valeurs des champs  `UID`, `PWD`, `Database` et`$serverName`. Ces informations vous serviront plus tard.

##Installation de WebMatrix

Vous pouvez installer WebMatrix à partir du [Portail de gestion][preview-portal]. 

1. Une fois connecté, accédez à la page Démarrage rapide de votre site Web, puis, en bas de la page, cliquez sur l'icône WebMatrix :

	![Install WebMatrix][InstallWebMatrix]

	Suivez les instructions des invites pour installer WebMatrix.

2. Une fois WebMatrix installé, il tentera d'ouvrir votre site en tant que projet WebMatrix. Vous pouvez choisir de modifier votre site directement ou de télécharger une copie locale. Pour ce didacticiel, sélectionnez 'Edit local copy'. 

3. Lorsque vous êtes invité à télécharger votre site, sélectionnez **Oui, installer à partir de la galerie de modèles**.

	![Download web site][download-site]

4. Parmi les modèles disponibles, sélectionnez **PHP**.

	![Site from template][site-from-template]

5. Sélectionnez le modèle **Site vide**. Nommez le site, puis cliquez sur **SUIVANT**.

	![Provide name for site][site-from-template-2]

WebMatrix ouvre votre site ainsi que certains fichiers par défaut.

##Développement de votre application

Durant les prochaines étapes, vous allez développer l'application Tasklist en ajoutant les fichiers téléchargés auparavant et en apportant quelques modifications. Cependant, vous pouvez ajouter vos propres fichiers ou en créer.

1. Quand votre site est ouvert dans WebMatrix, ajoutez les fichiers de votre application en cliquant sur **Ajouter existant** :

	![WebMatrix - Add existing files][edit_addexisting]

	Dans la boîte de dialogue qui s'affiche, accédez aux fichiers téléchargés auparavant, sélectionnez-les tous, puis cliquez sur Ouvrir. Lorsque vous y êtes invité, remplacez le fichier `index.php`. 

2. Ensuite, vous devez ajouter les informations de connexion à votre base de données SQL Server locale au fichier `taskmodel.php`. Ouvrez le fichier `taskmodel.php` en double-cliquant dessus, puis mettez à jour les informations de connexion à la base de données dans la fonction `connect`. (**Remarque** : Allez directement à la section [Publier votre application](#Publish) si vous ne voulez pas tester votre application localement, mais plutôt la publier directement dans Sites Web Azure.)

		// DB connection info
		$host = "localhost\sqlexpress";
		$user = "your user name";
		$pwd = "your password";
		$db = "tasklist";

	Enregistrez le fichier `taskmodel.php`.

3. Pour exécuter l'application, vous devez créer la table `items`. Cliquez avec le bouton droit sur le fichier `createtable.php`, puis sélectionnez **Lancer dans le navigateur**. Ceci lancera `createtable.php` dans votre navigateur et exécutera le code créant la table `items` dans la base de données `tasklist`.

	![WebMatrix - Launch createtable.php in browser][edit_run]

4. À présent, vous pouvez tester localement l'application. Cliquez avec le bouton droit sur le fichier `index.php`, puis sélectionnez **Lancer dans le navigateur**. Testez l'application en ajoutant des éléments, en les marquant comme terminés et en les supprimant.   


<h2><a id="Publish"></a>Publication de votre application</h2>

Avant de publier votre application dans Sites Web Azure, vous devez mettre à jour les informations de connexion à la base de données dans `taskmodel.php` en utilisant les informations de connexion que vous avez obtenues précédemment (dans la section [Création d'un site Web Azure et d'une base de données SQL](#CreateWebsite) ).

1. Ouvrez le fichier `taskmodel.php` en double-cliquant dessus, puis mettez à jour les informations de connexion à la base de données dans la fonction `connect`.

		// DB connection info
		$host = "value of $serverName";
		$user = "value of UID";
		$pwd = "the SQL password you created when creating the website";
		$db = "value of Database";
	
	Save the `taskmodel.php` file.

2. Dans WebMatrix, cliquez sur **Publier**, puis dans la boîte de dialogue **Aperçu de publication**, cliquez sur **Continuer**.

	![WebMatrix - Publish][edit_publish]

3. Accédez à http://[nom de votre site Web].azurewebsites.net/createtable.php pour créer la table `items`.

4. Enfin, accédez à http://[nom de votre site Web].azurewebsites.net/index.php pour lancer l'application.
	
##Modification et republication de votre application

Vous pouvez facilement modifier votre application en modifiant la copie locale du site que vous avez téléchargée précédemment, puis en la republiant, ou en apportant directement vos modifications en mode Distant. Ici, nous allons simplement modifier le titre dans le fichier `index.php`, puis l'enregistrer directement dans le site.

1. Dans WebMatrix, cliquez sur l'onglet Distant de votre site, puis sélectionnez **Ouvrir l'affichage distant**. Ceci ouvrira votre site à distance pour vous permettre de le modifier directement.
	 ![WebMatrix - Open Remote View][OpenRemoteView]
 
2. Double-cliquez sur le fichier `index.php` pour l'ouvrir.
	![WebMatrix - Open index file][Remote_editIndex]

3. Remplacez **My ToDo List** par **My Task List** dans les balises **title** et **h1**, puis enregistrez le fichier.


4. Une fois l'enregistrement terminé, cliquez sur le bouton Démarrer pour afficher les modifications sur le site en direct.
	![WebMatrix - Launch site in Remote][Remote_run]



## Étapes suivantes

Vous savez désormais comment créer et déployer un site Web de WebMatrix vers Azure. Pour en savoir plus sur WebMatrix, consultez les ressources suivantes :

* [WebMatrix pour Azure](http://go.microsoft.com/fwlink/?LinkID=253622&clcid=0x409)

* [Site Web WebMatrix](http://www.microsoft.com/click/services/Redirect2.ashx?CR_CC=200106398)





[install-SQLExpress]: http://www.microsoft.com/fr-fr/download/details.aspx?id=29062
[running-app]: ./media/web-sites-php-sql-database-use-webmatrix/tasklist_app_windows.png
[tasklist-sqlazure-download]: http://go.microsoft.com/fwlink/?LinkId=252504
[NewWebSite1]: ./media/web-sites-php-sql-database-use-webmatrix/NewWebSite1.jpg
[NewWebSite2]: ./media/web-sites-php-sql-database-use-webmatrix/NewWebSite2.png
[NewWebSite3_SQL]: ./media/web-sites-php-sql-database-use-webmatrix/NewWebSite3_SQL.png
[NewWebSite4_SQL]: ./media/web-sites-php-sql-database-use-webmatrix/NewWebSite4_SQL.png

[NewWebSite6_SQL]: ./media/web-sites-php-sql-database-use-webmatrix/NewWebSite6_SQL.png
[NewWebSite7]: ./media/web-sites-php-sql-database-use-webmatrix/NewWebSite7.png

[InstallWebMatrix]: ./media/web-sites-php-sql-database-use-webmatrix/InstallWebMatrix.png
[download-site]: ./media/web-sites-php-sql-database-use-webmatrix/download-site-1.png
[site-from-template]: ./media/web-sites-php-sql-database-use-webmatrix/site-from-template.png
[site-from-template-2]: ./media/web-sites-php-sql-database-use-webmatrix/site-from-template-2.png
[edit_addexisting]: ./media/web-sites-php-sql-database-use-webmatrix/edit_addexisting.png
[edit_run]: ./media/web-sites-php-sql-database-use-webmatrix/edit_run.png
[edit_publish]: ./media/web-sites-php-sql-database-use-webmatrix/edit_publish.png
[OpenRemoteView]: ./media/web-sites-php-sql-database-use-webmatrix/OpenRemoteView.png
[Remote_editIndex]: ./media/web-sites-php-sql-database-use-webmatrix/Remote_editIndex.png
[Remote_run]: ./media/web-sites-php-sql-database-use-webmatrix/Remote_run.png





















[preview-portal]: https://manage.windowsazure.com










<!--HONumber=42-->
