<properties
	pageTitle="Prise en main de Notification Hubs pour les applications Xamarin.Android"
	description="Découvrez comment utiliser Azure Notification Hubs pour envoyer des notifications Push à une application Xamarin Android."
	authors="yuaxu"
	manager="dwrede"
	editor=""
	services="notification-hubs"
	documentationCenter="xamarin"/>

<tags
	ms.service="notification-hubs"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-xamarin-android"
	ms.devlang="dotnet"
	ms.topic="hero-article"
	ms.date="11/11/2014"
	ms.author="donnam"/>

# Prise en main de Notification Hubs

[AZURE.INCLUDE [notification-hubs-selector-get-started](../includes/notification-hubs-selector-get-started.md)]

##Vue d'ensemble

Cette rubrique vous présente l'utilisation d'Azure Notification Hubs pour envoyer des notifications Push vers une application Xamarin.Android.
Dans ce didacticiel, vous allez créer une application Xamarin.Android vierge qui reçoit des notifications Push à l'aide de Google Cloud Messaging (GCM). Une fois la création terminée, vous pouvez diffuser des notifications Push sur tous les appareils sur lesquels votre application est installée au moyen de votre concentrateur de notification. Le code finalisé est disponible dans l'exemple d'application [NotificationHubs][GitHub].

Ce didacticiel présente un scénario de diffusion simple utilisant Notification Hubs. 

##Conditions préalables

Ce didacticiel requiert les éléments suivants :

+ [Xamarin.Android]
+ Un compte Google actif
+ [Composant Azure Mobile Services]
+ [Composant Azure Messaging]
+ [Composant Client Google Cloud Messaging]

Vous devez suivre ce didacticiel avant de pouvoir suivre tous les autres didacticiels Notification Hubs pour les applications Xamarin.Android.

> [AZURE.IMPORTANT] Pour suivre ce didacticiel, vous avez besoin d'un compte Azure actif. Si vous ne possédez pas de compte, vous pouvez créer un compte d'évaluation gratuit en quelques minutes. Pour plus d'informations, consultez la page [Version d'évaluation gratuite d'Azure](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A9C9624B5&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Ffr-fr%2Fdocumentation%2Farticles%2Fpartner-xamarin-notification-hubs-android-get-started%2F).

##<a name="register"></a>Activation de Google Cloud Messaging

[AZURE.INCLUDE [mobile-services-enable-Google-cloud-messaging](../includes/mobile-services-enable-Google-cloud-messaging.md)]

##<a name="configure-hub"></a>Configuration de votre concentrateur de notification

[AZURE.INCLUDE [notification-hubs-android-configure-push](../includes/notification-hubs-android-configure-push.md)]

##<a name="connecting-app"></a>Connexion de votre application au concentrateur de notification

### Création d'un projet

1. Dans Xamarin Studio (ou Visual Studio), cliquez sur **File** et **New**, puis sur **Android Application** dans la boîte de dialogue **New Solution** et enfin sur **OK**.

   	![][14]

	Un projet Android est créé.

2. Ouvrez les propriétés du projet en cliquant avec le bouton droit sur votre nouveau projet dans la vue Solution et en choisissant **Options**. Sélectionnez l'élément **Android Application** dans la section **Build**.

   	![][15]

3. Définissez **Minimum Android version** sur API Level 8.

4. Définissez **Target Android version** sur la version d'API que vous voulez cibler (API Level 8 ou supérieur).

5. Assurez-vous que la première lettre de votre nom de package (**Package name**) est en minuscule.

	> [AZURE.IMPORTANT] La première lettre du nom du package doit être une minuscule. Sinon, vous recevrez des erreurs du manifeste d'application lors de l'inscription de vos **BroadcastReceiver** et **IntentFilter** pour les notifications Push ci-dessous.

### Ajout des composants requis à votre projet

Le client Google Cloud Messaging, disponible dans le magasin de composants Xamarin, simplifie la prise en charge des notifications Push dans les applications Xamarin.Android.

1. Cliquez avec le bouton droit sur le dossier Components de l'application Xamarin.Android et choisissez **Get More Components...**.

2. Recherchez le composant **Azure Mobile Services** et ajoutez-le à votre projet.

3. Recherchez le composant **Azure Messaging** et ajoutez-le à votre projet.

4. Recherchez le composant **Google Cloud Messaging Client** et ajoutez-le à votre projet.


### Configuration de Notification Hubs dans votre projet

1. Créez une classe **Constants.cs** et définissez les valeurs constantes suivantes (en remplaçant les espaces réservés par des valeurs) :

        public const string SenderID = "<GoogleProjectNumber>"; // Google API Project Number

        // Azure app specific connection string and hub path
        public const string ConnectionString = "<Azure connection string>";
        public const string NotificationHubPath = "<hub path>";

2. Ajoutez les instructions using suivantes à **MainActivity.cs** :

		using Microsoft.WindowsAzure.MobileServices;
		using Gcm.Client;

3. Ajoutez la méthode suivante dans la classe **MainActivity** :

		private void RegisterWithGCM()
        {
            // Check to ensure everything's setup right
            GcmClient.CheckDevice(this);
            GcmClient.CheckManifest(this);

            // Register for push notifications
            System.Diagnostics.Debug.WriteLine("Registering...");
            GcmClient.Register(this, Constants.SenderID);
        }

4. Créez une classe **MyBroadcastReceiver**.

	> [AZURE.NOTE] Nous allons décrire la procédure à suivre pour créer un **BroadcastReceiver** à partir de zéro. Il existe toutefois une alternative rapide à la création manuelle du **MyBroadcastReceiver.cs**, qui consiste à se reporter au fichier **GcmService.cs** de l'exemple de projet Xamarin.Android sur GitHub. Dupliquer **GcmService.cs** et changer les noms des classes peut être un excellent point de départ.

5. Ajoutez les instructions using suivantes à **MyBroadcastReceiver.cs** (faisant référence au composant et à l'assembly ajoutés plus tôt) :

		using WindowsAzure.Messaging;
		using Gcm.Client;

5. Ajoutez les demandes d'autorisation suivantes entre les instructions **using** et la déclaration **namespace** :

		[assembly: Permission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
		[assembly: UsesPermission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
		[assembly: UsesPermission(Name = "com.google.android.c2dm.permission.RECEIVE")]

		//GET_ACCOUNTS is only needed for android versions 4.0.3 and below
		[assembly: UsesPermission(Name = "android.permission.GET_ACCOUNTS")]
		[assembly: UsesPermission(Name = "android.permission.INTERNET")]
		[assembly: UsesPermission(Name = "android.permission.WAKE_LOCK")]

6. Dans **MyBroadcastReceiver.cs**, changez la classe **MyBroadcastReceiver** comme suit :

    	[BroadcastReceiver(Permission=Gcm.Client.Constants.PERMISSION_GCM_INTENTS)]
        [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_MESSAGE }, 
			Categories = new string[] { "@PACKAGE_NAME@" })]
        [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_REGISTRATION_CALLBACK }, 
			Categories = new string[] { "@PACKAGE_NAME@" })]
        [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_LIBRARY_RETRY }, 
			Categories = new string[] { "@PACKAGE_NAME@" })]
        public class MyBroadcastReceiver : GcmBroadcastReceiverBase<PushHandlerService>
        {
            public static string[] SENDER_IDS = new string[] { Constants.SenderID };

            public const string TAG = "MyBroadcastReceiver-GCM";
        }

7. Ajoutez dans **MyBroadcastReceiver.cs** une classe nommée **PushHandlerService** qui dérive de **PushHandlerServiceBase**. Veillez à appliquer l'attribut **Service** sur la classe :

    	[Service] // Must use the service tag
    	public class PushHandlerService : GcmServiceBase
    	{
        	public static string RegistrationID { get; private set; }
        	private NotificationHub Hub { get; set; }

        	public PushHandlerService() : base(Constants.SenderID)
       		{
            	Log.Info(MyBroadcastReceiver.TAG, "PushHandlerService() constructor");
        	}
    	}


8. **GcmServiceBase** implémente les méthodes **OnRegistered()**, **OnUnRegistered()**, **OnMessage()**, **OnRecoverableError()** et **OnError()**. Notre classe d'implémentation **PushHandlerService** doit remplacer ces méthodes, et ces méthodes se déclencheront en réponse à l'interaction avec le concentrateur de notification.

9. Remplacez la méthode **OnRegistered()** dans **PushHandlerService** par le code suivant :

        protected override void OnRegistered(Context context, string registrationId)
        {
            Log.Verbose(MyBroadcastReceiver.TAG, "GCM Registered: " + registrationId);
            RegistrationID = registrationId;

            createNotification("PushHandlerService-GCM Registered...", "The device has been Registered, Tap to View!");

            Hub = new NotificationHub (Constants.NotificationHubPath, Constants.ConnectionString);
            try
            {
                Hub.UnregisterAll(registrationId);
            }
            catch (Exception ex)
            {
                Debug.WriteLine(ex.Message);
                Debugger.Break();
            }

            var tags = new List<string>() { "falcons" }; // create tags if you want

            try
            {
                var hubRegistration = Hub.RegisterNative (registrationId, tags);
            }
            catch (Exception ex)
            {
                Debug.WriteLine(ex.Message);
                Debugger.Break();
            }
        }

	> [AZURE.NOTE] Dans le code **OnRegistered()** ci-dessus, notez qu'il est possible de spécifier des balises pour l'enregistrement de canaux de messagerie spécifiques.

10. Remplacez la méthode **OnMessage** dans **PushHandlerService** par le code suivant :

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

11. Ajoutez la méthode **createNotification** suivante à **PushHandlerService** pour avertir les utilisateurs comme cela a été fait plus haut :

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

12. Remplacez les membres abstraits **OnUnRegistered()**, **OnRecoverableError()** et **OnError()** de manière à ce que votre code puisse être compilé.


<h2><a name="run-app"></a>Exécution de votre application dans l'émulateur</h2>

Quand vous exécutez cette application dans l'émulateur, veillez à utiliser un appareil virtuel Android (AVD) qui prend en charge les API Google.

	> [AZURE.IMPORTANT] Afin de recevoir des notifications Push, vous devez configurer un compte Google sur votre appareil virtuel Android (dans l'émulateur, accédez à **Paramètres**, puis cliquez sur **Ajouter un compte**). Assurez-vous également que l'émulateur est connecté à Internet.

1. Dans **Tools**, cliquez sur **Open Android Emulator Manager**, sélectionnez votre appareil, puis cliquez sur **Edit**.

   	![][18]

2. Sélectionnez **Google APIs** dans **Target**, puis cliquez sur **OK**.

   	![][19]

3. Dans la barre d'outils supérieure, cliquez sur **Run**, puis sélectionnez votre application. L'émulateur démarre et l'application est exécutée.

  L'application extrait le *registrationId* de GCM et s'inscrit auprès du concentrateur de notification.

<h2><a name="send"></a>Envoi de notifications à partir de votre serveur principal</h2>

Vous pouvez envoyer des notifications à l'aide de Notification Hubs à partir de n'importe quel serveur principal en utilisant l'<a href="http://msdn.microsoft.com/library/windowsazure/dn223264.aspx">interface REST</a>. Dans ce didacticiel, vous allez envoyer des notifications avec une application console .NET et un service mobile à l'aide d'un script Node.

Pour envoyer des notifications en utilisant une application .NET :

1. Créez une application console Visual C# :

   	![][20]

2. Ajoutez une référence au Kit de développement logiciel (SDK) Azure Service Bus à l'aide du <a href="http://nuget.org/packages/WindowsAzure.ServiceBus/">package NuGet WindowsAzure.ServiceBus</a>. Dans le menu principal de Visual Studio, cliquez sur **Outils**, sur **Library Package Manager**, puis sur **Console du gestionnaire de package**. Dans la fenêtre de la console, tapez :

        Install-Package WindowsAzure.ServiceBus

    et appuyez sur Entrée.

2. Ouvrez le fichier Program.cs et ajoutez l'instruction using suivante :

        using Microsoft.ServiceBus.Notifications;

3. Dans votre classe `Program`, ajoutez la méthode suivante :

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            await hub.SendGcmNativeNotificationAsync("{ \"data\" : {\"msg\":\"Hello from Azure!\"}}");
        }

4. Ajoutez ensuite les lignes suivantes dans votre méthode Main :

         SendNotificationAsync();
		 Console.ReadLine();

5. Appuyez sur la touche F5 pour exécuter l'application. Vous devez recevoir une notification toast.

   	![][21]

Pour envoyer une notification en utilisant un service mobile, suivez les instructions de la rubrique [Prise en main de Mobile Services], puis :

1. Connectez-vous au [portail de gestion Azure] et sélectionnez votre service mobile.

2. Sélectionnez l'onglet **Scheduler** dans la partie supérieure.

   	![][22]

3. Créez un travail planifié, insérez un nom, puis sélectionnez **À la demande**.

   	![][23]

4. Lorsque le travail est créé, cliquez sur son nom. Cliquez ensuite sur l'onglet **Script** dans la barre supérieure.

5. Insérez le script suivant dans votre fonction Scheduler. Remplacez les espaces réservés par le nom de votre concentrateur de notification et la chaîne de connexion pour *DefaultFullSharedAccessSignature* obtenue précédemment. Cliquez sur **Enregistrer**.

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

6. Cliquez sur **Exécuter une fois** sur la barre inférieure. Vous devez recevoir une notification toast.

## <a name="next-steps"> </a>Étapes suivantes

Dans cet exemple simple, vous avez envoyé des notifications à tous vos appareils Android. Afin de cibler des utilisateurs spécifiques, consultez le didacticiel [Utilisation de Notification Hubs pour envoyer des notifications Push aux utilisateurs] et, si vous souhaitez segmenter vos utilisateurs par groupes d'intérêt, vous pouvez lire [Utilisation de Notification Hubs pour envoyer les dernières nouvelles]. Pour plus d'informations sur l'utilisation de Notification Hubs, consultez les pages [Recommandations relatives à Notification Hubs] et [Procédures Notification Hubs pour Android].

<!-- Anchors. -->
[Activation de Google Cloud Messaging]: #register
[Configuration de Notification Hub]: #configure-hub
[Connexion de votre application au concentrateur de notification]: #connecting-app
[Exécution de votre application avec l'émulateur]: #run-app
[Envoi de notifications à partir de votre serveur principal]: #send
[Étapes suivantes]:#next-steps

<!-- Images. -->
[1]: ./media/partner-xamarin-notification-hubs-android-get-started/mobile-services-google-developers.png
[2]: ./media/partner-xamarin-notification-hubs-android-get-started/mobile-services-google-create-server.png
[3]: ./media/partner-xamarin-notification-hubs-android-get-started/mobile-services-google-create-server2.png
[4]: ./media/partner-xamarin-notification-hubs-android-get-started/mobile-services-google-create-server3.png

[7]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-from-portal.png
[8]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-from-portal2.png
[9]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-select-from-portal.png
[10]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-select-from-portal2.png
[11]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-configure-android.png
[12]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-connection-strings.png

[13]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-app1.png
[14]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-app2.png
[15]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-app3.png

[18]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-android-app7.png
[19]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-android-app8.png

[20]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-console-app.png
[21]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-android-toast.png
[22]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-scheduler1.png
[23]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-scheduler2.png

<!-- URLs. -->
[Page Soumettre une application]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Mes Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Kit de développement logiciel (SDK) Live pour Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Prise en main de Mobile Services]: /develop/mobile/tutorials/get-started-xamarin-android/#create-new-service
[JavaScript et HTML]: /develop/mobile/tutorials/get-started-with-push-js

[Portail de gestion Azure]: https://manage.windowsazure.com/
[objet wns]: http://go.microsoft.com/fwlink/p/?LinkId=260591
[Recommandations relatives à Notification Hubs]: http://msdn.microsoft.com/library/jj927170.aspx
[Procédures Notification Hubs pour Android]: http://msdn.microsoft.com/library/dn282661.aspx

[Utilisation de Notification Hubs pour envoyer des notifications Push aux utilisateurs]: /manage/services/notification-hubs/notify-users-aspnet
[Utilisation de Notification Hubs pour envoyer les dernières nouvelles]: /manage/services/notification-hubs/breaking-news-dotnet
[Page du composant GCMClient]: http://components.xamarin.com/view/GCMClient
[Page GitHub Xamarin.NotificationHub]: https://github.com/SaschaDittmann/Xamarin.NotificationHub
[GitHub]: http://go.microsoft.com/fwlink/p/?LinkId=331329
[Xamarin.Android]: http://xamarin.com/download/
[Composant Azure Mobile Services]: http://components.xamarin.com/view/azure-mobile-services/
[Composant Client Google Cloud Messaging]: http://components.xamarin.com/view/GCMClient/
[Composant Azure Messaging]: http://components.xamarin.com/view/azure-messaging

<!--HONumber=49-->