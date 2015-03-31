<properties 
	pageTitle="Prise en main d'Azure Mobile Engagement pour iOS" 
	description="Découvrez comment utiliser Azure Mobile Engagement avec les analyses et les notifications push sur iOS" 
	services="mobile-engagement" 
	documentationCenter="Mobile" 
	authors="kpiteira" 
	manager="dwrede" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-ios" 
	ms.devlang="objective-c" 
	ms.topic="article" 
	ms.date="02/11/2015" 
	ms.author="kapiteir" />

# Prise en main de Mobile Engagement

<div class="dev-center-tutorial-selector sublanding"><a href="/documentation/articles/mobile-engagement-windows-store-dotnet-get-started/" title="Windows Store">Windows Store</a><a href="/documentation/articles/mobile-engagement-windows-phone-get-started/" title="Windows Phone">Windows Phone</a><a href="/documentation/articles/mobile-engagement-ios-get-started/" title="iOS"  class="current">iOS</a><a href="/documentation/articles/mobile-engagement-android-get-started/" title="Android">Android</a></div>

Cette rubrique indique comment utiliser Azure Mobile Engagement pour comprendre l'utilisation de votre application et envoyer des notifications push aux utilisateurs segmentés d'une application iOS. 
Dans ce didacticiel, vous créez une application iOS vide qui collecte des données de base et reçoit des notifications push à l'aide du service APN (Apple Push Notification Service). Une fois l'application créée, vous pouvez diffuser des notifications push sur tous les appareils ou cibler des utilisateurs spécifiques en fonction des propriétés de l'appareil qu'ils utilisent.

Ce didacticiel illustre un scénario de diffusion simple à l'aide de Mobile Engagement. Vous allez apprendre comment utiliser Mobile Engagement pour toucher des utilisateurs et des groupes d'appareils spécifiques. 

Ce didacticiel requiert les éléments suivants :

+ Xcode, que vous pouvez installer depuis votre boutique d'applications Mac
+ Le [SDK iOS Mobile Engagement]
+ Le certificat de notification push (.p12) que vous pouvez obtenir dans votre Centre de développement Apple

Vous devez suivre ce didacticiel pour avoir accès à tous les autres didacticiels Mobile Engagement pour applications iOS. 

<div class="dev-callout"><strong>Remarque</strong> <p>Pour suivre ce didacticiel, vous avez besoin d'un compte Azure actif. Si vous ne possédez pas de compte, vous pouvez créer un compte d'évaluation gratuit en quelques minutes. Pour plus d'informations, consultez la page <a href="http://www.windowsazure.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Ffr-fr%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Version d'évaluation gratuite d'Azure</a>.</p></div>

<!--
##<a id="register"></a>Enable Apple Push Notification Service

[WACOM.INCLUDE [Enable Apple Push Notifications](../includes/enable-apple-push-notifications.md)]
-->

##<a id="setup-azme"></a>Configurer Mobile Engagement pour votre application

1. Connectez-vous au [portail de gestion Azure], puis cliquez sur **+NOUVEAU** en bas de l'écran.

2. Cliquez sur **Services d'application**, puis sur **Mobile Engagement** et sur **Créer**.

   	![][7]

3. Dans la fenêtre contextuelle qui s'affiche, remplissez les champs suivants :
 
   	![][8]

	1. *Nom de l'application* : vous pouvez taper le nom de votre application. Vous pouvez utiliser tous les caractères
	2. *Plateforme* : sélectionnez la plateforme cible pour cette application (si votre application cible plusieurs plateformes, répétez ce didacticiel pour chaque plateforme)
	3. *Nom de la ressource d'application* : il s'agit du nom par lequel cette application sera accessible via les API et les URL. Nous vous conseillons d'utiliser uniquement des caractères URL conventionnels : le nom généré automatiquement constitue une bonne base. Nous vous conseillons également d'ajouter le nom de la plateforme pour éviter tout conflit, car il doit être unique
	4. *Emplacement* : sélectionnez le centre de données où cette application (et surtout sa collection - voir ci-dessous) sera hébergée
	5. *Collection* : si vous avez déjà créé une application, sélectionnez une collection précédemment créée, sinon sélectionnez Nouvelle collection


	Quand vous avez terminé, cliquez sur le bouton de vérification pour terminer la création de votre application

4. Ensuite, sélectionnez l'application que vous venez de créer sous l'onglet Application :
 
   	![][9]

5. Puis, cliquez sur " Informations de connexion " pour afficher les paramètres de connexion à placer dans l'intégration du SDK :
 
   	![][10]

6. Enfin, notez la " Chaîne de connexion ", qui vous permettra d'identifier cette application dans votre code d'application :

   	![][11]

	>[AZURE.TIP] Vous pouvez utiliser l'icône de " copie " à droite de la chaîne de connexion pour la copier dans le presse-papiers.

##<a id="connecting-app"></a>Connexion de votre application au serveur principal de Mobile Engagement

Ce didacticiel présente une " intégration de base ", qui est le minimum requis pour collecter des données et envoyer une notification push. Vous trouverez la documentation complète d'intégration dans la [documentation du SDK iOS Mobile Engagement]

Nous allons créer une application de base avec Android Studio afin d'illustrer l'intégration

###Créer un projet iOS

Vous pouvez ignorer cette étape si vous disposez déjà d'une application et que vous êtes familiarisé avec le développement iOS

1. Lancez Xcode et sélectionnez " Create a new Xcode project " dans la fenêtre contextuelle

   	![][12]

2. Indiquez le nom de l'application, le nom et l'identificateur de la société. Notez-les, vous en aurez besoin plus tard, et cliquez sur Suivant

   	![][13]

3. Sélectionnez Single View Application et cliquez sur Suivant

   	![][14]


Xcode crée l'application de démonstration à laquelle nous allons intégrer Mobile Engagement

###Inclure la bibliothèque du SDK dans votre projet

Téléchargez et intégrez la bibliothèque du SDK

1. Téléchargez le [SDK iOS Mobile Engagement]
2. Extrayez le fichier .tar.gz dans un dossier de votre ordinateur
3. Cliquez avec le bouton droit sur le projet et sélectionnez " Add files to ... "

	![][17]

4. Accédez au dossier où vous avez extrait le SDK et sélectionnez le dossier  `EngagementSDK` puis appuyez sur OK

	![][18]

5. Ouvrez l'onglet `Build Phases` et, dans le menu `Link Binary With Libraries`, ajoutez les infrastructures comme indiqué ci-dessous :

	![][19]


###Connecter votre application au serveur principal de Mobile Engagement avec la chaîne de connexion

1. Copiez la ligne de code suivante en haut du fichier d'implémentation de votre délégué d'application

		#import "EngagementAgent.h"

2. Revenez au portail Azure dans la page *Informations de connexion* de votre application et copiez la chaîne de connexion

	![][11]

3. Collez-la dans le délégué  `didFinishLaunchingWithOptions`		

		- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
		{
  			[...]
  			[EngagementAgent init:@"Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}"];
  			[...]
		}

##Envoyer un écran à Mobile Engagement

Avant de commencer l'envoi de données et d'assurer que les utilisateurs sont actifs, vous devez envoyer au moins 1 écran (activité) au serveur principal de Mobile Engagement. Pour ce faire, vous devez surcharger la classe `UIViewController` avec le `EngagementViewController` fourni par notre SDK.
Ouvrez le fichier `ViewController.h`, importez `EngagementViewController.h` et remplacez la super classe de l'interface `ViewController` par `EngagementViewController`, comme illustré ci-dessous :

![][22]

##<a id="monitor"></a>Comment vérifier que votre application est connectée à l'aide du contrôle en temps réel

Cette section décrit comment vous assurer que votre application se connecte au serveur principal de Mobile Engagement à l'aide de la fonctionnalité de contrôle en temps réel de Mobile Engagement.

1. Accédez au portail Mobile Engagement

	Dans votre portail Azure, vérifiez que vous êtes bien dans l'application que nous utilisons pour ce projet, puis cliquez sur le bouton " Engagement " en bas de l'écran :

	![][26]

2. Vous accédez à la page de paramètres de votre application via le portail Engagement. Cliquez sur l'onglet " Monitor " :

	![][30]

3. Le moniteur est prêt à afficher tous les appareils, en temps réel, qui lancent votre application :

	![][31]

4. Revenez dans Xcode, lancez votre application dans le simulateur ou dans un appareil connecté

5. Si cela a fonctionné, vous devez voir 1 session affichée dans le moniteur. 

**Félicitations !** Vous avez réussi la première étape de ce didacticiel : votre application se connecte au serveur principal de Mobile Engagement et envoie déjà des données

6. Si vous cliquez sur le bouton Accueil du simulateur, le nombre de sessions dans le moniteur est réinitialisé à 0, comme illustré ci-dessus

	![][33]



##<a id="integrate-push"></a>Activation des notifications push et des messages dans l'application

Mobile Engagement vous permet d'interagir avec vos utilisateurs et de les TOUCHER avec des notifications push et des messages dans l'application, dans le cadre de campagnes. Ce module est appelé COUVERTURE dans le portail Mobile Engagement.
Les sections suivantes vous aident à configurer votre application pour les recevoir.

### Ajoutez la bibliothèque du module Couverture à votre projet

1. Cliquez avec le bouton droit sur votre projet
2. Sélectionnez `Add files to ...`
3. Accédez au dossier où vous avez extrait le SDK
4. Sélectionnez le dossier `EngagementReach`
5. Cliquez sur Ajouter.

### Modifiez le délégué de votre application

1. En haut de votre fichier d'implémentation, importez le module Couverture Engagement

		#import "AEReachModule.h"
	
2. Dans `application:didFinishLaunchingWithOptions`, créez un module Couverture et passez-le à la ligne existante d'initialisation d'Engagement :

		- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
			AEReachModule * reach = [AEReachModule moduleWithNotificationIcon:[UIImage imageNamed:@"icon.png"]];
			[EngagementAgent init:@"Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}" modules:reach, nil];
			[...]
			return YES;
		}	

###Activez votre application pour recevoir des notifications push du service APN

1. Ajoutez la ligne suivante à la méthode `application:didFinishLaunchingWithOptions` :

		if ([application respondsToSelector:@selector(registerUserNotificationSettings:)]) {
			[application registerUserNotificationSettings:[UIUserNotificationSettings settingsForTypes:(UIUserNotificationTypeBadge | UIUserNotificationTypeSound | UIUserNotificationTypeAlert) categories:nil]];
			[application registerForRemoteNotifications];
		}
		else {

			[application registerForRemoteNotificationTypes:(UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound | UIRemoteNotificationTypeAlert)];
		}

2. Ajoutez la méthode `application:didRegisterForRemoteNotificationsWithDeviceToken` comme suit :
 
		- (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData *)deviceToken
		{
 		   [[EngagementAgent shared] registerDeviceToken:deviceToken];
		}

3. Ajoutez la méthode `didReceiveRemoteNotification` comme suit :
4. 
		- (void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo
		{
		    [[EngagementAgent shared] applicationDidReceiveRemoteNotification:userInfo];
		}

###Accordez à Mobile Engagement l'accès à votre certificat push

Pour autoriser Mobile Engagement à envoyer des notifications push à votre place, vous devez lui accorder l'accès à votre certificat. Pour ce faire, configurez votre certificat et entrez-le dans le portail Mobile Engagement. Assurez-vous d'obtenir votre certificat .p12 comme expliqué dans la documentation d'Apple.

1. Accédez au portail Mobile Engagement

	Dans votre portail Azure, vérifiez que vous êtes bien dans l'application que nous utilisons pour ce projet, puis cliquez sur le bouton " Engagement " en bas de l'écran :

	![][26]

2. Vous accédez à la page de paramètres via le portail Engagement. Cliquez sur la section " Push natif " pour télécharger votre certificat p12 :

	![][27]

3. Sélectionnez votre p12, téléchargez-le et tapez votre mot de passe :

	![][28]

4. Ensuite, ajoutez votre profil de mise en service et créez votre application pour un appareil cible

Vous avez fini. Nous allons maintenant vérifier que vous avez correctement effectué cette intégration de base

##<a id="send"></a>Envoi d'une notification à votre application

Nous allons maintenant créer une campagne simple de notification push qui enverra une notification push à notre application :

1. Accédez à l'onglet Couverture de votre portail Mobile Engagement

2. Cliquez sur " Nouvelle annonce " pour créer votre campagne push
	
	![][35]

3. Configurez le premier champ de votre campagne :

	![][36]

	1. Donnez un nom quelconque à votre campagne
	2. Sélectionnez " Hors de l'application uniquement " pour l'heure de remise : il s'agit du type simple de notification push Apple qui comprend du texte
	3. Dans le texte de notification, tapez d'abord le titre, qui sera la première ligne de la notification push
	4. Tapez ensuite votre message, qui sera la deuxième ligne

4. Faites défiler vers le bas et, dans la section du contenu, sélectionnez " Notification uniquement "

	![][37]

5. Vous avez terminé la configuration de la campagne la plus élémentaire. À présent, faites défiler à nouveau et créez votre campagne pour l'enregistrer.
![][38]

6. Dernière étape, activez votre campagne
![][39]

7. Vous devriez voir une notification push dans votre appareil !

<!-- URLs. -->
[SDK iOS Mobile Engagement]: http://go.microsoft.com/?linkid=9864553
[Documentation du SDK Android Mobile Engagement]: http://go.microsoft.com/?linkid=9874682
[Portail de gestion Azure]: https://manage.windowsazure.com

<!-- Images. -->
[7]: ./media/mobile-engagement-ios-get-started/create-mobile-engagement-app.png
[8]: ./media/mobile-engagement-ios-get-started/create-azme-popup.png
[9]: ./media/mobile-engagement-ios-get-started/select-app.png
[10]: ./media/mobile-engagement-ios-get-started/app-main-page-select-connection-info.png
[11]: ./media/mobile-engagement-ios-get-started/app-connection-info-page.png
[12]: ./media/mobile-engagement-ios-get-started/xcode-new-project.png
[13]: ./media/mobile-engagement-ios-get-started/xcode-project-props.png
[14]: ./media/mobile-engagement-ios-get-started/xcode-simple-view.png
[17]: ./media/mobile-engagement-ios-get-started/xcode-add-files.png
[18]: ./media/mobile-engagement-ios-get-started/xcode-select-engagement-sdk.png
[19]: ./media/mobile-engagement-ios-get-started/xcode-build-phases.png
[22]: ./media/mobile-engagement-ios-get-started/xcode-view-controller.png
[23]: ./media/mobile-engagement-ios-get-started/copy-resources.png
[24]: ./media/mobile-engagement-ios-get-started/paste-resources.png
[25]: ./media/mobile-engagement-ios-get-started/paste-resources.png
[26]: ./media/mobile-engagement-ios-get-started/engage-button.png
[27]: ./media/mobile-engagement-ios-get-started/engagement-portal.png
[28]: ./media/mobile-engagement-ios-get-started/native-push-settings.png
[29]: ./media/mobile-engagement-ios-get-started/api-key.png
[30]: ./media/mobile-engagement-ios-get-started/clic-monitor-tab.png
[31]: ./media/mobile-engagement-ios-get-started/monitor.png
[32]: ./media/mobile-engagement-ios-get-started/launch.png
[33]: ./media/mobile-engagement-ios-get-started/monitor-0.png
[35]: ./media/mobile-engagement-ios-get-started/new-announcement.png
[36]: ./media/mobile-engagement-ios-get-started/campaign-first-params.png
[37]: ./media/mobile-engagement-ios-get-started/campaign-content.png
[38]: ./media/mobile-engagement-ios-get-started/campaign-create.png
[39]: ./media/mobile-engagement-ios-get-started/campaign-activate.png

<!--HONumber=47-->
