<properties 
	pageTitle="Site Web PHP avec stockage de tables - Didacticiel Azure" 
	description="Ce didacticiel vous montre comment créer un site Web PHP et utiliser le service de stockage de tables Azure sur le serveur principal." 
	services="web-sites, storage" 
	documentationCenter="php" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="PHP" 
	ms.topic="article" 
	ms.date="11/21/2014" 
	ms.author="tomfitz"/>

#Création d'un site Web PHP avec Azure Storage

Ce didacticiel vous montre comment créer un site Web PHP et utiliser le service de stockage de tables Azure sur le serveur principal. Il part du principe que vous avez installé [PHP][install-php] et un serveur Web sur votre ordinateur. Les instructions de ce didacticiel s'appliquent à n'importe quel système d'exploitation, notamment Windows, Mac et Linux. À la fin de ce guide, vous disposerez d'un site Web PHP s'exécutant dans Azure et capable d'accéder au service de stockage de tables.
 
Vous apprendrez à effectuer les opérations suivantes :

* installer les bibliothèques clientes Azure et les ajouter dans votre application ;
* utiliser les bibliothèques clientes pour créer des tables, et créer des entités de la table, exécuter des requêtes sur celles-ci ou les supprimer ;
* créer un compte Azure Storage et configurer votre application afin de l'utiliser ;
* créer un site Web Azure et le déployer avec Git.
 
Vous allez créer une application Web Tasklist simple dans PHP. Voici une capture d'écran de l'application terminée :

![Azure PHP web site][ws-storage-app]

[AZURE.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

##Installation des bibliothèques clientes Azure

Pour installer manuellement les bibliothèques clientes PHP pour Azure via Composer, procédez comme suit :

1. [Installez Git][install-git]

	> [AZURE.NOTE]
	> Sous Windows, vous devez aussi ajouter l'exécutable Git à votre variable d'environnement PATH.

2. Créez un fichier nommé **composer.json** à la racine de votre projet et ajoutez-y le code suivant :

		{
			"require": {
				"microsoft/windowsazure": "*"
			},			
			"repositories": [
				{
					"type": "pear",
					"url": "http://pear.php.net"
				}
			],
			"minimum-stability": "dev"
		}

3. Téléchargez **[composer.phar][composer-phar]** à la racine du projet.

4. Ouvrez une invite de commandes et exécutez cette commande à la racine du projet :

		php composer.phar install

##Prise en main des bibliothèques clientes

Il convient d'exécuter quatre étapes de base avant de pouvoir passer un appel à une API Azure lorsque vous utilisez les bibliothèques. Vous allez créer un script d'initialisation qui effectuera ces étapes.

* Créez un fichier nommé **init.php**.

* Commencez par inclure le script du chargeur automatique :

		require_once 'vendor\autoload.php'; 
	
* Incluez les espaces de noms que vous allez utiliser.

	Pour créer un client de service Azure, vous devez utiliser la classe **ServicesBuilder** :

		use WindowsAzure\Common\ServicesBuilder;

	Pour intercepter des exceptions produites par un appel d'API, vous avez besoin de la classe **ServiceException** :

		use WindowsAzure\Common\ServiceException;
	
* Pour instancier le client du service, vous devez également disposer d'une chaîne de connexion valide. Le format de la chaîne de connexion du service de Table est le suivant :

	Pour accéder à un service en ligne :
	
		DefaultEndpointsProtocol=[http|https];AccountName=[yourAccount];AccountKey=[yourKey]
	
	Pour accéder au stockage de l'émulateur :
	
		UseDevelopmentStorage=true

* Utilisez la méthode de fabrique `ServicesBuilder::createTableService` pour instancier un wrapper autour des appels du service de Table.

		$tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);
	
	`$tableRestProxy` contains a method for every REST call available on Azure Tables.


## Création d'une table

Avant de stocker des données, vous devez créer le conteneur qui remplira ce rôle, à savoir la table. 

* Créez un fichier nommé **createtable.php**.

* Commencez par inclure le script d'initialisation que vous venez de créer. Vous ferez cela dans chaque fichier qui accède à Azure :

		<?php
		require_once "init.php";

* Effectuez ensuite un appel à *createTable* en transmettant le nom de la table. À l'instar d'autres magasins de tables NoSQL, aucun schéma n'est requis pour les tables Azure.
	
		try	{
			$tableRestProxy->createTable('tasks');
		}
		catch(ServiceException $e){
			$code = $e->getCode();
			$error_message = $e->getMessage();
		    echo $code.": ".$error_message."<br />";
		}
		?>

	Vous trouverez les descriptions des codes et messages d'erreur sur cette page : [http://msdn.microsoft.com/library/windowsazure/dd179438.aspx][msdn-errors]


##Exécution d'une requête sur une table

La page d'accueil de l'application Tasklist doit répertorier toutes les tâches existantes et permettre l'insertion de nouvelles tâches.

* Créez un fichier nommé **index.php** et insérez le code HTML et PHP suivant qui formera l'en-tête de la page :
	
		<html>
		<head>
			<title>Index</title>
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
		<h1>My ToDo List <font color="grey" size="5">(powered by PHP and Azure Tables) </font></h1>
		<?php		
		require_once "init.php";

* Pour exécuter une requête sur les tables Azure concernant **toutes les entités** stockées dans la table *tasks*, vous appelez la méthode *queryEntities* en transmettant uniquement le nom de la table. Dans la section **Mise à jour d'une entité** ci-dessous, vous allez découvrir comment transférer un filtre en exécutant une requête pour une entité spécifique.

		try {
		    $result = $tableRestProxy->queryEntities('tasks');
		}
		catch(ServiceException $e){
		    $code = $e->getCode();
		    $error_message = $e->getMessage();
		    echo $code.": ".$error_message."<br />";
		}
		
* Pour itérer sur les entités dans le jeu de résultats :

		$entities = $result->getEntities();
			
		for ($i = 0; $i < count($entities); $i++) {

* Une fois que vous avez obtenu un objet `Entity`, le modèle permettant de lire des données est `Entity->getPropertyValue('[name]')` :

			if ($i == 0) {
				echo "<table border='1'>
				<tr>
					<td>Name</td>
					<td>Category</td>
					<td>Date</td>
					<td>Mark Complete?</td>
					<td>Delete?</td>
				</tr>";
			}
			echo "
				<tr>
					<td>".$entities[$i]->getPropertyValue('name')."</td>
					<td>".$entities[$i]->getPropertyValue('category')."</td>
					<td>".$entities[$i]->getPropertyValue('date')."</td>";
					if ($entities[$i]->getPropertyValue('complete') == false)
						echo "<td><a href='markitem.php?complete=true&pk=".$entities[$i]->getPartitionKey()."&rk=".$entities[$i]->getRowKey()."'>Mark Complete</a></td>";
					else
						echo "<td><a href='markitem.php?complete=false&pk=".$entities[$i]->getPartitionKey()."&rk=".$entities[$i]->getRowKey()."'>Unmark Complete</a></td>";
					echo "
					<td><a href='deleteitem.php?pk=".$entities[$i]->getPartitionKey()."&rk=".$entities[$i]->getRowKey()."'>Delete</a></td>
				</tr>";
		}
	
		if ($i > 0)
			echo "</table>";
		else
			echo "<h3>No items on list.</h3>";
		?>

* Last, you must insert the form that feeds data into the task insertion script and complete the HTML:

			<hr/>
			<form action="additem.php" method="post">
				<table border="1">
					<tr>
						<td>Item Name: </td>
						<td><input name="itemname" type="textbox"/></td>
					</tr>
					<tr>
						<td>Category: </td>
						<td><input name="category" type="textbox"/></td>
					</tr>
					<tr>
						<td>Date: </td>
						<td><input name="date" type="textbox"/></td>
					</tr>
				</table>
				<input type="submit" value="Add item"/>
			</form>
		</body>
		</html>

## Insertion des entités dans une table

Votre application peut maintenant lire tous les éléments stockés dans la table. Dans la mesure où la base de données ne contient aucun élément au début, ajoutons une fonction qui écrit des données dans la base de données.

* Créez un fichier nommé **additem.php**.

* Ajoutez le code suivant au fichier :

		<?php		
		require_once "init.php";		
		use WindowsAzure\Table\Models\Entity;
		use WindowsAzure\Table\Models\EdmType;		

* La première étape d'insertion d'une entité est l'instanciation d'un objet `Entity` et la définition de ses propriétés :
		
		$entity = new Entity();
		$entity->setPartitionKey('p1');
		$entity->setRowKey((string) microtime(true));
		$entity->addProperty('name', EdmType::STRING, $_POST['itemname']);
		$entity->addProperty('category', EdmType::STRING, $_POST['category']);
		$entity->addProperty('date', EdmType::STRING, $_POST['date']);
		$entity->addProperty('complete', EdmType::BOOLEAN, false);

* Vous pouvez alors transmettre l'objet`$entity` que vous venez de créer à la méthode `insertEntity` :

		try{
			$tableRestProxy->insertEntity('tasks', $entity);
		}
		catch(ServiceException $e){
			$code = $e->getCode();
			$error_message = $e->getMessage();
		    echo $code.": ".$error_message."<br />";
		}

* Enfin, pour revenir à la page d'accueil après l'insertion de l'entité :

		header('Location: index.php');		
		?>
	
## Mise à jour d'une entité

L'application de liste de tâches est capable de marquer un élément comme étant terminé et d'annuler également cette action. La page d'accueil transmet le *RowKey* et *PartitionKey* d'une entité et l'état cible (marked==1, unmarked==0).

* Créez un fichier nommé **markitem.php** et ajoutez la portion d'initialisation :

		<?php		
		require_once "init.php";
		

* La première étape de mise à jour d'une entité consiste à récupérer celle-ci dans la table :
		
		$result = $tableRestProxy->queryEntities('tasks', 'PartitionKey eq \''.$_GET['pk'].'\' and RowKey eq \''.$_GET['rk'].'\'');		
		$entities = $result->getEntities();		
		$entity = $entities[0];

	Comme vous pouvez le constater, le filtre d'exécution de la requête transmis revêt la forme `Key eq 'Value'`. Vous trouverez [ici][msdn-table-query-syntax] la description complète de la syntaxe de la requête.

* Vous pouvez ensuite modifier les propriétés :

		$entity->setPropertyValue('complete', ($_GET['complete'] == 'true') ? true : false);

* La méthode `updateEntity` exécute ensuite la mise à jour :

		try{
			$result = $tableRestProxy->updateEntity('tasks', $entity);
		}
		catch(ServiceException $e){
			$code = $e->getCode();
			$error_message = $e->getMessage();
		    echo $code.": ".$error_message."<br />";
		}

* Pour revenir à la page d'accueil après l'insertion de l'entité :

		header('Location: index.php');		
		?>


## Suppression d'une entité

La suppression d'un élément est réalisée par un appel unique à `deleteItem`. Les valeurs transmises sont **PartitionKey** et **RowKey**, qui composent la clé primaire de l'entité. Créez un fichier nommé **deleteitem.php** et insérez le code suivant :

		<?php
		
		require_once "init.php";		
		$tableRestProxy->deleteEntity('tasks', $_GET['pk'], $_GET['rk']);		
		header('Location: index.php');
		
		?>


## Création d'un compte Azure Storage

Afin que votre application stocke des données dans le cloud, vous devez créer au préalable un compte de stockage dans Azure, puis transmettre les informations d'authentification adéquates à la classe *Configuration*.

1. Connectez-vous au [Portail de gestion Azure][management-portal].

2. Cliquez sur l'icône **+ Nouveau** dans le coin inférieur gauche du portail.

	![Create New Azure web site][new-website]

3. Cliquez sur **Services de données**, **Stockage**, puis sur **Création rapide**.

	![Custom Create a new web site][storage-quick-create]
	
	Entrez une valeur pour **URL**, puis sélectionnez le centre de données de votre site Web dans la liste déroulante **REGION**. Cliquez sur le bouton **Créer un compte de stockage** au bas de la boîte de dialogue.

	![Fill in web site details][storage-quick-create-details]

	Quand le compte de stockage est créé, le texte **La création du compte de stockage " [NOM] " s'est terminée correctement** apparaît.

4. Vérifiez que l'onglet **Stockage** est sélectionné, puis sélectionnez le compte de stockage que vous venez de créer dans la liste.

5. Cliquez sur **Gérer les clés d'accès** dans la barre d'application au bas de la page.

	![Select Manage Keys][storage-manage-keys]

6. Notez le nom du compte de stockage que vous avez créé et le nom de la clé primaire.

	![Select Manage Keys][storage-access-keys]

7. Ouvrez **init.php** et remplacez `[YOUR_STORAGE_ACCOUNT_NAME]` et `[YOUR_STORAGE_ACCOUNT_KEY]` par le nom du compte et le nom de la clé que vous avez notés lors de l'étape précédente. Enregistrez le fichier.


## Création d'un site Web Azure et configuration de la publication Git

Pour créer un site Web Azure, procédez comme suit :

1. Connectez-vous au [Portail de gestion Azure][management-portal].
2. Cliquez sur l'icône **+ Nouveau** dans le coin inférieur gauche du portail.

	![Create New Azure Web Site][new-website]

3. Cliquez sur **Calcul**, sur **Site Web**, puis sur **Création rapide**.

	![Custom Create a new web site][website-quick-create]
	
	Entrez une valeur pour **URL**, puis sélectionnez le centre de données de votre site Web dans la liste déroulante **REGION**. Cliquez sur le bouton **Créer un site Web** en bas de la boîte de dialogue.

	![Fill in web site details][website-quick-create-details]

	Quand le site Web est créé, le texte " **La création du site Web[NOM DU SITE] s'est terminée correctement "** apparaît. Vous pouvez maintenant activer la publication Git.

5. Cliquez sur le nom du site Web affiché dans la liste des sites Web pour ouvrir le tableau de bord **DÉMARRAGE RAPIDE** du site Web.

	![Open web site dashboard][go-to-dashboard]


6. Dans le coin inférieur droit de la page de démarrage rapide, sélectionnez **Configurer le déploiement à partir du contrôle de code source**.

	![Set up Git publishing][setup-git-publishing]

6. À la question " Où est votre code source ? ", sélectionnez **Référentiel Git local**, puis cliquez sur la flèche.

	![where is your source code][where-is-code]

7. Pour activer la publication Git, vous devez fournir un nom d'utilisateur et un mot de passe. Notez le nom d'utilisateur et le mot de passe que vous créez. (si vous avez déjà configuré un référentiel Git, ignorez cette étape).

	![Create publishing credentials][credentials]

	La configuration du référentiel prend quelques secondes.

8. Lorsque le référentiel Git est prêt, vous pouvez consulter les instructions des commandes Git pour la configuration d'un référentiel local et la publication des fichiers dans Azure.

	![Git deployment instructions returned after creating a repository for the website.][git-instructions]

	Notez les instructions, car elles seront utilisées dans la prochaine section pour la publication de l'application.

##Publication de votre application

Pour exécuter l'application avec Git, procédez comme suit :

1. Ouvrez le dossier **vendor/microsoft/windowsazure** sous la racine de l'application, puis supprimez les fichiers et dossiers suivants :
	* .git
	* .gitattributes
	* .gitignore
			
	Lorsque le gestionnaire de package Composer télécharge les bibliothèques clientes Azure et leurs dépendances, il procède par clonage du référentiel GitHub où elles résident. Au cours de la prochaine étape, l'application sera déployée via Git avec la création d'un référentiel à partir du dossier racine de celle-ci. Git ignorera le sous-référentiel dans lequel résident les bibliothèques clientes à moins que les fichiers propres au référentiel ne soient supprimés.

2. Ouvrez GitBash (ou un terminal, si Git est dans votre `PATH`), remplacez les répertoires du répertoire racine de votre application, puis exécutez les commandes suivantes (**Remarque :** cette procédure est identique à celle indiquée à la fin de la section **Création d'un site Web Azure et configuration de la publication Git**) :

		git init
		git add .
		git commit -m "initial commit"
		git remote add azure [URL for remote repository]
		git push azure master

	Vous êtes invité à entrer le mot de passe que vous avez créé précédemment.

3. Accédez à **http://[votre domaine de site Web]/createtable.php** pour créer la table de l'application.
4. Accédez à **http://[votre domaine de site Web]/index.php** pour commencer à utiliser l'application.

Après la publication de votre application, vous pouvez y apporter des modifications, puis utiliser Git pour les publier. 

##Publication des modifications apportées à votre application

Pour publier des modifications apportées à votre application, procédez comme suit :

1. Modifiez votre application en local.
2. Ouvrez GitBash (ou un terminal, si Git est dans votre `PATH`), remplacez les répertoires du répertoire racine de votre application, puis exécutez les commandes suivantes :

		git add .
		git commit -m "comment describing changes"
		git push azure master

	Vous êtes invité à entrer le mot de passe que vous avez créé précédemment.

3. Accédez à **http://[votre domaine de site Web]/index.php** pour voir vos modifications. 

[install-php]: http://www.php.net/manual/en/install.php


[install-git]: http://git-scm.com/book/en/Getting-Started-Installing-Git
[composer-phar]: http://getcomposer.org/composer.phar

[msdn-errors]: http://msdn.microsoft.com/library/windowsazure/dd179438.aspx



[msdn-table-query-syntax]: http://msdn.microsoft.com/library/windowsazure/dd894031.aspx
[ws-storage-app]: ./media/web-sites-php-storage/ws-storage-app.png
[management-portal]: https://manage.windowsazure.com
[new-website]: ./media/web-sites-php-storage/new_website.jpg

[website-quick-create]: ./media/web-sites-php-storage/createsite.png
[website-quick-create-details]: ./media/web-sites-php-storage/sitedetails.png
[storage-quick-create]: ./media/web-sites-php-storage/createstorage.png
[storage-quick-create-details]: ./media/web-sites-php-storage/provideurl.png
[storage-manage-keys]: ./media/web-sites-php-storage/accesskeys.png
[storage-access-keys]: ./media/web-sites-php-storage/keydetails.png

[go-to-dashboard]: ./media/web-sites-php-storage/selectsite.png
[setup-git-publishing]: ./media/web-sites-php-storage/setup_git_publishing.png
[credentials]: ./media/web-sites-php-storage/git-deployment-credentials.png


[git-instructions]: ./media/web-sites-php-storage/git-instructions.png
[where-is-code]: ./media/web-sites-php-storage/where_is_code.png



<!--HONumber=42-->
