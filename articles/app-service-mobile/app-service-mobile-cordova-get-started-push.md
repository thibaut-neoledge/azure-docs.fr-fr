<properties
	pageTitle="Ajouter des notifications Push à l’application Apache Cordova à l’aide d’Azure Mobile Apps | Azure App Service"
	description="Découvrez comment utiliser Azure Mobile Apps pour envoyer des notifications Push à votre application Apache Cordova."
	services="app-service\mobile"
	documentationCenter="javascript"
	manager="ggailey777"
	editor=""
	authors="adrianhall"/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-html"
	ms.devlang="javascript"
	ms.topic="article"
	ms.date="08/11/2016"
	ms.author="glenga"/>

# Ajout de notifications Push à votre application Apache Cordova.

[AZURE.INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## Vue d'ensemble

Dans ce didacticiel, vous ajoutez des notifications Push au projet [Démarrage rapide Apache Cordova] afin qu’une notification Push soit envoyée chaque fois qu’un enregistrement est inséré. Ce didacticiel est basé sur le didacticiel [Démarrage rapide Apache Cordova], que vous devez effectuer en premier. Si vous disposez d’un serveur principal ASP.NET et n’utilisez pas le projet de serveur du démarrage rapide téléchargé, vous devez ajouter le package d’extension de notification Push à votre projet. Pour plus d'informations sur les packages d'extension de serveur, consultez [Fonctionnement avec le Kit de développement logiciel (SDK) du serveur principal .NET pour Azure Mobile Apps].

##<a name="prerequisites"></a>Configuration requise

Ce didacticiel est dédié aux applications Apache Cordova développées dans Visual Studio 2015 et exécutées sur l’Émulateur Google Android, un appareil Android, Windows ou iOS.

Pour réaliser ce didacticiel, vous avez besoin des éléments suivants :

* Un PC avec [Visual Studio Community 2015] ou version ultérieure.
* [Visual Studio Tools pour Apache Cordova].
* Un [compte Azure actif](https://azure.microsoft.com/pricing/free-trial/).
* Un projet [Démarrage rapide Apache Cordova]. Vous pouvez suivre d’autres didacticiels (comme l’[authentification]) au préalable, mais ce n’est pas obligatoire.
* (Android) Un [compte Google] avec une adresse électronique vérifiée et un appareil Android.
* (iOS) Un abonnement au programme pour développeurs Apple et un appareil iOS (le simulateur iOS ne prend pas en charge les notifications Push).
* (Windows) Un compte de développeur Windows Store et un appareil Windows 10.

Bien que les notifications Push soient prises en charge sur les émulateurs Android, nous avons constaté leur instabilité. Nous vous déconseillons de tester les notifications Push sur les émulateurs.

##<a name="create-hub"></a>Créer un hub de notification

[AZURE.INCLUDE [app-service-mobile-create-notification-hub](../../includes/app-service-mobile-create-notification-hub.md)]

[Regarder une vidéo montrant des étapes similaires](https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-3-Create-azure-notification-hub)

##Mettre à jour le projet de serveur pour l'envoi de notifications Push

[AZURE.INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]

##<a name="add-push-to-app"></a>Modifier votre application Cordova pour la réception de notifications Push

Vous devez vous assurer que votre projet d’application Apache Cordova est prêt à gérer les notifications Push. Vous devez installer le plug-in Push Cordova, ainsi que tous les services Push spécifiques à la plateforme.

#### Mise à jour de la version de Cordova dans votre projet.

Nous vous recommandons de mettre à jour le projet client vers Cordova 6.1.1 si votre projet est configuré à l’aide d’une version antérieure. Pour mettre à jour le projet, cliquez avec le bouton droit sur le fichier config.xml pour ouvrir le concepteur de configuration. Sélectionnez l’onglet Plateformes et choisissez 6.1.1 dans la zone de texte **Cordova CLI**.

Cliquez sur **Générer**, puis sur **Générer la solution** pour mettre à jour le projet.

#### Installation du plug-in Push

En mode natif, les applications Apache Cordova ne gèrent pas les fonctionnalités de réseau ou d’appareils. Ces fonctionnalités sont fournies par des plug-ins publiés sur [npm](https://www.npmjs.com/) ou sur GitHub. Le plug-in `phonegap-plugin-push` est utilisé pour gérer les notifications Push du réseau.

Vous pouvez installer le plug-in de notification Push de l’une des façons suivantes :

**À partir de l'invite de commandes :**

Exécutez la commande suivante :

    cordova plugin add phonegap-plugin-push

**À partir de Visual Studio :**

1.  Dans l’Explorateur de solutions, ouvrez le fichier `config.xml` et cliquez sur **Plug-ins** > **Personnalisé**, sélectionnez **Git** comme source d’installation, puis entrez `https://github.com/phonegap/phonegap-plugin-push` comme source.

	![](./media/app-service-mobile-cordova-get-started-push/add-push-plugin.png)

2.  Cliquez sur la flèche en regard de la source d’installation.

3. Si vous avez déjà un ID de projet numérique pour le projet de Console de développement Google, ajoutez-le dans **SENDER\_ID**. Sinon, entrez une valeur d’espace réservé, comme 777777. Si vous ciblez Android, vous pourrez modifier cette valeur dans le fichier config.xml ultérieurement.

4. Cliquez sur **Add**.

Le plug-in de notification Push est maintenant installé.

####Installation du plug-in Appareil

Suivez la même procédure que pour l’installation du plug-in Push, mais pour le plug-in Appareil que vous trouverez dans la liste principale des plug-ins (cliquez sur **Plug-ins** > **Principal** pour le trouver). Vous avez besoin de ce plug-in pour obtenir le nom de la plateforme (`device.platform`).

#### Inscription de votre appareil pour les notifications Push au démarrage

Initialement, nous inclurons un code minimal pour Android. Nous apporterons quelques petites modifications ultérieurement pour l’exécution sur iOS ou Windows 10.

1. Ajoutez un appel à **registerForPushNotifications** durant le rappel du processus de connexion, ou en bas de la méthode **onDeviceReady** :

		// Login to the service.
		client.login('google')
		    .then(function () {
		        // Create a table reference
		        todoItemTable = client.getTable('todoitem');

		        // Refresh the todoItems
		        refreshDisplay();

		        // Wire up the UI Event Handler for the Add Item
		        $('#add-item').submit(addItemHandler);
		        $('#refresh').on('click', refreshDisplay);

				    // Added to register for push notifications.
		        registerForPushNotifications();

		    }, handleError);

	Cet exemple illustre l’appel à **registerForPushNotifications** une fois l’authentification réussie, ce qui est recommandé lors de l’utilisation conjointe des notifications Push et de l’authentification dans votre application.

2. Ajoutez la nouvelle méthode **registerForPushNotifications** comme suit :

		// Register for Push Notifications. Requires that phonegap-plugin-push be installed.
		var pushRegistration = null;
		function registerForPushNotifications() {
		  pushRegistration = PushNotification.init({
		      android: { senderID: 'Your_Project_ID' },
		      ios: { alert: 'true', badge: 'true', sound: 'true' },
		      wns: {}
		  });

		// Handle the registration event.
		pushRegistration.on('registration', function (data) {
		  // Get the native platform of the device.
		  var platform = device.platform;
		  // Get the handle returned during registration.
		  var handle = data.registrationId;
		  // Set the device-specific message template.
		  if (platform == 'android' || platform == 'Android') {
		      // Register for GCM notifications.
		      client.push.register('gcm', handle, {
		          mytemplate: { body: { data: { message: "{$(messageParam)}" } } }
		      });
		  } else if (device.platform === 'iOS') {
		      // Register for notifications.            
		      client.push.register('apns', handle, {
		          mytemplate: { body: { aps: { alert: "{$(messageParam)}" } } }
		      });
		  } else if (device.platform === 'windows') {
		      // Register for WNS notifications.
		      client.push.register('wns', handle, {
		          myTemplate: {
		              body: '<toast><visual><binding template="ToastText01"><text id="1">$(messageParam)</text></binding></visual></toast>',
		              headers: { 'X-WNS-Type': 'wns/toast' } }
		      });
		  }
		});

		pushRegistration.on('notification', function (data, d2) {
		  alert('Push Received: ' + data.message);
		});

		pushRegistration.on('error', handleError);
		}

3. (Android) Dans le code ci-dessus, remplacez `Your_Project_ID` par l’ID de projet numérique associé à votre application dans la [Console de développement Google].

## (Facultatif) Configurer et exécuter l’application sur Android

Terminez cette section pour activer les notifications Push pour Android.

####<a name="enable-gcm"></a>Activation de Google Cloud Messaging

Dans la mesure où nous ciblons la plateforme Google Android en premier lieu, vous devez activer Google Cloud Messaging. De la même manière, si vous cibliez les appareils Microsoft Windows, vous activeriez la prise en charge WNS.

[AZURE.INCLUDE [mobile-services-enable-google-cloud-messaging](../../includes/mobile-services-enable-google-cloud-messaging.md)]

[Regarder une vidéo montrant des étapes similaires](https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-4-Set-up-gcm-for-push)

####<a name="configure-backend"></a>Configurer le serveur principal d’application mobile pour l’envoi de demandes de notifications Push à l’aide de GCM

[AZURE.INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push.md)]

####Configuration de votre application Cordova pour Android

Dans votre application Cordova, ouvrez le fichier config.xml et remplacez `Your_Project_ID` par l’ID de projet numérique associé à votre application dans la [Console de développement Google].

		<plugin name="phonegap-plugin-push" version="1.7.1" src="https://github.com/phonegap/phonegap-plugin-push.git">
			<variable name="SENDER_ID" value="Your_Project_ID" />
		</plugin>

Ouvrez le fichier index.js et mettez à jour le code pour utiliser votre ID de projet numérique.

		pushRegistration = PushNotification.init({
			android: { senderID: 'Your_Project_ID' },
			ios: { alert: 'true', badge: 'true', sound: 'true' },
			wns: {}
		});

####<a name="configure-device"></a>Configuration de votre appareil Android pour le débogage USB

Pour pouvoir déployer votre application sur votre appareil Android, vous devez activer le débogage USB. Sur votre téléphone Android, procédez comme suit :

1. Accédez à **Paramètres** > **À propos du téléphone**, puis cliquez sur **Numéro de build** jusqu’à ce que le mode développeur soit activé (environ 7 fois).

2. De retour dans **Paramètres** > **Options développeurs**, activez **Débogage USB**, puis connectez votre téléphone Android sur votre PC de développement à l’aide d’un câble USB.

Nous avons testé cette procédure à l’aide d’un appareil Google Nexus 5 X exécutant Android 6.0 (Marshmallow). Toutefois, les techniques sont communes à l’ensemble des versions Android modernes.

#### Installation des services Google Play

Le plug-in Push sollicite les services Google Play pour les notifications Push.

1.  Dans **Visual Studio**, cliquez sur **Outils** > **Android** > **Gestionnaire du Kit de développement logiciel (SDK) Android**, développez le dossier **Extras**, puis cochez la case correspondante pour vous assurer que chacun des Kits de développement logiciel suivants est installé.
    * Référentiel de prise en charge Android 20 ou version ultérieure
    * Services Google Play 27 ou version ultérieure
    * Référentiel Google 22 ou version ultérieure

2.  Cliquez sur **Packages d’installation** et attendez que l’installation se termine.

Les bibliothèques actuellement requises figurent dans la [documentation d’installation phonegap-plugin-push].

#### Test des notifications Push dans l’application sur Android

Vous pouvez désormais tester les notifications Push en exécutant l’application et en insérant des éléments dans la table TodoItem. Vous pouvez faire cela à partir du même appareil ou à partir d’un deuxième appareil, à condition d’utiliser le même serveur principal. Testez votre application Cordova sur la plateforme Android de l’une des manières suivantes :

- **Sur un appareil physique :** connectez votre appareil Android à votre ordinateur de développement avec un câble USB. Au lieu de **l’Émulateur Google Android**, sélectionnez **Appareil**. Visual Studio déploie l’application sur l’appareil et l’exécute. Vous pouvez alors interagir avec l’application sur l’appareil. Améliorez votre expérience du développement. Les applications de partage d’écran comme [Mobizen] peuvent vous aider à développer une application Android, en projetant votre écran Android sur un navigateur web, sur votre PC.

- **Sur un émulateur Android :** des étapes de configuration supplémentaires sont requises lors de l’exécution sur un émulateur.

	Assurez-vous de procéder au déploiement ou au débogage sur un périphérique virtuel sur lequel les API Google sont définis comme cible, comme indiqué dans le Gestionnaire d’appareil virtuel Android (AVD).

	![](./media/app-service-mobile-cordova-get-started-push/google-apis-avd-settings.png)

	Si vous souhaitez utiliser un émulateur x86 plus rapide, [installez le pilote HAXM](https://taco.visualstudio.com/fr-FR/docs/run-app-apache/#HAXM) et configurez l’émulateur pour l’utiliser.

	Ajoutez un compte Google à l’appareil Android en cliquant sur **Applications** > **Paramètres** > **Ajouter un compte**, puis suivez les invites pour ajouter un compte Google existant à l’appareil (nous vous recommandons d’utiliser un compte existant plutôt que d’en créer un nouveau).

	![](./media/app-service-mobile-cordova-get-started-push/add-google-account.png)

	Exécutez normalement l’application todolist et insérez un nouvel élément todo. Cette fois, une icône de notification s’affiche dans la zone de notification. Vous pouvez ouvrir le tiroir de notification pour afficher le texte intégral de la notification.

	![](./media/app-service-mobile-cordova-get-started-push/android-notifications.png)

##(Facultatif) Configurer et exécuter sur iOS

Cette section est dédiée à l’exécution du projet Cordova sur les appareils iOS. Vous pouvez ignorer cette section si vous n’utilisez pas d’appareils iOS.

####Installer et exécuter l’agent remotebuild iOS sur un Mac ou un service cloud

Avant de pouvoir exécuter une application Cordova sur iOS à l’aide de Visual Studio, suivez les étapes décrites dans le [Guide d’installation iOS](http://taco.visualstudio.com/fr-FR/docs/ios-guide/) pour installer et exécuter l’agent remotebuild.

Assurez-vous que vous pouvez générer l’application pour iOS. Les étapes décrites dans le guide d’installation sont obligatoires pour générer des applications pour iOS à partir de Visual Studio. Si vous n’avez pas de Mac, vous pouvez générer des applications pour iOS à l’aide de l’agent remotebuild sur un service comme MacInCloud. Pour plus d’informations, reportez-vous à la page relative à [l’exécution de votre application iOS dans le cloud](http://taco.visualstudio.com/fr-FR/docs/build_ios_cloud/).

####Rechercher l’ID à utiliser en tant qu’ID de l’application

Avant d’inscrire votre application pour les notifications Push, ouvrez le fichier config.xml dans votre application Cordova, recherchez la valeur d’attribut `id` dans l’élément de widget et copiez-la pour une utilisation ultérieure. Dans le code XML suivant, l’ID est `io.cordova.myapp7777777`.

		<widget defaultlocale="fr-FR" id="io.cordova.myapp7777777"
  		version="1.0.0" windows-packageVersion="1.1.0.0" xmlns="http://www.w3.org/ns/widgets"
			xmlns:cdv="http://cordova.apache.org/ns/1.0" xmlns:vs="http://schemas.microsoft.com/appx/2014/htmlapps">

Vous utiliserez cet identifiant ultérieurement lors de la création d’un ID d’application sur le portail des développeurs d’Apple. (Si vous créez un autre ID d’application sur le portail des développeurs et que vous souhaitez l’utiliser, vous devrez effectuer quelques étapes supplémentaires ultérieurement dans ce didacticiel pour modifier cet ID dans le fichier config.xml. L’ID de l’élément de widget doit correspondre à l’ID d’application sur le portail des développeurs.)

####Inscrire l'application pour les notifications push dans le portail de développeurs d'Apple

[AZURE.INCLUDE [Concentrateur de notification de base Xamarin - Activation des notifications Push Apple](../../includes/notification-hubs-xamarin-enable-apple-push-notifications.md)]

[Regarder une vidéo montrant des étapes similaires](https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-5-Set-up-apns-for-push)

####Configurer Azure pour l’envoi de notifications Push

1. Connectez-vous au [portail Azure](https://portal.azure.com/). Cliquez sur **Parcourir** > **Mobile Apps** > votre application mobile > **Paramètres** > **Notifications Push** > **Apple (APNS)** > **Télécharger un certificat**. Chargez le fichier de certificat Push p12 exporté plus tôt. Veillez à sélectionner **Bac à sable (sandbox)** si vous avez créé un certificat Push de développement pour le développement et le test. Sinon, sélectionnez **Production**. Votre service est désormais configuré et prêt à fonctionner avec les notifications Push sur iOS.

	![](./media/app-service-mobile-cordova-get-started-push/mobile-app-upload-apns-cert.png)

####Vérifier que votre ID d’application correspond à votre application Cordova

Si l’ID d’application que vous avez créé dans votre compte de développeur Apple correspond déjà à l’ID de l’élément de widget dans le fichier config.xml, vous pouvez ignorer cette étape. Toutefois, si les ID ne correspondent pas, procédez comme suit :

1. Supprimez le dossier platforms de votre projet.

2. Supprimez le dossier plugins de votre projet.

3. Supprimez le dossier node\_modules de votre projet.

4. Mettez à jour l’attribut id de l’élément de widget dans le fichier config.xml pour utiliser l’ID d’application que vous avez créé dans votre compte de développeur Apple.

5. Régénérez votre projet.

#####Tester les notifications Push dans votre application iOS

1. Dans Visual Studio, assurez-vous que **iOS** est sélectionné comme cible de déploiement, puis choisissez **Appareil** pour exécuter votre appareil iOS connecté.

	Vous pouvez exécuter un appareil iOS connecté à votre PC à l’aide d’iTunes. Les notifications Push ne sont pas prises en charge par le simulateur iOS.

2. Cliquez sur le bouton **Exécuter** ou sur **F5** dans Visual Studio afin de développer le projet et de démarrer l’application dans un appareil iOS, puis cliquez sur **OK** afin d’accepter les notifications Push.

	>[AZURE.NOTE] Vous devez accepter explicitement les notifications Push de votre application. Cette demande s’effectue uniquement lors du premier démarrage de l’application.

3. Dans l'application, tapez une tâche, puis cliquez sur l'icône plus (+).

4. Vérifiez que vous avez reçu une notification, puis cliquez sur OK pour la fermer.

##(Facultatif) Configurer et exécuter sur Windows

Cette section est dédiée à l’exécution du projet d’application Apache Cordova sur les appareils Windows 10 (le plug-in push PhoneGap est pris en charge sur Windows 10). Vous pouvez ignorer cette section si vous n’utilisez pas d’appareils Windows.

####Inscrire votre application Windows pour les notifications Push avec WNS

Pour utiliser les options de stockage dans Visual Studio, sélectionnez une cible de Windows à partir de la liste des plateformes de solution, par exemple **Windows-x64** ou **Windows-x86** (évitez **Windows-AnyCPU** pour les notifications Push).

[AZURE.INCLUDE [app-service-mobile-register-wns](../../includes/app-service-mobile-register-wns.md)]

[Regarder une vidéo montrant des étapes similaires](https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-6-Set-up-wns-for-push)

####Configurer le Notification Hub pour WNS

[AZURE.INCLUDE [app-service-mobile-configure-wns](../../includes/app-service-mobile-configure-wns.md)]

####Configurer votre application Cordova pour prendre en charge les notifications Push Windows

Ouvrez le concepteur de configuration (cliquez avec le bouton droit sur le fichier config.xml, puis sélectionnez **Concepteur de vue**), sélectionnez l’onglet **Windows**, puis cliquez sur **Windows 10** sous **Version cible de Windows**.

	>[AZURE.NOTE] If you are using a Cordova version prior to Cordova 5.1.1 (6.1.1 recommended), you must also set the Toast Capable flag to true in config.xml.

Pour prendre en charge les notifications Push dans vos versions (de débogage) par défaut, ouvrez le fichier build.json. Copiez la configuration de « lancement » dans votre configuration de débogage.

		"windows": {
			"release": {
				"packageCertificateKeyFile": "res\\native\\windows\\CordovaApp.pfx",
				"publisherId": "CN=yourpublisherID"
			}
		}

Après la mise à jour, le code précédent doit ressembler à ceci.

	"windows": {
		"release": {
			"packageCertificateKeyFile": "res\\native\\windows\\CordovaApp.pfx",
			"publisherId": "CN=yourpublisherID"
			},
		"debug": {
			"packageCertificateKeyFile": "res\\native\\windows\\CordovaApp.pfx",
			"publisherId": "CN=yourpublisherID"
			}
		}

Générez l’application et vérifiez l’absence d’erreurs. Votre application cliente doit désormais s’inscrire pour les notifications du serveur principal d’application mobile. Répétez cette section pour chaque projet Windows dans votre solution.

####Tester les notifications Push dans votre application Windows

Dans Visual Studio, assurez-vous qu’une plateforme Windows est sélectionnée comme cible de déploiement, telle que **Windows-x64** ou **Windows-x86**. Pour exécuter l’application sur un PC Windows 10 hébergeant Visual Studio, choisissez **Ordinateur local**.

Cliquez sur le bouton Exécuter pour générer le projet et démarrer l’application.

Dans l’application, tapez un nom pour un nouvel élément todoitem, puis cliquez sur l’icône de signe plus (+) pour l’ajouter.

Vérifiez qu’une notification est reçue lorsque l’élément est ajouté.

##<a name="next-steps"></a>Étapes suivantes

* Consultez la documentation relative à [Notification Hubs] afin d’en découvrir davantage sur les notifications Push.
* Si vous ne l’avez pas encore fait, continuez le didacticiel en [ajoutant l’authentification] à votre application Apache Cordova.

Découvrez comment utiliser les Kits de développement logiciel.

* [Kit de développement logiciel (SDK) Apache Cordova]
* [Kit de développement logiciel du serveur ASP.NET]
* [Kit de développement logiciel du serveur Node.js]

<!-- URLs -->
[ajoutant l’authentification]: app-service-mobile-cordova-get-started-users.md
[Démarrage rapide Apache Cordova]: app-service-mobile-cordova-get-started.md
[authentification]: app-service-mobile-cordova-get-started-users.md
[Fonctionnement avec le Kit de développement logiciel (SDK) du serveur principal .NET pour Azure Mobile Apps]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[compte Google]: http://go.microsoft.com/fwlink/p/?LinkId=268302
[Console de développement Google]: https://console.developers.google.com/home/dashboard
[documentation d’installation phonegap-plugin-push]: https://github.com/phonegap/phonegap-plugin-push/blob/master/docs/INSTALLATION.md
[Mobizen]: https://www.mobizen.com/
[Visual Studio Community 2015]: http://www.visualstudio.com/
[Visual Studio Tools pour Apache Cordova]: https://www.visualstudio.com/fr-FR/features/cordova-vs.aspx
[Notification Hubs]: ../notification-hubs/notification-hubs-overview.md
[Kit de développement logiciel (SDK) Apache Cordova]: app-service-mobile-cordova-how-to-use-client-library.md
[Kit de développement logiciel du serveur ASP.NET]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Kit de développement logiciel du serveur Node.js]: app-service-mobile-node-backend-how-to-use-server-sdk.md

<!---HONumber=AcomDC_0817_2016-->