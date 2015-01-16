<properties urlDisplayName="Get Started" pageTitle="Prise en main d'Azure Notification Hubs" metaKeywords="" description="Découvrez comment utiliser Azure Notification Hubs pour envoyer des notifications Push." metaCanonical="" services="notification-hubs" documentationCenter="Mobile" title="Get started with Notification Hubs" authors="ricksal" solutions="" manager="dwrede" editor="" />

<tags ms.service="notification-hubs" ms.workload="mobile" ms.tgt_pltfrm="Mobile-Android" ms.devlang="Java" ms.topic="article" ms.date="11/21/2014" ms.author="ricksal" />
# Prise en main de Notification Hubs

<div class="dev-center-tutorial-selector sublanding"><a href="/fr-fr/documentation/articles/notification-hubs-windows-store-dotnet-get-started/" title="Windows Universal">Windows Universal</a><a href="/fr-fr/documentation/articles/notification-hubs-windows-phone-get-started/" title="Windows Phone">Windows Phone</a><a href="/fr-fr/documentation/articles/notification-hubs-ios-get-started/" title="iOS">iOS</a><a href="/fr-fr/documentation/articles/notification-hubs-android-get-started/" title="Android" class="current">Android</a><a href="/fr-fr/documentation/articles/notification-hubs-kindle-get-started/" title="Kindle">Kindle</a><a href="/fr-fr/documentation/articles/notification-hubs-baidu-get-started/" title="Baidu">Baidu</a><a href="/fr-fr/documentation/articles/partner-xamarin-notification-hubs-ios-get-started/" title="Xamarin.iOS">Xamarin.iOS</a><a href="/fr-fr/documentation/articles/partner-xamarin-notification-hubs-android-get-started/" title="Xamarin.Android">Xamarin.Android</a></div>

Cette rubrique montre comment utiliser Azure Notification Hubs pour envoyer des notifications Push vers une application Android. 
Dans ce didacticiel, vous allez créer une application Android vide qui reçoit des notifications Push à l'aide de Google Cloud Messaging (GCM). Une fois la création terminée, vous pouvez diffuser des notifications Push sur tous les appareils sur lesquels votre application est installée au moyen de votre concentrateur de notification.

Ce didacticiel vous familiarise avec les étapes de base pour activer les notifications Push :

* [Activation de Google Cloud Messaging](#register)
* [Configuration de votre concentrateur de notification](#configure-hub)
* [Connexion de votre application au concentrateur de notification](#connecting-app)
* [Envoi d'une notification vers votre application](#send)
* [Test de votre application](#run-app)

Ce didacticiel présente un scénario de diffusion simple utilisant les concentrateurs de notification. Suivez scrupuleusement le didacticiel suivant pour apprendre à utiliser Notification Hubs pour accéder à des utilisateurs et groupes d'appareils spécifiques. 

Ce didacticiel requiert les éléments suivants :

+ Le Kit de développement logiciel (SDK) Android (nous présumons que vous utiliserez Eclipse), que vous pouvez télécharger <a href="http://go.microsoft.com/fwlink/?LinkId=389797">ici</a>
+ Le [Kit de développement logiciel (SDK) Mobile Services pour Android]

Vous devez suivre ce didacticiel avant de pouvoir suivre tous les autres didacticiels Notification Hubs pour les applications Android. 

<div class="dev-callout"><strong>Remarque</strong> <p>Pour suivre ce didacticiel, vous avez besoin d'un compte Azure actif. Si vous ne possédez pas de compte, vous pouvez créer un compte d'évaluation gratuit en quelques minutes. Pour plus d'informations, consultez les rubriques suivantes : <a href="http://www.windowsazure.com/fr-fr/pricing/free-trial/?WT.mc_id=A0E0E5C02&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Ffr-fr%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Version d'évaluation gratuite d'Azure</a>.</p></div>

##<a id="register"></a>Activation de Google Cloud Messaging

[WACOM.INCLUDE [Enable GCM](../includes/mobile-services-enable-Google-cloud-messaging.md)]

Ensuite, vous allez utiliser cette valeur de clé API afin d'activer Notification Hubs pour l'authentification avec GCM et l'envoi de notifications Push au nom de votre application.

##<a id="configure-hub"></a>Configuration de votre concentrateur de notification

1. Connectez-vous au [portail de gestion Azure], puis cliquez sur **+NOUVEAU** en bas de l'écran.

2. Cliquez sur **Services d'application**, sur **Service Bus**, sur **Concentrateur de notification**, puis sur **Création rapide**.

   	![][7]

3. Tapez un nom pour votre concentrateur de notification, sélectionnez la région souhaitée, puis cliquez sur **Créer un concentrateur de notification**.

   	![][8]

4. Cliquez sur l'espace de noms que vous venez de créer (en général ***nom du concentrateur de notification*-ns**), puis cliquez sur l'onglet **Configurer** en haut.

   	![][9]

5. Cliquez sur l'onglet **Concentrateurs de notification** en haut de la page, puis cliquez sur celui que vous venez de créer.

   	![][10]

6. Cliquez sur l'onglet **Configurer** en haut, entrez la valeur **Clé API** obtenue à la section précédente, puis cliquez sur **Enregistrer**.

   	![][11]

7. Sélectionnez l'onglet **Tableau de bord** en haut, puis cliquez sur **Afficher la chaîne de connexion**. Notez les deux chaînes de connexion.


Votre concentrateur de notification est configuré pour GCM, et vous disposez des chaînes de connexion pour inscrire votre application et envoyer des notifications Push.

##<a id="connecting-app"></a>Connexion de votre application au concentrateur de notification

###Création d'un projet Android

1. Dans Eclipse ADT, créez un projet Android (File, New, Android Application).

   	![][13]

2. Assurez-vous que **Minimum Required SDK (SDK minimum requis)** est défini sur *API 8: Android 2.2 (Froyo)* et que les deux entrées SDK suivantes sont définies sur la version la plus récente disponible. Sélectionnez Next, suivez l'Assistant, assurez-vous que l'option **Create activity** est sélectionnée afin de créer une activité vierge. Acceptez l'icône Launcher par défaut dans la zone suivante, puis cliquez sur **Finish** dans la dernière zone.

   	![][14]

###Ajout de services Google Play au projet

[WACOM.INCLUDE [Add Play Services](../includes/mobile-services-add-Google-play-services.md)]

###Ajout de code

1. Téléchargez le Kit de développement logiciel (SDK) Notification Hubs pour Android <a href="https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409">ici</a>. Décompressez le fichier .zip et copiez le fichier notificationhubs\notification-hubs-0.1.jar dans le répertoire \libs de votre projet dans l'Explorateur de package.

2. Téléchargez et décompressez le [Kit de développement logiciel (SDK) Mobile Services pour Android], ouvrez le dossier **notifications**, copiez le fichier **notifications-1.0.1.jar** dans le dossier *libs* de votre projet Eclipse, puis actualisez le dossier *libs*.

    <div class="dev-callout"><b>Remarque</b>
	<p>Les numéros à la fin du nom du fichier peuvent changer dans les versions ultérieures du Kit de développement logiciel (SDK).</p>
    </div>

	Maintenant, configurez l'application afin d'obtenir un *registrationId* depuis GCM, et utilisez-le pour inscrire l'instance d'application auprès du concentrateur de notification.

3. Dans le fichier AndroidManifest.xml, ajoutez la ligne suivante juste sous l'élément <uses-sdk/>. Assurez-vous de remplacer <your package> par le package que vous avez sélectionné pour votre application à l'étape 1 (com.yourCompany.wams_notificationhubs dans cet exemple).

        <uses-permission android:name="android.permission.INTERNET"/>
		<uses-permission android:name="android.permission.GET_ACCOUNTS"/>
		<uses-permission android:name="android.permission.WAKE_LOCK"/>
		<uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />

		<permission android:name="<your package>.permission.C2D_MESSAGE" android:protectionLevel="signature" />
		<uses-permission android:name="<your package>.permission.C2D_MESSAGE"/>

4. Dans la classe **MainActivity**, ajoutez les instructions suivantes.

		import android.os.AsyncTask;	
		import com.google.android.gms.gcm.*;
		import com.microsoft.windowsazure.messaging.*;
		import com.microsoft.windowsazure.notifications.NotificationsManager;


5. Ajoutez les membres privés suivants dans la partie supérieure de la classe.

	<div class="dev-callout"><b>Remarque</b>
    <p>Assurez-vous de définir SENDER_ID sur le numéro de projet obtenu précédemment.</p>
    </div> 

		private String SENDER_ID = "<your project number>";
		private GoogleCloudMessaging gcm;
		private NotificationHub hub;

6. Dans la méthode **OnCreate**, ajoutez le code suivant et assurez-vous de remplacer les espaces réservés par votre chaîne de connexion avec accès d'écoute obtenue dans la section précédente ainsi que le nom de votre concentrateur de notification qui s'affiche en haut de la page dans Azure pour votre concentrateur (**pas** l'ensemble de l'URL).

		NotificationsManager.handleNotifications(this, SENDER_ID, MyHandler.class);

		gcm = GoogleCloudMessaging.getInstance(this);
        
		String connectionString = "<your listen access connection string>";
		hub = new NotificationHub("<your notification hub name>", connectionString, this);
		
		registerWithNotificationHubs();

7. Dans MainActivity.java, créez la méthode suivante :

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

8. Android n'affichant pas de notifications, vous devez écrire votre propre récepteur. Dans **AndroidManifest.xml**, ajoutez l'élément suivant dans l'élément <application/>.

	<div class="dev-callout"><b>Remarque</b>
    <p>Remplacez l'espace réservé par votre nom de package.</p>
    </div> 

        <receiver android:name="com.microsoft.windowsazure.notifications.NotificationsBroadcastReceiver"
            android:permission="com.google.android.c2dm.permission.SEND">
            <intent-filter>
                <action android:name="com.google.android.c2dm.intent.RECEIVE" />
                <category android:name="**my_app_package**" />
            </intent-filter>
        </receiver>


9. Dans l'Explorateur de package, cliquez avec le bouton droit sur le package (sous le nœud src), cliquez sur **Nouveau**, puis sur **Classe**.

10. Dans **Nom**, tapez MyHandler, dans **Superclasse**, tapez com.microsoft.windowsazure.notifications.NotificationsHandler, puis cliquez sur **Terminer**

	![][6]

	Cela a permis de créer la classe MyHandler.

11. Ajoutez les instructions import suivantes :

		import android.app.NotificationManager;
		import android.app.PendingIntent;
		import android.content.Context;
		import android.content.Intent;
		import android.os.Bundle;
		import android.support.v4.app.NotificationCompat;
		

12. Ajoutez le code suivant à la classe :

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
		          .setContentTitle("Notification Hub Demo")
		          .setStyle(new NotificationCompat.BigTextStyle()
		                     .bigText(msg))
		          .setContentText(msg);
	
		     mBuilder.setContentIntent(contentIntent);
		     mNotificationManager.notify(NOTIFICATION_ID, mBuilder.build());
		}
	

##<a name="send"></a>Envoi d'une notification vers votre application

Vous pouvez envoyer des notifications à l'aide de Notification Hubs à partir de n'importe quel serveur principal utilisant <a href="http://msdn.microsoft.com/fr-fr/library/windowsazure/dn223264.aspx">l'interface REST</a>. Dans ce didacticiel, vous envoyez des notifications avec une application console .NET. Pour obtenir un exemple expliquant comment envoyer des notifications à partir d'un serveur principal Azure Mobile Services intégré à Notification Hubs, consultez la page **Prise en main des notifications Push dans Mobile Services** ([Service principal .NET](/fr-fr/documentation/articles/mobile-services-javascript-backend-android-get-started-push/) | [Service principal JavaScript](/fr-fr/documentation/articles/mobile-services-javascript-backend-android-get-started-push/)).  Pour obtenir un exemple expliquant comment envoyer des notifications à l'aide d'API REST, consultez la page **Utilisation de Notification Hubs depuis Java/PHP** ([Java](/fr-fr/documentation/articles/notification-hubs-java-backend-how-to/) | [PHP](/fr-fr/documentation/articles/notification-hubs-php-backend-how-to/)).

1. Dans Visual Studio, dans le menu **Fichier**, sélectionnez **Nouveau**, puis **Projet...**. Sous **Visual C#**, cliquez ensuite sur **Windows** et **Application console** et cliquez sur **OK**.  

   	![][20]

	Un projet d'application console est créé.

2. Dans le menu **Outils**, cliquez sur **Gestionnaire de package de bibliothèques**, puis sur **Console du Gestionnaire de package**. 

	La Console du Gestionnaire de package apparaît.

3. Dans la fenêtre de console, exécutez la commande suivante :

        Install-Package WindowsAzure.ServiceBus
    
	Cela permet d'ajouter une référence au Kit de développement logiciel (SDK) Azure Service Bus à l'aide du package NuGet <a href="http://nuget.org/packages/WindowsAzure.ServiceBus/">WindowsAzure.ServiceBus</a>. 

4. Ouvrez le fichier Program.cs et ajoutez l'instruction using suivante :

        using Microsoft.ServiceBus.Notifications;

5. Dans la classe **Program**, ajoutez la méthode suivante :

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            await hub.SendGcmNativeNotificationAsync("{ \"data\" : {\"msg\":\"Hello from Azure!\"}}");
        }

   	Remplacez l'espace réservé " hub name " par le nom du concentrateur de notification affiché sur le portail sous l'onglet **Concentrateurs de notification**. Remplacez aussi l'espace réservé de chaîne de connexion par la chaîne de connexion nommée **DefaultFullSharedAccessSignature** que vous avez obtenue dans la section " Configuration de votre concentrateur de notification ". 

	>[WACOM.NOTE]Assurez-vous d'utiliser la chaîne de connexion avec un accès **Total**, et non un accès **Écouter**. La chaîne d'accès Écouter n'est pas autorisée à envoyer des notifications.

5. Ajoutez les lignes suivantes à la méthode **Main** :

         SendNotificationAsync();
		 Console.ReadLine();

##<a name="run-app"></a>Test de votre application

Avant de tester l'application sur l'émulateur, vous devez effectuer les étapes de configuration de l'émulateur suivantes (ignorez-les si vous effectuez les tests sur un appareil physique).

1. Assurez-vous d'utiliser un appareil virtuel Android (AVD) prenant en charge les API Google.

2. Dans **Window**, cliquez sur **Android Virtual Device Manager**, choisissez votre appareil, puis cliquez sur **Edit**.

   	![][18]

3. Sélectionnez **Google APIs** dans **Target**, puis cliquez sur **OK**.

   	![][19]

4. 	Afin de recevoir des notifications Push, vous devez configurer un compte Google sur votre appareil virtuel Android (dans l'émulateur, accédez à <strong>Paramètres</strong> , puis cliquez sur <strong>Ajouter un compte</strong>). Assurez-vous également que l'émulateur est connecté à Internet.


Utilisez les étapes suivantes pour exécuter l'application sur un appareil ou sur l'émulateur :

1. Dans la barre d'outils supérieure d'Eclipse, cliquez sur **Run**, puis sélectionnez votre application. 
 
	L'émulateur démarre (si vous en utilisez un), puis charge et exécute l'application. L'application extrait le *registrationId* de GCM et s'inscrit auprès du concentrateur de notification.

3. Appuyez sur la touche F5 dans Visual Studio pour exécuter l'application console. 

	Une notification est envoyée à votre application.  
 
5. Quand une icône apparaît dans la zone de notification (en haut à gauche), tirez le tiroir de notification pour afficher la notification.  

   	![][21]

## <a name="next-steps"> </a>Étapes suivantes

Dans cet exemple simple, vous avez envoyé des notifications à tous vos appareils Android. Afin de cibler des utilisateurs spécifiques, consultez le didacticiel [Utilisation de Notification Hubs pour envoyer des notifications Push aux utilisateurs], et, si vous souhaitez segmenter vos utilisateurs par groupes d'intérêt, vous pouvez lire [Utilisation de Notification Hubs pour envoyer les dernières nouvelles]. Découvrez plus en détail comment utiliser Notification Hubs dans [Recommandations relatives à Notification Hubs].


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
[Kit de développement logiciel (SDK) Mobile Services pour Android]: https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409
[Page Soumette une application]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Mes applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Kit de développement logiciel (SDK) Live]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Prise en main de Mobile Services]: /fr-fr/develop/mobile/tutorials/get-started/#create-new-service
[Prise en main des données]: /fr-fr/develop/mobile/tutorials/get-started-with-data-android
[Prise en main de l'authentification]: /fr-fr/develop/mobile/tutorials/get-started-with-users-android
[Prise en main des notifications Push]: /fr-fr/develop/mobile/tutorials/get-started-with-push-android
[Envoi de notifications Push aux utilisateurs de l'application]: /fr-fr/develop/mobile/tutorials/push-notifications-to-users-android
[Autorisation des utilisateurs avec des scripts]: /fr-fr/develop/mobile/tutorials/authorize-users-in-scripts-android
[JavaScript et HTML]: /fr-fr/develop/mobile/tutorials/get-started-with-push-js
[Référencement d'un projet de bibliothèque]: http://go.microsoft.com/fwlink/?LinkId=389800
[Portail de gestion Azure]: https://manage.windowsazure.com/
[objet wns]: http://go.microsoft.com/fwlink/p/?LinkId=260591
[Recommandations relatives à Notification Hubs]: http://msdn.microsoft.com/fr-fr/library/jj927170.aspx

[Utilisation de Notification Hubs pour envoyer des notifications aux utilisateurs]: /fr-fr/manage/services/notification-hubs/notify-users-aspnet
[Utilisation des Notification Hubs pour diffuser les dernières nouvelles]: /fr-fr/manage/services/notification-hubs/breaking-news-dotnet


<!--HONumber=35.1-->
