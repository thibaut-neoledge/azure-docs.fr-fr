<properties linkid="mobile-services-dotnet-backend-xamarin-ios-get-started-users" urlDisplayName="Get Started with authentication in Mobile Services for Xamarin iOS apps" pageTitle="Get Started with authentication in Mobile Services for Xamarin iOS apps - Azure Mobile Services" metaKeywords="" description="Learn how to use Mobile Services to authenticate users of your Xamarin iOS app through a variety of identity providers, including Google, Facebook, Twitter, and Microsoft." metaCanonical="" services="" documentationCenter="Mobile" title="Get Started with authentication in Mobile Services" authors="donnam" solutions="" manager="" editor="mollybos" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-xamarin-ios" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="donnam"></tags>

# Prise en main de l'authentification dans Mobile Services

<div class="dev-center-tutorial-selector sublanding">
  <a href="/fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-users" title="Windows Store&nbsp;C#">Windows Store&nbsp;C#</a>
<a href="/fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-users" title="Windows Store JavaScript">Windows Store JavaScript</a>
<a href="/fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-users" title="Windows Phone">Windows Phone</a>
<a href="/fr-fr/documentation/articles/mobile-services-dotnet-backend-ios-get-started-users" title="iOS">iOS</a>
<a href="/fr-fr/documentation/articles/mobile-services-dotnet-backend-android-get-started-users" title="Android">Android</a>
<a href="/fr-fr/documentation/articles/mobile-services-dotnet-backend-xamarin-ios-get-started-users" title="Xamarin.iOS" class="current">Xamarin.iOS</a>
<a href="/fr-fr/documentation/articles/mobile-services-dotnet-backend-xamarin-android-get-started-users" title="Xamarin.Android">Xamarin.Android</a>    
</div>

<div class="dev-center-tutorial-subselector"><a href="/fr-fr/documentation/articles/mobile-services-dotnet-backend-xamarin-ios-get-started-users/" title=".NET backend" class="current">.NET backend</a> | <a href="/fr-fr/documentation/articles/partner-xamarin-mobile-services-ios-get-started-users/"  title="JavaScript backend">JavaScript backend</a></div>

Cette rubrique montre comment authentifier les utilisateurs dans Azure Mobile Services à partir de votre application. Dans ce didacticiel, vous allez ajouter l'authentification au projet de démarrage rapide à l'aide d'un fournisseur d'identité pris en charge par Mobile Services. Après avoir été authentifiée et autorisée par Mobile Services, la valeur de l'ID utilisateur s'affiche.

Ce didacticiel vous familiarise avec les étapes de base permettant d'activer l'authentification dans votre application :

1.  [Inscription de votre application pour l'authentification et configuration de Mobile Services][Inscription de votre application pour l'authentification et configuration de Mobile Services]
2.  [Restriction des autorisations de table pour les utilisateurs authentifiés][Restriction des autorisations de table pour les utilisateurs authentifiés]
3.  [Ajout de l'authentification à l'application][Ajout de l'authentification à l'application]

Ce didacticiel est basé sur le démarrage rapide de Mobile Services. Vous devez aussi d'abord suivre le didacticiel [Prise en main de Mobile Services][Prise en main de Mobile Services].

## <a name="register"></a>Inscription de votre application pour l'authentification et configuration de Mobile Services

[WACOM.INCLUDE [mobile-services-register-authentication][mobile-services-register-authentication]]

[WACOM.INCLUDE [mobile-services-dotnet-backend-aad-server-extension][mobile-services-dotnet-backend-aad-server-extension]]

## <a name="permissions"></a>Restriction des autorisations pour les utilisateurs authentifiés

[WACOM.INCLUDE [mobile-services-restrict-permissions-dotnet-backend][mobile-services-restrict-permissions-dotnet-backend]]

1.  Dans Visual Studio ou Xamarin Studio, exécutez le projet client sur un appareil ou un simulateur. Vérifiez qu'une exception non gérée avec un code d'état 401 (Non autorisé) est générée après le démarrage de l'application.

    Cela se produit, car l'application essaye d'accéder à Mobile Services en tant qu'utilisateur non authentifié, mais la table *TodoItem* requiert désormais l'authentification.

Ensuite, vous allez mettre à jour l'application pour authentifier les utilisateurs avant de demander des ressources à partir du service mobile.

## <a name="add-authentication"></a>Ajout de l'authentification à l'application

Dans cette section, vous allez modifier l'application de façon à afficher un écran de connexion avant d'afficher des données. Lorsque l'application démarre, elle ne se connecte alors pas à votre service mobile et n'affiche pas de données. Après le premier geste d'actualisation de l'utilisateur, l'écran de connexion s'affiche. Une fois la connexion réussie, la liste des tâches s'affiche.

1.  Dans le projet client, ouvrez le fichier **QSTodoService.cs** et ajoutez les déclarations suivantes à QSTodoService :

        // Mobile Service logged in user
        private MobileServiceUser user; 
        public MobileServiceUser User { get { return user; } }

2.  Ajoutez une nouvelle méthode **Authenticate** à **QSTodoService** avec la définition suivante :

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

    <div class="dev-callout"><b>Remarque</b>
<p>Si vous utilisez un fournisseur d'identit&eacute; autre que Facebook, remplacez la valeur transmise &agrave; la m&eacute;thode <strong>LoginAsync</strong> ci-dessus par l'une des valeurs suivantes&nbsp;: <i>MicrosoftAccount</i>, <i>Twitter</i>, <i>Google</i> ou <i>WindowsAzureActiveDirectory</i>.</p>
</div>

3.  Ouvrez **QSTodoListViewController.cs**. Modifiez la définition de méthode de **ViewDidLoad** pour supprimer l'appel à **RefreshAsync()** vers la fin :

        public override async void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            todoService = QSTodoService.DefaultService;

            todoService.BusyUpdate += (bool busy) => {
                if (busy)
                    activityIndicator.StartAnimating ();
                else 
                    activityIndicator.StopAnimating ();
            };

            // Comment out the call to RefreshAsync
            // await RefreshAsync ();

            AddRefreshControl ();
        }

4.  Modifiez la méthode **RefreshAsync** pour vous authentifier et afficher un écran de connexion si la propriété **User** a la valeur null. Au code suivant en haut de la définition de méthode :

        // start of RefreshAsync method
        if (todoService.User == null) {
            await QSTodoService.DefaultService.Authenticate (this);
            if (todoService.User == null) {
                Console.WriteLine ("couldn't login!!");
                return;
            }
        }
        // rest of RefreshAsync method

5.  Appuyez sur le bouton **Exécuter** pour générer le projet et démarrer l'application dans le simulateur iPhone. Vérifiez que l'application n'affiche aucune donnée.

    Effectuez le geste d'actualisation en affichant la liste des éléments, ce qui fait apparaître l'écran de connexion. Une fois que vous avez entré des informations d'identification valides, l'application affiche la liste des tâches et vous pouvez apporter des mises à jour aux données.

<!-- ## <a name="next-steps"> </a>Next steps  In the next tutorial, [Service-side authorization of Mobile Services users][Authorize users with scripts], you will take the user ID value provided by Mobile Services based on an authenticated user and use it to filter the data returned by Mobile Services.   -->
<!-- Anchors. -->
<!-- URLs. -->

  [Windows Store C#]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-users "Windows Store C#"
  [Windows Store JavaScript]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-users "Windows Store JavaScript"
  [Windows Phone]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-users "Windows Phone"
  [iOS]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-ios-get-started-users "iOS"
  [Android]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-android-get-started-users "Android"
  [Xamarin.iOS]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-xamarin-ios-get-started-users "Xamarin.iOS"
  [Xamarin.Android]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-xamarin-android-get-started-users "Xamarin.Android"
  [.NET backend]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-xamarin-ios-get-started-users/ ".NET backend"
  [JavaScript backend]: /fr-fr/documentation/articles/partner-xamarin-mobile-services-ios-get-started-users/ "JavaScript backend"
  [Inscription de votre application pour l'authentification et configuration de Mobile Services]: #register
  [Restriction des autorisations de table pour les utilisateurs authentifiés]: #permissions
  [Ajout de l'authentification à l'application]: #add-authentication
  [Prise en main de Mobile Services]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-xamarin-ios-get-started/
  [mobile-services-register-authentication]: ../includes/mobile-services-register-authentication.md
  [mobile-services-dotnet-backend-aad-server-extension]: ../includes/mobile-services-dotnet-backend-aad-server-extension.md
  [mobile-services-restrict-permissions-dotnet-backend]: ../includes/mobile-services-restrict-permissions-dotnet-backend.md
