<properties
	pageTitle="Prise en main de l'authentification (Xamarin.iOS) - Mobile Services"
	description="Découvrez comment utiliser l'authentification dans votre application Azure Mobile Services pour Xamarin.iOS."
	documentationCenter="xamarin"
	services="mobile-services"
	manager="dwrede"
	authors="lindydonna"
	editor=""/>


<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-xamarin-ios"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="02/11/2016"
	ms.author="donnam"/>

# Ajout de l'authentification à votre application Mobile Services

[AZURE.INCLUDE [mobile-services-selector-get-started-users](../../includes/mobile-services-selector-get-started-users.md)]

&nbsp;

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]
> Pour la version Mobile Apps équivalente de cette rubrique, consultez [Ajouter l’authentification à votre application Xamarin.iOS](../app-service-mobile/app-service-mobile-xamarin-ios-get-started-users.md).

Cette rubrique montre comment authentifier des utilisateurs dans Azure Mobile Services à partir de votre application. Dans ce didacticiel, vous allez ajouter l'authentification au projet de démarrage rapide à l'aide d'un fournisseur d'identité pris en charge par Mobile Services. Après avoir été authentifiée et autorisée par Mobile Services, la valeur de l'ID utilisateur s'affiche.

Ce didacticiel vous familiarise avec les étapes de base permettant d'activer l'authentification dans votre application :

1. [Inscrire votre application pour l'authentification et configurer Mobile Services]
2. [Restreindre les autorisations de table aux utilisateurs authentifiés]
3. [Ajouter l'authentification à l'application]

Ce didacticiel est basé sur le démarrage rapide de Mobile Services. Vous devez aussi d'abord suivre le didacticiel [Prise en main de Mobile Services].

[Xamarin.iOS], XCode 6.0 et iOS 7.0 ou versions ultérieures sont requis pour suivre ce didacticiel.

##<a name="register"></a>Inscrire votre application pour l'authentification et configurer Mobile Services

[AZURE.INCLUDE [mobile-services-register-authentication](../../includes/mobile-services-register-authentication.md)]

##<a name="permissions"></a>Restreindre les autorisations aux utilisateurs authentifiés


[AZURE.INCLUDE [mobile-services-restrict-permissions-javascript-backend](../../includes/mobile-services-restrict-permissions-javascript-backend.md)]


3. Dans Xcode, ouvrez le projet que vous avez créé avec le didacticiel [Prise en main de Mobile Services].

4. Appuyez sur le bouton **Exécuter** pour générer le projet et démarrer l'application dans l'émulateur iPhone ; vérifiez qu'une exception non prise en charge avec le code d'état 401 (Non autorisé) est générée après le démarrage de l'application.

   	Cela se produit, car l'application essaye d'accéder à Mobile Services en tant qu'utilisateur non authentifié, mais la table _TodoItem_ requiert désormais l'authentification.

Ensuite, vous allez mettre à jour l'application pour authentifier les utilisateurs avant de demander des ressources à partir du service mobile.

##<a name="add-authentication"></a>Ajout de l’authentification à l’application

1. Ouvrez le fichier de projet **QSToDoService** et ajoutez les variables suivantes.

		// Mobile Service logged in user
		private MobileServiceUser user;
		public MobileServiceUser User { get { return user; } }

2. Ensuite, ajoutez une nouvelle méthode intitulée **Authenticate** à **ToDoService** en la définissant comme suit :

        private async Task Authenticate(MonoTouch.UIKit.UIViewController view)
        {
            try
            {
                user = await client.LoginAsync(view, MobileServiceAuthenticationProvider.MicrosoftAccount);
            }
            catch (Exception ex)
            {
                Console.Error.WriteLine (@"ERROR - AUTHENTICATION FAILED {0}", ex.Message);
            }
        }

	> [AZURE.NOTE] Si vous utilisez un autre fournisseur d'identité qu'un compte Microsoft, remplacez la valeur passée à la méthode **LoginAsync** ci-dessus par l'une des valeurs suivantes : _Facebook_, _Twitter_, _Google_ ou _WindowsAzureActiveDirectory_.

3. Déplacez la demande de la table **ToDoItem** du constructeur **ToDoService** vers une nouvelle méthode intitulée **CreateTable** :

        private async Task CreateTable()
        {
            // Create an MSTable instance to allow us to work with the ToDoItem table
            todoTable = client.GetSyncTable<ToDoItem>();
        }

4. Créez une méthode publique asynchrone nommée **LoginAndGetData** en la définissant comme suit :

        public async Task LoginAndGetData(MonoTouch.UIKit.UIViewController view)
        {
            await Authenticate(view);
            await CreateTable();
        }

5. Dans **TodoListViewController**, annulez la méthode **ViewDidAppear** et définissez-la comme indiqué ci-dessous. Ce code connecte l'utilisateur si le **ToDoService** ne possède pas de descripteur sur l'utilisateur :

        public override async void ViewDidAppear(bool animated)
        {
            base.ViewDidAppear(animated);

            if (QSTodoService.DefaultService.User == null)
            {
                await QSTodoService.DefaultService.LoginAndGetData(this);
            }

            if (QSTodoService.DefaultService.User == null)
            {
                // TODO:: show error
                return;
            }


            await RefreshAsync();
        }
6. Supprimez l'appel initial à **RefreshAsync** dans **TodoListViewController.ViewDidLoad**.

7. Appuyez sur le bouton **Exécuter** pour générer le projet, démarrez l'application dans l'émulateur iPhone, puis connectez-vous avec le fournisseur d'identité de votre choix.

   	Lorsque vous êtes connecté, l'application doit s'exécuter sans erreur et vous devez pouvoir exécuter des requêtes Mobile Services et mettre à jour les données.

## Téléchargement de l'exemple terminé
Téléchargez le [projet d'exemple terminé]. Veillez à mettre à jour les variables **applicationURL** et **applicationKey** avec vos propres paramètres Azure.

## <a name="next-steps"></a>Étapes suivantes

Dans le didacticiel suivant, [Autorisation des utilisateurs avec des scripts], vous allez utiliser la valeur de l'ID utilisateur fournie par Mobile Services en fonction de l'utilisateur authentifié et l'utiliser pour filtrer les données renvoyées par Mobile Services.

<!-- Anchors. -->
[Inscrire votre application pour l'authentification et configurer Mobile Services]: #register
[Restreindre les autorisations de table aux utilisateurs authentifiés]: #permissions
[Ajouter l'authentification à l'application]: #add-authentication
[Next Steps]: #next-steps

<!-- Images. -->
[4]: ./media/partner-xamarin-mobile-services-ios-get-started-users/mobile-services-selection.png
[5]: ./media/partner-xamarin-mobile-services-ios-get-started-users/mobile-service-uri.png
[13]: ./media/partner-xamarin-mobile-services-ios-get-started-users/mobile-identity-tab.png
[14]: ./media/partner-xamarin-mobile-services-ios-get-started-users/mobile-portal-data-tables.png
[15]: ./media/partner-xamarin-mobile-services-ios-get-started-users/mobile-portal-change-table-perms.png

<!-- URLs. TODO:: update completed example project link with project download -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253

[Prise en main de Mobile Services]: /develop/mobile/tutorials/get-started-xamarin-ios
[Get started with data]: /develop/mobile/tutorials/get-started-with-data-xamarin-ios
[Get started with authentication]: /develop/mobile/tutorials/get-started-with-users-xamarin-ios
[Get started with push notifications]: /develop/mobile/tutorials/-get-started-with-push-xamarin-ios
[Autorisation des utilisateurs avec des scripts]: /develop/mobile/tutorials/authorize-users-in-scripts-xamarin-ios
[projet d'exemple terminé]: http://go.microsoft.com/fwlink/p/?LinkId=331328
[Xamarin.iOS]: http://xamarin.com/download

<!---HONumber=AcomDC_0323_2016-->