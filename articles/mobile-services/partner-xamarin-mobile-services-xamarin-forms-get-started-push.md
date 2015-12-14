 <properties
	pageTitle="Ajout de notifications Push à votre application Mobile Services (Xamarin.Forms) - Mobile Services"
	description="Découvrez comment utiliser les notifications Push dans les applications Xamarin.Forms avec Azure Mobile Services."
	documentationCenter="xamarin"
	authors="wesmc7777"
	manager="dwrede"
	services="mobile-services"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.tgt_pltfrm="mobile-xamarin"
	ms.workload="mobile"
	ms.date="10/05/2015"
	ms.author="wesmc"/>

# Ajout de notifications Push à votre application Xamarin.Forms

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;

[AZURE.INCLUDE [mobile-services-selector-get-started-push](../../includes/mobile-services-selector-get-started-push.md)]

##Vue d’ensemble

Ce didacticiel vous montre comment utiliser Azure Mobile Services pour envoyer des notifications Push aux applications iOS, Android et Windows Phone de votre solution Xamarin.Forms. Commencez par créer un service mobile. Ensuite, téléchargez un exemple Starter, inscrivez-vous aux services de notification Push appropriés et ajoutez du code à la solution pour recevoir des notifications de ces services.

À la fin de ce didacticiel, votre service mobile envoie une notification Push chaque fois qu’un utilisateur ajoute une tâche dans l’une des applications. Cet exemple achevé figure ici : [exemple de notification Push Xamarin.Forms Azure achevé].

Ce didacticiel requiert les éléments suivants :

+ Un périphérique iOS 8 (vous ne pouvez pas tester les notifications push dans le simulateur iOS)
+ Un abonnement au programme pour développeurs iOS
+ [Xamarin.iOS Studio]
+ [Composant Azure Mobile Services]
+ Un compte Google actif
+ Le composant client [Google Cloud Messaging]. Vous ajouterez ce composant au cours du didacticiel.

Dans cette rubrique :

1. [Création d’un service mobile](#create-service)
2. [Téléchargement et configuration de l’exemple Starter](#download-starter-sample)
4. [Ajout de notifications Push à votre application Xamarin.Forms.iOS](#iOS)
5. [Ajout de notifications Push à votre application Xamarin.Forms.Android](#Android)
6. [Ajout de notifications Push à votre application Xamarin.Forms.Windows](#Windows)
7. [Mise à jour du script d’insertion de table Azure pour envoyer des notifications Push à toutes les applications](#all-apps)

## <a name="create-service"></a>Création d’un service mobile

[AZURE.INCLUDE [mobile-services-create-new-service-data](../../includes/mobile-services-create-new-service-data.md)]

Avant de pouvoir stocker des données d’application dans le nouveau service mobile, vous devez créer une table.

1. Dans le portail Azure Classic, cliquez sur **Mobile Services**, puis sur le service mobile que vous venez de créer.

2. Cliquez sur l’onglet **Data**, puis sur **+Create**.

    ![][123]

   	La boîte de dialogue **Create new table** s’affiche.

3. Dans **Table name**, saisissez _TodoItem_, puis cliquez sur le bouton de vérification.

    ![][124]

  	Cela crée une table de stockage **TodoItem** avec les autorisations par défaut définies, ce qui signifie que n’importe quel utilisateur de l’application peut accéder aux données de la table et les modifier.

    > [AZURE.NOTE]Le même nom de table est utilisé dans le démarrage rapide avec Mobile Services. Toutefois, chaque table est créée dans un schéma spécifique pour un service mobile donné. Cela vise à éviter les collisions de données lorsque plusieurs services mobiles utilisent la même base de données.

4. Cliquez sur la nouvelle table **TodoItem** et vérifiez qu’aucune ligne de données n’est présente.

5. Cliquez sur l’onglet **Columns** et vérifiez qu’il existe une seule colonne **id**, qui est créée automatiquement.

  	Cela correspond à la configuration minimale requise pour une table dans Mobile Services.

    > [AZURE.NOTE]Lorsque le schéma dynamique est activé sur votre service mobile, de nouvelles colonnes sont créées automatiquement lorsque des objets JSON sont envoyés au service mobile par une opération d’insertion ou de mise à jour.

Vous pouvez maintenant utiliser le nouveau service mobile en tant que stockage des données pour l’application.

## <a name="download-starter-sample"></a>Téléchargement et configuration de l’exemple Starter
Nous allons ajouter des notifications Push à un exemple existant.

1. Téléchargez l’exemple suivant : [exemple Starter de notification Push Xamarin.Forms Azure].

2. Dans le **portail Azure Classic**, cliquez sur [Mobile Services], puis sur le service mobile. Cliquez sur l’onglet **Tableau de bord**, puis notez l’**URL du site**. Ensuite, cliquez sur **Gérer les clés** et prenez note de la **Clé de l’application**. Ces valeurs sont nécessaires pour accéder au service mobile à partir de votre code d’application.

3. Dans le projet **ToDoAzure(Portable)** de la solution, ouvrez le fichier **Constants.cs**, remplacez `ApplicationURL` et `ApplicationKey` par l’URL du site et la clé de l’application obtenues à l’étape précédente.

## <a name="iOS"></a>Ajout de notifications Push à votre application Xamarin.Forms.iOS

Vous allez ajouter des notifications Push à l’application iOS à l’aide du service de notifications Push Apple (APNS, Apple Push Notification Service). Vous avez besoin d’un compte Google actif et du [composant Google Cloud Messaging Client].

>[AZURE.IMPORTANT]En raison des exigences liées au service APNS, vous devez déployer et tester les notifications Push sur un appareil compatible iOS (iPhone ou iPad) plutôt que sur un émulateur.

APNS utilise des certificats pour authentifier votre service mobile. Suivez ces instructions pour créer les certificats requis et les télécharger dans votre service mobile. Pour obtenir la documentation complète des fonctionnalités APNS, voir la page [Service de notification Push Apple].

### <a name="certificates"></a>Génération du fichier de demande de signature de certificat

Vous devez d’abord générer le fichier de demande de signature de certificat (CSR, Certificate Signing Request), qu’Apple utilise pour générer un certificat signé.

1. Depuis Utilitaires, exécutez l’outil **Trousseaux d’accès**.

2. Cliquez sur **Trousseaux d’accès**, développez **Assistant de certification**, puis cliquez sur **Demander un certificat à une autorité de certification**.

    ![][5]

3. Entrez l’**Adresse électronique de l’utilisateur**, entrez la valeur **Nom commun**, vérifiez que la case à cocher **Enregistré sur le disque** est activée, puis cliquez sur **Continuer**.

    ![][6]

4. Entrez un nom pour le fichier de demande de signature de certificat dans **Enregistrer sous**, sélectionnez l’emplacement dans **Où**, puis cliquez sur **Enregistrer**.

    ![][7]

    N’oubliez pas l’emplacement que vous avez choisi.

Ensuite, vous allez inscrire votre application auprès d’Apple, activer les notifications Push, puis télécharger ce fichier de demande de signature de certificat exporté pour créer un certificat Push.

### <a name="register"></a>Inscrire votre application pour les notifications push

Pour pouvoir envoyer des notifications Push vers une application iOS à partir des services mobiles, vous devez inscrire votre application auprès d’Apple, ainsi qu’aux notifications Push.

1. Si vous n’avez pas déjà inscrit votre application, accédez au <a href="http://go.microsoft.com/fwlink/p/?LinkId=272456" target="_blank">Portail de mise en service iOS</a> du centre de développement Apple, connectez-vous avec votre ID Apple, cliquez sur **Identifiers**, sur **App IDs**, puis sur le signe **+** pour créer un ID pour votre application.

    ![][102]

2. Entrez un nom pour votre application dans **Description**, entrez une valeur dans **Bundle Identifier** et retenez-la, activez l’option « Push Notifications » dans la section « App Services », puis cliquez sur **Continue**. Cet exemple utilise l’ID **MobileServices.Quickstart**, mais vous ne pouvez pas réutiliser le même ID, car chaque ID d’application doit être unique pour chaque utilisateur. Ainsi, nous vous recommandons d’ajouter votre nom complet ou vos initiales après le nom de l’application.

    ![][103]

    L’ID de votre application est généré et vous êtes invité à **Envoyer** les informations. Cliquez sur **Envoyer**.

    ![][104]

    Lorsque vous avez cliqué sur **Submit**, le message **Registration complete** s’affiche, comme montré ci-dessous. Cliquez sur **Done**.

    ![][105]

3. Recherchez l’ID de l’application que vous venez de créer, puis cliquez sur sa ligne.

    ![][106]

    Le fait de cliquer sur l’ID de l’application affiche les informations détaillées de cette application et de l’ID d’application. Cliquez sur le bouton **Settings**.

    ![][107]

4. Faites défiler jusqu’en bas de l’écran, puis cliquez sur le bouton **Create Certificate...** sous la section **Development Push SSL Certificate**.

    ![][108]

    Ceci affiche l’Assistant « Add iOS Certificate ».

    Remarque : ce didacticiel utilise un certificat de développement. Le même processus est utilisé lors de l’inscription d’un certificat de production. Assurez-vous simplement que vous avez défini le même type de certificat lorsque vous avez téléchargé le certificat vers Mobile Services.

5. Cliquez sur **Choose File**, accédez à l’emplacement où vous avez enregistré le fichier de demande de signature de certificat que vous avez créé lors de la première tâche, puis cliquez sur **Generate**.

    ![][110]

6. Une fois le certificat créé par le portail, cliquez sur le bouton **Download**, puis cliquez sur **Done**.

    ![][111]

    Ceci entraîne le téléchargement du certificat de signature et l’enregistre sur votre ordinateur dans le dossier Téléchargements.

    ![][9]

    Par défaut, le fichier téléchargé est un certificat de développement nommé <strong>aps\_development.cer</strong>.

7. Double-cliquez sur le certificat Push téléchargé **aps\_development.cer**.

    Ceci installe le nouveau certificat dans le Trousseau d’accès, comme indiqué ci-dessous :

    ![][10]

    Remarque : il se peut que le nom de votre certificat soit différent. Il portera toutefois le préfixe <strong>Apple Development iOS Push Notification Services:</strong>

Plus tard, vous devrez utiliser ce certificat pour générer un fichier .p12 et le télécharger vers Mobile Services pour activer l’authentification avec APNS.

### <a name="profile"></a>Créer un profil de mise en service pour l’application

1. Une fois de retour dans le <a href="http://go.microsoft.com/fwlink/p/?LinkId=272456" target="_blank">Portail de mise en service iOS</a>, sélectionnez **Provisioning Profiles**, **All**, puis cliquez sur le bouton **+** pour créer un profil. Ceci démarre l’Assistant **Ajouter le profil d’approvisionnement iOS**.

    ![][112]

2. Sélectionnez **iOS App Development** sous **Development** comme type de profil d’approvisionnement, puis cliquez sur **Continue**

3. Ensuite, sélectionnez l’ID de l’application de démarrage rapide Mobile Services dans la liste déroulante **App ID**, puis cliquez sur **Continue**.

    ![][113]

4. Sur l’écran **Sélectionner les certificats**, sélectionnez le certificat créé plus tôt, puis cliquez sur **Continuer**.

    ![][114]

5. Ensuite, sélectionnez les appareils (**Devices**) à utiliser pour le test, puis cliquez sur **Continue**.

    ![][115]

6. Ensuite, sélectionnez un nom pour le profil dans **Nom du profil**, cliquez sur **Générer**, puis sur **Terminé**.

    ![][116]

    Ceci entraîne la création d’un profil de mise en service.

    ![][117]

7. Dans Xcode, ouvrez l’Organisateur, sélectionnez la vue Appareils, sélectionnez **Profils d’approvisionnement** dans la section **Bibliothèque** dans le volet de gauche, puis cliquez sur le bouton **Actualiser** en bas du volet du milieu.

### <a name="configure-mobileServices"></a>Configurer Mobile Services pour l’envoi de requêtes push

Après l’inscription de votre application auprès d’APNS et une fois votre projet configuré, vous devez configurer votre service mobile pour l’intégrer à APNS.

1. Dans Trousseaux d’accès, cliquez avec le bouton droit sur le nouveau certificat, cliquez sur **Exporter**, donnez un nom à votre fichier, sélectionnez le format **.p12**, puis cliquez sur **Enregistrer**.

    ![][28]

    Notez le nom du fichier et l’emplacement du certificat exporté.

2. Connectez-vous au [portail Azure Classic], cliquez sur **Mobile Services**, puis sur votre application.

    ![][18]

3. Cliquez sur l’onglet **Push**, puis sur **Télécharger** sous **Paramètres de notification push Apple**.

    ![][19]

    Ceci affiche la boîte de dialogue Télécharger le certificat.

4. Cliquez sur **Fichier**, sélectionnez le fichier .p12 du certificat exporté, entrez le **Mot de passe**, vérifiez que le bon **Mode** est sélectionné, cliquez sur l’icône en forme de coche, puis cliquez sur **Enregistrer**.

    ![][20]

Votre service mobile est maintenant configuré pour fonctionner avec APNS.

### <a name="configure-app"></a>Configurer votre application Xamarin.iOS

1. Dans Xamarin.Studio ou Visual Studio, ouvrez **Info.plist** et mettez à jour la valeur de **Bundle Identifier** avec l’ID que vous avez créé précédemment.

    ![][121]

2. Descendez jusqu’à **Background Modes**, puis activez les cases à cocher **Enable Background Modes** et **Remote notifications**.

    ![][122]

3. Double-cliquez sur votre projet dans Solution Panel pour ouvrir les **options de projet**.

4.  Sélectionnez **iOS Bundle Signing** sous **Build**, puis sélectionnez les éléments **Identity** et **Provisioning profile** que vous venez de configurer pour ce projet.

    ![][120]

    Ceci vérifie que le projet Xamarin utilise le nouveau profil pour la signature du code. Pour obtenir la documentation officielle de l’approvisionnement des appareils Xamarin, voir la page [Approvisionnement des appareils Xamarin].

### <a name="add-push"></a>Ajout de notifications Push à votre application

1. Dans Xamarin.Studio ou Visual Studio, développez le projet **ToDoAzure.iOS**, ouvrez la classe **AppDelegate**, puis remplacez l’événement **FinishedLaunching** par le code suivant :

        public override bool FinishedLaunching(UIApplication app, NSDictionary options)
        {
             // registers for push for iOS8
            var settings = UIUserNotificationSettings.GetSettingsForTypes(
                UIUserNotificationType.Alert
                | UIUserNotificationType.Badge
                | UIUserNotificationType.Sound,
                new NSSet());

            global::Xamarin.Forms.Forms.Init();
            instance = this;
            CurrentPlatform.Init();

            todoItemManager = new ToDoItemManager();
            App.SetTodoItemManager(todoItemManager);


            UIApplication.SharedApplication.RegisterUserNotificationSettings(settings);
            UIApplication.SharedApplication.RegisterForRemoteNotifications();

            LoadApplication(new App());
            return base.FinishedLaunching(app, options);
        }

6. Dans **AppDelegate**, remplacez l’événement **RegisteredForRemoteNotifications** :

        public override void RegisteredForRemoteNotifications(UIApplication application, NSData deviceToken)
        {
            // Modify device token
            string _deviceToken = deviceToken.Description;
            _deviceToken = _deviceToken.Trim('<', '>').Replace(" ", "");

            // Get Mobile Services client
            MobileServiceClient client = todoItemManager.GetClient;

            // Register for push with Mobile Services
            IEnumerable<string> tag = new List<string>() { "uniqueTag" };

            const string template = "{"aps":{"alert":"$(message)"}}";

            var expiryDate = DateTime.Now.AddDays(90).ToString
                (System.Globalization.CultureInfo.CreateSpecificCulture("fr-FR"));

            var push = client.GetPush();

            push.RegisterTemplateAsync(_deviceToken, template, expiryDate, "myTemplate", tag)
        }

7. Dans **AppDelegate**, remplacez l’événement **ReceivedRemoteNotification** :

        public override void ReceivedRemoteNotification(UIApplication application, NSDictionary userInfo)
        {
            NSObject inAppMessage;

            bool success = userInfo.TryGetValue(new NSString("inAppMessage"), out inAppMessage);

            if (success)
            {
                var alert = new UIAlertView("Got push notification", inAppMessage.ToString(), null, "OK", null);
                alert.Show();
            }
        }

L’application est mise à jour et prend en charge les notifications Push.

### <a name="update-scripts"></a>Mise à jour du script d’insertion inscrit dans le portail Azure Classic

1. Dans le portail Azure Classic, cliquez sur l’onglet **Données**, puis sur la table **TodoItem**.

    ![][21]

2. Dans **todoitem**, cliquez sur l’onglet **Script** et sélectionnez **Insérer**.

    ![][22]

    La fonction appelée lors d’une insertion dans la table **TodoItem** s’affiche.

3. Remplacez la fonction insert par le code suivant, puis cliquez sur **Enregistrer** :

          function insert(item, user, request) {
          // Execute the request and send notifications.
             request.execute({
             success: function() {
              // Create a template-based payload.
              var payload = '{ "message" : "New item added: ' + item.text + '" }';

              // Write the default response and send a notification
              // to all platforms.
              push.send(null, payload, {
                  success: function(pushResponse){
                  console.log("Sent push:", pushResponse);
                  // Send the default response.
                  request.respond();
                  },
                  error: function (pushResponse) {
                      console.log("Error Sending push:", pushResponse);
                       // Send the an error response.
                      request.respond(500, { error: pushResponse });
                      }
               });
              }
           });
          }

    Ceci inscrit un nouveau script d’insertion, qui envoie une notification Push (le texte inséré) à l’appareil fourni dans la demande d’insertion.

   >[AZURE.NOTE]Ce script reporte l’envoi de la notification pour vous laisser le temps de fermer l’application pour recevoir une notification toast.

### <a name="test"></a>Tester les notifications push dans votre application

1. Appuyez sur le bouton **Démarrer** pour générer le projet, puis démarrez l’application sur un appareil compatible iOS, et enfin cliquez sur **OK** pour accepter les notifications Push.

   >[AZURE.NOTE]Vous devez accepter explicitement les notifications Push de votre application. Cette demande s’effectue uniquement lors du premier démarrage de l’application.

2. Dans l’application, cliquez sur le bouton **Ajouter**, ajoutez un titre de tâche, puis cliquez sur le bouton **Enregistrer**.

3. Vérifiez que vous avez reçu une notification, puis cliquez sur **OK** pour fermer celle-ci.


Vous avez terminé ce didacticiel.

## <a name="Android"></a>Ajout de notifications Push à votre application Xamarin.Forms.Android

Vous allez ajouter des notifications Push à l’application Android en utilisant le service Google Cloud Messaging (GCM). Vous avez besoin d’un compte Google actif et du [composant Google Cloud Messaging Client].

###<a id="register"></a>Activation de Google Cloud Messaging

[AZURE.INCLUDE [mobile-services-enable-Google-cloud-messaging](../../includes/mobile-services-enable-google-cloud-messaging.md)]

###<a id="configure"></a>Configurer votre service mobile pour l’envoi de demandes push

[AZURE.INCLUDE [mobile-services-android-configure-push](../../includes/mobile-services-android-configure-push.md)]

###<a id="update-scripts"></a>Mettre à jour le script d’insertion inscrit pour envoyer des notifications

>[AZURE.NOTE]Les étapes suivantes montrent comment mettre à jour le script inscrit vers l’opération d’insertion sur la table TodoItem dans le portail Azure Classic. Vous pouvez également accéder et modifier ce script de service mobile directement dans Visual Studio, dans le nœud Azure de l’Explorateur de serveurs.

Dans le [portail Azure Classic], cliquez sur l’onglet **Données**, puis sur la table **TodoItem**.

   ![][21]

2. Dans **todoitem**, cliquez sur l’onglet **Script** et sélectionnez **Insérer**.

   ![][22]

    This displays the function that is invoked when an insert occurs in the **TodoItem** table.

3. Remplacez la fonction insert par le code suivant, puis cliquez sur **Enregistrer** :

          function insert(item, user, request) {
          // Execute the request and send notifications.
             request.execute({
             success: function() {
              // Create a template-based payload.
              var payload = '{ "message" : "New item added: ' + item.text + '" }';

              // Write the default response and send a notification
              // to all platforms.
              push.send(null, payload, {
                  success: function(pushResponse){
                  console.log("Sent push:", pushResponse);
                  // Send the default response.
                  request.respond();
                  },
                  error: function (pushResponse) {
                      console.log("Error Sending push:", pushResponse);
                       // Send the an error response.
                      request.respond(500, { error: pushResponse });
                      }
               });
              }
           });
          }


    Ceci inscrit un nouveau script d’insertion, qui envoie une notification Push (le texte inséré) à l’appareil fourni dans la demande d’insertion.

   >[AZURE.NOTE]Ce script reporte l’envoi de la notification pour vous laisser le temps de fermer l’application pour recevoir une notification toast.


###<a id="configure-app"></a>Configuration du projet existant pour les notifications Push

1. Dans l’affichage de solutions, développez le dossier **Composants** de l’application Xamarin.Android et assurez-vous que le package Azure Mobile Services est installé.

2. Cliquez avec le bouton droit sur le dossier **Composants**, cliquez sur **Obtenir d’autres composants**, recherchez le composant **Client de messagerie Cloud Google** composant et l’ajouter au projet.

1. Ouvrez le fichier projet MainActivity.cs et ajoutez l’instruction using suivante à la classe :

		using Gcm.Client;


4.	Dans la classe **MainActivity**, ajoutez le code suivant à la méthode **OnCreate**, après l’appel à la méthode **LoadApplication** :

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
                CreateAndShowDialog(new Exception("There was an error creating the Mobile Service. Verify the URL"), "Error");
            }
            catch (Exception e)
            {
                CreateAndShowDialog(e, "Error");
            }

Vous pouvez maintenant ajouter des notifications Push à **MainActivity**.

###<a id="add-push"></a>Ajouter le code des notifications push à votre application

5. Dans le projet ToDoAzure.Droid, créez une classe dans le projet appelé `GcmService`.

5. Ajoutez les instructions Using suivantes à la classe **GcmService** :

		using Gcm.Client;
		using Microsoft.WindowsAzure.MobileServices;

6. Ajoutez les demandes d’autorisation suivantes entre les instructions **using** et la déclaration **namespace** :

		[assembly: Permission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
        [assembly: UsesPermission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
        [assembly: UsesPermission(Name = "com.google.android.c2dm.permission.RECEIVE")]

        //GET_ACCOUNTS is only needed for android versions 4.0.3 and below
        [assembly: UsesPermission(Name = "android.permission.GET_ACCOUNTS")]
        [assembly: UsesPermission(Name = "android.permission.INTERNET")]
        [assembly: UsesPermission(Name = "android.permission.WAKE_LOCK")]

7. Dans le fichier de projet **GcmService.cs**, ajoutez la classe suivante :

        [BroadcastReceiver(Permission = Gcm.Client.Constants.PERMISSION_GCM_INTENTS)]
        [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_MESSAGE }, Categories = new string[] { "@PACKAGE_NAME@" })]
        [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_REGISTRATION_CALLBACK }, Categories = new string[] { "@PACKAGE_NAME@" })]
        [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_LIBRARY_RETRY }, Categories = new string[] { "@PACKAGE_NAME@" })]

        public class PushHandlerBroadcastReceiver : GcmBroadcastReceiverBase<GcmService>
        {

            public static string[] SENDER_IDS = new string[] { "<PROJECT_NUMBER>" };

        }

	Dans le code ci-dessus, vous devez remplacer _`<PROJECT_NUMBER>`_ par le numéro de projet attribué par Google lorsque vous avez configuré votre application dans le portail des développeurs Google.

8. Dans le fichier de projet GcmService.cs, ajoutez le code suivant qui définit la classe **GcmService** :

         [Service]
         public class GcmService : GcmServiceBase
         {
             public static string RegistrationID { get; private set; }

             public GcmService()
                 : base(PushHandlerBroadcastReceiver.SENDER_IDS){}
         }


	Notez que cette classe dérive de **GcmServiceBase** et que l’attribut **Service** doit être appliqué à cette classe.

	>[AZURE.NOTE]La classe **GcmServiceBase** implémente les méthodes **OnRegistered()**, **OnUnRegistered()**, **OnMessage()** et **OnError()**. Vous devez remplacer ces méthodes dans la classe **GcmService**.

5. Ajoutez le code suivant à la classe **GcmService** qui remplace le gestionnaire d’événements **OnRegistered**.

        protected override void OnRegistered(Context context, string registrationId)
        {
            Log.Verbose(PushHandlerBroadcastReceiver.TAG, "GCM Registered: " + registrationId);
            RegistrationID = registrationId;

            createNotification("GcmService Registered...", "The device has been Registered, Tap to View!");

            MobileServiceClient client =  MainActivity.DefaultService.todoItemManager.GetClient;

            var push = client.GetPush();

            MainActivity.DefaultService.RunOnUiThread(() => Register(push, null));

        }
        public async void Register(Microsoft.WindowsAzure.MobileServices.Push push, IEnumerable<string> tags)
        {
            try
            {
                const string template = "{"data":{"message":"$(message)"}}";

                await push.RegisterTemplateAsync(RegistrationID, template, "mytemplate", tags);
            }
            catch (Exception ex)
            {
                System.Diagnostics.Debug.WriteLine(ex.Message);
                Debugger.Break();
            }
        }

	Cette méthode utilise l’ID d’inscription GCM retourné pour s’inscrire auprès d’Azure pour les notifications push.

10. Remplacez la méthode **OnMessage** dans **GcmService** par le code suivant :

        protected override void OnMessage(Context context, Intent intent)
        {
            Log.Info(PushHandlerBroadcastReceiver.TAG, "GCM Message Received!");

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

12. Ajoutez les substitutions de méthode suivantes pour **OnUnRegistered()** et **OnError()**, qui sont requis pour la compilation du projet.

        protected override void OnError(Context context, string errorId)
        {
              Log.Error(PushHandlerBroadcastReceiver.TAG, "GCM Error: " + errorId);
        }

###<a id="test"></a>Tester les notifications push dans votre application

Vous pouvez tester l’application en connectant directement un téléphone Android via un câble USB, ou en utilisant un appareil virtuel dans l’émulateur.

Lorsque vous exécutez cette application dans l’émulateur, veillez à utiliser un AVD (appareil virtuel Android) qui prend en charge les API Google.

> [AZURE.IMPORTANT]Afin de recevoir des notifications Push, vous devez configurer un compte Google sur votre appareil virtuel Android (dans l’émulateur, accédez à **Paramètres**, puis cliquez sur **Ajouter un compte**). Assurez-vous également que l’émulateur est connecté à Internet.

1. Dans **Outils**, cliquez sur **Open Android Emulator Manager**, sélectionnez votre appareil, puis cliquez sur **Modifier**.

    ![][125]

2. Sélectionnez **API Google** dans **Cible**, puis cliquez sur **OK**.

    ![][126]

3. Dans la barre d’outils supérieure, cliquez sur **Exécuter**, puis sélectionnez votre application. L’émulateur démarre et l’application est exécutée.

  L’application extrait le *registrationId* de GCM et s’inscrit auprès du concentrateur de notification.

1. Dans l’application, ajoutez une nouvelle tâche.

2. Faites glisser votre doigt du haut vers le bas de l’écran pour ouvrir le centre de notifications de l’appareil et afficher la notification.

	![][127]

## <a name="Windows"></a>Ajout de notifications Push à votre application Xamarin.Forms.Windows

Cette section vous montre comment utiliser Azure Mobile Services pour envoyer des notifications Push à l’application Windows Phone Silverlight de votre solution Xamarin.Forms.

###<a id="update-app"></a> Mise à jour de l’application pour l’inscription aux notifications

Pour permettre à votre application de recevoir les notifications Push, vous devez inscrire un canal de notification.

1. Dans Visual Studio, ouvrez le fichier App.xaml.cs et ajoutez l’instruction `using` suivante :

        using Microsoft.Phone.Notification;

3. Ajoutez le code suivant à App.xaml.cs :

        public static HttpNotificationChannel CurrentChannel { get; private set; }

        private void AcquirePushChannel()
        {
            CurrentChannel = HttpNotificationChannel.Find("MyPushChannel");

            if (CurrentChannel == null)
            {
                CurrentChannel = new HttpNotificationChannel("MyPushChannel");
                CurrentChannel.Open();
                CurrentChannel.BindToShellToast();
            }

            CurrentChannel.ChannelUriUpdated +=
                new EventHandler<NotificationChannelUriEventArgs>(async (o, args) =>
                {

                   // Register for notifications using the new channel
                    const string template =
                    "<?xml version="1.0" encoding="utf-8"?><wp:Notification " +
                    "xmlns:wp="WPNotification"><wp:Toast><wp:Text1>$(message)</wp:Text1></wp:Toast></wp:Notification>";

                    await client.GetPush()
                        .RegisterTemplateAsync(CurrentChannel.ChannelUri.ToString(), template, "mytemplate");
                });
        }

    Ce code récupère l’URI de canal ChannelURI pour l’application auprès du Service de notifications Push Microsoft (MPNS) utilisés par « Silverlight » Windows Phone 8.x, puis il inscrit ce ChannelURI aux notifications Push.

	>[AZURE.NOTE]Dans ce didacticiel, le service mobile envoie une notification toast à l’appareil. Lorsque vous envoyez une notification par vignette, vous devez appeler la méthode **BindToShellTile** sur le canal.

4. En haut du gestionnaire d’événements **Application\_Launching** dans App.xaml.cs, ajoutez l’appel suivant à la nouvelle méthode **AcquirePushChannel** :

        AcquirePushChannel();

	Cela permet de s’assurer que l’inscription est demandée à chaque chargement de la page. Dans votre application, vous souhaitez effectuer cette inscription régulièrement pour vous assurer de son exactitude.

5. Appuyez sur la touche **F5** pour exécuter l’application. Une boîte de dialogue s’affiche avec la clé d’inscription.

6.	Dans l’Explorateur de solutions, développez **Propriétés**, ouvrez le fichier WMAppManifest.xml, cliquez sur l’onglet **Fonctionnalités** et veillez à ce que la fonctionnalité **ID\_\_\_CAP\_\_\_PUSH\_NOTIFICATION** soit activée.

   	![Activer les notifications dans VS](./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-app-enable-push-wp8.png)

   	Cela permet de s’assurer que votre application peut déclencher des notifications toast.

###<a id="update-scripts"></a> Mise à jour des scripts serveur pour l’envoi de notifications Push

Enfin, vous devez mettre à jour le script inscrit dans l’opération d’insertion sur la table TodoItem pour envoyer les notifications.

1. Dans le [portail Azure Classic], cliquez sur l’onglet **Données**, puis sur la table **TodoItem**.

    ![][21]

2. Dans **todoitem**, cliquez sur l’onglet **Script** et sélectionnez **Insérer**.

    ![][22]

    La fonction appelée lors d’une insertion dans la table **TodoItem** s’affiche.

3. Remplacez la fonction insert par le code suivant, puis cliquez sur **Enregistrer** :

          function insert(item, user, request) {
          // Execute the request and send notifications.
             request.execute({
             success: function() {
              // Create a template-based payload.
              var payload = '{ "message" : "New item added: ' + item.text + '" }';

              // Write the default response and send a notification
              // to all platforms.
              push.send(null, payload, {
                  success: function(pushResponse){
                  console.log("Sent push:", pushResponse);
                  // Send the default response.
                  request.respond();
                  },
                  error: function (pushResponse) {
                      console.log("Error Sending push:", pushResponse);
                       // Send the an error response.
                      request.respond(500, { error: pushResponse });
                      }
               });
              }
           });
          }


    Ceci inscrit un nouveau script d’insertion, qui envoie une notification Push (le texte inséré) à l’appareil fourni dans la demande d’insertion.

3. Cliquez sur l’onglet **Push**, cochez **Activez des notifications push non authentifiées**, puis cliquez sur **Enregistrer**.

	Le service mobile peut ainsi se connecter à MPNS en mode non authentifié pour envoyer les notifications Push.

	>[AZURE.NOTE]Ce didacticiel utilise MPNS en mode non authentifié. Dans ce mode, MPNS limite le nombre de notifications à envoyer à un canal d’appareil. Pour supprimer cette restriction, vous devez générer et télécharger un certificat en cliquant sur **Télécharger** et en sélectionnant le certificat. Pour plus d’informations sur la génération du certificat, voir la rubrique [Configuration d’un service Web authentifié afin d’envoyer des notifications Push pour Windows Phone].

###<a id="test"></a> Test des notifications Push dans votre application

1. Dans Visual Studio, appuyez sur la touche F5 pour exécuter l’application.

    >[AZURE.NOTE]Vous pouvez rencontrer une exception « 401 Unauthorized RegistrationAuthorizationException » lors de tests sur l’émulateur Windows Phone. Cela peut se produire pendant l’appel de `RegisterNativeAsync()` en raison de la manière dont l’émulateur Windows Phone synchronise son horloge avec le PC hôte. Un jeton de sécurité pourrait être rejeté. Pour résoudre ce problème, il vous suffit de paramétrer manuellement l’horloge dans l’émulateur avant les tests.

5. Dans l’application, créez une tâche intitulée **Hello push**, puis cliquez immédiatement sur le bouton Démarrer ou sur le bouton Précédent pour quitter l’application.

  	Cela envoie une requête insert au service mobile pour stocker l’élément ajouté. Notez que l’appareil reçoit une notification toast qui indique **hello push**.

	![Notification toast reçue](./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-quickstart-push5-wp8.png)

	>[AZURE.NOTE]Vous ne recevrez pas cette notification si vous vous trouvez encore dans l’application. Pour recevoir une notification toast pendant que l’application est active, vous devez gérer l’événement [ShellToastNotificationReceived](http://msdn.microsoft.com/library/windowsphone/develop/microsoft.phone.notification.httpnotificationchannel.shelltoastnotificationreceived(v=vs.105).aspx).

<!-- Anchors. -->
[Generate the certificate signing request]: #certificates
[Register your app and enable push notifications]: #register
[Create a provisioning profile for the app]: #profile
[Configure Mobile Services]: #configure-mobileServices
[Configure the Xamarin.iOS App]: #configure-app
[Update scripts to send push notifications]: #update-scripts
[Add push notifications to the app]: #add-push
[Insert data to receive notifications]: #test

<!-- Images. -->

[5]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-step5.png
[6]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-step6.png
[7]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-step7.png

[9]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-step9.png
[10]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-step10.png

[17]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-step17.png
[18]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-selection.png
[19]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-push-tab-ios.png
[20]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-push-tab-ios-upload.png
[21]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-portal-data-tables.png
[22]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-insert-script-push2.png
[23]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-quickstart-push1-ios.png
[24]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-quickstart-push2-ios.png
[25]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-quickstart-push3-ios.png
[26]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-quickstart-push4-ios.png
[28]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-step18.png

[101]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-01.png
[102]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-02.png
[103]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-03.png
[104]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-04.png
[105]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-05.png
[106]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-06.png
[107]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-07.png
[108]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-08.png

[110]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-10.png
[111]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-11.png
[112]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-12.png
[113]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-13.png
[114]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-14.png
[115]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-15.png
[116]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-16.png
[117]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-17.png

[120]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-20.png
[121]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-21.png
[122]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-22.png
[123]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-data-tab-empty.png
[124]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-create-todoitem-table.png
[125]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/notification-hub-create-android-app7.png
[126]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/notification-hub-create-android-app8.png
[127]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/notification-area-received.png


[Xamarin.iOS Studio]: http://xamarin.com/platform
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[iOS Provisioning Portal]: http://go.microsoft.com/fwlink/p/?LinkId=272456
[Mobile Services iOS SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[Service de notification Push Apple]: http://go.microsoft.com/fwlink/p/?LinkId=272584
[Get started with Mobile Services]: mobile-services-ios-get-started.md

[Approvisionnement des appareils Xamarin]: http://developer.xamarin.com/guides/ios/getting_started/installation/device_provisioning/


[Mobile Services]: https://manage.windowsazure.com/
[portail Azure Classic]: https://manage.windowsazure.com/
[apns object]: http://go.microsoft.com/fwlink/p/?LinkId=272333
[Composant Azure Mobile Services]: http://components.xamarin.com/view/azure-mobile-services/
[completed example project]: http://go.microsoft.com/fwlink/p/?LinkId=331303
[Xamarin.iOS]: http://xamarin.com/download
[Google Cloud Messaging]: http://components.xamarin.com/view/GCMClient/
[composant Google Cloud Messaging Client]: http://components.xamarin.com/view/GCMClient/
[exemple Starter de notification Push Xamarin.Forms Azure]: https://github.com/Azure/mobile-services-samples/tree/master/TodoListXamarinForms
[exemple de notification Push Xamarin.Forms Azure achevé]: https://github.com/Azure/mobile-services-samples/tree/master/GettingStartedWithPushXamarinForms

<!---HONumber=AcomDC_1203_2015-->