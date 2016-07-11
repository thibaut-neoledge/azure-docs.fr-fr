<properties
	pageTitle="Ajout de notifications Push à votre application Xamarin.Forms | Microsoft Azure"
	description="Découvrez comment utiliser les services Azure pour envoyer des notifications Push multiplateforme à vos applications Xamarin.Forms."
	services="app-service\mobile"
	documentationCenter="xamarin"
	authors="wesmc7777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-xamarin"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="06/20/2016"
	ms.author="wesmc"/>

# Ajout de notifications Push à votre application Xamarin.Forms

[AZURE.INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

##Vue d'ensemble

Ce didacticiel vous montre comment utiliser les services Azure pour envoyer des notifications Push aux applications Xamarin.Forms exécutées sur différentes plateformes d’appareils natives, Android, iOS et Windows. Les notifications Push sont envoyées à partir d’un serveur principal d’applications mobiles Azure à l’aide d’Azure Notification Hubs. Les inscriptions de modèle sont utilisées afin que le même message puisse être envoyé aux appareils exécutés sur toutes les plateformes à l’aide des différents services de notifications Push (PNS). Pour plus d’informations sur l’envoi des notifications Push multiplateforme, consultez la documentation [Azure Notification Hubs](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md).

Vous ajoutez des notifications Push à chaque projet pris en charge par votre application Xamarin.Forms. Une notification Push est transmise à chaque fois qu’un enregistrement est inséré dans le serveur principal.

##Composants requis

Pour obtenir les meilleurs résultats avec ce didacticiel, nous vous recommandons de commencer par suivre le didacticiel [Créer une application Xamarin.Forms](app-service-mobile-xamarin-forms-get-started.md). Après avoir terminé ce didacticiel, vous disposerez d’un projet Xamarin.Forms qui est une application TodoList multiplateforme.

Si vous n’utilisez pas le projet de serveur du démarrage rapide téléchargé, vous devez ajouter le package d’extension de notification Push à votre projet. Pour plus d'informations sur les packages d'extension de serveur, consultez [Fonctionnement avec le Kit de développement logiciel (SDK) du serveur principal .NET pour Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

L’envoi de notifications Push aux appareils iOS requiert [l’abonnement au programme pour développeurs Apple](https://developer.apple.com/programs/ios/). Vous devez également utiliser un appareil iOS physique, car le [simulateur iOS ne prend pas en charge les notifications Push](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/iOS_Simulator_Guide/TestingontheiOSSimulator.html).

##<a name="create-hub"></a>Créer un hub de notification

[AZURE.INCLUDE [app-service-mobile-create-notification-hub](../../includes/app-service-mobile-create-notification-hub.md)]

##Mettre à jour le projet de serveur pour l'envoi de notifications Push

[AZURE.INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]


##(Facultatif) Configurer et exécuter le projet Android

Terminez cette section pour activer les notifications Push pour le projet Android Xamarin.Forms pour Android.


###Activation de Google Cloud Messaging (GCM)

[AZURE.INCLUDE [mobile-services-enable-google-cloud-messaging](../../includes/mobile-services-enable-google-cloud-messaging.md)]

###Configurer le serveur principal d’application mobile pour l’envoi de demandes de notifications Push à l’aide de GCM

[AZURE.INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push.md)]

###Ajouter les notifications Push au projet Android

Avec le serveur principal configuré pour utiliser Google Cloud Messaging (GCM), nous pouvons ajouter les composants et le code au client qui permet à l’application de s’inscrire auprès de GCM, de s’inscrire pour les notifications Push avec Azure Notification Hubs via le serveur principal d’applications mobiles et de recevoir des notifications.

1. Dans le projet **Android**, cliquez avec le bouton droit sur le dossier **Composants**, cliquez sur **Obtenir d’autres composants…**, recherchez le composant **Client Google Cloud Messaging** et ajoutez-le au projet. Ce composant prend en charge les notifications Push pour un projet Xamarin Android.


2. Ouvrez le fichier projet MainActivity.cs et ajoutez l’instruction using suivante au début du fichier :

		using Gcm.Client;

3. Ajoutez le code suivant à la méthode **OnCreate** après l’appel à **LoadApplication** :

	    try
	    {
	        // Check to ensure everything's setup right
	        GcmClient.CheckDevice(this);
	        GcmClient.CheckManifest(this);

	        // Register for push notifications
	        System.Diagnostics.Debug.WriteLine("Registering...");
	        GcmClient.Register(this, PushHandlerBroadcastReceiver.SENDER_IDS);
	    }
	    catch (Java.Net.MalformedURLException)
	    {
	        CreateAndShowDialog("There was an error creating the client. Verify the URL.", "Error");
	    }
	    catch (Exception e)
	    {
	        CreateAndShowDialog(e.Message, "Error");
	    }


4. Ajoutez une nouvelle méthode d’assistance **CreateAndShowDialog**, comme suit :

		private void CreateAndShowDialog(String message, String title)
		{
			AlertDialog.Builder builder = new AlertDialog.Builder(this);

			builder.SetMessage (message);
			builder.SetTitle (title);
			builder.Create().Show ();
		}


5. Ajoutez le code suivant à la classe **MainActivity** :

		// Create a new instance field for this activity.
		static MainActivity instance = null;

		// Return the current activity instance.
		public static MainActivity CurrentActivity
		{
		    get
		    {
		        return instance;
		    }
		}

	Cela expose l’instance **MainActivity** actuelle afin que nous puissions exécuter sur le thread de l’interface utilisateur principale.

6. Initialisez l’élément `instance`, la variable au début de la méthode **OnCreate**, comme suit.

		// Set the current instance of MainActivity.
		instance = this;

2. Ajoutez un nouveau fichier de classe au projet **Android** nommé `GcmService.cs`, et assurez-vous que les instructions **using** suivantes figurent en haut du fichier :

		using Android.App;
		using Android.Content;
		using Android.Media;
		using Android.Support.V4.App;
		using Android.Util;
		using Gcm.Client;
		using Microsoft.WindowsAzure.MobileServices;
		using Newtonsoft.Json.Linq;
		using System;
		using System.Collections.Generic;
		using System.Diagnostics;
		using System.Text;


9. Ajoutez les demandes d’autorisation suivantes au début du fichier, après les instructions **using** et avant la déclaration **d’espace de noms**.

		[assembly: Permission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
		[assembly: UsesPermission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
		[assembly: UsesPermission(Name = "com.google.android.c2dm.permission.RECEIVE")
		[assembly: UsesPermission(Name = "android.permission.INTERNET")]
		[assembly: UsesPermission(Name = "android.permission.WAKE_LOCK")]
		//GET_ACCOUNTS is only needed for android versions 4.0.3 and below
		[assembly: UsesPermission(Name = "android.permission.GET_ACCOUNTS")]

10. Ajoutez la définition de classe suivante à l’espace de noms.

		[BroadcastReceiver(Permission = Gcm.Client.Constants.PERMISSION_GCM_INTENTS)]
		[IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_MESSAGE }, Categories = new string[] { "@PACKAGE_NAME@" })]
		[IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_REGISTRATION_CALLBACK }, Categories = new string[] { "@PACKAGE_NAME@" })]
		[IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_LIBRARY_RETRY }, Categories = new string[] { "@PACKAGE_NAME@" })]
		public class PushHandlerBroadcastReceiver : GcmBroadcastReceiverBase<GcmService>
		{
		    public static string[] SENDER_IDS = new string[] { "<PROJECT_NUMBER>" };
		}

	>[AZURE.NOTE]Remplacez **<PROJECT\_NUMBER>** par le numéro de projet noté précédemment.

11. Remplacez la classe **GcmService** vide par le code suivant, qui utilise le nouveau récepteur de diffusion :

		 [Service]
		 public class GcmService : GcmServiceBase
		 {
		     public static string RegistrationID { get; private set; }

		     public GcmService()
		         : base(PushHandlerBroadcastReceiver.SENDER_IDS){}
		 }


12. Ajoutez le code suivant à la classe **GcmService** qui remplace le gestionnaire d’événements **OnRegistered** et implémente une méthode **Register**.

		protected override void OnRegistered(Context context, string registrationId)
		{
		    Log.Verbose("PushHandlerBroadcastReceiver", "GCM Registered: " + registrationId);
		    RegistrationID = registrationId;

            var push = TodoItemManager.DefaultManager.CurrentClient.GetPush();

		    MainActivity.CurrentActivity.RunOnUiThread(() => Register(push, null));
		}

        public async void Register(Microsoft.WindowsAzure.MobileServices.Push push, IEnumerable<string> tags)
        {
            try
            {
                const string templateBodyGCM = "{"data":{"message":"$(messageParam)"}}";

                JObject templates = new JObject();
                templates["genericMessage"] = new JObject
                {
                	{"body", templateBodyGCM}
                };

                await push.RegisterAsync(RegistrationID, templates);
                Log.Info("Push Installation Id", push.InstallationId.ToString());
            }
            catch (Exception ex)
            {
                System.Diagnostics.Debug.WriteLine(ex.Message);
                Debugger.Break();
            }
        }

		Note that this code uses the `messageParam` parameter in the template registration. 

13. Ajoutez le code suivant qui implémente **OnMessage** :

		protected override void OnMessage(Context context, Intent intent)
		{
		    Log.Info("PushHandlerBroadcastReceiver", "GCM Message Received!");

		    var msg = new StringBuilder();

		    if (intent != null && intent.Extras != null)
		    {
		        foreach (var key in intent.Extras.KeySet())
		            msg.AppendLine(key + "=" + intent.Extras.Get(key).ToString());
		    }

		    //Store the message
		    var prefs = GetSharedPreferences(context.PackageName, FileCreationMode.Private);
		    var edit = prefs.Edit();
		    edit.PutString("last_msg", msg.ToString());
		    edit.Commit();

		    string message = intent.Extras.GetString("message");
		    if (!string.IsNullOrEmpty(message))
		    {
		        createNotification("New todo item!", "Todo item: " + message);
		        return;
		    }

		    string msg2 = intent.Extras.GetString("msg");
		    if (!string.IsNullOrEmpty(msg2))
		    {
		        createNotification("New hub message!", msg2);
		        return;
		    }

		    createNotification("Unknown message details", msg.ToString());
		}

        void createNotification(string title, string desc)
        {
            //Create notification
            var notificationManager = GetSystemService(Context.NotificationService) as NotificationManager;

            //Create an intent to show ui
            var uiIntent = new Intent(this, typeof(MainActivity));

            //Use Notification Builder
            NotificationCompat.Builder builder = new NotificationCompat.Builder(this);

            //Create the notification
            //we use the pending intent, passing our ui intent over which will get called
            //when the notification is tapped.
            var notification = builder.SetContentIntent(PendingIntent.GetActivity(this, 0, uiIntent, 0))
                    .SetSmallIcon(Android.Resource.Drawable.SymActionEmail)
                    .SetTicker(title)
                    .SetContentTitle(title)
                    .SetContentText(desc)

                    //Set the notification sound
                    .SetSound(RingtoneManager.GetDefaultUri(RingtoneType.Notification))

                    //Auto cancel will remove the notification once the user touches it
                    .SetAutoCancel(true).Build();

            //Show the notification
            notificationManager.Notify(1, notification);
        }

	Cela gère les notifications entrantes et les envoie dans le Gestionnaire de notifications pour les afficher.

14. **GcmServiceBase** requiert également la mise en œuvre des méthodes d’assistance **OnUnRegistered** et **OnError** que vous pouvez effectuer comme suit :

		protected override void OnUnRegistered(Context context, string registrationId)
		{
			Log.Error("PushHandlerBroadcastReceiver", "Unregistered RegisterationId : " + registrationId);
		}

		protected override void OnError(Context context, string errorId)
		{
			Log.Error("PushHandlerBroadcastReceiver", "GCM Error: " + errorId);
		}

Vous êtes maintenant prêt à tester les notifications Push dans l’application exécutée sur un appareil Android ou sur l’émulateur.

###Tester les notifications Push dans votre application Android

Les deux premières étapes sont requises uniquement lorsque vous testez sur un émulateur.

1. Assurez-vous de procéder au déploiement ou au débogage sur un périphérique virtuel sur lequel les API Google sont définis comme cible, comme indiqué dans le Gestionnaire d’appareil virtuel Android (AVD).

2. Ajoutez un compte Google à l’appareil Android en cliquant sur **Applications** > **Paramètres** > **Ajouter un compte**, puis suivez les invites pour utiliser Ajouter un compte Google existant à l’appareil pour en créer un nouveau.

1. Dans Visual Studio ou Xamarin Studio, cliquez avec le bouton droit sur le projet **Android**, puis cliquez sur **Définir comme projet de démarrage**.

2. Appuyez sur le bouton **Exécuter** pour générer le projet et lancer l’application sur votre appareil ou émulateur Android.

3. Dans l’application, tapez une tâche, puis cliquez sur l’icône plus (**+**).

4. Vérifiez qu’une notification est reçue lorsqu’un élément est ajouté.


##(Facultatif) Configurer et exécuter le projet iOS

Cette section est dédiée à l’exécution du projet Xamarin iOS pour les appareils iOS. Vous pouvez ignorer cette section si vous n’utilisez pas d’appareils iOS.

[AZURE.INCLUDE [notification-hubs-xamarin-enable-apple-push-notifications](../../includes/notification-hubs-xamarin-enable-apple-push-notifications.md)]


####Configurer le Notification Hub pour APNS

1. Connectez-vous au [portail Azure](https://portal.azure.com/). Cliquez sur **Parcourir** > **Mobile Apps** > votre application mobile > **Paramètres** > **Notifications Push** > **Apple (APNS)** > **Télécharger un certificat**. Chargez le fichier de certificat Push p12 exporté plus tôt. Veillez à sélectionner **Bac à sable (sandbox)** si vous avez créé un certificat Push de développement pour le développement et le test. Sinon, sélectionnez **Production**. Votre service est désormais configuré et prêt à fonctionner avec les notifications Push sur iOS.

	![](./media/app-service-mobile-xamarin-ios-get-started-push/mobile-app-upload-apns-cert.png)


	Ensuite, vous avez configurer le paramètre de projet iOS dans Xamarin Studio ou Visual Studio.

[AZURE.INCLUDE [app-service-mobile-xamarin-ios-configure-project](../../includes/app-service-mobile-xamarin-ios-configure-project.md)]


####Ajout de notifications Push à votre application iOS

1. Ajoutez l’instruction `using` suivante au début du fichier **AppDelegate.cs**.

        using Microsoft.WindowsAzure.MobileServices;
		using Newtonsoft.Json.Linq;


2. Dans le projet iOS, ouvrez AppDelegate.cs et mettez à jour`FinishedLaunching` pour prendre en charge des notifications à distance comme suit.

		public override bool FinishedLaunching (UIApplication app, NSDictionary options)
		{
			global::Xamarin.Forms.Forms.Init ();

			Microsoft.WindowsAzure.MobileServices.CurrentPlatform.Init();

            // IMPORTANT: uncomment this code to enable sync on Xamarin.iOS
            // For more information, see: http://go.microsoft.com/fwlink/?LinkId=620342
            //SQLitePCL.CurrentPlatform.Init();

            // registers for push for iOS8
            var settings = UIUserNotificationSettings.GetSettingsForTypes(
                UIUserNotificationType.Alert
                | UIUserNotificationType.Badge
                | UIUserNotificationType.Sound,
                new NSSet());

            UIApplication.SharedApplication.RegisterUserNotificationSettings(settings);
            UIApplication.SharedApplication.RegisterForRemoteNotifications();

			LoadApplication (new App ());

			return base.FinishedLaunching (app, options);
		}


4. Dans AppDelegate.cs, ajoutez également un remplacement pour l’événement **RegisteredForRemoteNotifications** afin de vous inscrire pour les notifications :

        public override void RegisteredForRemoteNotifications(UIApplication application, NSData deviceToken)
        {
            const string templateBodyAPNS = "{"aps":{"alert":"$(messageParam)"}}";

            JObject templates = new JObject();
            templates["genericMessage"] = new JObject
                {
                  {"body", templateBodyAPNS}
                };

            // Register for push with your mobile app
            Push push = TodoItemManager.DefaultManager.CurrentClient.GetPush();
            push.RegisterAsync(deviceToken, templates);
        }

5. Dans AppDelegate.cs, ajoutez également un remplacement pour l’événement **DidReceivedRemoteNotification** afin de traiter les notifications entrantes pendant l’exécution de l’application :

        public override void DidReceiveRemoteNotification(UIApplication application, NSDictionary userInfo, Action<UIBackgroundFetchResult> completionHandler)
        {
            NSDictionary aps = userInfo.ObjectForKey(new NSString("aps")) as NSDictionary;

            string alert = string.Empty;
            if (aps.ContainsKey(new NSString("alert")))
                alert = (aps[new NSString("alert")] as NSString).ToString();

            //show alert
            if (!string.IsNullOrEmpty(alert))
            {
                UIAlertView avAlert = new UIAlertView("Notification", alert, null, "OK", null);
                avAlert.Show();
            }
        }

L’application est mise à jour et prend en charge les notifications Push.

####Tester les notifications Push dans votre application iOS

1. Cliquez avec le bouton droit sur le projet iOS, puis cliquez sur **Définir comme projet de démarrage**.

2. Cliquez sur le bouton **Exécuter** ou sur **F5** dans Visual Studio afin de développer le projet et de démarrer l’application dans un appareil iOS, puis cliquez sur **OK** afin d’accepter les notifications Push.

	> [AZURE.NOTE] Vous devez accepter explicitement les notifications Push de votre application. Cette demande s’effectue uniquement lors du premier démarrage de l’application.

3. Dans l’application, tapez une tâche, puis cliquez sur l’icône plus (**+**).

4. Vérifiez que vous avez reçu une notification, puis cliquez sur **OK** pour fermer celle-ci.


##(Facultatif) Configurer et exécuter les projets Windows

Cette section s’applique à l’exécution des projets Xamarin.Forms WinApp et WinPhone81 pour les appareils Windows. Ces étapes prennent également en charge les projets de plateforme Windows universelle (UWP). Vous pouvez ignorer cette section si vous n’utilisez pas d’appareils Windows.


####Inscrire votre application Windows pour les notifications Push avec WNS

[AZURE.INCLUDE [app-service-mobile-register-wns](../../includes/app-service-mobile-register-wns.md)]


####Configurer le Notification Hub pour WNS

[AZURE.INCLUDE [app-service-mobile-configure-wns](../../includes/app-service-mobile-configure-wns.md)]


####Ajouter des notifications Push à votre application Windows

1. Dans Visual Studio, ouvrez le fichier **App.xaml.cs** dans un projet Windows, et ajoutez les instructions **using** suivantes.

		using Newtonsoft.Json.Linq;
		using Microsoft.WindowsAzure.MobileServices;
		using System.Threading.Tasks;
		using Windows.Networking.PushNotifications;
		using <your_TodoItemManager_portable_class_namespace>;

	Remplacez `<your_TodoItemManager_portable_class_namespace>` par l’espace de noms de votre projet portable qui contient la classe `TodoItemManager`.
 

2. Dans le fichier App.xaml.cs, ajoutez la méthode **InitNotificationsAsync** suivante :

        private async Task InitNotificationsAsync()
        {
            var channel = await PushNotificationChannelManager
                .CreatePushNotificationChannelForApplicationAsync();

            const string templateBodyWNS = 
				"<toast><visual><binding template="ToastText01"><text id="1">$(messageParam)</text></binding></visual></toast>";

            JObject headers = new JObject();
            headers["X-WNS-Type"] = "wns/toast";

            JObject templates = new JObject();
            templates["genericMessage"] = new JObject
			{
				{"body", templateBodyWNS},
				{"headers", headers} // Needed for WNS.
			};

            await TodoItemManager.DefaultManager.CurrentClient.GetPush()
				.RegisterAsync(channel.Uri, templates);
        }

	Cette méthode récupère le canal des notifications Push et inscrit un modèle pour recevoir les notifications de modèle à partir de votre Notification Hub. Un modèle de notification prenant en charge *messageParam* sera transmis à ce client.

3. Dans App.xaml.cs, mettez à jour la définition de méthode du gestionnaire d’événements **OnLaunched** en ajoutant le modificateur `async`, puis ajoutez la ligne de code suivante à la fin de la méthode :

        await InitNotificationsAsync();

	Cela permet de s’assurer que l’inscription aux notifications Push est créée ou actualisée à chaque lancement de l’application. Il est important d’effectuer cette opération pour vous assurer que le canal Push WNS est toujours actif.

4. Dans l’Explorateur de solutions pour Visual Studio, ouvrez le fichier **Package.appxmanifest**, puis définissez **Compatible toast** sur **Oui** sous **Notifications**.

5. Générez l’application et vérifiez l’absence d’erreurs. Votre application cliente doit désormais s’inscrire pour les notifications de modèle du serveur principal d’application mobile. Répétez cette section pour chaque projet Windows dans votre solution.


####Tester les notifications Push dans votre application Windows

1. Dans Visual Studio, cliquez avec le bouton droit sur un projet Windows, puis cliquez sur **Définir comme projet de démarrage**.

2. Appuyez sur le bouton **Exécuter** pour générer le projet et démarrer l'application.

3. Dans l’application, tapez un nom pour un nouvel élément todoitem, puis cliquez sur l’icône de signe plus (**+**) pour l’ajouter.

4. Vérifiez qu’une notification est reçue lorsque l’élément est ajouté.

##Étapes suivantes

Apprenez-en plus sur les notifications Push :

* [Utiliser le Kit de développement logiciel (SDK) de serveur principal .NET pour Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#how-to-add-tags-to-a-device-installation-to-enable-push-to-tags) Les balises vous permettent de cibler des clients segmentés avec des notifications Push. Apprenez à ajouter des balises à l’installation d’un appareil.

* [Diagnostiquer les problèmes de notification Push](../notification-hubs/notification-hubs-push-notification-fixer.md) Il existe différentes raisons pour lesquelles les notifications peuvent être perdues ou n’arrivent pas sur les appareils. Cette rubrique vous explique comment analyser et déterminer la cause première des défaillances de notification Push.

Vous pouvez poursuivre avec l’un des didacticiels suivants :

* [Ajouter l’authentification à votre application ](app-service-mobile-xamarin-forms-get-started-users.md) Découvrez comment authentifier les utilisateurs de votre application avec un fournisseur d’identité.

* [Activer la synchronisation hors connexion pour votre application](app-service-mobile-xamarin-forms-get-started-offline-data.md) Apprenez à ajouter une prise en charge hors connexion à votre application à l’aide d’un serveur principal d’applications mobiles. La synchronisation hors connexion permet aux utilisateurs finaux d'interagir avec une application mobile pour afficher, ajouter ou modifier des données, même lorsqu'il n'existe aucune connexion réseau.

<!-- Images. -->

<!-- URLs. -->
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Xcode]: https://go.microsoft.com/fwLink/?LinkID=266532
[apns object]: http://go.microsoft.com/fwlink/p/?LinkId=272333

<!---HONumber=AcomDC_0629_2016-->