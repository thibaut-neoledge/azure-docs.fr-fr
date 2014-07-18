<properties linkid="develop-mobile-tutorials-get-started-with-push-android" urlDisplayName="Get Started with Push (Android)" pageTitle="Get started with push notifications (Android) | Mobile Dev Center" metaKeywords="" description="Learn how to use Azure Mobile Services to send push notifications to your Android app." metaCanonical="" services="" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" authors="ricksal" solutions="" manager="dwrede" editor="" />

Prise en main des notifications Push dans Mobile Services
=========================================================

[Windows Store C\#](/en-us/develop/mobile/tutorials/get-started-with-push-dotnet "Windows Store C#")[Windows Store JavaScript](/en-us/develop/mobile/tutorials/get-started-with-push-js "Windows Store JavaScript")[Windows Phone](/en-us/develop/mobile/tutorials/get-started-with-push-wp8 "Windows Phone")[iOS](/en-us/develop/mobile/tutorials/get-started-with-push-ios "iOS")[Android](/en-us/develop/mobile/tutorials/get-started-with-push-android "Android")[Xamarin.iOS](/en-us/develop/mobile/tutorials/get-started-with-push-xamarin-ios "Xamarin.iOS")[Xamarin.Android](/en-us/develop/mobile/tutorials/get-started-with-push-xamarin-android "Xamarin.Android")

Cette rubrique montre comment utiliser Azure Mobile Services pour envoyer des notifications Push vers une application Android. Dans ce didacticiel, vous ajoutez des notifications Push à l'aide du service Google Cloud Messaging (GCM) au projet de démarrage rapide. Une fois que vous avez terminé, votre service mobile envoie une notification Push à chaque fois qu'un enregistrement est inséré.

> [WACOM.NOTE] Cette rubrique présente l'activation des notifications Push à l'aide de la prise en charge héritée par Mobile Services. Azure Notification Hubs s'intègre avec Mobile Services pour vous permettre d'envoyer des notifications Push basées sur un modèle et interplateforme à des millions d'appareils. Par défaut, les notifications Push avec Notification Hubs ne sont pas activées et actuellement, il n'existe pas de prise en charge de Notification Hub pour Android dans les bibliothèques Mobile Services. Toutefois, vous pouvez envoyer des notifications Push à partir de votre service mobile à l'aide des bibliothèques Notification Hub. Pour plus d'informations, consultez la page [Prise en main de Notification Hubs](/en-us/documentation/articles/notification-hubs-android-get-started/).

Ce didacticiel vous familiarise avec les étapes de base pour activer les notifications Push :

-   [Activation de Google Cloud Messaging](#register)
-   [Configuration de Mobile Services](#configure)
-   [Ajout de notifications Push à votre application](#add-push)
-   [Mise à jour des scripts pour l'envoi de notifications Push](#update-scripts)
-   [Insertion de données pour recevoir des notifications](#test)

Ce didacticiel requiert les éléments suivants :

-   [Kit de développement logiciel (SDK) Mobile Services pour Android](https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409)
-   Un compte Google actif

Ce didacticiel est basé sur le démarrage rapide de Mobile Services. Avant de commencer de didacticiel, vous devez effectuer la [Prise en main de Mobile Services](/en-us/develop/mobile/tutorials/get-started-android).

Activation de Google Cloud Messaging
------------------------------------

[WACOM.INCLUDE [Enable GCM](../includes/mobile-services-enable-Google-cloud-messaging.md)]

Ensuite, vous allez utiliser cette valeur de clé API afin d'activer Mobile Services pour l'authentification avec GCM et l'envoi des notifications Push au nom de votre application.

Configuration de Mobile Services pour l'envoi de requêtes Push
--------------------------------------------------------------

1.  Connectez-vous au [portail de gestion Azure](https://manage.windowsazure.com/), cliquez sur **Mobile Services**, puis sur l’application.

	![][18]

2.  Cliquez sur l'onglet **Push**, entrez la valeur **Clé API** fournie par GCM dans la procédure précédente, puis cliquez sur **Enregistrer**.

	![][19]

Votre service mobile est désormais configuré pour utiliser GCM pour l'envoi de notifications Push.

Ajout de notifications Push à votre application
-----------------------------------------------

### Ajout de services Google Play au projet

[WACOM.INCLUDE [Add Play Services](../includes/mobile-services-add-Google-play-services.md)]

### Ajout de code

1.  Ouvrez le fichier projet **AndroidManifest.xml**. Google Cloud Messaging a des spécifications requises d'API minimales pour le développement et les tests, auxquelles la propriété **minSdkVersion** du manifeste doit se conformer. Consultez la page [Configuration du Kit de développement logiciel (SDK) des services Google Play](http://go.microsoft.com/fwlink/?LinkId=389801) pour déterminer comment définir cette valeur si vous devez la définir en dessous de 16 parce que vous utilisez un appareil plus ancien. Définissez la propriété comme il convient.

2.  Assurez-vous que dans l'élément `uses-sdk`, **targetSdkVersion** est défini sur le numéro d'une plateforme de Kit de développement logiciel (SDK) qui a été installée (étape 1). Il est préférable de le définir sur la dernière version disponible.

3.  La balise **uses-sdk** peut ressembler à ceci, en fonction de vos choix aux étapes précédentes :

         <uses data-morhtml="true"-sdk
             android:minSdkVersion="17"
             android:targetSdkVersion="19" />

4.  Dans le code des deux prochaines étapes, remplacez _`**my_app_package**`_ par le nom du package de l'application de votre projet, qui est la valeur de l'attribut `package` de la balise `manifest`.

5.  Ajoutez les nouvelles autorisations suivantes après l'élément existant `uses-permission` :

         <permission android:name="**my_app_package**.permission.C2D_MESSAGE" 
             android:protectionLevel="signature" />
         <uses data-morhtml="true"-permission android:name="**my_app_package**.permission.C2D_MESSAGE" /> 
         <uses data-morhtml="true"-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
         <uses data-morhtml="true"-permission android:name="android.permission.GET_ACCOUNTS" />
         <uses data-morhtml="true"-permission android:name="android.permission.WAKE_LOCK" />

6.  Ajoutez le code suivant après la balise de début `application` :

         <receiver android:name="com.microsoft.windowsazure.notifications.NotificationsBroadcastReceiver"
             android:permission="com.google.android.c2dm.permission.SEND">
             <intent data-morhtml="true"-filter>
                 <action android:name="com.google.android.c2dm.intent.RECEIVE" />
                 <category android:name="**my_app_package**" />
             </intent-filter>
         </receiver>

7.  Ouvrez le fichier ToDoItem.java, puis ajoutez le code suivant à la classe **TodoItem** :

             @com.google.gson.annotations.SerializedName("handle")
             private String mHandle;
            
             public String getHandle() {
                 return mHandle;
             }
            
             public final void setHandle(String handle) {
                 mHandle = handle;
             }

    Ce code crée une propriété qui contient l'ID d'inscription.

    **Remarque**

    Lorsque le schéma dynamique est activé sur votre service mobile, une nouvelle colonne **handle** est automatiquement ajoutée à la table **TodoItem** lorsqu'un nouvel élément contenant cette propriété est inséré.

8.  Téléchargez et décompressez le [Kit de développement logiciel (SDK) Mobile Services pour Android](https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409), ouvrez le dossier **notifications**, copiez le fichier **notifications-1.0.1.jar** dans le dossier *libs* de votre projet Eclipse, puis actualisez le dossier *libs*.

    **Remarque**

    Les numéros à la fin du nom du fichier peuvent changer dans les versions ultérieures du Kit de développement logiciel (SDK).

9.  Ouvrez le fichier *ToDoItemActivity.java*, puis ajoutez l'instruction import suivante :

        import com.microsoft.windowsazure.notifications.NotificationsManager;

10. Ajoutez la variable privée suivante à la classe, où _`<PROJECT_NUMBER>`_ est le numéro de projet attribué par Google à votre application dans la procédure précédente :

        public static final String SENDER_ID = "<PROJECT data-morhtml="true"_NUMBER>";

11. Dans la méthode **onCreate**, avant l'instanciation de MobileServiceClient, ajoutez ce code qui inscrit le gestionnaire de notification pour l'appareil :

        NotificationsManager.handleNotifications(this, SENDER_ID, MyHandler.class);

12. Ajoutez la ligne de code suivante à la méthode **addItem** avant que l'élément soit inséré dans la table :

        item.setHandle(MyHandler.getHandle());

    Ce code définit la propriété `handle` de l'élément sur l'ID d'inscription de l'appareil.

13. Dans l'Explorateur de package, cliquez avec le bouton droit sur le package (sous le nœud `src`), cliquez sur **Nouveau**, puis sur **Classe**.

14. Dans **Nom**, tapez `MyHandler`, dans **Superclasse**, tapez `com.microsoft.windowsazure.notifications.NotificationsHandler`, puis cliquez sur **Terminer**

    ![](./media/mobile-services-android-get-started-push/mobile-services-android-create-class.png)

    Cela a permis de créer la classe MyHandler.

15. Ajoutez les instructions import suivantes :

        import android.content.Context;
            
        import com.microsoft.windowsazure.notifications.NotificationsHandler;

16. Ajoutez le code suivant :

        @com.google.gson.annotations.SerializedName("handle")
        private static String mHandle;

        public static String getHandle() {
            return mHandle;
        }

        public static final void setHandle(String handle) {
            mHandle = handle;
        }

17. Remplacez la méthode à substituer **onRegistered** existante par le code suivant :

        @Override
        public void onRegistered(Context context, String gcmRegistrationId) {
            super.onRegistered(context, gcmRegistrationId);
                
            setHandle(gcmRegistrationId);
        }

L'application est mise à jour et prend en charge les notifications Push.

Mise à jour du script de la fonction insert inscrite dans le portail de gestion
-------------------------------------------------------------------------------

1.  Dans le portail de gestion, cliquez sur l'onglet **Données**, puis sur la table **TodoItem**.

	![][21]

2.  Dans **todoitem**, cliquez sur l'onglet **Script** et sélectionnez **Insérer**.

	![](./media/mobile-services-android-get-started-push/mobile-insert-script-push2.png) 

	La fonction appelée lors d'une insertion dans la table **TodoItem** s'affiche.

1.  Remplacez la fonction insert par le code suivant, puis cliquez sur **Enregistrer** :

         function insert(item, user, request) {
             request.execute({
                 success: function() {
                     // Ecrire dans la réponse puis envoyer la notification en arrière-plan
                     request.respond();
                     push.gcm.send(item.handle, item.text, {
                         success: function(response) {
                             console.log('Push notification sent: ', response);
                         }, error: function(error) {
                             console.log('Error sending push notification: ', error);
                         }
                     });
                 }
             });
         }

	Cela enregistre un nouveau script d'insertion, qui utilise l'[gcm object] pour envoyer une notification Push (le texte inséré) à l'appareil fourni dans la requête d'insertion. 

Test des notifications Push dans votre application
--------------------------------------------------

**Remarque**

Lorsque vous exécutez cette application dans l'émulateur, veillez à utiliser un AVD (appareil virtuel Android) qui prend en charge les API Google.

1.  Redémarrez Eclipse, puis dans l'Explorateur de package, cliquez avec le bouton droit sur le projet, cliquez sur **Propriétés**, puis sur **Android**, activez **API Google**, puis cliquez sur **OK**.

    ![](./media/mobile-services-android-get-started-push/mobile-services-import-android-properties.png)

	Cela permet de cibler le projet pour les API Google.

2.  Dans **Fenêtre**, sélectionnez **Gestionnaire d'appareil virtuel Android**, choisissez votre appareil, puis cliquez sur **Modifier**.

    ![](./media/mobile-services-android-get-started-push/mobile-services-android-virtual-device-manager.png)

3.  Sélectionnez **API Google** dans **Cible**, puis cliquez sur OK.

	![][25]

    Cela permet de cibler l'AVD pour utiliser les API Google.

4.  Dans le menu **Exécuter**, cliquez sur **Exécuter** pour démarrer l'application.

5.  Dans l'application, tapez un texte explicite, comme *Nouvelle tâche Mobile Services*, puis cliquez sur le bouton **Ajouter**.

	![](./media/mobile-services-android-get-started-push/mobile-quickstart-push1-android.png)

6.  Une boîte de notification noire apparaît brièvement dans la partie inférieure de l'écran.

	![](./media/mobile-services-android-get-started-push/mobile-push-icon.png)

Vous avez terminé ce didacticiel.

Étapes suivantes
----------------

Dans cet exemple simple, un utilisateur reçoit une notification Push avec les données venant d'être insérées. Le jeton de l'appareil utilisé par GCM est fourni au service mobile par le client dans la requête. Dans le didacticiel suivant, [Notifications Push pour les utilisateurs d'application](/en-us/develop/mobile/tutorials/push-notifications-to-users-android), vous allez créer une table Devices distincte dans laquelle stocker les jetons d'appareil et envoyer une notification Push à tous les canaux stockés en cas d'insertion.

<!-- Images. -->

[1]: ./media/mobile-services-android-get-started-push/mobile-services-google-developers.png
[2]: ./media/mobile-services-android-get-started-push/mobile-services-google-create-server.png
[3]: ./media/mobile-services-android-get-started-push/mobile-services-google-create-server2.png
[4]: ./media/mobile-services-android-get-started-push/mobile-services-google-create-server3.png
[5]: ./media/mobile-services-android-get-started-push/mobile-services-android-sdk-manager.png
[6]: ./media/mobile-services-android-get-started-push/mobile-services-android-create-class.png
[7]: ./media/mobile-services-android-get-started-push/mobile-google-cloud-new-project.png
[8]: ./media/mobile-services-android-get-started-push/mobile-google-cloud-sms-verify.png
[9]: ./media/mobile-services-android-get-started-push/mobile-google-cloud-project-created.png
[10]: ./media/mobile-services-android-get-started-push/mobile-google-choose-play-api.png
[18]: ./media/mobile-services-android-get-started-push/mobile-services-selection.png
[19]: ./media/mobile-services-android-get-started-push/mobile-push-tab-android.png
[21]: ./media/mobile-services-android-get-started-push/mobile-portal-data-tables.png
[22]: ./media/mobile-services-android-get-started-push/mobile-insert-script-push2.png
[23]: ./media/mobile-services-android-get-started-push/mobile-services-import-android-properties.png
[24]: ./media/mobile-services-android-get-started-push/mobile-services-android-virtual-device-manager.png
[25]: ./media/mobile-services-android-get-started-push/mobile-services-android-virtual-device-manager-edit.png
[26]: ./media/mobile-services-android-get-started-push/mobile-quickstart-push1-android.png
[27]: ./media/mobile-services-android-get-started-push/mobile-quickstart-push2-android.png
[28]: ./media/mobile-services-android-get-started-push/mobile-push-icon.png
[29]: ./media/mobile-services-android-get-started-push/mobile-eclipse-import-Play-library.png

<!-- URLs. -->
[Google Cloud Console]: https://cloud.google.com/console
[Google apis]: http://go.microsoft.com/fwlink/p/?LinkId=268303
[Android Provisioning Portal]: http://go.microsoft.com/fwlink/p/?LinkId=272456
[Set Up Google Play Services SDK]: http://go.microsoft.com/fwlink/?LinkId=389801
[Referencing a library project]: http://go.microsoft.com/fwlink/?LinkId=389800
[Mobile Services Android SDK]: https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409
[Get started with Mobile Services]: /en-us/develop/mobile/tutorials/get-started-android
[Get started with data]: /en-us/develop/mobile/tutorials/get-started-with-data-android
[Get started with authentication]: /en-us/develop/mobile/tutorials/get-started-with-users-android
[Get started with push notifications]: /en-us/develop/mobile/tutorials/get-started-with-push-android
[Push notifications to app users]: /en-us/develop/mobile/tutorials/push-notifications-to-users-android
[Authorize users with scripts]: /en-us/develop/mobile/tutorials/authorize-users-android

[Azure Management Portal]: https://manage.windowsazure.com/
[Mobile Services server script reference]: http://go.microsoft.com/fwlink/?LinkId=262293
[Mobile Services android conceptual]: /en-us/develop/mobile/how-to-guides/work-with-android-client-library/
[gcm object]: http://go.microsoft.com/fwlink/p/?LinkId=282645