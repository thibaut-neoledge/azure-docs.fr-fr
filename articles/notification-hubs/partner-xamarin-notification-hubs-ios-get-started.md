<properties
	pageTitle="Prise en main de Notification Hubs pour les applications Xamarin iOS | Microsoft Azure"
	description="Ce didacticiel vous apprend à utiliser Azure Notification Hubs pour envoyer des notifications Push vers une application Xamarin iOS."
	services="notification-hubs"
	documentationCenter="xamarin"
	authors="ysxu"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="notification-hubs"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-xamarin-ios"
	ms.devlang="dotnet"
	ms.topic="hero-article"
	ms.date="07/28/2015"
	ms.author="yuaxu"/>

# Prise en main de Notification Hubs

[AZURE.INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

##Vue d’ensemble

Ce didacticiel vous montre comment utiliser Azure Notification Hubs pour envoyer des notifications Push vers une application iOS. Vous allez créer une application Xamarin.iOS vide qui reçoit des notifications Push à l’aide du service de notification Push Apple (APN, Apple Push Notification). Une fois l’opération terminée, vous pouvez utiliser votre hub de notification pour diffuser des notifications Push sur tous les appareils exécutant votre application. Le code finalisé est disponible dans l'exemple [Application NotificationHubs][GitHub].

Ce didacticiel présente un scénario de diffusion simple utilisant les Notification Hubs.

##Configuration requise

Ce didacticiel requiert les éléments suivants :

+ [XCode 6.0][Install Xcode]
+ Un appareil compatible iOS 7.0 (ou version ultérieure)
+ Un abonnement au programme pour développeurs iOS
+ [Xamarin.iOS]
+ [Composant Azure Mobile Services]

   >[AZURE.NOTE]En raison des exigences de configuration requise pour les notifications Push, vous devez déployer et tester les notifications Push sur un appareil compatible iOS (iPhone ou iPad) au lieu d’un simulateur.

Vous devez suivre ce didacticiel avant de pouvoir suivre tous les autres didacticiels Notification Hubs pour les applications Xamarin.iOS.

> [AZURE.IMPORTANT]Pour suivre ce didacticiel, vous avez besoin d'un compte Azure actif. Si vous ne possédez pas de compte, vous pouvez créer un compte d'évaluation gratuit en quelques minutes. Pour plus d'informations, consultez la page [Version d'évaluation gratuite d'Azure](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A643EE910&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Ffr-FR%2Fdocumentation%2Farticles%2Fpartner-xamarin-notification-hubs-ios-get-started).

Le service de notification Push Apple utilise des certificats pour authentifier votre service mobile. Suivez ces instructions pour créer les certificats requis et les télécharger dans votre service mobile. Pour obtenir la documentation complète des fonctionnalités APNS, consultez la page [Service de notification Push Apple].


##<a name="certificates"></a>Génération du fichier de demande de signature de certificat

Vous devez d'abord générer le fichier de demande de signature de certificat (CSR, Certificate Signing Request), qu'Apple utilise pour générer un certificat signé.

1. Dans le dossier Utilitaires, exécutez l'outil Trousseaux d'accès.

2. Cliquez sur **Trousseaux d’accès**, développez **Assistant de certification**, puis cliquez sur **Demander un certificat à une autorité de certification**.

  	![][5]

3. Sélectionnez **Adresse électronique de l'utilisateur**, entrez les valeurs **Nom commun** et **Adresse électronique de l'AC**, vérifiez que la case à cocher **Enregistrée sur disque** est activée, puis cliquez sur **Continuer**.

  	![][6]

4. Saisissez un nom pour le fichier de demande de signature de certificat dans **Enregistrer sous**, sélectionnez l’emplacement dans **Où**, puis cliquez sur **Enregistrer**.

  	![][7]

  	Cette opération enregistre le fichier CSR dans l’emplacement sélectionné. L’emplacement par défaut est sur le bureau. Notez l’emplacement que vous avez sélectionné pour ce fichier.

Ensuite, inscrivez votre application auprès d'Apple, activez les notifications Push, puis téléchargez ce fichier de demande de signature de certificat exporté pour créer un certificat Push.

##<a name="register"></a>Inscription de votre application pour les notifications Push

Pour pouvoir envoyer des notifications Push vers une application iOS depuis Mobile Services, vous devez inscrire votre application auprès d’Apple, ainsi qu’aux notifications Push.

1. Si vous n’avez pas encore inscrit votre application, accédez au <a href="http://go.microsoft.com/fwlink/p/?LinkId=272456" target="_blank">Portail de mise en service iOS</a> du centre de développement Apple, connectez-vous avec votre ID Apple, cliquez sur **Identifiers**, sur **App IDs**, et enfin sur le signe **+** pour inscrire une nouvelle application.

   	![][105]

2. Saisissez le nom de votre application dans **Description** et une valeur dans **Bundle Identifier**, activez l’option **Notifications push** dans la section **App Services**, puis cliquez sur **Continue**.

   	![][106]

   	![][107]

   	![][108]


	L’ID de votre application est généré et vous êtes invité à envoyer les informations. Cliquez sur **Envoyer**.

   	![][109]

	Lorsque vous avez cliqué sur **Submit**, le message **Registration complete** s'affiche, comme montré ci-dessous. Cliquez sur **Done**.

   	![][110]

	> [AZURE.NOTE]Si vous choisissez d’entrer une valeur **Bundle Identifier** autre que **MobileServices.Quickstart**, vous devez également mettre à jour la valeur d’identificateur de lot dans votre projet Xcode.

3. Recherchez l’ID de l’application que vous venez de créer, puis cliquez sur la ligne correspondante.

   	![][111]

	Le fait de cliquer sur l’ID de l’application affiche des informations détaillées de cette application et de l’ID d’application.

   	![][112]

   	![][113]

4. Cliquez sur **Edit**, faites défiler l’écran jusqu’en bas, puis cliquez sur le bouton **Create Certificate…** sous la section **Development Push SSL Certificate**.

   	![][114]

	Cette opération permet d’afficher l’Assistant d’ajout de certificat iOS.

   	![][115]

	> [AZURE.NOTE]Ce didacticiel utilise un certificat de développement. Le même processus est utilisé lors de l’inscription d’un certificat de production. Assurez-vous simplement que vous avez défini le même type de certificat lorsque vous avez téléchargé le certificat vers Mobile Services.

5. Cliquez sur **Choose File**, accédez à l’emplacement où vous avez enregistré le fichier de demande de signature de certificat que vous avez créé lors de la première tâche, puis cliquez **Generate**.

  	![][116]

6. Une fois le certificat créé par le portail, cliquez sur le bouton **Download**, puis sur **Done**.

  	![][118]

  	![][119]

   	Ceci entraîne le téléchargement du certificat de signature et l'enregistre sur votre ordinateur dans le dossier **Téléchargements**.

  	![][9]

    > [AZURE.NOTE]Par défaut, le fichier téléchargé est un certificat de développement nommé **aps\_development.cer**.

7. Double-cliquez sur le certificat Push téléchargé **aps\_development.cer**.

	Ceci installe le nouveau certificat dans le Trousseau d'accès, comme indiqué ci-dessous :

   	![][10]

	> [AZURE.NOTE]Il se peut que le nom de votre certificat soit différent. Il portera toutefois le préfixe <strong>Apple Development iOS Push Notification Services</strong>.

	Plus tard, vous devrez utiliser ce certificat pour générer un fichier .p12 et le télécharger sur votre concentrateur de notification pour activer les notifications Push avec APN.

##<a name="profile"></a>Créer un profil de mise en service pour l'application

1. Une fois de retour dans le <a href="http://go.microsoft.com/fwlink/p/?LinkId=272456" target="_blank">Portail de mise en service iOS</a>, sélectionnez **Provisioning Profiles**, **All**, puis cliquez sur le bouton **+** pour créer un profil. Ainsi, l’Assistant **Ajout du profil de mise en service d’iOS** s’affiche.

   	![][120]

2. Sélectionnez **iOS App Development** sous **Development** en tant que type de profil de mise en service, puis cliquez sur **Continue**.

   	![][121]

3. Sélectionnez l’ID de l’application de démarrage rapide Mobile Services dans la liste déroulante **App ID**, puis cliquez sur **Continue**.

   	![][122]

4. Dans l’écran **Select certificates**, sélectionnez le certificat créé plus tôt, puis cliquez sur **Continue**.

   	![][123]

5. Sélectionnez les appareils à utiliser pour le test, puis cliquez sur **Continue**.

   	![][124]

6. Enfin, sélectionnez un nom pour le profil dans **Profile Name**, cliquez sur **Generate**, puis sur **Done**.

   	![][125]

   	![][126]

  	Ceci entraîne la création d'un profil de mise en service.

7. Dans Xcode, ouvrez l’Organisateur, sélectionnez la vue Devices, puis **Provisioning Profiles** dans la section **Library** du volet de gauche et importez le profil d’approvisionnement que vous venez de créer.

8. Sur la gauche, sélectionnez votre appareil et importez à nouveau le profil de mise en service.

9. Dans Trousseaux d'accès, cliquez avec le bouton droit sur le nouveau certificat, cliquez sur **Exporter**, tapez un nom pour votre certificat, sélectionnez le format **.p12**, puis cliquez sur **Enregistrer**.

   	![][18]

  	Notez le nom du fichier et l'emplacement du certificat exporté.

Ceci vérifie que le projet Xcode utilise le nouveau profil pour la signature du code. Ensuite, vous devez télécharger le certificat vers votre concentrateur de notification.

##<a name="configure-hub"></a>Configuration de votre hub de notification

1. Connectez-vous au [portail Azure] et cliquez sur **+NOUVEAU** en bas de l’écran.

2. Cliquez sur **Services d’application**, sur **Service Bus**, sur **Notification Hub**, puis cliquez sur **Création rapide**.

   	![][27]

3. Tapez un nom pour votre hub de notification, sélectionnez la région souhaitée, puis cliquez sur **Créer un hub de notification**.

   	![][28]

4. Cliquez sur l’espace de noms que vous venez de créer (en général ***nom du hub de notification*-ns**), puis cliquez sur l’onglet **Configurer** en haut de l’écran.

   	![][29]

5. Cliquez sur l’onglet **Notifications Hubs** en haut de la page, puis cliquez sur le hub de notification que vous venez de créer.

   	![][210]

6. Cliquez sur l'onglet **Configurer** en haut de la page, puis cliquez sur **Télécharger** dans les paramètres de notification d'Apple. Sélectionnez ensuite le certificat **.p12** exporté précédemment, ainsi que le mot de passe du certificat. Assurez-vous de déterminer si vous souhaitez utiliser la **Production** (si vous souhaitez envoyer des notifications Push aux utilisateurs qui ont acheté votre application depuis le magasin) ou le service Push **Bac à sable (Sandbox)** (au cours du développement).

   	![][211]

7. Cliquez sur l'onglet **Tableau de bord** dans la partie supérieure, puis cliquez sur **Informations de connexion**. Notez les deux chaînes de connexion.

   	![][212]

Votre Notification Hub est configuré pour APNS, et vous disposez des chaînes de connexion pour inscrire votre application et envoyer des notifications.

##<a name="connecting-app"></a>Connexion de votre application au hub de notification

### Création d'un projet

1. Dans Xamarin Studio, créez un projet iOS et sélectionnez le modèle **Unified API** > **Single View Application**.

   	![][31]

2. Ajoutez une référence au composant Azure Messaging. Dans la vue Solution, cliquez avec le bouton droit sur le dossier **Components** de votre projet, puis choisissez **Get More Components**. Recherchez le composant **Azure Messaging** et ajoutez-le à votre projet.

3. Dans **AppDelegate.cs**, ajoutez l'instruction using suivante :

    using WindowsAzure.Messaging;

4. Déclarez une instance de **SBNotificationHub** :

		private SBNotificationHub Hub { get; set; }

5. Créez une classe **Constants.cs** avec les variables suivantes :

        // Azure app-specific connection string and hub path
        public const string ConnectionString = "<Azure connection string>";
        public const string NotificationHubPath = "<Azure hub path>";


6. Dans **AppDelegate.cs**, mettez à jour **FinishedLaunching()** afin qu’il corresponde à ce qui suit :

        public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
        {
            UIRemoteNotificationType notificationTypes = UIRemoteNotificationType.Alert |
                UIRemoteNotificationType.Badge | UIRemoteNotificationType.Sound;
            UIApplication.SharedApplication.RegisterForRemoteNotificationTypes(notificationTypes);

            return true;
        }

7. Remplacez la méthode **RegisteredForRemoteNotifications()** dans **AppDelegate.cs** :

        public override void RegisteredForRemoteNotifications(UIApplication application, NSData deviceToken)
        {
            Hub = new SBNotificationHub(Constants.ConnectionString, Constants.NotificationHubPath);

            Hub.UnregisterAllAsync (deviceToken, (error) => {
                if (error != null)
                {
                    Console.WriteLine("Error calling Unregister: {0}", error.ToString());
                    return;
                }

                NSSet tags = null; // create tags if you want
                Hub.RegisterNativeAsync(deviceToken, tags, (errorCallback) => {
                    if (errorCallback != null)
                        Console.WriteLine("RegisterNativeAsync error: " + errorCallback.ToString());
                });
            });
        }

8. Remplacez la méthode **ReceivedRemoteNotification()** dans **AppDelegate.cs** :

        public override void ReceivedRemoteNotification(UIApplication application, NSDictionary userInfo)
        {
            ProcessNotification(userInfo, false);
        }

9. Créez la méthode **ProcessNotification()** suivante dans **AppDelegate.cs** :

        void ProcessNotification(NSDictionary options, bool fromFinishedLaunching)
        {
            // Check to see if the dictionary has the aps key.  This is the notification payload you would have sent
            if (null != options && options.ContainsKey(new NSString("aps")))
            {
                //Get the aps dictionary
                NSDictionary aps = options.ObjectForKey(new NSString("aps")) as NSDictionary;

                string alert = string.Empty;

                //Extract the alert text
                // NOTE: If you're using the simple alert by just specifying
                // "  aps:{alert:"alert msg here"}  ", this will work fine.
                // But if you're using a complex alert with Localization keys, etc.,
                // your "alert" object from the aps dictionary will be another NSDictionary.
                // Basically the JSON gets dumped right into a NSDictionary,
                // so keep that in mind.
                if (aps.ContainsKey(new NSString("alert")))
                    alert = (aps [new NSString("alert")] as NSString).ToString();

                //If this came from the ReceivedRemoteNotification while the app was running,
                // we of course need to manually process things like the sound, badge, and alert.
                if (!fromFinishedLaunching)
                {
                    //Manually show an alert
                    if (!string.IsNullOrEmpty(alert))
                    {
                        UIAlertView avAlert = new UIAlertView("Notification", alert, null, "OK", null);
                        avAlert.Show();
                    }
                }
            }
        }

    > [AZURE.NOTE]Vous pouvez choisir de remplacer **FailedToRegisterForRemoteNotifications()** pour gérer les situations, par exemple, l’absence de connexion réseau, etc.


10. Exécutez l'application sur votre appareil.

##<a name="send"></a>Envoi de notifications à partir de votre serveur principal

Vous pouvez envoyer des notifications en utilisant Notification Hubs à partir d’un serveur principal utilisant notre <a href="http://msdn.microsoft.com/library/windowsazure/dn223264.aspx">interface REST</a>. Dans ce didacticiel, vous allez envoyer des notifications en utilisant une application console .NET et un service mobile à l’aide d’un script de noeud.

Pour envoyer des notifications en utilisant une application .NET :

1. Créez une application console Visual C# :

   	![][213]

2. Ajoutez une référence au Kit de développement logiciel (SDK) Azure Service Bus est ajoutée à l’aide du <a href="http://nuget.org/packages/WindowsAzure.ServiceBus/">package NuGet WindowsAzure.ServiceBus</a>. Dans le menu principal de Visual Studio, cliquez sur **Outils**, **Library Package Manager**, puis sur **Console du gestionnaire de package**. Puis, dans la fenêtre de la console, saisissez le code suivant et appuyez sur Entrée :

        Install-Package WindowsAzure.ServiceBus

2. Ouvrez le fichier Program.cs et ajoutez l’instruction using suivante :

        using Microsoft.ServiceBus.Notifications;

3. Dans votre classe `Program`, ajoutez la méthode suivante :

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            var alert = "{"aps":{"alert":"Hello from .NET!"}}";
            await hub.SendAppleNativeNotificationAsync(alert);
        }

4. Ajoutez ensuite les lignes suivantes dans votre méthode `Main` :

         SendNotificationAsync();
		 Console.ReadLine();

5. Appuyez sur la touche F5 pour exécuter l'application. Vous devez recevoir une alerte sur votre appareil. Si vous utilisez le Wi-Fi, assurez-vous que votre connexion fonctionne.

Vous trouverez toutes les charges utiles possibles dans le [Guide de programmation des notifications locales et Push] d'Apple.

Pour envoyer une notification en utilisant un service mobile, suivez les instructions de la rubrique [Prise en main de Mobile Services], puis :

1. Connectez-vous au [portail Azure] et sélectionnez votre service mobile.

2. Sélectionnez l’onglet **Planificateur** dans la partie supérieure.

   	![][215]

3. Créez un travail planifié, insérez un nom, puis sélectionnez **On demand**.

   	![][216]

4. Lorsque le travail est créé, cliquez sur son nom. Cliquez ensuite sur l’onglet **Script** dans la barre supérieure.

5. Insérez le script suivant dans votre fonction Scheduler. Remplacez les espaces réservés par le nom de votre concentrateur de notification et la chaîne de connexion pour *DefaultFullSharedAccessSignature* obtenue précédemment. Cliquez sur **Enregistrer**.

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


6. Cliquez sur **Exécuter une fois** sur la barre inférieure. Vous devez recevoir une alerte sur votre appareil.

## <a name="next-steps"> </a>Étapes suivantes

Dans cet exemple simple, vous avez envoyé des notifications à tous vos appareils iOS. Pour cibler certains utilisateurs, reportez-vous au didacticiel [Utilisation des Notification Hubs pour envoyer des notifications Push aux utilisateurs]. Pour segmenter vos utilisateurs par groupes d'intérêt, consultez la page [Utilisation des Notification Hubs pour diffuser les dernières nouvelles]. Pour plus d’informations sur l’utilisation de Notification Hubs, consultez les pages [Guide de Notifications Hubs] et [Procédures Notification Hubs pour iOS].

<!-- Anchors. -->
[Generate the certificate signing request]: #certificates
[Register your app and enable push notifications]: #register
[Create a provisioning profile for the app]: #profile
[Configure your Notification Hub]: #configure-hub
[Connecting your app to the Notification Hub]: #connecting-app
[Send notifications from your back-end]: #send
[Next Steps]: #next-steps

<!-- Images. -->
[5]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-step5.png
[6]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-step6.png
[7]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-step7.png

[9]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-step9.png
[10]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-step10.png
[18]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-step18.png
[105]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-05.png
[106]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-06.png
[107]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-07.png
[108]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-08.png
[109]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-09.png
[110]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-10.png
[111]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-11.png
[112]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-12.png
[113]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-13.png
[114]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-14.png
[115]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-15.png
[116]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-16.png

[118]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-18.png
[119]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-19.png

[120]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-20.png
[121]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-21.png
[122]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-22.png
[123]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-23.png
[124]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-24.png
[125]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-25.png
[126]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-26.png

[27]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-create-from-portal.png
[28]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-create-from-portal2.png
[29]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-select-from-portal.png
[210]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-select-from-portal2.png
[211]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-configure-ios.png
[212]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-connection-strings.png


[213]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-create-console-app.png



[215]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-scheduler1.png
[216]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-scheduler2.png


[31]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-create-ios-app.png




<!-- URLs. -->
[Mobile Services iOS SDK]: http://go.microsoft.com/fwLink/?LinkID=266533
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253

[Prise en main de Mobile Services]: /develop/mobile/tutorials/get-started-xamarin-ios
[portail Azure]: https://manage.windowsazure.com/
[Guide de Notifications Hubs]: http://msdn.microsoft.com/library/jj927170.aspx
[Procédures Notification Hubs pour iOS]: http://msdn.microsoft.com/library/jj927168.aspx
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[iOS Provisioning Portal]: http://go.microsoft.com/fwlink/p/?LinkId=272456

[Utilisation des Notification Hubs pour envoyer des notifications Push aux utilisateurs]: /manage/services/notification-hubs/notify-users-aspnet
[Utilisation des Notification Hubs pour diffuser les dernières nouvelles]: /manage/services/notification-hubs/breaking-news-dotnet

[Guide de programmation des notifications locales et Push]: http://developer.apple.com/library/mac/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW1
[Service de notification Push Apple]: http://go.microsoft.com/fwlink/p/?LinkId=272584

[Composant Azure Mobile Services]: http://components.xamarin.com/view/azure-mobile-services/
[GitHub]: http://go.microsoft.com/fwlink/p/?LinkId=331329
[Xamarin.iOS]: http://xamarin.com/download
[WindowsAzure.Messaging]: https://github.com/infosupport/WindowsAzure.Messaging.iOS

<!---HONumber=Oct15_HO3-->