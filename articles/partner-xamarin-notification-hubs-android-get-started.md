<properties linkid="manage-services-notification-hubs-getting-started-xamarin-android" urlDisplayName="" pageTitle="Get started with Notification Hubs for Xamarin.Android apps" metaKeywords="" description="Learn how to use Azure Notification Hubs to send push notifications to a Xamarin Android application." metaCanonical="" authors="donnam" solutions="" manager="dwrede" editor="" services="mobile-services,notification-hubs" documentationCenter="" title="Get started with Notification Hubs" />

<tags ms.service="notification-hubs" ms.workload="mobile" ms.tgt_pltfrm="mobile-xamarin-android" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="donnam" />

# Prise en main de Notification Hubs

<div class="dev-center-tutorial-selector sublanding"><a href="/fr-fr/documentation/articles/notification-hubs-windows-store-dotnet-get-started/" title="Windows Universal">Windows Universal</a><a href="/fr-fr/documentation/articles/notification-hubs-windows-phone-get-started/" title="Windows Phone">Windows Phone</a><a href="/fr-fr/documentation/articles/notification-hubs-ios-get-started/" title="iOS">iOS</a><a href="/fr-fr/documentation/articles/notification-hubs-android-get-started/" title="Android">Android</a><a href="/fr-fr/documentation/articles/notification-hubs-kindle-get-started/" title="Kindle">Kindle</a><a href="/fr-fr/documentation/articles/partner-xamarin-notification-hubs-ios-get-started/" title="Xamarin.iOS">Xamarin.iOS</a><a href="/fr-fr/documentation/articles/partner-xamarin-notification-hubs-android-get-started/" title="Xamarin.Android" class="current">Xamarin.Android</a></div>

Cette rubrique vous indique comment utiliser Azure Notification Hubs pour envoyer des notifications Push à une application Xamarin.Android.
Dans ce didacticiel, vous allez créer une application Xamarin.Android vide qui reçoit des notifications Push à l'aide de Google Cloud Messaging (GCM). Une fois la création terminée, vous pouvez diffuser des notifications Push sur tous les appareils sur lesquels votre application est installée au moyen de votre concentrateur de notification. Le code finalisé est disponible dans l'exemple [Application NotificationHubs][Application NotificationHubs].

Ce didacticiel vous familiarise avec les étapes de base pour activer les notifications Push :

1.  [Activation de Google Cloud Messaging][Activation de Google Cloud Messaging]
2.  [Configuration de votre concentrateur de notification][Configuration de votre concentrateur de notification]
3.  [Connexion de votre application au concentrateur de notification][Connexion de votre application au concentrateur de notification]
4.  [Exécution de votre application avec l'émulateur][Exécution de votre application avec l'émulateur]
5.  [Envoi de notifications à partir de votre serveur principal][Envoi de notifications à partir de votre serveur principal]

Ce didacticiel présente un scénario de diffusion simple utilisant les concentrateurs de notification. Ce didacticiel requiert les éléments suivants :

-   [Xamarin.Android][1]
-   Un compte Google actif
-   [Composant Azure Mobile Services][Composant Azure Mobile Services]
-   [Composant Google Cloud Messaging][Composant Google Cloud Messaging]

Vous devez suivre ce didacticiel avant de pouvoir suivre tous les autres didacticiels Notification Hubs pour les applications Xamarin.Android.

<div class="dev-callout"><strong>Remarque</strong> <p>Pour suivre ce didacticiel, vous avez besoin d'un compte Azure actif. Si vous ne poss&eacute;dez pas de compte, vous pouvez cr&eacute;er un compte d'&eacute;valuation gratuit en quelques minutes. Pour plus d'informations, consultez la page <a href="http://www.windowsazure.com/fr-fr/pricing/free-trial/?WT.mc_id=A9C9624B5&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Ffr-fr%2Fmanage%2Fservices%2Fnotification-hubs%2Fgetting-started-xamarin-android%2F" target="_blank">Version d'&eacute;valuation gratuite d'Azure</a>.</p></div>

## <a name="register"></a><span class="short-header">Activation de Google Cloud Messaging</span>Activation de Google Cloud Messaging

<div class="dev-callout"><b>Remarque</b>
<p>Pour effectuer la proc&eacute;dure d&eacute;crite dans cette rubrique, vous devez disposer d'un compte Google avec une adresse &eacute;lectronique v&eacute;rifi&eacute;e. Pour cr&eacute;er un compte Google, consultez la page <a href="http://go.microsoft.com/fwlink/p/?LinkId=268302" target="_blank">accounts.google.com</a>.</p>
</div>

1.  Accédez au site web [Google apis][Google apis], connectez-vous avec votre compte Google, puis cliquez sur **Create project...**.

    ![][0]

    <div class="dev-callout"><b>Remarque</b>
<p>Si vous disposez d&eacute;j&agrave; d'un projet en cours, vous &ecirc;tes redirig&eacute; vers la page <strong>Dashboard</strong> une fois la connexion &eacute;tablie. Pour cr&eacute;er un projet depuis le tableau de bord, d&eacute;veloppez <strong>API Project</strong>, cliquez sur <strong>Create...</strong> sous <strong>Other projects</strong>, puis entrez un nom de projet et cliquez sur <strong>Create project</strong>.</p>
</div>

2.  Cliquez sur **Overview** dans la colonne de gauche et notez le numéro de projet dans la section **Dashboard**.

    Plus loin dans le didacticiel, vous allez définir cette valeur en tant que variable PROJECT\_ID dans le client.

3.  Sur la page [Google apis][Google apis], cliquez sur **Services**, puis sur la touche bascule pour activer **Google Cloud Messaging for Android** et accepter les conditions de service.

4.  Cliquez sur **API Access**, puis sur **Create new Server key...**.

    ![][2]

5.  Sous **Configure Server Key for API Project**, cliquez sur **Create**.

    ![][3]

6.  Prenez note de la valeur de **API key**.

    ![][4]

Ensuite, vous allez utiliser cette valeur de clé API afin d'activer Notification Hubs pour l'authentification avec GCM et l'envoi de notifications Push au nom de votre application.

## <a name="configure-hub"></a><span class="short-header">Configuration de votre concentrateur de notification</span>Configuration de votre concentrateur de notification

1.  Connectez-vous au [portail de gestion Azure][portail de gestion Azure] et cliquez sur **+NOUVEAU** en bas de l'écran.

2.  Cliquez sur **Services d'application**, puis sur **Service Bus**, **Concentrateur de notification**, **Création rapide**.

    ![][5]

3.  Tapez un nom pour votre concentrateur de notification, sélectionnez la région souhaitée, puis cliquez sur **Créer un concentrateur de notification**.

    ![][6]

4.  Cliquez sur l'espace de noms que vous venez de créer (en général ***nom du concentrateur de notification*-ns**), puis cliquez sur l'onglet **Configurer** en haut.

    ![][7]

5.  Cliquez sur l'onglet **Concentrateurs de notification** en haut de la page, puis cliquez sur celui que vous venez de créer.

    ![][8]

6.  Cliquez sur l'onglet **Configurer** dans la partie supérieure, saisissez la valeur **Clé de l'API** obtenue à la section précédente, puis cliquez sur **Enregistrer**.

    ![][9]

7.  Sélectionnez l'onglet **Tableau de bord** en haut, puis cliquez sur **Informations de connexion**. Notez les deux chaînes de connexion.

    ![][10]

Votre concentrateur de notification est configuré pour GCM, et vous disposez des chaînes de connexion pour inscrire votre application et envoyer des notifications Push.

## <a name="connecting-app"></a><span class="short-header">Connexion de votre application</span>Connexion de votre application au concentrateur de notification

### Création d'un projet

1.  Dans Xamarin Studio (ou Visual Studio), créez un projet Android (File, New, Solution, Android Application).

    ![][11]
    ![][12]

2.  Ouvrez les propriétés du projet en cliquant avec le bouton droit sur votre nouveau projet dans la vue Solution et en choisissant **Options**. Sélectionnez l'élément **Android Application** dans la section **Build**.

    ![][13]

3.  Définissez **Minimum Android version** sur API Level 8.

4.  Définissez **Target Android version** sur la version d'API que vous voulez cibler (niveau d'API 8 ou supérieur).

5.  Assurez-vous que la première lettre de votre nom de package (**Package name**) est en minuscule.

    <div class="dev-callout"><b>Remarque</b>
<p>La premi&egrave;re lettre du nom du package doit &ecirc;tre une minuscule. Sinon, vous recevrez des erreurs du manifeste d'application lors de l'inscription de vos **BroadcastReceiver** et **IntentFilter** pour les notifications Push ci-dessous.</p>
</div>

### Ajouter le client Google Cloud Messaging à votre projet

Le client Google Cloud Messaging, disponible dans le magasin de composants Xamarin, simplifie la prise en charge des notifications Push dans les applications Xamarin.Android.

1.  Cliquez avec le bouton droit sur le dossier Components de l'application Xamarin.Android et choisissez **Get More Components...**.

2.  Recherchez le composant **Google Cloud Messaging Client**.

3.  Ajoutez le composant à l'application Xamarin.Android. Les références d'assembly nécessaires sont automatiquement ajoutées.

### Ajout de Xamarin.NotificationHub à votre projet

Cet assembly permet de vous inscrire facilement à Azure Notification Hubs. Il peut être téléchargé à l'aide des instructions ci-dessous ou à partir de la page de [téléchargement d'exemples][Application NotificationHubs].

1.  Accédez à la page [Xamarin.NotificationHub Github][Xamarin.NotificationHub Github], téléchargez et créez le dossier source.

2.  Créez un dossier \*\*\_external\*\* dans le dossier de votre projet Xamarin.Android et copiez-y le fichier **ByteSmith.WindowsAzure.Messaging.Android.dll** compilé.

3.  Ouvrez votre projet Xamarin.Android dans Xamarin Studio (ou Visual Studio).

4.  Cliquez avec le bouton droit sur le dossier **References** du projet et choisissez **Edit References...**

5.  Accédez à l'onglet **.Net Assembly**, au dossier \*\*\_external\*\* de votre projet, sélectionnez le fichier **ByteSmith.WindowsAzure.Messaging.Android.dll** créé précédemment et cliquez sur **Add**. Cliquez sur OK pour fermer la boîte de dialogue.

### Configuration de Notification Hubs dans votre projet

1.  Créez une classe **Constants.cs** et définissez les valeurs constantes suivantes (en remplaçant les espaces réservés par des valeurs) :

        public const string SenderID = "<GoogleProjectNumber>"; // Google API Project Number

        // Azure app specific connection string and hub path
        public const string ConnectionString = "<Azure connection string>";
        public const string NotificationHubPath = "<hub path>";

2.  Ajoutez les instructions using suivantes à **MainActivity.cs** :

        using ByteSmith.WindowsAzure.Messaging;
        using Gcm.Client;

3.  Ajoutez la méthode suivante dans la classe **MainActivity** :

        private void RegisterWithGCM()
        {
            // Check to ensure everything's setup right
            GcmClient.CheckDevice(this);
            GcmClient.CheckManifest(this);

            // Register for push notifications
            System.Diagnostics.Debug.WriteLine("Registering...");
            GcmClient.Register(this, Constants.SenderID);
        }

4.  Créez une classe **MyBroadcastReceiver**.

    <div class="dev-callout"><b>Remarque</b>
<p>Nous allons d&eacute;crire la proc&eacute;dure &agrave; suivre pour cr&eacute;er un <b>BroadcastReceiver</b> &agrave; partir de z&eacute;ro. Il existe toutefois une alternative rapide &agrave; la cr&eacute;ation manuelle du <b>MyBroadcastReceiver.cs</b>, qui consiste &agrave; se reporter au fichier <b>GcmService.cs</b> de l'exemple de projet Xamarin.Android sur GitHub. Dupliquer <b>GcmService.cs</b> et changer les noms des classes peut &ecirc;tre un excellent point de d&eacute;part.</p>
</div>

5.  Ajoutez les instructions using suivantes à **MyBroadcastReceiver.cs** (faisant référence au composant et à l'assembly ajoutés plus tôt) :

        using ByteSmith.WindowsAzure.Messaging;
        using Gcm.Client;

6.  Ajoutez les demandes d'autorisation suivantes entre les instructions **using** et la déclaration **namespace** :

        [assembly: Permission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
        [assembly: UsesPermission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
        [assembly: UsesPermission(Name = "com.google.android.c2dm.permission.RECEIVE")]

        //GET_ACCOUNTS is only needed for android versions 4.0.3 and below
        [assembly: UsesPermission(Name = "android.permission.GET_ACCOUNTS")]
        [assembly: UsesPermission(Name = "android.permission.INTERNET")]
        [assembly: UsesPermission(Name = "android.permission.WAKE_LOCK")]

7.  Dans **MyBroadcastReceiver.cs**, changez la classe **MyBroadcastReceiver** comme suit :

        [BroadcastReceiver(Permission=Gcm.Client.Constants.PERMISSION_GCM_INTENTS)]
        [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_MESSAGE }, Categories = new string[] { "@PACKAGE_NAME@" })]
        [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_REGISTRATION_CALLBACK }, Categories = new string[] { "@PACKAGE_NAME@" })]
        [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_LIBRARY_RETRY }, Categories = new string[] { "@PACKAGE_NAME@" })]
        public class MyBroadcastReceiver : GcmBroadcastReceiverBase<GcmService>
        {
            public static string[] SENDER_IDS = new string[] { Constants.SenderID };

            public const string TAG = "MyBroadcastReceiver-GCM";
        }

8.  Ajoutez dans **MyBroadcastReceiver.cs** une classe nommée **PushHandlerService** qui dérive de **PushHandlerServiceBase**. Veillez à utiliser la directive **Service** sur la classe :

        [Service] //Must use the service tag
        public class GcmService : GcmServiceBase
        {
            public static string RegistrationID { get; private set; }
            private NotificationHub Hub { get; set; }

            public GcmService() : base(Constants.SenderID) 
            {
                Log.Info(MyBroadcastReceiver.TAG, "GcmService() constructor"); 
            }
        }

9.  **GcmServiceBase** implémente les méthodes **OnRegistered()**, **OnUnRegistered()**, **OnMessage()**, **OnRecoverableError()** et **OnError()**. Notre classe d'implémentation **GcmService** doit remplacer ces méthodes, lesquelles se déclencheront en réponse à l'interaction avec le concentrateur de notification.

10. Remplacez la méthode **OnRegistered()** dans **PushHandlerService** par le code suivant :

        protected override async void OnRegistered(Context context, string registrationId)
        {
            Log.Verbose(MyBroadcastReceiver.TAG, "GCM Registered: " + registrationId);
            RegistrationID = registrationId;

            createNotification("GcmService-GCM Registered...", "The device has been Registered, Tap to View!");

            Hub = new NotificationHub(Constants.NotificationHubPath, Constants.ConnectionString);
            try
            {
                await Hub.UnregisterAllAsync(registrationId);
            }
            catch (Exception ex)
            {
                Debug.WriteLine(ex.Message);
                Debugger.Break();
            }

            var tags = new List<string>() { "falcons" }; // create tags if you want

            try
            {
                var hubRegistration = await Hub.RegisterNativeAsync(registrationId, tags);
            }
            catch (Exception ex)
            {
                Debug.WriteLine(ex.Message); 
                Debugger.Break();
            }
        }

    <div class="dev-callout"><b>Remarque</b>
<p>Dans le code <b>OnRegistered()</b> ci-dessus, notez qu'il est possible de sp&eacute;cifier des balises pour l'enregistrement de canaux de messagerie sp&eacute;cifiques.</p>
</div>

11. Remplacez la méthode **OnMessage** dans **PushHandlerService** par le code suivant :

        protected override void OnMessage(Context context, Intent intent)
        {
            Log.Info(MyBroadcastReceiver.TAG, "GCM Message Received!");

            var msg = new StringBuilder();

            if (intent != null && intent.Extras != null)
            {
                foreach (var key in intent.Extras.KeySet())
                    msg.AppendLine(key + "=" + intent.Extras.Get(key).ToString());
            }

            string messageText = intent.Extras.GetString("msg");
            if (!string.IsNullOrEmpty(messageText))
            {
                createNotification("New hub message!", messageText);
                return;
            }

            createNotification("Unknown message details", msg.ToString());
        }

12. Ajoutez la méthode **createNotification** suivante à **PushHandlerService** pour avertir les utilisateurs comme cela a été fait plus haut :

        void createNotification(string title, string desc)
        {
            //Create notification
            var notificationManager = GetSystemService(Context.NotificationService) as NotificationManager;

            //Create an intent to show ui
            var uiIntent = new Intent(this, typeof(MainActivity));

            //Create the notification
            var notification = new Notification(Android.Resource.Drawable.SymActionEmail, title);

            //Auto cancel will remove the notification once the user touches it
            notification.Flags = NotificationFlags.AutoCancel;

            //Set the notification info
            //we use the pending intent, passing our ui intent over which will get called
            //when the notification is tapped.
            notification.SetLatestEventInfo(this, title, desc, PendingIntent.GetActivity(this, 0, uiIntent, 0));

            //Show the notification
            notificationManager.Notify(1, notification);
        }

13. Remplacez les membres abstraits **OnUnRegistered()**, **OnRecoverableError()** et **OnError()** de manière à ce que votre code puisse être compilé.

## <a name="run-app"></a><span class="short-header">Exécution de votre application</span>Exécution de votre application dans l'émulateur

Lorsque vous exécutez cette application dans l'émulateur, veillez à utiliser un AVD (appareil virtuel Android) qui prend en charge les API Google.

1.  Dans **Outils**, cliquez sur **Open Android Emulator Manager**, sélectionnez votre appareil, puis cliquez sur **Modifier**.

    ![][14]

2.  Sélectionnez **API Google** dans **Cible**, puis cliquez sur **OK**.

    ![][15]

3.  Dans la barre d'outils supérieure, cliquez sur **Exécuter**, puis sélectionnez votre application. L'émulateur démarre et l'application est exécutée.

4.  L'application extrait le *registrationId* de GCM et s'inscrit auprès du concentrateur de notification.

    <div class="dev-callout"><b>Remarque</b>
<p>Afin de recevoir des notifications Push, vous devez configurer un compte Google sur votre appareil virtuel Android (dans l'&eacute;mulateur, acc&eacute;dez &agrave; <b>Param&egrave;tres</b>, puis cliquez sur <b>Ajouter un compte</b>). Assurez-vous &eacute;galement que l'&eacute;mulateur est connect&eacute; &agrave; Internet.</p>
</div>

## <a name="send"></a><span class="short-header">Envoi de notifications</span>Envoi de notifications à partir de votre serveur principal

Vous pouvez envoyer des notifications à l'aide de Notification Hubs à partir de n'importe quel serveur principal utilisant l'[interface REST][interface REST]. Dans ce didacticiel, vous envoyez des notifications avec une application console .NET et un Mobile Service à l'aide d'un script Node.

Pour envoyer des notifications en utilisant une application .NET :

1.  Créez une application console Visual C# :

    ![][16]

2.  Ajoutez une référence au Kit de développement logiciel (SDK) Azure Service Bus à l'aide du [package NuGet WindowsAzure.ServiceBus][package NuGet WindowsAzure.ServiceBus]. Dans le menu principal de Visual Studio, cliquez sur **Outils**, sur **Library Package Manager**, puis sur **Console du Gestionnaire de package**. Dans la fenêtre de la console, tapez :

        Install-Package WindowsAzure.ServiceBus

    et appuyez sur Entrée.

3.  Ouvrez le fichier Program.cs et ajoutez l'instruction using suivante :

        using Microsoft.ServiceBus.Notifications;

4.  Dans votre classe `Program`, ajoutez la méthode suivante :

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            await hub.SendGcmNativeNotificationAsync("{ \"data\" : {\"msg\":\"Hello from Azure!\"}}");
        }

5.  Ajoutez ensuite les lignes suivantes dans votre méthode Main :

         SendNotificationAsync();
         Console.ReadLine();

6.  Appuyez sur la touche F5 pour exécuter l'application. Vous devez recevoir une notification toast.

    ![][17]

Pour envoyer une notification en utilisant un service mobile, suivez les instructions de la rubrique [Prise en main de Mobile Services][Prise en main de Mobile Services], puis :

1.  Connectez-vous au [portail de gestion Azure][portail de gestion Azure] et sélectionnez votre service mobile.

2.  Sélectionnez l'onglet **Planificateur** dans la partie supérieure.

    ![][18]

3.  Créez un travail planifié, insérez un nom, puis sélectionnez **À la demande**.

    ![][19]

4.  Lorsque le travail est créé, cliquez sur son nom. Cliquez ensuite sur l'onglet **Script** dans la barre supérieure.

5.  Insérez le script suivant dans votre fonction Planificateur. Remplacez les espaces réservés par le nom de votre concentrateur de notification et la chaîne de connexion pour *DefaultFullSharedAccessSignature* obtenue précédemment. Cliquez sur **Enregistrer**.

        var azure = require('azure');
        var notificationHubService = azure.createNotificationHubService('<hub name>', '<connection string>');
        notificationHubService.gcm.send(null,'{"data":{"msg" : "Hello from Mobile Services!"}}',
          function (error)
          {
            if (!error) {
               console.warn("Notification successful");
            }
            else
            {
              console.warn("Notification failed" + error);
            }
          }
        );

6.  Cliquez sur **Exécuter une fois** sur la barre inférieure. Vous devez recevoir une notification toast.

## <a name="next-steps"> </a>Étapes suivantes

Dans cet exemple simple, vous avez envoyé des notifications à tous vos appareils Android. Afin de cibler des utilisateurs spécifiques, consultez le didacticiel [Utilisation de Notification Hubs pour envoyer des notifications Push aux utilisateurs][Utilisation de Notification Hubs pour envoyer des notifications Push aux utilisateurs], et, si vous souhaitez segmenter vos utilisateurs par groupes d'intérêt, vous pouvez lire [Utilisation des Notification Hubs pour diffuser les dernières nouvelles][Utilisation des Notification Hubs pour diffuser les dernières nouvelles]. Pour plus d'informations sur l'utilisation de Notification Hubs, consultez les pages [Vue d'ensemble des concentrateurs de notification][Vue d'ensemble des concentrateurs de notification] et [Procédures Notification Hubs pour Android][Procédures Notification Hubs pour Android].

<!-- Anchors. --> 
<!-- Images. --> 
<!-- URLs. -->

  [Xamarin.Android]: /fr-fr/documentation/articles/partner-xamarin-notification-hubs-android-get-started/ "Xamarin.Android"
  [Application NotificationHubs]: http://go.microsoft.com/fwlink/p/?LinkId=331329
  [Activation de Google Cloud Messaging]: #register
  [Configuration de votre concentrateur de notification]: #configure-hub
  [Connexion de votre application au concentrateur de notification]: #connecting-app
  [Exécution de votre application avec l'émulateur]: #run-app
  [Envoi de notifications à partir de votre serveur principal]: #send
  [1]: http://xamarin.com/download/
  [Composant Azure Mobile Services]: http://components.xamarin.com/view/azure-mobile-services/
  [Composant Google Cloud Messaging]: http://components.xamarin.com/view/GCMClient/
  [Google apis]: http://go.microsoft.com/fwlink/p/?LinkId=268303
  [0]: ./media/partner-xamarin-notification-hubs-android-get-started/mobile-services-google-developers.png
  [2]: ./media/partner-xamarin-notification-hubs-android-get-started/mobile-services-google-create-server.png
  [3]: ./media/partner-xamarin-notification-hubs-android-get-started/mobile-services-google-create-server2.png
  [4]: ./media/partner-xamarin-notification-hubs-android-get-started/mobile-services-google-create-server3.png
  [portail de gestion Azure]: https://manage.windowsazure.com/
  [5]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-from-portal.png
  [6]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-from-portal2.png
  [7]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-select-from-portal.png
  [8]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-select-from-portal2.png
  [9]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-configure-android.png
  [10]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-connection-strings.png
  [11]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-app1.png
  [12]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-app2.png
  [13]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-app3.png
  [Xamarin.NotificationHub Github]: https://github.com/SaschaDittmann/Xamarin.NotificationHub
  [14]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-android-app7.png
  [15]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-android-app8.png
  [interface REST]: http://msdn.microsoft.com/fr-fr/library/windowsazure/dn223264.aspx
  [16]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-console-app.png
  [package NuGet WindowsAzure.ServiceBus]: http://nuget.org/packages/WindowsAzure.ServiceBus/
  [17]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-android-toast.png
  [Prise en main de Mobile Services]: /fr-fr/develop/mobile/tutorials/get-started-xamarin-android/#create-new-service
  [18]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-scheduler1.png
  [19]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-scheduler2.png
  [Utilisation de Notification Hubs pour envoyer des notifications Push aux utilisateurs]: /fr-fr/manage/services/notification-hubs/notify-users-aspnet
  [Utilisation des Notification Hubs pour diffuser les dernières nouvelles]: /fr-fr/manage/services/notification-hubs/breaking-news-dotnet
  [Vue d'ensemble des concentrateurs de notification]: http://msdn.microsoft.com/fr-fr/library/jj927170.aspx
  [Procédures Notification Hubs pour Android]: http://msdn.microsoft.com/fr-fr/library/dn282661.aspx
