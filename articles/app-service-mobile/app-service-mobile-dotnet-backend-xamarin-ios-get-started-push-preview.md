<properties 
	pageTitle="Ajout de notifications push à votre application Xamarin iOS à l'aide d'Azure App Service" 
	description="Découvrez comment utiliser Azure App Service pour envoyer des notifications push à votre application Xamarin iOS." 
	services="app-service\mobile" 
	documentationCenter="xamarin" 
	authors="ysxu"
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="app-service-mobile" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-xamarin-ios" 
	ms.devlang="dotnet" 
	ms.topic="article"
	ms.date="06/18/2015" 
	ms.author="yuaxu"/>

# Ajout de notifications push à votre application Xamarin iOS

[AZURE.INCLUDE [app-service-mobile-selector-get-started-push-preview](../../includes/app-service-mobile-selector-get-started-push-preview.md)]

Cette rubrique montre comment utiliser Azure App Service pour envoyer des notifications push à une application Xamarin iOS 8. Dans ce didacticiel, vous ajoutez des notifications push à l’aide du service de notifications push Apple (APNS) dans le projet [Prise en main des applications mobiles App Service]. Une fois la procédure terminée, votre backend mobile enverra une notification push chaque fois qu'un enregistrement sera inséré.

Ce didacticiel requiert les éléments suivants :

+ Un appareil iOS 8
+ Un abonnement au programme pour développeurs iOS
+ [Xamarin.iOS Studio]
+ [Composant Azure Mobile Services]

   >[AZURE.NOTE]En raison de la configuration requise pour les notifications Push, vous devez déployer et tester les notifications Push sur un appareil compatible iOS (iPhone ou iPad) au lieu d'un émulateur.

Le service de notification push Apple (APNS) utilise des certificats pour authentifier votre application mobile. Suivez ces instructions pour créer les certificats requis et les télécharger dans votre application mobile. Pour obtenir la documentation complète des fonctionnalités APNS, consultez la page [Service de notification Push Apple].

##<a name="review"></a>Examiner la configuration de votre projet de serveur (facultatif)

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-enable-push-preview](../../includes/app-service-mobile-dotnet-backend-enable-push-preview.md)]

## <a name="certificates"></a>Génération du fichier de demande de signature de certificat

Vous devez d'abord générer le fichier de demande de signature de certificat (CSR, Certificate Signing Request), qu'Apple utilise pour générer un certificat signé.

1. Depuis Utilitaires, exécutez l’outil **Trousseaux d’accès**.

2. Cliquez sur **Trousseaux d'accès**, développez **Assistant de certification**, puis cliquez sur **Demander un certificat à une autorité de certification**.

    ![][5]

3. Entrez l’**Adresse électronique de l’utilisateur**, entrez la valeur **Nom commun**, vérifiez que la case à cocher **Enregistré sur le disque** est activée, puis cliquez sur **Continuer**.

    ![][6]

4. Entrez un nom pour le fichier de demande de signature de certificat dans **Enregistrer sous**, sélectionnez l'emplacement dans **Où**, puis cliquez sur **Enregistrer**.

    ![][7]
  
    N'oubliez pas l'emplacement que vous avez choisi.

Ensuite, vous allez inscrire votre application auprès d'Apple, activer les notifications Push, puis télécharger ce fichier de demande de signature de certificat exporté pour créer un certificat Push.

## <a name="register"></a>Inscription de votre application pour les notifications Push

Pour pouvoir envoyer des notifications push à un appareil iOS à partir de votre application mobile, vous devez inscrire votre application auprès d'Apple et activer les notifications push.

1. Si vous n’avez pas déjà inscrit votre application, accédez au Portail</a> de mise en service iOS du centre de développement Apple, connectez-vous avec votre ID Apple, cliquez sur **Identifiers**, sur **App IDs**, puis sur le signe **+** pour inscrire une nouvelle application.
    
    ![][102]

2. Entrez un nom pour votre application dans **Description**, entrez une valeur dans **Bundle Identifier** et retenez-la, activez l’option « Push Notifications » dans la section « App Services », puis cliquez sur **Continue**. Cet exemple utilise l'ID **MobileServices.Quickstart**, mais vous ne pouvez pas réutiliser le même ID, car chaque ID d'application doit être unique pour chaque utilisateur. Ainsi, nous vous recommandons d'ajouter votre nom complet ou vos initiales après le nom de l'application.

    ![][103]
   
    L'ID de votre application est généré et vous êtes invité à **Envoyer** les informations. Cliquez sur **Envoyer**.
   
    ![][104]
   
    Lorsque vous avez cliqué sur **Submit**, le message **Registration complete** s'affiche, comme montré ci-dessous. Cliquez sur **Done**.
   
    ![][105]

3. Recherchez l'ID de l'application que vous venez de créer, puis cliquez sur sa ligne.

    ![][106]
   
    Le fait de cliquer sur l'ID de l'application affiche les informations détaillées de cette application et de l'ID d'application. Cliquez sur le bouton **Settings**.
   
    ![][107]
   
4. Faites défiler jusqu'en bas de l'écran, puis cliquez sur le bouton **Create Certificate...** sous la section **Development Push SSL Certificate**.

    ![][108]

    Ceci affiche l'Assistant « Add iOS Certificate ».
   
    Remarque : ce didacticiel utilise un certificat de développement. Le même processus est utilisé lors de l'inscription d'un certificat de production. Veillez simplement à définir le même type de certificat quand vous téléchargez le certificat vers votre application mobile.

5. Cliquez sur **Choose File**, accédez à l’emplacement où vous avez enregistré le fichier de demande de signature de certificat que vous avez créé lors de la première tâche, puis cliquez sur **Generate**.

    ![][110]
  
6. Une fois le certificat créé par le portail, cliquez sur le bouton **Download**, puis cliquez sur **Done**.
 
    ![][111]

    Ceci entraîne le téléchargement du certificat de signature et l'enregistre sur votre ordinateur dans le dossier Téléchargements.

    ![][9]

    Par défaut, le fichier téléchargé est un certificat de développement nommé <strong>aps_development.cer</strong>.

7. Double-cliquez sur le certificat Push téléchargé **aps_development.cer**.

    Ceci installe le nouveau certificat dans le Trousseau d'accès, comme indiqué ci-dessous :

    ![][10]

    Remarque : il se peut que le nom de votre certificat soit différent. Il portera toutefois le préfixe <strong>Apple Development iOS Push Notification Services:</strong>

Plus tard, vous utiliserez ce certificat pour générer un fichier .p12 et le téléchargerez sur votre application mobile pour activer l'authentification avec APNS.

## <a name="profile"></a>Création d’un profil de mise en service pour l’application
 
1. Une fois de retour dans le <a href="http://go.microsoft.com/fwlink/p/?LinkId=272456" target="_blank">Portail de mise en service iOS</a>, sélectionnez **Provisioning Profiles**, **All**, puis cliquez sur le bouton **+** pour créer un profil. Ceci démarre l’Assistant **Ajouter le profil d’approvisionnement iOS**.

    ![][112]

2. Sélectionnez **Développement d’application iOS** sous **Développement** en tant que type de profil d’approvisionnement, puis cliquez sur **Continuer**.

3. Ensuite, sélectionnez l’ID d’application de l’application de démarrage rapide **Mobile App** dans la liste déroulante App ID, puis cliquez sur **Continue**.

    ![][113]

4. Sur l’écran **Sélectionner les certificats**, sélectionnez le certificat créé plus tôt, puis cliquez sur **Continuer**.

    ![][114]

5. Ensuite, sélectionnez les appareils (**Devices**) à utiliser pour le test, puis cliquez sur **Continue**.
  
    ![][115]

6. Ensuite, sélectionnez un nom pour le profil dans **Nom du profil**, cliquez sur **Générer**, puis sur **Terminé**.

    ![][116]

    Ceci entraîne la création d'un profil de mise en service.

    ![][117]

## <a name="configure-appServiceMobile"></a>Configuration du serveur principal mobile App Service pour l’envoi de demandes push

[AZURE.INCLUDE [app-service-mobile-apns-configure-push-preview](../../includes/app-service-mobile-apns-configure-push-preview.md)]

##<a id="update-server"></a>Mise à jour du serveur pour l’envoi de notifications Push

1. Dans Visual Studio, cliquez avec le bouton droit sur la solution, puis cliquez sur **Manage NuGet Packages**.

2. Recherchez **Microsoft.Azure.NotificationHubs** et cliquez sur **Installer** pour tous les projets dans la solution.

3. Dans l’Explorateur de solutions de Visual Studio, développez le dossier **Contrôleurs** du projet de serveur principal mobile. Ouvrez TodoItemController.cs. Au début du fichier, ajoutez les instructions `using` suivantes :

        using System.Collections.Generic;
        using Microsoft.Azure.NotificationHubs;

4. Ajoutez l’extrait de code suivant à la méthode `PostTodoItem` après l’appel à **InsertAsync** :

        // get Notification Hubs credentials associated with this Mobile App
        string notificationHubName = this.Services.Settings.NotificationHubName;
        string notificationHubConnection = this.Services.Settings.Connections[ServiceSettingsKeys.NotificationHubConnectionString].ConnectionString;

        // connect to notification hub
        NotificationHubClient Hub = NotificationHubClient.CreateClientFromConnectionString(notificationHubConnection, notificationHubName);

        // iOS payload
        var appleNotificationPayload = "{"aps":{"alert":"" + item.Text + ""}}";

        await Hub.Push.SendAppleNativeNotificationAsync(appleNotificationPayload);

    Ce code indique au hub de notifications associé à cette application mobile d'envoyer une notification push après l'insertion d'une tâche.


## <a name="publish-the-service"></a>Publication du serveur principal mobile sur Azure

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-publish-service-preview](../../includes/app-service-mobile-dotnet-backend-publish-service-preview.md)]

## <a name="configure-app"></a>Configuration de votre application Xamarin.iOS

1. Dans Xamarin.Studio, ouvrez **Info.plist** et mettez à jour la valeur de **Bundle Identifier** avec l’ID que vous avez créé précédemment.

    ![][121]

2. Descendez jusqu’à **Background Modes**, puis activez les cases à cocher **Enable Background Modes** et **Remote notifications**.

    ![][122]

3. Double-cliquez sur votre projet dans Solution Panel pour ouvrir les **options de projet**.

4.  Sélectionnez **iOS Bundle Signing** sous **Build**, puis sélectionnez les éléments **Identity** et **Provisioning profile** que vous venez de configurer pour ce projet.

    ![][120]

    Ceci vérifie que le projet Xamarin utilise le nouveau profil pour la signature du code. Pour obtenir la documentation officielle de l’approvisionnement des appareils Xamarin, consultez la page [Approvisionnement des appareils Xamarin].

## <a name="add-push"></a>Ajout de notifications Push à votre application

1. Dans **QSTodoService**, remplacez la déclaration de client existante de sorte que **AppDelegate** puisse acquérir le client mobile :
        
        public MobileServiceClient client { get; private set; }

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

3. Dans le même fichier, remplacez l’événement **RegisteredForRemoteNotifications** :

        public override void RegisteredForRemoteNotifications(UIApplication application, NSData deviceToken)
        {
            MobileServiceClient client = QSTodoService.DefaultService.GetClient;

            // Register for push with your mobile app
            var push = client.GetPush();
            push.RegisterAsync(deviceToken);
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

L'application est mise à jour et prend en charge les notifications Push.


## <a name="publish-the-service"></a>Publication du serveur principal mobile sur Azure

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-publish-service-preview](../../includes/app-service-mobile-dotnet-backend-publish-service-preview.md)]

## <a name="test"></a>Test des notifications Push dans votre application

1. Cliquez sur le bouton **Exécuter** pour générer le projet et démarrer l'application sur un appareil compatible iOS, puis cliquez sur **OK** pour accepter les notifications Push.
	
	> [AZURE.NOTE]Vous devez accepter explicitement les notifications Push de votre application. Cette demande s'effectue uniquement lors du premier démarrage de l'application.

2. Dans l’application, tapez une tâche, puis cliquez sur l’icône plus (**+**).

3. Vérifiez que vous avez reçu une notification, puis cliquez sur **OK** pour fermer celle-ci.

4. Répétez l'étape 2 et fermez immédiatement l'application, puis vérifiez qu'une notification est affichée.

Vous avez terminé ce didacticiel.

<!-- Images. -->

[24]: ./media/mobile-services-ios-get-started-push/mobile-services-quickstart-push2-ios.png
[Prise en main des applications mobiles App Service]: app-service-mobile-dotnet-backend-xamarin-ios-get-started-preview.md

[5]: ./media/app-service-mobile-dotnet-backend-xamarin-ios-get-started-push-preview/mobile-services-ios-push-step5.png
[6]: ./media/app-service-mobile-dotnet-backend-xamarin-ios-get-started-push-preview/mobile-services-ios-push-step6.png
[7]: ./media/app-service-mobile-dotnet-backend-xamarin-ios-get-started-push-preview/mobile-services-ios-push-step7.png

[9]: ./media/app-service-mobile-dotnet-backend-xamarin-ios-get-started-push-preview/mobile-services-ios-push-step9.png
[10]: ./media/app-service-mobile-dotnet-backend-xamarin-ios-get-started-push-preview/mobile-services-ios-push-step10.png

[17]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-step17.png
[18]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-selection.png
[19]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-push-tab-ios.png
[20]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-push-tab-ios-upload.png
[21]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-portal-data-tables.png
[22]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-insert-script-push2.png
[23]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-quickstart-push1-ios.png
[25]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-quickstart-push3-ios.png
[26]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-quickstart-push4-ios.png
[28]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-step18.png

[101]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-01.png
[102]: ./media/app-service-mobile-dotnet-backend-xamarin-ios-get-started-push-preview/mobile-services-ios-push-02.png
[103]: ./media/app-service-mobile-dotnet-backend-xamarin-ios-get-started-push-preview/mobile-services-ios-push-03.png
[104]: ./media/app-service-mobile-dotnet-backend-xamarin-ios-get-started-push-preview/mobile-services-ios-push-04.png
[105]: ./media/app-service-mobile-dotnet-backend-xamarin-ios-get-started-push-preview/mobile-services-ios-push-05.png
[106]: ./media/app-service-mobile-dotnet-backend-xamarin-ios-get-started-push-preview/mobile-services-ios-push-06.png
[107]: ./media/app-service-mobile-dotnet-backend-xamarin-ios-get-started-push-preview/mobile-services-ios-push-07.png
[108]: ./media/app-service-mobile-dotnet-backend-xamarin-ios-get-started-push-preview/mobile-services-ios-push-08.png

[110]: ./media/app-service-mobile-dotnet-backend-xamarin-ios-get-started-push-preview/mobile-services-ios-push-10.png
[111]: ./media/app-service-mobile-dotnet-backend-xamarin-ios-get-started-push-preview/mobile-services-ios-push-11.png
[112]: ./media/app-service-mobile-dotnet-backend-xamarin-ios-get-started-push-preview/mobile-services-ios-push-12.png
[113]: ./media/app-service-mobile-dotnet-backend-xamarin-ios-get-started-push-preview/mobile-services-ios-push-13.png
[114]: ./media/app-service-mobile-dotnet-backend-xamarin-ios-get-started-push-preview/mobile-services-ios-push-14.png
[115]: ./media/app-service-mobile-dotnet-backend-xamarin-ios-get-started-push-preview/mobile-services-ios-push-15.png
[116]: ./media/app-service-mobile-dotnet-backend-xamarin-ios-get-started-push-preview/mobile-services-ios-push-16.png
[117]: ./media/app-service-mobile-dotnet-backend-xamarin-ios-get-started-push-preview/mobile-services-ios-push-17.png

[120]: ./media/app-service-mobile-dotnet-backend-xamarin-ios-get-started-push-preview/mobile-services-ios-push-20.png
[121]: ./media/app-service-mobile-dotnet-backend-xamarin-ios-get-started-push-preview/mobile-services-ios-push-21.png
[122]: ./media/app-service-mobile-dotnet-backend-xamarin-ios-get-started-push-preview/mobile-services-ios-push-22.png

[Xamarin.iOS Studio]: http://xamarin.com/platform
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[iOS Provisioning Portal]: http://go.microsoft.com/fwlink/p/?LinkId=272456
[Mobile Services iOS SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[Service de notification Push Apple]: http://go.microsoft.com/fwlink/p/?LinkId=272584
[Get started with Mobile Services]: /fr-fr/develop/mobile/tutorials/get-started-xamarin-ios
[Get started with data]: /fr-fr/develop/mobile/tutorials/get-started-with-data-xamarin-ios
[Get started with authentication]: /fr-fr/develop/mobile/tutorials/get-started-with-users-xamarin-ios
[Get started with push notifications]: /fr-fr/develop/mobile/tutorials/get-started-with-push-xamarin-ios
[Push notifications to app users]: /fr-fr/develop/mobile/tutorials/push-notifications-to-users-ios
[Authorize users with scripts]: /fr-fr/develop/mobile/tutorials/authorize-users-in-scripts-xamarin-ios
[Approvisionnement des appareils Xamarin]: http://developer.xamarin.com/guides/ios/getting_started/installation/device_provisioning/


[Azure Management Portal]: https://manage.windowsazure.com/
[apns object]: http://go.microsoft.com/fwlink/p/?LinkId=272333
[Composant Azure Mobile Services]: http://components.xamarin.com/view/azure-mobile-services/
[completed example project]: http://go.microsoft.com/fwlink/p/?LinkId=331303

 

<!---HONumber=July15_HO4-->