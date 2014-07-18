<properties linkid="develop-mobile-tutorials-get-started-with-users-dotnet" urlDisplayName="Get Started with Users" pageTitle="Get started with authentication (Windows Store) | Mobile Dev Center" metaKeywords="" description="Learn how to use Mobile Services to authenticate users of your Windows Store app through a variety of identity providers, including Google, Facebook, Twitter, and Microsoft." metaCanonical="" services="" documentationCenter="Mobile" title="Get started with authentication in Mobile Services" authors="" solutions="" manager="" editor="" />

Prise en main de l'authentification dans Mobile Services
========================================================

[Windows Store C\#](/en-us/develop/mobile/tutorials/get-started-with-users-dotnet "Windows Store C#")[Windows Store JavaScript](/en-us/develop/mobile/tutorials/get-started-with-users-js "Windows Store JavaScript")[Windows Phone](/en-us/develop/mobile/tutorials/get-started-with-users-wp8 "Windows Phone")[iOS](/en-us/develop/mobile/tutorials/get-started-with-users-ios "iOS")[Android](/en-us/develop/mobile/tutorials/get-started-with-users-android "Android")[HTML](/en-us/develop/mobile/tutorials/get-started-with-users-html "HTML")[Xamarin.iOS](/en-us/develop/mobile/tutorials/get-started-with-users-xamarin-ios "Xamarin.iOS")[Xamarin.Android](/en-us/develop/mobile/tutorials/get-started-with-users-xamarin-android "Xamarin.Android")

Cette rubrique montre comment authentifier les utilisateurs dans Azure Mobile Services à partir de votre application. Dans ce didacticiel, vous allez ajouter l'authentification au projet de démarrage rapide à l'aide d'un fournisseur d'identité pris en charge par Mobile Services. Après avoir été authentifiée et autorisée par Mobile Services, la valeur de l'ID utilisateur s'affiche.

Vous pouvez regarder une version vidéo de ce didacticiel en cliquant sur le clip à droite.

[regarder le didacticiel](http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Introduction-to-Windows-Azure-Mobile-Services) [Lire la vidéo](http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Windows-Store-app-Getting-Started-with-Authentication-in-Windows-Azure-Mobile-Services) 10:04

Ce didacticiel vous familiarise avec les étapes de base permettant d'activer l'authentification dans votre application :

1.  [Inscription de votre application pour l'authentification et configuration de Mobile Services](#register)
2.  [Restriction des autorisations de table pour les utilisateurs authentifiés](#permissions)
3.  [Ajout de l'authentification à l'application](#add-authentication)

Ce didacticiel est basé sur le démarrage rapide de Mobile Services. Vous devez aussi d'abord suivre le didacticiel [Prise en main de Mobile Services](/en-us/develop/mobile/tutorials/get-started).

**Remarque**

Ce didacticiel présente la méthode de base fournie par Mobile Services pour authentifier les utilisateurs à l'aide de différents fournisseurs d'identité. Cette méthode est facilement configurable et prend en charge plusieurs fournisseurs. Elle requiert toutefois que les utilisateurs se connectent à chaque fois que l'application démarre. Pour utiliser plutôt Live Connect afin de fournir une authentification unique dans votre application Windows Store, consultez la rubrique [Authentification unique pour les applications Windows Store à l'aide de Live Connect](/en-us/develop/mobile/tutorials/single-sign-on-windows-8-dotnet).

Inscription de votre applicationInscription de votre application pour l'authentification et configuration de Mobile Services
----------------------------------------------------------------------------------------------------------------------------

Pour pouvoir authentifier les utilisateurs, vous devez inscrire votre application avec un fournisseur d'identité. Vous devez ensuite inscrire la clé secrète cliente générée par le fournisseur avec Mobile Services.

1.  Connectez-vous au [portail de gestion Azure](https://manage.windowsazure.com/), cliquez sur **Mobile Services**, puis sur le service mobile.

	![][4]

2.  Cliquez sur l'onglet **Tableau de bord**, puis notez la valeur **Mobile Service URL**.

	![][5]

    Il se peut que le fournisseur d'identité vous demande cette valeur lorsque vous inscrivez votre application.

3.  Choisissez un fournisseur d'identité pris en charge dans la liste ci-dessous et suivez la procédure pour inscrire votre application auprès de ce fournisseur :

	-   [Compte Microsoft](/en-us/develop/mobile/how-to-guides/register-for-microsoft-authentication/)
	-   [Connexion Facebook](/en-us/develop/mobile/how-to-guides/register-for-facebook-authentication/)
	-   [Connexion Twitter](/en-us/develop/mobile/how-to-guides/register-for-twitter-authentication/)
	-   [Connexion Google](/en-us/develop/mobile/how-to-guides/register-for-google-authentication/)
	-   [Azure Active Directory](/en-us/documentation/articles/mobile-services-how-to-register-active-directory-authentication/)

    	N'oubliez pas de noter les valeurs de l'identité du client et de la clé secrète cliente générées par le fournisseur.

    	**Remarque relative à la sécurité**

    	La clé secrète générée par le fournisseur est une information d'identification de sécurité importante. Ne partagez pas cette clé secrète avec quiconque et ne la distribuez pas avec votre application.

1.  De retour dans le portail de gestion, cliquez sur l'onglet **Identité**, entrez les valeurs de l'identificateur d'application et de la clé secrète partagée fournies par votre fournisseur d'identité, puis cliquez sur **Enregistrer**.

	![][13]

2.  (Facultatif) Suivez la procédure décrite dans la rubrique [Inscription du package de votre application Windows Store pour l'authentification Microsoft](/en-us/develop/mobile/how-to-guides/register-windows-store-app-package).

    **Remarque**

    Cette étape est facultative, car elle concerne uniquement le fournisseur de connexion du compte Microsoft. Lorsque vous inscrivez les informations du package de votre application Windows Store auprès de Mobile Services, le client peut réutiliser les informations d'identification du compte Microsoft pour fournir une authentification unique. Si vous ne le faites pas, vos utilisateurs se connectant via le compte Microsoft seront invités à se connecter à chaque appel de la méthode de connexion. Suivez cette étape si vous prévoyez d'utiliser le fournisseur d'identité du compte Microsoft.

Votre service mobile et votre application sont désormais configurés pour utiliser le fournisseur d'authentification choisi.

Restrictions des autorisationsRestrictions des autorisations pour les utilisateurs authentifiés
-----------------------------------------------------------------------------------------------

1.  Dans le portail de gestion, cliquez sur l'onglet **Données**, puis sur la table **TodoItem**.

	![][14]

2.  Cliquez sur l'onglet **Autorisations**, définissez toutes les autorisations sur **Only authenticated users**, puis cliquez sur **Enregistrer**. Cela permet de s'assurer que toutes les opérations effectuées sur la table **TodoItem** requièrent un utilisateur authentifié. Cela simplifie aussi les scripts dans le didacticiel suivant, car il ne sera pas nécessaire d'autoriser la possibilité d'utilisateurs anonymes.

	![][15]

3.  Dans Visual Studio 2012 Express pour Windows 8, ouvrez le projet que vous avez créé avec le didacticiel [Prise en main de Mobile Services](/en-us/develop/mobile/tutorials/get-started).

4.  Appuyez sur la touche F5 pour exécuter cette application basée sur le démarrage rapide ; vérifiez qu'une exception non prise en charge avec le code d'état 401 (Non autorisé) est générée après le démarrage de l'application.

	Cela se produit car l'application essaye d'accéder à Mobile Services en tant qu'utilisateur non authentifié, mais la table _TodoItem_ requiert désormais l'authentification.

Ensuite, vous allez mettre à jour l'application pour authentifier les utilisateurs avant de demander des ressources à partir du service mobile.

Ajout de l'authentificationAjout de l'authentification à l'application
----------------------------------------------------------------------

1.  Ouvrez le fichier mainpage.xaml.cs et ajoutez l'instruction using suivante :

         using Windows.UI.Popups;

2.  Ajoutez l'extrait de code suivant à la classe MainPage :

         private MobileServiceUser user;
         private async System.Threading.Tasks.Task Authenticate()
         {
             while (user == null)
             {
                 string message;
                 try
                 {
                     user = await App.MobileService
                         .LoginAsync(MobileServiceAuthenticationProvider.Facebook);
                     message = 
                         string.Format("You are now logged in - {0}", user.UserId);
                 }
                 catch (InvalidOperationException)
                 {
                     message = "You must log in. Login Required";
                 }
                            
                 var dialog = new MessageDialog(message);
                 dialog.Commands.Add(new UICommand("OK"));
                 await dialog.ShowAsync();
             }
         }

    Cela crée une variable membre pour le stockage de l'utilisateur actuel et une méthode pour gérer le processus d'authentification. L'utilisateur est authentifié à l'aide d'une connexion Facebook. Si vous utilisez un fournisseur d'identité différent de Facebook, remplacez la valeur de **MobileServiceAuthenticationProvider** ci-dessus par la valeur de votre fournisseur.

    **Remarque**

    Si vous avez inscrit les informations du package de l'application Windows Store avec Mobile Services, vous devez appeler la méthode [LoginAsync](http://go.microsoft.com/fwlink/p/?LinkId=311594) en fournissant la valeur **true** pour le paramètre *useSingleSignOn*. Si vous ne le faites pas, vos utilisateurs seront toujours invités à se connecter à chaque appel de la méthode de connexion.

3.  Remplacez la méthode à substituer **OnNavigatedTo** par la méthode suivante qui appelle la nouvelle méthode **Authenticate** :

         protected override async void OnNavigatedTo(NavigationEventArgs e)
         {
             await Authenticate();
             RefreshTodoItems();
         }

4.  Appuyez sur la touche F5 pour exécuter l'application et vous connecter à l'application avec le fournisseur d'identité choisi.

	Lorsque vous êtes connecté, l'application doit s'exécuter sans erreur et vous devez pouvoir exécuter des requêtes Mobile Services et mettre à jour les données.

Étapes suivantes
----------------

Dans le didacticiel suivant, [Autorisation des utilisateurs avec des scripts](/en-us/develop/mobile/tutorials/authorize-users-in-scripts-dotnet), vous allez utiliser la valeur de l'ID utilisateur fournie par Mobile Services en fonction de l'utilisateur authentifié et l'utiliser pour filtrer les données renvoyées par Mobile Services. Obtenez plus d'informations sur Mobile Services avec .NET dans [le guide de fonctionnement Mobile Services .NET](/en-us/develop/mobile/how-to-guides/work-with-net-client-library)

<!-- Anchors. -->
[Register your app for authentication and configure Mobile Services]: #register
[Restrict table permissions to authenticated users]: #permissions
[Add authentication to the app]: #add-authentication
[Next Steps]:#next-steps

<!-- Images. -->




[4]: ./media/mobile-services-dotnet-get-started-users/mobile-services-selection.png
[5]: ./media/mobile-services-dotnet-get-started-users/mobile-service-uri.png







[13]: ./media/mobile-services-dotnet-get-started-users/mobile-identity-tab.png
[14]: ./media/mobile-services-dotnet-get-started-users/mobile-portal-data-tables.png
[15]: ./media/mobile-services-dotnet-get-started-users/mobile-portal-change-table-perms.png


<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Single sign-on for Windows Store apps by using Live Connect]: ./mobile-services-single-sign-on-win8-dotnet.md
[Get started with Mobile Services]: /en-us/develop/mobile/tutorials/get-started
[Get started with data]: /en-us/develop/mobile/tutorials/get-started-with-data-dotnet
[Get started with authentication]: /en-us/develop/mobile/tutorials/get-started-with-users-dotnet
[Get started with push notifications]: /en-us/develop/mobile/tutorials/get-started-with-push-dotnet
[Authorize users with scripts]: /en-us/develop/mobile/tutorials/authorize-users-in-scripts-dotnet
[JavaScript and HTML]: /en-us/develop/mobile/tutorials/get-started-with-users-js

[Azure Management Portal]: https://manage.windowsazure.com/
[Mobile Services .NET How-to Conceptual Reference]: /en-us/develop/mobile/how-to-guides/work-with-net-client-library
[Register your Windows Store app package for Microsoft authentication]: /en-us/develop/mobile/how-to-guides/register-windows-store-app-package