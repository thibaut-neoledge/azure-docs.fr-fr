<properties linkid="develop-notificationhubs-tutorials-get-started-xamarin-ios" urlDisplayName="Get Started" pageTitle="Get Started with Notification Hubs for Xamarin iOS apps" metaKeywords="" description="Learn how to use Azure Notification Hubs to send push notifications to a Xamarin iOS application." metaCanonical="" disqusComments="0" umbracoNaviHide="1" services="mobile-services,notification-hubs" documentationCenter="" title="Get started with Notification Hubs" authors="donnam" manager="dwrede" />

<tags ms.service="notification-hubs" ms.workload="mobile" ms.tgt_pltfrm="mobile-xamarin-ios" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="donnam"></tags>

# Prise en main de Notification Hubs

<div class="dev-center-tutorial-selector sublanding"><a href="/fr-fr/documentation/articles/notification-hubs-windows-store-dotnet-get-started/" title="Windows Universal">Windows Universal</a><a href="/fr-fr/documentation/articles/notification-hubs-windows-phone-get-started/" title="Windows Phone">Windows Phone</a><a href="/fr-fr/documentation/articles/notification-hubs-ios-get-started/" title="iOS">iOS</a><a href="/fr-fr/documentation/articles/notification-hubs-android-get-started/" title="Android">Android</a><a href="/fr-fr/documentation/articles/notification-hubs-kindle-get-started/" title="Kindle">Kindle</a><a href="/fr-fr/documentation/articles/partner-xamarin-notification-hubs-ios-get-started/" title="Xamarin.iOS" class="current">Xamarin.iOS</a><a href="/fr-fr/documentation/articles/partner-xamarin-notification-hubs-android-get-started/" title="Xamarin.Android">Xamarin.Android</a></div>

Cette rubrique vous indique comment utiliser Azure Notification Hubs pour envoyer des notifications Push à une application iOS.
Dans ce didacticiel, vous créez une application Xamarin.iOS vide qui reçoit des notifications Push à l'aide du service Apple Push Notification (APN). Une fois la création terminée, vous pouvez diffuser des notifications Push sur tous les appareils sur lesquels votre application est installée au moyen de votre concentrateur de notification. Le code finalisé est disponible dans l'exemple [Application NotificationHubs][].

Ce didacticiel vous familiarise avec les étapes de base permettant d'activer les notifications Push :

1.  [Génération de la demande de signature de certificat][]
2.  [Inscription de votre application et activation des notifications Push][]
3.  [Création d'un profil de mise en service pour l'application][]
4.  [Configuration de votre concentrateur de notification][]
5.  [Connexion de votre application au concentrateur de notification][]
6.  [Envoi de notifications à partir de votre serveur principal][]

Ce didacticiel présente un scénario de diffusion simple utilisant les concentrateurs de notification. Ce didacticiel requiert les composants requis suivants :

-   [XCode 5.0][]
-   Un appareil compatible iOS 5.0 (ou version ultérieure)
-   Un abonnement au programme pour développeurs iOS
-   [Xamarin.iOS][1]
-   [Composant Azure Mobile Services][]

   <div class="dev-callout"><b>Remarque</b><br /> 
   <p>En raison de la configuration requise pour les notifications Push, vous devez d&eacute;ployer et tester les notifications Push sur un appareil compatible iOS (iPhone ou iPad) au lieu d'un simulateur.</p>
   </div>

Vous devez suivre ce didacticiel avant de pouvoir suivre tous les autres didacticiels Notification Hubs pour les applications Xamarin.iOS.

<div class="dev-callout"><strong>Remarque</strong> 
<p>Pour suivre ce didacticiel, vous avez besoin d'un compte Azure actif. Si vous ne poss&eacute;dez pas de compte, vous pouvez cr&eacute;er un compte d'&eacute;valuation gratuit en quelques minutes. Pour plus d'informations, consultez la page <a href="http://www.windowsazure.com/fr-fr/pricing/free-trial/?WT.mc_id=A643EE910&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Ffr-fr%2Fmanage%2Fservices%2Fnotification-hubs%2Fgetting-started-xamarin-ios%2F" target="_blank">Version d'&eacute;valuation gratuite d'Azure</a>.</p>
</div>

Le service de notification Push Apple (APNS) utilise des certificats pour authentifier votre service mobile. Suivez ces instructions pour créer les certificats requis et les télécharger dans votre service mobile. Pour obtenir la documentation complète des fonctionnalités APNS, consultez la page [Service de notification Push Apple][].

## <a name="certificates"></a><span class="short-header">Génération d'un fichier CSR</span>Génération d'un fichier de demande de signature de certificat (CSR)

Vous devez d'abord générer le fichier de demande de signature de certificat (CSR, Certificate Signing Request), qu'Apple utilise pour générer un certificat signé.

1.  Dans le dossier Utilitaires, exécutez l'outil Trousseaux d'accès.

2.  Cliquez sur **Trousseaux d'accès**, développez **Assistant de certification**, puis cliquez sur **Demander un certificat à une autorité de certification**.

    ![][]

3.  Sélectionnez **Adresse électronique de l'utilisateur**, entrez les valeurs **Nom commun** et **Adresse électronique de l'AC**, vérifiez que la case à cocher **Enregistrée sur disque** est activée, puis cliquez sur **Continuer**.

    ![][2]

4.  Entrez un nom pour le fichier de demande de signature de certificat dans **Enregistrer sous**, sélectionnez l'emplacement dans **Où**, puis cliquez sur **Enregistrer**.

    ![][3]

    Ceci enregistre le fichier de demande de signature de certificat dans l'emplacement sélectionné, l'emplacement par défaut étant situé sur le Bureau. Notez l'emplacement sélectionné pour ce fichier.

Ensuite, inscrivez votre application auprès d'Apple, activez les notifications Push, puis téléchargez ce fichier de demande de signature de certificat exporté pour créer un certificat Push.

## <a name="register"></a><span class="short-header">Inscription de votre application</span>Inscription de votre application pour les notifications Push

Pour pouvoir envoyer des notifications Push vers une application iOS à partir des services mobiles, vous devez inscrire votre application auprès d'Apple, ainsi qu'aux notifications Push.

1.  Si vous n'avez pas déjà inscrit votre application, accédez au [Portail de mise en service iOS][] du centre de développement Apple, connectez-vous avec votre ID Apple, cliquez sur **Identifiers**, sur **App IDs**, puis sur le signe **+** pour inscrire une nouvelle application.

    ![][4]

2.  Entrez le nom de votre application dans **Description**, entrez une valeur dans **Bundle Identifier**, activez l'option « Push Notifications » dans la section « App Services », puis cliquez sur **Continue**.

    ![][5]

    ![][6]

    ![][7]

    Ceci génère l'ID de votre application et vous demande d'envoyer les informations. Cliquez sur **Submit**.

    ![][8]

    Lorsque vous avez cliqué sur **Submit**, le message **Registration complete** s'affiche, comme montré ci-dessous. Cliquez sur **Done**.

    ![][9]

    <div class="dev-callout"><b>Remarque</b>
<p>Si vous choisissez d'entrer une valeur <strong>Bundle Identifier</strong> autre que <b>MobileServices.Quickstart</b>, vous devez &eacute;galement mettre &agrave; jour la valeur d'identificateur de lot dans votre projet Xcode.</p>
 </div>

3.  Recherchez l'ID de l'application que vous venez de créer, puis cliquez sur sa ligne.

    ![][10]

    Le fait de cliquer sur l'ID de l'application affiche les informations détaillées de cette application et de l'ID d'application.

    ![][11]

    ![][12]

4.  Cliquez sur **Edit**, faites défiler jusqu'au bas de l'écran, puis cliquez sur le bouton **Create Certificate…** sous la section **Development Push SSL Certificate**.

    ![][13]

    Ceci affiche l'Assistant « Add iOS Certificate ».

    ![][14]

    <div class="dev-callout"><b>Remarque</b>
<p>Ce didacticiel utilise un certificat de d&eacute;veloppement. Le m&ecirc;me processus est utilis&eacute; lors de l'inscription d'un certificat de production. Assurez-vous simplement que vous avez d&eacute;fini le m&ecirc;me type de certificat lorsque vous avez t&eacute;l&eacute;charg&eacute; le certificat vers Mobile Services.</p>
</div>

5.  Cliquez sur **Choose File**, accédez à l'emplacement où vous avez enregistré le fichier de demande de signature de certificat que vous avez créé lors de la première tâche, puis cliquez **Generate**.

    ![][15]

6.  Une fois le certificat créé par le portail, cliquez sur le bouton **Download**, puis sur **Done**.

    ![][16]

    ![][17]

    Ceci entraîne le téléchargement du certificat de signature et l'enregistre sur votre ordinateur dans le dossier **Téléchargements**.

    ![][18]

    <div class="dev-callout"><b>Remarque</b>
<p>Par d&eacute;faut, le fichier t&eacute;l&eacute;charg&eacute; est un certificat de d&eacute;veloppement nomm&eacute; <strong>aps_development.cer</strong>.</p>
</div>

7.  Double-cliquez sur le certificat Push téléchargé **aps\_development.cer**.

    Ceci installe le nouveau certificat dans le Trousseau d'accès, comme indiqué ci-dessous :

    ![][19]

    > [WACOM.NOTE]
    > Le nom de votre certificat peut être différent, mais son préfixe sera **Apple Development iOS Push Notification Services:**.

    Plus tard, vous devrez utiliser ce certificat pour générer un fichier .p12 et le télécharger sur votre concentrateur de notification pour activer les notifications Push avec APNS.

## <a name="profile"></a><span class="short-header">Mise en service de l'application</span>Création du profil de mise en service de l'application

1.  Une fois de retour sur le [Portail de mise en service iOS][Portail de mise en service iOS], sélectionnez **Provisioning Profiles**, **All**, puis cliquez sur le signe **+** pour créer un profil. Ainsi, l'Assistant **Add iOS Provisioning Profile** s'affiche.

    ![][20]

2.  Sélectionnez **iOS App Development** sous **Development** en tant que type de profil de mise en service, puis cliquez sur **Continue**.

    ![][21]

3.  Ensuite, sélectionnez l'ID de l'application de démarrage rapide Mobile Services dans la liste déroulante **App ID**, puis cliquez sur **Continue**.

    ![][22]

4.  Dans l'écran **Select certificates**, sélectionnez le certificat créé plus tôt, puis cliquez sur **Continue**.

    ![][23]

5.  Ensuite, sélectionnez les appareils (**Devices**) à utiliser pour le test, puis cliquez sur **Continue**.

    ![][24]

6.  Enfin, sélectionnez un nom pour le profil dans **Profile Name**, cliquez sur **Generate**, puis sur **Done**.

    ![][25]

    ![][26]

    Ceci entraîne la création d'un profil de mise en service.

7.  Dans Xcode, ouvrez l'Organisateur, sélectionnez la vue Devices, puis **Provisioning Profiles** dans la section **Library** du volet de gauche et importez le profil de mise en service que vous venez de créer.

8.  Sur la gauche, sélectionnez votre appareil et importez à nouveau le profil de mise en service.

9.  Dans Trousseaux d'accès, cliquez avec le bouton droit sur le nouveau certificat, cliquez sur **Exporter**, tapez un nom pour votre certificat, sélectionnez le format **.p12**, puis cliquez sur **Enregistrer**.

    ![][27]

    Notez le nom du fichier et l'emplacement du certificat exporté.

Ceci vérifie que le projet Xcode utilise le nouveau profil pour la signature du code. Ensuite, vous devez télécharger le certificat vers votre concentrateur de notification.

## <a name="configure-hub"></a><span class="short-header">Configuration de votre concentrateur de notification</span>Configuration de votre concentrateur de notification

1.  Connectez-vous au [portail de gestion Azure][] et cliquez sur **+NOUVEAU** en bas de l'écran.

2.  Cliquez sur **Services d'application**, puis sur **Service Bus**, **Concentrateur de notification**, **Création rapide**.

    ![][28]

3.  Tapez un nom pour votre concentrateur de notification, sélectionnez la région souhaitée, puis cliquez sur **Créer un concentrateur de notification**.

    ![][29]

4.  Cliquez sur l'espace de noms que vous venez de créer (en général ***nom du concentrateur de notification*-ns**), puis cliquez sur l'onglet **Configurer** en haut.

    ![][30]

5.  Cliquez sur l'onglet **Concentrateurs de notification** en haut de la page, puis cliquez sur celui que vous venez de créer.

    ![][31]

6.  Cliquez sur l'onglet **Configurer** en haut de la page, puis cliquez sur **Télécharger** dans les paramètres de notification d'Apple. Sélectionnez ensuite le certificat **.p12** exporté précédemment ainsi que le mot de passe du certificat. Assurez-vous de choisir si vous souhaitez utiliser la **Production** (si vous souhaitez envoyer des notifications Push aux utilisateurs qui ont acheté votre application depuis le store) ou le service Push **Bac à sable (sandbox)** (au cours du développement).

    ![][32]

7.  Cliquez sur l'onglet **Tableau de bord** dans la partie supérieure, puis cliquez sur **Informations de connexion**. Notez les deux chaînes de connexion.

    ![][33]

Votre Notification Hub est configuré pour APNS, et vous disposez des chaînes de connexion pour inscrire votre application et envoyer des notifications.

## <a name="connecting-app"></a><span class="short-header">Connexion de votre application</span>Connexion de votre application au concentrateur de notification

### Télécharger la bibliothèque WindowsAzure.Messaging

Cet assembly permet de vous inscrire facilement à Azure Notification Hubs. Il peut être téléchargé à l'aide des instructions ci-dessous ou à partir de la page de [téléchargement d'exemples][Application NotificationHubs].

1.  Téléchargez la source de [WindowsAzure.Messaging][] depuis GitHub.

2.  Compilez le projet et recherchez l'assembly de sortie **WindowsAzure.messaging.dll**. Vous en aurez besoin pour configurer votre application Xamarin.iOS ci-dessous.

### Création d'un projet

1.  Dans Xamarin Studio, créez un projet iOS et sélectionnez le modèle **Single View Application**.

    ![][34]

2.  Commencez par ajouter une référence au composant Azure Mobile Services. Dans la vue Solution, cliquez avec le bouton droit sur le dossier **Components** de votre projet, puis choisissez **Get More Components**. Recherchez le composant **Azure Mobile Services** et ajoutez-le à votre projet.

3.  Ensuite, ajoutez une référence à la bibliothèque WindowsAzure.Messaging téléchargée précédemment. Cliquez avec le bouton droit sur le dossier **References** et choisissez **Edit References...**. Sous l'onglet **.Net Assembly**, recherchez **WindowsAzure.Messaging.dll** et

4.  Dans **AppDelegate.cs**, ajoutez l'instruction using suivante :

        using Microsoft.WindowsAzure.MobileServices;

    using WindowsAzure.Messaging;

5.  Déclarez une instance de **SBNotificationHub** :

        private SBNotificationHub Hub { get; set; }

6.  Créez une classe **Constants.cs** avec les variables suivantes :

        // Azure app specific connection string and hub path
        public const string ConnectionString = "<Azure connection string>";
        public const string NotificationHubPath = "<Azure hub path>";

7.  Dans **AppDelegate.cs**, mettez à jour **FinishedLaunching()** afin qu'il corresponde à ce qui suit :

        public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
        {
            UIRemoteNotificationType notificationTypes = UIRemoteNotificationType.Alert | 
                UIRemoteNotificationType.Badge | UIRemoteNotificationType.Sound;
            UIApplication.SharedApplication.RegisterForRemoteNotificationTypes(notificationTypes); 

            return true;
        }

8.  Remplacez la méthode **RegisteredForRemoteNotifications()** dans **AppDelegate.cs** :

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

9.  Remplacez la méthode **ReceivedRemoteNotification()** dans **AppDelegate.cs** :

        public override void ReceivedRemoteNotification(UIApplication application, NSDictionary userInfo)
        {
            ProcessNotification(userInfo, false);
        }

10. Créez la méthode **ProcessNotification()** suivante dans **AppDelegate.cs** :

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
                // "  aps:{alert:"alert msg here"}  " this will work fine.
                // But if you're using a complex alert with Localization keys, etc., 
                // your "alert" object from the aps dictionary will be another NSDictionary. 
                // Basically the json gets dumped right into a NSDictionary, 
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

    <div class="dev-callout"><b>Remarque</b>
<p>Vous pouvez choisir de remplacer **FailedToRegisterForRemoteNotifications()** afin de g&eacute;rer les situations impliquant une absence de connexion r&eacute;seau, etc.</p>
</div>

11. Exécutez l'application sur votre appareil.

## <a name="send"></a><span class="short-header">Envoi de notifications</span>Envoi de notifications à partir de votre serveur principal

Vous pouvez envoyer des notifications en utilisant Notification Hubs à partir de tous les serveurs principaux à l'aide de l'[interface REST][]. Dans ce didacticiel, vous envoyez des notifications avec une application console .NET et un Mobile Service à l'aide d'un script Node.

Pour envoyer des notifications en utilisant une application .NET :

1.  Créez une application console Visual C# :

    ![][35]

2.  Ajoutez une référence au Kit de développement logiciel (SDK) Azure Service Bus à l'aide du [package NuGet WindowsAzure.ServiceBus][]. Dans le menu principal de Visual Studio, cliquez sur **Outils**, sur **Library Package Manager**, puis sur **Console du Gestionnaire de package**. Dans la fenêtre de la console, tapez :

        Install-Package WindowsAzure.ServiceBus and press Enter.

3.  Ouvrez le fichier Program.cs et ajoutez l'instruction using suivante :

        using Microsoft.ServiceBus.Notifications;

4.  Dans votre classe `Program`, ajoutez la méthode suivante :

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            var alert = "{\"aps\":{\"alert\":\"Hello from .NET!\"}}";
            await hub.SendAppleNativeNotificationAsync(alert);
        }

5.  Ajoutez ensuite la ligne suivante dans votre méthode `Main` :

         SendNotificationAsync();
         Console.ReadLine();

6.  Appuyez sur la touche F5 pour exécuter l'application. Vous devez recevoir une alerte sur votre appareil. Si vous utilisez le Wi-Fi, assurez-vous que votre connexion fonctionne.

Vous trouverez toutes les charges utiles possibles dans le [Guide de programmation des notifications locales et Push][] d'Apple.

Pour envoyer une notification en utilisant un service mobile, suivez les instructions de la rubrique [Prise en main de Mobile Services][], puis :

1.  Connectez-vous au [portail de gestion Azure][] et sélectionnez votre service mobile.

2.  Sélectionnez l'onglet **Scheduler** dans la partie supérieure.

    ![][36]

3.  Créez un travail planifié, insérez un nom, puis sélectionnez **On demand**.

    ![][37]

4.  Lorsque le travail est créé, cliquez sur son nom. Cliquez ensuite sur l'onglet **Script** dans la barre supérieure.

5.  Insérez le script suivant dans votre fonction Scheduler. Remplacez les espaces réservés par le nom de votre concentrateur de notification et la chaîne de connexion pour *DefaultFullSharedAccessSignature* obtenue précédemment. Cliquez sur **Enregistrer**.

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

6.  Cliquez sur **Exécuter une fois** sur la barre inférieure. Vous devez recevoir une alerte sur votre appareil.

## <a name="next-steps"> </a>Étapes suivantes

Dans cet exemple simple, vous avez envoyé des notifications à tous vos appareils iOS. Afin de cibler des utilisateurs spécifiques, consultez le didacticiel [Utilisation de Notification Hubs pour envoyer des notifications Push aux utilisateurs][], et, si vous souhaitez segmenter vos utilisateurs par groupes d'intérêt, vous pouvez lire [Utilisation de Notification Hubs pour envoyer les dernières nouvelles][]. Pour plus d'informations sur l'utilisation de Notification Hubs, consultez les pages [Vue d'ensemble des concentrateurs de notification][] et [Procédures Notification Hubs pour iOS][].

<!-- Anchors. --> 
<!-- Images. --> 
<!-- URLs. -->

  [Windows Universal]: /fr-fr/documentation/articles/notification-hubs-windows-store-dotnet-get-started/ "Windows Universal"
  [Windows Phone]: /fr-fr/documentation/articles/notification-hubs-windows-phone-get-started/ "Windows Phone"
  [iOS]: /fr-fr/documentation/articles/notification-hubs-ios-get-started/ "iOS"
  [Android]: /fr-fr/documentation/articles/notification-hubs-android-get-started/ "Android"
  [Kindle]: /fr-fr/documentation/articles/notification-hubs-kindle-get-started/ "Kindle"
  [Xamarin.iOS]: /fr-fr/documentation/articles/partner-xamarin-notification-hubs-ios-get-started/ "Xamarin.iOS"
  [Xamarin.Android]: /fr-fr/documentation/articles/partner-xamarin-notification-hubs-android-get-started/ "Xamarin.Android"
  [Application NotificationHubs]: http://go.microsoft.com/fwlink/p/?LinkId=331329
  [Génération de la demande de signature de certificat]: #certificates
  [Inscription de votre application et activation des notifications Push]: #register
  [Création d'un profil de mise en service pour l'application]: #profile
  [Configuration de votre concentrateur de notification]: #configure-hub
  [Connexion de votre application au concentrateur de notification]: #connecting-app
  [Envoi de notifications à partir de votre serveur principal]: #send
  [XCode 5.0]: https://go.microsoft.com/fwLink/p/?LinkID=266532
  [1]: http://xamarin.com/download
  [Composant Azure Mobile Services]: http://components.xamarin.com/view/azure-mobile-services/
  [Version d'évaluation gratuite d'Azure]: http://www.windowsazure.com/fr-fr/pricing/free-trial/?WT.mc_id=A643EE910&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Ffr-fr%2Fmanage%2Fservices%2Fnotification-hubs%2Fgetting-started-xamarin-ios%2F
  [Service de notification Push Apple]: http://go.microsoft.com/fwlink/p/?LinkId=272584
  []: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-step5.png
  [2]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-step6.png
  [3]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-step7.png
  [Portail de mise en service iOS]: http://go.microsoft.com/fwlink/p/?LinkId=272456
  [4]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-05.png
  [5]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-06.png
  [6]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-07.png
  [7]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-08.png
  [8]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-09.png
  [9]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-10.png
  [10]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-11.png
  [11]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-12.png
  [12]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-13.png
  [13]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-14.png
  [14]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-15.png
  [15]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-16.png
  [16]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-18.png
  [17]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-19.png
  [18]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-step9.png
  [19]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-step10.png
  [20]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-20.png
  [21]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-21.png
  [22]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-22.png
  [23]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-23.png
  [24]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-24.png
  [25]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-25.png
  [26]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-26.png
  [27]: ./media/partner-xamarin-notification-hubs-ios-get-started/mobile-services-ios-push-step18.png
  [portail de gestion Azure]: https://manage.windowsazure.com/
  [28]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-create-from-portal.png
  [29]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-create-from-portal2.png
  [30]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-select-from-portal.png
  [31]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-select-from-portal2.png
  [32]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-configure-ios.png
  [33]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-connection-strings.png
  [WindowsAzure.Messaging]: https://github.com/infosupport/WindowsAzure.Messaging.iOS
  [34]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-create-ios-app.png
  [interface REST]: http://msdn.microsoft.com/fr-fr/library/windowsazure/dn223264.aspx
  [35]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-create-console-app.png
  [package NuGet WindowsAzure.ServiceBus]: http://nuget.org/packages/WindowsAzure.ServiceBus/
  [Guide de programmation des notifications locales et Push]: http://developer.apple.com/library/mac/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW1
  [Prise en main de Mobile Services]: /fr-fr/develop/mobile/tutorials/get-started-xamarin-ios
  [36]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-scheduler1.png
  [37]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-scheduler2.png
  [Utilisation de Notification Hubs pour envoyer des notifications Push aux utilisateurs]: /fr-fr/manage/services/notification-hubs/notify-users-aspnet
  [Utilisation de Notification Hubs pour envoyer les dernières nouvelles]: /fr-fr/manage/services/notification-hubs/breaking-news-dotnet
  [Vue d'ensemble des concentrateurs de notification]: http://msdn.microsoft.com/fr-fr/library/jj927170.aspx
  [Procédures Notification Hubs pour iOS]: http://msdn.microsoft.com/fr-fr/library/jj927168.aspx
