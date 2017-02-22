---
title: "Bien démarrer avec Azure AD iOS | Microsoft Docs"
description: "Création d’une application iOS qui s’intègre avec Azure AD pour la connexion et appelle des API protégées par Azure AD en utilisant OAuth."
services: active-directory
documentationcenter: ios
author: xerners
manager: mbaldwin
editor: 
ms.assetid: 42303177-9566-48ed-8abb-279fcf1e6ddb
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 01/07/2017
ms.author: brandwe
translationtype: Human Translation
ms.sourcegitcommit: c579135f798ea0c2a5461fdd7c88244d2d6d78c6
ms.openlocfilehash: c74e63621d422f8fa13bc1dd2730ec2c3325a46a


---
# <a name="integrate-azure-ad-into-an-ios-app"></a>Intégration d’Azure AD dans une application iOS
[!INCLUDE [active-directory-devquickstarts-switcher](../../../includes/active-directory-devquickstarts-switcher.md)]

[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

Pour les clients iOS qui doivent accéder à des ressources protégées, Azure AD fournit la bibliothèque d’authentification Active Directory (ADAL).  Le seul objectif de cette bibliothèque ADAL est de faciliter l’obtention des jetons d’accès pour votre application.  Pour illustrer sa facilité d’utilisation, nous allons créer une application de liste des tâches Objective C qui effectue les actions suivantes :

* obtention de jetons d’accès pour appeler l’API Azure AD Graph à l’aide du [protocole d’authentification OAuth 2.0](https://msdn.microsoft.com/library/azure/dn645545.aspx);
* recherche, dans un répertoire, d’utilisateurs correspondant à un alias donné ;

Pour générer l’application fonctionnelle complète, vous devez :

1. inscrire votre application auprès d’Azure AD ;
2. installer et configurer la bibliothèque ADAL ;
3. utiliser la bibliothèque ADAL pour obtenir des jetons à partir d’Azure AD.

Pour commencer, téléchargez [la structure de l’application](https://github.com/AzureADQuickStarts/NativeClient-iOS/archive/skeleton.zip) ou [l’exemple terminé](https://github.com/AzureADQuickStarts/NativeClient-iOS/archive/complete.zip).  Vous avez également besoin d’un client Azure AD dans lequel vous pouvez créer des utilisateurs et inscrire une application.  Si vous ne disposez pas encore d’un client, [découvrez comment en obtenir un](active-directory-howto-tenant.md).

> [!TIP]
> Essayez la version préliminaire de notre nouveau [portail des développeurs](https://identity.microsoft.com/Docs/iOS) qui vous permettra de devenir opérationnel avec Azure Active Directory en quelques minutes !  Le portail des développeurs vous guidera tout au long du processus d’inscription d’une application et d’intégration d’Azure AD dans votre code.  Une fois terminé, vous disposerez d’une application simple qui peut authentifier les utilisateurs dans votre client et d’un serveur principal qui peut accepter les jetons et effectuer la validation. 
> 
> 

## <a name="1-determine-what-your-redirect-uri-will-be-for-ios"></a>1. Déterminez votre URI de redirection pour iOS
Afin de pouvoir lancer en toute sécurité vos applications dans certains scénarios d’authentification unique, vous devez créer un **URI de redirection** dans un format particulier. Un URI de redirection permet de garantir que les jetons sont renvoyés vers la bonne application qui les a appelés.

Le format iOS d’un URI de redirection est le suivant :

```
<app-scheme>://<bundle-id>
```

* **aap-scheme** : il est enregistré dans votre projet XCode. Cela permet aux autres applications de vous appeler. Vous trouverez cela sous Info.plist -> Types d’URL -> Identificateur d’URL. Vous devez en créer une si vous n’en avez pas encore au moins une configurée.
* **bundle-id** : il s’agit de l’identificateur d’offre groupée se trouvant sous « identité » dans les paramètres de votre projet XCode.

Voici un exemple de code de démarrage rapide : ***msquickstart://com.microsoft.azureactivedirectory.samples.graph.QuickStart***

## <a name="2-register-the-directorysearcher-application"></a>2. Inscription de l’application DirectorySearcher
Pour autoriser votre application à obtenir des jetons, vous devez tout d’abord l’enregistrer dans votre client Azure AD et lui accorder l’autorisation d’accéder à l’API Graph Azure AD :

1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Dans la barre supérieure, cliquez sur votre compte et, dans la liste **Répertoire**, choisissez le locataire Active Directory auprès duquel vous voulez inscrire votre application.
3. Cliquez sur **Autres services** dans le volet de navigation gauche et choisissez **Azure Active Directory**.
4. Cliquez sur **Inscriptions des applications**, puis sur **Ajouter**.
5. Suivez les invites et créez une **application cliente native**.
  * Le **nom** de l’application doit décrire votre application aux utilisateurs finaux.
  * L’ **URI de redirection** est une combinaison de schémas et de chaînes qu’Azure AD utilise pour renvoyer des réponses concernant les jetons.  Entrez une valeur spécifique de votre application en fonction des informations ci-dessus.
6. Une fois l’inscription terminée, AAD affecte un ID d’application unique à votre application.  Copiez cette valeur dans l’onglet de l’application, car vous en aurez besoin dans les sections suivantes.
7. Sur la page **Paramètres**, choisissez **Autorisations requises** et **Ajouter**. Sélectionnez l’API **Microsoft Graph** et ajoutez l’autorisation **Lire les données de l’annuaire** sous **Autorisations déléguées**.  Cela permet à votre application d’interroger l’API Graph concernant les utilisateurs.

## <a name="3-install--configure-adal"></a>3. Installez et configurez ADAL
Maintenant que vous disposez d’une application dans Azure AD, vous pouvez installer la bibliothèque ADAL et écrire votre code lié à l’identité.  Pour permettre à ADAL de communiquer avec Azure AD, vous devez lui fournir des informations sur l’inscription de votre application.

* Commencez par ajouter la bibliothèque ADAL au projet DirectorySearcher à l’aide de Cocapods.

```
$ vi Podfile
```
Ajoutez le code suivant à ce podfile :

```
source 'https://github.com/CocoaPods/Specs.git'
link_with ['QuickStart']
xcodeproj 'QuickStart'

pod 'ADALiOS'
```

Chargez maintenant le podfile à l’aide de Cocoapods. Cette opération crée un nouvel espace de travail XCode que vous allez charger.

```
$ pod install
...
$ open QuickStart.xcworkspace
```

* Dans le projet de démarrage rapide, ouvrez le fichier plist `settings.plist`.  Remplacez les valeurs des éléments de la section afin qu’elles reflètent les valeurs que vous avez saisies dans le portail Azure.  Votre code se réfère à ces valeurs chaque fois qu’il utilise la bibliothèque ADAL.
  * `tenant` est le domaine de votre client Azure AD, par exemple, contoso.onmicrosoft.com.
  * `clientId` est l’ID client de votre application que vous avez copié à partir du portail.
  * `redirectUri` est l’URL de redirection que vous avez inscrite dans le portail.

## <a name="4----use-adal-to-get-tokens-from-aad"></a>4.    Utilisation de la bibliothèque ADAL pour obtenir des jetons à partir d’AAD
Le principe de base de la bibliothèque ADAL consiste simplement à appeler un completionBlock `+(void) getToken : ` chaque fois que votre application a besoin d’un jeton d’accès, et la bibliothèque ADAL s’occupe du reste.  

* Dans le projet `QuickStart`, ouvrez `GraphAPICaller.m` et recherchez le commentaire `// TODO: getToken for generic Web API flows. Returns a token with no additional parameters provided.` vers le haut.  C’est à ce moment-là que vous fournissez à la bibliothèque ADAL, à l’aide d’une méthode CompletionBlock, les coordonnées dont elle a besoin pour communiquer avec Azure AD et que vous lui indiquez comment mettre en cache des jetons.

```ObjC
+(void) getToken : (BOOL) clearCache
           parent:(UIViewController*) parent
completionHandler:(void (^) (NSString*, NSError*))completionBlock;
{
    AppData* data = [AppData getInstance];
    if(data.userItem){
        completionBlock(data.userItem.accessToken, nil);
        return;
    }

    ADAuthenticationError *error;
    authContext = [ADAuthenticationContext authenticationContextWithAuthority:data.authority error:&error];
    authContext.parentController = parent;
    NSURL *redirectUri = [[NSURL alloc]initWithString:data.redirectUriString];

    [ADAuthenticationSettings sharedInstance].enableFullScreen = YES;
    [authContext acquireTokenWithResource:data.resourceId
                                 clientId:data.clientId
                              redirectUri:redirectUri
                           promptBehavior:AD_PROMPT_AUTO
                                   userId:data.userItem.userInformation.userId
                     extraQueryParameters: @"nux=1" // if this strikes you as strange it was legacy to display the correct mobile UX. You most likely won't need it in your code.
                          completionBlock:^(ADAuthenticationResult *result) {

                              if (result.status != AD_SUCCEEDED)
                              {
                                  completionBlock(nil, result.error);
                              }
                              else
                              {
                                  data.userItem = result.tokenCacheStoreItem;
                                  completionBlock(result.tokenCacheStoreItem.accessToken, nil);
                              }
                          }];
}

```

* Nous devons à présent utiliser ce jeton pour rechercher des utilisateurs dans le graphique. La méthode commentThis `// TODO: implement SearchUsersList` effectue une demande GET auprès de l’API Graph Azure AD pour l’interroger à propos d’utilisateurs dont l’UPN commence par le terme de recherche donné.  Cependant, pour interroger l’API Graph, vous devez inclure un jeton d’accès (access_token) dans l’en-tête `Authorization` de la demande ; c’est à ce moment qu’intervient la bibliothèque ADAL.

```ObjC
+(void) searchUserList:(NSString*)searchString
                parent:(UIViewController*) parent
       completionBlock:(void (^) (NSMutableArray* Users, NSError* error)) completionBlock
{
    if (!loadedApplicationSettings)
    {
        [self readApplicationSettings];
    }

    AppData* data = [AppData getInstance];

    NSString *graphURL = [NSString stringWithFormat:@"%@%@/users?api-version=%@&$filter=startswith(userPrincipalName, '%@')", data.taskWebApiUrlString, data.tenant, data.apiversion, searchString];


    [self craftRequest:[self.class trimString:graphURL]
                parent:parent
     completionHandler:^(NSMutableURLRequest *request, NSError *error) {

         if (error != nil)
         {
             completionBlock(nil, error);
         }
         else
         {

             NSOperationQueue *queue = [[NSOperationQueue alloc]init];

             [NSURLConnection sendAsynchronousRequest:request queue:queue completionHandler:^(NSURLResponse *response, NSData *data, NSError *error) {

                 if (error == nil && data != nil){

                     NSDictionary *dataReturned = [NSJSONSerialization JSONObjectWithData:data options:0 error:nil];

                     // We can grab the top most JSON node to get our graph data.
                     NSArray *graphDataArray = [dataReturned objectForKey:@"value"];

                     // Don't be thrown off by the key name being "value". It really is the name of the
                     // first node. :-)

                     //each object is a key value pair
                     NSDictionary *keyValuePairs;
                     NSMutableArray* Users = [[NSMutableArray alloc]init];

                     for(int i =0; i < graphDataArray.count; i++)
                     {
                         keyValuePairs = [graphDataArray objectAtIndex:i];

                         User *s = [[User alloc]init];
                         s.upn = [keyValuePairs valueForKey:@"userPrincipalName"];
                         s.name =[keyValuePairs valueForKey:@"givenName"];

                         [Users addObject:s];
                     }

                     completionBlock(Users, nil);
                 }
                 else
                 {
                     completionBlock(nil, error);
                 }

             }];
         }
     }];

}

```
* Lorsque votre application demande un jeton en appelant `getToken(...)`, la bibliothèque ADAL tente de renvoyer un jeton sans demander à l’utilisateur ses informations d’identification.  Si la bibliothèque ADAL détermine que l’utilisateur doit se connecter pour obtenir un jeton, elle affiche une boîte de dialogue de connexion, récupère les informations d’identification de l’utilisateur et renvoie un jeton après une authentification réussie.  Si la bibliothèque ADAL ne peut pas renvoyer un jeton pour une raison quelconque, `AdalException`est renvoyé.
* Notez que l’objet `AuthenticationResult` contient un objet `tokenCacheStoreItem` qui peut être utilisé pour collecter des informations dont votre application peut avoir besoin.  Dans le guide de démarrage rapide, `tokenCacheStoreItem` est utilisé pour déterminer si authentification a déjà eu lieu.

## <a name="5-build-and-run-the-application"></a>5. Génération et exécution de l’application
Félicitations ! Vous disposez désormais d’une application iOS fonctionnelle capable d’authentifier les utilisateurs, d’appeler en toute sécurité les API web à l’aide d’OAuth 2.0 et d’obtenir des informations de base concernant l’utilisateur.  Si vous ne l’avez pas encore fait, il est maintenant temps de remplir votre client avec quelques utilisateurs.  Exécutez votre application de démarrage rapide et connectez-vous à l’aide d’un de ces utilisateurs.  Recherchez d’autres utilisateurs en fonction de leur UPN.  Fermez l’application et exécutez-la de nouveau.  Observez que la session utilisateur reste identique.

La bibliothèque ADAL facilite l’intégration de toutes ces fonctionnalités d’identité communes dans votre application.  Elle effectue les tâches ingrates pour vous : gestion du cache, prise en charge du protocole OAuth, présentation d’une interface utilisateur de connexion à l’utilisateur, actualisation des jetons expirés et bien plus encore.  La seule chose que vous devez vraiment connaître est un appel unique d’API : `getToken`.

Pour référence, l’exemple terminé (sans vos valeurs de configuration) est fourni [ici](https://github.com/AzureADQuickStarts/NativeClient-iOS/archive/complete.zip).  

## <a name="additional-scenarios"></a>Autres cas de figure
Vous pouvez à présent aborder d’autres scénarios.  Par exemple :

* [Sécurisation d’une API web Node.js avec Azure AD](active-directory-devquickstarts-webapi-nodejs.md)
* En savoir plus sur l’ [Activation de l’authentification unique entre applications sur iOS à l’aide de la bibliothèque ADAL](active-directory-sso-ios.md)  

[!INCLUDE [active-directory-devquickstarts-additional-resources](../../../includes/active-directory-devquickstarts-additional-resources.md)]




<!--HONumber=Jan17_HO3-->


