<properties 
	pageTitle="Prise en main d'Azure Notification Hubs" 
	description="Découvrez comment utiliser Azure Notification Hubs pour envoyer des notifications Push aux utilisateurs." 
	services="notification-hubs" 
	documentationCenter="" 
	authors="piyushjo" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="notification-hubs" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-chrome" 
	ms.devlang="JavaScript" 
	ms.topic="article" 
	ms.date="03/15/2015" 
	ms.author="piyushjo"/>
	
# Prise en main de Notification Hubs

[AZURE.INCLUDE [notification-hubs-selector-get-started](../includes/notification-hubs-selector-get-started.md)]

Cette rubrique montre comment utiliser Azure Notification Hubs pour envoyer des notifications Push vers une application Chrome.

Un des principaux avantages de l'utilisation des notifications sur une application Chrome est que les notifications apparaissent dans le navigateur Google Chrome et qu'il n'est pas nécessaire que l'application soit en cours d'exécution ou ouverte dans le navigateur (même si le navigateur Chrome doit être en cours d'exécution). Vous obtenez également une vue consolidée de toutes vos notifications dans la fenêtre notifications de Chrome. 

>[AZURE.NOTE] Ce n'est pas une notification générique Push dans le navigateur. Elle est spécifique aux applications Chrome : consultez la page [Vue d'ensemble des applications Chrome] pour plus d'informations. Les applications Chrome étaient connues sous le nom d'" applications empaquetées " et sont différentes des " applications hébergées ", qui sont plus simples. Consultez la page [Applications Web installables] pour une présentation des différences. Les applications Chrome peuvent également être exécutées sur un mobile (Android et iOS) à l'aide d'Apache Cordova. Consultez la page [Applications Chrome sur mobile] pour en savoir plus. 

Dans ce didacticiel, nous allons créer une application Chrome qui reçoit des notifications Push à l'aide de Google Cloud Messaging (GCM). Une fois la création terminée, vous pourrez envoyer des notifications Push à tous les utilisateurs de Chrome qui ont installé cette application Chrome. 

Ce didacticiel vous familiarise avec les étapes de base pour activer les notifications Push :

* [Activation de Google Cloud Messaging (GCM)](#register)
* [Configuration de Notification Hub](#configure-hub)
* [Connexion de votre application Chrome à Notification Hub](#connect-app)
* [Envoi d'une notification à votre application Chrome](#send)
* [Étapes suivantes](#next-steps)

Ce didacticiel présente un scénario de diffusion simple utilisant Notification Hubs. La configuration de GCM et d'Azure Notification Hub est identique à celle pour Android, étant donné que [Google Cloud Messaging pour Chrome] a été déprécié et qu'il prend désormais en charge les appareils Android et les instances Chrome. 

Suivez scrupuleusement les didacticiels au sein des Étapes suivantes afin d'apprendre à utiliser Notification Hubs pour accéder à des utilisateurs et des groupes d'appareils spécifiques. 

>[AZURE.NOTE] Pour suivre ce didacticiel, vous avez besoin d'un compte Azure actif. Si vous ne possédez pas de compte, vous pouvez créer un compte d'évaluation gratuit en quelques minutes. Pour plus d'informations, consultez la page [Version d'évaluation gratuite d'Azure](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Ffr-fr%2Fdocumentation%2Farticles%notification-hubs-chrome-get-started%2F).

##<a id="register"></a>Activation de Google Cloud Messaging (GCM)

1. Accédez au site web [Google Cloud Console], connectez-vous avec vos informations d'identification de compte Google, puis cliquez sur le bouton **Create Project**. Saisissez un **Nom de projet** adéquat et cliquez sur le bouton **Create**. 

   	![][1]

2. Notez le **Numéro de projet** du projet que vous venez de créer, affiché sur la page des projets . Il vous servira d'**ID d'expéditeur GCM** dans l'application Chrome pour votre inscription auprès de GCM.

   	![][2]

3. Dans le volet gauche, cliquez sur **APIs & auth**, puis faites défiler la page et cliquez sur la touche bascule pour activer **Google Cloud Messaging pour Android**. Vous n'avez pas besoin d'activer *Google Cloud Messaging for Chrome*. Il est également possible que le nom passe simplement à *Google Cloud Messaging* à l'avenir. 

   	![][3]

4. Dans le volet gauche, cliquez sur **Credentials** -> **Create New Key** -> **Server Key** -> **Create**

   	![][4]

5. Notez la valeur **API KEY** du serveur. Vous configurerez ensuite cela dans votre Notification Hub afin qu'il puisse envoyer des notifications Push vers GCM. 

   	![][5]

##<a id="configure-hub"></a>Configuration de Notification Hub

1. Connectez-vous au **[Portail de gestion Azure]**, puis cliquez sur **+ NOUVEAU** dans la partie inférieure gauche de l'écran.

2. Cliquez sur **Services d'application**, sur **Service Bus**, sur **Concentrateur de notification**, puis sur **Création rapide**. Tapez un nom pour votre Notification Hub, sélectionnez une région, puis cliquez sur **Create a new Notification Hub**.

   	![][6]

4. Accédez au concentrateur de notification que vous venez de créer. Cliquez sur l'espace de noms qui héberge votre concentrateur de notification (généralement ***nom_du_concentrateur_de_notification*-ns**).   

   	![][7]

5. Cliquez sur l'onglet **concentrateurs de notification** en haut de la page.

   	![][8]

6. Cliquez maintenant sur l'onglet **Configuration** en haut de la page. 

   	![][9]

7. Dans l'onglet **Configuration**, faites défiler jusqu'à la section **Paramètres de messagerie de google cloud**, entrez la valeur de **clé API** obtenue dans la section précédente, puis cliquez sur **Enregistrer**.

   	![][10]

8. Sélectionnez l'onglet **Tableau de bord** en haut, puis cliquez sur **Informations de connexion** en bas de la page. 

   	![][11]
 
9. Notez la valeur **DefaultListenSharedAccessSignature** (dont vous aurez besoin au sein de l'application Chrome à inscrire dans le concentrateur de notification) et **DefaultFullSharedAccessSignature** (dont vous aurez besoin pour envoyer des notifications) 

   	![][12]

Votre concentrateur de notification est maintenant configuré pour fonctionner avec GCM et vous disposez des chaînes de connexion requises pour poursuivre la configuration.

##<a id="connect-app"></a>Connecter votre application Chrome au concentrateur de notification

###Création d'une application Chrome
L'exemple ci-dessous repose sur un [exemple GCM d'application Chrome] et utilise la méthode recommandée pour créer une application Chrome. Dans les sections ci-dessous, nous présenterons les étapes liées à Azure Notification Hub. Il est recommandé de télécharger la source de cette application Chrome depuis [l'exemple de concentrateur de notification d'application Chrome].

L'application Chrome est créée à l'aide de JavaScript et vous pouvez utiliser votre éditeur favori pour la création. 

1. Voici à quoi ressemblera cette application Chrome. 

   	![][15]

2. Créez un dossier et nommez-le **ChromePushApp**. Vous pouvez toutefois le nommer comme vous le souhaitez. 

3. Téléchargez *crypto-js library* depuis la [bibliothèque crypto-js] dans ce dossier. Ce dossier de bibliothèque contient deux sous-dossiers : *components* et *rollups*. 

4. Créez un fichier manifest.json. Toutes les applications Chrome reposent sur un fichier manifeste qui décrit les métadonnées de l'application, notamment ses autorisations.

		{
		  "name": "NH-GCM Notifications",
		  "description": "Chrome platform app.",
		  "manifest_version": 2,
		  "version": "0.1",
		  "app": {
		    "background": {
		      "scripts": ["background.js"]
		    }
		  },
		  "permissions": ["gcm", "storage", "notifications", "https://*.servicebus.windows.net/*"],
		  "icons": { "128": "gcm_128.png" }
		}
	
	Notez l'élément *permissions* qui spécifie que cette application Chrome pourra recevoir des notifications Push de GCM. Il doit également spécifier l'URI du concentrateur de notification Azure où l'application Chrome effectuera un appel REST pour l'inscription.
	Cet exemple utilise un fichier icône gcm_128.png que vous trouverez dans la source réutilisée de l'exemple GCM d'origine. Vous pouvez utiliser l'image que vous souhaitez. 
 
5. Créez un fichier appelé background.js en utilisant le code suivant :

		// Returns a new notification ID used in the notification.
		function getNotificationId() {
		  var id = Math.floor(Math.random() * 9007199254740992) + 1;
		  return id.toString();
		}
		
		function messageReceived(message) {
		  // A message is an object with a data property that
		  // consists of key-value pairs.
		
		  // Concatenate all key-value pairs to form a display string.
		  var messageString = "";
		  for (var key in message.data) {
		    if (messageString != "")
		      messageString += ", "
		    messageString += key + ":" + message.data[key];
		  }
		  console.log("Message received: " + messageString);
		
		  // Pop up a notification to show the GCM message.
		  chrome.notifications.create(getNotificationId(), {
		    title: 'GCM Message',
		    iconUrl: 'gcm_128.png',
		    type: 'basic',
		    message: messageString
		  }, function() {});
		}
		
		var registerWindowCreated = false;
		
		function firstTimeRegistration() {
		  chrome.storage.local.get("registered", function(result) {
		
		    registerWindowCreated = true;
		    chrome.app.window.create(
		      "register.html",
		      {  width: 520,
		         height: 500,
		         frame: 'chrome'
		      },
		      function(appWin) {}
		    );
		  });
		}
		
		// Set up a listener for GCM message event.
		chrome.gcm.onMessage.addListener(messageReceived);
		
		// Set up listeners to trigger the first time registration.
		chrome.runtime.onInstalled.addListener(firstTimeRegistration);
		chrome.runtime.onStartup.addListener(firstTimeRegistration);

	Il s'agit du fichier qui affiche le code html de la fenêtre de l'application Chrome (*register.html*) et qui définit également le gestionnaire *messageReceived* pour gérer les notifications Push entrantes. 

6. Créez un fichier appelé *register.html* qui définit l'interface utilisateur de l'application Chrome. Notez que cet exemple utilise *CryptoJS v3.1.2*. Si vous avez téléchargé une autre version, corrigez le chemin d'accès de script src. 

		<html>
		
		<head>
		<title>GCM Registration</title>
		<script src="register.js"></script>
		<script src="CryptoJS v3.1.2/rollups/hmac-sha256.js"></script>
		<script src="CryptoJS v3.1.2/components/enc-base64-min.js"></script>
		</head>
		
		<body>
		
		Sender ID:<br/><input id="senderId" type="TEXT" size="20"><br/>
		<button id="registerWithGCM">Register with GCM</button>
		<br/>
		<br/>
		<br/>
		
		Notification Hub Name:<br/><input id="hubName" type="TEXT" style="width:400px"><br/><br/> 
		Connection String:<br/><textarea id="connectionString" type="TEXT" style="width:400px;height:60px"></textarea> 
		
		<br/>
		
		<button id="registerWithNH" disabled="true">Register with Azure Notification Hubs</button>
		
		<br/>
		<br/>
		
		<textarea id="console" type="TEXT" readonly style="width:500px;height:200px;background-color:#e5e5e5;padding:5px"></textarea> 
		
		</body>
		
		</html>

7. Créez un fichier appelé *register.js* avec le code ci-dessous. Ce fichier spécifie le script derrière *register.html*. Les applications Chrome n'autorisent pas l'exécution inline. Il convient donc de créer un script de sauvegarde distinct pour votre interface utilisateur. 

		var registrationId = "";
		var hubName        = "", connectionString = "";
		var originalUri    = "", targetUri = "", endpoint = "", sasKeyName = "", sasKeyValue = "", sasToken = "";
		
		window.onload = function() { 
		   document.getElementById("registerWithGCM").onclick = registerWithGCM;  
		   document.getElementById("registerWithNH").onclick = registerWithNH; 
		   updateLog("You have not registered yet. Please provider sender ID and register with GCM and then with  Notification Hubs."); 
		} 
		
		function updateLog(status) {
		  currentStatus = document.getElementById("console").innerHTML;
		  if (currentStatus != "") {
		    currentStatus = currentStatus + "\n\n";
		  }
		
		  document.getElementById("console").innerHTML = currentStatus  + status;
		}
		
		function registerWithGCM() {
		  var senderId = document.getElementById("senderId").value.trim();
		  chrome.gcm.register([senderId], registerCallback);
		
		  // Prevent register button from being clicked again before the registration finishes
		  document.getElementById("registerWithGCM").disabled = true;
		}
		
		function registerCallback(regId) {
		  registrationId = regId;
		  document.getElementById("registerWithGCM").disabled = false;
		
		  if (chrome.runtime.lastError) {
		    // When the registration fails, handle the error and retry the
		    // registration later.
		    updateLog("Registration failed: " + chrome.runtime.lastError.message);
		    return;
		  }
		
		  updateLog("Registration with GCM succeeded.");
		  document.getElementById("registerWithNH").disabled = false;
		
		  // Mark that the first-time registration is done.
		  chrome.storage.local.set({registered: true});
		}
		
		function registerWithNH() {
		  hubName = document.getElementById("hubName").value.trim();
		  connectionString = document.getElementById("connectionString").value.trim();
		  splitConnectionString();
		  generateSaSToken();
		  sendNHRegistrationRequest();
		}
		
		// From http://msdn.microsoft.com/library/dn495627.aspx 
		function splitConnectionString()
		{
		  var parts = connectionString.split(';');
		  if (parts.length != 3)
		  throw "Error parsing connection string";
		
		  parts.forEach(function(part) {
		    if (part.indexOf('Endpoint') == 0) {
		    endpoint = 'https' + part.substring(11);
		    } else if (part.indexOf('SharedAccessKeyName') == 0) {
		    sasKeyName = part.substring(20);
		    } else if (part.indexOf('SharedAccessKey') == 0) {
		    sasKeyValue = part.substring(16);
		    }
		  });
		
		  originalUri = endpoint + hubName;
		}
		
		function generateSaSToken()
		{
		  targetUri = encodeURIComponent(originalUri.toLowerCase()).toLowerCase();
		  var expiresInMins = 10; // 10 minute expiration
		
		  // Set expiration in seconds
		  var expireOnDate = new Date();
		  expireOnDate.setMinutes(expireOnDate.getMinutes() + expiresInMins);
		  var expires = Date.UTC(expireOnDate.getUTCFullYear(), expireOnDate
		    .getUTCMonth(), expireOnDate.getUTCDate(), expireOnDate
		    .getUTCHours(), expireOnDate.getUTCMinutes(), expireOnDate
		    .getUTCSeconds()) / 1000;
		  var tosign = targetUri + '\n' + expires;
		
		  // using CryptoJS
		  var signature = CryptoJS.HmacSHA256(tosign, sasKeyValue);
		  var base64signature = signature.toString(CryptoJS.enc.Base64);
		  var base64UriEncoded = encodeURIComponent(base64signature);
		
		  // construct authorization string
		  sasToken = "SharedAccessSignature sr=" + targetUri + "&sig="
		                  + base64UriEncoded + "&se=" + expires + "&skn=" + sasKeyName;
		}
		
		function sendNHRegistrationRequest()
		{
		  var registrationPayload = 
		  "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
		  "<entry xmlns=\"http://www.w3.org/2005/Atom\">" + 
		      "<content type=\"application/xml\">" + 
		          "<GcmRegistrationDescription xmlns:i=\"http://www.w3.org/2001/XMLSchema-instance\" xmlns=\"http://schemas.microsoft.com/netservices/2010/10/servicebus/connect\">" +
		              "<GcmRegistrationId>{GCMRegistrationId}</GcmRegistrationId>" +
		          "</GcmRegistrationDescription>" +
		      "</content>" +
		  "</entry>";
		
		  // Update the payload with the registration id obtained earlier
		  registrationPayload = registrationPayload.replace("{GCMRegistrationId}", registrationId);
		
		  var url = originalUri + "/registrations/?api-version=2014-09";
		  var client = new XMLHttpRequest();
		
		  client.onload = function () {
		    if (client.readyState == 4) {
		      if (client.status == 200) {
		        updateLog("Notification Hub Registration succesful!");
		        updateLog(client.responseText);
		      } else {
		        updateLog("Notification Hub Registration did not succeed!");
		        updateLog("HTTP Status: " + client.status + " : " + client.statusText);
		        updateLog("HTTP Response: " + "\n" + client.responseText);
		      }
		    }
		  };
		
		  client.onerror = function () {
		        updateLog("ERROR - Notification Hub Registration did not succeed!");
		  }
		
		  client.open("POST", url, true);
		  client.setRequestHeader("Content-Type", "application/atom+xml;type=entry;charset=utf-8");
		  client.setRequestHeader("Authorization", sasToken);
		  client.setRequestHeader("x-ms-version", "2014-09");
		
		  try {
		      client.send(registrationPayload);
		  }
		  catch(err) {
		      updateLog(err.message);
		  }
		}

	Le script ci-dessus comporte les points suivants :
	- *window.onload* définit les événements de clic de bouton des deux boutons de l'interface utilisateur : l'un gère l'inscription à GCM et l'autre utilise l'ID d'inscription renvoyé après l'inscription auprès de GCM pour permettre de s'inscrire à Azure Notification Hubs. 
	- La fonction *updateLog* définit une fonction de journalisation simple. 
	- *registerWithGCM* est le premier gestionnaire de clic de bouton effectuant l'appel *chrome.gcm.register* vers GCM afin d'inscrire cette instance de l'application Chrome. 
	- *registerCallback* est la fonction de rappel appelée lorsque l'appel d'inscription GCM ci-dessus est renvoyé. 
	- *registerWithNH* est le deuxième gestionnaire de clic de bouton qui gère l'inscription à Notification Hubs. Il obtient les valeurs *hubName* et *connectionString* définies par l'utilisateur et crée l'appel d'API REST d'inscription à Notification Hubs. 
	- *splitConnectionString* et *generateSaSToken* constituent l'implémentation Javascript de création d'un jeton SaS qui doit être envoyé dans tous les appels d'API REST. Pour plus d'informations, voir http://msdn.microsoft.com/library/dn495627.aspx. 
	- *sendNHRegistrationRequest* est la fonction qui effectue un appel REST HTTP. 
	- *registrationPayload* définit la charge utile xml de l'inscription. Plus d'informations à ce sujet ici : [Création de l'API REST NH d'inscription]. Nous mettons à jour l'ID d'enregistrement avec ce que nous avons reçu de GCM. 
	- *client* est une instance de *XMLHttpRequest* qui nous permet d'effectuer la requête HTTP POST. Notez que nous mettons à jour l'en-tête *Authorization* avec sasToken. La réussite de cet appel enregistre cette instance de l'application Chrome auprès d'Azure Notification Hubs. 
	

8. Vous devez voir l'affichage suivant pour votre dossier à la fin de ceci :
   	![][21]

###Installation et test de votre application Chrome

1. Ouvrez votre navigateur Chrome. Ouvrez les **extensions Chrome** et activez le **mode développeur**. 

   	![][16]

2. Cliquez sur **charger l'extension décompressée** et accédez au dossier où vous avez créé les fichiers. Vous pouvez également utiliser l'**outil de développeur d'applications et d'extensions Chrome** qui est une application Chrome (et que vous devrez installer à partir du Web Store de Chrome) et fournit des fonctionnalités de débogage avancées pour le développement d'applications Chrome. 

   	![][17]

3. Si votre application Chrome est créée sans erreur, elle s'affichera. 

   	![][18]

4. Entrez le **numéro de projet** que vous avez obtenu précédemment de la **console Google Cloud** en tant qu'ID de l'expéditeur et cliquez sur **inscription auprès de GCM**. Vous devriez obtenir un message vous indiquant que *l'inscription auprès de GCM a réussi*.

   	![][19]

5. Entrez votre **nom de concentrateur de notification** et la valeur **DefaultListenSharedAccessSignature** que vous avez obtenus à partir du portail de gestion Azure et cliquez sur **inscription à Azure Notification Hub**. Vous devriez voir apparaître un message indiquant que *l'inscription à Notification Hubs a réussi*, ainsi que les détails de la réponse à l'inscription qui contient l'ID d'inscription à Azure Notification Hubs. 

   	![][20]  

##<a name="send"></a>Envoi d'une notification à votre application Chrome

Dans ce didacticiel, vous envoyez des notifications avec une application de console .NET, même si vous pouvez envoyer des notifications avec Notification Hubs depuis n'importe quel serveur principal à l'aide de l'<a href="http://msdn.microsoft.com/library/windowsazure/dn223264.aspx">interface REST</a>. 

Pour découvrir un exemple de la procédure d'envoi de notifications à partir d'un serveur principal Azure Mobile Services intégré à Notification Hubs, voir **Prise en main des notifications Push dans Mobile Services** ([serveur principal .NET](mobile-services-javascript-backend-android-get-started-push.md) | [serveur principal JavaScript](mobile-services-javascript-backend-android-get-started-push.md)).  
Pour découvrir un exemple de la procédure d'envoi de notifications à l'aide des API REST, voir **Utilisation de Notification Hubs à partir de Java/PHP/Python** ([Java](notification-hubs-java-backend-how-to.md) | [PHP](notification-hubs-php-backend-how-to.md) | [Python](notification-hubs-python-backend-how-to.md)).

1. Dans Visual Studio, dans le menu **Fichier**, sélectionnez **Nouveau** puis **Projet...** puis, sous **Visual C#**, cliquez sur **Windows** et sur **Application console**, puis cliquez sur **OK**.  Un projet d'application console est créé.

2. Dans le menu **Outils**, cliquez sur **Gestionnaire de package de bibliothèques**, puis sur **Console du Gestionnaire de package**. La Console du Gestionnaire de package apparaît.

3. Dans la fenêtre de console, exécutez la commande suivante :

        Install-Package WindowsAzure.ServiceBus
    
   	Cela permet d'ajouter une référence au Kit de développement logiciel (SDK) Azure Service Bus à l'aide du <a href="http://nuget.org/packages/  WindowsAzure.ServiceBus/">package NuGet WindowsAzure.ServiceBus</a>. 

4. Ouvrez le fichier Program.cs et ajoutez l'instruction `using` suivante :

        using Microsoft.ServiceBus.Notifications;

5. Dans la classe **Program**, ajoutez la méthode suivante :

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            String message = "{\"data\":{\"message\":\"Hello Chrome from Azure Notification Hubs\"}}";
            await hub.SendGcmNativeNotificationAsync(message);
        }

   	Prenez soin de remplacer l'espace réservé " hub name " par le nom du concentrateur affiché dans le portail sous l'onglet **Concentrateurs de notification**. Remplacez aussi l'espace réservé de chaîne de connexion par la chaîne de connexion nommée **DefaultFullSharedAccessSignature** que vous avez obtenue dans la section " Configuration de votre concentrateur de notification ". 

	>[AZURE.NOTE] Assurez-vous d'utiliser la chaîne de connexion avec un accès **Total**, et non un accès **Écouter**. La chaîne d'accès Écouter n'est pas autorisée à envoyer des notifications.

5. Puis ajoutez les lignes ci-après à la méthode **Main** :

         SendNotificationAsync();
		 Console.ReadLine();

6. Assurez-vous que votre navigateur Chrome est ouvert. Il n'est pas nécessaire que votre application Chrome soit ouverte pour cela. Vous devriez voir le message de notification suivant sur votre bureau. 

   	![][13]

7. Vous pouvez également voir toutes vos notifications à l'aide de la fenêtre de notifications de Chrome accessible à partir de la barre des tâches (sous Windows) lorsque Chrome est en cours d'exécution. 

   	![][14]

## <a name="next-steps"> </a>Étapes suivantes

Dans cet exemple simple, vous avez envoyé des notifications à votre application Chrome. 
En savoir plus sur les concentrateurs de notification dans la [Vue d'ensemble des concentrateurs de notification].
Afin de cibler des utilisateurs spécifiques, consultez le didacticiel [Notification des utilisateurs via Azure Notification Hubs], et, si vous souhaitez segmenter vos utilisateurs par groupes d'intérêt, vous pouvez lire [Dernières nouvelles via Azure Notification Hubs]. 

<!-- Images. -->
[1]: ./media/notification-hubs-chrome-get-started/GoogleConsoleCreateProject.PNG
[2]: ./media/notification-hubs-chrome-get-started/GoogleProjectNumber.png
[3]: ./media/notification-hubs-chrome-get-started/EnableGCM.png
[4]: ./media/notification-hubs-chrome-get-started/CreateServerKey.png
[5]: ./media/notification-hubs-chrome-get-started/ServerKey.png
[6]: ./media/notification-hubs-chrome-get-started/CreateNH.png
[7]: ./media/notification-hubs-chrome-get-started/NHNamespace.png
[8]: ./media/notification-hubs-chrome-get-started/NamespaceConfigure.png
[9]: ./media/notification-hubs-chrome-get-started/NHConfigure.png
[10]: ./media/notification-hubs-chrome-get-started/NHConfigureGCM.png
[11]: ./media/notification-hubs-chrome-get-started/NHDashboard.png
[12]: ./media/notification-hubs-chrome-get-started/NHConnString.png
[13]: ./media/notification-hubs-chrome-get-started/ChromeNotification.png
[14]: ./media/notification-hubs-chrome-get-started/ChromeNotificationWindow.png
[15]: ./media/notification-hubs-chrome-get-started/ChromeApp.png
[16]: ./media/notification-hubs-chrome-get-started/ChromeExtensions.png
[17]: ./media/notification-hubs-chrome-get-started/ChromeLoadExtension.png
[18]: ./media/notification-hubs-chrome-get-started/ChromeAppLoaded.png
[19]: ./media/notification-hubs-chrome-get-started/ChromeAppGCM.png
[20]: ./media/notification-hubs-chrome-get-started/ChromeAppNH.png
[21]: ./media/notification-hubs-chrome-get-started/FinalFolderView.png

<!-- URLs. -->
[Exemple de concentrateur de notification d'application Chrome]: http://google.com
[Console Cloud Google]: http://cloud.google.com/console
[Portail de gestion Azure]: https://manage.windowsazure.com/
[Vue d'ensemble des concentrateurs de notification]: http://msdn.microsoft.com/library/jj927170.aspx
[Vue d'ensemble des applications Chrome]: https://developer.chrome.com/apps/about_apps
[Exemple d'application Chrome GCM]: https://github.com/GoogleChrome/chrome-app-samples/tree/master/samples/gcm-notifications
[Applications Web installables]: https://developers.google.com/chrome/apps/docs/
[Applications Chrome sur mobile]: https://developer.chrome.com/apps/chrome_apps_on_mobile
[Création de l'API REST NH d'inscription]: http://msdn.microsoft.com/library/azure/dn223265.aspx
[Bibliothèque crypto-js]: http://code.google.com/p/crypto-js/
[GCM avec les applications Chrome]: https://developer.chrome.com/apps/cloudMessaging
[Google Cloud Messaging pour Chrome]: https://developer.chrome.com/apps/cloudMessagingV1
[Notification des utilisateurs via Azure Notification Hubs]: notification-hubs-aspnet-backend-windows-dotnet-notify-users.md
[Dernières nouvelles via Azure Notification Hubs]: notification-hubs-windows-store-dotnet-send-breaking-news.md

<!--HONumber=49-->