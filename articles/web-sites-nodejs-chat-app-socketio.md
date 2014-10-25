<properties linkid="dev-nodejs-website-with-socketio" urlDisplayName="Website Using Socket.IO" pageTitle="Node.js Website using Socket.io - Azure tutorial" metaKeywords="Azure Node.js socket.io tutorial, Azure Node.js socket.io, Azure Node.js tutorial" description="A tutorial that demonstrates using socket.io in a node.js website hosted on Azure." metaCanonical="" services="web-sites" documentationCenter="nodejs" title="Build a Node.js Chat Application with Socket.IO on an Azure Website" authors="larryfr" solutions="" videoId="" scriptId="" manager="paulettm" editor="mollybos" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="nodejs" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr"></tags>

# Création d'une application de conversation instantanée Node.js avec Socket.IO sur un site web Azure

Socket.IO offre une communication en temps réel entre votre serveur node.js et vos clients à l'aide de WebSockets. Il prend également en charge l'utilisation d'autres transports (tels que l'interrogation longue) qui fonctionnent avec des navigateurs plus anciens. Ce didacticiel vous explique comment héberger une application de conversation instantanée socket.IO en tant que site web Azure. Pour plus d'informations sur Socket.IO, consultez le site [][]<http://socket.io/></a>.

> [WACOM.NOTE] Les procédures de cette tâche s'appliquent à l'offre Sites Web Azure. Pour les services cloud, consultez la page [Création d'une application de conversation instantanée Node.js avec Socket.IO sur un service cloud Azure][].

## <span id="Download"></span></a>Téléchargement de l'exemple de conversation instantanée

Pour ce projet, nous allons utiliser un exemple de conversation instantanée du [référentiel GitHub
Socket.IO](https://github.com/Automattic/socket.io). Procédez comme suit pour télécharger l'exemple
et l'ajouter au projet que vous avez déjà créé.

1.  Téléchargez une [version archivée ZIP ou GZ][] du projet Socket.IO (la version 1.0.6 a été utilisée pour ce document).

2.  Extrayez l'archive et copiez le répertoire **examples\\chat**
    dans un nouvel emplacement. Par exemple,
    **\\node\\chat**.

## <span id="Modify"></span></a>Modification du fichier App.js et installation des modules

Avant de déployer l'application sur Azure, nous devons
apporter quelques modifications mineures.

1.  Renommez le fichier **index.js** en **app.js**. Cela permet à Azure de détecter qu'il s'agit d'une application Node.js.

2.  Ouvrez le fichier **app.js** dans le Bloc-notes ou un autre éditeur de texte.

3.  Recherchez la section **Module dependencies** au début du fichier app.js et remplacez la ligne contenant `var io = require('../..')(server);` par `var io = require('socket.io')(server);` comme indiqué ci-dessous :

        var express = require('express');
        var app = express();
        var server = require('http').createServer(app);
        // var io = require('../..')(server);
        var io = require('socket.io')(server);
        var port = process.env.PORT || 3000;

Après avoir enregistré les modifications apportées à app.js, procédez comme suit pour
installer les modules nécessaires :

1.  Dans la ligne de commande, remplacez les répertoires par le répertoire **\\node\\chat**, puis exécutez la commande suivante pour installer les modules requis par cette application :

        npm install

    Ceci installe les modules répertoriés dans le fichier package.json. Une fois
    la commande terminée, un texte similaire à celui présenté ci-dessous doit
    s'afficher :

        express@3.4.8 node_modules\express
        ├── methods@0.1.0
        ├── merge-descriptors@0.0.1
        ├── debug@0.8.1
        ├── cookie-signature@1.0.1
        ├── range-parser@0.0.4
        ├── fresh@0.2.0
        ├── buffer-crc32@0.2.1
        ├── cookie@0.1.0
        ├── mkdirp@0.3.5
        ├── commander@1.3.2 (keypress@0.1.0)
        ├── send@0.1.4 (mime@1.2.11)
        └── connect@2.12.0 (uid2@0.0.3, pause@0.0.1, qs@0.6.6, bytes@0.2.1, raw-body@1.1.2, batch@0.5.0, negotiator@0.3.0, multiparty@2.2.0)

2.  Comme cet exemple fait partie du référentiel GitHub Socket.IO
    et qu'il est directement référencé par la bibliothèque Socket.IO
    par un chemin d'accès relatif, Socket.IO n'est pas référencé dans
    le fichier package.json. Vous devez donc l'installer en exécutant la commande suivante :

        npm install socket.io@1.0.6 -save

    > [WACOM.NOTE] Bien que les étapes décrites dans cet article puissent fonctionner avec les versions plus récentes de Socket.IO, nous les avons testées avec la version 1.0.6.

## <span id="Publish"></span></a>Création d'un site web Azure

Suivez cette procédure pour créer un site web Azure, activer la publication Git, puis activer la prise en charge de WebSocket pour le site web.

> [WACOM.NOTE] Pour effectuer ce didacticiel, vous avez besoin d’un compte Azure. Si vous ne possédez pas de compte, vous pouvez créer un compte d'évaluation gratuit en quelques minutes. Pour plus d'informations, consultez la page [Version d'évaluation gratuite d'Azure][].

1.  Sur la ligne de commande, accédez au répertoire **\\node\\chat**, puis utilisez la commande suivante pour créer un site web Azure et activer un référentiel Git pour le site web et le répertoire local. Ceci crée également un Git distant nommé « Azure ».

        azure site create mysitename --git

    Remplacez « mysitename » par le nom unique de votre site web.

2.  Validez les fichiers existants dans le référentiel local en utilisant les commandes suivantes :

        git add .
        git commit -m "Initial commit"

3.  Utilisez la commande suivante pour envoyer les fichiers dans le référentiel de site web Azure :

        git push azure master

    Vous recevez des messages d'état au fur et à mesure que les modules sont importés sur le serveur. Une fois ce processus terminé, l'application est hébergée sur votre site web Azure.

    > [WACOM.NOTE] Au cours de l'installation du module, il est possible que des erreurs indiquant que « Le projet importé ... est introuvable » s'affichent. Vous pouvez les ignorer sans problème.

4.  Socket.IO utilise WebSockets, qui n'est pas activé par défaut sur Azure. Pour activer WebSockets, utilisez la commande suivante :

        azure site set -w

    Entrez le nom du site web s'il vous est demandé.

    > [WACOM.NOTE]
    > La commande « azure site set -w » fonctionne uniquement avec les versions 0.7.4 et ultérieures de l'interface de ligne de commande interplateforme Azure. Vous pouvez également activer la prise en charge de WebSocket depuis le portail de gestion Azure.
    >
    > Pour activer WebSockets à partir du [portail de gestion Azure][], sélectionnez Configurer la page pour votre site web, sélectionnez « ON » pour l'entrée Web Sockets, puis cliquez sur Enregistrer.
    >
    > ![websockets][]

5.  Pour afficher le site web sur Azure, utilisez la commande suivante pour lancer votre navigateur web et accéder au site web hébergé :

        azure site browse

Votre application s'exécute à présent dans Azure et peut relayer
des messages de conversation instantanée entre différents clients en utilisant Socket.IO.

> [WACOM.NOTE] Par souci de simplification, cet exemple présente simplement une conversation instantanée entre des utilisateurs connectés à la même instance. Cela veut dire que si le service cloud crée deux instances de rôle de travail, un utilisateur pourra utiliser la conversation instantanée pour communiquer uniquement avec les autres utilisateurs connectés à la même instance de rôle de travail. Pour mettre à l'échelle l'application afin d'utiliser plusieurs instances de rôle, vous pouvez utiliser une technologie telle que Service Bus pour partager l'état de stockage Socket.IO entre plusieurs instances. Pour en savoir plus, consultez les exemples d'utilisation des files d'attente et rubriques de Service Bus dans le [référentiel GitHub Kit de développement logiciel (SDK) Azure pour Node.js][].

## Montée en charge

Vous pouvez faire monter en charge les applications Socket.IO en utilisant un **adaptateur** pour distribuer les messages et les événements entre plusieurs instances d'application. Plusieurs adaptateurs sont disponibles, mais [socket.io-redis][] est très facile à utiliser avec la fonctionnalité de cache Redis Azure.

> [WACOM.NOTE] Une autre condition requise à la montée en charge d'une solution Socket.IO est la prise en charge des sessions rémanentes. Celles-ci sont activées par défaut pour Sites Web Azure par l'intermédiaire d'Azure Request Routing. Pour plus d'informations, consultez [Affinité d'instance dans Sites Web Azure][].

### Création d'un cache Redis

Pour créer un cache, effectuez les étapes décrites dans [Création d'un cache dans le cache Azure Redis][].

> [WACOM.NOTE] Enregistrez le **Nom d'hôte** et la **Clé primaire** de votre cache, car ils seront nécessaires lors des étapes suivantes.

### Ajout des modules redis et socket.io-redis

1.  Sur la ligne de commande, accédez au répertoire **\\node\\chat** et exécutez la commande suivante :

        npm install socket.io-redis@0.1.3 redis@0.11.0 --save

    > [WACOM.NOTE] Les versions spécifiées dans cette commande sont celles utilisées lors des tests de cet article.

2.  Modifiez le fichier **app.js** en ajoutant les lignes suivantes juste après `var io = require('socket.io')(server);`

        var pub = require('redis').createClient(6379,'redishostname', {auth_pass: 'rediskey', return_buffers: true});
        var sub = require('redis').createClient(6379,'redishostname', {auth_pass: 'rediskey', return_buffers: true});

        var redis = require('socket.io-redis');
        io.adapter(redis({pubClient: pub, subClient: sub}));

    Remplacez **redishostname** et **rediskey** par le nom d'hôte et la clé de votre cache Redis.

    Cette commande permet de créer un client de publication et d'abonnement pour le cache Redis créé précédemment. Les clients sont ensuite utilisés avec l'adaptateur pour configurer Socket.IO de sorte à utiliser le cache Redis pour le transfert des messages et des événements entre les instances de votre application.

    > [WACOM.NOTE] Bien que l'adaptateur **socket.io-redis** puisse communiquer directement avec Redis, la version actuelle (au 14/07/2014) ne prend pas en charge l'authentification requise par le cache Redis Azure. La connexion initiale est donc créée à l'aide du module **redis**, puis le client est transferré à l'adaptateur **socket.io-redis**.
    >
    > Bien que le cache Redis Azure prenne en charge les connexions sécurisées sur le port 6380, les modules utilisés dans cet exemple ne prennent pas en charge les connexions sécurisées (au 14/07/2014). Le code ci-dessus utilise le port non sécurisé par défaut (6380).

3.  Enregistrez le fichier **app.js** modifié.

### Validation des modifications et redéploiement

Depuis la ligne de commande dans le répertoire **\\node\\chat**, utilisez les commandes suivantes pour valider les modifications et redéployer l'application.

    git add .
    git commit -m "implementing scale out"
    git push azure master

Une fois les modifications transmises au serveur, vous pouvez distribuer votre site sur plusieurs instances en exécutant la commande suivante.

    azure site scale instances --instances #

Où **\#** correspond au nombre d'instances à créer.

Vous pouvez vous connecter à votre site web à partir de plusieurs navigateurs ou ordinateurs pour vérifier que les messages sont envoyés correctement à tous les clients.

## <span id="tshooting"></span></a>Résolution des problèmes

### Limites de connexions

L'offre Sites Web Azure est disponible en plusieurs références SKU, qui déterminent les ressources accessibles à votre site, notamment le nombre de connexions WebSocket autorisées. Pour plus d'informations, consultez la page de [tarification des sites web][].

### Les messages ne sont pas envoyés avec WebSockets

Si les navigateurs clients utilisent l'interrogation longue plutôt que WebSockets, essayez d'appliquer les solutions suivantes.

-   **Essayez de limiter le transport à l'utilisation de WebSockets**

    Pour que Socket.IO utilise WebSockets comme transport de messagerie, le serveur et le client doivent tous deux prendre en charge WebSockets. Si ce n'est pas le cas, Socket.IO négocie un autre transport, tel que l'interrogation longue. La liste par défaut des transports utilisés par Socket.IO est `websocket, htmlfile, xhr-polling, jsonp-polling`. Vous pouvez le forcer à utiliser uniquement WebSockets en ajoutant le code suivant au fichier **app.js** après la ligne contenant `, nicknames = {};`.

        io.configure(function() {
          io.set('transports', ['websocket']);
        });

    > [WACOM.NOTE] Notez que les navigateurs plus anciens qui ne prennent pas en charge WebSockets ne pourront pas se connecter au site pendant que le code ci-dessus est actif, car il limite les communications à WebSockets.

-   **Utilisez le protocole SSL**

    WebSockets repose sur des en-têtes HTTP moins utilisés, tels que **Upgrade**. Certains périphériques réseau intermédiaires, tels que les proxys web, peuvent supprimer ces en-têtes. Pour éviter ce problème, vous pouvez établir la connexion WebSocket sur le protocole SSL.

    Le moyen le plus simple consiste à configurer Socket.IO avec `match origin protocol`. Socket.IO se charge alors de sécuriser les communications WebSockets de la même manière que la requête HTTP/HTTPS d'origine pour la page web. Si un navigateur utilise une URL HTTPS pour accéder à votre site web, les communications WebSocket ultérieures par l'intermédiaire de Socket.IO seront sécurisées sur SSL.

    Si vous voulez modifier cet exemple pour activer cette configuration, ajoutez le code suivant au fichier **app.js** après la ligne contenant `, nicknames = {};`.

        io.configure(function() {
          io.set('match origin protocol', true);
        });

-   **Vérifiez les paramètres de web.config**

    L'offre Sites Web Azure qui héberge des applications Node.js utilisent le fichier **web.config** pour acheminer les demandes entrantes vers l'application Node.js. Pour que WebSockets fonctionne correctement avec les applications Node.js, le fichier **web.config** doit contenir l'entrée suivante.

        <webSocket enabled="false"/>

    Cela désactive le module WebSockets IIS, qui comprend sa propre implémentation de WebSockets et entre en conflit avec les modules WebSocket spécifiques à Node.js tels que Socket.IO. Si cette ligne est absente ou a la valeur `true`, ce peut être la cause du non-fonctionnement du transport WebSocket sur votre application.

    Normalement, les applications Node.js n'incluent pas de fichier **web.config**. Par conséquent, l'offre Sites Web Azure en génère un automatiquement pour les applications Node.js lors de leur déploiement. Ce fichier étant généré automatiquement sur le serveur, vous devez utiliser l'URL FTP ou FTPS de votre site web pour l'afficher. Vous pouvez obtenir les URL FTP et FTPS de votre site dans le portail de gestion Azure en sélectionnant votre site web, puis le lien **Tableau de bord**. Les URL sont affichées dans la section **aperçu rapide**.

    > [WACOM.NOTE] Le fichier **web.config** est généré par Sites Web Azure uniquement si votre application n'en fournit pas. Si vous fournissez un fichier **web.config** à la racine de votre projet d'application, il sera utilisé par Sites Web Azure.

    Si l'entrée est absente ou a la valeur `true`, vous devez créer un fichier **web.config** à la racine de votre application Node.js et spécifier la valeur `false`. Vous trouverez ci-dessous un fichier **web.config** par défaut pour une application qui utilise **app.js** comme point d'entrée.

        <?xml version="1.0" encoding="utf-8"?>
        <!--
             This configuration file is required if iisnode is used to run node processes behind
             IIS or IIS Express.  For more information, visit:

             https://github.com/tjanczuk/iisnode/blob/master/src/samples/configuration/web.config
        -->

        <configuration>
          <system.webServer>
            <!-- Visit http://blogs.msdn.com/b/windowsazure/archive/2013/11/14/introduction-to-websockets-on-windows-azure-web-sites.aspx for more information on WebSocket support -->
            <webSocket enabled="false" />
            <handlers>
              <!-- Indicates that the server.js file is a node.js site to be handled by the iisnode module -->
              <add name="iisnode" path="app.js" verb="*" modules="iisnode"/>
            </handlers>
            <rewrite>
              <rules>
                <!-- Do not interfere with requests for node-inspector debugging -->
                <rule name="NodeInspector" patternSyntax="ECMAScript" stopProcessing="true">
                  <match url="^app.js\/debug[\/]?" />
                </rule>

                <!-- First we consider whether the incoming URL matches a physical file in the /public folder -->
                <rule name="StaticContent">
                  <action type="Rewrite" url="public{REQUEST_URI}"/>
                </rule>

                <!-- All other URLs are mapped to the node.js site entry point -->
                <rule name="DynamicContent">
                  <conditions>
                    <add input="{REQUEST_FILENAME}" matchType="IsFile" negate="True"/>
                  </conditions>
                  <action type="Rewrite" url="app.js"/>
                </rule>
              </rules>
            </rewrite>
            <!--
              You can control how Node is hosted within IIS using the following options:
                * watchedFiles: semi-colon separated list of files that will be watched for changes to restart the server
                * node_env: will be propagated to node as NODE_ENV environment variable
                * debuggingEnabled - controls whether the built-in debugger is enabled

              See https://github.com/tjanczuk/iisnode/blob/master/src/samples/configuration/web.config for a full list of options
            -->
            <!--<iisnode watchedFiles="web.config;*.js"/>-->
          </system.webServer>
        </configuration>

    > [WACOM.NOTE] Si votre application utilise un point d'entrée autre que **app.js**, vous devez remplacer toutes les occurrences d'**app.js** par le point d'entrée approprié. Par exemple, vous devez remplacer **app.js** par **server.js**.

## Étapes suivantes

Ce didacticiel vous a montré comment créer une application de conversation instantanée hébergée sur un site web Azure. Vous pouvez également héberger cette application dans le service cloud Azure. Pour savoir comment procéder, consultez la page [Création d'une application de conversation instantanée Node.js avec Socket.IO sur un service cloud Azure][1].

  []: http://socket.io/
  [Création d'une application de conversation instantanée Node.js avec Socket.IO sur un service cloud Azure]: http://www.windowsazure.com/fr-fr/develop/nodejs/tutorials/app-using-socketio/
  [version archivée ZIP ou GZ]: https://github.com/Automattic/socket.io/releases
  [Version d'évaluation gratuite d'Azure]: http://www.windowsazure.com/fr-fr/pricing/free-trial/?WT.mc_id=A7171371E
  [portail de gestion Azure]: https://manage.windowsazure.com
  [websockets]: ./media/web-sites-nodejs-chat-app-socketio/websockets.png
  [référentiel GitHub Kit de développement logiciel (SDK) Azure pour Node.js]: https://github.com/WindowsAzure/azure-sdk-for-node
  [socket.io-redis]: https://github.com/automattic/socket.io-redis
  [Affinité d'instance dans Sites Web Azure]: http://azure.microsoft.com/blog/2013/11/18/disabling-arrs-instance-affinity-in-windows-azure-web-sites/
  [Création d'un cache dans le cache Azure Redis]: http://go.microsoft.com/fwlink/p/?linkid=398592&clcid=0x409
  [tarification des sites web]: /fr-fr/pricing/details/web-sites/
  [1]: /fr-fr/develop/nodejs/tutorials/app-using-socketio/
