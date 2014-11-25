<properties linkid="develop-mobile-tutorials-get-started-with-users-xamarin-ios" urlDisplayName="Get Started with Authentication (Xamarin.iOS)" pageTitle="Get started with authentication (Xamarin.iOS) - Mobile Services" metaKeywords="Azure registering application, Azure authentication, application authenticate, authenticate mobile services, Mobile Services Xamarin.iOS" description="Learn how to use authentication in your Azure Mobile Services app for Xamarin.iOS." metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile"  services="mobile-services" title="Get started with authentication in Mobile Services" manager="dwrede" authors="donnam" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-xamarin-ios" ms.devlang="dotnet" ms.topic="article" ms.date="09/23/2014" ms.author="donnam" />

# Prise en main de l'authentification dans Mobile Services

[WACOM.INCLUDE [mobile-services-selector-get-started-users](../includes/mobile-services-selector-get-started-users.md)]

Cette rubrique montre comment authentifier les utilisateurs dans Azure Mobile Services à partir de votre application. Dans ce didacticiel, vous allez ajouter l'authentification au projet de démarrage rapide à l'aide d'un fournisseur d'identité pris en charge par Mobile Services. Après avoir été authentifiée et autorisée par Mobile Services, la valeur de l'ID utilisateur s'affiche.

Ce didacticiel vous familiarise avec les étapes de base permettant d'activer l'authentification dans votre application :

1.  [Inscription de votre application pour l'authentification et configuration de Mobile Services][Inscription de votre application pour l'authentification et configuration de Mobile Services]
2.  [Restriction des autorisations de table pour les utilisateurs authentifiés][Restriction des autorisations de table pour les utilisateurs authentifiés]
3.  [Ajout de l'authentification à l'application][Ajout de l'authentification à l'application]

Ce didacticiel est basé sur le démarrage rapide de Mobile Services. Vous devez aussi d'abord suivre le didacticiel [Prise en main de Mobile Services][Prise en main de Mobile Services].

[Xamarin.iOS], XCode 5.0 et iOS 5.0 ou versions ultérieures sont requis pour suivre ce didacticiel.

## <a name="register"></a><span class="short-header">Inscription de votre application</span>Inscription de votre application pour l'authentification et configuration de Mobile Services

[WACOM.INCLUDE [mobile-services-register-authentication](../includes/mobile-services-register-authentication.md)]

## <a name="permissions"></a><span class="short-header">Restriction des autorisations</span>Restriction des autorisations pour les utilisateurs authentifiés

[WACOM.INCLUDE [mobile-services-restrict-permissions-javascript-backend](../includes/mobile-services-restrict-permissions-javascript-backend.md)]

1.  Dans Xcode, ouvrez le projet que vous avez créé avec le didacticiel [Prise en main de Mobile Services][Prise en main de Mobile Services].

2.  Appuyez sur le bouton **Exécuter** pour générer le projet et démarrer l'application dans l'émulateur iPhone ; vérifiez qu'une exception non prise en charge avec le code d'état 401 (Non autorisé) est générée après le démarrage de l'application.

    Cela se produit, car l'application essaye d'accéder à Mobile Services en tant qu'utilisateur non authentifié, mais la table *TodoItem* requiert désormais l'authentification.

Ensuite, vous allez mettre à jour l'application pour authentifier les utilisateurs avant de demander des ressources à partir du service mobile.

## <a name="add-authentication"></a><span class="short-header">Ajout de l'authentification</span>Ajout de l'authentification à l'application

1.  Ouvrez le fichier de projet **TodoService** et ajoutez les variables suivantes.

        // Mobile Service logged in user
        private MobileServiceUser user; 
        public MobileServiceUser User { get { return user; } }

2.  Ensuite, ajoutez une nouvelle méthode intitulée **Authenticate** à **TodoService** en la définissant comme suit :

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
<p>Si vous utilisez un fournisseur d'identit&eacute; diff&eacute;rent d'un compte Microsoft, remplacez la valeur transmise &agrave; la m&eacute;thode <strong>LoginAsync</strong> ci-dessus par l'une des valeurs suivantes&nbsp;: <i>Facebook</i>, <i>Twitter</i>, <i>Google</i> ou <i>WindowsAzureActiveDirectory</i>.</p>
</div>

3.  Déplacez la demande de la table **TodoItem** depuis le constructeur **TodoService** vers une nouvelle méthode intitulée **CreateTable** :

        private async Task CreateTable()
        {
            // Create an MSTable instance to allow us to work with the TodoItem table
            todoTable = client.GetTable<TodoItem>();
        }

4.  Créez une méthode publique asynchrone nommée **LoginAndGetData** en la définissant comme suit :

        public async Task LoginAndGetData(UIViewController view)
        {
            await Authenticate(view);
            await CreateTable();
        }

5.  Dans **TodoListViewController**, annulez la méthode **ViewDidAppear** et définissez-la comme indiqué ci-dessous. Ce code connecte l'utilisateur si le **TodoService** ne possède pas de descripteur sur l'utilisateur :

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

6.  Supprimez l'appel initial à **RefreshAsync** dans **TodoListViewController.ViewDidLoad**.

7.  Appuyez sur le bouton **Exécuter** pour générer le projet, démarrez l'application dans l'émulateur iPhone, puis connectez-vous avec le fournisseur d'identité de votre choix.

    Lorsque vous êtes connecté, l'application doit s'exécuter sans erreur et vous devez pouvoir exécuter des requêtes Mobile Services et mettre à jour les données.

## Téléchargement de l'exemple terminé

Téléchargez le [projet d'exemple terminé][projet d'exemple terminé]. Veillez à mettre à jour les variables **applicationURL** et **applicationKey** avec vos propres paramètres Azure.

## <a name="next-steps"></a>Étapes suivantes

Dans le didacticiel suivant, [Autorisation des utilisateurs avec des scripts][Autorisation des utilisateurs avec des scripts], vous allez utiliser la valeur de l'ID utilisateur fournie par Mobile Services en fonction de l'utilisateur authentifié et l'utiliser pour filtrer les données renvoyées par Mobile Services.



<!-- URLs. TODO:: update completed example project link with project download -->

  [Inscription de votre application pour l'authentification et configuration de Mobile Services]: #register
  [Restriction des autorisations de table pour les utilisateurs authentifiés]: #permissions
  [Ajout de l'authentification à l'application]: #add-authentication
  [Prise en main de Mobile Services]: /fr-fr/develop/mobile/tutorials/get-started-xamarin-ios
  [projet d'exemple terminé]: http://go.microsoft.com/fwlink/p/?LinkId=331328
  [Autorisation des utilisateurs avec des scripts]: /fr-fr/develop/mobile/tutorials/authorize-users-in-scripts-xamarin-ios
