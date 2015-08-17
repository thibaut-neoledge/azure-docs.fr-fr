<properties 
	pageTitle="Prise en main de l'authentification dans Mobile Services pour les applications Xamarin Android - Azure Mobile Services" 
	description="Apprenez à utiliser Mobile Services pour authentifier les utilisateurs de votre application Xamarin Android par l'intermédiaire de divers fournisseurs d'identité, notamment Google, Facebook, Twitter et Microsoft." 
	services="mobile-services" 
	documentationCenter="xamarin" 
	authors="lindydonna" 
	manager="dwrede" 
	editor="mollybos"/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-xamarin-android" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="04/13/2015" 
	ms.author="donnam"/>

# Prise en main de l'authentification dans Mobile Services

[AZURE.INCLUDE [mobile-services-selector-get-started-users](../../includes/mobile-services-selector-get-started-users.md)]

Cette rubrique montre comment authentifier les utilisateurs dans Azure Mobile Services à partir de votre application. Dans ce didacticiel, vous allez ajouter l'authentification au projet de démarrage rapide à l'aide d'un fournisseur d'identité pris en charge par Mobile Services. Après avoir été authentifiée et autorisée par Mobile Services, la valeur de l'ID utilisateur s'affiche.

Ce didacticiel vous familiarise avec les étapes de base permettant d'activer l'authentification dans votre application :

1. [Inscrire votre application pour l'authentification et configurer Mobile Services]
2. [Restreindre les autorisations de table aux utilisateurs authentifiés]
3. [Ajouter l'authentification à l'application]

Ce didacticiel est basé sur le démarrage rapide de Mobile Services. Vous devez aussi d'abord suivre le didacticiel [Prise en main de Mobile Services].

##<a name="register"></a>Inscription de votre application pour l'authentification et configuration de Mobile Services

[AZURE.INCLUDE [mobile-services-register-authentication](../../includes/mobile-services-register-authentication.md)]

[AZURE.INCLUDE [mobile-services-dotnet-backend-aad-server-extension](../../includes/mobile-services-dotnet-backend-aad-server-extension.md)]

##<a name="permissions"></a>Restriction des autorisations pour les utilisateurs authentifiés

[AZURE.INCLUDE [mobile-services-restrict-permissions-dotnet-backend](../../includes/mobile-services-restrict-permissions-dotnet-backend.md)]

<ol start="6">
<li><p>Dans Visual Studio ou Xamarin Studio, exécutez le projet client sur un appareil ou un simulateur. Vérifiez qu'une exception non gérée avec un code d'état&#160;401 (Non autorisé) est générée après le démarrage de l'application.</p>
   
   	<p>Cela se produit, car l'application essaye d'accéder à Mobile Services en tant qu'utilisateur non authentifié, mais la table <em>TodoItem</em> requiert désormais l'authentification.</p></li>
</ol>

Ensuite, vous allez mettre à jour l'application pour authentifier les utilisateurs avant de demander des ressources à partir du service mobile.

##<a name="add-authentication"></a>Ajouter l'authentification à l'application

1. Ajoutez la propriété suivante à la classe **TodoActivity** :

			private MobileServiceUser user;

2. Ajoutez la méthode suivante à la classe **TodoActivity** :

	        private async Task Authenticate()
	        {
	            try
	            {
	                user = await client.LoginAsync(this, MobileServiceAuthenticationProvider.Facebook);
	                CreateAndShowDialog(string.Format("you are now logged in - {0}", user.UserId), "Logged in!");
	            }
	            catch (Exception ex)
	            {
	                CreateAndShowDialog(ex, "Authentication failed");
	            }
	        }

    Cela crée une méthode pour gérer le processus d'authentification. L'utilisateur est authentifié à l'aide d'une connexion Facebook. Une boîte de dialogue affiche l'identifiant de l'utilisateur authentifié.

    > [AZURE.NOTE]Si vous utilisez un autre fournisseur d’identité que Facebook, remplacez la valeur passée à la méthode **LoginAsync** ci-dessus par l’une des valeurs suivantes : _MicrosoftAccount_, _Twitter_, _Google_ ou _WindowsAzureActiveDirectory_.

3. Dans la méthode **onCreate**, ajoutez la ligne de code suivante après le code qui permet d’instancier l’objet `MobileServiceClient`.

		// Get the Mobile Service Table instance to use
        toDoTable = client.GetTable <ToDoItem> ();

        await Authenticate(); // add this line

	Ceci démarre le processus d'authentification et l'attend de façon asynchrone.


4. À partir du menu **Exécuter**, cliquez sur **Exécuter** pour démarrer l'application et vous connecter avec le fournisseur d'identité choisi.

   	Lorsque vous êtes connecté, l'application doit s'exécuter sans erreur et vous devez pouvoir exécuter des requêtes Mobile Services et mettre à jour les données.


<!-- ## <a name="next-steps"> </a>Next steps

In the next tutorial, [Service-side authorization of Mobile Services users][Authorize users with scripts], you will take the user ID value provided by Mobile Services based on an authenticated user and use it to filter the data returned by Mobile Services. 
 -->
<!-- Anchors. -->
[Inscrire votre application pour l'authentification et configurer Mobile Services]: #register
[Restreindre les autorisations de table aux utilisateurs authentifiés]: #permissions
[Ajouter l'authentification à l'application]: #add-authentication
[Next Steps]: #next-steps


<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Prise en main de Mobile Services]: mobile-services-dotnet-backend-xamarin-android-get-started.md
[Get started with authentication]: mobile-services-dotnet-backend-xamarin-android-get-started-users.md
[Get started with push notifications]: mobile-services-dotnet-backend-xamarin-android-get-started-push.md
[Authorize users with scripts]: ../mobile-services-dotnet-backend-windows-store-dotnet-authorize-users-in-scripts.md
[JavaScript and HTML]: ../mobile-services-dotnet-backend-windows-store-javascript-get-started-users.md

[Azure Management Portal]: https://manage.windowsazure.com/
 

<!---HONumber=August15_HO6-->