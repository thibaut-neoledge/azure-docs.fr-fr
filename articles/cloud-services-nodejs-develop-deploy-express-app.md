<properties urlDisplayName="Web App with Express" pageTitle="Application web avec Express (Node.js) - Didacticiel Azure" metaKeywords="didacticiel hello world Azure Node.js, hello world Azure Node.js, didacticiel de prise en main d'Azure Node.js, didacticiel Azure Node.js, didacticiel Azure Node.js Express" description="Ce didacticiel poursuit le didacticiel relatif au service cloud et présente l'utilisation du module Express." metaCanonical="" services="cloud-services" documentationCenter="nodejs" title="Build a Node.js web application using Express on an Azure Cloud Service" authors="larryfr" solutions="" manager="wpickett" editor="" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="nodejs" ms.topic="article" ms.date="09/17/2014" ms.author="wpickett" />






# Création d'une application web Node.js avec Express sur un service cloud Azure

Node.js inclut un ensemble minimal de fonctionnalités dans le runtime principal. Les développeurs utilisent souvent des modules tiers pour fournir des fonctionnalités supplémentaires lors du développement d'une application Node.js. Dans ce didacticiel, vous allez créer une application en utilisant le module [Express][], qui fournit une infrastructure MVC pour créer des applications web Node.js.

Voici une capture d'écran de l'application terminée :

![A web browser displaying Welcome to Express in Azure](./media/cloud-services-nodejs-develop-deploy-express-app/node36.png)

##Création d'un projet de service cloud

Procédez comme suit pour créer un projet de service cloud nommé " expressapp " :

1. À partir du menu **Démarrer** ou de l'**écran d'accueil**, recherchez **Azure PowerShell**. Enfin, cliquez avec le bouton droit sur **Azure PowerShell** et sélectionnez **Exécuter en tant qu'administrateur**.

	![Azure PowerShell icon](./media/cloud-services-nodejs-develop-deploy-express-app/azure-powershell-start.png)

	[WACOM.INCLUDE [install-dev-tools](../includes/install-dev-tools.md)]

2. Accédez au répertoire **c:\\node**, puis entrez les commandes suivantes pour créer une solution nommée **expressapp** et un rôle web nommé **WebRole1** :

		PS C:\node> New-AzureServiceProject expressapp
		PS C:\Node\expressapp> Add-AzureNodeWebRole
		PS C:\Node\expressapp> Set-AzureServiceProjectRole WebRole1 node 0.10.21

	> [WACOM.NOTE] Par défaut, **Add-AzureNodeWebRole** utilise une ancienne version de Node.js. L'instruction **Set-AzureServiceProjectRole** ci-dessus indique à Azure d'utiliser la version v0.10.21 de Node. 

##Installation d'Express

1. Installez le générateur Express en exécutant la commande suivante :

		PS C:\node\expressapp> npm install express-generator -g

	Le résultat de la commande npm doit ressembler à l'exemple ci-dessous : 

	![Windows PowerShell displaying the output of the npm install express command.](./media/cloud-services-nodejs-develop-deploy-express-app/express-g.png)

2. Accédez au répertoire **WebRole1** et utilisez la commande express pour créer une application :

        PS C:\node\expressapp\WebRole1> express

	Vous êtes invité à remplacer votre application précédente. Entrez **y** ou **yes** pour continuer. Express génère le fichier app.js et une structure de dossiers pour la création de votre application.

	![The output of the express command](./media/cloud-services-nodejs-develop-deploy-express-app/node23.png)


5.  Pour installer les dépendances supplémentaires définies dans le fichier package.json, entrez la commande suivante :

        PS C:\node\expressapp\WebRole1> npm install

	![The output of the npm install command](./media/cloud-services-nodejs-develop-deploy-express-app/node26.png)

6.  Utilisez la commande suivante pour copier le fichier **bin/www** vers **server.js**. De cette façon, le service cloud peut trouver le point d'entrée pour cette application.

		PS C:\node\expressapp\WebRole1> copy bin/www server.js

	Une fois cette commande terminée, un fichier **server.js** doit se trouver dans le répertoire WebRole1.

7.  Modifiez **server.js** pour supprimer l'un des caractères " . " de la ligne suivante.

		var app = require('../app');

	Une fois cette modification apportée, la ligne doit apparaître comme suit.

		var app = require('./app');

	Cette modification est nécessaire, car nous avons déplacé le fichier (anciennement **bin/www**) vers le même répertoire que le fichier d'application nécessaire. Une fois cette modification effectuée, enregistrez le fichier **server.js**.

8.  Utilisez la commande suivante pour exécuter l'application dans l'émulateur Windows Azure :

        PS C:\node\expressapp\WebRole1> Start-AzureEmulator -launch

	![A web page containing welcome to express.](./media/cloud-services-nodejs-develop-deploy-express-app/node28.png)

## Modification de la vue

﻿À présent, modifiez la vue pour afficher le message " Bienvenue sur Express dans
Azure ".

1.  Entrez la commande suivante pour ouvrir le fichier index.jade :

        PS C:\node\expressapp\WebRole1> notepad views/index.jade

    ![The contents of the index.jade file.](./media/cloud-services-nodejs-develop-deploy-express-app/getting-started-19.png)

    Jade est le moteur de vue par défaut des applications Express. Pour plus d'informations sur le moteur de vue Jade, consultez [http://jade-lang.com][].

2.  Modifiez la dernière ligne du texte par ajout **dans Azure**.

	![The index.jade file, the last line reads: p Welcome to \#{title} in Azure](./media/cloud-services-nodejs-develop-deploy-express-app/node31.png)

3.  Enregistrez le fichier et quittez le Bloc-notes.

4.  Actualisez votre navigateur pour afficher vos modifications.

	![A browser window, the page contains Welcome to Express in Azure](./media/cloud-services-nodejs-develop-deploy-express-app/node32.png)

Après avoir testé l'application, utilisez l'applet de commande **Stop-AzureEmulator** pour arrêter l'émulateur.

##Publication de l'application dans Azure

Dans la fenêtre Azure PowerShell, utilisez l'applet de commande **Publish-AzureServiceProject** pour déployer l'application dans un service cloud.

    PS C:\node\expressapp\WebRole1> Publish-AzureServiceProject -ServiceName myexpressapp -Location "East US" -Launch

Une fois le déploiement terminé, votre navigateur s'ouvre et affiche la page web.

![A web browser displaying the Express page. The URL indicates it is now hosted on Azure.](./media/cloud-services-nodejs-develop-deploy-express-app/node36.png)


  [Application web Node.js]: http://www.windowsazure.com/fr-fr/develop/nodejs/tutorials/getting-started/
  [Express]: http://expressjs.com/
  [http://jade-lang.com]: http://jade-lang.com


<!--HONumber=35.2-->
