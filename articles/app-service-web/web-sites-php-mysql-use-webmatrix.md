<properties 
	pageTitle="Créer et déployer une application web PHP-MySQL dans Azure App Service à l’aide de WebMatrix" 
	description="Didacticiel expliquant comment utiliser l’IDE WebMatrix gratuit pour créer et déployer une application web PHP dans Azure App Service, qui stocke les données dans MySQL."
	tags="azure-portal" 
	services="app-service\web" 
	documentationCenter="php" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="PHP" 
	ms.topic="article" 
	ms.date="07/07/2015" 
	ms.author="tomfitz"/>





# Créer et déployer une application web PHP-MySQL dans Azure App Service à l’aide de WebMatrix

Ce didacticiel explique comment utiliser WebMatrix pour développer et déployer une application PHP-MySQL dans [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714) Web Apps. WebMatrix est un outil de développement web gratuit de Microsoft doté de tous les éléments nécessaires pour développer un site web. WebMatrix prend en charge le PHP et inclut Intellisence pour le développement PHP.

Ce didacticiel part du principe que vous avez installé [MySQL][install-mysql] sur votre ordinateur pour pouvoir tester une application en local. Cependant, cette installation n'est pas obligatoire pour suivre ce didacticiel. Vous pouvez en effet déployer directement votre application dans Azure App Service Web Apps.

À la fin de ce guide, vous disposerez d’un site web PHP/MySQL exécuté dans Web Apps.
 
Vous apprendrez à effectuer les opérations suivantes :

* création d’un site web dans App Service Web Apps et une base de données MySQL à l’aide du [portail Azure](http://go.microsoft.com/fwlink/?LinkId=529715) ; (PHP étant activé par défaut dans Web Apps, l’exécution de votre code PHP ne requiert aucune action particulière) ;
* développement d'une application PHP avec WebMatrix ;
* publication et republication de votre application dans Web Apps avec WebMatrix.
 
En suivant ce didacticiel, vous allez créer une application Web Tasklist simple dans PHP. Cette application sera hébergée dans App Service Web Apps. Voici une capture d'écran de l'application en cours d'exécution :

![Site Web PHP Azure][running-app]

[AZURE.INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]

>[AZURE.NOTE]Si vous voulez vous familiariser avec Azure App Service avant d’ouvrir un compte Azure, accédez à la page [Essayer App Service](http://go.microsoft.com/fwlink/?LinkId=523751). Vous pourrez créer immédiatement une application web temporaire dans App Service. Aucune carte de crédit n’est requise ; vous ne prenez aucun engagement.

##Configuration requise

1. [Téléchargez][tasklist-mysql-download] les fichiers de l'application Tasklist. Il s'agit d'une application PHP simple qui vous permet d'ajouter, de marquer comme terminés et de supprimer des éléments d'une liste de tâches. Les éléments d'une liste de tâches sont stockés dans une base de données MySQL. L'application se compose des fichiers suivants :

	* **additem.php** : ajoute un élément à la liste.
	* **createtable.php** : crée la table MySQL de l’application. Ce fichier sera appelé une seule fois.
	* **deleteitem.php** : supprime un élément.
	* **getitems.php** : obtient tous les éléments de la base de données.
	* **index.php** : affiche les tâches et fournit un formulaire permettant d’ajouter un élément à la liste.
	* **markitemcomplete.php** : définit l’état d’un élément sur Terminé.
	* **taskmodel.php**:  contient les fonctionnalités permettant d’ajouter, d’obtenir, de mettre à jour et de supprimer des éléments de la base de données.

1. Créez une base de données MySQL locale nommée `tasklist`. Pour cela, vous pouvez utiliser l'espace de travail de base de données dans WebMatrix (après l'avoir installé, comme expliqué plus bas), ou une invite de commande MySQL comportant cette commande :

		mysql> create database tasklist;

	Suivez cette procédure seulement si vous voulez tester votre application en local.

## Création d’une application web et d’une base de données MySQL

Pour créer une application web et une base de données MySQL, suivez la procédure ci-après :

1. Connectez-vous au [portail Azure](https://portal.azure.com).

2. Cliquez sur l'icône **New** dans le coin inférieur gauche du portail.

	![Créer un site Web Azure](./media/web-sites-php-mysql-use-webmatrix/new_website2.png)

3. Cliquez sur **Web et mobilité**, puis sur **Application web et MySQL**.

	![Créer un site Web personnalisé](./media/web-sites-php-mysql-use-webmatrix/create_web_mysql.png)

4. Entrez un nom valide pour votre groupe de ressources.

    ![Définir le nom du groupe de ressources](./media/web-sites-php-mysql-use-webmatrix/set_group.png)

5. Entrez des valeurs pour votre nouvelle application web.

    ![Créer une application web](./media/web-sites-php-mysql-use-webmatrix/create_wa.png)

6. Entrez des valeurs pour votre nouvelle base de données, notamment l’acceptation des conditions juridiques.

	![Créer une base de données MySQL](./media/web-sites-php-mysql-use-webmatrix/create_db.png)

	Une fois que l’application web a été créée, vous voyez apparaître le nouveau groupe de ressources.

## Obtention des informations de connexion MySQL distantes

Pour vous connecter à la base de données MySQL qui s’exécute dans Web Apps, vous devez disposer des informations de connexion. Pour obtenir vos informations de connexion MySQL, procédez comme suit :

1. À partir de votre groupe de ressources, cliquez sur la base de données :

	![Sélectionner la base de données](./media/web-sites-php-mysql-use-webmatrix/select_database.png)

2. Dans le résumé de la base de données, sélectionnez **Propriétés**.

    ![Sélectionner les propriétés](./media/web-sites-php-mysql-use-webmatrix/select_properties.png)

2. Notez les valeurs de `Database`, `Host`, `User Id` et `Password`.

    ![Noter les propriétés](./media/web-sites-php-mysql-use-webmatrix/note-properties.png)

## Créer votre application dans WebMatrix

Durant les prochaines étapes, vous allez développer l'application Tasklist en ajoutant les fichiers téléchargés auparavant et en apportant quelques modifications. Cependant, vous pouvez ajouter vos propres fichiers ou en créer.

1. Lancez [Microsoft WebMatrix](http://www.microsoft.com/web/webmatrix/). Si ce n’est déjà fait, installez-le maintenant.
2. Si vous utilisez WebMatrix 3 pour la première fois, vous êtes invité à vous connecter à Azure. Sinon, vous pouvez cliquer sur le bouton **Se connecter**, puis choisir **Ajouter un compte**. Choisissez **Se connecter** à l'aide de votre compte Microsoft.

	![Ajouter un compte](./media/web-sites-php-mysql-use-webmatrix/webmatrix-add-account.png)

3. Si vous disposez d'un compte Azure, vous pouvez vous connecter avec votre compte Microsoft :

	![Connexion à Azure](./media/web-sites-php-mysql-use-webmatrix/webmatrix-sign-in.png)

1. Sur l'écran d'accueil, cliquez sur le bouton **Nouveau**, puis choisissez **Galerie de modèles** pour créer un site à partir de la galerie de modèles :

	![Nouveau site à partir de la galerie de modèles](./media/web-sites-php-mysql-use-webmatrix/webmatrix-site-from-template.png)

4. Parmi les modèles disponibles, sélectionnez **PHP**.

	![Site à partir d'un modèle][site-from-template]

5. Sélectionnez le modèle **Site vide**. Nommez le site, puis cliquez sur **SUIVANT**.

	![Nommer le site][site-from-template-2]

	WebMatrix ouvre votre site ainsi que certains fichiers par défaut.

	Durant les prochaines étapes, vous allez développer l'application Tasklist en ajoutant les fichiers téléchargés auparavant et en apportant quelques modifications. Cependant, vous pouvez ajouter vos propres fichiers ou en créer.

6. Ajoutez vos fichiers d'application en cliquant sur **Ajouter des fichiers existants** :

	![Ajouter des fichiers existants dans WebMatrix][edit_addexisting]

	Dans la boîte de dialogue qui s'affiche, accédez aux fichiers téléchargés auparavant, sélectionnez-les tous, puis cliquez sur Ouvrir. Lorsque vous y êtes invité, remplacez le fichier `index.php`.

7. Ensuite, ajoutez les informations de connexion à votre base de données MySQL locale dans le fichier `taskmodel.php`. Ouvrez le fichier `taskmodel.php` en double-cliquant dessus et mettez à jour les informations de connexion à la base de données dans la fonction `connect`. (** Remarque** : passez à la section [Publication de votre application](#Publish) si vous ne souhaitez pas la tester localement et si vous préférez la publier directement dans Azure App Service Web Apps.)

		// DB connection info
		$host = "localhost";
		$user = "your user name";
		$pwd = "your password";
		$db = "tasklist";

	Enregistrez le fichier `taskmodel.php`.

8. Pour exécuter l’application, vous devez créer la table `items`. Cliquez avec le bouton droit sur le fichier `createtable.php`, puis sélectionnez **Lancer dans le navigateur**. Ceci lance `createtable.php` dans votre navigateur et exécute le code qui crée la table `items` dans la base de données `tasklist`.

	![Lancer createtable.php dans le navigateur WebMatrix][edit_run]

9. À présent, vous pouvez tester l'application en local. Cliquez avec le bouton droit sur le fichier `index.php`, puis sélectionnez **Lancer dans le navigateur**. Testez l'application en ajoutant des éléments, en les marquant comme terminés et en les supprimant.


## Publication de votre application

Avant de publier votre application dans App Service Web Apps, vous devez mettre à jour les informations de connexion à la base de données dans `taskmodel.php` en utilisant les informations de connexion que vous avez obtenues précédemment (dans la section [Création d’une application web et d’une base de données MySQL](#CreateWebsite)).

1. Ouvrez le fichier `taskmodel.php` en double-cliquant dessus, puis mettez à jour les informations de connexion à la base de données dans la fonction `connect`.

		// DB connection info
		$host = "value of Data Source";
		$user = "value of User Id";
		$pwd = "value of Password";
		$db = "value of Database";
	
	Enregistrez le fichier `taskmodel.php`.

2. Cliquez sur **Publier** dans WebMatrix.

	![Publier dans WebMatrix][edit_publish]

3. Cliquez sur **Choisir un site existant dans Microsoft Azure**.

3. Sélectionnez l’application web App Service créée précédemment.

	![](./media/web-sites-php-mysql-use-webmatrix/webmatrix-publish-existing-site-choose.png)

3. Continuez à cliquer sur **Continuer** jusqu’à ce que WebMatrix publie le site dans Azure App Service Web Apps.

3. Accédez à http://[yournom du site web].azurewebsites.net/createtable.php pour créer la table `items`.

4. Enfin, accédez à http://[yournom du site web].azurewebsites.net/index.php pour utiliser l’application.
	
##Modification et republication de votre application

Vous pouvez facilement modifier votre application en modifiant la copie locale du site que vous avez téléchargée précédemment, puis en la republiant, ou en apportant directement vos modifications en mode Distant. Ici, nous allons simplement modifier le titre dans le fichier `index.php`, puis l’enregistrer directement dans le site.

1. Dans WebMatrix, cliquez sur l'onglet Distant de votre site, puis sélectionnez **Ouvrir l'affichage distant**. Ceci ouvre votre site distant pour vous permettre de le modifier directement. ![Ouvrir l'affichage distant dans WebMatrix][OpenRemoteView]
 
2. Ouvrez le fichier `index.php` en double-cliquant dessus. ![Ouvrir le fichier index dans WebMatrix][Remote_editIndex]

3. Remplacez **My ToDo List** par **My Task List** dans les balises **title** et **h1**, puis enregistrez le fichier.


4. Une fois l’enregistrement terminé, cliquez sur le bouton Exécuter pour afficher les modifications sur le site en direct. ![Démarrer le site en mode Distant dans WebMatrix][Remote_run]


## Étapes suivantes

* [Site web WebMatrix](http://www.microsoft.com/click/services/Redirect2.ashx?CR_CC=200106398)

## Changements apportés
* Pour obtenir un guide présentant les modifications apportées dans le cadre de la transition entre Sites Web et App Service, consultez la page [Azure App Service et les services Azure existants](http://go.microsoft.com/fwlink/?LinkId=529714).
* Pour obtenir un guide présentant les modifications apportées dans le cadre de la transition entre l’ancien et le nouveau portail, consultez la page [Références sur la navigation dans le portail Azure](http://go.microsoft.com/fwlink/?LinkId=529715).




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













 

<!---HONumber=July15_HO4-->