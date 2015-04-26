<properties 
	pageTitle="Authentification de votre application avec le service d'authentification unique de la bibliothèque d'authentification Active Directory (Xamarin.iOS) | Centre de développement mobile" 
	description="Découvrez comment authentifier les utilisateurs pour l'authentification unique avec la bibliothèque d'authentification AD dans votre application Xamarin.iOS." 
	documentationCenter="xamarin" 
	authors="wesmc7777" 
	manager="dwrede" 
	editor="" 
	services=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-xamarin-ios" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="09/29/2014" 
	ms.author="wesmc,mahender"/>

# Authentification de votre application avec le service d'authentification unique de la bibliothèque d'authentification Active Directory

[AZURE.INCLUDE [mobile-services-selector-adal-sso](../includes/mobile-services-selector-adal-sso.md)]

Dans ce didacticiel, vous allez ajouter le processus d'authentification au projet de démarrage rapide à l'aide de la bibliothèque d'authentification Active Directory. 

Pour qu'il soit possible d'authentifier les utilisateurs, vous devez inscrire votre application auprès d'Azure Active Directory (AAD). Cela se déroule en deux étapes : Vous devez d'abord inscrire votre service mobile et exposer les autorisations sur celui-ci. Ensuite, vous devez enregistrer votre application Xamarin.iOS et lui accorder l'accès à ces autorisations.


>[AZURE.NOTE] Ce didacticiel vise à mieux vous faire comprendre comment Mobile Services vous permet d'effectuer une authentification Azure Active Directory unique pour les applications Xamarin.iOS. Si vous n'avez aucune expérience de Mobile Services, suivez le didacticiel [Prise en main de Mobile Services].

Ce didacticiel vous familiarise avec ces étapes de base :

1. [Inscription de votre service mobile auprès d'Azure Active Directory]
2. [Inscription de votre application auprès d'Azure Active Directory]
3. [Configuration du service mobile afin d'exiger une authentification]
4. [Ajout du code d'authentification à l'application cliente]
5. [Test du client à l'aide de l'authentification]

Ce didacticiel requiert les éléments suivants :

* XCode 4.5 et iOS 6.0 (ou versions ultérieures) 
* Visual Studio avec l'[extension Xamarin] ou [Xamarin Studio] sur OS X
* Exécution du didacticiel [Prise en main de Mobile Services] ou [Prise en main des données].
* Kit de développement logiciel (SDK) de Microsoft Azure Mobile Services
* [Liaison Xamarin pour la bibliothèque d'authentification Active Directory pour iOS].

[AZURE.INCLUDE [mobile-services-dotnet-adal-register-service](../includes/mobile-services-dotnet-adal-register-service.md)]

[AZURE.INCLUDE [mobile-services-dotnet-adal-register-client](../includes/mobile-services-dotnet-adal-register-client.md)]

## <a name="require-authentication"></a>Configuration du service mobile afin d'exiger une authentification

[AZURE.INCLUDE [mobile-services-restrict-permissions-dotnet-backend](../includes/mobile-services-restrict-permissions-dotnet-backend.md)]

## <a name="add-authentication-code"></a>Ajout du code d'authentification à l'application cliente

1. Ajoutez votre liaison Xamarin pour la bibliothèque d'authentification Active Directory à votre projet Xamarin.iOS. Dans Visual Studio 2013, cliquez avec le bouton droit sur **Référence** sélectionnez **Ajouter une référence**. Accédez ensuite à votre bibliothèque de liaison et cliquez sur **Ajouter**. Veillez également à ajouter les storyboards à partir de la source ADAL.

2. Ajoutez le code suivant à la classe QSTodoService : 

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

6. Dans le code de la méthode `AuthenticateAsync` ci-dessus, remplacez **INSERT-AUTHORITY-HERE** par le nom du client dans lequel vous avez déployé votre application ; le format doit être https://login.windows.net/tenant-name.onmicrosoft.com. Cette valeur peut être copiée à partir de l'onglet Domaine de votre annuaire Azure Active Directory dans le [portail de gestion Azure].

7. Dans le code de la méthode  `AuthenticateAsync` ci-dessus, remplacez **INSERT-RESOURCE-URI-HERE** par l'**App ID URI** de votre service mobile. Si vous avez suivi les instructions de la rubrique [Inscription auprès de l'annuaire Azure Active Directory], votre URI ID d'application doit être semblable à https://todolist.azure-mobile.net/login/aad.

8. Dans le code de la méthode `AuthenticateAsync` ci-dessus, remplacez **INSERT-CLIENT-ID-HERE** par l'ID client que vous avez copié dans l'application cliente native.

9. Dans le code de la méthode `AuthenticateAsync` ci-dessus, remplacez **INSERT-REDIRECT-URI-HERE** par le /login/done endpoint de votre service mobile. Cette valeur doit être semblable à https://todolist.azure-mobile.net/login/done.


3. Dans QSTodoListViewController, modifiez **ViewDidLoad** en ajoutant le code suivant juste avant l'appel à RefreshAsync();

        if (QSTodoService.DefaultService.User == null)
        {
            await QSTodoService.DefaultService.Authenticate();
        }

## <a name="test-client"></a>Test du client à l'aide de l'authentification

1. Dans le menu Exécuter, cliquez sur Exécuter pour démarrer l'application. 
2. Une invite s'affiche alors pour vous permettre de vous connecter à votre annuaire Azure Active Directory.  
3. L'application authentifie et renvoie les tâches à effectuer.

   ![](./media/mobile-services-dotnet-backend-xamarin-ios-adal-sso-authentication/mobile-services-app-run.png)

<!-- Anchors. -->
[Inscription de votre service mobile auprès d'Azure Active Directory]: #register-mobile-service-aad
[Inscription de votre application auprès d'Azure Active Directory]: #register-app-aad
[Configuration du service mobile afin d'exiger une authentification]: #require-authentication
[Ajout du code d'authentification à l'application cliente]: #add-authentication-code
[Test du client à l'aide de l'authentification]: #test-client

<!-- URLs. -->
[Prise en main des données]: /fr-fr/documentation/articles/partner-xamarin-mobile-services-ios-get-started-data/
[Prise en main de Mobile Services]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-xamarin-ios-get-started/
[Inscription auprès d'Azure Active Directory]: /fr-fr/documentation/articles/mobile-services-how-to-register-active-directory-authentication/
[Portail de gestion Azure]: https://manage.windowsazure.com/
[Liaison Xamarin pour la bibliothèque d'authentification Active Directory pour iOS]: https://github.com/AzureADSamples/NativeClient-Xamarin-iOS
[Extension Xamarin]: http://xamarin.com/visual-studio
[Xamarin Studio]: http://xamarin.com/download


<!--HONumber=42-->
