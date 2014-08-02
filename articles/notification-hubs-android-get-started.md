<properties linkid="develop-notificationhubs-tutorials-get-started-android" urlDisplayName="Get Started" pageTitle="Get Started with Azure Notification Hubs" metaKeywords="" description="Learn how to use Azure Notification Hubs to push notifications." metaCanonical="" services="notification-hubs" documentationCenter="Mobile" title="Get started with Notification Hubs" authors="ricksal" solutions="" manager="dwrede" editor="" />

Prise en main de Notification Hubs
==================================

[Windows Store C\#](/en-us/manage/services/notification-hubs/getting-started-windows-dotnet "Windows Store C#")[Windows Phone](/en-us/documentation/articles/notification-hubs-windows-phone-get-started/ "Windows Phone")[iOS](/en-us/documentation/articles/notification-hubs-ios-get-started/ "iOS")[Android](/en-us/documentation/articles/notification-hubs-android-get-started/ "Android")[Kindle](/en-us/documentation/articles/notification-hubs-kindle-get-started/ "Kindle")[Xamarin.iOS](/en-us/documentation/articles/partner-xamarin-notification-hubs-ios-get-started/ "Xamarin.iOS")[Xamarin.Android](/en-us/documentation/articles/partner-xamarin-notification-hubs-android-get-started/ "Xamarin.Android")

Cette rubrique montre comment utiliser Azure Notification Hubs pour envoyer des notifications Push vers une application Android. Dans ce didacticiel, vous allez créer une application Android vide qui reçoit des notifications Push à l'aide de Google Cloud Messaging (GCM). Au final, vous pouvez diffuser des notifications Push sur tous les appareils sur lesquels votre application est installée au moyen de votre Notification Hub.

Ce didacticiel vous familiarise avec les étapes de base pour activer les notifications Push :

-   [Activation de Google Cloud Messaging](#register)
-   [Configuration de votre Notification Hub](#configure-hub)
-   [Connexion de votre application au Notification Hub](#connecting-app)
-   [Envoi d'une notification vers votre application](#send)
-   [Test de votre application](#run-app)

Ce didacticiel présente un scénario de diffusion simple utilisant Notification Hubs. Suivez scrupuleusement le didacticiel suivant pour apprendre à utiliser Notification Hubs pour accéder à des utilisateurs et groupes d'appareils spécifiques.

Ce didacticiel requiert les éléments suivants :

-   le Kit de développement logiciel (SDK) Android (nous présumons que vous utiliserez Eclipse), que vous pouvez télécharger [ici](http://go.microsoft.com/fwlink/?LinkId=389797) ;
-   le [Kit de développement logiciel (SDK) Mobile Services Android](https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409)

Vous devez suivre ce didacticiel avant de pouvoir suivre tous les autres didacticiels Notification Hubs pour les applications Android.

**Remarque**

Pour suivre ce didacticiel, vous avez besoin d'un compte Azure actif. Si vous ne possédez pas de compte, vous pouvez créer un compte d'évaluation gratuit en quelques minutes. Pour plus d'informations, consultez la page [Version d'évaluation gratuite d'Azure](http://www.windowsazure.com/en-us/pricing/free-trial/?WT.mc_id=A0E0E5C02&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fen-us%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F).

Activation de Google Cloud Messaging
------------------------------------

[WACOM.INCLUDE [Enable GCM](../includes/mobile-services-enable-Google-cloud-messaging.md)]

Ensuite, vous allez utiliser cette valeur de clé API afin d'activer Notification Hubs pour l'authentification avec GCM et l'envoi de notifications Push au nom de votre application.

Configuration de votre Notification Hub
---------------------------------------

1.  Connectez-vous au [portail de gestion Azure](https://manage.windowsazure.com/) et cliquez sur **+NEW** en bas de l'écran.

2.  Cliquez sur **App Services**, puis sur **Service Bus**, **Notification Hub**, **Quick Create**.

	![][7]

3.  Tapez un nom pour votre Notification Hub, sélectionnez la région souhaitée, puis cliquez sur **Create a new Notification Hub**.

	![][8]

4.  Cliquez sur l'espace de noms que vous venez de créer (en général ***nom du Notification Hub*-ns**), puis cliquez sur l'onglet **Configure** en haut.

	![][9]

5.  Cliquez sur l'onglet **Notification Hubs** en haut de la page, puis cliquez sur le Notification Hub que vous venez de créer.

	![][10]

6.  Cliquez sur l'onglet **Configure** dans la partie supérieure, saisissez la valeur **API Key** obtenue à la section précédente, puis cliquez sur **Save**.

	![][11]

7.  Sélectionnez l'onglet **Dashboard** en haut, puis cliquez sur **View Connection String**. Notez les deux chaînes de connexion.

Votre Notification Hub est configuré pour GCM, et vous disposez des chaînes de connexion pour inscrire votre application et envoyer des notifications Push.

Connexion de votre application au Notification Hub
--------------------------------------------------

### Création d'un projet Android

1.  Dans Eclipse ADT, créez un projet Android (File, New, Android Application).

	![][13]

2.  Assurez-vous que **Minimum Required SDK** est défini sur *API 8 : Android 2.2 (Froyo)* et que les deux entrées SDK suivantes sont définies sur la version la plus récente disponible. Sélectionnez Next, suivez l'Assistant, assurez-vous que l'option **Create activity** est sélectionnée afin de créer une activité vierge. Acceptez l'icône Launcher par défaut dans la zone suivante, puis cliquez sur **Finish** dans la dernière zone.

	![][14]

### Ajout de services Google Play au projet

[WACOM.INCLUDE [Add Play Services](../includes/mobile-services-add-Google-play-services.md)]

### Ajout de code

1.  Téléchargez le Kit de développement logiciel (SDK) Android Notification Hubs [ici](https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409). Décompressez le fichier .zip et copiez le fichier notificationhubs\\notification-hubs-0.1.jar dans le répertoire \\libs de votre projet dans l'Explorateur de package.

2.  Téléchargez et décompressez le [Kit de développement logiciel (SDK) Mobile Services pour Android](https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409), ouvrez le dossier **notifications**, copiez le fichier **notifications-1.0.1.jar** dans le dossier *libs* de votre projet Eclipse, puis actualisez le dossier *libs*.

    **Remarque**

    Les numéros à la fin du nom du fichier peuvent changer dans les versions ultérieures du Kit de développement logiciel (SDK).

    Maintenant, configurez l'application afin d'obtenir un *registrationId* depuis GCM, et utilisez-le pour inscrire l'instance d'application auprès du Notification Hub.

3.  Dans le fichier AndroidManifest.xml, ajoutez la ligne suivante juste sous l'élément <uses-sdk/>. Assurez-vous de remplacer `<your package>` par le package que vous avez sélectionné pour votre application à l'étape 1 (`com.yourCompany.wams_notificationhubs` dans cet exemple).

         <uses-permission android:name="android.permission.INTERNET"/>
         <uses-permission android:name="android.permission.GET_ACCOUNTS"/>
         <uses-permission android:name="android.permission.WAKE_LOCK"/>
         <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />

         <permission android:name="<votre package>.permission.C2D_MESSAGE" android:protectionLevel="signature" />
         <uses-permission android:name="<your package>.permission.C2D_MESSAGE"/>

4.  Dans la classe **MainActivity**, ajoutez les instructions suivantes.

         import android.os.AsyncTask;    
         import com.google.android.gms.gcm.*;
         import com.microsoft.windowsazure.messaging.*;
         import com.microsoft.windowsazure.notifications.NotificationsManager;

5.  Ajoutez les membres privés suivants dans la partie supérieure de la classe.

    **Remarque**

    Assurez-vous de définir SENDER\_ID sur le numéro de projet obtenu précédemment.

         private String SENDER_ID = "<your project number>";
         private GoogleCloudMessaging gcm;
         private NotificationHub hub;

6.  Dans la méthode **OnCreate**, ajoutez le code suivant et assurez-vous de remplacer les espaces réservés par votre chaîne de connexion avec accès d'écoute obtenue dans la section précédente ainsi que le nom de votre Notification Hub qui s'affiche en haut de la page dans Azure pour votre Hub (**pas** l'ensemble de l'URL).

         NotificationsManager.handleNotifications(this, SENDER_ID, MyHandler.class);

         gcm = GoogleCloudMessaging.getInstance(this);
            
         String connectionString = "<your listen access connection string>";
         hub = new NotificationHub("<your notification hub name>", connectionString, this);
            
         registerWithNotificationHubs();

7.  Dans MainActivity.java, créez la méthode suivante :

         @SuppressWarnings("unchecked")
         private void registerWithNotificationHubs() {
            new AsyncTask() {
               @Override
               protected Object doInBackground(Object... params) {
                  try {
                     String regid = gcm.register(SENDER_ID);
                     hub.register(regid);
                  } catch (Exception e) {
                     return e;
                  }
                  return null;
              }
            }.execute(null, null, null);
         }

8.  Android n'affichant pas de notifications, vous devez écrire votre propre récepteur. Dans **AndroidManifest.xml**, ajoutez l'élément suivant dans l'élément `<application/>`.

    **Remarque**

    Remplacez l'espace réservé par votre nom de package.

         <receiver android:name="com.microsoft.windowsazure.notifications.NotificationsBroadcastReceiver"
             android:permission="com.google.android.c2dm.permission.SEND">
             <intent-filter>
                 <action android:name="com.google.android.c2dm.intent.RECEIVE" />
                 <category android:name="**mon_package_app**" />
             </intent-filter>
         </receiver>

9.  Dans l'Explorateur de package, cliquez avec le bouton droit sur le package (sous le nœud `src`), cliquez sur **Nouveau**, puis sur **Classe**.

10. Dans **Nom**, tapez `MyHandler`, dans **Superclasse**, tapez `com.microsoft.windowsazure.notifications.NotificationsHandler`, puis cliquez sur **Terminer**

    ![](./media/notification-hubs-android-get-started/notification-hub-android-new-class.png)

    Cela a permis de créer la classe MyHandler.

11. Ajoutez les instructions import suivantes :

        import android.app.NotificationManager;
        import android.app.PendingIntent;
        import android.content.Context;
        import android.content.Intent;
        import android.os.Bundle;
        import android.support.v4.app.NotificationCompat;

12. Ajoutez le code suivant à la classe :

        public static final int NOTIFICATION_ID = 1;
        private NotificationManager mNotificationManager;
        NotificationCompat.Builder builder;
        Context ctx;

            
        @Override
        public void onReceive(Context context, Bundle bundle) {
            ctx = context;
            String nhMessage = bundle.getString("msg");

            sendNotification(nhMessage);
        }

        private void sendNotification(String msg) {
            mNotificationManager = (NotificationManager)
                      ctx.getSystemService(Context.NOTIFICATION_SERVICE);

            PendingIntent contentIntent = PendingIntent.getActivity(ctx, 0,
                  new Intent(ctx, MainActivity.class), 0);

            NotificationCompat.Builder mBuilder =
                  new NotificationCompat.Builder(ctx)
                  .setSmallIcon(R.drawable.ic_launcher)
                  .setContentTitle("Démonstration Notification Hub")
                  .setStyle(new NotificationCompat.BigTextStyle()
                             .bigText(msg))
                  .setContentText(msg);

             mBuilder.setContentIntent(contentIntent);
             mNotificationManager.notify(NOTIFICATION_ID, mBuilder.build());
        }

Envoi d'une notification vers votre application
-----------------------------------------------

Vous pouvez envoyer des notifications en utilisant Notification Hubs à partir de tous les serveurs principaux qui utilisent l'[interface REST](http://msdn.microsoft.com/en-us/library/windowsazure/dn223264.aspx). Dans ce didacticiel, nous présentons deux manières d'envoyer des notifications : avec une application console .NET et avec un service mobile utilisant un script node.

### Pour envoyer des notifications en utilisant une application de console .NET :

1.  Créez une application console Visual C\# :

	![][20]

2.  Ajoutez une référence au Kit de développement logiciel (SDK) Azure Service Bus à l'aide du [package NuGet WindowsAzure.ServiceBus](http://nuget.org/packages/WindowsAzure.ServiceBus/). Dans le menu principal de Visual Studio, cliquez sur **Outils**, sur **Library Package Manager**, puis sur **Console du gestionnaire de package**. Dans la fenêtre de la console, tapez :

         Install-Package WindowsAzure.ServiceBus

    et appuyez sur Entrée.

3.  Ouvrez le fichier Program.cs et ajoutez l'instruction using suivante :

         using Microsoft.ServiceBus.Notifications;

4.  Dans votre classe `Program`, ajoutez la méthode suivante :

         private static async void SendNotificationAsync()
         {
             NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
             await hub.SendGcmNativeNotificationAsync("{  private static async void SendNotificationAsync()
         {
             NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
             await hub.SendGcmNativeNotificationAsync("{ \"data\" : {\"msg\":\"Hello d'Azure !\"}}");
         }
        quot;data private static async void SendNotificationAsync()
         {
             NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
             await hub.SendGcmNativeNotificationAsync("{ \"data\" : {\"msg\":\"Hello d'Azure !\"}}");
         }
        quot; : { private static async void SendNotificationAsync()
         {
             NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
             await hub.SendGcmNativeNotificationAsync("{ \"data\" : {\"msg\":\"Hello d'Azure !\"}}");
         }
        quot;msg private static async void SendNotificationAsync()
         {
             NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
             await hub.SendGcmNativeNotificationAsync("{ \"data\" : {\"msg\":\"Hello d'Azure !\"}}");
         }
        quot;: private static async void SendNotificationAsync()
         {
             NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
             await hub.SendGcmNativeNotificationAsync("{ \"data\" : {\"msg\":\"Hello d'Azure !\"}}");
         }
        quot;Hello d'Azure ! private static async void SendNotificationAsync()
         {
             NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
             await hub.SendGcmNativeNotificationAsync("{ \"data\" : {\"msg\":\"Hello d'Azure !\"}}");
         }
        quot;}}");
         }

5.  Ajoutez ensuite les lignes suivantes dans votre méthode Main :

          SendNotificationAsync();
          Console.ReadLine();

### Pour envoyer une notification à l'aide d'un service mobile

1.  Connectez-vous au [portail de gestion Azure](https://manage.windowsazure.com/) et sélectionnez votre service mobile. Si vous ne possédez pas déjà un service mobile, consultez la page [Prise en main de Mobile Services](/en-us/develop/mobile/tutorials/get-started/#create-new-service).

2.  Sélectionnez l'onglet **Scheduler** en haut.

	![][22]

3.  Créez un travail planifié, insérez un nom, puis sélectionnez **On demand**.

	![][23]

4.  Lorsque le travail est créé, cliquez sur son nom. Cliquez ensuite sur l'onglet **Script** dans la barre supérieure.

5.  Insérez le script suivant dans votre fonction de planificateur. Remplacez les espaces réservés par le nom du Notification Hub et la chaîne de connexion pour *DefaultFullSharedAccessSignature* obtenue précédemment. Cliquez sur **Enregistrer**.

		var azure = require('azure');
		var notificationHubService = azure.createNotificationHubService('<hub name>', '<connection string>');
		notificationHubService.gcm.send(null,'{"data":{"msg" : "Hello de Mobile Services !"}}',
    		function (error) 
			{
           	  if (!error) {
                console.warn("Notification valide");
              }
              else
              {
                console.warn("Notification en échec" + error);
              }
           }
         );

Test de votre application
-------------------------

Pour tester cette application avec un téléphone réel, connectez-le à votre ordinateur avec un câble USB.

Pour tester cette application avec l'émulateur :

1.  Assurez-vous d'utiliser un appareil virtuel Android (AVD) prenant en charge les API Google.

2.  Dans **Fenêtre**, cliquez sur **Gestionnaire d'appareil virtuel Android**, choisissez votre appareil, puis cliquez sur **Modifier**.

	![][18]

3.  Sélectionnez **API Google** dans **Cible**, puis cliquez sur **OK**.

	![][19]

4.  Afin de recevoir des notifications Push, vous devez configurer un compte Google sur votre appareil virtuel Android (dans l'émulateur, accédez à **Paramètres**, puis cliquez sur **Ajouter un compte**). Assurez-vous également que l'émulateur est connecté à Internet.

Quel que soit l'appareil que vous choisissez, procédez comme suit :

1.  Dans la barre d'outils supérieure d'Eclipse, cliquez sur **Exécuter**, puis sélectionnez votre application. Cette opération charge votre application sur le téléphone attaché ou lance l'émulateur et charge et exécute l'application.

2.  L'application extrait le *registrationId* de GCM et s'inscrit auprès du Notification Hub.

3.  À présent, utilisez l'une des méthodes de la section précédente pour envoyer une notification vers votre application :
    -   Si vous utilisez une application console .Net, appuyez sur la touche F5 dans Visual Studio pour exécuter l'application, ce qui envoie une notification.
    -   Sinon, si vous utilisez un script Mobiles Services, cliquez sur **Exécuter une fois** sur la barre inférieure de votre écran de services mobiles, et le script envoie une notification.


4.  Une icône s'affiche dans la zone de notification (coin supérieur gauche). Tirez le tiroir de notification afin d'afficher la notification.

	![][21]

Étapes suivantes
----------------

Dans cet exemple simple, vous avez envoyé des notifications à tous vos appareils Android. Afin de cibler des utilisateurs spécifiques, consultez le didacticiel [Utilisation des Notification Hubs pour envoyer des notifications Push aux utilisateurs](/en-us/manage/services/notification-hubs/notify-users-aspnet), et, si vous souhaitez segmenter vos utilisateurs par groupes d'intérêt, vous pouvez lire [Utilisation des Notification Hubs pour diffuser les dernières nouvelles](/en-us/manage/services/notification-hubs/breaking-news-dotnet). Pour plus d'informations sur l'utilisation de Notification Hubs, consultez les pages [Vue d'ensemble des concentrateurs de notification](http://msdn.microsoft.com/en-us/library/jj927170.aspx) et [Procédures Notification Hubs pour Android](http://msdn.microsoft.com/en-us/library/dn282661.aspx).

<!-- Images. -->
[1]: ./media/notification-hubs-android-get-started/mobile-services-google-new-project.png
[2]: ./media/notification-hubs-android-get-started/mobile-services-google-create-server-key.png
[3]: ./media/notification-hubs-android-get-started/mobile-services-google-create-server-key2.png
[4]: ./media/notification-hubs-android-get-started/mobile-services-google-create-server-key3.png
[5]: ./media/notification-hubs-android-get-started/mobile-services-google-enable-GCM.png
[6]: ./media/notification-hubs-android-get-started/notification-hub-android-new-class.png
[7]: ./media/notification-hubs-android-get-started/notification-hub-create-from-portal.png
[8]: ./media/notification-hubs-android-get-started/notification-hub-create-from-portal2.png
[9]: ./media/notification-hubs-android-get-started/notification-hub-select-from-portal.png
[10]: ./media/notification-hubs-android-get-started/notification-hub-select-from-portal2.png
[11]: ./media/notification-hubs-android-get-started/notification-hub-configure-android.png
[12]: ./media/notification-hubs-android-get-started/notification-hub-connection-strings.png

[13]: ./media/notification-hubs-android-get-started/notification-hub-create-android-app.png
[14]: ./media/notification-hubs-android-get-started/notification-hub-create-android-app2.png
[15]: ./media/notification-hubs-android-get-started/notification-hub-create-android-app4.png
[16]: ./media/notification-hubs-android-get-started/notification-hub-create-android-app5.png
[17]: ./media/notification-hubs-android-get-started/notification-hub-create-android-app6.png

[18]: ./media/notification-hubs-android-get-started/notification-hub-create-android-app7.png
[19]: ./media/notification-hubs-android-get-started/notification-hub-create-android-app8.png

[20]: ./media/notification-hubs-android-get-started/notification-hub-create-console-app.png
[21]: ./media/notification-hubs-android-get-started/notification-hub-android-toast.png
[22]: ./media/notification-hubs-android-get-started/notification-hub-scheduler1.png
[23]: ./media/notification-hubs-android-get-started/notification-hub-scheduler2.png
[29]: ./media/mobile-services-android-get-started-push/mobile-eclipse-import-Play-library.png

<!-- URLs. -->
[Mobile Services Android SDK]: https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Get started with Mobile Services]: /en-us/develop/mobile/tutorials/get-started/#create-new-service
[Get started with data]: /en-us/develop/mobile/tutorials/get-started-with-data-android
[Get started with authentication]: /en-us/develop/mobile/tutorials/get-started-with-users-android
[Get started with push notifications]: /en-us/develop/mobile/tutorials/get-started-with-push-android
[Push notifications to app users]: /en-us/develop/mobile/tutorials/push-notifications-to-users-android
[Authorize users with scripts]: /en-us/develop/mobile/tutorials/authorize-users-in-scripts-android
[JavaScript and HTML]: /en-us/develop/mobile/tutorials/get-started-with-push-js
[Referencing a library project]: http://go.microsoft.com/fwlink/?LinkId=389800
[Azure Management Portal]: https://manage.windowsazure.com/
[wns object]: http://go.microsoft.com/fwlink/p/?LinkId=260591
[Notification Hubs Guidance]: http://msdn.microsoft.com/en-us/library/jj927170.aspx
[Notification Hubs How-To for Android]: http://msdn.microsoft.com/en-us/library/dn282661.aspx

[Use Notification Hubs to push notifications to users]: /en-us/manage/services/notification-hubs/notify-users-aspnet
[Use Notification Hubs to send breaking news]: /en-us/manage/services/notification-hubs/breaking-news-dotnet

