<properties linkid="develop-notificationhubs-tutorials-get-started-android" urlDisplayName="Get Started" pageTitle="Get Started with Azure Notification Hubs" metaKeywords="" description="Learn how to use Azure Notification Hubs to push notifications." metaCanonical="" services="notification-hubs" documentationCenter="Mobile" title="Get started with Notification Hubs" authors="ricksal" solutions="" manager="dwrede" editor="" />

<tags ms.service="notification-hubs" ms.workload="mobile" ms.tgt_pltfrm="Mobile-Android" ms.devlang="Java" ms.topic="article" ms.date="01/01/1900" ms.author="ricksal" />

# Prise en main de Notification Hubs

<div class="dev-center-tutorial-selector sublanding"><a href="/fr-fr/documentation/articles/notification-hubs-windows-store-dotnet-get-started/" title="Windows Universel">Windows Universel</a><a href="/fr-fr/documentation/articles/notification-hubs-windows-phone-get-started/" title="Windows Phone">Windows Phone</a><a href="/fr-fr/documentation/articles/notification-hubs-ios-get-started/" title="iOS">iOS</a><a href="/fr-fr/documentation/articles/notification-hubs-android-get-started/" title="Android" class="current">Android</a><a href="/fr-fr/documentation/articles/notification-hubs-kindle-get-started/" title="Kindle">Kindle</a><a href="/fr-fr/documentation/articles/partner-xamarin-notification-hubs-ios-get-started/" title="Xamarin.iOS">Xamarin.iOS</a><a href="/fr-fr/documentation/articles/partner-xamarin-notification-hubs-android-get-started/" title="Xamarin.Android">Xamarin.Android</a></div>

Cette rubrique montre comment utiliser Azure Notification Hubs pour envoyer des notifications Push vers une application Android.
Dans ce didacticiel, vous allez créer une application Android vide qui reçoit des notifications Push à l'aide de Google Cloud Messaging (GCM). Une fois la création terminée, vous pouvez diffuser des notifications Push sur tous les appareils sur lesquels votre application est installée au moyen de votre Notification Hub.

Ce didacticiel vous familiarise avec les étapes de base pour activer les notifications Push :

-   [Activation de Google Cloud Messaging][Activation de Google Cloud Messaging]
-   [Configuration de votre concentrateur de notification][Configuration de votre concentrateur de notification]
-   [Connexion de votre application au concentrateur de notification][Connexion de votre application au concentrateur de notification]
-   [Envoi d'une notification vers votre application][Envoi d'une notification vers votre application]
-   [Test de votre application][Test de votre application]

Ce didacticiel présente un scénario de diffusion simple utilisant les concentrateurs de notification. Suivez scrupuleusement le didacticiel suivant pour apprendre à utiliser Notification Hubs pour accéder à des utilisateurs et groupes d'appareils spécifiques.

Ce didacticiel requiert les éléments suivants :

-   le Kit de développement logiciel (SDK) Android (nous présumons que vous utiliserez Eclipse), que vous pouvez télécharger [ici][ici] ;
-   le [Kit de développement logiciel (SDK) Mobile Services Android][Kit de développement logiciel (SDK) Mobile Services Android]

Vous devez suivre ce didacticiel avant de pouvoir suivre tous les autres didacticiels Notification Hubs pour les applications Android.

<div class="dev-callout"><strong>Remarque</strong> <p>Pour suivre ce didacticiel, vous avez besoin d'un compte Azure actif. Si vous ne poss&eacute;dez pas de compte, vous pouvez cr&eacute;er un compte d'&eacute;valuation gratuit en quelques minutes. Pour plus d'informations, consultez la page <a href="http://www.windowsazure.com/fr-fr/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Ffr-fr%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Version d'&eacute;valuation gratuite d'Azure</a>.</p></div>

## <span id="register"></span></a>Activation de Google Cloud Messaging

[WACOM.INCLUDE [Enable GCM](../includes/mobile-services-enable-Google-cloud-messaging.md)]

Ensuite, vous allez utiliser cette valeur de clé API afin d'activer Notification Hubs pour l'authentification avec GCM et l'envoi de notifications Push au nom de votre application.

## <span id="configure-hub"></span></a>Configuration de votre concentrateur de notification

1.  Connectez-vous au [portail de gestion Azure][portail de gestion Azure] et cliquez sur **+NOUVEAU** en bas de l'écran.

2.  Cliquez sur **Services d'application**, puis sur **Service Bus**, **Concentrateur de notification**, **Création rapide**.

    ![][0]

3.  Tapez un nom pour votre concentrateur de notification, sélectionnez la région souhaitée, puis cliquez sur **Créer un concentrateur de notification**.

    ![][1]

4.  Cliquez sur l'espace de noms que vous venez de créer (en général ***nom du concentrateur de notification*-ns**), puis cliquez sur l'onglet **Configurer** en haut.

    ![][2]

5.  Cliquez sur l'onglet **Concentrateurs de notification** en haut de la page, puis cliquez sur celui que vous venez de créer.

    ![][3]

6.  Cliquez sur l'onglet **Configurer** dans la partie supérieure, saisissez la valeur **Clé de l'API** obtenue à la section précédente, puis cliquez sur **Enregistrer**.

    ![][4]

7.  Sélectionnez l'onglet **Tableau de bord** en haut, puis cliquez sur **Afficher la chaîne de connexion**. Notez les deux chaînes de connexion.

Votre concentrateur de notification est configuré pour GCM, et vous disposez des chaînes de connexion pour inscrire votre application et envoyer des notifications Push.

## <span id="connecting-app"></span></a>Connexion de votre application au concentrateur de notification

### Création d'un projet Android

1.  Dans Eclipse ADT, créez un projet Android (File, New, Android Application).

    ![][5]

2.  Assurez-vous que **Minimum Required SDK** est défini sur *API 8 : Android 2.2 (Froyo)* et que les deux entrées SDK suivantes sont définies sur la version la plus récente disponible. Sélectionnez Next, suivez l'Assistant, assurez-vous que l'option **Create activity** est sélectionnée afin de créer une activité vierge. Acceptez l'icône Launcher par défaut dans la zone suivante, puis cliquez sur **Finish** dans la dernière zone.

    ![][6]

### Ajout de services Google Play au projet

[WACOM.INCLUDE [Add Play Services](../includes/mobile-services-add-Google-play-services.md)]

### Ajout de code

1.  Téléchargez le Kit de développement logiciel (SDK) Android Notification Hubs [ici][Kit de développement logiciel (SDK) Mobile Services Android]. Décompressez le fichier .zip et copiez le fichier notificationhubs\\notification-hubs-0.1.jar dans le répertoire \\libs de votre projet dans l'Explorateur de package.

2.  Téléchargez et décompressez le [Kit de développement logiciel (SDK) Mobile Services pour Android][Kit de développement logiciel (SDK) Mobile Services Android], ouvrez le dossier **notifications**, copiez le fichier **notifications-1.0.1.jar** dans le dossier *libs* de votre projet Eclipse, puis actualisez le dossier *libs*.

    <div class="dev-callout"><b>Remarque</b>
<p>Les num&eacute;ros &agrave; la fin du nom du fichier peuvent changer dans les versions ult&eacute;rieures du Kit de d&eacute;veloppement logiciel (SDK).</p>
</div>

    Maintenant, configurez l'application afin d'obtenir un *registrationId* depuis GCM, et utilisez-le pour inscrire l'instance d'application auprès du Notification Hub.

3.  Dans le fichier AndroidManifest.xml, ajoutez la ligne suivante juste sous l'élément <uses-sdk></uses-sdk>. Assurez-vous de remplacer `<your package>` par le package que vous avez sélectionné pour votre application à l'étape 1 (`com.yourCompany.wams_notificationhubs` dans cet exemple).

        <uses-permission android:name="android.permission.INTERNET"/>
        <uses-permission android:name="android.permission.GET_ACCOUNTS"/>
        <uses-permission android:name="android.permission.WAKE_LOCK"/>
        <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />

        <permission android:name="<your package>.permission.C2D_MESSAGE" android:protectionLevel="signature" />
        <uses-permission android:name="<your package>.permission.C2D_MESSAGE"/>

4.  Dans la classe **MainActivity**, ajoutez les instructions suivantes.

        import android.os.AsyncTask;    
        import com.google.android.gms.gcm.*;
        import com.microsoft.windowsazure.messaging.*;
        import com.microsoft.windowsazure.notifications.NotificationsManager;

5.  Ajoutez les membres privés suivants dans la partie supérieure de la classe.

    <div class="dev-callout"><b>Remarque</b>
<p>Assurez-vous de d&eacute;finir SENDER_ID sur le num&eacute;ro de projet obtenu pr&eacute;c&eacute;demment.</p>
</div>

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

    <div class="dev-callout"><b>Remarque</b>
<p>Remplacez l'espace r&eacute;serv&eacute; par votre nom de package.</p>
</div>

        <receiver android:name="com.microsoft.windowsazure.notifications.NotificationsBroadcastReceiver"
            android:permission="com.google.android.c2dm.permission.SEND">
            <intent-filter>
                <action android:name="com.google.android.c2dm.intent.RECEIVE" />
                <category android:name="**my_app_package**" />
            </intent-filter>
        </receiver>

9.  Dans l'Explorateur de package, cliquez avec le bouton droit sur le package (sous le nœud `src`), cliquez sur **Nouveau**, puis sur **Classe**.

10. Dans **Nom**, tapez `MyHandler`, dans **Superclasse**, tapez `com.microsoft.windowsazure.notifications.NotificationsHandler`, puis cliquez sur **Terminer**

    ![][7]

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
                  .setContentTitle("Notification Hub Demo")
                  .setStyle(new NotificationCompat.BigTextStyle()
                             .bigText(msg))
                  .setContentText(msg);

             mBuilder.setContentIntent(contentIntent);
             mNotificationManager.notify(NOTIFICATION_ID, mBuilder.build());
        }

## <a name="send"></a>Envoi d'une notification vers votre application

Vous pouvez envoyer des notifications en utilisant Notification Hubs à partir des serveurs principaux en utilisant l'[interface REST][interface REST]. Dans ce didacticiel, vous envoyez des notifications avec une application console .NET. Pour obtenir un exemple expliquant comment envoyer des notifications à partir d'un serveur principal Azure Mobile Services intégré à Notification Hubs, voir **Prise en main des notifications Push dans Mobile Services** ([Service principal .NET][Service principal .NET] | [Service principal JavaScript][Service principal .NET]). Pour obtenir un exemple expliquant comment envoyer des notifications à l'aide d'API REST, voir **Utilisation de Notification Hubs depuis Java/PHP** ([Java][Java] | [PHP][PHP]).

1.  Dans Visual Studio, dans le menu **Fichier**, sélectionnez **Nouveau**, puis **Projet...**. Sous **Visual C#**, cliquez sur **Windows**, sur **Application console**, puis sur **OK**.

    ![][8]

    Un projet d'application console est créé.

2.  Dans le menu **Outils**, cliquez sur **Gestionnaire de package de bibliothèques**, puis sur **Console du Gestionnaire de package**.

    La Console du Gestionnaire de package apparaît.

3.  Dans la fenêtre de la console, exécutez la commande suivante :

        Install-Package WindowsAzure.ServiceBus

    Cela permet d'ajouter une référence au Kit de développement logiciel (SDK) Azure Service Bus à l'aide du [package NuGet WindowsAzure.ServiceBus][package NuGet WindowsAzure.ServiceBus].

4.  Ouvrez le fichier Program.cs et ajoutez l'instruction `using` suivante :

        using Microsoft.ServiceBus.Notifications;

5.  Dans la classe **Program**, ajoutez la méthode suivante :

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            await hub.SendGcmNativeNotificationAsync("{ \"data\" : {\"msg\":\"Hello from Azure!\"}}");
        }

    Remplacez l'espace réservé « hub name » par le nom du Notification Hub affiché dans le portail, sous l'onglet **Notification Hubs**. Remplacez aussi l'espace réservé de chaîne de connexion par la chaîne de connexion nommée **DefaultFullSharedAccessSignature** que vous avez obtenue dans la section « Configuration de votre concentrateur de notification ».

    > [WACOM.NOTE]Assurez-vous d'utiliser la chaîne de connexion avec un accès **Total**, et non un accès **Écouter**. La chaîne d'accès Écouter n'est pas autorisée à envoyer des notifications.

6.  Ajoutez les lignes suivantes à la méthode **Main** :

         SendNotificationAsync();
         Console.ReadLine();

## <a name="run-app"></a>Test de votre application

Avant de tester l'application sur l'émulateur, vous devez effectuer les étapes de configuration de l'émulateur suivantes (ignorez-les si vous effectuez les tests sur un appareil physique).

1.  Assurez-vous d'utiliser un appareil virtuel Android (AVD) prenant en charge les API Google.

2.  Dans **Fenêtre**, cliquez sur **Gestionnaire d'appareil virtuel Android**, choisissez votre appareil, puis cliquez sur **Modifier**.

    ![][9]

3.  Sélectionnez **API Google** dans **Cible**, puis cliquez sur **OK**.

    ![][10]

4.  Afin de recevoir des notifications Push, vous devez configurer un compte Google sur votre appareil virtuel Android (dans l'émulateur, accédez à **Paramètres**, puis cliquez sur **Ajouter un compte**). Assurez-vous également que l'émulateur est connecté à Internet.

Utilisez les étapes suivantes pour exécuter l'application sur un appareil ou sur l'émulateur :

1.  Dans la barre d'outils supérieure d'Eclipse, cliquez sur **Exécuter**, puis sélectionnez votre application.

    L'émulateur démarre (si vous en utilisez un), puis charge et exécute l'application. L'application extrait le *registrationId* de GCM et s'inscrit auprès du notification hub.

2.  Appuyez sur la touche F5 dans Visual Studio pour exécuter l'application console.

    Une notification est envoyée à votre application.

3.  Quand une icône apparaît dans la zone de notification (en haut à gauche), tirez le tiroir de notification pour afficher la notification.

    ![][11]

## <a name="next-steps"> </a>Étapes suivantes

Dans cet exemple simple, vous avez envoyé des notifications à tous vos appareils Android. Afin de cibler des utilisateurs spécifiques, consultez le didacticiel [Utilisation de Notification Hubs pour envoyer des notifications Push aux utilisateurs][Utilisation de Notification Hubs pour envoyer des notifications Push aux utilisateurs], et, si vous souhaitez segmenter vos utilisateurs par groupes d'intérêt, vous pouvez lire [Utilisation des Notification Hubs pour diffuser les dernières nouvelles][Utilisation des Notification Hubs pour diffuser les dernières nouvelles]. Découvrez plus en détail comment utiliser Notification Hubs dans [Vue d'ensemble des concentrateurs de notification][Vue d'ensemble des concentrateurs de notification].

<!-- Images. --> 
<!-- URLs. -->

  [Activation de Google Cloud Messaging]: #register
  [Configuration de votre concentrateur de notification]: #configure-hub
  [Connexion de votre application au concentrateur de notification]: #connecting-app
  [Envoi d'une notification vers votre application]: #send
  [Test de votre application]: #run-app
  [ici]: http://go.microsoft.com/fwlink/?LinkId=389797
  [Enable GCM]: ../includes/mobile-services-enable-Google-cloud-messaging.md
  [portail de gestion Azure]: https://manage.windowsazure.com/
  [0]: ./media/notification-hubs-android-get-started/notification-hub-create-from-portal.png
  [1]: ./media/notification-hubs-android-get-started/notification-hub-create-from-portal2.png
  [2]: ./media/notification-hubs-android-get-started/notification-hub-select-from-portal.png
  [3]: ./media/notification-hubs-android-get-started/notification-hub-select-from-portal2.png
  [4]: ./media/notification-hubs-android-get-started/notification-hub-configure-android.png
  [5]: ./media/notification-hubs-android-get-started/notification-hub-create-android-app.png
  [6]: ./media/notification-hubs-android-get-started/notification-hub-create-android-app2.png
  [Add Play Services]: ../includes/mobile-services-add-Google-play-services.md
  [7]: ./media/notification-hubs-android-get-started/notification-hub-android-new-class.png
  [interface REST]: http://msdn.microsoft.com/fr-fr/library/windowsazure/dn223264.aspx
  [Service principal .NET]: /fr-fr/documentation/articles/mobile-services-javascript-backend-android-get-started-push/
  [Java]: /fr-fr/documentation/articles/notification-hubs-java-backend-how-to/
  [PHP]: /fr-fr/documentation/articles/notification-hubs-php-backend-how-to/
  [8]: ./media/notification-hubs-android-get-started/notification-hub-create-console-app.png
  [package NuGet WindowsAzure.ServiceBus]: http://nuget.org/packages/WindowsAzure.ServiceBus/
  [9]: ./media/notification-hubs-android-get-started/notification-hub-create-android-app7.png
  [10]: ./media/notification-hubs-android-get-started/notification-hub-create-android-app8.png
  [11]: ./media/notification-hubs-android-get-started/notification-hub-android-toast.png
  [Utilisation de Notification Hubs pour envoyer des notifications Push aux utilisateurs]: /fr-fr/manage/services/notification-hubs/notify-users-aspnet
  [Utilisation des Notification Hubs pour diffuser les dernières nouvelles]: /fr-fr/manage/services/notification-hubs/breaking-news-dotnet
  [Vue d'ensemble des concentrateurs de notification]: http://msdn.microsoft.com/fr-fr/library/jj927170.aspx
