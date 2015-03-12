<properties 
	pageTitle="Site Web PHP avec MySQL et WebMatrix - Didacticiel Azure" 
	description="Un didacticiel qui vous montre comment utiliser l'IDE WebMatrix gratuite pour créer et déployer un site Web PHP qui stocke les données dans MySQL." 
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





#Création et déploiement d'un site Web Azure PHP-MySQL avec WebMatrix

Ce didacticiel vous explique comment utiliser WebMatrix pour développer et déployer une application PHP-MySQL sur un site Web Azure. WebMatrix est un outil de développement Web gratuit de Microsoft doté de tous les éléments nécessaires pour développer un site Web. WebMatrix prend en charge le PHP et inclut Intellisence pour le développement PHP.

Ce didacticiel part du principe que vous avez installé [MySQL][install-mysql] sur votre ordinateur pour pouvoir tester une application en local. Cependant, cette installation n'est pas obligatoire pour suivre ce didacticiel. Vous pouvez en effet déployer votre application directement sur Sites Web Azure.

À la fin de ce guide, vous disposerez d'un site Web PHP-MySQL exécuté dans Azure.
 
Vous apprendrez à effectuer les opérations suivantes :

* création d'un site Web Azure et d'une base de données MySQL à l'aide du portail de gestion. Comme PHP est activé par défaut dans Sites Web Azure, aucune action particulière n'est requise pour exécuter votre code PHP.
* développement d'une application PHP avec WebMatrix ;
* publication et republication de votre application dans Azure avec WebMatrix.
 
En suivant ce didacticiel, vous allez créer une application Web Tasklist simple dans PHP. L'application est hébergée dans un site Web Azure. Voici une capture d'écran de l'application en cours d'exécution :

![Azure PHP Web Site][running-app]

> [AZURE.NOTE]
> Pour effectuer ce didacticiel, vous avez besoin d'un compte Azure. Vous pouvez <a href="http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/">activer les avantages de votre abonnement MSDN</a> ou <a href="http://azure.microsoft.com/pricing/free-trial/">obtenir une version d'évaluation gratuite</a>.
> 
> Si vous voulez prendre en main Sites Web Azure avant de créer un compte, accédez à <a href="https://trywebsites.azurewebsites.net/?language=php">https://trywebsites.azurewebsites.net</a>, où vous pouvez immédiatement et gratuitement créer un site de départ ASP.NET de courte durée dans Sites Web Azure. Aucun numéro de carte de crédit, ni engagement de quelque sorte ne sont exigés.

##Configuration requise

1. [Téléchargez][tasklist-mysql-download] les fichiers de l'application Tasklist. Il s'agit d'une application PHP simple qui vous permet d'ajouter, de marquer comme terminés et de supprimer des éléments d'une liste de tâches. Les éléments de la liste des tâches sont stockés dans une base de données MySQL. L'application se compose des fichiers suivants :

	* **additem.php** : ajoute un élément à la liste.
	* **createtable.php** : crée la table MySQL pour l'application. Ce fichier sera appelé une seule fois.
	* **deleteitem.php** : supprime un élément.
	* **getitems.php** : obtient tous les éléments de la base de données.
	* **index.php** : affiche les tâches et fournit un formulaire pour ajouter un élément à la liste.
	* **markitemcomplete.php** : définit l'état d'un élément sur Terminé.
	* **taskmodel.php** : contient les fonctionnalités permettant d'ajouter, d'obtenir, de mettre à jour et de supprimer des éléments de la base de données.

1. Créez une base de données MySQL locale intitulée `tasklist`. Vous pouvez le faire soit depuis l'espace de travail des bases de données dans WebMatrix (après son installation, comme décrit plus loin dans ce didacticiel), soit depuis l'invite de commandes MySQL avec la commande suivante :

		mysql> create database tasklist;

	Suivez cette procédure seulement si vous voulez tester votre application en local.

<h2><a id="CreateWebsite"></a>Création d'un site Web Azure et d'une base de données MySQL</h2>

1. Connectez-vous au [portail de gestion][preview-portal].
1. Cliquez sur l'icône **+ Nouveau** dans le coin inférieur gauche du portail.

	![Create New Azure Web Site][NewWebSite1]

1. Cliquez sur **SITE WEB**, puis sur **CRÉATION PERSONNALISÉE**.

	![Custom Create a new Web Site][NewWebSite2]

	> [AZURE.NOTE]
	> Vous ne pouvez pas créer de base de données MySQL pour un site Web après la création de ce dernier. Vous devez créer un site Web et une base de données MySQL comme décrit dans les étapes précédentes.

1. Entrez une valeur pour **URL** et, dans la liste déroulante **BASE DE DONNÉES** sélectionnez **Créer une base de données MySQL**, puis sélectionnez un centre de données pour votre site Web dans la liste déroulante **RÉGION**. Cliquez sur la flèche située en bas de la boîte de dialogue.

	![Fill in web site details][NewWebSite3]

5. Entrez une valeur pour le **NOM** de votre base de données, sélectionnez le centre de données de votre base de données dans la liste déroulante **RÉGION**, puis cochez la case qui indique que vous acceptez les conditions juridiques. Cliquez sur la coche située en bas de la boîte de dialogue.

	![Create new MySQL database][NewWebSite4]

	Quand le site Web est créé, le texte **La création du site Web " [NOM DU SITE] " s'est terminée correctement** apparaît.

	Vous devez ensuite obtenir les informations de connexion MySQL.


6. Cliquez sur le nom du site Web affiché dans la liste des sites Web pour ouvrir la page Démarrage rapide du site Web.

	![Open web site dashboard][NewWebSite5]

7. Cliquez sur l'onglet **CONFIGURER** :

	![Configure tab][NewWebSite6]

8. Faites défiler jusqu'à la section **chaînes de connexion**. Les valeurs de `Database`, `Data Source`, `User Id` et `Password` sont (respectivement) le nom de la base de données, le nom du serveur, le nom d'utilisateur et le mot de passe utilisateur. Notez les informations de connexion à la base de données, car vous en aurez besoin ultérieurement.

	![Connection string][ConnectionString]

##Installation de WebMatrix et développement de votre application

Vous pouvez installer WebMatrix à partir du [portail de gestion][preview-portal]. 

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

	Durant les prochaines étapes, vous allez développer l'application Tasklist en ajoutant les fichiers téléchargés auparavant et en apportant quelques modifications. Cependant, vous pouvez ajouter vos propres fichiers ou en créer.

6. Ajoutez les fichiers de votre application en cliquant sur **Ajouter existant** :

	![WebMatrix - Add existing files][edit_addexisting]

	Dans la boîte de dialogue qui s'affiche, accédez aux fichiers téléchargés auparavant, sélectionnez-les tous, puis cliquez sur Ouvrir. Lorsque vous y êtes invité, remplacez le fichier `index.php`. 

7. Vous devez ensuite ajouter les informations de connexion à votre base de données MySQL locale au fichier `taskmodel.php`. Ouvrez le fichier `taskmodel.php` en double-cliquant dessus, puis mettez à jour les informations de connexion à la base de données dans la fonction `connect`. (**Remarque** : Allez directement à la section [Publier votre application](#Publish) si vous ne voulez pas tester votre application localement, mais plutôt la publier directement dans Sites Web Azure.)

		// DB connection info
		$host = "localhost";
		$user = "your user name";
		$pwd = "your password";
		$db = "tasklist";

	Enregistrez le fichier `taskmodel.php`.

8. Pour exécuter l'application, vous devez créer la table `items`. Cliquez avec le bouton droit sur le fichier `createtable.php`, puis sélectionnez **Lancer dans le navigateur**. Ceci lancera `createtable.php` dans votre navigateur et exécutera le code créant la table `items` dans la base de données `tasklist`.

	![WebMatrix - Launch createtable.php in browser][edit_run]

9. À présent, vous pouvez tester localement l'application. Cliquez avec le bouton droit sur le fichier `index.php`, puis sélectionnez **Lancer dans le navigateur**. Testez l'application en ajoutant des éléments, en les marquant comme terminés et en les supprimant.  


<h2><a id="Publish"></a>Publication de votre application</h2>

Avant de publier votre application dans Sites Web Azure, vous devez mettre à jour les informations de connexion à la base de données dans `taskmodel.php` en utilisant les informations de connexion que vous avez obtenues précédemment (dans la section [Création d'un site Web Azure et d'une base de données MySQ](#CreateWebsite) ).

1. Ouvrez le fichier `taskmodel.php` en double-cliquant dessus, puis mettez à jour les informations de connexion à la base de données dans la fonction `connect`.

		// DB connection info
		$host = "value of Data Source";
		$user = "value of User Id";
		$pwd = "value of Password";
		$db = "value of Database";
	
	Save the `taskmodel.php` file.

2. Dans WebMatrix, cliquez sur **Publier**, puis dans la boîte de dialogue **Aperçu de publication**, cliquez sur **Continuer**.

	![WebMatrix - Publish][edit_publish]

3. Accédez à http://[nom de votre site Web].azurewebsites.net/createtable.php pour créer la table `items`.

4. Enfin, accédez à http://[nom de votre site Web].azurewebsites.net/index.php pour utiliser l'application.
	
##Modification et republication de votre application

Vous pouvez facilement modifier votre application en modifiant la copie locale du site que vous avez téléchargée précédemment, puis en la republiant, ou en apportant directement vos modifications en mode Distant. Ici, nous allons simplement modifier le titre dans le fichier `index.php`, puis l'enregistrer directement dans le site.

1. Dans WebMatrix, cliquez sur l'onglet Distant de votre site, puis sélectionnez **Ouvrir l'affichage distant**. Ceci ouvrira votre site à distance pour vous permettre de le modifier directement.
	 ![WebMatrix - Open Remote View][OpenRemoteView]
 
2. Double-cliquez sur le fichier `index.php` pour l'ouvrir.
	![WebMatrix - Open index file][Remote_editIndex]

3. Remplacez **My ToDo List** par **My Task List** dans les balises **title** et **h1**, puis enregistrez le fichier.


4. Une fois l'enregistrement terminé, cliquez sur le bouton Démarrer pour afficher les modifications sur le site en direct.
	![WebMatrix - Launch site in Remote][Remote_run]


# Étapes suivantes

Vous savez désormais comment créer et déployer un site Web de WebMatrix vers Azure. Pour en savoir plus sur WebMatrix, consultez les ressources suivantes :

* [WebMatrix pour Azure](http://go.microsoft.com/fwlink/?LinkID=253622&clcid=0x409)

* [Site Web WebMatrix](http://www.microsoft.com/click/services/Redirect2.ashx?CR_CC=200106398)





[install-mysql]: http://dev.mysql.com/doc/refman/5.6/en/installing.html
[running-app]: ./media/web-sites-php-mysql-use-webmatrix/tasklist_app_windows.png
[tasklist-mysql-download]: http://go.microsoft.com/fwlink/?LinkId=252506
[NewWebSite1]: ./media/web-sites-php-mysql-use-webmatrix/NewWebSite1.jpg
[NewWebSite2]: ./media/web-sites-php-mysql-use-webmatrix/NewWebSite2.png
[NewWebSite3]: ./media/web-sites-php-mysql-use-webmatrix/NewWebSite3.png
[NewWebSite4]: ./media/web-sites-php-mysql-use-webmatrix/NewWebSite4.png
[NewWebSite5]: ./media/web-sites-php-mysql-use-webmatrix/NewWebSite5.png
[NewWebSite6]: ./media/web-sites-php-mysql-use-webmatrix/NewWebSite6.png
[ConnectionString]: ./media/web-sites-php-mysql-use-webmatrix/ConnectionString.png
[InstallWebMatrix]: ./media/web-sites-php-mysql-use-webmatrix/InstallWebMatrix.png
[download-site]: ./media/web-sites-php-mysql-use-webmatrix/download-site-1.png
[site-from-template]: ./media/web-sites-php-mysql-use-webmatrix/site-from-template.png
[site-from-template-2]: ./media/web-sites-php-mysql-use-webmatrix/site-from-template-2.png
[edit_addexisting]: ./media/web-sites-php-mysql-use-webmatrix/edit_addexisting.png
[edit_run]: ./media/web-sites-php-mysql-use-webmatrix/edit_run.png
[edit_publish]: ./media/web-sites-php-mysql-use-webmatrix/edit_publish.png
[OpenRemoteView]: ./media/web-sites-php-mysql-use-webmatrix/OpenRemoteView.png
[Remote_editIndex]: ./media/web-sites-php-mysql-use-webmatrix/Remote_editIndex.png
[Remote_run]: ./media/web-sites-php-mysql-use-webmatrix/Remote_run.png













[preview-portal]: https://manage.windowsazure.com















<!--HONumber=42-->
