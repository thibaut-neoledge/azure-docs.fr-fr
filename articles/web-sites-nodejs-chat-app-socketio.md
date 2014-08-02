<properties linkid="dev-nodejs-website-with-socketio" urlDisplayName="Website Using Socket.IO" pageTitle="Node.js Website using Socket.io - Azure tutorial" metaKeywords="Azure Node.js socket.io tutorial, Azure Node.js socket.io, Azure Node.js tutorial" description="A tutorial that demonstrates using socket.io in a node.js website hosted on Azure." metaCanonical="" services="web-sites" documentationCenter="Node.js" title="Build a Node.js Chat Application with Socket.IO on an Azure Web Site" authors="larryfr" solutions="" videoId="" scriptId="" manager="paulettm" editor="mollybos" />

Création d'une application de conversation instantanée Node.js avec Socket.IO sur un site Web Azure
===================================================================================================

Socket.IO permet une communication en temps réel entre votre serveur node.js et vos clients. Ce didacticiel présente l'hébergement d'une application de conversation instantanée socket.IO en tant que site Web Azure. Pour plus d'informations sur Socket.IO, consultez le site <http://socket.io/>.

**Remarque**

Les procédures de cette tâche s'appliquent à Sites Web Azure. Pour Cloud Services, consultez la page [Création d'une application de conversation instantanée Node.js avec Socket.IO sur un service cloud Azure](http://www.windowsazure.com/en-us/develop/nodejs/tutorials/app-using-socketio/).

Voici une capture d'écran de l'application terminée :

![Navigateur affichant l'application de conversation instantanée](./media/web-sites-nodejs-chat-app-socketio/websitesocketcomplete.png)

Téléchargement de l'exemple de conversation instantanée
-------------------------------------------------------

Pour ce projet, nous utiliserons un exemple de conversation instantanée obtenu depuis le [référentiel GitHub Socket.IO](https://github.com/LearnBoost/socket.io/tree/0.9.14). Procédez comme suit pour télécharger l'exemple et l'ajouter au projet créé précédemment.

1.  Créez une copie locale du référentiel en utilisant le bouton **Clone**. Vous pouvez aussi télécharger le projet en utilisant le bouton **ZIP**.

    ![Fenêtre de navigateur affichant https://github.com/LearnBoost/socket.io/tree/master/examples/chat, avec l'icône de téléchargement ZIP en surbrillance](./media/web-sites-nodejs-chat-app-socketio/socketio-2.png)

2.  Accédez à la structure de répertoires du référentiel local, jusqu'à atteindre le répertoire **examples\\chat**. Copiez le contenu de ce répertoire dans un répertoire distinct, tel que **\\node\\chat**.

Modification du fichier App.js et installation des modules
----------------------------------------------------------

Avant de déployer l'application sur Azure, il est nécessaire d'apporter quelques modifications mineures. Procédez comme suit avec le fichier app.js :

1.  Ouvrez le fichier app.js dans le Bloc-notes ou un autre éditeur de texte.

2.  Recherchez la section **Module dependencies** située au début du fichier app.js, puis remplacez la ligne contenant **sio = require('..//..//lib//socket.io')** par **sio = require('socket.io')**, comme indiqué ci-dessous :

		var express = require('express')
  		, stylus = require('stylus')
  		, nib = require('nib')
		//, sio = require('..//..//lib//socket.io'); //Original
  		, sio = require('socket.io');                //Updated

3.  Pour que l'application écoute le bon port, ouvrez le fichier app.js dans le Bloc-notes (ou votre éditeur favori), puis modifiez la ligne suivante en remplaçant **3000** par **process.env.PORT**, comme indiqué ci-dessous :

        //app.listen(3000, function () {            //Original
        app.listen(process.env.PORT, function () {  //Mis à jour
          var addr = app.address();
          console.log('   app listening on http://' + addr.address + ':' + addr.port);
        });

Après avoir enregistré les modifications apportées à app.js, procédez comme suit pour installer les modules requis :

1.  Dans la ligne de commande, remplacez les répertoires par le répertoire **\\node\\chat**, puis exécutez la commande suivante pour installer les modules requis par cette application :

        npm install

    Ceci installe les modules répertoriés dans le fichier package.json. Une fois la commande terminée, un texte similaire à celui présenté ci-dessous doit s'afficher :

    ![Résultat de la commande npm install](./media/web-sites-nodejs-chat-app-socketio/socketio-7.png)

2.  Comme cet exemple fait partie du référentiel GitHub Socket.IO et qu'il est directement référencé par la bibliothèque Socket.IO par un chemin d'accès relatif, Socket.IO n'est pas référencé dans le fichier package.json. Vous devez donc l'installer en exécutant la commande suivante :

        npm install socket.io -save

Création d'un site Web Azure et activation de la publication Git
----------------------------------------------------------------

Suivez cette procédure pour créer un site Web Azure et activer la publication Git pour celui-ci.

**Remarque**

Pour effectuer ce didacticiel, vous avez besoin d'un compte Azure. Si vous ne possédez pas de compte, vous pouvez créer un compte d'évaluation gratuit en quelques minutes. Pour plus d'informations, consultez la page [Version d'évaluation gratuite d'Azure](http://www.windowsazure.com/en-us/pricing/free-trial/?WT.mc_id=A7171371E).

1.  Dans la ligne de commande, remplacez les répertoires par le répertoire **\\node\\chat**, puis utilisez la commande suivante pour créer un site Web Azure et activer un référentiel Git pour le site Web et le répertoire local. Ceci crée également un Git distant nommé « Azure ».

         azure site create nomdusite --git

    Remplacez « nomdusite » par le nom unique de votre site Web.

2.  Validez les fichiers existants dans le référentiel local en utilisant les commandes suivantes :

         git add .
         git commit -m "Initial commit"

3.  Utilisez la commande suivante pour envoyer les fichiers dans le référentiel de site Web Azure :

        git push azure master

    Vous recevez des messages d'état au fur et à mesure que les modules sont importés sur le serveur. Une fois ce processus terminé, l'application est hébergée sur votre site Web Azure.

<div class="dev-callout">

    <b>Remarque</b>
    <p>Au cours de l'installation du module, il est possible que des erreurs indiquant que « Le projet XXX importé est introuvable » s'affichent. Vous pouvez ignorer ces messages en toute sécurité.</p>
    </div>

1.  Socket.IO utilise WebSockets, qui n'est pas activé par défaut sur Azure. Pour activer WebSockets, utilisez la commande suivante :

         azure site set -w

    Entrez le nom du site s'il vous est demandé.

    > [WACOM.NOTE] La commande « azure site set -w » fonctionne uniquement avec les versions 0.7.4 et ultérieures de l'interface de ligne de commande interplateforme Azure. Vous pouvez également activer la prise en charge de WebSocket depuis le portail de gestion Azure.
    >
    > Pour activer WebSockets à partir du [portail de gestion Azure](https://manage.windowsazure.com), sélectionnez Configurer la page pour votre site Web, sélectionnez « ON » pour l'entrée Web Sockets, puis cliquez sur Enregistrer.
    >
    > ![websockets](./media/web-sites-nodejs-chat-app-socketio/websockets.png)

2.  Pour afficher le site Web sur Azure, utilisez la commande suivante pour lancer votre navigateur Internet et accéder au site Web hébergé :

         azure site browse

Votre application s'exécute à présent dans Azure, et peut transmettre des messages de conversation instantanée entre différents clients en utilisant Socket.IO.

**Remarque**

Par souci de simplification, cet exemple présente simplement une conversation instantanée entre des utilisateurs connectés à la même instance. Cela veut dire que si le service cloud crée deux instances de rôle de travail, un utilisateur pourra utiliser la conversation instantanée pour communiquer uniquement avec les autres utilisateurs connectés à la même instance de rôle de travail. Pour mettre à l'échelle l'application afin d'utiliser plusieurs instances de rôle, vous pouvez utiliser une technologie telle que Service Bus pour partager l'état de stockage Socket.IO entre plusieurs instances. Pour en savoir plus, consultez les exemples d'utilisation des files d'attente et rubriques de Service Bus dans le [référentiel GitHub Kit de développement logiciel (SDK) Azure pour Node.js](https://github.com/WindowsAzure/azure-sdk-for-node).

Étapes suivantes
----------------

Ce didacticiel vous a montré comment créer une application de conversation instantanée de base, hébergée dans un site Web Azure. Vous pouvez également héberger cette application dans le service cloud Azure. Pour savoir comment procéder, consultez la page [Création d'une application de conversation instantanée Node.js avec Socket.IO sur un service cloud Azure](/en-us/develop/nodejs/tutorials/app-using-socketio/).

