<properties
	pageTitle="Prise en main de l'authentification dans Mobile Services pour les applications Xamarin iOS | Microsoft Azure"
	description="Apprenez à utiliser Mobile Services pour authentifier les utilisateurs de votre application Xamarin iOS par l'intermédiaire de divers fournisseurs d'identité, notamment Google, Facebook, Twitter et Microsoft."
	services="mobile-services"
	documentationCenter="xamarin"
	authors="lindydonna"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-xamarin-ios"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="03/18/2016" 
	ms.author="donnam"/>

# Ajout de l'authentification à votre application Mobile Services

[AZURE.INCLUDE [mobile-services-selector-get-started-users](../../includes/mobile-services-selector-get-started-users.md)]

&nbsp;

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]
> Pour la version Mobile Apps équivalente de cette rubrique, consultez [Ajouter l’authentification à votre application Xamarin.iOS](../app-service-mobile/app-service-mobile-xamarin-ios-get-started-users.md).

Cette rubrique montre comment authentifier des utilisateurs dans Mobile Services à partir de votre application. Dans ce didacticiel, vous allez ajouter l'authentification au projet de démarrage rapide à l'aide d'un fournisseur d'identité pris en charge par Mobile Services. Après avoir été authentifiée et autorisée par Mobile Services, la valeur de l'ID utilisateur s'affiche.

Ce didacticiel vous familiarise avec les étapes de base permettant d'activer l'authentification dans votre application :

1. [Inscrire votre application pour l'authentification et configurer Mobile Services]
2. [Restreindre les autorisations de table aux utilisateurs authentifiés]
3. [Ajouter l'authentification à l'application]

Ce didacticiel est basé sur le démarrage rapide de Mobile Services. Vous devez aussi d'abord suivre le didacticiel [Prise en main de Mobile Services].

##<a name="register"></a>Inscription de votre application pour l'authentification et configuration de Mobile Services

[AZURE.INCLUDE [mobile-services-register-authentication](../../includes/mobile-services-register-authentication.md)]

[AZURE.INCLUDE [mobile-services-dotnet-backend-aad-server-extension](../../includes/mobile-services-dotnet-backend-aad-server-extension.md)]

##<a name="permissions"></a>Restriction des autorisations pour les utilisateurs authentifiés

[AZURE.INCLUDE [mobile-services-restrict-permissions-dotnet-backend](../../includes/mobile-services-restrict-permissions-dotnet-backend.md)]

&nbsp;&nbsp;&nbsp;6. Dans Visual Studio ou Xamarin Studio, exécutez le projet client sur un appareil ou un simulateur. Vérifiez qu'une exception non gérée avec un code d'état 401 (Non autorisé) est générée après le démarrage de l'application.

Cela se produit, car l’application essaie d’accéder à Mobile Services en tant qu’utilisateur non authentifié alors que la table *TodoItem* nécessite désormais l’authentification.

Ensuite, vous allez mettre à jour l'application pour authentifier les utilisateurs avant de demander des ressources à partir du service mobile.

##<a name="add-authentication"></a>Ajout de l'authentification à l'application

Dans cette section, vous allez modifier l'application de façon à afficher un écran de connexion avant d'afficher des données. Quand l’application démarre, elle ne se connecte pas à votre service mobile et n’affiche pas de données. Après le premier geste d'actualisation de l'utilisateur, l'écran de connexion s'affiche. Une fois la connexion réussie, la liste des tâches s'affiche.

1. Dans le projet client, ouvrez le fichier **QSTodoService.cs** et ajoutez les déclarations suivantes à QSTodoService :

		// Mobile Service logged in user
		private MobileServiceUser user;
		public MobileServiceUser User { get { return user; } }

2. Ajoutez une nouvelle méthode **Authenticate** à **QSTodoService** avec la définition suivante :

        private async Task Authenticate(UIViewController view)
        {
            try
            {
                user = await client.LoginAsync(view, MobileServiceAuthenticationProvider.Facebook);
            }
            catch (Exception ex)
            {
                Console.Error.WriteLine (@"ERROR - AUTHENTICATION FAILED {0}", ex.Message);
            }
        }

	> [AZURE.NOTE] Lorsque vous utilisez un autre fournisseur d’identité que Facebook, remplacez la valeur passée à la méthode **LoginAsync** ci-dessus par l’une des valeurs suivantes : _MicrosoftAccount_, _Twitter_, _Google_ ou _WindowsAzureActiveDirectory_.

3. Ouvrez **QSTodoListViewController.cs** et modifiez la définition de méthode de **ViewDidLoad** pour supprimer ou placer en commentaire l’appel à **RefreshAsync()** vers la fin.

4. Ajoutez le code suivant en haut de la définition de méthode **RefreshAsync** :

		// Add at the start of the RefreshAsync method.
		if (todoService.User == null) {
			await QSTodoService.DefaultService.Authenticate (this);
			if (todoService.User == null) {
				Console.WriteLine ("You must sign in.");
				return;
			}
		}
		
	Cette opération affiche un écran de connexion pour tenter l’authentification lorsque la propriété **User** a la valeur null. Lorsque la connexion est réussie, la propriété **User** est définie.

5. Appuyez sur le bouton **Exécuter** pour générer le projet et démarrer l'application dans le simulateur iPhone. Vérifiez que l'application n'affiche aucune donnée. **RefreshAsync()** n’a pas encore été appelé.

6. Effectuez le geste d’actualisation en tirant la liste des éléments vers le bas, ce qui appelle **RefreshAsync()**. Cette opération appelle **Authenticate()** pour démarrer l’authentification et l’écran de connexion s’affiche. Lorsque vous êtes authentifié, l'application affiche la liste des tâches et vous pouvez mettre à jour les données.

## <a name="next-steps"> </a>Étapes suivantes

Dans le didacticiel suivant, [Autorisation côté service des utilisateurs Mobile Services][Authorize users with scripts], vous allez prendre la valeur d'ID utilisateur fournie par Mobile Services sur la base d'un utilisateur authentifié et l'utiliser pour filtrer les données renvoyées par Mobile Services.


<!-- Anchors. -->
[Inscrire votre application pour l'authentification et configurer Mobile Services]: #register
[Restreindre les autorisations de table aux utilisateurs authentifiés]: #permissions
[Ajouter l'authentification à l'application]: #add-authentication
[Next Steps]: #next-steps


<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Prise en main de Mobile Services]: mobile-services-dotnet-backend-xamarin-ios-get-started.md
[Get started with authentication]: mobile-services-dotnet-backend-xamarin-ios-get-started-users.md
[Get started with push notifications]: mobile-services-dotnet-backend-xamarin-ios-get-started-push.md
[Authorize users with scripts]: ../mobile-services-dotnet-backend-windows-store-dotnet-authorize-users-in-scripts.md
[JavaScript and HTML]: ../mobile-services-dotnet-backend-windows-store-javascript-get-started-users.md

<!---HONumber=AcomDC_0323_2016-->