<properties 
	pageTitle="Ajouter des notifications Push à votre application Xamarin.iOS à l’aide d’Azure App Service" 
	description="Découvrez comment utiliser Azure App Service pour envoyer des notifications Push à votre application Xamarin.iOS." 
	services="app-service\mobile" 
	documentationCenter="xamarin" 
	authors="wesmc7777"
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="app-service-mobile" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-xamarin-ios" 
	ms.devlang="dotnet" 
	ms.topic="article"
	ms.date="12/18/2015" 
	ms.author="wesmc"/>

# Ajouter des notifications Push à votre application Xamarin.iOS

[AZURE.INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]
&nbsp;  
[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

##Vue d'ensemble

Ce didacticiel est basé sur le [didacticiel Démarrage rapide de Xamarin.iOS](app-service-mobile-xamarin-ios-get-started.md), que vous devez effectuer en premier. Vous ajoutez des notifications Push au projet Démarrage rapide Xamarin.iOS afin qu'une notification Push soit envoyée chaque fois qu'un enregistrement est inséré. Si vous n’utilisez pas le projet de serveur du démarrage rapide téléchargé, vous devez ajouter le package d’extension de notification Push à votre projet. Pour plus d'informations sur les packages d'extension de serveur, consultez [Fonctionnement avec le Kit de développement logiciel (SDK) du serveur principal .NET pour Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

##Composants requis

Pour réaliser ce didacticiel, vous avez besoin des éléments suivants :

* Un compte Azure actif. Si vous n’avez pas encore de compte, inscrivez-vous pour obtenir une version d’essai Azure et jusqu’à 10 backends d’application mobile gratuits. Vous pouvez continuer à les utiliser même après la fin de votre période d’évaluation. Consultez [Version d'évaluation gratuite d'Azure](https://azure.microsoft.com/pricing/free-trial/).

* Un Mac sur lequel sont installés [Xamarin Studio] et [Xcode] v4.4 ou version ultérieure. Vous pouvez exécuter l'application Xamarin.Forms à l'aide de Visual Studio sur un ordinateur Windows si vous le souhaitez. Mais c'est une opération peu plus compliqué, car vous devez vous connecter à un Mac en réseau exécutant l'hôte de build Xamarin.iOS. Si vous souhaitez effectuer cette opération, consultez la rubrique [Installation de Xamarin.iOS sur Windows].

* Un appareil iOS physique. Les notifications Push ne sont pas prises en charge par le simulateur iOS.

* Pour vous inscrire au Apple Push Notification Service (APNS), vous devez faire partie du [programme de développement Apple](https://developer.apple.com/programs/ios/).

* Terminez le [didacticiel de démarrage rapide Xamarin.iOS](app-service-mobile-xamarin-ios-get-started.md).


##Inscrire l'application pour les notifications push dans le portail de développeurs d'Apple

[AZURE.INCLUDE [Notification Hubs Xamarin - Activer les notifications Push Apple](../../includes/notification-hubs-xamarin-enable-apple-push-notifications.md)]

##Configuration de votre application mobile pour l'envoi de notifications push

Pour configurer l'envoi de notifications depuis votre application, créez un Hub et configurez-le pour les services de notification de plateforme que vous utiliserez.

1. Dans le [portail Azure](https://portal.azure.com/), cliquez sur **Parcourir** > **Mobile Apps** > votre application mobile > **Paramètres** > **Mobile** > **Push** > **Concentrateur de notification** > **+ Concentrateur de notification**, indiquez un nom et un espace de noms associés à un nouveau concentrateur de notification, puis cliquez sur le bouton **OK**.

	![](./media/app-service-mobile-xamarin-ios-get-started-push/mobile-app-configure-notification-hub.png)

2. Dans le panneau Créer un concentrateur de notification, cliquez sur **Créer**.

3. Cliquez sur **Push** > **Apple (APNS)** > **Télécharger le certificat**. Chargez le fichier de certificat push .p12 exporté précédemment. Veillez à sélectionner **Bac à sable (sandbox)** si vous avez créé un certificat Push de développement pour le développement et le test. Sinon, sélectionnez **Production**.

	![](./media/app-service-mobile-xamarin-ios-get-started-push/mobile-app-upload-apns-cert.png)

Votre service est désormais configuré et prêt à fonctionner avec les notifications Push sur iOS.

##Mettre à jour le projet de serveur pour l'envoi de notifications Push

[AZURE.INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]

##Configurer votre projet Xamarin.iOS

[AZURE.INCLUDE [app-service-mobile-xamarin-ios-configure-project](../../includes/app-service-mobile-xamarin-ios-configure-project.md)]

##Ajout de notifications Push à votre application

1. Dans **QSTodoService**, ajoutez la propriété suivante pour qu’**AppDelegate** puisse acquérir le client mobile :
        
            public MobileServiceClient GetClient {
            get
            {
                return client;
            }
            private set
            {
                client = value;
            }
        }

1. Ajoutez l’instruction `using` suivante au début du fichier **AppDelegate.cs**.

		using Microsoft.WindowsAzure.MobileServices;
		using Newtonsoft.Json.Linq;

2. Dans **AppDelegate**, remplacez l'événement **FinishedLaunching** :

        public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
        {
            // registers for push for iOS8
            var settings = UIUserNotificationSettings.GetSettingsForTypes(
                UIUserNotificationType.Alert 
                | UIUserNotificationType.Badge 
                | UIUserNotificationType.Sound, 
                new NSSet());

            UIApplication.SharedApplication.RegisterUserNotificationSettings(settings); 
            UIApplication.SharedApplication.RegisterForRemoteNotifications();

            return true;
        }

3. Dans le même fichier, remplacez l’événement **RegisteredForRemoteNotifications**. Dans ce code, vous inscrivez une notification de modèle simple qui sera envoyée sur toutes les plateformes prises en charge par le serveur.
 
	Pour plus d’informations sur les modèles avec Notification Hubs, consultez [Modèles](../notification-hubs/notification-hubs-templates.md).


        public override void RegisteredForRemoteNotifications(UIApplication application, NSData deviceToken)
        {
            MobileServiceClient client = QSTodoService.DefaultService.GetClient;

            const string templateBodyAPNS = "{"aps":{"alert":"$(messageParam)"}}";

            JObject templates = new JObject();
            templates["genericMessage"] = new JObject
            {
                {"body", templateBodyAPNS}
            };

            // Register for push with your mobile app
            var push = client.GetPush();
            push.RegisterAsync(deviceToken, templates);
        }


4. Ensuite, remplacez l’événement **DidReceivedRemoteNotification** :

        public override void DidReceiveRemoteNotification (UIApplication application, NSDictionary userInfo, Action<UIBackgroundFetchResult> completionHandler)
        {
            NSDictionary aps = userInfo.ObjectForKey(new NSString("aps")) as NSDictionary;

            string alert = string.Empty;
            if (aps.ContainsKey(new NSString("alert")))
                alert = (aps [new NSString("alert")] as NSString).ToString();

            //show alert
            if (!string.IsNullOrEmpty(alert))
            {
                UIAlertView avAlert = new UIAlertView("Notification", alert, null, "OK", null);
                avAlert.Show();
            }
        }

L’application est mise à jour et prend en charge les notifications Push.

## <a name="test"></a>Test des notifications Push dans votre application

1. Cliquez sur le bouton **Exécuter** pour générer le projet et démarrer l'application sur un appareil compatible iOS, puis cliquez sur **OK** pour accepter les notifications Push.
	
	> [AZURE.NOTE] Vous devez accepter explicitement les notifications Push de votre application. Cette demande s’effectue uniquement lors du premier démarrage de l’application.

2. Dans l’application, tapez une tâche, puis cliquez sur l’icône plus (**+**).

3. Vérifiez que vous avez reçu une notification, puis cliquez sur **OK** pour fermer celle-ci.

4. Répétez l’étape 2 et fermez immédiatement l’application, puis vérifiez qu’une notification est affichée.

Vous avez terminé ce didacticiel.

<!-- Images. -->

<!-- URLs. -->
[Xamarin Studio]: http://xamarin.com/platform
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Xcode]: https://go.microsoft.com/fwLink/?LinkID=266532
[Installation de Xamarin.iOS sur Windows]: http://developer.xamarin.com/guides/ios/getting_started/installation/windows/


 

<!---HONumber=AcomDC_0128_2016-->