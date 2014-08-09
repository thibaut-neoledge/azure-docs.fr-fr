<properties linkid="develop-mobile-tutorials-get-started-with-push-js-vs2013" urlDisplayName="Get Started with Push (JS)" pageTitle="Get started with push notifications (Android JavaScript) | Mobile Dev Center" metaKeywords="" description="Learn how to use Windows Azure Mobile Services to send push notifications to your Android JavaScript app." metaCanonical="http://www.windowsazure.com/fr-fr/develop/mobile/tutorials/get-started-with-push-dotnet/" services="" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" authors="ricksal" solutions="" writer="ricksal" manager="" editor="" />

Prise en main des notifications Push dans Mobile Services
=========================================================

[Windows Store C\#](/fr-fr/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-get-started-push "Windows Store C#") [Windows Store JavaScript](/fr-fr/documentation/articles/mobile-services-javascript-backend-windows-store-javascript-get-started-push "Windows Store JavaScript") [Windows Phone](/fr-fr/documentation/articles/mobile-services-javascript-backend-windows-phone-get-started-push "Windows Phone") [iOS](/fr-fr/documentation/articles/mobile-services-ios-get-started-push "iOS") [Android](/fr-fr/documentation/articles/mobile-services-javascript-backend-android-get-started-push "Android")

Cette rubrique montre comment utiliser Azure Mobile Services pour envoyer des notifications Push à une application Android. Dans ce didacticiel, vous ajoutez des notifications Push au projet de démarrage rapide à l'aide du service Google Cloud Messaging (GCM). Une fois que vous avez terminé, votre service mobile envoie une notification Push à chaque fois qu'un enregistrement est inséré.

[WACOM.NOTE] Ce didacticiel présente l'intégration de Mobile Services à Notification Hubs, qui en est actuellement au stade préliminaire. Par défaut, l'envoi de notifications Push à l'aide de Notification Hubs n'est pas activé depuis un service principal JavaScript. Une fois le nouveau concentrateur de notification créé, le processus d'intégration est irréversible. Pour le moment, les notifications Push pour iOS sont uniquement disponibles via la prise en charge Push par défaut décrite dans [cette version de la rubrique](/fr-fr/documentation/articles/mobile-services-android-get-started-push/).

Ce didacticiel vous familiarise avec les étapes de base permettant d'activer les notifications Push :

1.  [Activation de Google Cloud Messaging](#register)
2.  [Configuration de Mobile Services](#configure)
3.  [Ajout de notifications Push à votre application](#add-push)
4.  [Mise à jour des scripts pour l'envoi de notifications Push](#update-scripts)
5.  [Insertion de données pour recevoir des notifications](#test)

Ce didacticiel est basé sur le démarrage rapide de Mobile Services. Avant de le lancer, suivez d'abord le didacticiel [Prise en main de Mobile Services](/en-us/develop/mobile/tutorials/get-started/) ou [Prise en main des données](/en-us/develop/mobile/tutorials/get-started-with-data-js/) pour connecter votre projet au service mobile.

Activation de Google Cloud Messaging
------------------------------------

[WACOM.INCLUDE [Enable GCM](../includes/mobile-services-enable-Google-cloud-messaging.md)]

Ensuite, vous allez utiliser cette valeur de clé API afin d'activer Mobile Services pour l'authentification avec GCM et l'envoi des notifications Push au nom de votre application.

Configuration de Mobile Services pour l'envoi de requêtes Push
--------------------------------------------------------------

1.  Connectez-vous au [portail de gestion Windows Azure](https://manage.windowsazure.com/), cliquez sur **Mobile Services**, puis sur l'application.

	![](./media/mobile-services-android-get-started-push/mobile-services-selection.png)

2.  Dans l'onglet **Push**, cliquez sur **Activer la transmission push améliorée**, puis cliquez sur **Oui** pour accepter la modification de la configuration.

    ![](./media/mobile-services-android-get-started-push/mobile-enable-enhanced-push.png)

    ![](../includes/media/mobile-services-javascript-backend-register-windows-store-app/mobile-enable-enhanced-push.png)

    La configuration est ainsi mise à jour sur votre service mobile afin d'utiliser la fonctionnalité de notification Push améliorée fournie par Notification Hubs. Votre service mobile payant vous donne gratuitement accès à une partie de Notification Hubs. Pour plus d'informations, consultez la rubrique [Mobile Services - Tarification](http://go.microsoft.com/fwlink/p/?LinkID=311786).

    **Important**

    Cette opération réinitialise vos informations d'identification Push et modifie le comportement des méthodes Push dans vos scripts. Ces modifications sont irréversibles. N'utilisez pas cette méthode pour ajouter un concentrateur de notification à un service mobile de production. Pour plus d'informations sur l'activation de notifications Push améliorées dans un service mobile de production, consultez [ce guide](http://go.microsoft.com/fwlink/p/?LinkId=391951).

3.  Entrez la valeur **API Key** fournie par GCM au cours de la procédure précédente, puis cliquez sur **Enregistrer**.

	![](./media/mobile-services-android-get-started-push/mobile-push-tab-android.png)

    **Important**

    Lorsque vous définissez vos informations d'identification GCM pour les notifications Push améliorées dans l'onglet Push du portail, celles-ci sont partagées avec Notification Hubs pour configurer le concentrateur de notification avec votre application.

Votre service mobile et votre application sont désormais configurés pour fonctionner avec GCM et Notification Hubs.

Ajout de notifications Push à votre application
-----------------------------------------------

### Vérification de la version du Kit de développement logiciel (SDK) Android

[WACOM.INCLUDE [Verify SDK](../includes/mobile-services-verify-android-sdk-version.md)]

L'étape suivante consiste à installer les services Google Play. Google Cloud Messaging a des spécifications requises d'API minimales pour le développement et les tests, auxquelles la propriété **minSdkVersion** du manifeste doit se conformer.

Si vous envisagez de procéder à un test avec un appareil ancien, consultez la rubrique [Configuration du Kit de développement logiciel (SDK) des services Google Play](http://go.microsoft.com/fwlink/?LinkId=389801) pour déterminer comment définir cette valeur.

### Ajout de services Google Play au projet

[WACOM.INCLUDE [Add Play Services](../includes/mobile-services-add-Google-play-services.md)]

### Ajout de code

1.  Ouvrez le fichier `AndroidManifest.xml`. Dans le code des deux prochaines étapes, remplacez _`**my_app_package**`_ par le nom du package de l'application de votre projet, qui est la valeur de l'attribut `package` de la balise `manifest`.

2.  Ajoutez les nouvelles autorisations suivantes après l'élément existant `uses-permission` :

         <permission android:name="**my_app_package**.permission.C2D_MESSAGE" 
             android:protectionLevel="signature" />
         <uses-permission android:name="**my_app_package**.permission.C2D_MESSAGE" /> 
         <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
         <uses-permission android:name="android.permission.GET_ACCOUNTS" />
         <uses-permission android:name="android.permission.WAKE_LOCK" />

3.  Ajoutez le code suivant après la balise de début `application` :

         <receiver android:name="com.microsoft.windowsazure.notifications.NotificationsBroadcastReceiver"

4.  Téléchargez et décompressez le [Kit de développement logiciel (SDK) Mobile Services pour Android], ouvrez le dossier **notifications**, copiez le fichier **notifications-1.0.1.jar** dans le dossier *libs* de votre projet Eclipse, puis actualisez le dossier *libs*.

    **Remarque**

    Les numéros à la fin du nom du fichier peuvent changer dans les versions ultérieures du Kit de développement logiciel (SDK).

5.  Ouvrez le fichier *ToDoItemActivity.java*, puis ajoutez l'instruction import suivante :

        import com.microsoft.windowsazure.notifications.NotificationsManager;

6.  Ajoutez la variable privée suivante à la classe, où *`<PROJECT_NUMBER>`* est le numéro de projet attribué par Google à votre application dans la procédure précédente :

         public static final String SENDER_ID = "<PROJECT_NUMBER>";

7.  Dans la méthode **onCreate**, avant l'instanciation de MobileServiceClient, ajoutez ce code qui inscrit le gestionnaire de notification pour l'appareil :

         NotificationsManager.handleNotifications(this, SENDER_ID, MyHandler.class);

    Plus tard, nous définirons la classe MyHandler.class référencée dans ce code.

8.  Dans l'Explorateur de package, cliquez avec le bouton droit sur le package (sous le nœud `src`), cliquez sur **Nouveau**, puis sur **Classe**.

9.  Dans **Nom**, tapez `MyHandler`, dans **Superclass**, tapez `com.microsoft.windowsazure.notifications.NotificationsHandler`, puis cliquez sur **Terminer**

    ![](./media/mobile-services-android-get-started-push/mobile-services-android-create-class.png)

    La nouvelle classe MyHandler est créée.

10. Ajoutez l'instruction import suivante :

        import android.content.Context;

11. Ajoutez ensuite le code suivant à la classe :

        public static final int NOTIFICATION_ID = 1;
        private NotificationManager mNotificationManager;
        NotificationCompat.Builder builder;
        Context ctx;

12. Ajoutez le code suivant pour remplacer la méthode **onRegistered** qui permet d'inscrire votre appareil auprès du concentrateur de notification du service mobile.

        @Override
        public void onRegistered(Context context, String gcmRegistrationId) {
            super.onRegistered(context, gcmRegistrationId);
                
            ToDoActivity.mClient.getPush().register(gcmRegistrationId, null, new RegistrationCallback() {
                @Override
                public void onRegister(Registration registration, Exception exception) {
                      if (exception != null) {
                            // Traitement de l'erreur
                      }
                }
            });
        }

13. Ajoutez le code suivant pour remplacer la méthode **onReceive** qui entraîne l'affichage de la notification lors de sa réception.

        @Override
        public void onReceive(Context context, Bundle bundle) {
            ctx = context;
            String nhMessage = bundle.getString("message");

            sendNotification(nhMessage);
        }

        private void sendNotification(String msg) {
            mNotificationManager = (NotificationManager)
                      ctx.getSystemService(Context.NOTIFICATION_SERVICE);

            PendingIntent contentIntent = PendingIntent.getActivity(ctx, 0,
                  new Intent(ctx, ToDoActivity.class), 0);

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

L'application est mise à jour et prend en charge les notifications Push.

Mise à jour du script de la fonction insert inscrite dans le portail de gestion
-------------------------------------------------------------------------------

1.  Dans le portail de gestion, cliquez sur l'onglet **Données**, puis sur la table **TodoItems**.

	![](./media/mobile-services-android-get-started-push/mobile-portal-data-tables.png)

2.  Cliquez sur **todoitem** et sur l'onglet **Script**, puis sélectionnez **Insert**.

	![](./media/mobile-services-android-get-started-push/mobile-insert-script-push2.png) 

	La fonction appelée lors d'une insertion dans la table **TodoItem** s'affiche.

1.  Remplacez la fonction insert par le code suivant, puis cliquez sur **Enregistrer** :

         function insert(item, user, request) {
         // Définissez une charge utile pour la notification toast Google Cloud Messaging.
         var payload = 
             '{"data":{"message" : "Hello from Mobile Services!"}}';
            
         request.execute({
             success: function() {
                 // Si l'insertion aboutit, envoyez une notification.
                 push.gcm.send(null, payload, {
                     success: function(pushResponse) {
                         console.log("Sent push:", pushResponse, payload);
                         request.respond();
                         },              
                     error: function (pushResponse) {
                         console.log("Error Sending push:", pushResponse);
                         request.respond(500, { error: pushResponse });
                         }
                     });
                 },
             error: function(err) {
                 console.log("request.execute error", err)
                 request.respond();
             }
           });
         }

	Cela enregistre un nouveau script d'insertion qui utilise l'[objet gcm] pour envoyer une notification Push à tous les appareils enregistrés après l'insertion. 

Test des notifications Push dans votre application
--------------------------------------------------

Vous pouvez tester l'application en connectant directement un téléphone Android via un câble USB, ou en utilisant un appareil virtuel dans l'émulateur.

### Configuration de l'émulateur pour le test

Lorsque vous exécutez cette application dans l'émulateur, veillez à utiliser un AVD (appareil virtuel Android) qui prend en charge les API Google.

1.  Redémarrez Eclipse, puis dans l'Explorateur de package, cliquez avec le bouton droit sur le projet, cliquez sur **Propriétés** et sur **Android**, activez **API Google** et cliquez sur **OK**.

    ![](./media/mobile-services-android-get-started-push/mobile-services-import-android-properties.png)

Cela permet de cibler le projet pour les API Google.

1.  Dans **Fenêtre**, sélectionnez **Gestionnaire d'appareil virtuel Android**, choisissez votre appareil, puis cliquez sur **Modifier**.

    ![](./media/mobile-services-android-get-started-push/mobile-services-android-virtual-device-manager.png)

2.  Sélectionnez **API Google** dans **Cible**, puis cliquez sur OK.

	![](./media/mobile-services-android-get-started-push/mobile-services-android-virtual-device-manager-edit.png)

    Cela permet de cibler l'AVD pour utiliser les API Google.

### Exécution du test

1.  Dans le menu **Exécuter** d'Eclipse, cliquez sur **Exécuter** pour démarrer l'application.

2.  Dans l'application, tapez un texte explicite, comme *Nouvelle tâche Mobile Services* puis cliquez sur le bouton **Ajouter**.

	![](./media/mobile-services-android-get-started-push/mobile-quickstart-push1-android.png)

1.  Faites glisser votre doigt du haut vers le bas de l'écran pour ouvrir le centre de notifications de l'appareil et afficher la notification.

Vous avez terminé ce didacticiel.

Étapes suivantes
----------------

Ce didacticiel présente la fonctionnalité de notification Push de base fournie par Mobile Services. Si votre application requiert des fonctionnalités plus avancées, comme l'envoi de notifications interplateforme, le routage basé sur l'abonnement ou des volumes très importants, envisagez l'utilisation de Windows Azure Notification Hubs avec votre service mobile. Pour plus d'informations, consultez l'une des rubriques Notification Hubs suivantes :

-   [Prise en main de Notification Hubs](/en-us/manage/services/notification-hubs/getting-started-windows-dotnet/)
    En savoir plus sur l'utilisation de Notification Hubs dans votre application Android.

-   [Envoi de notifications aux abonnés](/en-us/manage/services/notification-hubs/breaking-news-dotnet/)
    En savoir plus sur l'inscription des utilisateurs et la réception des notifications Push pour les catégories qui les intéressent.

-   [Envoi de notifications aux utilisateurs](/en-us/manage/services/notification-hubs/notify-users/)
    En savoir plus sur l'envoi de notifications Push depuis un service mobile à certains utilisateurs sur n'importe quel appareil.

-   [Envoi de notifications interplateforme aux utilisateurs](/en-us/manage/services/notification-hubs/notify-users-xplat-mobile-services/)
    En savoir plus sur l'utilisation de modèles pour envoyer des notifications Push depuis un service mobile, sans avoir à gérer des charges utiles propres à la plateforme sur votre serveur principal.

Pour plus d'informations sur les rubriques Mobile Services suivantes :

-   [Prise en main des données](/en-us/develop/mobile/tutorials/get-started-with-data-js/)
    En savoir plus sur le stockage et l'interrogation des données à l'aide de Mobile Services.

-   [Prise en main de l'authentification](/en-us/develop/mobile/tutorials/get-started-with-users-js)
    En savoir plus sur l'authentification des utilisateurs de votre application avec un compte Windows.

-   [Référence de script serveur Mobile Services](http://go.microsoft.com/fwlink/?LinkId=262293)
    En savoir plus sur l'enregistrement et l'utilisation des scripts serveur.

-   [Guide de fonctionnement Mobile Services HTML/JavaScript](/en-us/develop/mobile/how-to-guides/work-with-html-js-client/) 
    En savoir plus sur l'utilisation de Mobile Services avec HTML et JavaScript


