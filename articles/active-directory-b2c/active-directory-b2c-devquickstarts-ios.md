<properties pageTitle="Version préliminaire d'Azure AD B2C : appel d'une API Web à partir d'une application iOS | Microsoft Azure" description="Cet article décrit comment créer une application iOS de type « liste des tâches » qui appelle une API Web node.js utilisant des jetons du porteur OAuth 2.0. L'application iOS et l'API Web utilisent toutes les deux Azure AD B2C pour gérer les identités des utilisateurs et authentifier les utilisateurs." services="active-active-b2c" documentationCenter="ios" authors="brandwe" manager="mbaldwin" editor=""/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="objectivec"
	ms.topic="article"
	ms.date="09/22/2015"
	ms.author="brandwe"/>

# Version préliminaire d'Azure AD B2C : appel d'une API Web à partir d'une application iOS

<!-- TODO [AZURE.INCLUDE [active-directory-b2c-devquickstarts-web-switcher](../../includes/active-directory-b2c-devquickstarts-web-switcher.md)]-->

Avec Azure AD B2C, vous pouvez ajouter des fonctionnalités de gestion des identités en libre-service puissantes à vos applications iOS et API Web en quelques étapes seulement. Cet article vous explique comment créer une application iOS de type « liste des tâches », qui appelle une API Web node.js utilisant des jetons du porteur OAuth 2.0. L'application iOS et l'API Web utilisent toutes les deux Azure AD B2C pour gérer les identités des utilisateurs et authentifier les utilisateurs.

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]
	
> [AZURE.NOTE]Ce guide de démarrage rapide nécessite que vous disposiez d'une API Web protégée par Azure AD avec B2C. Nous en avons créé une pour .Net et node.js afin que vous puissiez l'utiliser. Cette procédure pas à pas suppose que l'exemple d'API Web node.js est configuré. Reportez-vous à l'[exemple API Web Azure Active Directory pour Node.js](active-directory-b2c-devquickstarts-api-node.md`).

> [AZURE.NOTE]Cet article ne couvre pas l'implémentation de la connexion, de l'inscription et de la gestion de profil avec Azure AD B2C. Il s'intéresse principalement à l'appel d'API web après que l'utilisateur s'est authentifié. Si ce n'est pas déjà fait, commencez par consulter le [didacticiel de prise en main de l'application web .NET](active-directory-b2c-devquickstarts-web-dotnet.md) pour en savoir plus sur les principes de base d'Azure AD B2C.

## 1\. Obtention d'un répertoire Azure AD B2C

Avant de pouvoir utiliser Azure AD B2C, vous devez créer un répertoire ou un client. Un répertoire est un conteneur destiné à recevoir tous vos utilisateurs, applications, groupes et autres. Si vous n'en avez pas encore, reportez-vous à [Créer un répertoire B2C](active-directory-b2c-get-started.md) avant d'aller plus loin.

## 2\. Création d'une application

Vous devez maintenant créer dans votre répertoire B2C une application fournissant à Azure AD certaines informations nécessaires pour communiquer de manière sécurisée avec votre application. L'application et l'API Web seront alors toutes les deux représentées par un seul **ID d'application**, car elles constituent une application logique. Pour créer une application, suivez [ces instructions](active-directory-b2c-app-registration.md). Assurez-vous de

- Inclure une **application web/API web** dans l'application
- Saisir `http://localhost:3000/auth/openid/return` en tant qu'**URL de réponse** ; il s'agit de l'URL par défaut pour cet exemple de code.
- Créer une **clé secrète d'application** pour votre application et notez-la quelque part. Vous en aurez besoin rapidement.
- Notez également l'**ID d'application** affecté à votre application. Vous en aurez aussi besoin rapidement.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## 3\. Création de vos stratégies

Dans Azure AD B2C, chaque expérience utilisateur est définie par une [**stratégie**](active-directory-b2c-reference-policies.md). Cette application contient trois expériences liées à l'identité : l'inscription, la connexion et la connexion avec Facebook. Vous devez créer une stratégie de chaque type, comme décrit dans l’[article de référence de stratégie](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy). Lors de la création de vos trois stratégies, assurez-vous de :

- Choisir le **Nom d’affichage** et quelques autres attributs d’inscription dans votre stratégie d’inscription.
- Choisir les revendications d’application **Nom d’affichage** et **ID objet** dans chaque stratégie. Vous pouvez aussi choisir d'autres revendications.
- Noter le **nom** de chaque stratégie après sa création. Il doit porter le préfixe `b2c_1_`. Vous aurez besoin des noms de ces stratégies rapidement. 

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Une fois vos trois stratégies créées, vous pouvez générer votre application.

Remarque : cet article n'explique pas comment utiliser les stratégies que vous venez de créer. Pour en savoir plus sur la façon dont les stratégies fonctionnent dans Azure AD B2C, nous vous recommandons de commencer par lire le [didacticiel sur la prise en main de l’application web .NET](active-directory-b2c-devquickstarts-web-dotnet.md).

## 4\. Téléchargement du code

Le code associé à ce didacticiel est stocké [sur GitHub](https://github.com/AzureADQuickStarts/B2C-NativeClient-iOS). Pour générer l’exemple à mesure que vous avancez, vous pouvez [télécharger une structure de projet sous la forme d’un fichier zip](https://github.com/AzureADQuickStarts/B2C-NativeClient-iOS/archive/skeleton.zip) ou cloner la structure :

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-NativeClient-iOS.git
```

> [AZURE.NOTE]**Le téléchargement de la structure est nécessaire pour suivre ce didacticiel.** En raison de la complexité de l’implémentation d’une application entièrement fonctionnelle sur iOS, la **structure** dispose d’un code d’expérience utilisateur qui s’exécutera quand vous aurez terminé le didacticiel ci-dessous. Il s'agit d'une mesure visant à gagner du temps pour le développeur. Le code de l'expérience utilisateur n'est pas associé à la rubrique d'ajout de B2C à une application iOS.

L’application terminée est également [disponible en tant que fichier .zip](https://github.com/AzureADQuickStarts/B2C-NativeClient-iOS/archive/complete.zip) ou sur la branche `complete` du même référentiel.


Chargez maintenant le podfile à l’aide de Cocoapods. Cette opération crée un nouvel espace de travail XCode que vous allez charger. Si vous n’avez pas Cocoapods, visitez [le site web pour le programme d’installation cocoapods](https://cocoapods.org).

```
$ pod install
...
$ open Microsoft Tasks for Consumers.xcworkspace
```

## 5\. Configuration de l'application de la tâche iOS

Afin que l'application de la tâche iOS communique avec Azure AD B2C, vous devez renseigner certains paramètres communs. Dans le dossier `Microsoft Tasks`, ouvrez le fichier `settings.plist` qui se trouve à la racine et remplacez les valeurs de la section `<dict>`. Ces valeurs seront utilisées dans l'application.

```
<dict>
	<key>authority</key>
	<string>https://login.microsoftonline.com/<your tenant name>.onmicrosoft.com/</string>
	<key>clientId</key>
	<string><Enter the Application Id assinged to your app by the Azure portal, e.g.580e250c-8f26-49d0-bee8-1c078add1609></string>
	<key>scopes</key>
	<array>
		<string><Enter the Application Id assinged to your app by the Azure portal, e.g.580e250c-8f26-49d0-bee8-1c078add1609></string>
	</array>
	<key>additionalScopes</key>
	<array>
		<string></string>
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

## 6\. Obtention des jetons d'accès et appel de l'API de tâche

Cette section démontre comment procéder à un échange de jeton OAuth 2.0 dans une application Web à l'aide des bibliothèques et des infrastructures de Microsoft. Si vous n’êtes pas familiarisé avec les **codes d’autorisation** et les **jetons d’accès**, nous vous conseillons de consulter les [informations de référence sur le protocole OAuth 2.0](active-directory-b2c-reference-protocols.md).

#### Créer des fichiers d'en-tête avec nos méthodes que nous allons utiliser.

Nous aurons besoin de méthodes pour obtenir un jeton avec notre stratégie sélectionnée, puis appeler notre serveur de la tâche. Configurons-les dès maintenant.

Créez un fichier appelé `samplesWebAPIConnector.h` sous /Microsoft Tasks dans votre projet XCode.

Ajoutez le code suivant pour définir ce que nous devons faire :

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

Vous verrez qu’il s’agit d’opérations CRUD simples sur notre API, ainsi qu’une méthode `doPolicy` qui vous permet d’obtenir un jeton avec la stratégie sélectionnée.

Vous verrez également que nous avons deux autres fichiers d'en-tête que nous devons définir et qui contiendront notre élément de tâche et les données de notre stratégie. Créons-les maintenant :

Créez un fichier appelé `samplesTaskItem.h` en utilisant le code suivant :

```
#import <Foundation/Foundation.h>

@interface samplesTaskItem : NSObject

@property NSString *itemName;
@property NSString *ownerName;
@property BOOL completed;
@property (readonly) NSDate *creationDate;

@end
```

Créons également un fichier `samplesPolicyData.h` pour contenir les données de la stratégie :

```
#import <Foundation/Foundation.h>

@interface samplesPolicyData : NSObject

@property (strong) NSString* policyName;
@property (strong) NSString* policyID;

+(id) getInstance;

@end
```
#### Ajout d'une implémentation pour les éléments de tâche et de stratégie

Maintenant que nos fichiers d'en-tête sont en place, nous devons écrire du code pour stocker les données que nous utiliserons dans notre exemple.

Créez un fichier appelé `samplesPolicyData.m` en utilisant le code suivant :

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

#### Écriture de code de configuration pour notre appel dans la bibliothèque ADAL pour iOS

Le code rapide permettant de stocker les objets de l'interface utilisateur est terminé. Nous devons maintenant écrire du code pour accéder à la bibliothèque ADAL pour iOS avec les paramètres dans notre fichier `settings.plist` afin d’obtenir un jeton d’accès à fournir à notre serveur de la tâche. Cette étape peut se révéler compliquée, donc restez concentré.

Tout notre travail sera effectué dans `samplesWebAPIConnector.m`.

Tout d’abord, créons notre implémentation `doPolicy()` que nous avons rédigée dans notre fichier d’en-tête `samplesWebAPIConnector.h` :

```
+(void) doPolicy:(samplesPolicyData *)policy
         parent:(UIViewController*) parent
completionBlock:(void (^) (ADProfileInfo* userInfo, NSError* error)) completionBlock
{
    if (!loadedApplicationSettings)
    {
        [self readApplicationSettings];
    }
    
    NSDictionary* params = [self convertPolicyToDictionary:policy];
    
    [self getClaimsWithPolicyClearingCache:NO policy:policy params:params parent:parent completionHandler:^(ADProfileInfo* userInfo, NSError* error) {
        
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

Vous constatez que la méthode est relativement simple. Elle prend comme entrée l’objet `samplesPolicyData` que nous avons créé il y a quelques instants, le ViewController parent, puis un rappel. Le rappel est intéressant et nous allons l'examiner en détail.

1. Vous constatez que le `completionBlock` contient ADProfileInfo en tant que type qui sera retourné avec un objet `userInfo`. ADProfileInfo est le type qui contient l'ensemble de la réponse du serveur, en particulier les revendications. 

2. Vous pouvez constater que nous utilisons `readApplicationSettings`. Ceci permet de lire les données fournies dans `settings.plist`.
3. Vous verrez que nous avons une méthode `convertPolicyToDictionary:policy` qui utilise notre stratégie et la met en forme en tant qu’URL à envoyer au serveur. Nous écrirons cette méthode d'assistance ultérieurement.
4. Enfin, nous avons une méthode `getClaimsWithPolicyClearingCache` plutôt volumineuse. Il s'agit de l'appel réel à la bibliothèque ADAL pour iOS que nous devons écrire. Nous ferons cela plus tard.


Ensuite, nous allons écrire cette méthode `convertPolicyToDictionary` sous le code que nous venons d’écrire :

```
// Here we have some converstion helpers that allow us to parse passed items in to dictionaries for URLEncoding later.

+(NSDictionary*) convertTaskToDictionary:(samplesTaskItem*)task
{
    NSMutableDictionary* dictionary = [[NSMutableDictionary alloc]init];
    
    if (task.itemName){
        [dictionary setValue:task.itemName forKey:@"task"];
    }
    
    return dictionary;
}

+(NSDictionary*) convertPolicyToDictionary:(samplesPolicyData*)policy
{
    NSMutableDictionary* dictionary = [[NSMutableDictionary alloc]init];

    
    if (policy.policyID){
        [dictionary setValue:policy.policyID forKey:@"p"];
    }
    
    return dictionary;
}

```
Ce code relativement simple ajoute un p à notre stratégie afin que l’aspect de la requête soit ?p=<policy>.

Maintenant, écrivons notre méthode volumineuse `getClaimsWithPolicyClearingCache`. Elle est suffisamment volumineuse pour mériter sa propre section

#### Création de notre appel à la bibliothèque ADAL pour iOS

Si vous avez téléchargé la structure de GitHub, vous verrez que nous en avons déjà plusieurs en place pour nous aider avec l'exemple d'application. Ils suivent tous le modèle de `get(Claims|Token)With<verb>ClearningCache`. En utilisant des conventions Objetive C, ceci se lit presque normalement. Par exemple « obtenir un jeton avec des paramètres supplémentaires que je donne et effacer le cache ». Cela équivaut à `getTokenWithExtraParamsClearingCache()`. Plutôt simple.

Nous allons écrire « obtenir des revendications et un jeton avec la stratégie que je fournis et ne pas effacer le cache » ou `getClaimsWithPolicyClearingCache`. Nous obtenons toujours un jeton à partir de la bibliothèque ADAL, donc il n'est pas nécessaire de spécifier « Revendications et jeton » dans la méthode. Cependant, parfois vous voulez simplement le jeton sans la surcharge de l’analyse des revendications, donc nous avons fourni une méthode sans Revendications appelée `getTokenWithPolicyClearingCache` dans la structure.

Écrivons ce code maintenant :

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

La première partie doit vous sembler familière. Nous chargeons les paramètres qui ont été fournis dans `Settings.plist` et les assignons à `data`. Nous configurons ensuite une `ADAuthenticationError` qui prendra toute erreur provenant de la bibliothèque ADAL pour iOS. Nous créons également un `authContext` qui consiste à configurer notre appel à la bibliothèque ADAL. Nous lui transmettons notre *autorité* pour démarrer. Nous donnons également au `authContext` une référence à notre contrôleur parent, afin de pouvoir le retourner. Nous convertissons également notre `redirectURI` qui était une chaîne dans notre `settings.plist` dans le type NSURL attendu par la bibliothèque ADAL. Enfin, nous définissons un `correlationId` qui est simplement un UUID qui peut suivre l’appel vers le client et le serveur, et vice versa. Cela est utile pour le débogage.

À présent, passons à l'appel à la bibliothèque ADAL. C'est à ce moment-là que l'appel diffère de ce à quoi vous pourriez vous attendre avec les utilisations précédentes de la bibliothèque ADAL pour iOS :

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

Vous pouvez voir ici que l'appel est assez simple.

**scopes** : étendues que nous transmettons au serveur, que nous souhaitons demander au serveur, pour l’utilisateur qui se connecte. Pour la version préliminaire de B2C, nous transmettons la valeur client\_id. Toutefois, ceci sera modifié pour la lecture des étendues à l'avenir. Ce document sera alors mis à jour. **addtionalScopes** : étendues supplémentaires que vous pourriez vouloir utiliser pour votre application. Elles seront utilisées à l’avenir. **clientId** : ID de l’application vous avez obtenu du portail. **redirectURI** : redirection où nous nous attendons à ce que le jeton soit publié. **identifier** : moyen d’identifier l’utilisateur. Ainsi, nous pouvons voir s’il existe un jeton utilisable dans le cache plutôt que de toujours demander un autre jeton au serveur. Vous constatez que ceci est exécuté dans un type appelé `ADUserIdentifier` et nous pouvons spécifier ce que nous voulons utiliser en tant qu’ID. Vous devriez utiliser le nom d’utilisateur. **promptBehavior** : ceci est obsolète et doit être remplacé par AD\_PROMPT\_ALWAYS **extraQueryParameters** : tout contenu supplémentaire que vous souhaitez transmettre au serveur dans un format codé de type URL. **policy** : stratégie que vous appelez. La partie la plus importante de cette procédure pas à pas.

Vous pouvez voir dans completionBlock que nous transmettons le `ADAuthenticationResult` qui contient notre jeton et les informations de profil (si l’appel a réussi)

À l'aide du code ci-dessus, vous pouvez obtenir un jeton pour la stratégie que vous fournissez. Nous allons utiliser ce jeton pour appeler l'API.

#### Création de nos appels de tâche au serveur

Maintenant que nous disposons d'un jeton, nous devons le fournir à notre API pour autorisation.

Rappelez-vous que nous avions trois méthodes à implémenter :

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

Notre `getTasksList` fournit un tableau qui représente les tâches dans notre serveur. `addTask` et `deleteTask` effectuent l’action suivante et renvoient TRUE ou FALSE en cas de réussite.

Tout d’abord, écrivons notre `getTaskList` :

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

Le cadre de cette procédure pas-à-pas n’inclut pas le code de tâche, mais il y a quelque chose d’intéressant que vous avez peut-être remarqué : une méthode `craftRequest` qui prend l’URL de notre tâche. Cette méthode est ce que nous utilisons pour créer la requête, avec le jeton d'accès que nous avons reçu, pour le serveur. Écrivons cela maintenant.

Ajoutons le code suivant au fichier `samplesWebAPIConnector.m' :

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

Comme vous pouvez le voir, cette opération prend un URI de site web, lui ajoute le jeton avec l’en-tête `Bearer` dans HTTP, puis nous le retourne. Nous appelons l’API `getTokenClearingCache`, ce qui peut sembler bizarre au premier abord, mais nous utilisons simplement cet appel pour obtenir un jeton à partir du cache et nous assurer qu’il est toujours valide (les appels getToken* font cela pour nous en demandant à la bibliothèque ADAL). Nous utilisons ce code dans chaque appel. Maintenant, revenons à la création de nos méthodes de tâche supplémentaires.

Écrivons notre `addTask` :

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

Cette opération suit le même modèle, mais introduit une autre méthode (finale) que nous devons implémenter : `convertTaskToDictionary` qui utilise notre tableau et en fait un objet de dictionnaire qui est plus facilement transformé pour les paramètres de requête que nous devons transmettre au serveur. Ce code est très simple :

```
// Here we have some converstion helpers that allow us to parse passed items in to dictionaries for URLEncoding later.

+(NSDictionary*) convertTaskToDictionary:(samplesTaskItem*)task
{
    NSMutableDictionary* dictionary = [[NSMutableDictionary alloc]init];
    
    if (task.itemName){
        [dictionary setValue:task.itemName forKey:@"task"];
    }
    
    return dictionary;
}

```

Enfin, écrivons notre `deleteTask` :

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

### Ajouter la déconnexion à notre application.

La dernière chose à faire est d'implémenter la déconnexion pour notre application. Ceci est plutôt simple. De nouveau, dans notre fichier `sampleWebApiConnector.m` :

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

## 9\. Exécution de l'exemple d'application

Pour terminer, générez et exécutez les applications dans xCode. Inscrivez-vous ou connectez-vous à l'application et créez des tâches pour l'utilisateur connecté. Déconnectez-vous et reconnectez-vous par le biais d'un autre utilisateur, ce qui a pour effet de créer des tâches pour cet utilisateur.

Notez la façon dont les tâches sont stockées par utilisateur sur l'API, dans la mesure où l'API extrait l'identité de l'utilisateur à partir du jeton d'accès qu'il reçoit.

Pour référence, l’exemple terminé [est fourni au format .zip ici](https://github.com/AzureADQuickStarts/B2C-NativeClient-iOS/archive/complete.zip). Vous pouvez également le cloner à partir de GitHub :

```git clone --branch complete https://github.com/AzureADQuickStarts/B2C-NativeClient-iOS```

## Étapes suivantes

Vous pouvez maintenant aborder des rubriques B2C plus sophistiquées. Par exemple :

[Appel d'une API Web node.js à partir d'une application Web node.js >>]()

[Personnalisation de l'UX de l'application B2C >>]()

<!---HONumber=Oct15_HO1-->