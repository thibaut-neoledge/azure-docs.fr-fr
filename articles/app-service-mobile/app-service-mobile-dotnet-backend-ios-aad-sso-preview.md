<properties
        pageTitle="Authentifier les utilisateurs de votre application iOS à l’aide de l’authentification Azure Active Directory"
        description="Découvrez comment connecter des utilisateurs dans votre application iOS à l'aide de la bibliothèque d'authentification Active Directory."
        documentationCenter="Mobile"
        authors="mattchenderson"
        services="app-service\mobile"
        manager="dwrede" />

<tags ms.service="app-service-mobile"
ms.workload="mobile"
ms.tgt_pltfrm="mobile-ios"
ms.devlang="objective-c"
ms.topic="article"
ms.date="09/14/2015"
ms.author="mahender" />

# Ajouter l’authentification Azure Active Directory à votre application iOS

[AZURE.INCLUDE [app-service-mobile-selector-aad-sso](../../includes/app-service-mobile-selector-aad-sso.md)]

[AZURE.INCLUDE [app-service-mobile-note-mobile-services-preview](../../includes/app-service-mobile-note-mobile-services-preview.md)]

Dans ce didacticiel, vous allez ajouter le processus d'authentification au projet de démarrage rapide à l'aide de la bibliothèque d'authentification Active Directory (ADAL). Vous pouvez également activer l'authentification avec moins de configuration en utilisant le kit de développement logiciel (SDK) Mobile Apps, comme indiqué dans le didacticiel [Ajout d’une authentification à votre application]. L’utilisation de cette rubrique sur ADAL fournit une expérience d'authentification plus intégrée pour les utilisateurs finaux. De plus, ADAL offre des capacités plus riches pour l'accès à d'autres ressources protégées AAD.

Pour qu'il soit possible d'authentifier les utilisateurs avec ADAL, vous devez inscrire votre application auprès de votre client Azure Active Directory (AAD). Cela se déroule en deux étapes : Vous devez d'abord inscrire votre service App Service et exposer les autorisations sur celui-ci. Vous devez ensuite inscrire votre application iOS et lui accorder l'accès à ces autorisations.

Ce didacticiel requiert les éléments suivants :

* XCode 4.5 et iOS 6.0 (ou versions ultérieures)
* Exécution du didacticiel [Prise en main des applications mobiles]
* Kit de développement logiciel (SDK) de Microsoft Azure Mobile Services
* [Bibliothèque d'authentification Active Directory pour iOS]

##<a name="review"></a>Examiner la configuration de votre projet de serveur (facultatif)

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-enable-auth-preview](../../includes/app-service-mobile-dotnet-backend-enable-auth-preview.md)]

## <a name="register-application"></a>Inscription de votre application auprès d’Azure Active Directory

[AZURE.INCLUDE [app-service-mobile-adal-register-app](../../includes/app-service-mobile-adal-register-app.md)]

## <a name="require-authentication"></a>Configuration de l’application afin d’exiger une authentification

[AZURE.INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

## <a name="add-adal"></a>Ajout d’une référence à la bibliothèque d’authentification Active Directory

1. Téléchargez la [bibliothèque d’authentification Active Directory pour iOS].

2. Dans Xcode Navigator, sélectionnez votre projet, puis cliquez sur **File** et choisissez **Add Files to...**. Accédez à l’emplacement où vous avez téléchargé la bibliothèque et sélectionnez **ADALiOS.xcodeproj**.

3. Sélectionnez à nouveau votre projet et ouvrez l’onglet **Build Settings**. Accédez à la section **Linking** et ajoutez `-ObjC` à **Other Linker Flags**.

4. Sélectionnez l’onglet **Build Phases**. Sous **Target Dependencies**, ajoutez `ADALiOS`.

5. Sous **Link Binary With Libraries**, ajoutez `libADALiOS.a`.

Vous pourrez désormais faire référence à la bibliothèque d'authentification Active Directory dans votre projet.

## <a name="add-authentication-code"></a>Ajout du code d’authentification à l’application cliente

2. Dans le contrôleur QSTodoListViewController, incluez ADAL avec le code suivant :

        #import "ADALiOS/ADAuthenticationContext.h"

3. Ajoutez ensuite la méthode suivante :

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

4. Dans le code de la méthode `loginAndGetData` ci-dessus, remplacez **INSERT-AUTHORITY-HERE** par le nom du client dans lequel vous avez déployé votre application ; le format doit être https://login.windows.net/tenant-name.onmicrosoft.com. Cette valeur peut être copiée dans l'onglet Domaine de votre annuaire Azure Active Directory dans le [portail de gestion Azure].

5. Dans le code de la méthode `loginAndGetData`, remplacez **INSERT-RESOURCE-URI-HERE** par l’**URI ID d’application** de votre application mobile. Si vous avez suivi les instructions de la rubrique [Configuration de votre application mobile avec Azure Active Directory], votre URI ID d’application doit être semblable à https://contosogateway.azurewebsites.net/login/aad.

6. Dans le code de la méthode `loginAndGetData` ci-dessus, remplacez **INSERT-CLIENT-ID-HERE** par l’ID client que vous avez copié dans l’application cliente native.

7. Dans le code de la méthode `loginAndGetData` ci-dessus, remplacez **INSERT-REDIRECT-URI-HERE** par le point de terminaison /login/done de votre passerelle App Service. Cette valeur doit être semblable à https://contosogateway.azurewebsites.net/login/done.

8. Dans le contrôleur QSTodoListViewController, modifiez `viewDidLoad` en remplaçant `[self refresh]` par le code suivant :

        [self loginAndGetData];

## <a name="test-client"></a>Test du client à l’aide de l’authentification

1. Dans le menu Produit, cliquez sur Exécuter pour démarrer l'application.
2. Une invite s'affiche alors pour vous permettre de vous connecter à votre annuaire Azure Active Directory.  
3. L'application authentifie et renvoie les tâches à effectuer.

<!-- URLs. -->
[Configuration de votre application mobile avec Azure Active Directory]: app-service-mobile-how-to-configure-active-directory-authentication-preview.md
[portail de gestion Azure]: https://manage.windowsazure.com/
[Bibliothèque d'authentification Active Directory pour iOS]: https://github.com/MSOpenTech/azure-activedirectory-library-for-ios
[bibliothèque d’authentification Active Directory pour iOS]: https://github.com/MSOpenTech/azure-activedirectory-library-for-ios
 [Prise en main des applications mobiles]: app-service-mobile-dotnet-backend-ios-get-started-preview.md
 [Ajout d’une authentification à votre application]: app-service-mobile-dotnet-backend-ios-get-started-users-preview.md

<!---HONumber=Oct15_HO2-->