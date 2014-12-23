<properties urlDisplayName="Website Using Socket.IO" pageTitle="Didacticiel Node.js avec Socket.IO - Azure" metaKeywords="Azure Node.js socket.io tutorial, Azure Node.js socket.io, Azure Node.js tutorial" description="A tutorial that demonstrates using socket.io in a node.js website hosted on Azure." metaCanonical="" services="web-sites" documentationCenter="nodejs" title="Build a Node.js Chat Application with Socket.IO on an Azure Website" authors="larryfr" solutions="" videoId="" scriptId="" manager="wpickett" editor="mollybos" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="nodejs" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr" />




#Création d'une application de conversation instantanée Node.js avec Socket.IO sur un site web Azure

Socket.IO offre une communication en temps réel entre votre serveur node.js et vos clients à l'aide de WebSockets. Il prend également en charge l'utilisation d'autres transports (tels que l'interrogation longue) qui fonctionnent avec des navigateurs plus anciens. Ce didacticiel vous explique comment héberger une application de conversation instantanée socket.IO en tant que site web Azure. Pour plus d'informations sur Socket.IO, consultez le site [http://socket.io/][socketio].

> [WACOM.NOTE] Les procédures de cette tâche s'appliquent aux sites web Azure. Pour les services cloud, consultez la page <a href="http://www.windowsazure.com/fr-fr/develop/nodejs/tutorials/app-using-socketio/">Création d'une application de conversation instantanée Node.js avec Socket.IO sur un service cloud Azure</a>.


## <a id="Download"></a>Téléchargement de l'exemple de conversation instantanée

Pour ce projet, nous allons utiliser un exemple de conversation instantanée du référentiel [GitHub
Référentiel GitHub]. Procédez comme suit pour télécharger l'exemple
et l'ajouter au projet que vous avez déjà créé.

1.  Téléchargez une [version archivée ZIP ou GZ][] du projet Socket.IO (la version 1.0.6 a été utilisée pour ce document)


3.  Extrayez l'archive et copiez le répertoire **examples\\chat**
    dans un nouvel emplacement. Par exemple, 
    **\\node\\chat**.

## <a id="Modify"></a>Modification du fichier App.js et installation des modules

Avant de déployer l'application sur Azure, nous devons
apporter quelques modifications mineures.

1.  Renommez le fichier **index.js** en **app.js**. Cela permet à Azure de détecter qu'il s'agit d'une application Node.js.

1.  Ouvrez le fichier **app.js** dans le Bloc-notes ou un autre éditeur de texte.

2.  Recherchez la section **Module dependencies** au début du fichier app.js et remplacez la ligne contenant var io = require('../..')(server); par var io = require('socket.io')(server); comme indiqué ci-dessous :

		var express = require('express');
		var app = express();
		var server = require('http').createServer(app);
		// var io = require('../..')(server);
		var io = require('socket.io')(server);
		var port = process.env.PORT || 3000;


Après avoir enregistré les modifications apportées à app.js, procédez comme suit pour
installer les modules nécessaires :

1.  Dans la ligne de commande, remplacez les répertoires par le répertoire **\\node\\chat**, puis exécutez la commande suivante pour installer les modules requis par cette application :

         npm install

    Ceci installe les modules répertoriés dans le fichier package.json. Une fois
    la commande terminée, un texte similaire à celui présenté ci-dessous doit
    s'afficher :

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
    et qu'il est directement référencé par la bibliothèque Socket.IO par
    un chemin d'accès relatif, Socket.IO n'est pas référencé dans le fichier package.json.
    Vous devez donc l'installer en exécutant la commande suivante :

        npm install socket.io@1.0.6 -save

	> [WACOM.NOTE] Bien que les étapes décrites dans cet article puissent fonctionner avec les versions plus récentes de Socket.IO, nous les avons testées avec la version 1.0.6.

## <a id="Publish"></a>Création d'un site web Azure

Suivez cette procédure pour créer un site web Azure, activer la publication Git, puis activer la prise en charge de WebSocket pour le site web.

> [WACOM.NOTE]Pour effectuer ce didacticiel, vous avez besoin d'un compte Azure. Si vous ne possédez pas de compte, vous pouvez créer un compte d'évaluation gratuit en quelques minutes. Pour plus d'informations, consultez la page <a href="http://www.windowsazure.com/fr-fr/pricing/free-trial/?WT.mc_id=A7171371E" target="_blank">Azure Free Trial</a>.

1. From the command-line, change directories to the **\\node\chat** directory and use the following command to create a new Azure Website and enable a Git repository for the website and the local directory. This will also create a Git remote named 'azure'.

		azure site create mysitename --git

	You must replace 'mysitename' with a unique name for your website.

2. Commit the existing files to the local repository by using the following commands:

		git add .
		git commit -m "Initial commit"

3. Push the files to the Azure Website repository with the following command:

		git push azure master

	You will receive status messages as modules are imported on the server. Once this process has completed, the application will be hosted on your Azure Website.

 	> [WACOM.NOTE] During module installation, you may notice errors that 'The imported project ... was not found'. These can safely be ignored.

4. Socket.IO uses WebSockets, which are not enabled by default on Azure. To enable web sockets, use the following command:

		azure site set -w

	If prompted, enter the name of the website.

	>[WACOM.NOTE]
	>The 'azure site set -w' command will only work with version 0.7.4 or higher of the Azure Cross-Platform Command-Line Interface. You can also enable WebSocket support using the Azure Management Portal.
	>
	>To enable WebSockets using the [Azure Management Portal](https://manage.windowsazure.com), select the Configure page for your website, select 'ON' for the Web Sockets entry, and then click Save.
	>	
	>![websockets](./media/web-sites-nodejs-chat-app-socketio/websockets.png)
	
5. To view the website on Azure, use the following command to launch your web browser and navigate to the hosted website:

		azure site browse

Your application is now running on Azure, and can relay chat
messages between different clients using Socket.IO.

> [WACOM.NOTE] For simplicity, this sample is limited to chatting between users connected to the same instance. This means that if the cloud service creates two worker role instances, users will only be able to chat with others connected to the same worker role instance. To scale the application to work with multiple role instances, you could use a technology like Service Bus to share the Socket.IO store state across instances. For examples, see the Service Bus Queues and Topics usage samples in the <a href="https://github.com/WindowsAzure/azure-sdk-for-node">Azure SDK for Node.js GitHub repository</a>.

##Scale out

Socket.IO applications can be scaled out by using an __adapter__ to distribute messages and events between multiple application instances. While there are several adapters available, the [socket.io-redis](https://github.com/automattic/socket.io-redis) adapter can be easily used with the Azure Redis Cache feature.

> [WACOM.NOTE] An additional requirement for scaling out a Socket.IO solution is support for sticky sessions. Sticky sessions are enabled by default for Azure Websites through Azure Request Routing. For more information, see [Instance Affinity in Azure Web Sites](http://azure.microsoft.com/blog/2013/11/18/disabling-arrs-instance-affinity-in-windows-azure-web-sites/)

###Create a Redis cache

Perform the steps in [Create a cache in Azure Redis Cache](http://go.microsoft.com/fwlink/p/?linkid=398592&clcid=0x409) to create a new cache.

> [WACOM.NOTE] Save the __Host name__ and __Primary key__ for your cache, as these will be needed in the next steps.

###Add the redis and socket.io-redis modules

1. From a command-line, change to the __\\node\\chat__ directory and use the following command.

		npm install socket.io-redis@0.1.3 redis@0.11.0 --save

	> [WACOM.NOTE] The versions specified in this command are the versions used when testing this article.

2. Modify the __app.js__ file to add the following lines immediately after `var io = require('socket.io')(server);`

		var pub = require('redis').createClient(6379,'redishostname', {auth_pass: 'rediskey', return_buffers: true});
		var sub = require('redis').createClient(6379,'redishostname', {auth_pass: 'rediskey', return_buffers: true});
		
		var redis = require('socket.io-redis');
		io.adapter(redis({pubClient: pub, subClient: sub}));


	Replace __redishostname__ and __rediskey__ with the host name and key for your Redis cache.

	This will create a publish and subscribe client to the Redis cache created previously. The clients are then used with the adapter to configure Socket.IO to use the Redis cache for passing messages and events between instances of your application

	> [WACOM.NOTE] While the __socket.io-redis__ adapter can communicate directly to Redis, the current version (as of 7/14/2014) does not support the authentication required by Azure Redis cache. So the initial connection is created using the __redis__ module, then the client is passed to the __socket.io-redis__ adapter.
	> 
	> While Azure Redis Cache supports secure connections using port 6380, the modules used in this example do not support secure connections as of 7/14/2014. The above code uses the default, unsecure port of 6380.

3. Save the modified __app.js__

###Commit changes and redeploy

From the command-line in the __\\node\\chat__ directory, use the following commands to commit changes and redeploy the application.

	git add .
	git commit -m "implementing scale out"
	git push azure master

Once the changes have been pushed to the server, you can scale your site across multiple instances by using the following command.

	azure site scale instances --instances #

Where __#__ is the number of instances to create. 

You can connect to your website from multiple browsers or computers to verify that messages are correctly sent to all clients.

##<a id="tshooting"></a>Troubleshooting

###Connection limits

Azure Websites is available in multiple SKUs, which determine the resources available to your site. This includes the number of allowed WebSocket connections. For more information, see the [Web Sites Pricing page][pricing].

###Messages aren't being sent using WebSockets

If client browsers keep falling back to long polling instead of using WebSockets, it may be because of one of the following.

* **Try limiting the transport to just WebSockets**

	In order for Socket.IO to use WebSockets as the messaging transport, both the server and client must support WebSockets. If one or the other does not, Socket.IO will negotiate another transport, such as long polling. The default list of transports used by Socket.IO is ` websocket, htmlfile, xhr-polling, jsonp-polling`. You can force it to only use WebSockets by adding the following code to the **app.js** file, after the line containing `, nicknames = {};`.

		io.configure(function() {
		  io.set('transports', ['websocket']);
		});

	> [WACOM.NOTE] Note that older browsers that do not support WebSockets will not be able to connect to the site while the above code is active, as it restricts communication to WebSockets only.

* **Use SSL**

	WebSockets relies on some lesser used HTTP headers, such as the **Upgrade** header. Some intermediate network devices, such as web proxies, may remove these headers. To avoid this problem, you can establish the WebSocket connection over SSL.

	An easy way to accomplish this is to configure Socket.IO to `match origin protocol`. This instructs Socket.IO to secure WebSockets communication the same as the originating HTTP/HTTPS request for the web page. If a browser uses an HTTPS URL to visit your website, subsequent WebSocket communications through Socket.IO will be secured over SSL.

	To modify this example to enable this configuration, add the following code to the **app.js** file after the line containing `, nicknames = {};`.

		io.configure(function() {
		  io.set('match origin protocol', true);
		});

* **Verify web.config settings**

	Azure Websites that host Node.js applications use the **web.config** file to route incoming requests to the Node.js application. For WebSockets to function correctly with Node.js applications, the **web.config** must contain the following entry.

		<webSocket enabled="false"/>

	Cela désactive le module WebSockets IIS, qui comprend sa propre implémentation de WebSockets et entre en conflit avec les modules WebSocket spécifiques de Node.js tels que Socket.IO. Si cette ligne est absente ou a la valeur true, ce peut être la cause du non-fonctionnement du transport WebSocket sur votre application.

	Normalement, les applications Node.js n'incluent pas de fichier **web.config**. Par conséquent, l'offre Sites Web Azure en génère un automatiquement pour les applications Node.js lors de leur déploiement. Ce fichier étant généré automatiquement sur le serveur, vous devez utiliser l'URL FTP ou FTPS de votre site web pour l'afficher. Vous pouvez obtenir les URL FTP et FTPS de votre site dans le portail de gestion Azure en sélectionnant votre site web, puis le lien **Tableau de bord**. Les URL sont affichées dans la section **Aperçu rapide**.

	> [WACOM.NOTE] Le fichier **web.config** est généré par Sites Web Azure uniquement si votre application n'en fournit pas. Si vous fournissez un fichier **web.config** à la racine de votre projet d'application, il sera utilisé par Sites Web Azure.

	Si l'entrée est absente ou a la valeur true, vous devez créer un fichier **web.config** à la racine de votre application Node.js et spécifier la valeur false.   Vous trouverez ci-dessous un fichier **web.config** par défaut pour une application qui utilise **app.js** comme point d'entrée.

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

##Étapes suivantes

Ce didacticiel vous a montré comment créer une application de conversation instantanée hébergée sur un site web Azure. Vous pouvez également héberger cette application dans le service cloud Azure. Pour savoir comment procéder, consultez la page [Création d'une application de conversation instantanée Node.js avec Socket.IO sur un service cloud Azure][cloudservice].

[socketio]: http://socket.io/
[completed-app]: ./media/web-sites-nodejs-chat-app-socketio/websitesocketcomplete.png
[Référentiel GitHub Socket.IO]: https://github.com/Automattic/socket.io
[release]: https://github.com/Automattic/socket.io/releases
[cloudservice]: /fr-fr/develop/nodejs/tutorials/app-using-socketio/

[chat-example-view]: ./media/web-sites-nodejs-chat-app-socketio/socketio-2.png
[npm-output]: ./media/web-sites-nodejs-chat-app-socketio/socketio-7.png
[pricing]: /fr-fr/pricing/details/web-sites/
