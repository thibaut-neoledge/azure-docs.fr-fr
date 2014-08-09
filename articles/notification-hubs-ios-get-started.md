<properties linkid="develop-notificationhubs-tutorials-get-started-ios" urlDisplayName="Get Started" pageTitle="Get Started with Azure Notification Hubs" metaKeywords="" description="Learn how to use Azure Notification Hubs to push notifications." metaCanonical="" services="notification-hubs" documentationCenter="Mobile" title="Get started with Notification Hubs" authors="elioda" solutions="" manager="" editor="" />

Prise en main de Notification Hubs
==================================

[Windows Store C\#](/en-us/manage/services/notification-hubs/getting-started-windows-dotnet "Windows Store C#")[Windows Phone](/fr-fr/documentation/articles/notification-hubs-windows-phone-get-started/ "Windows Phone")[iOS](/fr-fr/documentation/articles/notification-hubs-ios-get-started/ "iOS")[Android](/fr-fr/documentation/articles/notification-hubs-android-get-started/ "Android")[Kindle](/fr-fr/documentation/articles/notification-hubs-kindle-get-started/ "Kindle")[Xamarin.iOS](/fr-fr/documentation/articles/partner-xamarin-notification-hubs-ios-get-started/ "Xamarin.iOS")[Xamarin.Android](/fr-fr/documentation/articles/partner-xamarin-notification-hubs-android-get-started/ "Xamarin.Android")

Cette rubrique montre comment utiliser Azure Notification Hubs pour envoyer des notifications Push vers une application iOS. Le didacticiel vous apprend à créer une application iOS vide qui reçoit des notifications Push au moyen du Service de notifications Push Apple (APNS). Au final, vous pouvez diffuser des notifications Push sur tous les appareils sur lesquels votre application est installée au moyen de votre Notification Hub.

Ce didacticiel vous familiarise avec les étapes de base permettant d'activer les notifications Push :

1.  [Génération de la demande de signature de certificat](#certificates)
2.  [Inscription de votre application et activation des notifications Push](#register)
3.  [Création d'un profil de mise en service pour l'application](#profile)
4.  [Configuration de votre Notification Hub](#configure-hub)
5.  [Connexion de votre application au Notification Hub](#connecting-app)
6.  [Envoi de notifications à partir de votre serveur principal](#send)

Ce didacticiel présente un scénario de diffusion simple utilisant les Notification Hubs. Suivez scrupuleusement le didacticiel suivant pour apprendre à utiliser Notification Hubs pour accéder à des utilisateurs et groupes d'appareils spécifiques. Ce didacticiel requiert les composants requis suivants :

-   [Kit de développement logiciel (SDK) Mobile Services iOS](http://go.microsoft.com/fwLink/?LinkID=266533)
-   [XCode 4.5](https://go.microsoft.com/fwLink/p/?LinkID=266532)
-   Un appareil compatible iOS 5.0 (ou versions ultérieures)
-   Un abonnement au programme pour développeurs iOS

    **Remarque**

    En raison de la configuration requise pour les notifications Push, vous devez déployer et tester les notifications Push sur un appareil compatible iOS (iPhone ou iPad) au lieu d'un émulateur.

Vous devez suivre ce didacticiel avant de pouvoir suivre tous les autres didacticiels Notification Hubs pour les applications iOS.

**Remarque**

Pour suivre ce didacticiel, vous avez besoin d'un compte Azure actif. Si vous ne possédez pas de compte, vous pouvez créer un compte d'évaluation gratuit en quelques minutes. Pour plus d'informations, consultez la page [Version d'évaluation gratuite d'Azure](http://www.windowsazure.com/fr-fr/pricing/free-trial/?WT.mc_id=A0E0E5C02&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fen-us%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F).

Le service de notifications Push Apple (APNS) utilise des certificats pour authentifier votre service mobile. Suivez ces instructions pour créer les certificats requis et les télécharger dans votre service mobile. Pour obtenir la documentation complète des fonctionnalités APNS, consultez la page [Service de notification Push Apple].

Génération d'un fichier CSRGénération d'un fichier de demande de signature de certificat (CSR)
----------------------------------------------------------------------------------------------

Vous devez d'abord générer le fichier de demande de signature de certificat (CSR), qu'Apple utilise pour générer un certificat signé.

1.  Dans le dossier Utilitaires, exécutez l'outil Trousseau d'accès.

2.  Cliquez sur **Trousseau d'accès**, développez **Assistant Certificat**, puis cliquez sur **Demander un certificat à partir d'une autorité de certification...**.

	![](./media/notification-hubs-ios-get-started/mobile-services-ios-push-step5.png)

1.  Sélectionnez votre **Adresse de messagerie de l'utilisateur** et votre **Nom commun**, vérifiez que la case à cocher **Enregistré sur le disque** est activée, puis cliquez sur **Continuer**. Laissez le champ **Adresse de messagerie d'autorité de certification** vide, car il n'est pas requis.

	![](./media/notification-hubs-ios-get-started/mobile-services-ios-push-step6.png)

1.  Entrez un nom pour le fichier de demande de signature de certificat dans **Enregistrer sous**, sélectionnez l'emplacement dans **Où**, puis cliquez sur **Enregistrer**.

	![](./media/notification-hubs-ios-get-started/mobile-services-ios-push-step7.png)

Ceci enregistre le fichier de demande de signature de certificat dans l'emplacement sélectionné, l'emplacement par défaut étant situé sur le Bureau. Notez l'emplacement sélectionné pour ce fichier.

Ensuite, inscrivez votre application auprès d'Apple, activez les notifications Push, puis téléchargez ce fichier de demande de signature de certificat exporté pour créer un certificat Push.

Inscription de votre applicationInscription de votre application pour les notifications Push
--------------------------------------------------------------------------------------------

Pour pouvoir envoyer des notifications Push vers une application iOS à partir des services mobiles, vous devez inscrire votre application auprès d'Apple, ainsi qu'aux notifications Push.

1.  Si vous n'avez pas déjà inscrit votre application, accédez au [Portail de mise en service iOS](http://go.microsoft.com/fwlink/p/?LinkId=272456) auprès du centre de développement Apple, connectez-vous avec votre ID Apple, cliquez sur **Identifiers**, puis sur **App IDs**, puis sur le signe **+** pour inscrire une nouvelle application.

	![][B102] 

2.  Entrez un nom pour votre application dans **Description**, entrez la valeur *MobileServices.Quickstart* dans **Bundle Identifier**, activez l'option « Push Notifications » dans la section « App Services », puis cliquez sur **Continue**. Cet exemple utilise l'ID **MobileServices.Quickstart**, mais vous ne pouvez pas réutiliser le même ID, car chaque ID d'application doit être unique pour chaque utilisateur. Ainsi, nous vous recommandons d'ajouter votre nom complet ou vos initiales après le nom de l'application.

	![][B103]
           
	Ceci génère l'ID de votre application et vous demande d'**Envoyer** les informations. Cliquez sur **Envoyer**
           
	![][B104] 
           
	Lorsque vous avez cliqué sur **Envoyer**, le message **Inscription terminée** s'affiche à l'écran, comme montré ci-dessous. Cliquez sur **Terminé**.
           
	![][B105]

    > [WACOM.NOTE] Si vous choisissez d'entrer une autre valeur **Bundle Identifier** que *MobileServices.Quickstart*, vous devez également mettre à jour la valeur d'identificateur de lot dans votre projet Xcode.

3.  Recherchez l'ID de l'application que vous venez de créer, puis cliquez sur sa ligne.

	![][B106]
           
	Le fait de cliquer sur l'ID de l'application affiche les informations détaillées de cette application et de l'ID d'application. Cliquez sur le bouton **Paramètres**.
           
	![][B107] 

4.  Faites défiler jusqu'en bas de l'écran, puis cliquez sur le bouton **Create Certificate...** sous la section **Development Push SSL Certificate**.

	![][B108] 

	Ceci affiche l'Assistant « Ajouter un certificat iOS ».
           
	![][B108] 

    > [WACOM.NOTE] Ce didacticiel utilise un certificat de développement. Le même processus est utilisé lors de l'inscription d'un certificat de production. Assurez-vous simplement que vous avez défini le même type de certificat lorsque vous avez téléchargé le certificat vers Mobile Services.

5.  Cliquez sur **Choose File**, accédez à l'emplacement où vous avez enregistré le fichier de demande de signature de certificat que vous avez créé lors de la première tâche, puis cliquez sur **Generate**.

	![](./media/notification-hubs-ios-get-started/notification-hub-clone-mobile-services-ios-push-10.png)

1.  Une fois le certificat créé par le portail, cliquez sur le bouton **Download**, puis cliquez sur **Done**.

	![](./media/notification-hubs-ios-get-started/notification-hub-clone-mobile-services-ios-push-11.png)

	Ceci entraîne le téléchargement du certificat de signature et l'enregistre sur votre ordinateur dans le dossier Téléchargements.

	![](./media/notification-hubs-ios-get-started/notification-hub-clone-mobile-services-ios-push-step9.png)

    > [WACOM.NOTE] Par défaut, le certificat de développement du fichier téléchargé est nommé **aps_development.cer**.

1.  Double-cliquez sur le certificat Push téléchargé **aps\_development.cer**.

	Ceci installe le nouveau certificat dans le Trousseau d'accès, comme indiqué ci-dessous :

	![][B10]

    > [WACOM.NOTE] Le nom de votre certificat peut être différent, il aura tout de même le préfixe **Apple Development iOS Push Notification Services:**.

Plus tard, vous devrez utiliser ce certificat pour générer un fichier .p12 et le télécharger vers Mobile Services pour activer l'authentification avec APNS.

Mise en service de l'applicationCréation du profil de mise en service de l'application
--------------------------------------------------------------------------------------

1.  Une fois de retour sur le [Portail de mise en service iOS](http://go.microsoft.com/fwlink/p/?LinkId=272456), sélectionnez **Provisioning Profiles**, sélectionnez **All**, puis cliquez sur le signe **+** pour créer un nouveau profil. Ainsi, l'Assistant **Add iOS Provisioning Profile** s'affiche.

	![][120]

2.  Sélectionnez **iOS App Development** sous **Development** en tant que type de profil de mise en service, puis cliquez sur **Continue**

	![][121]

3.  Ensuite, sélectionnez l'ID de l'application de démarrage rapide Mobile Services dans la liste déroulante **App ID**, puis cliquez sur **Continue**.

	![][122]

4.  Dans l'écran **Select certificates**, sélectionnez le certificat créé plus tôt, puis cliquez sur **Continue**.

	![][123]

5.  Ensuite, sélectionnez les **Appareils** à utiliser pour le test, puis cliquez sur **Continue**.

	![][124]

6.  Enfin, sélectionnez un nom pour le profil dans **Profile Name**, cliquez sur **Generate**, puis sur **Done**.

	![][125]
           
	![][126]

    Ceci entraîne la création d'un profil de mise en service.

7.  Dans Xcode, ouvrez l'Organisateur, sélectionnez la vue Devices, puis **Provisioning Profiles** dans la section **Library** du volet de gauche et importez le profil de mise en service que vous venez de créer.

8.  Sur la gauche, sélectionnez votre appareil et importez à nouveau le profil de mise en service.

9.  Dans Trousseau d'accès, cliquez avec le bouton droit sur le nouveau certificat, cliquez sur **Exporter**, tapez un nom pour votre certificat, sélectionnez le format **.p12**, puis cliquez sur **Enregistrer**.

	![][18]

    Notez le nom du fichier et l'emplacement du certificat exporté.

Configuration de votre Notification HubConfiguration de votre Notification Hub
------------------------------------------------------------------------------

1.  Connectez-vous au [portail de gestion Azure](https://manage.windowsazure.com/) et cliquez sur **+NEW** en bas de l'écran.

2.  Cliquez sur **App Services**, puis sur **Service Bus**, **Notification Hub**, **Quick Create**.

	![][27]

3.  Tapez un nom pour votre Notification Hub, sélectionnez la région souhaitée, puis cliquez sur **Create a new Notification Hub**.

	![][28]

4.  Cliquez sur l'espace de noms que vous venez de créer (en général ***nom du Notification Hub*-ns**), puis cliquez sur l'onglet **Configure** en haut.

	![][29]

5.  Cliquez sur l'onglet **Notification Hubs** en haut de la page, puis cliquez sur le Notification Hub que vous venez de créer.

	![][210]

6.  Cliquez sur l'onglet **Configure** en haut de la page, puis cliquez sur **Upload** dans les paramètres de notification d'Apple. Sélectionnez ensuite le certificat **.p12** exporté précédemment ainsi que le mot de passe du certificat. Assurez-vous d'indiquer si vous souhaitez utiliser la **Production** (si vous souhaitez envoyer des notifications Push aux utilisateurs qui ont acheté votre application depuis le store) ou le service Push **Bac à sable** (au cours du développement).

	![][211]

7.  Cliquez sur l'onglet **Dashboard** dans la partie supérieure, puis sur **Connection Information**. Notez les deux chaînes de connexion.

	![][212]

Votre Notification Hub est configuré pour APNS, et vous disposez des chaînes de connexion pour inscrire votre application et envoyer des notifications.

Connexion de votre applicationConnexion de votre application au Notification Hub
--------------------------------------------------------------------------------

1.  Dans XCode, créez un projet iOS et sélectionnez le modèle **Single View Application**.

	![][31]

2.  Sous **Targets**, cliquez sur le nom de votre projet, développez **Code Signing Identity**, puis sous **Debug**, sélectionnez le profil d'identité de signature du code. En outre, si vous utilisez une version plus récente de XCode, basculez les **Niveaux** de **Basic** à **All** et définissez la ligne **Provisioning Profile** sur le profil de mise à niveau.

	![][32]

3.  Télécharger le Kit de développement logiciel (SDK) Azure Mobile pour iOS. Ouvrez le fichier. zip et faites glisser le dossier WindowsAzureMessaging.Framework dans le dossier Structure de votre projet XCode. Sélectionnez **Copy items in destination group's folder**, puis cliquez sur **OK**.

	![][33]

4.  Dans le fichier AppDelegate.h, ajoutez l'instruction d'importation suivante :

          #import <WindowsAzureMessaging/WindowsAzureMessaging.h>

5.  Dans le fichier AppDelegate.m, ajoutez le code suivant dans la méthode `didFinishLaunchingWithOptions` :

          [[UIApplication sharedApplication] registerForRemoteNotificationTypes: UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];

6.  Dans le même fichier, ajoutez la méthode suivante :

         - (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData *) deviceToken {    
             SBNotificationHub* hub = [[SBNotificationHub alloc] initWithConnectionString:
                                       @"<connection string>" notificationHubPath:@"mynh"];
                
             [hub registerNativeWithDeviceToken:deviceToken tags:nil completion:^(NSError* error) {
                 if (error != nil) {
                     NSLog(@"Error registering for notifications: %@", error);
                 }
            }];
         }

7.  *(facultatif)* À nouveau, dans le même fichier, ajoutez la méthode suivante pour afficher une **UIAlert** si la notification est reçue alors que l'application est active :

         - (void)application:(UIApplication *)application didReceiveRemoteNotification: (NSDictionary *)userInfo {
             NSLog(@"%@", userInfo);
             UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:
             [[userInfo objectForKey:@"aps"] valueForKey:@"alert"] delegate:nil cancelButtonTitle:
             @"OK" otherButtonTitles:nil, nil];
             [alert show];
         }

8.  Exécutez l'application sur votre appareil.

Envoi de notificationsEnvoi de notifications à partir de votre serveur principal
--------------------------------------------------------------------------------

Vous pouvez envoyer des notifications en utilisant Notification Hubs à partir de tous les serveurs principaux à l'aide de l'[interface REST](http://msdn.microsoft.com/fr-fr/library/windowsazure/dn223264.aspx). Dans ce didacticiel, vous envoyez des notifications avec une application console .NET et un Mobile Service à l'aide d'un script Node.

Pour envoyer des notifications en utilisant une application .NET :

1.  Créez une application console Visual C\# :

    ![](./media/notification-hubs-ios-get-started/notification-hub-create-console-app.png)

2.  Ajoutez une référence au Kit de développement logiciel (SDK) Azure Service Bus à l'aide du [package NuGet WindowsAzure.ServiceBus](http://nuget.org/packages/WindowsAzure.ServiceBus/). Dans le menu principal de Visual Studio, cliquez sur **Outils**, sur **Library Package Manager**, puis sur **Console du gestionnaire de package**. Dans la fenêtre de la console, tapez :

         Install-Package WindowsAzure.ServiceBus

    et appuyez sur Entrée.

3.  Ouvrez le fichier Program.cs et ajoutez l'instruction using suivante :

         using Microsoft.ServiceBus.Notifications;

4.  Dans votre classe `Program`, ajoutez la méthode suivante. Remplacez l'espace réservé « nom hub » par le nom du Notification Hub affiché sur le portail sous l'onglet **Notification Hubs** (par exemple, **monnotificationhub2** dans l'exemple précédent) :

         private static async void SendNotificationAsync()
         {
             NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
             var alert = "{ private static async void SendNotificationAsync()
         {
             NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
             var alert = "{\"aps\":{\"alerte\":\"Hello depuis .NET !\"}}";
             await hub.SendAppleNativeNotificationAsync(alert);
         }
        quot;aps private static async void SendNotificationAsync()
         {
             NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
             var alert = "{\"aps\":{\"alerte\":\"Hello depuis .NET !\"}}";
             await hub.SendAppleNativeNotificationAsync(alert);
         }
        quot;:{ private static async void SendNotificationAsync()
         {
             NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
             var alert = "{\"aps\":{\"alerte\":\"Hello depuis .NET !\"}}";
             await hub.SendAppleNativeNotificationAsync(alert);
         }
        quot;alerte private static async void SendNotificationAsync()
         {
             NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
             var alert = "{\"aps\":{\"alerte\":\"Hello depuis .NET !\"}}";
             await hub.SendAppleNativeNotificationAsync(alert);
         }
        quot;: private static async void SendNotificationAsync()
         {
             NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
             var alert = "{\"aps\":{\"alerte\":\"Hello depuis .NET !\"}}";
             await hub.SendAppleNativeNotificationAsync(alert);
         }
        quot;Hello depuis .NET ! private static async void SendNotificationAsync()
         {
             NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
             var alert = "{\"aps\":{\"alerte\":\"Hello depuis .NET !\"}}";
             await hub.SendAppleNativeNotificationAsync(alert);
         }
        quot;}}";
             await hub.SendAppleNativeNotificationAsync(alert);
         }

5.  Ajoutez ensuite la ligne suivante dans votre méthode `Main` :

          SendNotificationAsync();
          Console.ReadLine();

6.  Appuyez sur la touche F5 pour exécuter l'application. Vous devez recevoir une alerte sur votre appareil. Si vous utilisez le Wi-Fi, assurez-vous que votre connexion fonctionne.

Vous trouverez toutes les charges utiles possibles dans le [Guide de programmation des notifications locales et Push](http://developer.apple.com/library/mac/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW1) d'Apple.

Pour envoyer une notification en utilisant un service Mobile Service, suivez les instructions de la rubrique [Prise en main de Mobile Services](/en-us/develop/mobile/tutorials/get-started-ios), puis :

1.  Connectez-vous au [portail de gestion Azure](https://manage.windowsazure.com/) et sélectionnez votre service mobile.

2.  Sélectionnez l'onglet **Scheduler** en haut.

	![][215]

3.  Créez un travail planifié, insérez un nom, puis sélectionnez **On demand**.

	![][216]

4.  Lorsque le travail est créé, cliquez sur son nom. Cliquez ensuite sur l'onglet **Script** dans la barre supérieure.

5.  Insérez le script suivant dans votre fonction de planificateur. Remplacez les espaces réservés par le nom du Notification Hub et la chaîne de connexion pour *DefaultFullSharedAccessSignature* obtenue précédemment. Cliquez sur **Enregistrer**.

		var azure = require('azure');
		var notificationHubService = azure.createNotificationHubService('<Hubname>', '<SAS Full access >');
		notificationHubService.apns.send(
	    	null,
    		{"aps":
        		{
          		"alert": "Hello from Mobile Services!"
        		}
    		},
    		function (error)
    		{
	        	if (!error) {
    	        	console.warn("Notification successful");
        		}
    		}
		);

6.  Cliquez sur **Run Once** sur la barre inférieure. Vous devez recevoir une alerte sur votre appareil.

Étapes suivantes
----------------

Dans cet exemple simple, vous avez envoyé des notifications à tous vos appareils iOS. Afin de cibler des utilisateurs spécifiques, consultez le didacticiel [Utilisation des Notification Hubs pour envoyer des notifications Push aux utilisateurs](/en-us/manage/services/notification-hubs/notify-users-aspnet), et, si vous souhaitez segmenter vos utilisateurs par groupes d'intérêt, vous pouvez lire [Utilisation des Notification Hubs pour diffuser les dernières nouvelles](/en-us/manage/services/notification-hubs/breaking-news-dotnet). Pour plus d'informations sur l'utilisation de Notification Hubs, consultez les pages [Vue d'ensemble des concentrateurs de notification](http://msdn.microsoft.com/fr-fr/library/jj927170.aspx) et [Procédures Notification Hubs pour iOS](http://msdn.microsoft.com/fr-fr/library/jj927168.aspx).

<!-- Anchors. -->
[Generate the certificate signing request]: #certificates
[Register your app and enable push notifications]: #register
[Create a provisioning profile for the app]: #profile
[Configure your Notification Hub]: #configure-hub
[Connecting your app to the Notification Hub]: #connecting-app
[Send notifications from your back-end]: #send
[Next Steps]:#next-steps

<!-- Images. -->
[5]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-step5.png
[6]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-step6.png
[7]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-step7.png

[9]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-step9.png
[10]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-step10.png


[18]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-step18.png


[105]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-05.png
[106]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-06.png
[107]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-07.png
[108]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-08.png
[109]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-09.png
[110]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-10.png
[111]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-11.png
[112]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-12.png
[113]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-13.png
[114]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-14.png
[115]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-15.png
[116]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-16.png

[118]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-18.png
[119]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-19.png

[120]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-20.png
[121]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-21.png
[122]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-22.png
[123]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-23.png
[124]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-24.png
[125]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-25.png
[126]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-26.png

[27]: ./media/notification-hubs-ios-get-started/notification-hub-create-from-portal.png
[28]: ./media/notification-hubs-ios-get-started/notification-hub-create-from-portal2.png
[29]: ./media/notification-hubs-ios-get-started/notification-hub-select-from-portal.png
[210]: ./media/notification-hubs-ios-get-started/notification-hub-select-from-portal2.png
[211]: ./media/notification-hubs-ios-get-started/notification-hub-configure-ios.png
[212]: ./media/notification-hubs-ios-get-started/notification-hub-connection-strings.png


[213]: ./media/notification-hubs-ios-get-started/notification-hub-create-console-app.png



[215]: ./media/notification-hubs-ios-get-started/notification-hub-scheduler1.png
[216]: ./media/notification-hubs-ios-get-started/notification-hub-scheduler2.png


[31]: ./media/notification-hubs-ios-get-started/notification-hub-create-ios-app.png
[32]: ./media/notification-hubs-ios-get-started/notification-hub-create-ios-app2.png
[33]: ./media/notification-hubs-ios-get-started/notification-hub-create-ios-app3.png


[B102]: ./media/notification-hubs-ios-get-started/notification-hub-clone-mobile-services-ios-push-02.png
[B103]: ./media/notification-hubs-ios-get-started/notification-hub-clone-mobile-services-ios-push-03.png
[B104]: ./media/notification-hubs-ios-get-started/notification-hub-clone-mobile-services-ios-push-04.png
[B105]: ./media/notification-hubs-ios-get-started/notification-hub-clone-mobile-services-ios-push-05.png
[B106]: ./media/notification-hubs-ios-get-started/notification-hub-clone-mobile-services-ios-push-06.png
[B107]: ./media/notification-hubs-ios-get-started/notification-hub-clone-mobile-services-ios-push-07.png
[B108]: ./media/notification-hubs-ios-get-started/notification-hub-clone-mobile-services-ios-push-08.png
[B110]: ./media/notification-hubs-ios-get-started/notification-hub-clone-mobile-services-ios-push-10.png
[B111]: ./media/notification-hubs-ios-get-started/notification-hub-clone-mobile-services-ios-push-11.png
[B9]: ./media/notification-hubs-ios-get-started/notification-hub-clone-mobile-services-ios-push-step9.png
[B10]: ./media/notification-hubs-ios-get-started/notification-hub-clone-mobile-services-ios-push-step10.png


<!-- URLs. -->
[Mobile Services iOS SDK]: http://go.microsoft.com/fwLink/?LinkID=266533
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253

[Get started with Mobile Services]: /en-us/develop/mobile/tutorials/get-started-ios
[Azure Management Portal]: https://manage.windowsazure.com/
[Notification Hubs Guidance]: http://msdn.microsoft.com/fr-fr/library/jj927170.aspx
[Notification Hubs How-To for iOS]: http://msdn.microsoft.com/fr-fr/library/jj927168.aspx
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[iOS Provisioning Portal]: http://go.microsoft.com/fwlink/p/?LinkId=272456

[Use Notification Hubs to push notifications to users]: /en-us/manage/services/notification-hubs/notify-users-aspnet
[Use Notification Hubs to send breaking news]: /en-us/manage/services/notification-hubs/breaking-news-dotnet

[Local and Push Notification Programming Guide]: http://developer.apple.com/library/mac/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW1