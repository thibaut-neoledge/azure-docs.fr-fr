<properties 
	pageTitle="Ajouter des notifications Push à votre application Xamarin.Forms à l’aide d’Azure App Service" 
	description="Découvrez comment utiliser Azure App Service pour envoyer des notifications Push à votre application Xamarin.Forms." 
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
	ms.date="12/19/2015" 
	ms.author="wesmc"/>

# Ajout de notifications Push à votre application Xamarin.Forms

[AZURE.INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]
&nbsp;  
[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

##Vue d'ensemble

Ce didacticiel est basé sur le didacticiel [Démarrage rapide de Xamarin.Forms](app-service-mobile-xamarin-forms-get-started.md), que vous devez effectuer en premier. Vous ajouterez la prise en charge des notifications Push pour chacun des projets que vous souhaitez prendre en charge dans le projet de démarrage rapide Xamarin.Forms. Chaque fois qu’un enregistrement est inséré, une notification Push est transmise.

Si vous n’utilisez pas le projet de serveur du démarrage rapide téléchargé, vous devez ajouter le package d’extension de notification Push à votre projet. Pour plus d'informations sur les packages d'extension de serveur, consultez [Fonctionnement avec le Kit de développement logiciel (SDK) du serveur principal .NET pour Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

Le [simulateur iOS ne prend pas en charge les notifications push](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/iOS_Simulator_Guide/TestingontheiOSSimulator.html), vous devez donc utiliser un appareil iOS physique. Vous devrez également vous inscrire au [programme pour développeurs Apple](https://developer.apple.com/programs/ios/).

##Configuration requise

Pour réaliser ce didacticiel, vous avez besoin des éléments suivants :

* Un compte Azure actif. Si vous n’avez pas encore un compte, inscrivez-vous pour obtenir une version d’évaluation Azure et jusqu’à 10 applications mobiles gratuites. Vous pouvez continuer à les utiliser même après la fin de votre période d’évaluation. Consultez [Version d'évaluation gratuite d'Azure](https://azure.microsoft.com/pricing/free-trial/).

* Un Mac sur lequel sont installés [Xamarin Studio] et [Xcode] v4.4 ou version ultérieure. Vous pouvez exécuter l’application Xamarin.Forms à l’aide de Visual Studio sur votre ordinateur Windows si vous le souhaitez, mais c’est un peu plus compliqué, car vous devez vous connecter à un Mac en réseau exécutant l’hôte de build Xamarin.iOS. Si vous souhaitez effectuer cette opération, consultez la rubrique [Installation de Xamarin.iOS sur Windows].

* Un appareil iOS physique. Les notifications Push ne sont pas prises en charge par le simulateur iOS.

* Terminez le [didacticiel de démarrage rapide Xamarin.Forms](app-service-mobile-xamarin-forms-get-started.md).

##Créer un Concentrateur de notification De base pour votre application mobile

Pour configurer l’envoi des notifications sur votre application, créez un Notification Hub et configurez-le pour les services de notification de plateforme que vous utiliserez.

Cette procédure vous amène à créer un Notification Hub. Si vous en avez déjà un, il vous suffit de le sélectionner.

1. Connectez-vous au [portail Azure](https://portal.azure.com/). Cliquez sur **Parcourir** > **Applications mobiles** > votre serveur principal > **Paramètres** > **Mobile** > **Notifications Push** > **Hub de notification** > **+ Hub de notification**, fournissez un nom et un espace de noms associés à un nouveau Notification Hub, puis cliquez sur le bouton **OK**.

	![](./media/app-service-mobile-xamarin-ios-get-started-push/mobile-app-configure-notification-hub.png)

2. Dans le panneau Créer un concentrateur de notification, cliquez sur **Créer**.


##Mettre à jour le projet de serveur pour l'envoi de notifications Push

[AZURE.INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]


##(Facultatif) Configurer et exécuter le projet Android

Cette section s’applique à l’exécution du projet Xamarin pour Android. Vous pouvez ignorer cette section si vous n’utilisez pas d’appareils Android.


####Activation de Google Cloud Messaging (GCM)


[AZURE.INCLUDE [mobile-services-enable-google-cloud-messaging](../../includes/mobile-services-enable-google-cloud-messaging.md)]


####Configurer le Notification Hub pour GCM

1. Connectez-vous au [portail Azure](https://portal.azure.com/). Cliquez sur **Parcourir** > **Applications mobiles** > votre application mobile > **Paramètres** > **Notifications Push** > **Google (GCM)**. Collez la clé d’API serveur créée précédemment, puis cliquez sur **Enregistrer**. Votre service est désormais configuré et prêt à fonctionner avec les notifications Push sur Android.

	![](./media/app-service-mobile-xamarin-forms-get-started-push/mobile-app-save-gcm-api-key.png)


####Ajouter les notifications Push au projet Android

1. Cliquez avec le bouton droit sur le dossier Composants, cliquez sur Obtenir d’autres composants, recherchez le composant **Client Google Cloud Messaging** et ajoutez-le au projet. Ce composant facilite l’utilisation des notifications Push avec un projet Android Xamarin.

2. Ouvrez le fichier projet MainActivity.cs et ajoutez l’instruction using suivante au début du fichier :

		using Gcm.Client;

3. Ajoutez le code suivant à la méthode `OnCreate` après l’appel à `LoadApplication`.

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
	        CreateAndShowDialog("There was an error creating the Mobile Service. Verify the URL", "Error");
	    }
	    catch (Exception e)
	    {
	        CreateAndShowDialog(e.Message, "Error");
	    }


4. Ajoutez le code suivant pour la méthode d’assistance `CreateAndShowDialog`.

		private void CreateAndShowDialog(String message, String title) 
		{
			AlertDialog.Builder builder = new AlertDialog.Builder(this);

			builder.SetMessage (message);
			builder.SetTitle (title);
			builder.Create().Show ();
		}


5. Dans la classe `MainActivity`, ajoutez le code suivant pour exposer l’élément `MainActivity` actuel, de manière à ce que nous puissions exécuter certains éléments d’interface utilisateur sur le thread principal d’interface utilisateur :
		
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

6. Initialisez l’élément `instance`, variable au début de la méthode `MainActivity.OnCreate`.

		// Set the current instance of MainActivity.
		instance = this;

7. Ajoutez un nouveau fichier de classe au projet **Android**. Nommez le nouveau fichier de classe **GcmService**.

8. Assurez-vous que les instructions `using` suivantes sont incluses au début du fichier.

		using Gcm.Client;
		using Microsoft.WindowsAzure.MobileServices;
		using Android.App;
		using Android.Content;
		using System.Collections.Generic;
		using System.Diagnostics;
		using Android.Util;
		using Newtonsoft.Json.Linq;
		using System.Text;
		using System.Linq;

9. Ajoutez les demandes d’autorisation suivantes au début du fichier, après les instructions `using` et avant la déclaration `namespace`.

		[assembly: Permission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
		[assembly: UsesPermission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
		[assembly: UsesPermission(Name = "com.google.android.c2dm.permission.RECEIVE")]
		
		//GET_ACCOUNTS is only needed for android versions 4.0.3 and below
		[assembly: UsesPermission(Name = "android.permission.GET_ACCOUNTS")]
		[assembly: UsesPermission(Name = "android.permission.INTERNET")]
		[assembly: UsesPermission(Name = "android.permission.WAKE_LOCK")]

10. Ajoutez la définition de classe suivante à l’espace de noms. Remplacez **<PROJECT_NUMBER>** par le numéro de projet noté précédemment.

		[BroadcastReceiver(Permission = Gcm.Client.Constants.PERMISSION_GCM_INTENTS)]
		[IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_MESSAGE }, Categories = new string[] { "@PACKAGE_NAME@" })]
		[IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_REGISTRATION_CALLBACK }, Categories = new string[] { "@PACKAGE_NAME@" })]
		[IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_LIBRARY_RETRY }, Categories = new string[] { "@PACKAGE_NAME@" })]		
		public class PushHandlerBroadcastReceiver : GcmBroadcastReceiverBase<GcmService>
		{		
		    public static string[] SENDER_IDS = new string[] { "<PROJECT_NUMBER>" };		
		}

11. Mettez à jour votre classe `GcmService` afin d’utiliser le nouveau récepteur de diffusion.

		 [Service]
		 public class GcmService : GcmServiceBase
		 {
		     public static string RegistrationID { get; private set; }
		
		     public GcmService()
		         : base(PushHandlerBroadcastReceiver.SENDER_IDS){}
		 }


12. Ajoutez le code suivant à la classe GcmService qui remplace le gestionnaire d’événements OnRegistered et implémente une méthode `Register`.

	Ce code inscrira un corps de modèle pour recevoir les notifications de modèle à l’aide du paramètre `messageParam`. Les notifications de modèle vous permettent d’envoyer des notifications multiplateformes. Pour plus d’informations, consultez [Modèles](https://msdn.microsoft.com/library/azure/dn530748.aspx).
		
		protected override void OnRegistered(Context context, string registrationId)
		{
		    Log.Verbose("PushHandlerBroadcastReceiver", "GCM Registered: " + registrationId);
		    RegistrationID = registrationId;
		
		    createNotification("GcmService Registered...", "The device has been Registered, Tap to View!");
		
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

13. Vous devez implémenter `OnMessage` pour gérer une notification Push entrante. Dans ce code, nous allons gérer la notification et l’envoyer au gestionnaire de notifications.

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
	
14. Vous devez également implémenter des gestionnaires `OnUnRegistered` et `OnError` pour le récepteur.

		protected override void OnUnRegistered(Context context, string registrationId)
		{
			Log.Error("PushHandlerBroadcastReceiver", "Unregistered RegisterationId : " + registrationId);
		}

		protected override void OnError(Context context, string errorId)
		{
			Log.Error("PushHandlerBroadcastReceiver", "GCM Error: " + errorId);
		}



####Tester les notifications Push dans votre application Android

1. Dans Visual Studio ou Xamarin Studio, cliquez avec le bouton droit sur le projet **Android**, puis cliquez sur **Définir comme projet de démarrage**.
 
2. Cliquez sur le bouton **Exécuter** pour générer le projet et démarrer l'application sur un appareil compatible iOS, puis cliquez sur **OK** pour accepter les notifications Push.
	
	> [AZURE.NOTE] Vous devez accepter explicitement les notifications Push de votre application. Cette demande s’effectue uniquement lors du premier démarrage de l’application.

2. Dans l’application, tapez une tâche, puis cliquez sur l’icône plus (**+**).

3. Vérifiez que vous avez reçu une notification, puis cliquez sur **OK** pour fermer celle-ci.





##(Facultatif) Configurer et exécuter le projet iOS

Cette section est dédiée à l’exécution du projet Xamarin iOS pour les appareils iOS. Vous pouvez ignorer cette section si vous n’utilisez pas d’appareils iOS.

[AZURE.INCLUDE [Concentrateur de notification de base Xamarin - Activation des notifications Push Apple](../../includes/notification-hubs-xamarin-enable-apple-push-notifications.md)]


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

2. Cliquez sur le bouton **Exécuter** ou sur **F5** dans Visual Studio afin de développer le projet et de démarrer l’application dans un appareil compatible iOS, puis cliquez sur **OK** afin d’accepter les notifications Push.
	
	> [AZURE.NOTE] Vous devez accepter explicitement les notifications Push de votre application. Cette demande s’effectue uniquement lors du premier démarrage de l’application.

3. Dans l’application, tapez une tâche, puis cliquez sur l’icône plus (**+**).

4. Vérifiez que vous avez reçu une notification, puis cliquez sur **OK** pour fermer celle-ci.




##(Facultatif) Configurer et exécuter le projet Windows

Cette section s’applique à l’exécution du projet WinApp Xamarin pour les appareils Windows. Vous pouvez ignorer cette section si vous n’utilisez pas d’appareils Windows.


####Inscrire votre application Windows pour les notifications Push avec WNS

[AZURE.INCLUDE [app-service-mobile-register-wns](../../includes/app-service-mobile-register-wns.md)]


####Configurer le Notification Hub pour WNS

[AZURE.INCLUDE [app-service-mobile-configure-wns](../../includes/app-service-mobile-configure-wns.md)]


####Ajouter des notifications Push à votre application Windows

1. Dans Visual Studio, ouvrez **App.xaml.cs** dans le projet **WinApp**. Ajoutez les instructions `using` suivantes.

		using System.Threading.Tasks;
		using Windows.Networking.PushNotifications;
		using WesmcMobileAppGaTest;
		using Microsoft.WindowsAzure.MobileServices;
		using Newtonsoft.Json.Linq;

2. Dans App.xaml.cs, ajoutez la méthode `InitNotificationsAsync` suivante. Cette méthode récupère le canal des notifications Push et inscrit un modèle pour recevoir les notifications de modèle à partir du Notification Hub. Un modèle de notification prenant en charge `messageParam` sera transmis à ce client.

        private async Task InitNotificationsAsync()
        {
            var channel = await PushNotificationChannelManager
                .CreatePushNotificationChannelForApplicationAsync();

            const string templateBodyWNS = "<toast><visual><binding template="ToastText01"><text id="1">$(messageParam)</text></binding></visual></toast>";

            JObject headers = new JObject();
            headers["X-WNS-Type"] = "wns/toast";

            JObject templates = new JObject();
            templates["genericMessage"] = new JObject
                {
                  {"body", templateBodyWNS},
                  {"headers", headers} // Only needed for WNS & MPNS
                };

            await TodoItemManager.DefaultManager.CurrentClient.GetPush().RegisterAsync(channel.Uri, templates);
        }

3. Dans App.xaml.cs, mettez à jour le gestionnaire d’événements `OnLaunched` avec l’attribut `async`, puis appelez `InitNotificationsAsync`.

        protected async override void OnLaunched(LaunchActivatedEventArgs e)
        {
            Frame rootFrame = Window.Current.Content as Frame;

            // Do not repeat app initialization when the Window already has content,
            // just ensure that the window is active
            if (rootFrame == null)
            {
                // Create a Frame to act as the navigation context and navigate to the first page
                rootFrame = new Frame();
                // Set the default language
                rootFrame.Language = Windows.Globalization.ApplicationLanguages.Languages[0];
                rootFrame.NavigationFailed += OnNavigationFailed;
                Xamarin.Forms.Forms.Init(e); 
                if (e.PreviousExecutionState == ApplicationExecutionState.Terminated)
                {
                    //TODO: Load state from previously suspended application
                }
                // Place the frame in the current Window
                Window.Current.Content = rootFrame;
            }

            if (rootFrame.Content == null)
            {
                // When the navigation stack isn't restored navigate to the first page,
                // configuring the new page by passing required information as a navigation
                // parameter
                rootFrame.Navigate(typeof(MainPage), e.Arguments);
            }
            // Ensure the current window is active
            Window.Current.Activate();

            await InitNotificationsAsync();
        }

4. Dans l’Explorateur de solutions pour Visual Studio, ouvrez le fichier **Package.appxmanifest**, puis définissez **Compatible toast** sur **Oui** sous **Notifications**.

5. Générez l’application et vérifiez l’absence d’erreurs. Votre application cliente doit désormais s’inscrire pour les notifications de modèle du serveur principal d’application mobile.


####Tester les notifications Push dans votre application Windows

1. Dans Visual Studio, cliquez avec le bouton droit sur le projet **WinApp**, puis cliquez sur **Définir comme projet de démarrage**.


2. Cliquez sur le bouton **Exécuter** pour générer le projet et démarrer l'application sur un appareil compatible iOS, puis cliquez sur **OK** pour accepter les notifications Push.
	
	> [AZURE.NOTE] Vous devez accepter explicitement les notifications Push de votre application. Cette demande s’effectue uniquement lors du premier démarrage de l’application.

3. Dans l’application, tapez une tâche, puis cliquez sur l’icône plus (**+**).

4. Vérifiez que vous avez reçu une notification, puis cliquez sur **OK** pour fermer celle-ci.



<!-- Images. -->

<!-- URLs. -->
[Xamarin Studio]: http://xamarin.com/platform
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Xcode]: https://go.microsoft.com/fwLink/?LinkID=266532
[Installation de Xamarin.iOS sur Windows]: http://developer.xamarin.com/guides/ios/getting_started/installation/windows/
[apns object]: http://go.microsoft.com/fwlink/p/?LinkId=272333

<!---HONumber=AcomDC_0128_2016-->