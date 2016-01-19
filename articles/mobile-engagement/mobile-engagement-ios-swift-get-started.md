<properties
	pageTitle="Prise en main d’Azure Mobile Engagement pour iOS dans Swift"
	description="Découvrez comment utiliser Azure Mobile Engagement avec les analyses et les notifications Push pour les applications iOS"
	services="mobile-engagement"
	documentationCenter="ios"
	authors="piyushjo"
	manager="dwrede"
	editor="" />

<tags
	ms.service="mobile-engagement"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="swift"
	ms.topic="get-started-article"
	ms.date="09/22/2015"
	ms.author="piyushjo" />

# Prise en main d’Azure Mobile Engagement pour les applications iOS dans Swift

> [AZURE.SELECTOR]
- [Universal Windows](mobile-engagement-windows-store-dotnet-get-started.md)
- [Windows Phone Silverlight](mobile-engagement-windows-phone-get-started.md)
- [iOS | Obj C](mobile-engagement-ios-get-started.md)
- [iOS | Swift](mobile-engagement-ios-swift-get-started.md)
- [Android](mobile-engagement-android-get-started.md)
- [Cordova](mobile-engagement-cordova-get-started.md)

Cette rubrique indique comment utiliser Azure Mobile Engagement pour comprendre l'utilisation de votre application et envoyer des notifications push aux utilisateurs segmentés d'une application iOS. Dans ce didacticiel, vous créez une application iOS vide qui collecte des données de base et reçoit des notifications push à l'aide du service APN (Apple Push Notification Service).

Ce didacticiel requiert les éléments suivants :

+ XCode 6 ou XCode 7 que vous pouvez installer depuis votre MAC App Store
+ Le [kit de développement logiciel (SDK) iOS Mobile Engagement]
+ Le certificat de notification push (.p12) que vous pouvez obtenir dans votre Centre de développement Apple

> [AZURE.NOTE]Ce didacticiel utilise Swift version 2.0.

Vous devez suivre ce didacticiel pour avoir accès à tous les autres didacticiels Mobile Engagement pour applications iOS.

> [AZURE.IMPORTANT]L’exécution de ce didacticiel est obligatoire si vous souhaitez effectuer tous les autres didacticiels Mobile Engagement pour les applications iOS. Pour cela, vous devez avoir un compte Azure actif. Si vous ne possédez pas de compte, vous pouvez créer un compte d'évaluation gratuit en quelques minutes. Pour plus d'informations, consultez la page <a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Ffr-FR%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Version d'évaluation gratuite d'Azure</a>.

##<a id="setup-azme"></a>Configuration de Mobile Engagement pour votre application iOS

[AZURE.INCLUDE [Création d’application Mobile Engagement dans le portail](../../includes/mobile-engagement-create-app-in-portal.md)]

##<a id="connecting-app"></a>Connexion de votre application au serveur principal Mobile Engagement

Ce didacticiel aborde l'intégration de base qui correspond aux éléments nécessaires à la collection de données et à l'envoi de notifications push. Vous trouverez la documentation complète d’intégration dans [Intégration du Kit de développement logiciel (SDK) iOS Mobile Engagement](../mobile-engagement-ios-sdk-overview/).

Nous allons créer une application de base à l’aide de XCode afin d’illustrer l’intégration :

###Créer un projet iOS

[AZURE.INCLUDE [Créer un projet iOS](../../includes/mobile-engagement-create-new-ios-app.md)]

###Connectez votre application au serveur principal Mobile Engagement

1. Téléchargez le [kit de développement logiciel (SDK) iOS Mobile Engagement]
2. Extrayez le fichier .tar.gz dans un dossier de votre ordinateur
3. Cliquez avec le bouton droit sur le projet et sélectionnez « Add files to ... »

	![][1]

4. Accédez au dossier dans lequel vous avez extrait le kit de développement logiciel (SDK) et sélectionnez le dossier `EngagementSDK`, puis appuyez sur OK.

	![][2]

5. Ouvrez l’onglet `Build Phases` et dans le menu `Link Binary With Libraries`, ajoutez les infrastructures, comme indiqué ci-dessous :

	![][3]

6. Pour **XCode 7**, ajoutez `libxml2.tbd` au lieu de `libxml2.dylib`.

7. Créez un en-tête de pontage pour pouvoir utiliser les API Objective C du Kit de développement logiciel (SDK) en choisissant Fichier > Nouveau > Fichier > iOS > Source > Fichier d’en-tête.

	![][4]

8. Modifiez le fichier d’en-tête de pontage pour exposer le code Mobile Engagement Objective-C sur votre code Swift, puis ajoutez les importations ci-après :

		/* Mobile Engagement Agent */
		#import "AEModule.h"
		#import "AEPushMessage.h"
		#import "AEStorage.h"
		#import "EngagementAgent.h"
		#import "EngagementTableViewController.h"
		#import "EngagementViewController.h"
		#import "AEIdfaProvider.h"

9. Sous Paramètres de génération, assurez-vous que le paramètre de génération d’en-tête de pontage Objective-C sous Compilateur Swift- Génération de code a un chemin d’accès à cet en-tête. Voici un exemple de chemin d’accès : **$(SRCROOT)/MySuperApp/MySuperApp-Bridging-Header.h (selon le chemin d’accès)**

	![][6]

10. De retour sur le portail Azure, dans la page *Informations de connexion* de votre application, copiez la chaîne de connexion.

	![][5]

11. Collez la chaîne de connexion dans le délégué `didFinishLaunchingWithOptions`

		func application(application: UIApplication, didFinishLaunchingWithOptions launchOptions: [NSObject: AnyObject]?) -> Bool
		{
  			[...]
				EngagementAgent.init("Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}")
  			[...]
		}

##<a id="monitor"></a>Activation de la surveillance en temps réel

Pour commencer à envoyer des données et vous assurer que les utilisateurs sont actifs, vous devez envoyer au moins un écran (activité) au serveur principal Mobile Engagement.

1. Ouvrez le fichier **ViewController.swift** et remplacez la classe de base **ViewController** pare **EngagementViewController** :

	`class ViewController : EngagementViewController {`

##<a id="monitor"></a>Connexion d’application avec l’analyse en temps réel

[AZURE.INCLUDE [Connexion d’application avec l’analyse en temps réel](../../includes/mobile-engagement-connect-app-with-monitor.md)]

##<a id="integrate-push"></a>Activation des notifications Push et de la messagerie intégrée à l’application

Mobile Engagement vous permet d'interagir et d'atteindre vos utilisateurs à l'aide de notifications Push et de la messagerie dans l'application, dans le cadre d'une campagne. Ce module s'appelle Couverture dans le portail Mobile Engagement. Les sections suivantes vous permettront de configurer votre application pour la réception des notifications.

### Activer votre application pour recevoir des notifications Push Silent

[AZURE.INCLUDE [mobile-engagement-ios-silent-push](../../includes/mobile-engagement-ios-silent-push.md)]

### Ajoutez la bibliothèque du module Couverture à votre projet

1. Cliquez avec le bouton droit sur votre projet
2. Sélectionnez `Add file to ...`
3. Accédez au dossier où vous avez extrait le SDK
4. Sélectionnez le dossier `EngagementReach`
5. Cliquez sur Ajouter.
6. Modifiez le fichier d’en-tête de pontage pour exposer les en-têtes Mobile Engagement Objective-C Reach, puis ajoutez les importations ci-après :

		/* Mobile Engagement Reach */
		#import "AEAnnouncementViewController.h"
		#import "AEAutorotateView.h"
		#import "AEContentViewController.h"
		#import "AEDefaultAnnouncementViewController.h"
		#import "AEDefaultNotifier.h"
		#import "AEDefaultPollViewController.h"
		#import "AEInteractiveContent.h"
		#import "AENotificationView.h"
		#import "AENotifier.h"
		#import "AEPollViewController.h"
		#import "AEReachAbstractAnnouncement.h"
		#import "AEReachAnnouncement.h"
		#import "AEReachContent.h"
		#import "AEReachDataPush.h"
		#import "AEReachDataPushDelegate.h"
		#import "AEReachModule.h"
		#import "AEReachNotifAnnouncement.h"
		#import "AEReachPoll.h"
		#import "AEReachPollQuestion.h"
		#import "AEViewControllerUtil.h"
		#import "AEWebAnnouncementJsBridge.h"

### Modifier votre délégué d'Application

1. Dans `didFinishLaunchingWithOptions`, créez un module Couverture et passez-le à la ligne existante d’initialisation d’Engagement :

		func application(application: UIApplication, didFinishLaunchingWithOptions launchOptions: [NSObject: AnyObject]?) -> Bool {
			let reach = AEReachModule.moduleWithNotificationIcon(UIImage(named:"icon.png")) as! AEReachModule
			EngagementAgent.init("Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}", modulesArray:[reach])
			[...]
			return true
		}

###Activez votre application pour recevoir des notifications push du service APN
1. Ajoutez la ligne suivante à la méthode `didFinishLaunchingWithOptions` :

		/* Ask user to receive push notifications */
		if #available(iOS 8.0, *)
		{
		   let settings = UIUserNotificationSettings(forTypes: [UIUserNotificationType.Alert, UIUserNotificationType.Badge, UIUserNotificationType.Sound], categories: nil)
		   application.registerUserNotificationSettings(settings)
		   application.registerForRemoteNotifications()
		}
		else
		{
		   application.registerForRemoteNotificationTypes([UIRemoteNotificationType.Alert, UIRemoteNotificationType.Badge, UIRemoteNotificationType.Sound])
		}

2. Ajoutez la méthode `didRegisterForRemoteNotificationsWithDeviceToken` comme suit :

		func application(application: UIApplication, didRegisterForRemoteNotificationsWithDeviceToken deviceToken: NSData)
		{
			EngagementAgent.shared().registerDeviceToken(deviceToken)
		}

3. Ajoutez la méthode `didReceiveRemoteNotification:fetchCompletionHandler:` comme suit :

		func application(application: UIApplication, didReceiveRemoteNotification userInfo: [NSObject : AnyObject], fetchCompletionHandler completionHandler: (UIBackgroundFetchResult) -> Void)
		{
			EngagementAgent.shared().applicationDidReceiveRemoteNotification(userInfo, fetchCompletionHandler:completionHandler)
		}

[AZURE.INCLUDE [mobile-engagement-ios-send-push-push](../../includes/mobile-engagement-ios-send-push.md)]

<!-- URLs. -->
[kit de développement logiciel (SDK) iOS Mobile Engagement]: http://aka.ms/qk2rnj

<!-- Images. -->
[1]: ./media/mobile-engagement-ios-get-started/xcode-add-files.png
[2]: ./media/mobile-engagement-ios-get-started/xcode-select-engagement-sdk.png
[3]: ./media/mobile-engagement-ios-get-started/xcode-build-phases.png
[4]: ./media/mobile-engagement-ios-swift-get-started/add-header-file.png
[5]: ./media/mobile-engagement-ios-get-started/app-connection-info-page.png
[6]: ./media/mobile-engagement-ios-swift-get-started/add-bridging-header.png

<!---HONumber=AcomDC_0114_2016-->