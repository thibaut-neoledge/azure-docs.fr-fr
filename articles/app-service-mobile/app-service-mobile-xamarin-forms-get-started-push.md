---
title: "Ajout de notifications Push à votre application Xamarin.Forms | Microsoft Docs"
description: "Découvrez comment utiliser les services Azure pour envoyer des notifications Push multiplateforme à vos applications Xamarin.Forms."
services: app-service\mobile
documentationcenter: xamarin
author: ysxu
manager: syntaxc4
editor: 
ms.assetid: d9b1ba9a-b3f2-4d12-affc-2ee34311538b
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin
ms.devlang: dotnet
ms.topic: article
ms.date: 10/12/2016
ms.author: yuaxu
ms.openlocfilehash: 912367636f1b26b3b07fbd5fe3fe8ed053218fd5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="add-push-notifications-to-your-xamarinforms-app"></a>Ajout de notifications Push à votre application Xamarin.Forms
[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## <a name="overview"></a>Vue d’ensemble
Dans ce didacticiel, vous ajoutez des notifications Push à tous les projets qui résultent du [démarrage rapide Xamarin.Forms](app-service-mobile-xamarin-forms-get-started.md). Cela signifie qu’une notification Push est envoyée à tous les clients inter-plateformes à chaque fois qu’un enregistrement est inséré.

Si vous n’utilisez pas le projet de serveur du démarrage rapide téléchargé, vous devrez ajouter le package d’extension de notification Push. Consultez [Fonctionnement avec le Kit de développement logiciel (SDK) du serveur principal .NET pour Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) pour plus d’informations.

## <a name="prerequisites"></a>Composants requis
Pour iOS, vous avez besoin d’une [appartenance au programme pour développeurs Apple](https://developer.apple.com/programs/ios/) et d’un appareil iOS physique. Les [notifications Push ne sont pas prises en charge par le simulateur iOS](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/iOS_Simulator_Guide/TestingontheiOSSimulator.html).

## <a name="configure-hub"></a>Configurer un hub de notification
[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

## <a name="update-the-server-project-to-send-push-notifications"></a>Mettre à jour le projet de serveur pour l'envoi de notifications Push
[!INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]

## <a name="configure-and-run-the-android-project-optional"></a>Configurer et exécuter le projet Android (facultatif)
Terminez cette section pour activer les notifications Push pour le projet Android Xamarin.Forms pour Android.

### <a name="enable-firebase-cloud-messaging-fcm"></a>Activation de Firebase Cloud Messaging (FCM)
[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

### <a name="configure-the-mobile-apps-back-end-to-send-push-requests-by-using-fcm"></a>Configurer le serveur principal Mobile Apps pour l’envoi de demandes de notifications Push à l’aide de FCM
[!INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push.md)]

### <a name="add-push-notifications-to-the-android-project"></a>Ajouter les notifications Push au projet Android
Une fois le serveur principal configuré avec FCM, vous pouvez ajouter des codes et des composants au client pour vous inscrire auprès de FCM. Vous pouvez également vous inscrire aux notifications Push avec Azure Notification Hubs via le serveur principal Mobile Apps et recevoir des notifications.

1. Dans le projet **Droid**, cliquez avec le bouton droit sur le dossier **Composants**, puis cliquez sur **Get More Components...** (Obtenir davantage de composants). Recherchez ensuite le composant **Client Google Cloud Messaging** et ajoutez-le au projet. Ce composant prend en charge les notifications Push pour un projet Xamarin Android.
2. Ouvrez le fichier de projet MainActivity.cs et ajoutez l’instruction suivante au début du fichier :

        using Gcm.Client;
3. Ajoutez le code suivant à la méthode **OnCreate** après l’appel à **LoadApplication** :

        try
        {
            // Check to ensure everything's set up right
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
4. Ajoutez une nouvelle méthode d’assistance **CreateAndShowDialog** , comme suit :

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

    Cela expose l’instance **MainActivity** actuelle afin que nous puissions l’exécuter sur le thread de l’interface utilisateur principale.
6. Initialisez la variable `instance` au début de la méthode **OnCreate**, comme suit.

        // Set the current instance of MainActivity.
        instance = this;
7. Ajoutez un nouveau fichier de classe au projet **Droid** nommé `GcmService.cs`, et assurez-vous que les instructions **using** suivantes figurent en haut du fichier :

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
8. Ajoutez les demandes d’autorisation suivantes au début du fichier, après les instructions **using** et avant la déclaration **namespace**.

        [assembly: Permission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
        [assembly: UsesPermission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
        [assembly: UsesPermission(Name = "com.google.android.c2dm.permission.RECEIVE")]
        [assembly: UsesPermission(Name = "android.permission.INTERNET")]
        [assembly: UsesPermission(Name = "android.permission.WAKE_LOCK")]
        //GET_ACCOUNTS is only needed for android versions 4.0.3 and below
        [assembly: UsesPermission(Name = "android.permission.GET_ACCOUNTS")]
9. Ajoutez la définition de classe suivante à l’espace de noms.

       [BroadcastReceiver(Permission = Gcm.Client.Constants.PERMISSION_GCM_INTENTS)]
       [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_MESSAGE }, Categories = new string[] { "@PACKAGE_NAME@" })]
       [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_REGISTRATION_CALLBACK }, Categories = new string[] { "@PACKAGE_NAME@" })]
       [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_LIBRARY_RETRY }, Categories = new string[] { "@PACKAGE_NAME@" })]
       public class PushHandlerBroadcastReceiver : GcmBroadcastReceiverBase<GcmService>
       {
           public static string[] SENDER_IDS = new string[] { "<PROJECT_NUMBER>" };
       }

   > [!NOTE]
   > Remplacez **<PROJECT_NUMBER>** par le numéro de projet noté précédemment.    
   >
   >
10. Remplacez la classe **GcmService** vide par le code suivant, qui utilise le nouveau récepteur de diffusion :

         [Service]
         public class GcmService : GcmServiceBase
         {
             public static string RegistrationID { get; private set; }

             public GcmService()
                 : base(PushHandlerBroadcastReceiver.SENDER_IDS){}
         }
11. Ajoutez le code suivant à la classe **GcmService**. Cela remplace le gestionnaire d’événements **OnRegistered** et met en œuvre une méthode **Register**.

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
                const string templateBodyGCM = "{\"data\":{\"message\":\"$(messageParam)\"}}";

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

    Notez que ce code utilise le paramètre `messageParam` pour l’inscription du modèle.
12. Ajoutez le code suivant qui implémente **OnMessage**:

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
13. **GcmServiceBase** requiert également la mise en œuvre des méthodes d’assistance **OnUnRegistered** et **OnError** que vous pouvez effectuer comme suit :

        protected override void OnUnRegistered(Context context, string registrationId)
        {
            Log.Error("PushHandlerBroadcastReceiver", "Unregistered RegisterationId : " + registrationId);
        }

        protected override void OnError(Context context, string errorId)
        {
            Log.Error("PushHandlerBroadcastReceiver", "GCM Error: " + errorId);
        }

Vous êtes maintenant prêt à tester les notifications Push dans l’application exécutée sur un appareil Android ou sur l’émulateur.

### <a name="test-push-notifications-in-your-android-app"></a>Tester les notifications Push dans votre application Android
Les deux premières étapes sont requises uniquement lorsque vous testez sur un émulateur.

1. Assurez-vous de procéder au déploiement ou au débogage sur un appareil virtuel sur lequel les API Google sont définies comme cible, comme indiqué ci-dessous dans le Gestionnaire d’appareil virtuel Android.
2. Ajoutez un compte Google à l’appareil Android en cliquant sur **Applications** > **Paramètres** > **Ajouter un compte**. Puis suivez les invites pour ajouter un compte Google existant sur l’appareil ou pour en créer un nouveau.
3. Dans Visual Studio ou Xamarin Studio, cliquez avec le bouton droit sur le projet **Droid**, puis cliquez sur **Définir comme projet de démarrage**.
4. Cliquez sur **Exécuter** pour générer le projet et lancer l’application sur votre appareil ou émulateur Android.
5. Dans l’application, tapez une tâche, puis cliquez sur l’icône plus (**+**).
6. Vérifiez qu’une notification est reçue lorsqu’un élément est ajouté.

## <a name="configure-and-run-the-ios-project-optional"></a>Configurer et exécuter le projet iOS (facultatif)
Cette section est dédiée à l’exécution du projet Xamarin iOS pour les appareils iOS. Vous pouvez ignorer cette section si vous n’utilisez pas d’appareils iOS.

[!INCLUDE [Enable Apple Push Notifications](../../includes/enable-apple-push-notifications.md)]

#### <a name="configure-the-notification-hub-for-apns"></a>Configurer le Notification Hub pour APNS
[!INCLUDE [app-service-mobile-apns-configure-push](../../includes/app-service-mobile-apns-configure-push.md)]

Ensuite, vous allez configurer le paramètre de projet iOS dans Xamarin Studio ou Visual Studio.

[!INCLUDE [app-service-mobile-xamarin-ios-configure-project](../../includes/app-service-mobile-xamarin-ios-configure-project.md)]

#### <a name="add-push-notifications-to-your-ios-app"></a>Ajout de notifications Push à votre application iOS
1. Dans le projet **iOS**, ouvrez AppDelegate.cs et ajoutez l’instruction suivante en haut du fichier de code.

        using Newtonsoft.Json.Linq;
2. Dans la classe **AppDelegate**, ajoutez également un remplacement pour l’événement **RegisteredForRemoteNotifications** afin de vous inscrire pour les notifications :

        public override void RegisteredForRemoteNotifications(UIApplication application,
            NSData deviceToken)
        {
            const string templateBodyAPNS = "{\"aps\":{\"alert\":\"$(messageParam)\"}}";

            JObject templates = new JObject();
            templates["genericMessage"] = new JObject
                {
                  {"body", templateBodyAPNS}
                };

            // Register for push with your mobile app
            Push push = TodoItemManager.DefaultManager.CurrentClient.GetPush();
            push.RegisterAsync(deviceToken, templates);
        }
3. Dans **AppDelegate**, ajoutez également la substitution suivante pour le Gestionnaire d’événements **DidReceiveRemoteNotification** :

        public override void DidReceiveRemoteNotification(UIApplication application,
            NSDictionary userInfo, Action<UIBackgroundFetchResult> completionHandler)
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

    Cette méthode gère les notifications entrantes pendant que l’application est en cours d’exécution.
4. Dans la classe **AppDelegate**, ajoutez le code suivant à la méthode **FinishedLaunching** :

        // Register for push notifications.
        var settings = UIUserNotificationSettings.GetSettingsForTypes(
            UIUserNotificationType.Alert
            | UIUserNotificationType.Badge
            | UIUserNotificationType.Sound,
            new NSSet());

        UIApplication.SharedApplication.RegisterUserNotificationSettings(settings);
        UIApplication.SharedApplication.RegisterForRemoteNotifications();

    Cela permet la prise en charge de l’enregistrement Push des notifications et des demandes à distance.

L’application est mise à jour et prend en charge les notifications Push.

#### <a name="test-push-notifications-in-your-ios-app"></a>Tester les notifications Push dans votre application iOS
1. Cliquez avec le bouton droit sur le projet iOS et cliquez sur **Définir comme projet de démarrage**.
2. Cliquez sur le bouton **Exécuter** ou sur **F5** dans Visual Studio pour générer le projet et démarrer l’application sur un appareil iOS. Ensuite, cliquez sur **OK** pour accepter les notifications Push.

   > [!NOTE]
   > Vous devez accepter explicitement les notifications Push de votre application. Cette demande s’effectue uniquement lors du premier démarrage de l’application.
   >
   >
3. Dans l’application, tapez une tâche, puis cliquez sur l’icône plus (**+**).
4. Vérifiez que vous avez reçu une notification, puis cliquez sur **OK** pour fermer celle-ci.

## <a name="configure-and-run-windows-projects-optional"></a>Configurer et exécuter des projets Windows (facultatif)
Cette section s’applique à l’exécution des projets Xamarin.Forms WinApp et WinPhone81 pour les appareils Windows. Ces étapes prennent également en charge les projets de plateforme Windows universelle (UWP). Vous pouvez ignorer cette section si vous n’utilisez pas d’appareils Windows.

#### <a name="register-your-windows-app-for-push-notifications-with-windows-notification-service-wns"></a>Inscrire votre application Windows aux notifications Push avec le service de notification Windows (Windows Notification Service, WNS)
[!INCLUDE [app-service-mobile-register-wns](../../includes/app-service-mobile-register-wns.md)]

#### <a name="configure-the-notification-hub-for-wns"></a>Configurer le Notification Hub pour WNS
[!INCLUDE [app-service-mobile-configure-wns](../../includes/app-service-mobile-configure-wns.md)]

#### <a name="add-push-notifications-to-your-windows-app"></a>Ajouter des notifications Push à votre application Windows
1. Dans Visual Studio, ouvrez le fichier **App.xaml.cs** dans un projet Windows et ajoutez les instructions suivantes.

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
                "<toast><visual><binding template=\"ToastText01\"><text id=\"1\">$(messageParam)</text></binding></visual></toast>";

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
3. Dans le fichier App.xaml.cs, mettez à jour la définition de méthode du gestionnaire d’événements **OnLaunched** en ajoutant le modificateur `async`. Puis, ajoutez la ligne de code suivante à la fin de la méthode :

        await InitNotificationsAsync();

    Cela permet de s’assurer que l’inscription aux notifications Push est créée ou actualisée à chaque lancement de l’application. Il est important d’effectuer cette opération pour vous assurer que le canal Push WNS est toujours actif.  
4. Dans l’Explorateur de solutions pour Visual Studio, ouvrez le fichier **Package.appxmanifest**, puis définissez **Compatible toast** sur **Oui** sous **Notifications**.
5. Générez l’application et vérifiez l’absence d’erreurs. Votre application cliente doit désormais s’inscrire pour les notifications de modèle du serveur principal Mobile Apps. Répétez cette section pour chaque projet Windows dans votre solution.

#### <a name="test-push-notifications-in-your-windows-app"></a>Tester les notifications Push dans votre application Windows
1. Dans Visual Studio, cliquez avec le bouton droit sur un projet Windows, puis cliquez sur **Définir comme projet de démarrage**.
2. Appuyez sur le bouton **Exécuter** pour générer le projet et démarrer l'application.
3. Dans l’application, tapez un nom pour un nouvel élément todoitem, puis cliquez sur l’icône de signe plus (**+**) pour l’ajouter.
4. Vérifiez qu’une notification est reçue lorsque l’élément est ajouté.

## <a name="next-steps"></a>Étapes suivantes
Apprenez-en plus sur les notifications Push :

* [Diagnostiquer les problèmes de notification Push](../notification-hubs/notification-hubs-push-notification-fixer.md)  
  Il existe différentes raisons pour lesquelles les notifications peuvent être perdues ou n’arrivent pas sur les appareils. Cette rubrique vous explique comment analyser et déterminer la cause première des défaillances de notification Push.

Vous pouvez poursuivre avec l’un des didacticiels suivants :

* [Ajouter l’authentification à votre application ](app-service-mobile-xamarin-forms-get-started-users.md)  
  Découvrez comment authentifier les utilisateurs de votre application avec un fournisseur d’identité.
* [Activer la synchronisation hors connexion pour votre application](app-service-mobile-xamarin-forms-get-started-offline-data.md)  
  Découvrez comment ajouter une prise en charge hors connexion à votre application à l’aide d’un serveur principal Mobile Apps. La synchronisation hors connexion permet aux utilisateurs d’interagir avec une application mobile &mdash;pour afficher, ajouter ou modifier des données&mdash;, même lorsqu’il n’existe aucune connexion réseau.

<!-- Images. -->

<!-- URLs. -->
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Xcode]: https://go.microsoft.com/fwLink/?LinkID=266532
[apns object]: http://go.microsoft.com/fwlink/p/?LinkId=272333
