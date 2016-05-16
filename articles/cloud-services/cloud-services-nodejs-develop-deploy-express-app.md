<properties 
	pageTitle="Application web avec Express (Node.js) | Microsoft Azure" 
	description="Ce didacticiel poursuit le didacticiel relatif au service cloud et présente l’utilisation du module Express." 
	services="cloud-services" 
	documentationCenter="nodejs" 
	authors="rmcmurray" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="cloud-services" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="nodejs" 
	ms.topic="article" 
	ms.date="05/04/2016" 
	ms.author="robmcm"/>






# Création d'une application web Node.js avec Express sur un service cloud Azure

Node.js inclut un ensemble minimal de fonctionnalités dans le runtime principal. Les développeurs utilisent souvent des modules tiers pour fournir des fonctionnalités supplémentaires lors du développement d'une application Node.js. Dans ce didacticiel, vous allez créer une application en utilisant le module [Express][], qui fournit une infrastructure MVC pour créer des applications Web Node.js.

Voici une capture d'écran de l'application terminée :

![Navigateur Web affichant Bienvenue sur Express dans Azure](./media/cloud-services-nodejs-develop-deploy-express-app/node36.png)

##Création d'un projet de service cloud

Procédez comme suit pour créer un projet de service cloud nommé « expressapp » :

1. À partir du **menu Démarrer** ou de l’**écran d’accueil**, recherchez **Windows PowerShell**. Enfin, cliquez avec le bouton droit sur **Windows PowerShell** et sélectionnez **Exécuter en tant qu’administrateur**.

	![Icône Azure PowerShell](./media/cloud-services-nodejs-develop-deploy-express-app/azure-powershell-start.png)

	[AZURE.INCLUDE [install-dev-tools](../../includes/install-dev-tools.md)]

2. Remplacez les répertoires du répertoire **c:\\node**, puis entrez les commandes suivantes pour créer une solution nommée **expressapp** et un rôle Web nommé **WebRole1** :

		PS C:\node> New-AzureServiceProject expressapp
		PS C:\Node\expressapp> Add-AzureNodeWebRole
		PS C:\Node\expressapp> Set-AzureServiceProjectRole WebRole1 Node 0.10.21

	> [AZURE.NOTE] Par défaut, **Add-AzureNodeWebRole** utilise une ancienne version de Node.js. L'instruction **Set-AzureServiceProjectRole** ci-dessus indique à Azure d'utiliser la version v0.10.21 de Node. Notez que les paramètres respectent la casse. Vous pouvez vérifier que la version correcte de Node.js a été sélectionnée en vérifiant la propriété **moteurs** dans **WebRole1\\package.json**.

##Installation d'Express

1. Installez le générateur Express en exécutant la commande suivante :

		PS C:\node\expressapp> npm install express-generator -g

	Le résultat de la commande npm doit ressembler à l'exemple ci-dessous :

	![Windows PowerShell affichant le résultat de la commande npm install express.](./media/cloud-services-nodejs-develop-deploy-express-app/express-g.png)

2. Remplacez les répertoires du répertoire **WebRole1** et utilisez la commande express pour créer une application :

        PS C:\node\expressapp\WebRole1> express

	Vous êtes invité à remplacer votre application précédente. Tapez **y** ou **yes** pour continuer. Express génère le fichier app.js et une structure de dossiers pour la création de votre application.

	![Résultat de la commande express](./media/cloud-services-nodejs-develop-deploy-express-app/node23.png)


5.  Pour installer les dépendances supplémentaires définies dans le fichier package.json, entrez la commande suivante :

        PS C:\node\expressapp\WebRole1> npm install

	![Résultat de la commande npm install](./media/cloud-services-nodejs-develop-deploy-express-app/node26.png)

6.  Utilisez la commande suivante pour copier le fichier **bin/www** vers **server.js**. De cette façon, le service cloud peut trouver le point d'entrée pour cette application.

		PS C:\node\expressapp\WebRole1> copy bin/www server.js

	Une fois cette commande terminée, un fichier **server.js** doit se trouver dans le répertoire WebRole1.

7.  Modifiez **server.js** pour supprimer l'un des caractères « . » de la ligne suivante.

		var app = require('../app');

	Une fois cette modification apportée, la ligne doit apparaître comme suit.

		var app = require('./app');

	Cette modification est nécessaire, car nous avons déplacé le fichier (anciennement **bin/www**) vers le même répertoire que le fichier d'application requis. Une fois cette modification effectuée, enregistrez le fichier **server.js**.

8.  Utilisez la commande suivante pour exécuter l'application dans l'émulateur Azure :

        PS C:\node\expressapp\WebRole1> Start-AzureEmulator -launch

	![Page Web contenant Bienvenue sur Express.](./media/cloud-services-nodejs-develop-deploy-express-app/node28.png)

## Modification de la vue

À présent, modifiez la vue pour afficher le message « Bienvenue sur Express dans Azure ».

1.  Entrez la commande suivante pour ouvrir le fichier index.jade :

        PS C:\node\expressapp\WebRole1> notepad views/index.jade

    ![Contenu du fichier index.jade.](./media/cloud-services-nodejs-develop-deploy-express-app/getting-started-19.png)

    Jade est le moteur de vue par défaut des applications Express. Pour plus d'informations sur le moteur de vue Jade, consultez [http://jade-lang.com][].

2.  Modifiez la dernière ligne du texte en ajoutant **dans Azure**.

	![Dernières lignes du fichier index.jade : p Bienvenue sur #{title} dans Azure](./media/cloud-services-nodejs-develop-deploy-express-app/node31.png)

3.  Enregistrez le fichier et quittez le Bloc-notes.

4.  Actualisez votre navigateur pour afficher vos modifications.

	![Navigateur Web affichant la page Bienvenue sur Express dans Azure](./media/cloud-services-nodejs-develop-deploy-express-app/node32.png)

Après avoir testé l'application, utilisez la cmdlet **Stop-AzureEmulator** pour arrêter l'émulateur.

##Publication de l'application dans Azure

Dans la fenêtre Azure PowerShell, utilisez la cmdlet **Publish-AzureServiceProject** pour déployer l'application dans un service cloud.

    PS C:\node\expressapp\WebRole1> Publish-AzureServiceProject -ServiceName myexpressapp -Location "East US" -Launch

Une fois le déploiement terminé, votre navigateur s'ouvre et affiche la page Web.

![Navigateur web affichant la page Express. L'URL indique que la page est maintenant hébergée sur Azure.](./media/cloud-services-nodejs-develop-deploy-express-app/node36.png)

## Étapes suivantes

Pour plus d'informations, consultez le [Centre pour développeurs Node.js](/develop/nodejs/).

  [Node.js Web Application]: http://www.windowsazure.com/develop/nodejs/tutorials/getting-started/
  [Express]: http://expressjs.com/
  [http://jade-lang.com]: http://jade-lang.com

 

<!---HONumber=AcomDC_0504_2016-->