<properties 
	pageTitle="Authentification de votre application avec le service d'authentification unique de la bibliothèque d'authentification Active Directory (iOS) | Centre de développement mobile" 
	description="Découvrez comment authentifier les utilisateurs pour l'authentification unique avec la bibliothèque d'authentification AD dans votre application iOS." 
	documentationCenter="ios" 
	authors="wesmc7777" 
	manager="dwrede" 
	editor="" 
	services=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-ios" 
	ms.devlang="objective-c" 
	ms.topic="article" 
	ms.date="10/10/2014" 
	ms.author="wesmc,mahender"/>

# Authentification de votre application avec le service d'authentification unique de la bibliothèque d'authentification Active Directory

[AZURE.INCLUDE [mobile-services-selector-adal-sso](../includes/mobile-services-selector-adal-sso.md)]

Dans ce didacticiel, vous allez ajouter le processus d'authentification au projet de démarrage rapide à l'aide de la bibliothèque d'authentification Active Directory.

Pour qu'il soit possible d'authentifier les utilisateurs, vous devez inscrire votre application auprès d'Azure Active Directory (AAD). Cela se déroule en deux étapes : Vous devez d'abord inscrire votre service mobile et exposer les autorisations sur celui-ci. Vous devez ensuite inscrire votre application iOS et lui accorder l'accès à ces autorisations.


>[AZURE.NOTE] Ce didacticiel vise à mieux vous faire comprendre en quoi Mobile Services vous permet d'effectuer une authentification Azure Active Directory unique pour les applications iOS. Si vous n'avez aucune expérience de Mobile Services, suivez le didacticiel [Prise en main de Mobile Services].

Ce didacticiel vous familiarise avec ces étapes de base :

1. [Inscription de votre service mobile auprès d'Azure Active Directory]
2. [Inscription de votre application auprès d'Azure Active Directory]
3. [Configuration du service mobile afin d'exiger une authentification]
4. [Ajout du code d'authentification à l'application cliente]
5. [Test du client à l'aide de l'authentification]

Ce didacticiel requiert les éléments suivants :

* XCode 4.5 et iOS 6.0 (ou versions ultérieures)
* Assimilation du didacticiel [Prise en main de Mobile Services] ou [Prise en main des données].
* Kit de développement logiciel (SDK) de Microsoft Azure Mobile Services
* [Bibliothèque d'authentification Active Directory pour iOS]

[AZURE.INCLUDE [mobile-services-dotnet-adal-register-client](../includes/mobile-services-dotnet-adal-register-client.md)]

## <a name="require-authentication"></a>Configuration du service mobile afin d'exiger une authentification

[AZURE.INCLUDE [mobile-services-restrict-permissions-dotnet-backend](../includes/mobile-services-restrict-permissions-dotnet-backend.md)]

## <a name="add-authentication-code"></a>Ajout du code d'authentification à l'application cliente

1. Téléchargez la [bibliothèque d'authentification Active Directory pour iOS] et incluez-la dans votre projet. Veillez également à ajouter les storyboards à partir de la source ADAL.

2. Dans le contrôleur QSTodoListViewController, incluez ADAL avec le code suivant :

        #import "ADALiOS/ADAuthenticationContext.h"

2. Ajoutez ensuite la méthode suivante :

        - (void) loginAndGetData
        {
            MSClient *client = self.todoService.client;
            if (client.currentUser != nil) {
                return;
            }

            NSString *authority = @"<INSERT-AUTHORITY-HERE>";
            NSString *resourceURI = @"<INSERT-RESOURCE-URI-HERE>";
            NSString *clientID = @"<INSERT-CLIENT-ID-HERE>";
            NSString *redirectURI = @"<INSERT-REDIRECT-URI-HERE>";

            ADAuthenticationError *error;
            ADAuthenticationContext *authContext = [ADAuthenticationContext authenticationContextWithAuthority:authority error:&error];
            NSURL *redirectUri = [[NSURL alloc]initWithString:redirectURI];

            [authContext acquireTokenWithResource:resourceURI clientId:clientID redirectUri:redirectUri completionBlock:^(ADAuthenticationResult *result) {
                if (result.tokenCacheStoreItem == nil)
                {
                    return;
                }
                else
                {
                    NSDictionary *payload = @{
                        @"access_token" : result.tokenCacheStoreItem.accessToken
                    };
                    [client loginWithProvider:@"windowsazureactivedirectory" token:payload completion:^(MSUser *user, NSError *error) {
                        [self refresh];
                    }];
                }
            }];
        }


6. Dans le code de la méthode `loginAndGetData` ci-dessus, remplacez **INSERT-AUTHORITY-HERE** par le nom du client dans lequel vous avez déployé votre application ; le format doit être ttps://login.windows.net/tenant-name.onmicrosoft.com. Cette valeur peut être copiée dans l'onglet Domaine de votre annuaire Azure Active Directory dans le [portail de gestion Azure].

7. Dans le code de la méthode `loginAndGetData` ci-dessus, remplacez **INSERT-RESOURCE-URI-HERE** par l'**URI ID d'application** de votre service mobile. Si vous avez suivi les instructions de la rubrique [Inscription auprès d'Azure Active Directory], votre URI ID d'application doit être semblable à https://todolist.azure-mobile.net/login/aad.

8. Dans le code de la méthode `loginAndGetData` ci-dessus, remplacez **INSERT-CLIENT-ID-HERE** par l'ID client que vous avez copié dans l'application cliente native.

9. Dans le code de la méthode `loginAndGetData` ci-dessus, remplacez **INSERT-REDIRECT-URI-HERE** par le point de terminaison /login/done de votre service mobile. Cette valeur doit ressembler à https://todolist.azure-mobile.net/login/done.


3. Dans le contrôleur QSTodoListViewController, modifiez `ViewDidLoad` en remplaçant `[self refresh]` par le code suivant :

        [self loginAndGetData];

## <a name="test-client"></a>Test du client à l'aide de l'authentification

1. Dans le menu Produit, cliquez sur Exécuter pour démarrer l'application.
2. Une invite s'affiche alors pour vous permettre de vous connecter à votre annuaire Azure Active Directory.  
3. L'application authentifie et renvoie les tâches à effectuer.

   ![](./media/mobile-services-dotnet-backend-ios-adal-sso-authentication/mobile-services-app-run.png)

<!-- Anchors. -->
[Inscription de votre service mobile auprès d'Azure Active Directory]: #register-mobile-service-aad
[Inscription de votre application auprès d'Azure Active Directory]: #register-app-aad
[Configuration du service mobile afin d'exiger une authentification]: #require-authentication
[Ajout du code d'authentification à l'application cliente]: #add-authentication-code
[Test du client à l'aide de l'authentification]: #test-client

<!-- URLs. -->
[Prise en main des données]: /fr-fr/documentation/articles/mobile-services-ios-get-started-data/
[Prise en main de Mobile Services]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-ios-get-started/
[Inscription auprès d'Azure Active Directory]: /fr-fr/documentation/articles/mobile-services-how-to-register-active-directory-authentication/
[Portail de gestion Azure]: https://manage.windowsazure.com/
[Bibliothèque d'authentification Active Directory pour iOS]: https://github.com/MSOpenTech/azure-activedirectory-library-for-ios


<!--HONumber=42-->
