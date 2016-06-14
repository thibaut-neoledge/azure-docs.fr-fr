<properties
	pageTitle="Version préliminaire d’Azure Active Directory B2C : appel d’une API web depuis une application iOS | Microsoft Azure"
	description="Cet article vous explique comment créer une application iOS de type « liste de tâches » qui appelle une API web Node.js en utilisant les jetons du porteur OAuth 2.0. L’application iOS et l’API web utilisent toutes les deux Azure Active Directory B2C pour gérer les identités des utilisateurs et les authentifier."
	services="active-directory-b2c"
	documentationCenter="ios"
	authors="brandwe"
	manager="mbaldwin"
	editor=""/>

<tags ms.service="active-directory-b2c" ms.workload="identity" ms.tgt_pltfrm="na" ms.devlang="objectivec" ms.topic="hero-article"

	ms.date="05/31/2016"
	ms.author="brandwe"/>

# Version préliminaire d’Azure AD B2C : appel d’une API web depuis une application iOS

<!-- TODO [AZURE.INCLUDE [active-directory-b2c-devquickstarts-web-switcher](../../includes/active-directory-b2c-devquickstarts-web-switcher.md)]-->

Avec Azure Active Directory (Azure AD) B2C, vous pouvez ajouter de puissantes fonctionnalités de gestion des identités en libre-service à vos applications iOS et API web, en seulement quelques étapes. Cet article vous explique comment créer une application iOS de type « liste des tâches » qui appelle une API web Node.js en utilisant les jetons du porteur OAuth 2.0. L’application iOS et l’API web utilisent toutes les deux Azure AD B2C pour gérer les identités des utilisateurs et les authentifier.

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

> [AZURE.NOTE]
	Pour fonctionner correctement, ce démarrage rapide nécessite que vous disposiez déjà d’une API web protégée par Azure AD B2C. Nous en avons créé une pour .Net et Node.js afin que vous puissiez l’utiliser. Cette procédure pas à pas suppose que l’exemple d’API web Node.js est configuré. Pour en savoir plus, voir l’[exemple d’API web Azure Active Directory pour Node.js](active-directory-b2c-devquickstarts-api-node.md).

> [AZURE.NOTE]
	Cet article ne couvre pas l’implémentation de la connexion, de l’inscription et de la gestion de profil avec Azure AD B2C. Il s’intéresse principalement à l’appel des API web après l’authentification de l’utilisateur. Si ce n’est pas déjà fait, commencez par consulter le [didacticiel de prise en main de l’application web .NET](active-directory-b2c-devquickstarts-web-dotnet.md) pour en savoir plus sur les principes fondamentaux d’Azure AD B2C.

## Obtention d'un répertoire Azure AD B2C

Avant de pouvoir utiliser Azure AD B2C, vous devez créer un répertoire ou un client. Un répertoire est un conteneur destiné à recevoir tous vos utilisateurs, applications, groupes, etc. Si vous n’en possédez pas déjà un, [créez un répertoire B2C](active-directory-b2c-get-started.md) avant de continuer.

## Création d'une application

Vous devez maintenant créer dans votre répertoire B2C une application fournissant à Azure AD les informations nécessaires pour communiquer avec votre application en toute sécurité. L’application et l’API web sont alors toutes les deux représentées par un seul **ID d’application** car elles constituent une application logique. Pour créer une application, suivez [ces instructions](active-directory-b2c-app-registration.md). Veillez à effectuer les opérations suivantes :

- Incluez une **application web/API web** dans l’application.
- Entrer `http://localhost:3000/auth/openid/return` comme **URL de réponse**. Il s’agit de l’URL par défaut pour cet exemple de code.
- Créez une **clé secrète d’application** pour votre application et copiez-la. Vous en aurez besoin ultérieurement.
- Copiez l’**ID d’application** affecté à votre application. Vous en aurez besoin ultérieurement.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## Création de vos stratégies

Dans Azure AD B2C, chaque expérience utilisateur est définie par une [stratégie](active-directory-b2c-reference-policies.md). Cette application contient trois expériences liées à l’identité : l’inscription, la connexion et la connexion avec Facebook. Vous devez créer une stratégie pour chaque type, comme décrit dans l’[article de référence sur les stratégies](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy). Lors de la création des trois stratégies, assurez-vous de :

- Choisissez le **nom d’affichage** et les attributs d’inscription dans votre stratégie d’inscription.
- Choisissez les revendications **nom d'affichage** et **ID objet** comme revendications d'application pour chaque stratégie. Vous pouvez aussi choisir d'autres revendications.
- Copiez le **nom** de chaque stratégie après sa création. Il doit porter le préfixe `b2c_1_`. Vous aurez besoin des noms de ces stratégies ultérieurement.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Une fois vos 3 stratégies créées, vous pouvez générer votre application.

Remarque : cet article n’explique pas comment utiliser les stratégies que vous venez de créer. Pour en savoir plus sur le fonctionnement des stratégies dans Azure AD B2C, commencez par consulter le [didacticiel de prise en main des applications web .NET](active-directory-b2c-devquickstarts-web-dotnet.md).

## Téléchargement du code

Le code associé à ce didacticiel [est stocké sur GitHub](https://github.com/AzureADQuickStarts/B2C-NativeClient-iOS). Pour générer l’exemple à mesure que vous avancez, vous pouvez [télécharger la structure de projet sous la forme d’un fichier .zip](https://github.com/AzureADQuickStarts/B2C-NativeClient-iOS/archive/skeleton.zip). Vous pouvez également cloner la structure :

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-NativeClient-iOS.git
```

> [AZURE.NOTE] **Vous devez télécharger la structure pour suivre ce didacticiel.** En raison de la complexité de l’implémentation d’une application entièrement fonctionnelle sur iOS, la **structure** dispose d’un code d’expérience utilisateur qui s’exécute une fois que vous avez terminé le didacticiel. Cette mesure fait gagner du temps au développeur. Le code de l’expérience utilisateur est sans rapport avec la rubrique d’ajout de B2C à une application iOS.

L’application terminée est également [disponible en tant que fichier .zip](https://github.com/AzureADQuickStarts/B2C-NativeClient-iOS/archive/complete.zip) ou sur la branche `complete` du même référentiel.

Ensuite, chargez `podfile` à l’aide de CocoaPods. Cette opération crée un nouvel espace de travail Xcode que vous allez charger. Si vous n’avez pas CocoaPods, [visitez le site web pour l’installer](https://cocoapods.org).

```
$ pod install
...
$ open Microsoft Tasks for Consumers.xcworkspace
```

## Configuration de l'application de la tâche iOS

Afin que l’application de la tâche iOS communique avec Azure AD B2C, vous devez renseigner certains paramètres courants. Dans le dossier `Microsoft Tasks`, ouvrez le fichier `settings.plist` qui se trouve à la racine du projet, puis remplacez les valeurs dans la section `<dict>`. Ces valeurs seront utilisées dans l'application.

```
<dict>
	<key>authority</key>
	<string>https://login.microsoftonline.com/<your tenant name>.onmicrosoft.com/</string>
	<key>clientId</key>
	<string><Enter the Application Id assigned to your app by the Azure portal, e.g.580e250c-8f26-49d0-bee8-1c078add1609></string>
	<key>scopes</key>
	<array>
		<string><Enter the Application Id assigned to your app by the Azure portal, e.g.580e250c-8f26-49d0-bee8-1c078add1609></string>
	</array>
	<key>additionalScopes</key>
	<array>
	</array>
	<key>redirectUri</key>
	<string>urn:ietf:wg:oauth:2.0:oob</string>
	<key>taskWebAPI</key>
	<string>http://localhost/tasks:3000</string>
	<key>emailSignUpPolicyId</key>
	<string><Enter your sign up policy name, e.g.g b2c_1_sign_up></string>
	<key>faceBookSignInPolicyId</key>
	<string><your sign in policy for FB></string>
	<key>emailSignInPolicyId</key>
	<string><Enter your sign in policy name, e.g. b2c_1_sign_in></string>
	<key>fullScreen</key>
	<false/>
	<key>showClaims</key>
	<true/>
</dict>
</plist>
```

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

## Obtention des jetons d'accès et appel de l'API de tâche

Cette section démontre comment procéder à un échange de jeton OAuth 2.0 dans une application web à l’aide des bibliothèques et des infrastructures de Microsoft. Si vous n’êtes pas familiarisé avec les codes d’autorisation et les jetons d’accès, nous vous conseillons de consulter les [informations de référence sur le protocole OAuth 2.0](active-directory-b2c-reference-protocols.md).

### Création de fichiers d’en-tête à l’aide de méthodes

Pour obtenir un jeton avec la stratégie sélectionnée, puis appeler le serveur de la tâche, vous avez besoin de méthodes. Il vous faut les définir.

Créez un fichier appelé `samplesWebAPIConnector.h` sous `/Microsoft Tasks` dans votre projet Xcode.

Ajoutez le code suivant pour définir ce que vous devez faire :

```
#import <Foundation/Foundation.h>
#import "samplesTaskItem.h"
#import "samplesPolicyData.h"
#import "ADALiOS/ADAuthenticationContext.h"

@interface samplesWebAPIConnector : NSObject<NSURLConnectionDataDelegate>

+(void) getTaskList:(void (^) (NSArray*, NSError* error))completionBlock
             parent:(UIViewController*) parent;

+(void) addTask:(samplesTaskItem*)task
         parent:(UIViewController*) parent
completionBlock:(void (^) (bool, NSError* error)) completionBlock;

+(void) deleteTask:(samplesTaskItem*)task
            parent:(UIViewController*) parent
   completionBlock:(void (^) (bool, NSError* error)) completionBlock;

+(void) doPolicy:(samplesPolicyData*)policy
         parent:(UIViewController*) parent
completionBlock:(void (^) (ADProfileInfo* userInfo, NSError* error)) completionBlock;

+(void) signOut;

@end
```

Il s’agit de simples opérations de création, de lecture, de mise à jour et de suppression (CRUD) sur votre API, ainsi que d’une méthode `doPolicy`. À l’aide de cette méthode, vous pouvez obtenir un jeton avec la stratégie souhaitée.

Notez que deux autres fichiers d’en-tête doivent être définis. Ils vont contenir l’élément de votre tâche et les données de la stratégie. Créez-les maintenant :

Créez le fichier `samplesTaskItem.h` en utilisant le code suivant :

```
#import <Foundation/Foundation.h>

@interface samplesTaskItem : NSObject

@property NSString *itemName;
@property NSString *ownerName;
@property BOOL completed;
@property (readonly) NSDate *creationDate;

@end
```

Créez également le fichier `samplesPolicyData.h` qui va contenir les données de votre stratégie :

```
#import <Foundation/Foundation.h>

@interface samplesPolicyData : NSObject

@property (strong) NSString* policyName;
@property (strong) NSString* policyID;

+(id) getInstance;

@end
```
### Ajout d’une implémentation pour les éléments de tâche et de stratégie

Maintenant que vos fichiers d’en-tête sont en place, vous pouvez écrire du code pour stocker les données que vous allez utiliser dans votre exemple.

Créez le fichier `samplesPolicyData.m` en utilisant le code suivant :

```
#import <Foundation/Foundation.h>
#import "samplesPolicyData.h"

@implementation samplesPolicyData

+(id) getInstance
{
    static samplesPolicyData *instance = nil;
    static dispatch_once_t onceToken;

    dispatch_once(&onceToken, ^{
        instance = [[self alloc] init];
        NSDictionary *dictionary = [NSDictionary dictionaryWithContentsOfFile:[[NSBundle mainBundle] pathForResource:@"settings" ofType:@"plist"]];
        instance.policyName = [dictionary objectForKey:@"policyName"];
        instance.policyID = [dictionary objectForKey:@"policyID"];


    });

    return instance;
}


@end
```

### Écriture du code de configuration pour l’appel dans la bibliothèque ADAL pour iOS

Le code rapide permettant de stocker les objets de l’interface utilisateur est maintenant terminé. Ensuite, vous devez écrire le code permettant d’accéder à l’Active Directory Authentication Library (ADAL) pour iOS en utilisant les paramètres que vous avez placés dans `settings.plist`. Cela vous permet d’obtenir un jeton d’accès à transmettre au serveur de la tâche.

Tout votre travail est effectué dans `samplesWebAPIConnector.m`.

Commencez par implémenter le code `doPolicy()` que vous avez écrit dans le fichier d’en-tête `samplesWebAPIConnector.h` :

```
+(void) doPolicy:(samplesPolicyData *)policy
         parent:(UIViewController*) parent
completionBlock:(void (^) (ADProfileInfo* userInfo, NSError* error)) completionBlock
{
    if (!loadedApplicationSettings)
    {
        [self readApplicationSettings];
    }

    [self getClaimsWithPolicyClearingCache:NO policy:policy params:nil parent:parent completionHandler:^(ADProfileInfo* userInfo, NSError* error) {

        if (userInfo == nil)
        {
            completionBlock(nil, error);
        }

        else {

            completionBlock(userInfo, nil);
        }
    }];

}


```

La méthode est simple. L’objet `samplesPolicyData` que vous avez créé est pris comme entrées, le `ViewController` parent et un rappel. Le rappel est intéressant, et nous allons examiner cela plus en détail.

- Notez que `completionBlock` a pour type `ADProfileInfo` et que ce type est renvoyé à l’aide d’un objet `userInfo`. `ADProfileInfo` est le type qui contient toutes les réponses du serveur, notamment les revendications.
- Notez également `readApplicationSettings`. Cela permet de lire les données fournies dans `settings.plist`.
- Enfin, vous disposez d’une méthode `getClaimsWithPolicyClearingCache` relativement volumineuse. Il s’agit de l’appel réel à la bibliothèque ADAL pour iOS que vous devez écrire. Nous allons revenir sur ce point ultérieurement.

Ensuite, écrivez votre méthode `getClaimsWithPolicyClearingCache` volumineuse. Elle l’est suffisamment pour mériter sa propre section.

### Création de l’appel à la bibliothèque ADAL pour iOS

Après avoir téléchargé la structure depuis GitHub, vous constatez que nous disposons déjà de plusieurs appels pour nous aider avec l’exemple d’application. Ils suivent tous le modèle suivant : `get(Claims|Token)With<verb>ClearningCache`. Ils se lisent presque normalement en utilisant des conventions Objective-C. Par exemple, « Obtenir un jeton avec des paramètres supplémentaires que je fournis et effacer le cache » équivaut à `getTokenWithExtraParamsClearingCache()`.

Vous écrivez « Obtenir des revendications et un jeton avec la stratégie que je fournis et ne pas effacer le cache » ou `getClaimsWithPolicyClearingCache`. Vous obtenez toujours un jeton à partir de la bibliothèque ADAL. Il n’est donc pas nécessaire de spécifier « revendications et un jeton » dans la méthode. Cependant, vous voulez parfois simplement le jeton sans devoir analyser les revendications. Nous vous proposons donc également dans la structure une méthode sans revendications appelée `getTokenWithPolicyClearingCache`.

Écrivez ce code :

```
+(void) getClaimsWithPolicyClearingCache  : (BOOL) clearCache
                           policy:(samplesPolicyData *)policy
                           params:(NSDictionary*) params
                           parent:(UIViewController*) parent
                completionHandler:(void (^) (ADProfileInfo*, NSError*))completionBlock;
{
    SamplesApplicationData* data = [SamplesApplicationData getInstance];


    ADAuthenticationError *error;
    authContext = [ADAuthenticationContext authenticationContextWithAuthority:data.authority error:&error];
    authContext.parentController = parent;
    NSURL *redirectUri = [[NSURL alloc]initWithString:data.redirectUriString];

    if(!data.correlationId ||
       [[data.correlationId stringByTrimmingCharactersInSet:[NSCharacterSet whitespaceAndNewlineCharacterSet]] length] == 0)
    {
        authContext.correlationId = [[NSUUID alloc] initWithUUIDString:data.correlationId];
    }

    [ADAuthenticationSettings sharedInstance].enableFullScreen = data.fullScreen;
    [authContext acquireTokenWithScopes:data.scopes
                      additionalScopes: data.additionalScopes
                              clientId:data.clientId
                           redirectUri:redirectUri
                            identifier:[ADUserIdentifier identifierWithId:data.userItem.profileInfo.username type:RequiredDisplayableId]
                            promptBehavior:AD_PROMPT_ALWAYS
                  extraQueryParameters: params.urlEncodedString
                                policy: policy.policyID
                       completionBlock:^(ADAuthenticationResult *result) {

                           if (result.status != AD_SUCCEEDED)
                           {
                               completionBlock(nil, result.error);
                           }                              else
                              {
                                  data.userItem = result.tokenCacheStoreItem;
                                  completionBlock(result.tokenCacheStoreItem.profileInfo, nil);
                              }
                          }];
}


```

La première partie doit vous sembler familière.

- Chargez les paramètres fournis dans `settings.plist` et affectez-les à `data`.
- Configurez le paramètre `ADAuthenticationError` qui accepte toutes les erreurs provenant de la bibliothèque ADAL pour iOS.
- Créez le paramètre `authContext` qui définit votre appel à la bibliothèque ADAL. Vous lui transmettez votre autorité pour commencer.
- Donnez au paramètre `authContext` une référence au contrôleur parent, pour pouvoir retourner à celui-ci.
- Convertissez `redirectURI` (qui était auparavant une chaîne dans `settings.plist`) en type NSURL qui correspond à ce qu’attend la bibliothèque ADAL.
- Configurez `correlationId`. Il s’agit d’un UUID qui peut suivre l’appel sur le client vers le serveur et vice versa. Cela est utile pour le débogage.

Ensuite, vous obtenez l’appel réel à la bibliothèque ADAL. C’est à ce moment-là que l’appel diffère de ce à quoi vous pourriez vous attendre avec les utilisations précédentes de la bibliothèque ADAL pour iOS :

```
[authContext acquireTokenWithScopes:data.scopes
                      additionalScopes: data.additionalScopes
                              clientId:data.clientId
                           redirectUri:redirectUri
                            identifier:[ADUserIdentifier identifierWithId:data.userItem.profileInfo.username type:RequiredDisplayableId]
                            promptBehavior:AD_PROMPT_ALWAYS
                  extraQueryParameters: params.urlEncodedString
                                policy: policy.policyID
                       completionBlock:^(ADAuthenticationResult *result) {

```

Vous pouvez voir ici que l’appel est assez simple.

`scopes` : les étendues que vous transmettez au serveur et que vous souhaitez demander depuis le serveur pour un utilisateur qui se connecte. Pour la version préliminaire de B2C, transmettez `client_id`. Toutefois, cela est susceptible d’être modifié à l’avenir pour la lecture des étendues. Nous prévoyons de mettre à jour ce document à ce moment-là. `additionalScopes` : étendues supplémentaires à utiliser éventuellement pour votre application. Elles sont censées être utilisées à l’avenir. `clientId` : ID d’application obtenu depuis le portail. `redirectURI` : URI de redirection sur lequel le jeton devrait être publié. `identifier` : moyen d’identifier un utilisateur pour voir s’il existe un jeton utilisable dans le cache. Cela évite de toujours devoir demander un autre jeton au serveur. Vous retrouvez ce paramètre dans un type appelé `ADUserIdentifier`, et vous pouvez indiquer ce que vous voulez utiliser en tant qu’ID. Il est conseillé d’utiliser `username`. `promptBehavior` : ce paramètre est déconseillé. Il est remplacé par `AD_PROMPT_ALWAYS`. `extraQueryParameters` : tout ce que vous voulez transférer en plus au serveur dans un format encodé URL. `policy` : stratégie appelée. C’est la partie la plus importante de cette procédure pas à pas.

Vous pouvez voir dans `completionBlock` que vous transférez `ADAuthenticationResult`. Ce paramètre contient vos informations de jeton et de profil (si l’appel a abouti).

À l’aide du code ci-dessus, vous pouvez obtenir un jeton pour la stratégie que vous fournissez. Vous pouvez ensuite utiliser ce jeton pour appeler l’API.

### Création des appels de tâche au serveur

Maintenant que vous disposez d’un jeton, vous devez le fournir à votre API pour autorisation.

Trois méthodes doivent être implémentées :

```
+(void) getTaskList:(void (^) (NSArray*, NSError* error))completionBlock
             parent:(UIViewController*) parent;

+(void) addTask:(samplesTaskItem*)task
         parent:(UIViewController*) parent
completionBlock:(void (^) (bool, NSError* error)) completionBlock;

+(void) deleteTask:(samplesTaskItem*)task
            parent:(UIViewController*) parent
   completionBlock:(void (^) (bool, NSError* error)) completionBlock;
```

`getTasksList` fournit un tableau qui représente les tâches sur votre serveur. `addTask` et `deleteTask` effectuent les actions suivantes et renvoient `true` ou `false` en cas de réussite.

Pour commencer, écrivez `getTaskList` :

```

+(void) getTaskList:(void (^) (NSArray*, NSError*))completionBlock
             parent:(UIViewController*) parent;
{
    if (!loadedApplicationSettings)
    {
        [self readApplicationSettings];
    }

    SamplesApplicationData* data = [SamplesApplicationData getInstance];

    [self craftRequest:[self.class trimString:data.taskWebApiUrlString]
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

                    NSArray *tasks = [NSJSONSerialization JSONObjectWithData:data options:0 error:nil];

                    //each object is a key value pair
                    NSDictionary *keyValuePairs;
                    NSMutableArray* sampleTaskItems = [[NSMutableArray alloc]init];

                    for(int i =0; i < tasks.count; i++)
                    {
                        keyValuePairs = [tasks objectAtIndex:i];

                        samplesTaskItem *s = [[samplesTaskItem alloc]init];
                        s.itemName = [keyValuePairs valueForKey:@"task"];

                        [sampleTaskItems addObject:s];
                    }

                    completionBlock(sampleTaskItems, nil);
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

Tout ce qui concerne le code de tâche dépasse le cadre de cette procédure pas à pas. Cependant, vous avez peut-être remarqué quelque chose d’intéressant : une méthode `craftRequest` qui prend l’URL de votre tâche. Cette méthode correspond à ce que vous utilisez pour créer la requête pour le serveur, avec le jeton d’accès que vous avez reçu. C’est ce que vous allez écrire maintenant.

Ajoutez le code suivant au fichier `samplesWebAPIConnector.m` :

```
+(void) craftRequest : (NSString*)webApiUrlString
               parent:(UIViewController*) parent
    completionHandler:(void (^)(NSMutableURLRequest*, NSError* error))completionBlock
{
    [self getClaimsWithPolicyClearingCache:NO parent:parent completionHandler:^(NSString* accessToken, NSError* error){

        if (accessToken == nil)
        {
            completionBlock(nil,error);
        }
        else
        {
            NSURL *webApiURL = [[NSURL alloc]initWithString:webApiUrlString];

            NSMutableURLRequest *request = [[NSMutableURLRequest alloc]initWithURL:webApiURL];

            NSString *authHeader = [NSString stringWithFormat:@"Bearer %@", accessToken];

            [request addValue:authHeader forHTTPHeaderField:@"Authorization"];

            completionBlock(request, nil);
        }
    }];
}
```

Cette opération prend un URI (uniform resource identifier) de site web, lui ajoute le jeton en utilisant l’en-tête `Bearer` dans HTTP, puis vous le renvoie. Vous appelez l’API `getTokenClearingCache`. Cela peut sembler étrange, mais vous utilisez simplement cet appel pour obtenir un jeton à partir du cache et pour vous assurer qu’il est toujours valide. (C’est ce que font pour vous les appels `getToken` en interrogeant la bibliothèque ADAL.) Vous allez utiliser ce code dans chaque appel. Ensuite, vous allez concevoir des méthodes de tâche supplémentaires.

Écrivez `addTask` :

```
+(void) addTask:(samplesTaskItem*)task
         parent:(UIViewController*) parent
completionBlock:(void (^) (bool, NSError* error)) completionBlock
{
    if (!loadedApplicationSettings)
    {
        [self readApplicationSettings];
    }

    SamplesApplicationData* data = [SamplesApplicationData getInstance];
    [self craftRequest:data.taskWebApiUrlString parent:parent completionHandler:^(NSMutableURLRequest* request, NSError* error){

        if (error != nil)
        {
            completionBlock(NO, error);
        }
        else
        {
            NSDictionary* taskInDictionaryFormat = [self convertTaskToDictionary:task];

            NSData* requestBody = [NSJSONSerialization dataWithJSONObject:taskInDictionaryFormat options:0 error:nil];

            [request setHTTPMethod:@"POST"];
            [request addValue:@"application/json" forHTTPHeaderField:@"Content-Type"];
            [request setHTTPBody:requestBody];

            NSString *myString = [[NSString alloc] initWithData:requestBody encoding:NSUTF8StringEncoding];

            NSLog(@"Request was: %@", request);
            NSLog(@"Request body was: %@", myString);

            NSOperationQueue *queue = [[NSOperationQueue alloc]init];

            [NSURLConnection sendAsynchronousRequest:request queue:queue completionHandler:^(NSURLResponse *response, NSData *data, NSError *error) {

                NSString* content = [[NSString alloc] initWithData:data encoding:NSUTF8StringEncoding];
                NSLog(@"%@", content);

                if (error == nil){

                    completionBlock(true, nil);
                }
                else
                {
                    completionBlock(false, error);
                }
            }];
        }
    }];
}
```

Le modèle suivi est identique, à ceci près qu’il introduit également la méthode finale que vous devez implémenter : `convertTaskToDictionary`. L’objectif est ici de prendre votre tableau et d’en faire un objet Dictionary. Cet objet prend plus facilement la forme des paramètres de requête que vous devez transmettre au serveur. Le code est simple :

```
// Here we have some conversation helpers that allow us to parse passed items into dictionaries for URLEncoding later.

+(NSDictionary*) convertTaskToDictionary:(samplesTaskItem*)task
{
    NSMutableDictionary* dictionary = [[NSMutableDictionary alloc]init];

    if (task.itemName){
        [dictionary setValue:task.itemName forKey:@"task"];
    }

    return dictionary;
}

```

Ensuite, écrivez `deleteTask` :

```
+(void) deleteTask:(samplesTaskItem*)task
            parent:(UIViewController*) parent
   completionBlock:(void (^) (bool, NSError* error)) completionBlock
{
    if (!loadedApplicationSettings)
    {
        [self readApplicationSettings];
    }

    SamplesApplicationData* data = [SamplesApplicationData getInstance];
    [self craftRequest:data.taskWebApiUrlString parent:parent completionHandler:^(NSMutableURLRequest* request, NSError* error){

        if (error != nil)
        {
            completionBlock(NO, error);
        }
        else
        {
            NSDictionary* taskInDictionaryFormat = [self convertTaskToDictionary:task];

            NSData* requestBody = [NSJSONSerialization dataWithJSONObject:taskInDictionaryFormat options:0 error:nil];

            [request setHTTPMethod:@"DELETE"];
            [request addValue:@"application/json" forHTTPHeaderField:@"Content-Type"];
            [request setHTTPBody:requestBody];

            NSLog(@"%@", request);

            NSOperationQueue *queue = [[NSOperationQueue alloc]init];

            [NSURLConnection sendAsynchronousRequest:request queue:queue completionHandler:^(NSURLResponse *response, NSData *data, NSError *error) {

                NSString* content = [[NSString alloc] initWithData:data encoding:NSUTF8StringEncoding];
                NSLog(@"%@", content);

                if (error == nil){

                    completionBlock(true, nil);
                }
                else
                {
                    completionBlock(false, error);
                }
            }];
        }
    }];
}
```

### Ajoutez la déconnexion à votre application.

La dernière chose à faire est d’implémenter la déconnexion pour votre application. C’est simple. Dans le fichier `sampleWebApiConnector.m` :

```
+(void) signOut
{
    [authContext.tokenCacheStore removeAll:nil];

    NSHTTPCookie *cookie;

    NSHTTPCookieStorage *storage = [NSHTTPCookieStorage sharedHTTPCookieStorage];
    for (cookie in [storage cookies])
    {
        [storage deleteCookie:cookie];
    }
}
```

## Exécution de l'exemple d'application

Pour terminer, générez et exécutez l’application dans Xcode. Inscrivez-vous ou connectez-vous à l’application, puis créez des tâches pour un utilisateur connecté. Déconnectez-vous et reconnectez-vous avec les identifiants d’un autre utilisateur, puis créez des tâches pour cet utilisateur.

Notez la façon dont les tâches sont stockées par utilisateur sur l’API, dans la mesure où l’API extrait l’identité de l’utilisateur à partir du jeton d’accès reçu.

Pour référence, l’exemple terminé [est fourni sous forme de fichier .zip](https://github.com/AzureADQuickStarts/B2C-NativeClient-iOS/archive/complete.zip). Vous pouvez également le cloner à partir de GitHub :

```git clone --branch complete https://github.com/AzureADQuickStarts/B2C-NativeClient-iOS```

## Étapes suivantes

Vous pouvez maintenant aborder des rubriques B2C plus sophistiquées. Vous pouvez essayer :

[Appel d’une API web Node.js depuis une application web Node.js]()

[Personnalisation de l’expérience utilisateur pour une application B2C]()

<!---HONumber=AcomDC_0608_2016-->