<properties
	pageTitle="Azure Active Directory B2C : appel d’une API web à partir d’une application iOS à l’aide d’une bibliothèque tierce | Microsoft Azure"
	description="Cet article vous explique comment créer une application iOS de type « liste de tâches » qui appelle une API web Node.js en utilisant les jetons du porteur OAuth 2.0 à l’aide d’une bibliothèque tierce."
	services="active-directory-b2c"
	documentationCenter="ios"
	authors="brandwe"
	manager="mbaldwin"
	editor=""/>

<tags ms.service="active-directory-b2c" ms.workload="identity" ms.tgt_pltfrm="na" ms.devlang="objectivec" ms.topic="hero-article"

	ms.date="07/26/2016"
	ms.author="brandwe"/>

# Azure AD B2C : appel d’une API web à partir d’une application iOS à l’aide d’une bibliothèque tierce

<!-- TODO [AZURE.INCLUDE [active-directory-b2c-devquickstarts-web-switcher](../../includes/active-directory-b2c-devquickstarts-web-switcher.md)]-->

La plateforme d’identité Microsoft utilise des normes ouvertes telles que OAuth2 et OpenID Connect. Cela permet aux développeurs de tirer parti de toute bibliothèque qu’ils souhaitent intégrer à nos services. Pour aider les développeurs à utiliser notre plateforme avec d’autres bibliothèques, nous avons rédigé quelques procédures pas à pas comme celle-ci, afin d’expliquer la configuration des bibliothèques tierces pour se connecter à la plateforme d’identité Microsoft. La plupart des bibliothèques qui implémentent [la spécification RFC6749 OAuth2](https://tools.ietf.org/html/rfc6749) seront en mesure de se connecter à la plateforme Microsoft Identity.


Si vous découvrez OAuth2 ou OpenID Connect, cet exemple de configuration n’est peut-être pas très parlant pour vous. Nous vous recommandons de consulter une brève [vue d’ensemble du protocole que nous avons décrit ici](active-directory-b2c-reference-protocols.md).

> [AZURE.NOTE]
    Certaines fonctionnalités de notre plateforme qui ont une expression dans ces normes, comme la gestion de la stratégie d’accès conditionnel et d’Intune, requièrent l’utilisation de nos bibliothèques d’identité Microsoft Azure open source.
   
Les scénarios et fonctionnalités Azure Active Directory ne sont pas tous pris en charge par la plateforme B2C. Pour déterminer si vous devez utiliser la plateforme B2C, consultez les [limites de B2C](active-directory-b2c-limitations.md).


## Obtention d'un répertoire Azure AD B2C

Avant de pouvoir utiliser Azure AD B2C, vous devez créer un répertoire ou un client. Un répertoire est un conteneur destiné à recevoir tous vos utilisateurs, applications, groupes, etc. Si vous n’en possédez pas déjà un, [créez un répertoire B2C](active-directory-b2c-get-started.md) avant de continuer.

## Création d'une application

Vous devez maintenant créer dans votre répertoire B2C une application fournissant à Azure AD les informations nécessaires pour communiquer avec votre application en toute sécurité. Dans ce cas, l’application et l’API web sont toutes deux représentées par un seul **ID d’application**, car elles constituent une seule application logique. Pour créer une application, suivez [ces instructions](active-directory-b2c-app-registration.md). Veillez à effectuer les opérations suivantes :

- Incluez un **appareil mobile** dans l’application.
- Copiez **l’ID d’application** affecté à votre application. Vous en aurez besoin ultérieurement.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## Création de vos stratégies

Dans Azure AD B2C, chaque expérience utilisateur est définie par une [stratégie](active-directory-b2c-reference-policies.md). Cette application contient une seule expérience liée à l’identité : une connexion et une inscription combinées. Vous devez créer cette stratégie pour chaque type, comme décrit dans [l’article de référence sur les stratégies](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy). Lorsque vous créez la stratégie, prenez soin de :

- Choisir le **nom d’affichage** et les attributs d’inscription dans votre stratégie.
- Choisir le **nom d’affichage** et **l’ID objet** comme revendications d’application pour chaque stratégie. Vous pouvez aussi choisir d'autres revendications.
- Copier le **nom** de chaque stratégie après sa création. Il doit porter le préfixe `b2c_1_`. Le nom de stratégie vous sera demandé ultérieurement.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Une fois vos stratégies créées, vous pouvez générer votre application.


## Téléchargement du code

Le code associé à ce didacticiel est stocké [sur GitHub](https://github.com/Azure-Samples/active-directory-ios-native-nxoauth2-b2c). Pour suivre la procédure, vous pouvez [télécharger l’application au format .zip](https://github.com/Azure-Samples/active-directory-ios-native-nxoauth2-b2c)/archive/master.zip) ou la cloner :

```
git clone git@github.com:Azure-Samples/active-directory-ios-native-nxoauth2-b2c.git
```

Ou téléchargez simplement le code terminé et démarrez immédiatement :

```
git clone --branch complete git@github.com:Azure-Samples/active-directory-ios-native-nxoauth2-b2c.git
```

## Télécharger la bibliothèque tierce nxoauth2 et lancer un espace de travail

Pour cette procédure pas à pas, nous utiliserons OAuth2Client à partir de GitHub, une bibliothèque OAuth2 pour Mac OS X et iOS (Cocoa & Cocoa touch). Cette bibliothèque est basée sur le brouillon 10 de la spécification OAuth2. Elle implémente le profil de l’application native et prend en charge le point de terminaison de l’autorisation de l’utilisateur final. Voici tout ce dont nous aurons besoin pour l’intégration avec la plateforme d’identité Microsoft.

### Ajout de la bibliothèque à votre projet à l’aide de CocoaPods

CocoaPods est un gestionnaire de dépendances pour les projets Xcode. Il gère automatiquement les étapes d’installation ci-dessus.

```
$ vi Podfile
```
Ajoutez le code suivant à ce podfile :

```
 platform :ios, '8.0'
 
 target 'SampleforB2C' do
 
 pod 'NXOAuth2Client'
 
 end
```

Chargez maintenant le podfile à l’aide de Cocoapods. Cette opération crée un nouvel espace de travail XCode que vous allez charger.

```
$ pod install
...
$ open SampleforB2C.xcworkspace

```

## Structure du projet

Nous avons défini la structure suivante pour notre projet dans le squelette :

* **vue principale** avec un volet de tâches ;
* **vue d’ajout de tâche** pour les données relatives à la tâche sélectionnée ;
* **vue de connexion** permettant à un utilisateur de se connecter à l’application.

Nous accéderons à différents fichiers dans le projet pour ajouter l’authentification. D’autres parties du code, comme le code de l’élément visuel, ne sont pas associées à l’identité et sont fournies pour vous.

## Créer le fichier `settings.plist` pour votre application

La configuration de l’application est plus simple si nous disposons d’un emplacement centralisé où placer nos valeurs de configuration. Cette approche vous aide également à comprendre le rôle de chaque paramètre dans votre application. Nous allons tirer profit de la *liste de propriétés* pour fournir ces valeurs à l’application.

* Créez/ouvrez le fichier `settings.plist` sous `Supporting Files` dans votre espace de travail d’application.

* Insérez-y les valeurs ci-après (nous les examinerons en détail dans la suite de cet article).

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
	<key>accountIdentifier</key>
	<string>B2C_Acccount</string>
	<key>clientID</key>
	<string><client ID></string>
	<key>clientSecret</key>
	<string></string>
	<key>authURL</key>
	<string>https://login.microsoftonline.com/<tenant name>/oauth2/v2.0/authorize?p=<policy name></string>
	<key>loginURL</key>
	<string>https://login.microsoftonline.com/<tenant name>/login</string>
	<key>bhh</key>
	<string>urn:ietf:wg:oauth:2.0:oob</string>
	<key>tokenURL</key>
	<string>https://login.microsoftonline.com/<tenant name>/oauth2/v2.0/token?p=<policy name></string>
	<key>keychain</key>
	<string>com.microsoft.azureactivedirectory.samples.graph.QuickStart</string>
	<key>contentType</key>
	<string>application/x-www-form-urlencoded</string>
	<key>taskAPI</key>
	<string>https://aadb2cplayground.azurewebsites.net</string>
</dict>
</plist>
```

Penchez-nous plus avant sur ces éléments.


Pour les éléments `authURL`, `loginURL`, `bhh` et `tokenURL`, vous remarquerez que vous devez indiquer votre nom de client. Il s’agit du nom de votre client B2C qui vous a été affecté. Par exemple, `kidventusb2c.onmicrosoft.com`. Si vous utilisez nos bibliothèques d’identité Microsoft Azure open source, nous extrairons ces données pour vous à l’aide de notre point de terminaison des métadonnées. Nous avons effectué le travail d’extraction de ces valeurs pour vous.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

La valeur `keychain` est le conteneur qu’utilisera la bibliothèque NXOAuth2Client afin de créer un trousseau pour le stockage de vos jetons. Si vous souhaitez obtenir l’authentification unique entre de nombreuses applications, vous pouvez spécifier le même porte-clés dans chacune de vos applications, ainsi que demander l’utilisation de ce porte-clés dans vos droits Xcode. Cela est décrit dans la documentation Apple.

La variable `<policy name>` à la fin de chaque URL indique les emplacements où vous avez placé la stratégie créée ci-dessus. L’application appellera ces stratégies en fonction du flux.

L’élément `taskAPI` est le point de terminaison REST que nous appellerons avec votre jeton B2C pour ajouter des tâches ou pour exécuter une requête sur des tâches existantes. Cet élément a été spécifiquement configuré pour cet exemple. Vous n’avez pas besoin de le modifier pour que l’exemple fonctionne.

Le reste de ces valeurs est requis pour utiliser la bibliothèque et simplement créer des emplacements pour que vous puissiez traiter des valeurs dans le contexte.

Maintenant que nous avons créé le fichier `settings.plist`, nous avons besoin d’écrire le code permettant de le lire.

## Configurer une classe AppData pour lire nos paramètres

Nous allons créer un fichier simple qui se contente d’analyser le fichier `settngs.plist` que nous avons créé ci-dessus, puis rendre ces paramètres disponibles pour n’importe quelle classe à l’avenir. Étant donné que nous ne souhaitons pas créer une nouvelle copie des données chaque fois que ces dernières sont demandées par une classe, nous allons utiliser un modèle Singleton et nous contenter de renvoyer la même instance créée chaque fois qu’une demande porte sur des paramètres.

* Créez un fichier `AppData.h` :

```objc
#import <Foundation/Foundation.h>

@interface AppData : NSObject

@property(strong) NSString *accountIdentifier;
@property(strong) NSString *taskApiString;
@property(strong) NSString *authURL;
@property(strong) NSString *clientID;
@property(strong) NSString *loginURL;
@property(strong) NSString *bhh;
@property(strong) NSString *keychain;
@property(strong) NSString *tokenURL;
@property(strong) NSString *clientSecret;
@property(strong) NSString *contentType;

+ (id)getInstance;

@end
```

* Créez un fichier `AppData.m` :

```objc
#import "AppData.h"

@implementation AppData

+ (id)getInstance {
  static AppData *instance = nil;
  static dispatch_once_t onceToken;

  dispatch_once(&onceToken, ^{
    instance = [[self alloc] init];

    NSDictionary *dictionary = [NSDictionary
        dictionaryWithContentsOfFile:[[NSBundle mainBundle]
                                         pathForResource:@"settings"
                                                  ofType:@"plist"]];
    instance.accountIdentifier = [dictionary objectForKey:@"accountIdentifier"];
    instance.clientID = [dictionary objectForKey:@"clientID"];
    instance.clientSecret = [dictionary objectForKey:@"clientSecret"];
    instance.authURL = [dictionary objectForKey:@"authURL"];
    instance.loginURL = [dictionary objectForKey:@"loginURL"];
    instance.bhh = [dictionary objectForKey:@"bhh"];
    instance.tokenURL = [dictionary objectForKey:@"tokenURL"];
    instance.keychain = [dictionary objectForKey:@"keychain"];
    instance.contentType = [dictionary objectForKey:@"contentType"];
    instance.taskApiString = [dictionary objectForKey:@"taskAPI"];

  });

  return instance;
}
@end
```

À présent, nous pouvons facilement accéder à nos données en appelant simplement `  AppData *data = [AppData getInstance];` dans toutes nos classes, comme démontré ci-après.



## Configurer la bibliothèque NXOAuth2Client dans votre AppDelegate

La bibliothèque NXOAuthClient requiert des valeurs pour sa configuration. Une fois cette opération terminée, vous pouvez utiliser le jeton acquis pour appeler l’API REST. Comme nous savons que l’élément `AppDelegate` sera appelé chaque fois que nous chargeons l’authentification, il est judicieux de placer nos valeurs de configuration dans ce fichier.
* Ouvrez le fichier `AppDelegate.m`.

* Importez certains fichiers d’en-tête que nous utiliserons par la suite.

```objc
#import "NXOAuth2.h" // the Identity library we are using
#import "AppData.h" // the class we just created we will use to load the settings of our application
```

* Ajoutez la méthode `setupOAuth2AccountStore` dans l’AppDelegate.

Nous devons créer un AccountStore, puis le remplir avec les données que nous venons de lire à partir du fichier `settings.plist`.

À ce stade, vous devez connaître certaines informations concernant le service B2C qui rendront ce code plus compréhensible :


1. Azure AD B2C utilise la *stratégie* telle qu’elle est fournie par les paramètres de requête pour traiter votre demande. Cela permet à Azure Active Directory d’agir comme un service indépendant uniquement pour votre application. Pour fournir ces paramètres de requête supplémentaires, nous devons indiquer la méthode `kNXOAuth2AccountStoreConfigurationAdditionalAuthenticationParameters:` avec nos paramètres de stratégie personnalisés.

2. Azure AD B2C utilise les étendues à peu près de la même façon que les autres serveurs OAuth2. Toutefois, étant donné que l’utilisation de B2C consiste tout autant à authentifier un utilisateur qu’à accéder aux ressources, certaines étendues sont absolument nécessaires pour que le flux fonctionne correctement. Il s’agit de l’étendue `openid`. Nos Kits de développement logiciel (SDK) Microsoft Identity fournissent automatiquement l’étendue `openid` pour vous, de sorte que vous ne la verrez pas dans la configuration de nos SDK. Toutefois, étant donné que nous utilisons une bibliothèque tierce, nous devons spécifier cette étendue.

```objc
- (void)setupOAuth2AccountStore {
  AppData *data = [AppData getInstance]; // The singleton we use to get the settings

  NSDictionary *customHeaders =
      [NSDictionary dictionaryWithObject:@"application/x-www-form-urlencoded"
                                  forKey:@"Content-Type"];

  // Azure B2C needs
  // kNXOAuth2AccountStoreConfigurationAdditionalAuthenticationParameters for
  // sending policy to the server,
  // therefore we use -setConfiguration:forAccountType:
  NSDictionary *B2cConfigDict = @{
    kNXOAuth2AccountStoreConfigurationClientID : data.clientID,
    kNXOAuth2AccountStoreConfigurationSecret : data.clientSecret,
    kNXOAuth2AccountStoreConfigurationScope :
        [NSSet setWithObjects:@"openid", data.clientID, nil],
    kNXOAuth2AccountStoreConfigurationAuthorizeURL :
        [NSURL URLWithString:data.authURL],
    kNXOAuth2AccountStoreConfigurationTokenURL :
        [NSURL URLWithString:data.tokenURL],
    kNXOAuth2AccountStoreConfigurationRedirectURL :
        [NSURL URLWithString:data.bhh],
    kNXOAuth2AccountStoreConfigurationCustomHeaderFields : customHeaders,
    //      kNXOAuth2AccountStoreConfigurationAdditionalAuthenticationParameters:customAuthenticationParameters
  };

  [[NXOAuth2AccountStore sharedStore] setConfiguration:B2cConfigDict
                                        forAccountType:data.accountIdentifier];
}
```
Ensuite, prenez soin de l’appeler dans l’AppDelegate sous la méthode `didFinishLaunchingWithOptions:`.

```
[self setupOAuth2AccountStore];
```


## Créez une classe `LoginViewController` qui sera utilisée pour gérer les demandes d’authentification.

Nous utilisons un affichage web pour la connexion du compte. Cela nous permet d’inviter l’utilisateur à utiliser des facteurs supplémentaires comme les SMS (s’ils sont configurés) ou de renvoyer les messages d’erreur à l’utilisateur. Ici, nous allons définir l’affichage web, puis écrire ultérieurement le code pour gérer les rappels qui surviendront dans l’affichage web à partir du service d’identité Microsoft.

* Créez une classe `LoginViewController.h`.

```objc
@interface LoginViewController : UIViewController <UIWebViewDelegate>
@property(weak, nonatomic) IBOutlet UIWebView *loginView; // Our webview that we will use to do authentication

- (void)handleOAuth2AccessResult:(NSURL *)accessResult; // Allows us to get a token after we've received an Access code.
- (void)setupOAuth2AccountStore; // We will need to add to our OAuth2AccountStore we setup in our AppDelegate
- (void)requestOAuth2Access; // This is where we invoke our webview.
```

Nous allons créer chacune de ces méthodes ci-après.

> [AZURE.NOTE] 
    Prenez soin de lier l’élément `loginView` à l’affichage web réel situé dans votre table de montage séquentiel. Dans le cas contraire, vous ne disposerez pas d’un affichage web pouvant apparaître au moment de l’authentification.

* Créez une classe `LoginViewController.m`.

* Ajoutez certaines variables destinées à contenir l’état lorsque nous nous authentifions.

```objc
NSURL *myRequestedUrl; \\ The URL request to Azure Active Directory 
NSURL *myLoadedUrl; \\ The URL loaded for Azure Active Directory
bool loginFlow = FALSE; 
bool isRequestBusy; \\ A way to give status to the thread that the request is still happening
NSURL *authcode; \\ A placeholder for our auth code.
```

* Substituer les méthodes d’affichage web pour gérer l’authentification

Nous devons indiquer à l’affichage web le comportement souhaité lorsqu’un utilisateur doit se connecter comme évoqué ci-dessus. Vous pouvez simplement couper et coller le code ci-dessous.

```objc
- (void)resolveUsingUIWebView:(NSURL *)URL {
  // We get the auth token from a redirect so we need to handle that in the
  // webview.

  if (![NSThread isMainThread]) {
    [self performSelectorOnMainThread:@selector(resolveUsingUIWebView:)
                           withObject:URL
                        waitUntilDone:YES];
    return;
  }

  NSURLRequest *hostnameURLRequest =
      [NSURLRequest requestWithURL:URL
                       cachePolicy:NSURLRequestUseProtocolCachePolicy
                   timeoutInterval:10.0f];
  isRequestBusy = YES;
  [self.loginView loadRequest:hostnameURLRequest];

  NSLog(@"resolveUsingUIWebView ready (status: UNKNOWN, URL: %@)",
        self.loginView.request.URL);
}

- (BOOL)webView:(UIWebView *)webView
    shouldStartLoadWithRequest:(NSURLRequest *)request
                navigationType:(UIWebViewNavigationType)navigationType {
  AppData *data = [AppData getInstance];

  NSLog(@"webView:shouldStartLoadWithRequest: %@ (%li)", request.URL,
        (long)navigationType);

  // The webview is where all the communication happens. Slightly complicated.

  myLoadedUrl = [webView.request mainDocumentURL];
  NSLog(@"***Loaded url: %@", myLoadedUrl);

  // if the UIWebView is showing our authorization URL or consent URL, show the
  // UIWebView control
  if ([request.URL.absoluteString rangeOfString:data.authURL
                                        options:NSCaseInsensitiveSearch]
          .location != NSNotFound) {
    self.loginView.hidden = NO;
  } else if ([request.URL.absoluteString rangeOfString:data.loginURL
                                               options:NSCaseInsensitiveSearch]
                 .location != NSNotFound) {
    // otherwise hide the UIWebView, we've left the authorization flow
    self.loginView.hidden = NO;
  } else if ([request.URL.absoluteString rangeOfString:data.bhh
                                               options:NSCaseInsensitiveSearch]
                 .location != NSNotFound) {
    // otherwise hide the UIWebView, we've left the authorization flow
    self.loginView.hidden = YES;
    [[NXOAuth2AccountStore sharedStore] handleRedirectURL:request.URL];
  } else {
    self.loginView.hidden = NO;
    // read the Location from the UIWebView, this is how Microsoft APIs is
    // returning the
    // authentication code and relation information. This is controlled by the
    // redirect URL we chose to use from Microsoft APIs
    // continue the OAuth2 flow
    // [[NXOAuth2AccountStore sharedStore] handleRedirectURL:request.URL];
  }

  return YES;
}

```

* Écrire du code pour gérer le résultat de la demande OAuth2

Nous avons besoin de code qui gère l’URL de redirection renvoyée à partir de l’affichage web. Si elle n’a pas réussi, nous essaierons à nouveau. En attendant, la bibliothèque indique l’erreur que vous pouvez voir dans la console ou gérer de façon asynchrone.

```objc
- (void)handleOAuth2AccessResult:(NSURL *)accessResult {
  // parse the response for success or failure
  if (accessResult)
  // if success, complete the OAuth2 flow by handling the redirect URL and
  // obtaining a token
  {
    [[NXOAuth2AccountStore sharedStore] handleRedirectURL:accessResult];
  } else {
    // start over
    [self requestOAuth2Access];
  }
}
```

* Configurez les fabriques Notification.

Nous créons la même méthode que dans l’élément `AppDelegate` ci-dessus, mais cette fois, nous allons ajouter certains éléments `NSNotification` destinés à nous indiquer ce qui se passe dans notre service. Nous configurons un observateur qui nous signale les modifications avec le jeton. Une fois que nous obtenons le jeton, nous renvoyons l’utilisateur vers `masterView`.



```objc
- (void)setupOAuth2AccountStore {
  [[NSNotificationCenter defaultCenter]
      addObserverForName:NXOAuth2AccountStoreAccountsDidChangeNotification
                  object:[NXOAuth2AccountStore sharedStore]
                   queue:nil
              usingBlock:^(NSNotification *aNotification) {
                if (aNotification.userInfo) {
                  // account added, we have access
                  // we can now request protected data
                  NSLog(@"Success!! We have an access token.");
                  dispatch_async(dispatch_get_main_queue(), ^{

                    MasterViewController *masterViewController =
                        [self.storyboard
                            instantiateViewControllerWithIdentifier:@"master"];
                    [self.navigationController
                        pushViewController:masterViewController
                                  animated:YES];
                  });
                } else {
                  // account removed, we lost access
                }
              }];

  [[NSNotificationCenter defaultCenter]
      addObserverForName:NXOAuth2AccountStoreDidFailToRequestAccessNotification
                  object:[NXOAuth2AccountStore sharedStore]
                   queue:nil
              usingBlock:^(NSNotification *aNotification) {
                NSError *error = [aNotification.userInfo
                    objectForKey:NXOAuth2AccountStoreErrorKey];
                NSLog(@"Error!! %@", error.localizedDescription);
              }];
}

```
* Ajoutez un code qui gère l’utilisateur chaque fois qu’une demande de connexion native est effectuée.

Créons une méthode qui sera appelée chaque fois que nous recevrons une demande d’authentification. Il s’agit de la méthode qui crée réellement un affichage web.

```objc
- (void)requestOAuth2Access {
  AppData *data = [AppData getInstance];

  // in order to login to Mircosoft APIs using OAuth2 we must show an embedded
  // browser (UIWebView)
  [[NXOAuth2AccountStore sharedStore]
           requestAccessToAccountWithType:data.accountIdentifier
      withPreparedAuthorizationURLHandler:^(NSURL *preparedURL) {
        // navigate to the URL returned by NXOAuth2Client

        NSURLRequest *r = [NSURLRequest requestWithURL:preparedURL];
        [self.loginView loadRequest:r];
      }];
}
```

* Enfin, appelons toutes les méthodes que nous avons écrites ci-dessus à chaque chargement de `LoginViewController`. Nous effectuons cette opération en ajoutant ces méthodes à notre méthode `viewDidLoad` fournie par Apple.

```objc
  [super viewDidLoad];
  // Do any additional setup after loading the view.

  // OAuth2 Code

  self.loginView.delegate = self;
  [self requestOAuth2Access];
  [self setupOAuth2AccountStore];
  NSURLCache *URLCache =
      [[NSURLCache alloc] initWithMemoryCapacity:4 * 1024 * 1024
                                    diskCapacity:20 * 1024 * 1024
                                        diskPath:nil];
  [NSURLCache setSharedURLCache:URLCache];
```

Vous avez à présent terminé la création de la procédure principale qui nous permettra d’interagir avec notre application pour la connexion. Une fois connectés, nous aurons besoin d’utiliser les jetons que nous avons reçus. Pour cela, nous allons créer un code d’assistance qui appellera les API REST pour nous à l’aide de cette bibliothèque.


## Créez une classe `GraphAPICaller` pour gérer nos demandes vers une API REST.

Nous disposons d’une configuration qui est chargée chaque fois que nous chargeons notre application. À présent, nous devons en faire quelque chose une fois que nous possédons un jeton.

* Créez un fichier `GraphAPICaller.h`.

```objc
@interface GraphAPICaller : NSObject <NSURLConnectionDataDelegate>

+ (void)addTask:(Task *)task
completionBlock:(void (^)(bool, NSError *error))completionBlock;

+ (void)getTaskList:(void (^)(NSMutableArray *, NSError *error))completionBlock;

@end
```

Comme vous pouvez le voir à partir de ce code, nous allons créer deux méthodes : l’une pour obtenir les tâches auprès d’une API, et l’autre pour ajouter des tâches à l’API.

À présent que nous avons configuré notre interface, ajoutons l’implémentation réelle :

* Créez un fichier `GraphAPICaller.m file`.

```objc
@implementation GraphAPICaller

// 
// Gets the tasks from our REST endpoint we specified in settings
//

+ (void)getTaskList:(void (^)(NSMutableArray *, NSError *))completionBlock

{
  AppData *data = [AppData getInstance];

  NSString *taskURL =
      [NSString stringWithFormat:@"%@%@", data.taskApiString, @"/api/tasks"];

  NXOAuth2AccountStore *store = [NXOAuth2AccountStore sharedStore];
  NSMutableArray *Tasks = [[NSMutableArray alloc] init];

  NSArray *accounts = [store accountsWithAccountType:data.accountIdentifier];
  [NXOAuth2Request performMethod:@"GET"
      onResource:[NSURL URLWithString:taskURL]
      usingParameters:nil
      withAccount:accounts[0]
      sendProgressHandler:^(unsigned long long bytesSend,
                            unsigned long long bytesTotal) {
        // e.g., update a progress indicator
      }
      responseHandler:^(NSURLResponse *response, NSData *responseData,
                        NSError *error) {
        // Process the response
        if (!error) {
          NSDictionary *dataReturned =
              [NSJSONSerialization JSONObjectWithData:responseData
                                              options:0
                                                error:nil];
          NSLog(@"Graph Response was: %@", dataReturned);

          if ([dataReturned count] != 0) {

            for (NSMutableDictionary *theTask in dataReturned) {

              Task *t = [[Task alloc] init];
              t.name = [theTask valueForKey:@"Text"];

              [Tasks addObject:t];
            }
          }

          completionBlock(Tasks, nil);
        } else {
          completionBlock(nil, error);
        }

      }];
}

// 
// Adds a task from our REST endpoint we specified in settings
//

+ (void)addTask:(Task *)task
completionBlock:(void (^)(bool, NSError *error))completionBlock {

  AppData *data = [AppData getInstance];

  NSString *taskURL =
      [NSString stringWithFormat:@"%@%@", data.taskApiString, @"/api/tasks"];

  NXOAuth2AccountStore *store = [NXOAuth2AccountStore sharedStore];
  NSDictionary *params = [self convertParamsToDictionary:task.name];

  NSArray *accounts = [store accountsWithAccountType:data.accountIdentifier];
  [NXOAuth2Request performMethod:@"POST"
      onResource:[NSURL URLWithString:taskURL]
      usingParameters:params
      withAccount:accounts[0]
      sendProgressHandler:^(unsigned long long bytesSend,
                            unsigned long long bytesTotal) {
        // e.g., update a progress indicator
      }
      responseHandler:^(NSURLResponse *response, NSData *responseData,
                        NSError *error) {
        // Process the response
        if (responseData) {
          NSDictionary *dataReturned =
              [NSJSONSerialization JSONObjectWithData:responseData
                                              options:0
                                                error:nil];
          NSLog(@"Graph Response was: %@", dataReturned);

          completionBlock(TRUE, nil);
        } else {
          completionBlock(FALSE, error);
        }

      }];
}

+ (NSDictionary *)convertParamsToDictionary:(NSString *)task {
  NSMutableDictionary *dictionary = [[NSMutableDictionary alloc] init];

  [dictionary setValue:task forKey:@"Text"];

  return dictionary;
}

@end
```

## Exécution de l'exemple d'application

Pour terminer, générez et exécutez l’application dans Xcode. Inscrivez-vous ou connectez-vous à l’application, puis créez des tâches pour un utilisateur connecté. Déconnectez-vous et reconnectez-vous avec les identifiants d’un autre utilisateur, puis créez des tâches pour cet utilisateur.

Notez la façon dont les tâches sont stockées par utilisateur sur l’API, dans la mesure où l’API extrait l’identité de l’utilisateur à partir du jeton d’accès reçu.


## Étapes suivantes

Vous pouvez maintenant passer à des rubriques B2C plus poussées. Vous pouvez essayer :

[Appel d’une API web Node.js depuis une application web Node.js]()

[Personnalisation de l’expérience utilisateur pour une application B2C]()

<!---HONumber=AcomDC_1005_2016-->