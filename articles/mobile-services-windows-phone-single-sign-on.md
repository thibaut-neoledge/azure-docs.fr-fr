<properties linkid="develop-mobile-tutorials-single-sign-on-windows-8-wp8" urlDisplayName="Authenticate with single sign-on" pageTitle="Authenticate your app with Live Connect (Windows Phone) | Mobile Dev Center" metaKeywords="" description="Learn how to use Live Connect single sign-on in Azure Mobile Services from a Windows Phone application." metaCanonical="" services="" documentationCenter="Mobile" title="Authenticate your Windows Phone 8 app with Live Connect single sign-on" authors="glenga" solutions="" manager="" editor="" />

Authentification unique de votre application Windows Phone 8 avec Live Connect
==============================================================================

[Windows Store C\#](/en-us/develop/mobile/tutorials/single-sign-on-windows-8-dotnet "Windows Store C#")[Windows Store JavaScript](/en-us/develop/mobile/tutorials/single-sign-on-windows-8-js "Windows Store JavaScript")[Windows Phone](/en-us/develop/mobile/tutorials/single-sign-on-wp8/ "Windows Phone")

Cette rubrique vous montre comment utiliser la solution d'authentification unique Live Connect pour authentifier les utilisateurs dans Azure Mobile Services depuis une application Windows Phone 8. Dans ce didacticiel, vous allez ajouter le processus d'authentification au projet de démarrage rapide à l'aide de Live Connect. Après avoir été authentifié par Live Connect, l'utilisateur connecté est accueilli par son nom et son ID est affiché.

**Remarque**

Ce didacticiel montre les avantages de l'authentification unique Live Connect pour les applications Windows Phone. Cette fonction vous permet d'authentifier plus facilement un utilisateur déjà connecté avec votre service mobile. Pour une expérience d'authentification plus globale permettant la prise en charge de plusieurs fournisseurs de services d'authentification, consultez la rubrique [Prise en main de l'authentification](/en-us/develop/mobile/tutorials/get-started-with-users-wp8/).

Ce didacticiel vous familiarise avec les étapes de base pour activer l'authentification Live Connect :

1.  [Inscription de votre application pour l'authentification et configuration de Mobile Services](#register)
2.  [Restriction des autorisations de table pour les utilisateurs authentifiés](#permissions)
3.  [Ajout de l'authentification à l'application](#add-authentication)

Ce didacticiel requiert les éléments suivants :

-   [Kit de développement logiciel (SDK) Live pour Windows et Windows Phone](http://go.microsoft.com/fwlink/p/?LinkId=262253)
-   Microsoft Visual Studio 2012 Express pour Windows Phone

Ce didacticiel est basé sur le démarrage rapide de Mobile Services. Vous devez aussi d'abord suivre le didacticiel [Prise en main de Mobile Services](/en-us/develop/mobile/tutorials/get-started-wp8).

Inscription de votre applicationInscription de votre application avec Live Connect
----------------------------------------------------------------------------------

Pour pouvoir authentifier des utilisateurs, vous devez inscrire votre application dans le Centre des développeurs Live Connect. Vous devez ensuite inscrire la clé secrète client pour intégrer Live Connect à Mobile Services.

1.  Connectez-vous au [portail de gestion Azure](https://manage.windowsazure.com/), cliquez sur **Mobile Services**, puis sur le service mobile.

	![][4]

2.  Cliquez sur l'onglet **Tableau de bord**, puis notez la valeur **URL du site**.

	![][5]

    Cette valeur vous permet de définir le domaine de redirection.

3.  Accédez à la page [Mes applications](http://go.microsoft.com/fwlink/p/?LinkId=262039) dans le Centre des développeurs Live Connect, puis connectez-vous avec votre compte Microsoft si nécessaire.

4.  Cliquez sur **Créer une application**, puis tapez le **Nom de l'application** et cliquez sur **J'accepte**.

	![][1] 

	Cette action inscrit l'application auprès de Live Connect.

5.  Cliquez sur la **page Paramètres de l'application**, puis sur **Paramètres de l'API** et notez les valeurs de **ID client** et **Clé secrète client**.

	![][2]

    **Remarque relative à la sécurité**

    La clé secrète client est une information d'identification de sécurité importante. Ne la partagez pas avec quiconque et ne la distribuez pas avec votre application.

6.  Dans **Domaine de redirection**, entrez l'URL de votre service mobile à l'étape 2, cliquez sur **Oui** sous **Application cliente mobile**, puis cliquez sur **Enregistrer**.

7.  De retour dans le portail de gestion, cliquez sur l'onglet **Identité**, entrez la **clé secrète client** récupérée dans Live Connect, puis cliquez sur **Enregistrer**.

	![][13]

Votre service mobile et votre application sont désormais configurés pour utiliser Live Connect.

Restrictions des autorisationsRestrictions des autorisations pour les utilisateurs authentifiés
-----------------------------------------------------------------------------------------------

1.  Dans le portail de gestion, cliquez sur l'onglet **Données**, puis sur la table **TodoItem**.

	![][14]

2.  Cliquez sur l'onglet **Autorisations**, définissez toutes les autorisations sur **Only authenticated users**, puis cliquez sur **Enregistrer**. Cela permet de s'assurer que toutes les opérations effectuées sur la table **TodoItem** requièrent un utilisateur authentifié. Cela simplifie aussi les scripts dans le didacticiel suivant, car il ne sera pas nécessaire d'autoriser la possibilité d'utilisateurs anonymes.

	![][15]

3.  Dans Visual Studio 2012 Express pour Windows Phone, ouvrez le projet que vous avez créé avec le didacticiel [Prise en main de Mobile Services](/en-us/develop/mobile/tutorials/get-started-wp8).

4.  Appuyez sur la touche F5 pour exécuter cette application basée sur le démarrage rapide ; vérifiez qu'une exception associée au code d'état 401 (Non autorisé) est générée.

	Cela se produit, car l'application accède à Mobile Services en tant qu'utilisateur non authentifié, mais la table _TodoItem_ requiert désormais une authentification.

Ensuite, vous allez mettre à jour l'application pour authentifier les utilisateurs avec Live Connect avant de demander des ressources à partir du service mobile.

Ajout de l'authentificationAjout de l'authentification à l'application
----------------------------------------------------------------------

1.  Téléchargez et installez le [Kit de développement logiciel (SDK) Live pour Windows et Windows Phone](http://go.microsoft.com/fwlink/p/?LinkId=262253).

2.  Dans le menu **Projet** de Visual Studio, cliquez sur **Ajouter une référence**, puis développez **Assemblys**, cliquez sur **Extensions**, cochez **Microsoft.Live**, puis cliquez sur **OK**.

	![][16]

	Cette action permet d'ajouter une référence au Kit de développement logiciel (SDK) Live dans le projet.

3.  Ouvrez le fichier projet mainpage.xaml.cs et ajoutez les instructions using suivantes :

         using Microsoft.Live;      

4.  Ajoutez l'extrait de code suivant à la classe MainPage :

         private LiveConnectSession session;
         private async System.Threading.Tasks.Task Authenticate()
         {
             LiveAuthClient liveIdClient = new LiveAuthClient("<< INSERT CLIENT ID HERE >>");

             while (session == null)
             {
                 LiveLoginResult result = await liveIdClient.LoginAsync(new[] { "wl.basic" });
                 if (result.Status == LiveConnectSessionStatus.Connected)
                 {
                     session = result.Session;
                     LiveConnectClient client = new LiveConnectClient(result.Session);
                     LiveOperationResult meResult = await client.GetAsync("me");
                     MobileServiceUser loginResult = await App.MobileService
                         .LoginWithMicrosoftAccountAsync(result.Session.AuthenticationToken);

                     string title = string.Format("Welcome {0}!", meResult.Result["first_name"]);
                     var message = string.Format("You are now logged in - {0}", loginResult.UserId);
                     MessageBox.Show(message, title, MessageBoxButton.OK);                    
                 }
                 else
                 {
                     session = null;
                     MessageBox.Show("You must log in.", "Login Required", MessageBoxButton.OK);                    
                 }
             }
          }

	Cela crée une variable membre pour le stockage de la session Live Connect actuelle et une méthode pour gérer le processus d'authentification.

5.  Mettez à jour la chaîne *<< INSERT CLIENT ID HERE >>* de l'étape précédente avec la valeur d'ID client générée lorsque vous avez inscrit votre application auprès de Live Connect.

    **Remarque**

    Dans une application Windows Phone 8, une instance de la classe **LiveAuthClient** est créée en transmettant la valeur d'ID cliente au constructeur de classe. Dans une [application Windows Store](/en-us/develop/mobile/tutorials/single-sign-on-windows-8-dotnet/), la même classe est instanciée en transmettant l'URI du domaine de redirection.

6.  Supprimez le remplacement de méthode **OnNavigatedTo** existant (ou convertissez-le en commentaires) et remplacez-le par la méthode suivante qui gère l'événement **Loaded** pour la page.

         async void MainPage_Loaded(object sender, RoutedEventArgs e)
         {
             await Authenticate();
             RefreshTodoItems();
         }

	Cette méthode appelle la nouvelle méthode **Authenticate**. 

7.  Remplacez le constructeur MainPage par le code suivant :

         // Constructeur
         public MainPage()
         {
             InitializeComponent();
             this.Loaded += MainPage_Loaded;
         }

	Ce constructeur enregistre également le gestionnaire pour l'événement Loaded.

8.  Appuyez sur la touche F5 pour exécuter l'application et vous connecter à Live Connect avec votre compte Microsoft.

	Lorsque vous êtes connecté, l'application s'exécute sans erreur, et vous pouvez exécuter des requêtes Mobile Services et mettre à jour les données.

Étapes suivantes
----------------

Dans le didacticiel suivant, [Autorisation des utilisateurs avec des scripts](/en-us/develop/mobile/tutorials/authorize-users-in-scripts-wp8), vous allez utiliser la valeur de l'ID utilisateur fournie par Mobile Services en fonction de l'utilisateur authentifié et l'utiliser pour filtrer les données renvoyées par Mobile Services. Pour plus d'informations sur l'utilisation d'autres fournisseurs d'identité à des fins d'authentification, consultez la page [Prise en main de l'authentification](/en-us/develop/mobile/tutorials/get-started-with-users-wp8).

<!-- Anchors. -->
[Register your app for authentication and configure Mobile Services]: #register
[Restrict table permissions to authenticated users]: #permissions
[Add authentication to the app]: #add-authentication
[Next Steps]:#next-steps

<!-- Images. -->
[1]: ./media/mobile-services-windows-phone-single-sign-on/mobile-services-live-connect-add-app.png
[2]: ./media/mobile-services-windows-phone-single-sign-on/mobile-live-connect-app-api-settings-mobile.png
[4]: ./media/mobile-services-windows-phone-single-sign-on/mobile-services-selection.png
[5]: ./media/mobile-services-windows-phone-single-sign-on/mobile-service-uri.png





[13]: ./media/mobile-services-windows-phone-single-sign-on/mobile-identity-tab-ma-only.png
[14]: ./media/mobile-services-windows-phone-single-sign-on/mobile-portal-data-tables.png
[15]: ./media/mobile-services-windows-phone-single-sign-on/mobile-portal-change-table-perms.png
[16]: ./media/mobile-services-windows-phone-single-sign-on/mobile-add-reference-live-wp8.png

<!-- URLs. -->
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows and Windows Phone]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Get started with Mobile Services]: /en-us/develop/mobile/tutorials/get-started-wp8
[Get started with data]: /en-us/develop/mobile/tutorials/get-started-with-data-wp8
[Get started with authentication]: /en-us/develop/mobile/tutorials/get-started-with-users-wp8
[Get started with push notifications]: /en-us/develop/mobile/tutorials/get-started-with-push-wp8
[Authorize users with scripts]: /en-us/develop/mobile/tutorials/authorize-users-in-scripts-wp8

[Azure Management Portal]: https://manage.windowsazure.com/