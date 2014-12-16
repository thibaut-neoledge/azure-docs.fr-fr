<properties linkid="dev-nodejs-worker-app-with-socketio" urlDisplayName="App Using Socket.IO" pageTitle="Node.js application using Socket.io - Azure tutorial" metaKeywords="Azure Node.js socket.io tutorial, Azure Node.js socket.io, Azure Node.js tutorial" description="A tutorial that demonstrates using socket.io in a node.js application hosted on Azure." metaCanonical="" services="cloud-services" documentationCenter="nodejs" title="Build a Node.js Chat Application with Socket.IO on an Azure Cloud Service" authors="larryfr" solutions="" manager="" editor="" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="nodejs" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr" />

# Création d'une application de conversation instantanée Node.js avec Socket.IO sur un service cloud Azure

Socket.IO permet une communication en temps réel entre votre serveur
node.js et vos clients. Ce didacticiel présente l'hébergement d'une
application de conversation instantanée socket.IO dans Azure. Pour plus d'informations
sur Socket.IO, consultez le site <http://socket.io/>.

Voici une capture d'écran de l'application terminée :

![Fenêtre de navigateur affichant le service hébergé sur Azure][Fenêtre de navigateur affichant le service hébergé sur Azure]

## Création d'un projet de service cloud

La procédure suivante permet de créer un projet de service cloud hébergeant l'application Socket.IO.

1.  À partir du menu **Démarrer** ou de l'**écran d'accueil**, recherchez **Azure PowerShell**. Enfin, cliquez avec le bouton droit sur **Azure PowerShell** et sélectionnez **Exécuter en tant qu'administrateur**.

    ![Icône Azure PowerShell][Icône Azure PowerShell]

    [WACOM.INCLUDE [install-dev-tools](../includes/install-dev-tools.md)]

2.  Remplacez les répertoires du répertoire **c:\\node**, puis entrez les commandes suivantes pour créer une solution nommée **chatapp** et un rôle de travail nommé **WorkerRole1** :

        PS C:\node> New-AzureServiceProject chatapp
        PS C:\Node> Add-AzureNodeWorkerRole

    Vous obtenez la réponse suivante :

    ![Résultat des cmdlets new-azureservice et add-azurenodeworkerrole][Résultat des cmdlets new-azureservice et add-azurenodeworkerrole]

## Téléchargement de l'exemple de conversation instantanée

Pour ce projet, nous utiliserons un exemple de conversation instantanée
obtenu depuis le [référentiel Socket.IOGitHub][référentiel Socket.IOGitHub]. Procédez comme suit pour télécharger l'exemple et
l'ajouter au projet créé précédemment.

1.  Créez une copie locale du référentiel en utilisant le bouton **Clone**. Vous pouvez aussi télécharger le projet en utilisant le bouton **ZIP**.

    ![Fenêtre de navigateur affichant https://github.com/LearnBoost/socket.io/tree/master/examples/chat, avec l'icône de téléchargement ZIP en surbrillance][Fenêtre de navigateur affichant https://github.com/LearnBoost/socket.io/tree/master/examples/chat, avec l'icône de téléchargement ZIP en surbrillance]

2.  Accédez à la structure de répertoires du référentiel local, jusqu'à atteindre le répertoire **examples\\chat**
    . Copiez le contenu de ce répertoire dans le répertoire
    **C:\\node\\chatapp\\WorkerRole1** créé précédemment.

    ![Explorateur affichant le contenu du répertoire examples\chat extrait de l'archive][chat-example-view]

    Les éléments mis en surbrillance sur cette capture d'écran sont les fichiers copiés depuis le répertoire **examples\\chat**.

3.  Dans le répertoire **C:\\node\\chatapp\\WorkerRole1**, supprimez le fichier **server.js**, puis renommez le fichier **app.js** en **server.js**. Ceci supprime le fichier **server.js** par défaut créé précédemment par la cmdlet **Add-AzureNodeWorkerRole** et le remplace par le fichier d'application de l'exemple de conversation instantanée.

### Modification du fichier Server.js et installation des modules

Avant de tester l'application dans l'émulateur Azure, nous
allons apporter quelques modifications mineures. Procédez comme suit avec le
fichier server.js :

1.  Ouvrez le fichier server.js dans le Bloc-notes ou un autre éditeur de texte.

2.  Recherchez la section **Module dependencies** située au début du fichier server.js, puis remplacez la ligne contenant **sio = require('..//..//lib//socket.io')** par **sio = require('socket.io')**, comme indiqué ci-dessous :

        var express = require('express')
        , stylus = require('stylus')
        , nib = require('nib')
        //, sio = require('..//..//lib//socket.io'); //Original
        , sio = require('socket.io');                //Updated

3.  Pour que l'application écoute le bon port, ouvrez le fichier
    server.js dans le Bloc-notes (ou votre éditeur favori), puis modifiez
    la ligne suivante en remplaçant **3000** par **process.env.port**, comme indiqué ci-dessous :

        //app.listen(3000, function () {            //Original
        app.listen(process.env.port, function () {  //Updated
          var addr = app.address();
          console.log('   app listening on http://' + addr.address + ':' + addr.port);
        });

Après avoir enregistré vos modifications dans le fichier server.js,
procédez comme suit pour installer les modules requis,
puis testez l'application dans l'émulateur Azure :

1.  Utilisez **Azure PowerShell** pour remplacer les répertoires du répertoire **C:\\node\\chatapp\\WorkerRole1**, puis exécutez la commande suivante pour installer les modules requis par cette application :

        PS C:\node\chatapp\WorkerRole1> npm install

    Ceci installe les modules répertoriés dans le fichier package.json. Une
    fois la commande terminée, un texte similaire à celui présenté
    ci-dessous doit s'afficher :

    ![Résultat de la commande npm install][Résultat de la commande npm install]

2.  Comme cet exemple fait partie du référentiel GitHub Socket.IO
    et qu'il est directement référencé par la bibliothèque Socket.IO
    par un chemin d'accès relatif, Socket.IO n'est pas référencé
    dans le fichier package.json. Vous devez donc l'installer en exécutant la commande suivante :

        PS C:\node\chatapp\WorkerRole1> npm install socket.io -save

### Test et déploiement

1.  Démarrez l'émulateur en exécutant la commande suivante :

        PS C:\node\chatapp\WorkerRole1> Start-AzureEmulator -Launch

2.  ﻿À l'ouverture de la fenêtre du navigateur, entrez un pseudonyme, puis appuyez sur Entrée.
    Vous pouvez ainsi publier des messages sous ce pseudonyme. Pour
    tester la fonctionnalité multi-utilisateurs, ouvrez d'autres fenêtres du
    navigateur en utilisant la même URL et en entrant différents pseudonymes.

    ![Deux fenêtres du navigateur affichant des messages de conversation instantanée des utilisateurs User1 et User2][Deux fenêtres du navigateur affichant des messages de conversation instantanée des utilisateurs User1 et User2]

3.  Une fois le test de l'application terminé, arrêtez l'émulateur en
    exécutant la commande suivante :

        PS C:\node\chatapp\WorkerRole1> Stop-AzureEmulator

4.  Pour déployer l'application dans Azure, utilisez la cmdlet
    **Publish-AzureServiceProject**. Par exemple :

        PS C:\node\chatapp\WorkerRole1> Publish-AzureServiceProject -ServiceName mychatapp -Location "East US" -Launch

    <div class="dev-callout">
<strong>Remarque</strong>
<p>Veillez &agrave; utiliser un nom unique, sans quoi le processus de publication &eacute;choue. Une fois le d&eacute;ploiement termin&eacute;, le navigateur s'ouvre et acc&egrave;de au service d&eacute;ploy&eacute;.</p>
<p>Si vous recevez une erreur indiquant que le nom d'abonnement fourni n'existe pas dans le profil de publication import&eacute;, vous devez t&eacute;l&eacute;charger et importer le profil de publication de votre abonnement avant de le d&eacute;ployer dans Azure. Consultez la section <b>D&eacute;ploiement de l'application dans Azure</b> du didacticiel <a href="https://www.windowsazure.com/fr-fr/develop/nodejs/tutorials/getting-started/">Cr&eacute;ation et d&eacute;ploiement d'une application Node.js dans Azure Cloud Services</a></p>
</div>

    ![Fenêtre de navigateur affichant le service hébergé sur Azure][Fenêtre de navigateur affichant le service hébergé sur Azure]

    <div class="dev-callout">
<strong>Remarque</strong>
<p>Si vous recevez une erreur indiquant que le nom d'abonnement fourni n'existe pas dans le profil de publication import&eacute;, vous devez t&eacute;l&eacute;charger et importer le profil de publication de votre abonnement avant de le d&eacute;ployer dans Azure. Consultez la section <b>D&eacute;ploiement de l'application dans Azure</b> du didacticiel <a href="https://www.windowsazure.com/fr-fr/develop/nodejs/tutorials/getting-started/">Cr&eacute;ation et d&eacute;ploiement d'une application Node.js dans Azure Cloud Services</a></p>
</div>

Votre application s'exécute à présent dans Azure, et peut
transmettre des messages de conversation instantanée entre différents clients en utilisant Socket.IO.

<div class="dev-callout">
<strong>Remarque</strong>
<p>Par souci de simplification, cet exemple pr&eacute;sente simplement une conversation instantan&eacute;e entre des utilisateurs connect&eacute;s &agrave; la m&ecirc;me instance. Cela veut dire que si le service cloud cr&eacute;e deux instances de r&ocirc;le de travail, un utilisateur pourra utiliser la conversation instantan&eacute;e pour communiquer uniquement avec les autres utilisateurs connect&eacute;s &agrave; la m&ecirc;me instance de r&ocirc;le de travail. Pour mettre &agrave; l'&eacute;chelle l'application afin d'utiliser plusieurs instances de r&ocirc;le, vous pouvez utiliser une technologie telle que Service Bus pour partager l'&eacute;tat de stockage Socket.IO entre plusieurs instances. Pour en savoir plus, consultez les exemples d'utilisation des files d'attente et rubriques de Service Bus dans le <a href="https://github.com/WindowsAzure/azure-sdk-for-node">r&eacute;f&eacute;rentiel GitHub Kit de d&eacute;veloppement logiciel (SDK) Azure pour Node.js</a>.</p>
</div>

## Étapes suivantes

Ce didacticiel vous a montré comment créer une application de conversation instantanée de base, hébergée dans un service cloud Azure. Pour plus d'informations sur l'hébergement de cette application sur un site web Azure, consultez la page [Création d'une application de conversation instantanée Node.js avec Socket.IO sur un site web Azure][Création d'une application de conversation instantanée Node.js avec Socket.IO sur un site web Azure].

  [Fenêtre de navigateur affichant le service hébergé sur Azure]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-10.png
  [Icône Azure PowerShell]: ./media/cloud-services-nodejs-chat-app-socketio/azure-powershell-start.png
  [Résultat des cmdlets new-azureservice et add-azurenodeworkerrole]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-1.png
  [référentiel Socket.IOGitHub]: https://github.com/LearnBoost/socket.io/tree/0.9.14
  [Fenêtre de navigateur affichant https://github.com/LearnBoost/socket.io/tree/master/examples/chat, avec l'icône de téléchargement ZIP en surbrillance]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-22.png
  [Résultat de la commande npm install]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-7.png
  [Deux fenêtres du navigateur affichant des messages de conversation instantanée des utilisateurs User1 et User2]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-8.png
  [Création d'une application de conversation instantanée Node.js avec Socket.IO sur un site web Azure]: /fr-fr/develop/nodejs/tutorials/website-using-socketio/
  [chat-example-view]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-22.png
