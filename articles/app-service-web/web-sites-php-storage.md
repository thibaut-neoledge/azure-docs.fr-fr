<properties 
	pageTitle="Création d’une application web PHP dans Azure App Service et déploiement à l’aide de Git" 
	description="Ce didacticiel vous explique comment créer une application web PHP dans Azure App Service et comment utiliser le service de stockage de tables Azure sur le serveur principal." 
	services="app-service\web, storage" 
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
	ms.date="07/02/2015" 
	ms.author="tomfitz"/>

# Création d’une application web PHP dans Azure App Service et déploiement à l’aide de Git

Ce didacticiel vous explique comment créer une application web PHP dans [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714) et comment utiliser le service de stockage de tables Azure sur le serveur principal. Il part du principe que vous avez installé [PHP][install-php] et un serveur Web sur votre ordinateur. Les instructions de ce didacticiel s’appliquent à n’importe quel système d’exploitation, notamment Windows, Mac et Linux. À la fin de ce guide, vous disposerez d’une application web PHP s’exécutant dans Azure et capable d’accéder au service de stockage de tables.
 
Vous apprendrez à effectuer les opérations suivantes :

* installer les bibliothèques clientes Azure et les ajouter dans votre application ;
* utiliser les bibliothèques clientes pour créer des tables, et créer des entités de la table, exécuter des requêtes sur celles-ci ou les supprimer ;
* créer un compte Azure Storage et configurer votre application afin de l’utiliser ;
* créer une application web Azure et la déployer à l’aide de Git.
 
Vous allez créer une application web Tasklist simple dans PHP. Voici une capture d’écran de l’application terminée :

![Application web PHP Azure][ws-storage-app]

[AZURE.INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]

>[AZURE.NOTE]Si vous voulez vous familiariser avec Azure App Service avant d’ouvrir un compte Azure, accédez à la page [Essayer App Service](http://go.microsoft.com/fwlink/?LinkId=523751). Vous pourrez créer immédiatement une application web temporaire dans App Service. Aucune carte de crédit n’est requise ; vous ne prenez aucun engagement.

##Installation des bibliothèques clientes Azure

Pour installer manuellement les bibliothèques clientes PHP pour Azure via Composer, procédez comme suit :

1. [Installation de Git][install-git]

	> [AZURE.NOTE]Sous Windows, vous devez aussi ajouter l’exécutable Git à votre variable d’environnement PATH.

2. Créez un fichier nommé **composer.json** à la racine de votre projet et ajoutez-y le code suivant :

         {
             "repositories": [
                 {
                     "type": "pear",
                     "url": "http://pear.php.net"
                 }
             ],
             "require": {
                 "pear-pear.php.net/mail_mime" : "*",
                 "pear-pear.php.net/http_request2" : "*",
                 "pear-pear.php.net/mail_mimedecode" : "*",
                 "microsoft/windowsazure": "*"
             }
         }

3. Téléchargez **[composer.phar][composer-phar]** à la racine du projet.

4. Ouvrez une invite de commandes et exécutez cette commande à la racine du projet :

		php composer.phar install

##Prise en main des bibliothèques clientes

Il convient d'exécuter quatre étapes de base avant de pouvoir passer un appel à une API Azure lorsque vous utilisez les bibliothèques. Vous allez créer un script d'initialisation qui effectuera ces étapes.

* Créez un fichier nommé **init.php**.

* Commencez par inclure le script du chargeur automatique :

		require_once 'vendor\autoload.php'; 
	
* Incluez les espaces de noms que vous allez utiliser.

	Pour créer un client de service Azure, vous devez utiliser la classe **ServicesBuilder** :

		use WindowsAzure\Common\ServicesBuilder;

	Pour intercepter des exceptions produites par un appel d'API, vous avez besoin de la classe **ServiceException** :

		use WindowsAzure\Common\ServiceException;
	
* Pour instancier le client du service, vous devez également disposer d'une chaîne de connexion valide. Le format de la chaîne de connexion du service de Table est le suivant :

	Pour accéder à un service en ligne :
	
		DefaultEndpointsProtocol=[http|https];AccountName=[yourAccount];AccountKey=[yourKey]
	
	Pour accéder au stockage de l’émulateur :
	
		UseDevelopmentStorage=true

* Utilisez la méthode de fabrique `ServicesBuilder::createTableService` pour instancier un wrapper autour des appels du service de Table.

		$tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);
	
	`$tableRestProxy` contient une méthode pour chaque appel REST disponible sur les tables Azure.


## Création d’une table

Avant de stocker des données, vous devez créer le conteneur qui remplira ce rôle, à savoir la table.

* Créez un fichier nommé **createtable.php**.

* Commencez par inclure le script d'initialisation que vous venez de créer. Vous ferez cela dans chaque fichier qui accède à Azure :

		<?php
		require_once "init.php";

* Effectuez ensuite un appel à *createTable* en transmettant le nom de la table. À l’instar d’autres magasins de tables NoSQL, aucun schéma n’est requis pour les tables Azure.
	
		try	{
			$tableRestProxy->createTable('tasks');
		}
		catch(ServiceException $e){
			$code = $e->getCode();
			$error_message = $e->getMessage();
		    echo $code.": ".$error_message."<br />";
		}
		?>

	Les messages et codes d’erreur se trouvent ici : [http://msdn.microsoft.com/library/windowsazure/dd179438.aspx][msdn-errors]


##Exécution d’une requête sur une table

La page d'accueil de l'application Tasklist doit répertorier toutes les tâches existantes et permettre l'insertion de nouvelles tâches.

* Créez un fichier nommé **index.php** et insérez le code HTML et PHP suivant qui formera l'en-tête de la page :
	
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
		
* Pour itérer sur les entités dans le jeu de résultats :

		$entities = $result->getEntities();
			
		for ($i = 0; $i < count($entities); $i++) {

* Une fois que vous avez obtenu un objet `Entity`, le modèle permettant de lire des données est `Entity->getPropertyValue('[name]')` :

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

* Enfin, vous devez insérer le formulaire qui fournit les données au script d’insertion de la tâche et compléter le code HTML :

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

* Ajoutez le code suivant au fichier :

		<?php		
		require_once "init.php";		
		use WindowsAzure\Table\Models\Entity;
		use WindowsAzure\Table\Models\EdmType;		

* La première étape d’insertion d’une entité consiste à instancier un objet `Entity` et à définir ses propriétés :
		
		$entity = new Entity();
		$entity->setPartitionKey('p1');
		$entity->setRowKey((string) microtime(true));
		$entity->addProperty('name', EdmType::STRING, $_POST['itemname']);
		$entity->addProperty('category', EdmType::STRING, $_POST['category']);
		$entity->addProperty('date', EdmType::STRING, $_POST['date']);
		$entity->addProperty('complete', EdmType::BOOLEAN, false);

* Vous pouvez alors transmettre l’objet `$entity` que vous venez de créer à la méthode `insertEntity` :

		try{
			$tableRestProxy->insertEntity('tasks', $entity);
		}
		catch(ServiceException $e){
			$code = $e->getCode();
			$error_message = $e->getMessage();
		    echo $code.": ".$error_message."<br />";
		}

* Enfin, pour revenir à la page d’accueil après l’insertion de l’entité :

		header('Location: index.php');		
		?>
	
## Mise à jour d'une entité

L'application de liste de tâches est capable de marquer un élément comme étant terminé et d'annuler également cette action. La page d'accueil transmet *RowKey* et *PartitionKey* d'une entité et l'état cible (marqué==1, non marqué==0).

* Créez un fichier nommé **markitem.php** et ajoutez la portion d'initialisation :

		<?php		
		require_once "init.php";
		

* La première étape de mise à jour d’une entité consiste à récupérer celle-ci dans la table :
		
		$result = $tableRestProxy->queryEntities('tasks', 'PartitionKey eq ''.$_GET['pk'].'' and RowKey eq ''.$_GET['rk'].''');		
		$entities = $result->getEntities();		
		$entity = $entities[0];

	Comme vous pouvez le constater, le filtre d’exécution de la requête transmis revêt la forme `Key eq 'Value'`. Vous trouverez [ici][msdn-table-query-syntax] la description complète de la syntaxe de la requête.

* Vous pouvez ensuite modifier les propriétés :

		$entity->setPropertyValue('complete', ($_GET['complete'] == 'true') ? true : false);

* La méthode `updateEntity` exécute ensuite la mise à jour :

		try{
			$result = $tableRestProxy->updateEntity('tasks', $entity);
		}
		catch(ServiceException $e){
			$code = $e->getCode();
			$error_message = $e->getMessage();
		    echo $code.": ".$error_message."<br />";
		}

* Pour revenir à la page d’accueil après l’insertion de l’entité :

		header('Location: index.php');		
		?>


## Suppression d’une entité

La suppression d’un élément est réalisée par un appel unique à `deleteItem`. Les valeurs transmises sont **PartitionKey** et **RowKey**, qui composent la clé primaire de l’entité. Créez un fichier nommé **deleteitem.php** et insérez le code suivant :

		<?php
		
		require_once "init.php";		
		$tableRestProxy->deleteEntity('tasks', $_GET['pk'], $_GET['rk']);		
		header('Location: index.php');
		
		?>


## créer un compte Azure Storage

Afin que votre application stocke des données dans le cloud, vous devez créer au préalable un compte de stockage dans Azure, puis transmettre les informations d’authentification adéquates à la classe *Configuration*.

1. Connectez-vous au [portail Azure][management-portal].

2. Cliquez sur l’icône **Nouveau** en bas à gauche du portail, puis cliquez sur **Données + stockage** > **Stockage**. Attribuez un nom unique au compte de stockage et créez un [groupe de ressources](../resource-group-overview.md).

	![Création d’un nouveau compte de stockage][storage-quick-create]
	
	Une fois le compte de stockage créé, le bouton **Notifications** affiche la mention **RÉUSSITE** en vert clignotant et le panneau du compte de stockage s’ouvre pour indiquer qu’il appartient au groupe de ressources créé.

5. Cliquez sur la section **Paramètres** dans le panneau du compte de stockage. Prenez note du nom du compte et de la clé primaire.

	![Sélection des clés de gestion][storage-access-keys]

7. Ouvrez **init.php** et remplacez `[YOUR_STORAGE_ACCOUNT_NAME]` et `[YOUR_STORAGE_ACCOUNT_KEY]` par le nom du compte et le nom de la clé que vous avez notés lors de l’étape précédente. Enregistrez le fichier .

## Création d’une application web Azure et configuration de la publication Git

Suivez cette procédure pour créer une application web Azure :

1. Connectez-vous au [portail Azure][management-portal].

2. Créez une application web vide avec les instructions de la page [Procédure de création d’une application web à l’aide du portail Azure](../web-sites-create-deploy.md#createawebsiteportal). Assurez-vous de créer un [plan App Service](azure-web-sites-web-hosting-plans-in-depth-overview) et de sélectionner le groupe de ressources que vous avez créé précédemment pour le compte de stockage.

	Une fois l’application web créée, le bouton **Notifications** clignote en vert avec la mention **RÉUSSITE** et le panneau de l’application web s’ouvre pour indiquer qu’elle appartient au nouveau groupe de ressources créé.

6. Sur le panneau de l’application web, cliquez sur **Configurer le déploiement continu** et choisissez **Référentiel Git local**. Cliquez sur **OK**.

	![Configuration de la publication Git][setup-git-publishing]

7. Avant de déployer votre référentiel Git local dans Azure, vous devez configurer les informations d’identification de déploiement. Sur le panneau de l’application web, cliquez sur **Tous les paramètres** > **Informations d’identification de déploiement** pour configurer les informations d’identification. Cliquez ensuite sur **Enregistrer**.

	![Création des informations d’identification de publication][credentials]

	La configuration du référentiel prend quelques secondes.

8. Une fois le référentiel Git opérationnel, vous y appliquez vos modifications. Obtenez l’URL du référentiel en cliquant sur la même section de déploiement sur le panneau de l’application web.

	![Instructions de déploiement Git affichées après la création d’un référentiel pour l’application web.][git-instructions]

	Notez les instructions, car elles seront utilisées dans la prochaine section pour la publication de l’application.

##Publication de votre application

Pour exécuter l'application avec Git, procédez comme suit :

1. Ouvrez le dossier **vendor/microsoft/windowsazure** sous la racine de l'application, puis supprimez les fichiers et dossiers suivants :
	* .git
	* .gitattributes
	* .gitignore
			
	Lorsque le gestionnaire de package Composer télécharge les bibliothèques clientes Azure et leurs dépendances, il le fait en clonant le référentiel GitHub dans lequel elles résident. Dans l'étape suivante, l'application sera déployée via Git en créant un référentiel à partir du dossier racine de l'application. Git ignorera le sous-référentiel dans lequel résident les bibliothèques clientes à moins que les fichiers propres au référentiel ne soient supprimés.

2. Ouvrez GitBash (ou un terminal, si Git est dans votre `PATH`), remplacez les répertoires du répertoire racine de votre application, puis exécutez les commandes suivantes :

		git init
		git add .
		git commit -m "initial commit"
		git remote add azure [URL for remote repository]
		git push azure master

	Vous êtes invité à entrer le mot de passe que vous avez créé précédemment.

3. Accédez à **http://[your web app domain]/createtable.php** pour créer la table de l’application.
4. Accédez à **http://[your web app domain]/index.php** pour commencer à utiliser l’application.

Après la publication de votre application, vous pouvez y apporter des modifications, puis utiliser Git pour les publier.

##Publication des modifications apportées à votre application

Pour publier des modifications apportées à votre application, procédez comme suit :

1. Modifiez votre application en local.
2. Ouvrez GitBash (ou un terminal, si Git est dans votre `PATH`), remplacez les répertoires du répertoire racine de votre application, puis exécutez les commandes suivantes :

		git add .
		git commit -m "comment describing changes"
		git push azure master

	Vous êtes invité à entrer le mot de passe que vous avez créé précédemment.

3. Accédez à **http://[your web app domain]/index.php** pour voir vos modifications.

## Changements apportés
* Pour obtenir un guide présentant les modifications apportées dans le cadre de la transition entre Sites Web et App Service, consultez la page : [Azure App Service et les services Azure existants](http://go.microsoft.com/fwlink/?LinkId=529714)
* Pour obtenir un guide présentant les modifications apportées dans le cadre de la transition entre l’ancien et le nouveau portail, consultez : [Références sur la navigation dans le portail Azure](http://go.microsoft.com/fwlink/?LinkId=529715)




[install-php]: http://www.php.net/manual/en/install.php
[install-git]: http://git-scm.com/book/en/Getting-Started-Installing-Git
[composer-phar]: http://getcomposer.org/composer.phar
[msdn-errors]: http://msdn.microsoft.com/library/windowsazure/dd179438.aspx

[msdn-table-query-syntax]: http://msdn.microsoft.com/library/windowsazure/dd894031.aspx
[ws-storage-app]: ./media/web-sites-php-storage/ws-storage-app.png
[management-portal]: https://portal.azure.com

[storage-quick-create]: ./media/web-sites-php-storage/createstorage.png
[storage-access-keys]: ./media/web-sites-php-storage/keydetails.png

[setup-git-publishing]: ./media/web-sites-php-storage/setup_git_publishing.png
[credentials]: ./media/web-sites-php-storage/git-deployment-credentials.png

[git-instructions]: ./media/web-sites-php-storage/git-instructions.png
 

<!---HONumber=July15_HO4-->