<properties 
	pageTitle="Application Node.js avec Socket.io - Didacticiel Azure" 
	description="Ce didacticiel présente l'utilisation de socket.io dans une application node.js hébergée sur Azure." 
	services="cloud-services" 
	documentationCenter="nodejs" 
	authors="" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="cloud-services" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="nodejs" 
	ms.topic="article" 
	ms.date="09/17/2014" 
	ms.author="wpickett"/>





# Création d'une application de conversation instantanée Node.js avec Socket.IO sur un service cloud Azure

Socket.IO permet une communication en temps réel entre votre serveur node.js
et vos clients. Ce didacticiel présente l'hébergement d'une
application de conversation basée sur Socket.IO sur Azure. Pour plus d'informations
sur Socket.IO, consultez <a href="http://socket.io/">http://socket.io/</a>.

Voici une capture d'écran de l'application terminée :

![A browser window displaying the service hosted on Azure][completed-app]  

## Création d'un projet de service cloud

La procédure suivante permet de créer un projet de service cloud hébergeant l'application Socket.IO.

1. À partir du menu **Démarrer** ou de l'**écran d'accueil**, recherchez **Azure PowerShell**. Enfin, cliquez avec le bouton droit sur **Azure PowerShell** et sélectionnez **Exécuter en tant qu'administrateur**.

	![Azure PowerShell icon][powershell-menu]

	[AZURE.INCLUDE [install-dev-tools](../includes/install-dev-tools.md)]



2. Accédez au répertoire **c:\node**, puis entrez les commandes suivantes pour créer une solution nommée **chatapp** et un rôle de travail nommé **WorkerRole1** :

		PS C:\node> New-AzureServiceProject chatapp
		PS C:\Node> Add-AzureNodeWorkerRole

	Vous obtenez la réponse suivante :

	![The output of the new-azureservice and add-azurenodeworkerrolecmdlets](./media/cloud-services-nodejs-chat-app-socketio/socketio-1.png)

## Téléchargement de l'exemple de conversation instantanée

Pour ce projet, nous allons utiliser un exemple de conversation instantanée du [référentiel GitHub de Socket.IO]. Procédez comme suit pour télécharger l'exemple
et l'ajouter au projet que vous avez déjà créé.

1.  Créez une copie locale du référentiel en utilisant le bouton **Cloner**. Vous pouvez aussi télécharger le projet en utilisant le bouton **ZIP**.

    ![A browser window viewing https://github.com/LearnBoost/socket.io/tree/master/examples/chat, with the ZIP download icon highlighted][chat-example-view]

3.  Accédez à la structure de répertoires du référentiel local jusqu'à atteindre le répertoire **examples\\chat**.
    existant. Copiez le contenu de ce répertoire dans le
    répertoire **C:\\node\\chatapp\\WorkerRole1** créé précédemment.

    ![Explorer, displaying the contents of the examples\\chat directory extracted from the archive][chat-contents]

    Les éléments mis en surbrillance sur cette capture d'écran sont les fichiers copiés depuis le répertoire **examples\chat**.

4.  Dans le répertoire **C:\node\chatapp\WorkerRole1**, supprimez le fichier **server.js**, puis renommez le fichier **app.js** en **server.js**. Ceci supprime le fichier **server.js** par défaut créé précédemment par l'applet de commande **Add-AzureNodeWorkerRole**, et le remplace par le fichier d'application de l'exemple de conversation instantanée.

### Modification du fichier Server.js et installation des modules

Avant de tester l'application dans l'émulateur Azure, nous devons
apporter quelques modifications mineures. Effectuez les opérations suivantes pour le
fichier server.js :

1.  Ouvrez le fichier server.js dans le Bloc-notes ou un autre éditeur de texte.

2.  Recherchez la section **Module dependencies** située au début du fichier server.js, puis remplacez la ligne contenant **sio = require('..//..//lib//socket.io')** par **sio = require('socket.io')**, comme indiqué ci-dessous :

		var express = require('express')
  		, stylus = require('stylus')
  		, nib = require('nib')
		//, sio = require('..//..//lib//socket.io'); //Original
  		, sio = require('socket.io');                //Updated

3.  Pour garantir que l'application écoute le port correct, ouvrez
    server.js dans le bloc-notes ou votre éditeur favori, puis remplacez la
    ligne suivante en remplaçant **3000** par **process.env.port** comme indiqué ci-dessous :

        //app.listen(3000, function () {            //Original
		app.listen(process.env.port, function () {  //Updated
		  var addr = app.address();
		  console.log('   app listening on http://' + addr.address + ':' + addr.port);
		});

Après avoir enregistré les modifications apportées à server.js, procédez comme suit pour
installer les modules requis et tester l'application dans
l'émulateur Azure :

1.  Utilisez **Azure PowerShell** pour remplacer les répertoires du répertoire **C:\\node\\chatapp\\WorkerRole1**, puis exécutez la commande suivante pour installer les modules nécessaires à cette application :

        PS C:\node\chatapp\WorkerRole1> npm install

    Ceci installe les modules répertoriés dans le fichier package.json. Une fois
    la commande terminée, un texte similaire à celui présenté ci-dessous doit
    s'afficher :

    ![The output of the npm install command][The-output-of-the-npm-install-command]

4.  Comme cet exemple fait partie du référentiel GitHub Socket.IO
    et qu'il est directement référencé par la bibliothèque Socket.IO par
    un chemin d'accès relatif, Socket.IO n'est pas référencé dans le fichier package.json.
    Vous devez donc l'installer en exécutant la commande suivante :

        PS C:\node\chatapp\WorkerRole1> npm install socket.io -save

### Test et déploiement

1.  Démarrez l'émulateur en exécutant la commande suivante :

        PS C:\node\chatapp\WorkerRole1> Start-AzureEmulator -Launch

2.  ﻿À l'ouverture de la fenêtre du navigateur, entrez un pseudonyme, puis appuyez sur Entrée.
    Vous pouvez ainsi publier des messages sous ce pseudonyme. Pour tester
    la fonctionnalité multi-utilisateurs, ouvrez d'autres fenêtres de navigateur à l'aide de la
    même URL et en entrant différents surnoms.

    ![Two browser windows displaying chat messages from User1 and User2](./media/cloud-services-nodejs-chat-app-socketio/socketio-8.png)

3.  Une fois le test de l'application terminé, arrêtez l'émulateur en exécutant la
    commande suivante :

        PS C:\node\chatapp\WorkerRole1> Stop-AzureEmulator

4.  Pour déployer l'application dans Azure, utilisez l'
    applet de commande **Publish-AzureServiceProject**. Par exemple :

        PS C:\node\chatapp\WorkerRole1> Publish-AzureServiceProject -ServiceName mychatapp -Location "East US" -Launch

	> [AZURE.IMPORTANT] Veillez à utiliser un nom unique, sans quoi le processus de publication échoue. Une fois le déploiement terminé, le navigateur s'ouvre et accède au service déployé.
	> 
	> Si vous recevez une erreur indiquant que le nom d'abonnement fourni n'existe pas dans le profil de publication importé, vous devez télécharger et importer le profil de publication de votre abonnement avant de le déployer dans Azure. Consultez la section **Déploiement de l'application dans Azure** du didacticiel [Création et déploiement d'une application Node.js dans Azure Cloud Services](https://www.windowsazure.com/fr-fr/develop/nodejs/tutorials/getting-started/)

    ![A browser window displaying the service hosted on Azure][completed-app]

	> [AZURE.NOTE] Si vous recevez une erreur indiquant que le nom d'abonnement fourni n'existe pas dans le profil de publication importé, vous devez télécharger et importer le profil de publication de votre abonnement avant de le déployer dans Azure. Consultez la section **Déploiement de l'application dans Azure** du didacticiel [Création et déploiement d'une application Node.js dans Azure Cloud Services](https://www.windowsazure.com/fr-fr/develop/nodejs/tutorials/getting-started/)

Votre application s'exécute désormais dans Azure, et peut relayer des conversations
entre différents clients via Socket.IO.

> [AZURE.NOTE] Par souci de simplification, cet exemple présente simplement une conversation instantanée entre des utilisateurs connectés à la même instance. Cela veut dire que si le service cloud crée deux instances de rôle de travail, un utilisateur pourra utiliser la conversation instantanée pour communiquer uniquement avec les autres utilisateurs connectés à la même instance de rôle de travail. Pour mettre à l'échelle l'application afin d'utiliser plusieurs instances de rôle, vous pouvez utiliser une technologie telle que Service Bus pour partager l'état de stockage Socket.IO entre plusieurs instances. Pour en savoir plus, consultez les exemples d'utilisation des files d'attente et rubriques Service Bus dans le [Kit de développement logiciel (SDK) Azure pour le référentiel GitHub Node.js](https://github.com/WindowsAzure/azure-sdk-for-node).

##Étapes suivantes

Ce didacticiel vous a montré comment créer une application de conversation instantanée de base, hébergée dans un service cloud Azure. Pour plus d'informations sur l'hébergement de cette application sur un site Web Azure, consultez la page [Création d'une application de conversation instantanée Node.js avec Socket.IO sur un site Web Azure][chatwebsite].

  [chatwebsite]: /fr-fr/develop/nodejs/tutorials/website-using-socketio/

  [Contrat SLA Azure]: http://azure.microsoft.com/support/sla/
  [Kit de développement logiciel (SDK) Azure pour le référentiel GitHub Node.js]: https://github.com/WindowsAzure/azure-sdk-for-node
  [completed-app]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-10.png
  [Kit de développement logiciel (SDK) Azure pour Node.js]: https://www.windowsazure.com/fr-fr/develop/nodejs/
  [Application Web Node.js]: https://www.windowsazure.com/fr-fr/develop/nodejs/tutorials/getting-started/
  [référentiel GitHub de Socket.IO]: https://github.com/LearnBoost/socket.io/tree/0.9.14
  [Considérations relatives à Azure]: #windowsazureconsiderations
  [Hébergement de l'exemple de conversation dans un rôle de travail]: #hostingthechatexampleinawebrole
  [Résumé et étapes suivantes]: #summary
  [powershell-menu]: ./media/cloud-services-nodejs-chat-app-socketio/azure-powershell-start.png

  [exemple de conversation]: https://github.com/LearnBoost/socket.io/tree/master/examples/chat
  [chat-example-view]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-22.png
  
  
  [chat-contents]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-5.png
  [The-output-of-the-npm-install-command]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-7.png
  [Résultat de la commande Publish-AzureService]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-9.png
  

<!--HONumber=45--> 
