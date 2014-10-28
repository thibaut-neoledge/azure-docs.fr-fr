<properties linkid="develop-mobile-tutorials-sso-with-adal" urlDisplayName="Active Directory SSO Authentication with ADAL" pageTitle="Authenticate your app with Active Directory Authentication Library Single Sign-On (Windows Store) | Mobile Dev Center" metaKeywords="" description="Learn how to authentication users for single sign-on with ADAL in your Windows Store application." metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Authenticate your app with Active Directory Authentication Library Single Sign-On" authors="wesmc" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="wesmc"></tags>

# Authentification de votre application avec le service d'authentification unique de la bibliothèque d'authentification Active Directory

<div class="dev-center-tutorial-selector sublanding">
<a href="/fr-fr/documentation/articles/mobile-services-windows-store-dotnet-adal-sso-authentication" title="Windows Store&nbsp;C#" class="current">Windows Store&nbsp;C#</a>
<a href="/fr-fr/documentation/articles/mobile-services-dotnet-backend-ios-adal-sso-authentication" title="iOS" >iOS</a>
<a href="/fr-fr/documentation/articles/mobile-services-dotnet-backend-xamarin-ios-adal-sso-authentication" title="Xamarin.iOS">Xamarin.iOS</a>
</div>

Dans ce didacticiel, vous allez ajouter le processus d'authentification au projet de démarrage rapide à l'aide de la bibliothèque d'authentification Active Directory.

Pour qu'il soit possible d'authentifier les utilisateurs, vous devez inscrire votre application auprès d'Azure Active Directory (AAD). Cela se déroule en deux étapes : Vous devez d'abord inscrire votre service mobile et exposer les autorisations sur celui-ci. Vous devez ensuite inscrire votre application Windows Store et lui accorder l'accès à ces autorisations.

> [WACOM.NOTE] Ce didacticiel vise à mieux vous faire comprendre en quoi Mobile Services vous permet d'effectuer une authentification Azure Active Directory unique pour les applications Windows Store. Si vous n'avez aucune expérience de Mobile Services, suivez le didacticiel [Prise en main de Mobile Services][Prise en main de Mobile Services].

Ce didacticiel vous familiarise avec ces étapes de base :

1.  [Inscription de votre service mobile auprès d'Azure Active Directory][Inscription de votre service mobile auprès d'Azure Active Directory]
2.  [Inscription de votre application auprès d'Azure Active Directory][Inscription de votre application auprès d'Azure Active Directory]
3.  [Configuration du service mobile afin d'exiger une authentification][Configuration du service mobile afin d'exiger une authentification]
4.  [Ajout du code d'authentification à l'application cliente][Ajout du code d'authentification à l'application cliente]
5.  [Test du client à l'aide de l'authentification][Test du client à l'aide de l'authentification]

Ce didacticiel requiert les éléments suivants :

-   Visual Studio 2013 s'exécutant sous Windows 8.1.
-   L'exécution du didacticiel [Prise en main de Mobile Services][Prise en main de Mobile Services] ou [Prise en main de données][Prise en main de données].
-   Kit de développement logiciel (SDK) Microsoft Azure Mobile Services – Package NuGet
-   Bibliothèque d'authentification Active Directory – Package NuGet

[WACOM.INCLUDE [mobile-services-dotnet-adal-register-service][mobile-services-dotnet-adal-register-service]]

## <a name="register-app-aad"></a>Inscription de votre application auprès d'Azure Active Directory

Pour inscrire l'application auprès d'Azure Active Directory, vous devez l'associer au Windows Store et posséder un identificateur de sécurité (SID) du package pour l'application. Le SID en question est enregistré avec les paramètres d'application natifs dans Azure Active Directory.

### Association de l'application à un nouveau nom d'application Store

1.  Dans Visual Studio, cliquez avec le bouton droit sur l'application cliente, puis sélectionnez **Store** et **Associer l'application au Windows Store**.

    ![][]

2.  Connectez-vous à votre compte de centre de développement.

3.  Entrez le nom de l'application que vous souhaitez réserver pour l'application et cliquez ensuite sur **Réserver**.

    ![][1]

4.  Sélectionnez le nouveau nom d'application et cliquez sur **Suivant**.

5.  Cliquez sur **Associer** pour associer l'application au nom du magasin.

### Extraction de l'identificateur de sécurité (SID) du package de votre application

Vous devez, à présent, extraire l'identificateur de sécurité (SID) du package qui sera configuré avec les paramètres d'application natifs.

1.  Connectez-vous au [Tableau de bord du centre de développement Windows][Tableau de bord du centre de développement Windows], puis cliquez sur **Modifier** sur l'application.

    ![][2]

2.  Cliquez sur **Services**

    ![][3]

3.  Cliquez sur **Site des services Microsoft Live**.

    ![][4]

4.  Copiez votre SID de package situé en haut de la page.

    ![][5]

### Création de l'inscription de l'application native

1.  Accédez à **Active Directory** dans le [portail de gestion Azure][portail de gestion Azure], puis cliquez sur votre annuaire.

    ![][6]

2.  Cliquez en haut sur l'onglet **Applications**, puis cliquez pour **AJOUTER** une application.

    ![][7]

3.  Cliquez sur **Ajouter une application développée par mon organisation**.

4.  Dans l'Assistant Ajout d'application, entrez un **Nom** pour votre application et cliquez sur le type **Application cliente native**. Ensuite, cliquez pour continuer.

    ![][8]

5.  Dans la zone **URI de redirection**, collez le SID du package d'application que vous avez copié précédemment, puis cliquez pour terminer l'inscription.

    ![][9]

6.  Cliquez sur l'onglet **Configurer** de l'application native et copiez l'**ID client**. Vous en aurez besoin ultérieurement.

    ![][10]

7.  Faites défiler la page vers le bas jusqu'à la section **Autorisations pour d'autres applications** et accordez un accès complet à l'application du service mobile que vous avez inscrite précédemment. Cliquez ensuite sur **Enregistrer**.

    ![][11]

Votre service mobile est maintenant configuré dans AAD de manière à accepter des connexions via l'authentification unique à partir de votre application.

## <a name="require-authentication"></a>Configuration du service mobile afin d'exiger une authentification

[WACOM.INCLUDE [mobile-services-restrict-permissions-dotnet-backend][mobile-services-restrict-permissions-dotnet-backend]]

## <a name="add-authentication-code"></a>Ajout du code d'authentification à l'application cliente

1.  Ouvrez votre projet d'application cliente Windows Store dans Visual Studio.

[WACOM.INCLUDE [mobile-services-dotnet-adal-install-nuget][mobile-services-dotnet-adal-install-nuget]]

1.  Dans la fenêtre Explorateur de solutions de Visual Studio, ouvrez le fichier MainPage.xaml.cs et ajoutez les lignes suivantes à l'aide d'instructions.

        using Windows.UI.Popups;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
        using Newtonsoft.Json.Linq;

2.  Ajoutez le code suivant à la classe MainPage qui déclare la méthode `AuthenticateAsync`.

        private MobileServiceUser user; 
        private async Task AuthenticateAsync()
        {
            string authority = "<INSERT-AUTHORITY-HERE>";
            string resourceURI = "<INSERT-RESOURCE-URI-HERE>";
            string clientID = "<INSERT-CLIENT-ID-HERE>"; 
            while (user == null)
            {
                string message;
                try
                {
                  AuthenticationContext ac = new AuthenticationContext(authority);
                  AuthenticationResult ar = await ac.AcquireTokenAsync(resourceURI, clientID);
                  JObject payload = new JObject();
                  payload["access_token"] = ar.AccessToken;
                  user = await App.MobileService.LoginAsync(MobileServiceAuthenticationProvider.WindowsAzureActiveDirectory, payload);
                  message = string.Format("You are now logged in - {0}", user.UserId);
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

3.  Dans le code de la méthode `AuthenticateAsync` ci-dessus, remplacez **INSERT-AUTHORITY-HERE** par le nom du client dans lequel vous avez approvisionné votre application. Le format doit être <https://login.windows.net/tenant-name.onmicrosoft.com>. Cette valeur peut être copiée dans l'onglet Domaine de votre annuaire Azure Active Directory dans le [portail de gestion Azure][portail de gestion Azure].

4.  Dans le code de la méthode `AuthenticateAsync` ci-dessus, remplacez **INSERT-RESOURCE-URI-HERE** par l'**URI ID d'application** de votre service mobile. Si vous avez suivi les instructions de la rubrique [Inscription auprès de l'annuaire Azure Active Directory][Inscription auprès de l'annuaire Azure Active Directory], votre URI ID d'application doit être semblable à <https://todolist.azure-mobile.net/login/aad>.

5.  Dans le code de la méthode `AuthenticateAsync` ci-dessus, remplacez **INSERT-CLIENT-ID-HERE** par l'ID client que vous avez copié dans l'application cliente native.

6.  Dans la fenêtre Explorateur de solutions de Visual Studio, ouvrez le fichier Package.appxmanifest dans le projet client. Cliquez sur l'onglet **Capacités**, puis activez **Application d'entreprise** et **Réseaux privés (client et serveur)**. Enregistrez le fichier.

    ![][12]

7.  Dans le fichier MainPage.cs, mettez à jour le gestionnaire d'événements `OnNavigatedTo` pour appeler la méthode `AuthenticateAsync` comme suit.

        protected override async void OnNavigatedTo(NavigationEventArgs e)
        {
            if (user == null)
                await AuthenticateAsync();

            RefreshTodoItems();
        }

## <a name="test-client"></a>Test du client à l'aide de l'authentification

1.  Exécutez l'application cliente dans Visual Studio.
2.  Une invite s'affiche alors pour vous permettre de vous connecter à votre annuaire Azure Active Directory.
3.  L'application authentifie et renvoie les tâches à effectuer.

    ![][13]

<!-- Anchors. --> 

<!-- Images --> 

<!-- URLs. -->

  [Windows Store C\#]: /fr-fr/documentation/articles/mobile-services-windows-store-dotnet-adal-sso-authentication "Windows Store C#"
  [iOS]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-ios-adal-sso-authentication "iOS"
  [Xamarin.iOS]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-xamarin-ios-adal-sso-authentication "Xamarin.iOS"
  [Prise en main de Mobile Services]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started/
  [Inscription de votre service mobile auprès d'Azure Active Directory]: #register-mobile-service-aad
  [Inscription de votre application auprès d'Azure Active Directory]: #register-app-aad
  [Configuration du service mobile afin d'exiger une authentification]: #require-authentication
  [Ajout du code d'authentification à l'application cliente]: #add-authentication-code
  [Test du client à l'aide de l'authentification]: #test-client
  [Prise en main de données]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/
  [mobile-services-dotnet-adal-register-service]: ../includes/mobile-services-dotnet-adal-register-service.md
  []: ./media/mobile-services-windows-store-dotnet-adal-sso-authenticate/mobile-services-vs-associate-app.png
  [1]: ./media/mobile-services-windows-store-dotnet-adal-sso-authenticate/mobile-services-vs-reserve-store-appname.png
  [Tableau de bord du centre de développement Windows]: http://go.microsoft.com/fwlink/p/?LinkID=266734
  [2]: ./media/mobile-services-windows-store-dotnet-adal-sso-authenticate/mobile-services-store-app-edit.png
  [3]: ./media/mobile-services-windows-store-dotnet-adal-sso-authenticate/mobile-services-store-app-services.png
  [4]: ./media/mobile-services-windows-store-dotnet-adal-sso-authenticate/mobile-services-live-services-site.png
  [5]: ./media/mobile-services-windows-store-dotnet-adal-sso-authenticate/mobile-services-store-app-package-sid.png
  [portail de gestion Azure]: https://manage.windowsazure.com/
  [6]: ./media/mobile-services-windows-store-dotnet-adal-sso-authenticate/mobile-services-select-aad.png
  [7]: ./media/mobile-services-windows-store-dotnet-adal-sso-authenticate/mobile-services-aad-applications-tab.png
  [8]: ./media/mobile-services-windows-store-dotnet-adal-sso-authenticate/mobile-services-native-selection.png
  [9]: ./media/mobile-services-windows-store-dotnet-adal-sso-authenticate/mobile-services-native-sid-redirect-uri.png
  [10]: ./media/mobile-services-windows-store-dotnet-adal-sso-authenticate/mobile-services-native-client-id.png
  [11]: ./media/mobile-services-windows-store-dotnet-adal-sso-authenticate/mobile-services-native-add-permissions.png
  [mobile-services-restrict-permissions-dotnet-backend]: ../includes/mobile-services-restrict-permissions-dotnet-backend.md
  [mobile-services-dotnet-adal-install-nuget]: ../includes/mobile-services-dotnet-adal-install-nuget.md
  [Inscription auprès de l'annuaire Azure Active Directory]: /fr-fr/documentation/articles/mobile-services-how-to-register-active-directory-authentication/
  [12]: ./media/mobile-services-windows-store-dotnet-adal-sso-authenticate/mobile-services-package-appxmanifest.png
  [13]: ./media/mobile-services-windows-store-dotnet-adal-sso-authenticate/mobile-services-app-run.png
