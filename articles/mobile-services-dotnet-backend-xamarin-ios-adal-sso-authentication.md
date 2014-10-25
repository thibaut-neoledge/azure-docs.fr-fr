<properties linkid="develop-mobile-tutorials-sso-with-adal-xamarin-ios" urlDisplayName="Active Directory SSO Authentication with ADAL" pageTitle="Authenticate your app with Active Directory Authentication Library Single Sign-On (Xamarin.iOS) | Mobile Dev Center" metaKeywords="" description="Learn how to authentication users for single sign-on with ADAL in your Xamarin.iOS application." metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Authenticate your app with Active Directory Authentication Library Single Sign-On" authors="wesmc,mahender" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-xamarin-ios" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="wesmc,mahender"></tags>

# Authentification de votre application avec le service d'authentification unique de la bibliothèque d'authentification Active Directory

<div class="dev-center-tutorial-selector sublanding">
<a href="/fr-fr/documentation/articles/mobile-services-windows-store-dotnet-adal-sso-authentication" title="Windows Store&nbsp;C#" >Windows Store&nbsp;C#</a>
<a href="/fr-fr/documentation/articles/mobile-services-dotnet-backend-ios-adal-sso-authentication" title="iOS" >iOS</a>
<a href="/fr-fr/documentation/articles/mobile-services-dotnet-backend-xamarin-ios-adal-sso-authentication" title="Xamarin.iOS" class="current">Xamarin.iOS</a>
</div>

Dans ce didacticiel, vous allez ajouter le processus d'authentification au projet de démarrage rapide à l'aide de la bibliothèque d'authentification Active Directory.

Pour qu'il soit possible d'authentifier les utilisateurs, vous devez inscrire votre application auprès d'Azure Active Directory (AAD). Cela se déroule en deux étapes : Vous devez d'abord inscrire votre service mobile et exposer les autorisations sur celui-ci. Ensuite, vous devez enregistrer votre application Xamarin.iOS et lui accorder l'accès à ces autorisations.

> [WACOM.NOTE] Ce didacticiel vise à mieux vous faire comprendre en quoi Mobile Services vous permet d'effectuer une authentification Azure Active Directory unique pour les applications Xamarin.iOS. Si vous n'avez aucune expérience de Mobile Services, suivez le didacticiel [Prise en main de Mobile Services][Prise en main de Mobile Services].

Ce didacticiel vous familiarise avec ces étapes de base :

1.  [Inscription de votre service mobile auprès d'Azure Active Directory][Inscription de votre service mobile auprès d'Azure Active Directory]
2.  [Inscription de votre application auprès d'Azure Active Directory][Inscription de votre application auprès d'Azure Active Directory]
3.  [Configuration du service mobile afin d'exiger une authentification][Configuration du service mobile afin d'exiger une authentification]
4.  [Ajout du code d'authentification à l'application cliente][Ajout du code d'authentification à l'application cliente]
5.  [Test du client à l'aide de l'authentification][Test du client à l'aide de l'authentification]

Ce didacticiel requiert les éléments suivants :

-   XCode 4.5 et iOS 6.0 (ou versions ultérieures)
-   Visual Studio avec l'[extension Xamarin][extension Xamarin] ou [Xamarin Studio][Xamarin Studio] sur OS X
-   L'exécution du didacticiel [Prise en main de Mobile Services][Prise en main de Mobile Services] ou [Prise en main de données][Prise en main de données].
-   Kit de développement logiciel (SDK) de Microsoft Azure Mobile Services
-   [Liaison Xamarin pour la bibliothèque d'authentification Active Directory pour iOS][Liaison Xamarin pour la bibliothèque d'authentification Active Directory pour iOS].

[WACOM.INCLUDE [mobile-services-dotnet-adal-register-service][mobile-services-dotnet-adal-register-service]]

[WACOM.INCLUDE [mobile-services-dotnet-adal-register-client][mobile-services-dotnet-adal-register-client]]

## <a name="require-authentication"></a>Configuration du service mobile afin d'exiger une authentification

[WACOM.INCLUDE [mobile-services-restrict-permissions-dotnet-backend][mobile-services-restrict-permissions-dotnet-backend]]

## <a name="add-authentication-code"></a>Ajout du code d'authentification à l'application cliente

1.  Ajoutez votre liaison Xamarin pour la bibliothèque d'authentification Active Directory à votre projet Xamarin.iOS. Dans Visual Studio 2013, cliquez avec le bouton droit sur **Référence** et sélectionnez **Ajouter une référence**. Accédez ensuite à votre bibliothèque de liaison et cliquez sur **Ajouter**. Veillez également à ajouter les storyboards à partir de la source ADAL.

2.  Ajoutez le code suivant à la classe QSTodoService :

        private MobileServiceUser user;
        public MobileServiceUser User { get { return user; } }

        public async Task Authenticate()
        {
            var aadAuthority = @"<INSERT-AUTHORITY-HERE>";
            var aadResource = @"<INSERT-RESOURCE-URI-HERE>";
            var aadClientId = @"<INSERT-CLIENT-ID-HERE>";
            var aadRedirect = @"<INSERT-REDIRECT-URI-HERE>";

            ADAuthenticationError error;
            var aadContext = ADAuthenticationContext.AuthenticationContextWithAuthority(aadAuthority, out error);
            if (error != null)
            {
                throw new InvalidOperationException("Error creating the AAD context: " + error.ErrorDetails);
            }

            var tcs = new TaskCompletionSource<string>();
            aadContext.AcquireTokenWithResource(aadResource, aadClientId, new NSUrl(aadRedirect), result =>
                {
                    if (result.Status == ADAuthenticationResultStatus.SUCCEEDED)
                    {
                        tcs.SetResult(result.AccessToken);
                    }
                    else
                    {
                        string errorDetails;
                        if (result.Status == ADAuthenticationResultStatus.USER_CANCELLED)
                        {
                            errorDetails = "Authentication cancelled by user";
                        }
                        else
                        {
                            errorDetails = result.Error.ErrorDetails;
                        }

                        tcs.SetException(new InvalidOperationException("Error during authentication: " + errorDetails));
                    }
                });
            string accessToken = await tcs.Task;

            var token = new JObject(new JProperty("access_token", accessToken));

            try
            {
                user = await this.client.LoginAsync(MobileServiceAuthenticationProvider.WindowsAzureActiveDirectory, token);
            }
            catch (Exception ex)
            {
                Console.Error.WriteLine (@"ERROR - AUTHENTICATION FAILED {0}", ex.Message);
            }
        }

3.  Dans le code de la méthode `AuthenticateAsync` ci-dessus, remplacez **INSERT-AUTHORITY-HERE** par le nom du client dans lequel vous avez approvisionné votre application. Le format doit être <https://login.windows.net/tenant-name.onmicrosoft.com>. Cette valeur peut être copiée dans l'onglet Domaine de votre annuaire Azure Active Directory dans le [portail de gestion Azure][portail de gestion Azure].

4.  Dans le code de la méthode `AuthenticateAsync` ci-dessus, remplacez **INSERT-RESOURCE-URI-HERE** par l'**URI ID d'application** de votre service mobile. Si vous avez suivi les instructions de la rubrique [Inscription auprès de l'annuaire Azure Active Directory][Inscription auprès de l'annuaire Azure Active Directory], votre URI ID d'application doit être semblable à <https://todolist.azure-mobile.net/login/aad>.

5.  Dans le code de la méthode `AuthenticateAsync` ci-dessus, remplacez **INSERT-CLIENT-ID-HERE** par l'ID client que vous avez copié dans l'application cliente native.

6.  Dans le code de la méthode `AuthenticateAsync` ci-dessus, remplacez **INSERT-REDIRECT-URI-HERE** par le point de terminaison /login/done de votre service mobile. Cette valeur doit être semblable à <https://todolist.azure-mobile.net/login/done>.

7.  Dans QSTodoListViewController, modifiez **ViewDidLoad** en ajoutant le code suivant juste avant l'appel à RefreshAsync();

        if (QSTodoService.DefaultService.User == null)
        {
            await QSTodoService.DefaultService.Authenticate();
        }

## <a name="test-client"></a>Test du client à l'aide de l'authentification

1.  Dans le menu Exécuter, cliquez sur Exécuter pour démarrer l'application.
2.  Une invite s'affiche alors pour vous permettre de vous connecter à votre annuaire Azure Active Directory.
3.  L'application authentifie et renvoie les tâches à effectuer.

![][]

<!-- Anchors. -->
<!-- URLs. -->

  [Windows Store C\#]: /fr-fr/documentation/articles/mobile-services-windows-store-dotnet-adal-sso-authentication "Windows Store C#"
  [iOS]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-ios-adal-sso-authentication "iOS"
  [Xamarin.iOS]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-xamarin-ios-adal-sso-authentication "Xamarin.iOS"
  [Prise en main de Mobile Services]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-xamarin-ios-get-started/
  [Inscription de votre service mobile auprès d'Azure Active Directory]: #register-mobile-service-aad
  [Inscription de votre application auprès d'Azure Active Directory]: #register-app-aad
  [Configuration du service mobile afin d'exiger une authentification]: #require-authentication
  [Ajout du code d'authentification à l'application cliente]: #add-authentication-code
  [Test du client à l'aide de l'authentification]: #test-client
  [extension Xamarin]: http://xamarin.com/visual-studio
  [Xamarin Studio]: http://xamarin.com/download
  [Prise en main de données]: /fr-fr/documentation/articles/partner-xamarin-mobile-services-ios-get-started-data/
  [Liaison Xamarin pour la bibliothèque d'authentification Active Directory pour iOS]: https://github.com/AzureADSamples/NativeClient-Xamarin-iOS
  [mobile-services-dotnet-adal-register-service]: ../includes/mobile-services-dotnet-adal-register-service.md
  [mobile-services-dotnet-adal-register-client]: ../includes/mobile-services-dotnet-adal-register-client.md
  [mobile-services-restrict-permissions-dotnet-backend]: ../includes/mobile-services-restrict-permissions-dotnet-backend.md
  [portail de gestion Azure]: https://manage.windowsazure.com/
  [Inscription auprès de l'annuaire Azure Active Directory]: /fr-fr/documentation/articles/mobile-services-how-to-register-active-directory-authentication/
  []: ./media/mobile-services-dotnet-backend-xamarin-ios-adal-sso-authentication/mobile-services-app-run.png
