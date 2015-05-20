<properties 
	pageTitle="Prise en main d’Azure Mobile Engagement pour iOS dans Swift" 
	description="Découvrez comment utiliser Azure Mobile Engagement avec les analyses et les notifications Push pour les applications iOS"
	services="mobile-engagement" 
	documentationCenter="Mobile" 
	authors="piyushjo" 
	manager="dwrede" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-ios" 
	ms.devlang="swift" 
	ms.topic="article" 
	ms.date="04/30/2015" 
	ms.author="piyushjo" />

# Prise en main d’Azure Mobile Engagement pour les applications iOS dans Swift

> [AZURE.SELECTOR]
- [Windows Universal](mobile-engagement-windows-store-dotnet-get-started.md) 
- [Windows Phone Silverlight](mobile-engagement-windows-phone-get-started.md) 
- [iOS - Obj C](mobile-engagement-ios-get-started.md) 
- [iOS - Swift](mobile-engagement-ios-swift-get-started.md)
- [Android](mobile-engagement-android-get-started.md) 

Cette rubrique indique comment utiliser Azure Mobile Engagement pour comprendre l'utilisation de votre application et envoyer des notifications push aux utilisateurs segmentés d'une application iOS. Dans ce didacticiel, vous créez une application iOS vide qui collecte des données de base et reçoit des notifications push à l'aide du service APN \(Apple Push Notification Service\). Quand vous aurez terminé, vous serez en mesure de diffuser des notifications Push vers tous les appareils, ainsi que de cibler des utilisateurs spécifiques en fonction des propriétés de leurs appareils.

Ce didacticiel montre un scénario de diffusion simple à l'aide de Mobile Engagement. Veillez à bien suivre le didacticiel qui suit pour savoir comment utiliser Mobile Engagement dans le but de cibler des utilisateurs et des groupes d'appareils spécifiques.

Ce didacticiel requiert les éléments suivants :

+ XCode, que vous pouvez installer depuis votre boutique d’applications Mac
+ Le [Kit de développement logiciel \(SDK\) iOS Mobile Engagement]
+ Le certificat de notification push \(.p12\) que vous pouvez obtenir dans votre Centre de développement Apple

Vous devez suivre ce didacticiel pour avoir accès à tous les autres didacticiels Mobile Engagement pour applications iOS.

> [AZURE.IMPORTANT]L’exécution de ce didacticiel est obligatoire si vous souhaitez effectuer tous les autres didacticiels Mobile Engagement pour les applications iOS. Pour ce faire, vous devez avoir un compte Azure actif. Si vous ne possédez pas de compte, vous pouvez créer un compte d'évaluation gratuit en quelques minutes. Pour plus d’informations, consultez la page <a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Ffr-fr%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Version d’évaluation gratuite d’Azure</a>.

<!--
##<a id="register"></a>Enable Apple Push Notification Service

[WACOM.INCLUDE [Enable Apple Push Notifications](../includes/enable-apple-push-notifications.md)]
-->

##<a id="setup-azme"></a>Configuration de Mobile Engagement pour votre application

1. Connectez-vous au portail de gestion Azure et cliquez sur **+NEW** en bas de l’écran.

2. Cliquez sur **App Services**, **Mobile Engagement**, puis **Créer**.

   	![][7]

3. Dans la fenêtre indépendante qui s'affiche, entrez les informations suivantes :
 
   	![][8]

	- **Nom de l’application** : tapez ici le nom de votre application. Tous les caractères sont autorisés.
	- **Plateforme** : sélectionnez la plateforme cible \(\*\*iOS\*\*\) de l’application \(si l’application cible plusieurs plateformes, répétez ce didacticiel pour chacune des plateformes\). 
	- **Nom de la ressource d’application** : nom utilisé dans les API et les URL pour faire référence à l’application. Vous devez utiliser uniquement des caractères d’URL conventionnels. Le nom généré automatiquement vous fournit un point de départ solide. Il est également recommandé d’ajouter le nom de la plateforme pour éviter tout conflit de noms, puisque ce nom doit être unique.
	- **Emplacement** : sélectionnez le centre de données qui hébergera l’application \(et surtout sa collection\).
	- **Collection** : si vous avez déjà créé une application, sélectionnez une collection créée précédemment, sinon sélectionnez Nouvelle collection.
	- **Nom de collection** : correspond à votre groupe d’applications. Cela permet également de garantir que toutes vos applications font partie d’un groupe qui autorise les calculs agrégés de statistiques. Vous devez utiliser ici le nom de votre société ou service le cas échéant.

4. Sélectionnez l’application que vous venez de créer sous l’onglet **Applications**.

5. Ensuite, cliquez sur **Informations de connexion** pour afficher les paramètres de connexion que vous voulez ajouter à l’intégration de votre Kit de développement logiciel \(SDK\) dans votre application mobile.
 
   	![][10]

6. Copiez la **chaîne de connexion** : cela vous sert à identifier cette application dans votre code d’application et à vous connecter avec Engagement Mobile à partir de votre application mobile.

   	![][11]

##<a id="connecting-app"></a>Connexion de votre application au serveur principal Mobile Engagement

Ce didacticiel aborde l'intégration de base qui correspond aux éléments nécessaires à la collection de données et à l'envoi de notifications Push. Vous trouverez la documentation complète d’intégration dans la [documentation du Kit de développement logiciel \(SDK\) iOS Mobile Engagement]

Nous allons créer une application de base à l’aide de XCode afin d’illustrer l’intégration :

###Créer un projet iOS

Vous pouvez ignorer cette étape si vous disposez déjà d'une application et que vous êtes familiarisé avec le développement iOS

1. Lancez Xcode et sélectionnez **Create a new Xcode project** dans la fenêtre contextuelle

   	![][12]

2. Sélectionnez **Single View Application** et cliquez sur Suivant

   	![][14]

3. Renseignez le **nom de produit**, le **nom de l’organisation** et l’**identificateur d’organisation**. Assurez-vous que vous avez sélectionné le langage **Swift**.

   	![][40]

Xcode crée l'application de démonstration à laquelle nous allons intégrer Mobile Engagement

###Connexion de votre application au serveur principal Mobile Engagement 

1. Téléchargez le [Kit de développement logiciel \(SDK\) iOS Mobile Engagement]
2. Extrayez le fichier .tar.gz dans un dossier de votre ordinateur
3. Cliquez avec le bouton droit sur le projet et sélectionnez « Add files to ... »

	![][17]

4. Accédez au dossier dans lequel vous avez extrait le Kit de développement logiciel \(SDK\) et sélectionnez le dossier `EngagementSDK`, puis appuyez sur OK.

	![][18]

5. Ouvrez l’onglet `Build Phases` et dans le menu `Link Binary With Libraries`, ajoutez les infrastructures, comme indiqué ci-dessous :

	![][19]

6. Créez un en-tête de pontage pour pouvoir utiliser les API Objective C du Kit de développement logiciel \(SDK\) en choisissant Fichier \> Nouveau \> Fichier \> iOS \> Source \> Fichier d’en-tête.

	![][41]

7. Modifiez le fichier d’en-tête de pontage pour exposer le code AzME Objective-C sur votre code Swift, puis ajoutez les importations ci-après :

		/* Mobile Engagement Agent */
		#import "AEModule.h"
		#import "AEPushDelegate.h"
		#import "AEPushMessage.h"
		#import "AEStorage.h"
		#import "EngagementAgent.h"
		#import "EngagementTableViewController.h"
		#import "EngagementViewController.h"
		#import "AEIdfaProvider.h"

8. Sous Paramètres de génération, assurez-vous que le paramètre de génération d’en-tête de pontage Objective-C sous Compilateur Swift- Génération de code a un chemin d’accès à cet en-tête. Voici un exemple de chemin d’accès : **$\(SRCROOT\)/MySuperApp/MySuperApp-Bridging-Header.h \(selon le chemin d’accès\)**

9. De retour sur le portail Azure, dans la page *Informations de connexion* de votre application, copiez la chaîne de connexion.

	![][11]

10. Collez la chaîne de connexion dans le délégué `didFinishLaunchingWithOptions`

		func application(application: UIApplication, didFinishLaunchingWithOptions launchOptions: [NSObject: AnyObject]?) -> Bool
		{
  			[...]
				EngagementAgent.init("Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}")
  			[...]
		}

##<a id="monitor"></a>Activation de la surveillance en temps réel

Pour commencer à envoyer des données et s'assurer que les utilisateurs sont actifs, vous devez envoyer au moins un écran \(activité\) au serveur principal Mobile Engagement.

- Ouvrez le fichier `ViewController.h`, importez `EngagementViewController.h` et remplacez la super classe de l’interface `ViewController` par `EngagementViewController`.

###Vérification de la connexion de l’application à la surveillance en temps réel

Cette section montre comment vérifier que votre application est connectée au serveur principal Mobile Engagement à l'aide de la fonctionnalité de surveillance en temps réel de Mobile Engagement.

1. Accédez au portail Mobile Engagement

	Dans votre portail Azure, vérifiez que vous êtes bien dans l'application que nous utilisons pour ce projet, puis cliquez sur le bouton « Engagement » en bas de l'écran :

	![][26]

2. Vous accédez à la page de paramètres de votre application via le portail Engagement. Cliquez sur l'onglet « Monitor » :

	![][30]

3. Le moniteur est prêt à afficher tous les appareils, en temps réel, qui lancent votre application :

	![][31]

4. Revenez dans Xcode, lancez votre application dans le simulateur ou dans un appareil connecté

5. Si cela a fonctionné, vous devriez voir une session.

**Félicitations !** Vous avez réussi la première étape de ce didacticiel : votre application se connecte au serveur principal de Mobile Engagement et envoie déjà des données

6. Si vous cliquez sur le bouton Accueil du simulateur, le nombre de sessions dans le moniteur est réinitialisé à 0, comme illustré ci-dessus

	![][33]

##<a id="integrate-push"></a>Activation des notifications Push et de la messagerie intégrée à l’application

Mobile Engagement vous permet d'interagir et d'atteindre vos utilisateurs à l'aide de notifications Push et de la messagerie dans l'application, dans le cadre d'une campagne. Ce module s'appelle Couverture dans le portail Mobile Engagement. Les sections suivantes vous permettront de configurer votre application pour la réception des notifications.

### Ajoutez la bibliothèque du module Couverture à votre projet

1. Cliquez avec le bouton droit sur votre projet
2. Sélectionnez `Add file to ...`
3. Accédez au dossier où vous avez extrait le SDK
4. Sélectionnez le dossier `EngagementReach`
5. Cliquez sur Ajouter.
6. Modifiez le fichier d’en-tête de pontage pour exposer les en-têtes Couverture AzME Objective-C, puis ajoutez les importations suivantes :

		/* Mobile Engagement Reach */
		#import "AE_TBXML.h"
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

		if application.respondsToSelector("registerUserNotificationSettings:")
		{
			application.registerUserNotificationSettings(UIUserNotificationSettings(
			forTypes: (UIUserNotificationType.Alert | UIUserNotificationType.Badge | UIUserNotificationType.Sound),
			categories: nil))
			application.registerForRemoteNotifications()
		}
		else
		{
			application.registerForRemoteNotificationTypes(UIRemoteNotificationType.Alert | UIRemoteNotificationType.Badge | UIRemoteNotificationType.Sound)
		}

2. Ajoutez la méthode `didRegisterForRemoteNotificationsWithDeviceToken` comme suit :

		func application(application: UIApplication, didRegisterForRemoteNotificationsWithDeviceToken deviceToken: NSData)
		{
			EngagementAgent.shared().registerDeviceToken(deviceToken)
		}

3. Ajoutez la méthode `didReceiveRemoteNotification` comme suit :

		func application(application: UIApplication, didReceiveRemoteNotification userInfo: [NSObject : AnyObject])
		{
			EngagementAgent.shared().applicationDidReceiveRemoteNotification(userInfo)
		}

###Accordez à Mobile Engagement l'accès à votre certificat push

Pour autoriser Mobile Engagement à envoyer des notifications push à votre place, vous devez lui accorder l'accès à votre certificat. Pour ce faire, configurez votre certificat et entrez-le dans le portail Mobile Engagement. Assurez-vous d'obtenir votre certificat .p12 comme expliqué dans la documentation d'Apple.

1. Accédez au portail Mobile Engagement. Vérifiez que vous vous trouvez dans l’application utilisée pour ce projet, puis cliquez sur le bouton « Activer » situé au bas de l’écran :

	![][26]

2. Vous accédez à la page de paramètres via le portail Engagement. Cliquez sur la section « Push natif » pour télécharger votre certificat p12 :

	![][27]

3. Sélectionnez votre p12, téléchargez-le et tapez votre mot de passe :

	![][28]

4. Ensuite, ajoutez votre profil de mise en service et créez votre application pour un appareil cible

Vous avez fini. Nous allons maintenant vérifier que vous avez correctement effectué cette intégration de base

##<a id="send"></a>Envoi d’une notification vers votre application

Nous allons maintenant créer une campagne simple de notification push qui enverra une notification push à notre application :

1. Accédez à l'onglet Couverture de votre portail Mobile Engagement

2. Cliquez sur **Nouvelle annonce** pour créer votre campagne Push
	
	![][35]

3. Configurez le premier champ de votre campagne :

	![][36]

	- 	Attribuez le nom de votre choix à votre campagne
	- 	Sélectionnez « Hors de l’application uniquement » pour l’heure de remise : il s’agit du type simple de notification Push Apple qui comprend du texte.
	- 	Dans le texte de notification, tapez d'abord le titre, qui sera la première ligne de la notification push
	- 	Tapez ensuite votre message, qui sera la deuxième ligne


4. Faites défiler vers le bas et, dans la section du contenu, sélectionnez « Notification uniquement »

	![][37]

5. Vous venez de créer une campagne de base. Maintenant, faites défiler l’écran vers le bas pour créer et enregistrer votre propre campagne. ![][38]

6. Enfin, dernière étape, activez votre campagne ![][39]

7. Vous devriez voir une notification push dans votre appareil !

<!-- URLs. -->
[Kit de développement logiciel \(SDK\) iOS Mobile Engagement]: http://go.microsoft.com/?linkid=9864553
[Mobile Engagement Android SDK documentation]: http://go.microsoft.com/?linkid=9874682

<!-- Images. -->
[7]: ./media/mobile-engagement-common/create-mobile-engagement-app.png
[8]: ./media/mobile-engagement-common/create-azme-popup.png
[10]: ./media/mobile-engagement-common/app-main-page-select-connection-info.png
[11]: ./media/mobile-engagement-common/app-connection-info-page.png
[12]: ./media/mobile-engagement-ios-get-started/xcode-new-project.png
[13]: ./media/mobile-engagement-ios-get-started/xcode-project-props.png
[14]: ./media/mobile-engagement-ios-get-started/xcode-simple-view.png
[17]: ./media/mobile-engagement-ios-get-started/xcode-add-files.png
[18]: ./media/mobile-engagement-ios-get-started/xcode-select-engagement-sdk.png
[19]: ./media/mobile-engagement-ios-get-started/xcode-build-phases.png
[22]: ./media/mobile-engagement-ios-get-started/xcode-view-controller.png
[26]: ./media/mobile-engagement-common/engage-button.png
[27]: ./media/mobile-engagement-common/engagement-portal.png
[28]: ./media/mobile-engagement-ios-get-started/native-push-settings.png
[30]: ./media/mobile-engagement-common/clic-monitor-tab.png
[31]: ./media/mobile-engagement-common/monitor.png
[33]: ./media/mobile-engagement-ios-get-started/monitor-0.png
[35]: ./media/mobile-engagement-common/new-announcement.png
[36]: ./media/mobile-engagement-ios-get-started/campaign-first-params.png
[37]: ./media/mobile-engagement-common/campaign-content.png
[38]: ./media/mobile-engagement-common/campaign-create.png
[39]: ./media/mobile-engagement-common/campaign-activate.png
[40]: ./media/mobile-engagement-ios-get-started/SwiftSelection.png
[41]: ./media/mobile-engagement-ios-get-started/AddHeaderFile.png

<!--HONumber=54-->