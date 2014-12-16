<properties urlDisplayName="Get Started with Authentication (Xamarin.iOS)" pageTitle="Prise en main de l'authentification (Xamarin.iOS) - Mobile Services" metaKeywords="inscription de l'application Azure, authentification Azure, authentification de l'application, authentification des services mobiles, Mobile Services Xamarin.iOS" description="Learn how to use authentication in your Azure Mobile Services app for Xamarin.iOS." metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile"  services="mobile-services" title="Get started with authentication in Mobile Services" manager="dwrede" authors="donnam" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-xamarin-ios" ms.devlang="dotnet" ms.topic="article" ms.date="09/23/2014" ms.author="donnam" />

# Ajout de l'authentification à votre application Mobile Services

[WACOM.INCLUDE [mobile-services-selector-get-started-users](../includes/mobile-services-selector-get-started-users.md)]

Cette rubrique vous présente l'authentification des utilisateurs dans Azure Mobile Services à partir de votre application.  Dans ce didacticiel, vous allez ajouter l'authentification au projet de démarrage rapide à l'aide d'un fournisseur d'identité pris en charge par Mobile Services. Après avoir été authentifiée et autorisée par Mobile Services, la valeur de l'ID utilisateur s'affiche.  

Ce didacticiel vous familiarise avec les étapes de base permettant d'activer l'authentification dans votre application :

1. [Inscription de votre application pour l'authentification et configuration de Mobile Services]
2. [Restriction des autorisations de table pour les utilisateurs authentifiés]
3. [Ajout de l'authentification à l'application]

Ce didacticiel est basé sur le démarrage rapide de Mobile Services. Vous devez aussi d'abord suivre le didacticiel [Prise en main de Mobile Services]. 

[Xamarin.iOS], XCode 5.0 et iOS 5.0 ou versions ultérieures sont requis pour suivre ce didacticiel.

<h2><a name="register"></a>Inscription de votre application pour l'authentification et configuration de Mobile Services</h2>

[WACOM.INCLUDE [mobile-services-register-authentication](../includes/mobile-services-register-authentication.md)] 

<h2><a name="permissions"></a>Restriction des autorisations pour les utilisateurs authentifiés</h2>


[WACOM.INCLUDE [mobile-services-restrict-permissions-javascript-backend](../includes/mobile-services-restrict-permissions-javascript-backend.md)] 


3. Dans Eclipse, ouvrez le projet que vous avez créé avec le didacticiel [Prise en main de Mobile Services]. 

4. Appuyez sur le bouton **Exécuter** pour générer le projet et démarrer l'application dans l'émulateur iPhone ; vérifiez qu'une exception non prise en charge avec le code d'état 401 (Non autorisé) est générée après le démarrage de l'application. 
   
   	Cela se produit car l'application essaye d'accéder à Mobile Services en tant qu'utilisateur non authentifié, mais la table _TodoItem_ requiert désormais l'authentification.

Ensuite, vous allez mettre à jour l'application pour authentifier les utilisateurs avant de demander des ressources à partir du service mobile.

<h2><a name="add-authentication"></a>Ajout de l'authentification à l'application</h2>

1. Ouvrez le fichier de projet **TodoService** et ajoutez les variables suivantes.

		// Mobile Service logged in user
		private MobileServiceUser user; 
		public MobileServiceUser User { get { return user; } }

2. Ensuite, ajoutez une nouvelle méthode intitulée **Authenticate** à **TodoService** en la définissant comme suit :

        private async Task Authenticate(UIViewController view)
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

    <div class="dev-callout"><b>Remarque</b>
	<p>Si vous utilisez un fournisseur d'identité différent d'un compte Microsoft, remplacez la valeur transmise à la méthode <strong>LoginAsync</strong> ci-dessus par l'une des valeurs suivantes : <i>Facebook</i>, <i>Twitter</i>, <i>Google</i> ou <i>WindowsAzureActiveDirectory</i>.</p>
    </div>

3. Déplacez la demande de la table **TodoItem** depuis le constructeur **TodoService** vers une nouvelle méthode intitulée **CreateTable** :

        private async Task CreateTable()
        {
            // Create an MSTable instance to allow us to work with the TodoItem table
            todoTable = client.GetTable<TodoItem>();
        }
	
4. Créez une méthode publique asynchrone nommée **LoginAndGetData** en la définissant comme suit :

        public async Task LoginAndGetData(UIViewController view)
        {
            await Authenticate(view);
            await CreateTable();
        }

5. Dans **TodoListViewController**, annulez la méthode **ViewDidAppear** et définissez-la comme indiqué ci-dessous. Ce code connecte l'utilisateur si le **TodoService** ne possède pas de descripteur sur l'utilisateur :

        public override async void ViewDidAppear(bool animated)
        {
            base.ViewDidAppear(animated);

            if (TodoService.DefaultService.User == null)
            {
                await TodoService.DefaultService.LoginAndGetData(this);
            }

            if (TodoService.DefaultService.User == null)
            {
                // TODO:: show error
                return;
            } 
                
            RefreshAsync();
        }
6. Supprimez l'appel initial à **RefreshAsync** dans **TodoListViewController.ViewDidLoad**.
		
7. Appuyez sur le bouton **Exécuter** pour générer le projet, démarrez l'application dans l'émulateur iPhone, puis connectez-vous avec le fournisseur d'identité de votre choix.

   	Lorsque vous êtes connecté, l'application doit s'exécuter sans erreur et vous devez pouvoir exécuter des requêtes Mobile Services et mettre à jour les données.

## Téléchargement de l'exemple terminé
Téléchargez [l'exemple de projet terminé]. Veillez à mettre à jour les variables **applicationURL** et **applicationKey** avec vos propres paramètres Azure. 

## <a name="next-steps"></a>Étapes suivantes

Dans le didacticiel suivant, [Autorisation des utilisateurs avec des scripts], vous allez utiliser la valeur de l'ID utilisateur fournie par Mobile Services en fonction de l'utilisateur authentifié et l'utiliser pour filtrer les données renvoyées par Mobile Services. 

<!-- Anchors. -->
[Inscription de votre application pour l'authentification et configuration de Mobile Services]: #register
[Restriction des autorisations de table pour les utilisateurs authentifiés]: #permissions
[Ajout de l'authentification à l'application]: #add-authentication
[Étapes suivantes]:#next-steps

<!-- Images. -->
[4]: ./media/partner-xamarin-mobile-services-ios-get-started-users/mobile-services-selection.png
[5]: ./media/partner-xamarin-mobile-services-ios-get-started-users/mobile-service-uri.png
[13]: ./media/partner-xamarin-mobile-services-ios-get-started-users/mobile-identity-tab.png
[14]: ./media/partner-xamarin-mobile-services-ios-get-started-users/mobile-portal-data-tables.png
[15]: ./media/partner-xamarin-mobile-services-ios-get-started-users/mobile-portal-change-table-perms.png

<!-- URLs. TODO:: update completed example project link with project download -->
[Page Soumettre une application]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Mes applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Kit de développement logiciel (SDK) Live]: http://go.microsoft.com/fwlink/p/?LinkId=262253

[Prise en main de Mobile Services]: /fr-fr/develop/mobile/tutorials/get-started-xamarin-ios
[Prise en main des données]: /fr-fr/develop/mobile/tutorials/get-started-with-data-xamarin-ios
[Prise en main de l'authentification]: /fr-fr/develop/mobile/tutorials/get-started-with-users-xamarin-ios
[Prise en main des notifications Push]: /fr-fr/develop/mobile/tutorials/-get-started-with-push-xamarin-ios
[Autorisation des utilisateurs avec des scripts]: /fr-fr/develop/mobile/tutorials/authorize-users-in-scripts-xamarin-ios

[Portail de gestion Azure]: https://manage.windowsazure.com/
[Exemple de projet terminé]: http://go.microsoft.com/fwlink/p/?LinkId=331328
