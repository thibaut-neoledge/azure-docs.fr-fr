<properties
	pageTitle="Créer une application de conversation instantanée Node.js avec Socket.IO dans Azure App Service"
	description="Didacticiel démontrant l’utilisation de socket.io dans une application web node.js hébergée sur Azure."
	services="app-service\web"
	documentationCenter="nodejs"
	authors="rmcmurray"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="nodejs"
	ms.topic="article"
	ms.date="08/11/2016"
	ms.author="robmcm"/>

# Créer une application de conversation instantanée Node.js avec Socket.IO dans Azure App Service

Socket.IO offre une communication en temps réel entre votre serveur node.js et vos clients à l'aide de WebSockets. Il prend également en charge l’utilisation d’autres transports (tels que l’interrogation longue) qui fonctionnent avec des navigateurs plus anciens. Ce didacticiel décrit la procédure d’hébergement d’une application de conversation instantanée Socket.IO sous la forme d’une application web Azure et explique comment la mettre à l’échelle à l’aide d’[Cache Redis Azure]. Pour plus d’informations sur Socket.IO, consultez <http://socket.io/>.

> [AZURE.NOTE] Les procédures de cette tâche s’appliquent à [App Service Web Apps]. Pour Cloud Services, consultez la page [Création d’une application de conversation instantanée Node.js avec Socket.IO sur un service cloud Azure].

## Télécharger l’exemple de conversation instantanée

Pour ce projet, nous utiliserons un exemple de conversation instantanée obtenu depuis le [référentiel GitHub Socket.IO]. Procédez comme suit pour télécharger l'exemple et l'ajouter au projet créé précédemment.

1.  Téléchargez une [version archivée ZIP ou GZ] du projet Socket.IO (la version 1.3.5 a été utilisée pour ce document).

1.  Extrayez l’archive et copiez le répertoire **examples\\chat** dans un nouvel emplacement. Par exemple, **\\node\\chat**.

## Modifier le fichier app.js et installer les modules

1.  Renommez le fichier **index.js** en **app.js**. Cela permet à Azure de détecter qu'il s'agit d'une application Node.js.

1.  Ouvrez le fichier **app.js** dans un éditeur de texte. Modifiez la ligne contenant `var io = require('../..')(server);` comme indiqué ci-dessous :

		var express = require('express');
		var app = express();
		var server = require('http').createServer(app);
		// var io = require('../..')(server);
        // New:
		var io = require('socket.io')(server);
		var port = process.env.PORT || 3000;

1. Ouvrez le fichier **package.json** et ajoutez une référence à socket.io sous `dependencies`, comme indiqué ci-dessous :

        "dependencies": {
		  "express": "3.4.8",
		  "socket.io": "1.3.5"
		}

1. Depuis la ligne de commande, accédez au répertoire **\\node\\chat** et utilisez la commande npm pour installer les modules requis par cette application :

        npm install

    Cette opération installe les modules dans un sous-dossier nommé **node\_modules**.

## Créer une application web Azure

Suivez la procédure ci-après pour créer une application web Azure, activer la publication Git, puis activer la prise en charge de WebSocket pour l’application web.

> [AZURE.NOTE] Pour suivre ce didacticiel, vous avez besoin d'un compte Azure. Si vous ne possédez pas de compte, vous pouvez créer un compte d'évaluation gratuit en quelques minutes. Pour plus d’informations, consultez la page <a href="http://www.windowsazure.com/pricing/free-trial/?WT.mc_id=A7171371E" target="_blank">Version d’essai gratuite d’Azure</a>.

1. Installez l’interface de ligne de commande Azure (CLI Azure) et connectez-la à votre abonnement Azure. Consultez [Installation et configuration de l’interface de ligne de commande Azure](../xplat-cli-install.md).

1. Si vous configurez un référentiel pour la première fois dans Azure, vous devez créer des informations d’identification de connexion. À partir de l’interface de ligne de commande Azure, entrez la commande suivante :

		azure site deployment user set [username] [password]

1. Accédez au répertoire **\\node\\chat**, puis utilisez la commande ci-après pour créer une application web Azure et un référentiel Git local. Cette commande crée également un Git distant nommé ’azure’.

		azure site create mysitename --git

	Vous devez remplacer ’mysitename’ par le nom unique de votre application web.

1. Validez les fichiers existants dans le référentiel local en utilisant les commandes suivantes :

		git add .
		git commit -m "Initial commit"

1. Utilisez la commande ci-après pour transmettre les fichiers au référentiel Azure Web Apps :

		git push azure master

	Lorsque vous y êtes invité, entrez vos informations d'identification de l'étape 2. Vous recevez des messages d'état au fur et à mesure que les modules sont importés sur le serveur. Une fois ce processus terminé, l’application est hébergée sur votre application web Azure.

 	> [AZURE.NOTE] Lors de l’installation du module, il est possible que des erreurs du type ’Le projet importé... est introuvable’ s’affichent. Vous pouvez les ignorer sans problème.

1. Socket.IO utilise WebSockets, qui n'est pas activé par défaut sur Azure. Pour activer WebSockets, utilisez la commande suivante :

		azure site set -w

	Si vous y êtes invité, entrez le nom de l’application web.

	>[AZURE.NOTE]
	La commande ’azure site set -w’ ne fonctionne qu’avec la version 0.7.4 ou une version ultérieure de l’interface de ligne de commande Azure. Vous pouvez également activer la prise en charge de WebSocket à l’aide du [portail Azure](https://portal.azure.com).
	>
	>Pour activer WebSockets à l’aide du portail Azure, cliquez sur l’application web dans le panneau Web Apps, puis sur **Tous les paramètres** > **Paramètres de l’application**. Sous **Web Sockets**, cliquez sur **Activé**. Cliquez ensuite sur **Enregistrer**.

1. Pour visualiser l’application web sur Azure, utilisez la commande ci-après afin de lancer votre navigateur web et d’accéder à l’application web hébergée :

		azure site browse

Votre application s’exécute à présent sur Azure et peut relayer des messages de conversation instantanée entre différents clients en utilisant Socket.IO.

## Montée en charge

Vous pouvez faire monter en charge les applications Socket.IO en utilisant un __adaptateur__ pour distribuer les messages et les événements entre plusieurs instances d'application. Plusieurs adaptateurs sont disponibles, mais [socket.io-redis] est très facile à utiliser avec la fonctionnalité de cache Redis Azure.

> [AZURE.NOTE] Une autre condition requise par la montée en charge d’une solution Socket.IO est la prise en charge des sessions rémanentes. Celles-ci sont activées par défaut pour Azure Web Apps par l’intermédiaire d’Azure Request Routing. Pour plus d'informations, consultez [Affinité d'instance dans Sites Web Azure].

### Création d'un cache Redis

Pour créer un cache, effectuez les étapes décrites dans [Création d'un cache dans le cache Azure Redis].

> [AZURE.NOTE] Enregistrez le __Nom d’hôte__ et la __Clé primaire__ de votre cache, car ces paramètres seront nécessaires lors des étapes suivantes.

### Ajout des modules redis et socket.io-redis

1. Sur la ligne de commande, accédez au répertoire __\\node\\chat__ et exécutez la commande suivante :

		npm install socket.io-redis@0.1.4 redis@0.12.1 --save

	> [AZURE.NOTE] Les versions spécifiées dans cette commande sont celles utilisées lors des tests de cet article.

1. Modification du fichier __app.js__ en lui ajoutant les lignes suivantes juste après `var io = require('socket.io')(server);`

		var pub = require('redis').createClient(6379,'redishostname', {auth_pass: 'rediskey', return_buffers: true});
		var sub = require('redis').createClient(6379,'redishostname', {auth_pass: 'rediskey', return_buffers: true});

		var redis = require('socket.io-redis');
		io.adapter(redis({pubClient: pub, subClient: sub}));

	Remplacez __redishostname__ et __rediskey__ par le nom d'hôte et la clé de votre cache Redis.

	Cette commande permet de créer un client de publication et d'abonnement pour le cache Redis créé précédemment. Les clients sont ensuite utilisés avec l'adaptateur pour configurer Socket.IO de sorte à utiliser le cache Redis pour le transfert des messages et des événements entre les instances de votre application.

	> [AZURE.NOTE] Bien que l’adaptateur __socket.io-redis__ puisse communiquer directement avec Redis, la version actuelle ne prend pas en charge l’authentification requise par le cache Azure Redis. La connexion initiale est donc créée à l'aide du module __redis__, puis le client est transferré à l'adaptateur __socket.io-redis__.
	>
	> Bien que Cache Redis Azure prenne en charge les connexions sécurisées sur le port 6380, les modules utilisés dans cet exemple ne gèrent pas les connexions sécurisées depuis le 14/07/2014. Le code ci-dessus utilise le port non sécurisé par défaut 6379.

1. Enregistrez le fichier __app.js__ modifié.

### Validation des modifications et redéploiement

Depuis la ligne de commande dans le répertoire __\\node\\chat__, utilisez les commandes suivantes pour valider les modifications et redéployer l'application.

	git add .
	git commit -m "implementing scale out"
	git push azure master

Une fois les modifications transmises au serveur, vous pouvez distribuer votre site sur plusieurs instances en exécutant la commande suivante.

	azure site scale instances --instances #

Où __#__ correspond au nombre d'instances à créer.

Vous pouvez vous connecter à votre application web à partir de plusieurs navigateurs ou ordinateurs pour vérifier que les messages sont correctement envoyés à tous les clients.

## Résolution de problèmes

### Limites de connexions

La fonctionnalité Azure Web Apps est disponible dans plusieurs références (SKU) qui déterminent les ressources disponibles pour votre site. Cela inclut le nombre de connexions WebSocket autorisées. Pour plus d’informations, consultez la [page de tarification de Web Apps].

### Les messages ne sont pas envoyés avec WebSockets

Si les navigateurs clients utilisent l'interrogation longue plutôt que WebSockets, essayez d'appliquer les solutions suivantes.

* **Tentative de limitation du transport à WebSockets**

	Pour que Socket.IO utilise WebSockets comme transport de messagerie, le serveur et le client doivent tous deux prendre en charge WebSockets. Si ce n'est pas le cas, Socket.IO négocie un autre transport, tel que l'interrogation longue. La liste par défaut des transports utilisés par Socket.IO est ` websocket, htmlfile, xhr-polling, jsonp-polling`. Vous pouvez le forcer à n’utiliser que WebSockets en ajoutant le code suivant au fichier **app.js** après la ligne contenant `, nicknames = {};`.

		io.configure(function() {
		  io.set('transports', ['websocket']);
		});

	> [AZURE.NOTE] Notez que les navigateurs plus anciens qui ne prennent pas en charge WebSockets ne pourront pas se connecter au site pendant que le code ci-dessus est actif, car il limite les communications à WebSockets.

* **Utilisation du protocole SSL**

	WebSockets repose sur des en-têtes HTTP moins utilisés, tels que **Upgrade**. Certains périphériques réseau intermédiaires, tels que les proxys web, peuvent supprimer ces en-têtes. Pour éviter ce problème, vous pouvez établir la connexion WebSocket sur le protocole SSL.

	Le moyen le plus simple consiste à configurer Socket.IO avec `match origin protocol`. Socket.IO se charge alors de sécuriser les communications WebSockets de la même manière que la requête HTTP/HTTPS d'origine pour la page web. Si un navigateur utilise une URL HTTPS pour accéder à votre site web, les communications WebSocket ultérieures par l'intermédiaire de Socket.IO seront sécurisées sur SSL.

	Pour modifier cet exemple et activer cette configuration, ajoutez le code suivant au fichier **app.js** après la ligne contenant `, nicknames = {};`.

		io.configure(function() {
		  io.set('match origin protocol', true);
		});

* **Vérification des paramètres de web.config**

	Les applications web Azure qui hébergent des applications Node.js utilisent le fichier **web.config** pour acheminer les demandes entrantes vers l’application Node.js. Pour que WebSockets fonctionne correctement avec les applications Node.js, le fichier **web.config** doit contenir l'entrée suivante.

		<webSocket enabled="false"/>

	Cela désactive le module WebSockets IIS, qui comprend sa propre implémentation de WebSockets et entre en conflit avec les modules WebSocket spécifiques à Node.js tels que Socket.IO. Si cette ligne est absente ou a la valeur `true`, ceci peut expliquer que le transport WebSocket ne fonctionne pas sur votre application.

	Normalement, les applications Node.js n'incluent pas de fichier **web.config**. Par conséquent, l'offre Sites Web Azure en génère un automatiquement pour les applications Node.js lors de leur déploiement. Ce fichier étant généré automatiquement sur le serveur, vous devez utiliser l'URL FTP ou FTPS de votre site web pour l'afficher. Vous pouvez obtenir les URL FTP et FTPS de votre site dans le portail Azure Classic en sélectionnant votre application web, puis le lien **Tableau de bord**. Les URL sont affichées dans la section **aperçu rapide**.

	> [AZURE.NOTE] Le fichier **web.config** n’est généré par Sites Web Azure que si votre application n’en fournit pas. Si vous fournissez un fichier **web.config** à la racine de votre projet d’application, il sera utilisé par Azure Web Apps.

	Si l’entrée est absente ou a la valeur `true`, vous devez créer un fichier **web.config** à la racine de votre application Node.js et spécifier la valeur `false`. Vous trouverez ci-dessous un fichier **web.config** par défaut pour une application qui utilise **app.js** comme point d'entrée.

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
		      <!-- Indicates that the server.js file is a node.js web app to be handled by the iisnode module -->
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

		        <!-- All other URLs are mapped to the node.js web app entry point -->
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

	Si votre application utilise un point d’entrée autre que **app.js**, vous devez remplacer toutes les occurrences d’**app.js** par le point d’entrée approprié. Par exemple, vous devez remplacer **app.js** par **server.js**.

>[AZURE.NOTE] Si vous voulez vous familiariser avec Azure App Service avant d’ouvrir un compte Azure, accédez à la page [Essayer App Service]. Vous pourrez créer immédiatement une application web temporaire dans App Service. Aucune carte de crédit n’est requise ; vous ne prenez aucun engagement.

## Étapes suivantes

Ce didacticiel vous a expliqué comment créer une application de conversation instantanée hébergée dans une application web Azure. Vous pouvez également héberger cette application dans le service cloud Azure. Pour savoir comment procéder, consultez la page [Création d'une application de conversation instantanée Node.js avec Socket.IO sur un service cloud Azure].

Pour plus d'informations, consultez aussi le [Centre pour développeurs Node.js].

## Changements apportés

* Pour obtenir un guide présentant les modifications apportées dans le cadre de la transition entre Sites Web et App Service, consultez la page [Azure App Service et les services Azure existants].

<!-- URL List -->

[Cache Redis Azure]: /documentation/services/redis-cache/
[App Service Web Apps]: http://go.azure.microsoft.com/.com/fwlink/?LinkId=529714
[page de tarification de Web Apps]: http://go.azure.microsoft.com/.com/fwlink/?LinkId=511643
[Création d'une application de conversation instantanée Node.js avec Socket.IO sur un service cloud Azure]: ../cloud-services/cloud-services-nodejs-chat-app-socketio.md
[Création d’une application de conversation instantanée Node.js avec Socket.IO sur un service cloud Azure]: ../cloud-services/cloud-services-nodejs-chat-app-socketio.md
[Install and Configure the Azure CLI]: ../xplat-cli-install.md
[Azure App Service et les services Azure existants]: http://go.azure.microsoft.com/.com/fwlink/?LinkId=529714
[Centre pour développeurs Node.js]: /develop/nodejs/
[Essayer App Service]: http://go.azure.microsoft.com/.com/fwlink/?LinkId=523751
[Affinité d'instance dans Sites Web Azure]: https://azure.azure.microsoft.com/.com/blog/2013/11/18/disabling-arrs-instance-affinity-in-windows-azure-web-sites/
[Création d'un cache dans le cache Azure Redis]: ../redis-cache/cache-dotnet-how-to-use-azure-redis-cache.md

[socket.io-redis]: https://github.com/socketio/socket.io-redis
[référentiel GitHub Socket.IO]: https://github.com/socketio/socket.io
[version archivée ZIP ou GZ]: https://github.com/socketio/socket.io/releases

<!-- IMG List -->

[chat-example-view]: ./media/web-sites-nodejs-chat-app-socketio/socketio-2.png
[npm-output]: ./media/web-sites-nodejs-chat-app-socketio/socketio-7.png
[completed-app]: ./media/web-sites-nodejs-chat-app-socketio/websitesocketcomplete.png

<!---HONumber=AcomDC_0817_2016-->