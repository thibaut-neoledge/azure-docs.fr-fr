<properties linkid="develop-mobile-tutorials-get-started-with-push-xamarin-android" urlDisplayName="Get Started with Push Notifications" pageTitle="Get started with push notifications - Mobile Services" metaKeywords="" description="Learn how to use push notifications in Xamarin.Android apps with Azure Mobile Services." metaCanonical="" disqusComments="0" umbracoNaviHide="1" title="Get started with push notifications in Mobile Services" documentationCenter="Mobile" authors="" />

Prise en main des notifications Push dans Mobile Services
=========================================================

[Windows Store C\#](/en-us/develop/mobile/tutorials/get-started-with-push-dotnet "Windows Store C#")[Windows Store JavaScript](/en-us/develop/mobile/tutorials/get-started-with-push-js "Windows Store JavaScript")[Windows Phone](/en-us/develop/mobile/tutorials/get-started-with-push-wp8 "Windows Phone")[iOS](/en-us/develop/mobile/tutorials/get-started-with-push-ios "iOS")[Android](/en-us/develop/mobile/tutorials/get-started-with-push-android "Android")[Xamarin.iOS](/en-us/develop/mobile/tutorials/get-started-with-push-xamarin-ios "Xamarin.iOS")[Xamarin.Android](/en-us/develop/mobile/tutorials/get-started-with-push-xamarin-android "Xamarin.Android")

Cette rubrique montre comment utiliser Azure Mobile Services pour envoyer des notifications Push à une application Xamarin.Android. Dans ce didacticiel, vous ajoutez des notifications Push à l'aide du service Google Cloud Messaging (GCM) au projet de démarrage rapide. Une fois la procédure terminée, votre service mobile envoie une notification Push chaque fois qu'un enregistrement est inséré.

Ce didacticiel vous familiarise avec les étapes de base permettant d'activer les notifications Push :

1.  [Inscription de votre application pour les notifications Push](#register)
2.  [Configuration de Mobile Services](#configure)
3.  [Ajout de notifications Push à l'application](#add-push)
4.  [Mise à jour des scripts pour l'envoi de notifications Push](#update-scripts)
5.  [Insertion de données pour recevoir des notifications](#test)

Ce didacticiel requiert les éléments suivants :

-   Un compte Google actif

Ce didacticiel est basé sur le démarrage rapide de Mobile Services. Avant de commencer ce didacticiel, vous devez effectuer le didacticiel [Prise en main de Mobile Services](/en-us/develop/mobile/tutorials/get-started-xamarin-android).

Inscription de votre applicationInscription de votre application pour les notifications Push
--------------------------------------------------------------------------------------------

**Remarque**

Pour effectuer la procédure décrite dans cette rubrique, vous devez disposer d'un compte Google avec une adresse électronique vérifiée. Pour créer un compte Google, consultez la page [accounts.google.com](http://go.microsoft.com/fwlink/p/?LinkId=268302).

1.  Rendez-vous sur le site Web [API Google](http://go.microsoft.com/fwlink/p/?LinkId=268303), connectez-vous avec votre compte Google, puis cliquez sur **Create project...**.

    ![](./media/partner-xamarin-mobile-services-android-get-started-push/mobile-services-google-developers.png)

    > [WACOM.NOTE] Si vous disposez déjà d'un projet en cours, vous êtes redirigé vers la page **Dashboard** une fois la connexion établie. Pour créer un projet depuis le tableau de bord, développez **API Project<**, cliquez sur **Create...** sous **Other projects**, puis entrez un nom de projet et cliquez sur **Create project**.

2.  Cliquez sur le bouton Overview dans la colonne de gauche et notez le numéro de projet dans la section Dashboard.

    Plus loin dans le didacticiel, vous allez définir cette valeur en tant que variable **PROJECT\_ID** dans le client.

3.  Sur la page [Google apis](http://go.microsoft.com/fwlink/p/?LinkId=268303), cliquez sur **Services**, puis sur la touche bascule pour activer **Google Cloud Messaging for Android** et accepter les conditions de service.

4.  Cliquez sur **API Access**, puis sur **Create new Server key...**.

    ![](./media/partner-xamarin-mobile-services-android-get-started-push/mobile-services-google-create-server.png)

5.  Sous **Configure Server Key for API Project**, cliquez sur **Create**.

    ![](./media/partner-xamarin-mobile-services-android-get-started-push/mobile-services-google-create-server2.png)

6.  Prenez note de la valeur de **API key**.

    ![](./media/partner-xamarin-mobile-services-android-get-started-push/mobile-services-google-create-server3.png)

Ensuite, vous allez utiliser cette valeur de clé API afin d'activer Mobile Services pour l'authentification avec GCM et l'envoi des notifications Push au nom de votre application.

<h2>Configuration du service Configuration de Mobile Services pour l'envoi de requêtes Push</h2>

1.  Connectez-vous au [portail de gestion Azure](https://manage.windowsazure.com/), cliquez sur **Mobile Services**, puis sur l'application.

    ![](./media/partner-xamarin-mobile-services-android-get-started-push/mobile-services-selection.png)

2.  Cliquez sur l'onglet **Push**, entrez la valeur de **API Key** fournie par GCM dans la procédure précédente, puis cliquez sur **Save**.

    ![](./media/partner-xamarin-mobile-services-android-get-started-push/mobile-push-tab-android.png)

    Votre service mobile est désormais configuré pour utiliser GCM pour l'envoi de notifications Push.

<h2>Ajout de notifications Push Ajout de notifications Push à l'application</h2>

1.  Nous allons d'abord ajouter **PushSharp** en tant que référence dans notre projet. Pour ce faire, nous devons compiler la dernière version de PushSharp et ajouter la DLL compilée comme référence pointant vers notre projet Xamarin.Android.

2.  Accédez à la page [PushSharp Github](https://github.com/Redth/PushSharp) et téléchargez la dernière version. Une fois que vous avez extrait l'ensemble de fichiers, accédez à l'exemple de dossier de projet suivant :

    **/Client.Samples/PushSharp.ClientSample.MonoForAndroid/PushSharp.ClientSample.MonoForAndroid.Gcm/**

    .. et ouvrez le fichier projet :

    ** PushSharp.ClientSample.MonoForAndroid.Gcm.csproj **

3.  Générez l'exemple de client MonoForAndroid PushSharp en mode **Release**.

4.  Créez un dossier **\_external** dans votre dossier de projet Xamarin.Android.

5.  Copiez le fichier suivant depuis l'exemple de client MonoForAndroid PushSharp dans le dossier **\_external** nouvellement créé de votre dossier de projet Xamarin.Android :

    **\\bin\\Release\\PushSharp.Client.MonoForAndroid.dll**

6.  Ouvrez votre projet Xamarin.Android dans Xamarin Studio (ou Visual Studio).

7.  Cliquez avec le bouton droit sur le dossier **References** du projet et choisissez **Edit References...**

8.  Ouvrez l'onglet **.Net Assembly**, accédez au dossier **\_external** de votre projet, sélectionnez le fichier **PushSharp.Client.MonoForAndroid.dll** que nous avons généré précédemment et cliquez sur **Add**. Cliquez sur OK pour fermer la boîte de dialogue.

9.  Ouvrez **Constants.cs** et ajoutez la ligne suivante, en remplaçant **PROJECT\_ID** par l'ID de projet Google que vous avez noté précédemment :

         public const string SenderID = "PROJECT_ID"; // Numéro de projet de l'API Google

10. Copiez le fichier **PushService.cs** depuis l'exemple de client MonoForAndroid PushSharp dans votre dossier de projet Xamarin.Android et ajoutez-le à votre projet.

11. Remplacez l'espace de noms utilisé dans **PushService.cs** par celui de votre projet (exemple : XamarinTodoQuickStart).

12. Éditez le tableau **SENDER\_IDS** dans **PushService.cs** de manière à référencer la constante **SenderID** que nous avons créée ci-dessus :

        public static string[] SENDER_IDS = new string[] { Constants.SenderID };

13. Ajoutez une nouvelle propriété statique au **PushHandlerService** dans **PushService.cs** pour effectuer le suivi de l'ID d'inscription de notre appareil :

        public static string RegistrationID { get; private set; }

14. Mettez à jour la méthode **OnRegistered** dans **PushService.cs** pour stocker l'ID d'inscription reçu dans notre variable statique locale :

        protected override void OnRegistered(Context context, string registrationId)
        {
            Log.Verbose(PushHandlerBroadcastReceiver.TAG, "GCM Registered: " + registrationId);
            RegistrationID = registrationId;
        }

15. Mettez à jour la méthode **OnMessage** dans **PushService.cs** pour afficher le message Push reçu en tant qu'élément de la notification (remplacer l'appel **createNotification** existant) :

        string message = intent.Extras.GetString("message");
        createNotification("New todo item!", "Todo item: " + message);

16. Notez que la méthode **OnMessage** est dotée du code suivant par défaut pour stocker le dernier message Push reçu :

        //Stockez le message
        var prefs = GetSharedPreferences(context.PackageName, FileCreationMode.Private);
        var edit = prefs.Edit();
        edit.PutString("last_msg", msg.ToString());
        edit.Commit();

17. Mettez à jour la méthode **createNotification** dans **PushService.cs** pour référencer **TodoActivity** au lieu de **DefaultActivity**.

18. Ouvrez **TodoActivity.cs** et ajoutez l'instruction using suivante :

        using PushSharp.Client;

19. Dans **TodoActivity.cs**, insérez les lignes suivantes juste au-dessus de l'endroit où a été créé **MobileServiceClient** :

        // Vérifiez que la configuration a été correctement effectuée dans son intégralité
        PushClient.CheckDevice(this);
        PushClient.CheckManifest(this);

        // Inscrivez-vous pour recevoir des notifications Push
        System.Diagnostics.Debug.WriteLine("Registering...");
        PushClient.Register(this, PushHandlerBroadcastReceiver.SENDER_IDS);

20. Ouvrez **TodoItem.cs** et ajoutez un nouveau champ pour effectuer le suivi de l'ID d'appareil inscrit pour la personne qui a ajouté l'élément TodoItem :

        [DataMember(Name = "channel")]
        public string RegistrationId { get; set; }

21. Dans **TodoActivity.cs**, mettez à jour la méthode **AddItem** en définissant le **RegistrationID** du nouvel élément **TodoItem** ajouté sur l'ID d'inscription de l'appareil reçu lors du processus d'inscription :

        // Créez un nouvel élément
        var item = new TodoItem() {
            Text = textNewTodo.Text,
            Complete = false,
            RegistrationId = PushHandlerService.RegistrationID
        };

L'application est mise à jour et prend en charge les notifications Push.

Mise à jour du script d'insertionMise à jour du script d'insertion inscrit dans le portail de gestion
-----------------------------------------------------------------------------------------------------

1.  Dans le portail de gestion, cliquez sur l'onglet **Données**, puis sur la table **TodoItem**.

    ![](./media/partner-xamarin-mobile-services-android-get-started-push/mobile-portal-data-tables.png)

2.  Dans **TodoItem**, cliquez sur l'onglet **Script** et sélectionnez **Insérer**.

	![](./media/partner-xamarin-mobile-services-android-get-started-push/mobile-insert-script-push2.png)

    La fonction appelée lors d'une insertion dans la table **TodoItem** s'affiche.

1.  Remplacez la fonction insert par le code suivant, puis cliquez sur **Enregistrer** :

         function insert(item, user, request) {
             request.execute({
                 success: function() {
                     // Complétez la réponse, puis envoyez la notification en arrière-plan
                     request.respond();
                     push.gcm.send(item.channel, item.text, {
                         success: function(response) {
                             console.log('Push notification sent: ', response);
                         }, error: function(error){
                             console.log('Error sending push notification: ', error);
                         }
                     });
                 }
             });
         }

    Ceci inscrit un nouveau script d'insertion, qui utilise l'[objet gcm](http://go.microsoft.com/fwlink/p/?LinkId=282645) pour envoyer une notification Push (le texte inséré) à l'appareil fourni dans la demande d'insertion.

Test de l'applicationTest des notifications Push dans l'application
-------------------------------------------------------------------

1.  Exécutez l'application et ajoutez un nouvel élément Todo. Vérifiez que vous recevez une notification Push concernant le nouvel élément Todo qui est ajouté.

2.  Consultez l'onglet **Logs** de votre application mobile dans le portail de gestion Azure pour prendre connaissance des messages de journalisation que nous avons ajoutés à la méthode **Insert** dans la table **TodoItem** ci-dessus.

3.  Consultez la table **TodoItem** dans le portail de gestion Azure pour prendre connaissance des ID d'inscription d'appareil uniques dans la nouvelle colonne **channel** qui a été ajoutée.

Vous avez terminé ce didacticiel.

Téléchargement de l'exemple terminé
-----------------------------------

Téléchargez le [projet d'exemple terminé](http://go.microsoft.com/fwlink/p/?LinkId=331303). Veillez à mettre à jour les variables **ApplicationURL**, **ApplicationKey** et **SenderID** en fonction de vos propres paramètres Azure.

Étapes suivantes
----------------

Dans cet exemple simple, un utilisateur reçoit une notification Push avec les données venant d'être insérées. Dans le didacticiel suivant, [Notifications Push pour les utilisateurs d'application](/en-us/develop/mobile/tutorials/push-notifications-to-users-android), vous allez créer une table Devices distincte pour y stocker les jetons d'appareil et envoyer une notification Push à tous les canaux stockés en cas d'insertion.

