<properties
	pageTitle="Application iOS Azure AD v2.0 | Microsoft Azure"
	description="Génération d’une application iOS qui connecte les utilisateurs à l’aide de leur compte Microsoft personnel et de leurs comptes professionnel ou scolaire à l’aide de bibliothèques tierces."
	services="active-directory"
	documentationCenter=""
	authors="brandwe"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="objective-c"
	ms.topic="article"
	ms.date="06/28/2016"
	ms.author="brandwe"/>

# Ajouter la connexion à une application iOS à l’aide d’une bibliothèque tierce avec l’API Graph utilisant le point de terminaison v2.0

La plateforme d’identité Microsoft utilise des normes ouvertes telles que OAuth2 et OpenID Connect. Cela permet aux développeurs de tirer parti de toute bibliothèque qu’ils souhaitent intégrer à nos services. Pour aider les développeurs à utiliser notre plateforme avec d’autres bibliothèques, nous avons rédigé quelques procédures pas à pas comme celle-ci, afin d’expliquer la configuration des bibliothèques tierces pour se connecter à la plateforme d’identité Microsoft. La plupart des bibliothèques qui implémentent [la spécification RFC6749 OAuth2](https://tools.ietf.org/html/rfc6749) seront en mesure de se connecter à la plateforme Microsoft Identity.

Cette application permet à un utilisateur de se connecter à son entreprise, puis de rechercher d’autres utilisateurs dans son entreprise à l’aide de l’API Graph.

Si vous découvrez OAuth2 ou OpenID Connect, cet exemple de configuration n’est peut-être pas très parlant pour vous. Nous vous recommandons de consulter une brève [vue d’ensemble du protocole que nous avons décrit ici](active-directory-v2-protocols-oauth-code.md).


> [AZURE.NOTE]
    Certaines fonctionnalités de notre plateforme qui ont une expression dans ces normes, comme la gestion de la stratégie d’accès conditionnel et d’Intune, requièrent l’utilisation de nos bibliothèques d’identité Microsoft Azure open source.

> [AZURE.NOTE] 
    Les scénarios et les fonctionnalités Azure Active Directory ne sont pas tous pris en charge par le point de terminaison v2.0. Pour déterminer si vous devez utiliser le point de terminaison v2.0, consultez les [limites de v2.0](active-directory-v2-limitations.md).

## Télécharger
Le code associé à ce didacticiel est stocké [sur GitHub](https://github.com/Azure-Samples/active-directory-ios-native-nxoauth2-v2). Pour suivre la procédure, vous pouvez [télécharger la structure de l’application au format .zip](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet/archive/skeleton.zip) ou la cloner :

```
git clone --branch skeleton git@github.com:Azure-Samples/active-directory-ios-native-nxoauth2-v2.git
```

Sinon, téléchargez et commencez dès maintenant :

```
git clone git@github.com:Azure-Samples/active-directory-ios-native-nxoauth2-v2.git
```

## Inscription d’une application
Créez une application à l’adresse [apps.dev.microsoft.com](https://apps.dev.microsoft.com), ou suivez cette [procédure détaillée](active-directory-v2-app-registration.md). Veillez à respecter les points suivants :

- copier l'**ID d'application** attribué à votre application, vous en aurez bientôt besoin ;
- ajouter la plateforme **Mobile** pour votre application ;
- copier l'**URI de redirection** à partir du portail. Vous devez utiliser la valeur par défaut de `urn:ietf:wg:oauth:2.0:oob`.


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
 
 target 'QuickStart' do
 
 pod 'NXOAuth2Client'
 
 end
```

Chargez maintenant le podfile à l’aide de Cocoapods. Cette opération crée un nouvel espace de travail XCode que vous allez charger.

```
$ pod install
...
$ open QuickStart.xcworkspace
```

## La structure du projet

Nous avons défini la structure suivante pour notre projet dans le squelette :

* Une vue principale avec une recherche de noms UPN
* Une vue détaillée pour les données concernant l’utilisateur sélectionné
* Une vue de connexion permettant à un utilisateur de se connecter à l’application pour interroger le graphique.

Nous accéderons à différents fichiers dans le squelette pour ajouter l’authentification. D’autres parties du code, comme le code de l’élément visuel, ne sont pas associées à l’identité et sont fournies pour vous.

## Configurer le fichier settings.plst dans la bibliothèque

-	Dans le projet de démarrage rapide, ouvrez le fichier plist `settings.plist`. Remplacez les valeurs des éléments de la section afin qu’elles reflètent les valeurs que vous avez saisies dans le portail Azure. Votre code se réfère à ces valeurs chaque fois qu’il utilise la bibliothèque ADAL.
    -	`clientId` est l’ID client de votre application que vous avez copié à partir du portail.
    -	`redirectUri` est l’URL de redirection fournie par le portail.

## Configurer la bibliothèque NXOAuth2Client dans votre LoginViewController

La bibliothèque NXOAuthClient requiert des valeurs pour sa configuration. Une fois cette opération terminée, vous pouvez utiliser le jeton acquis pour appeler l’API Graph. Étant donné que nous savons que l’élément `LoginView` sera appelé chaque fois que nous avons besoin de l’authentification, il est judicieux de placer nos valeurs de configuration dans ce fichier.
* Ouvrir le fichier `LoginViewController.m`

* Ajoutons quelques valeurs dans le code qui définit le contexte pour l’authentification et l’autorisation. Je vais fournir des explications détaillées ci-dessous.

```objc
NSString *scopes = @"offline_access User.ReadBasic.All";
NSString *authURL = @"https://login.microsoftonline.com/common/oauth2/v2.0/authorize";
NSString *loginURL = @"https://login.microsoftonline.com/common/login";
NSString *bhh = @"urn:ietf:wg:oauth:2.0:oob?code=";
NSString *tokenURL = @"https://login.microsoftonline.com/common/oauth2/v2.0/token";
NSString *keychain = @"com.microsoft.azureactivedirectory.samples.graph.QuickStart";
static NSString * const kIDMOAuth2SuccessPagePrefix = @"session_state=";
NSURL *myRequestedUrl;
NSURL *myLoadedUrl;
bool loginFlow = FALSE;
bool isRequestBusy;
NSURL *authcode;
```

Penchez-nous plus avant sur ces éléments.

La première chaîne que nous configurons concerne `scopes`. L’étendue que nous allons demander pour cette application est `User.ReadBasic.All`, ce qui nous permet de lire le profil de base de tous les utilisateurs dans notre répertoire. Vous pouvez en savoir plus sur toutes les étendues disponibles pour [l’utilisation avec Microsoft Graph ici](https://graph.microsoft.io/docs/authorization/permission_scopes).

Pour `authURL`, `loginURL`, `bhh`, `tokenURL`, utilisez les valeurs indiquées ci-dessus. Si vous utilisez nos bibliothèques d’identité Microsoft Azure open source, nous extrairons ces données pour vous à l’aide de notre point de terminaison des métadonnées. Nous avons effectué le travail d’extraction de ces valeurs pour vous.

La valeur `keychain` est le conteneur qu’utilisera la bibliothèque NXOAuth2Client pour créer un porte-clés pour stocker vos jetons. Si vous souhaitez obtenir l’authentification unique entre de nombreuses applications, vous pouvez spécifier le même porte-clés dans chacune de vos applications, ainsi que demander l’utilisation de ce porte-clés dans vos droits Xcode. Cela est décrit dans la documentation Apple.

Le reste de ces valeurs est requis pour utiliser la bibliothèque et simplement créer des emplacements pour que vous puissiez traiter des valeurs dans le contexte.

* Créer un cache d’URL

À l’intérieur de l’élément `(void)viewDidLoad()`, qui est toujours appelé une fois que la vue est chargée, nous préparons un cache pour notre utilisation.

Ajoutez le code suivant :

```objc
- (void)viewDidLoad {
    [super viewDidLoad];
    self.loginView.delegate = self;
    [self setupOAuth2AccountStore];
    [self requestOAuth2Access];
    NSURLCache *URLCache = [[NSURLCache alloc] initWithMemoryCapacity:4 * 1024 * 1024
                                                         diskCapacity:20 * 1024 * 1024
                                                             diskPath:nil];
    [NSURLCache setSharedURLCache:URLCache];
    
}
```

* Créez un affichage web que nous allons utiliser pour la connexion.

Nous utilisons un affichage web pour la connexion du compte. Cela nous permet d’inviter l’utilisateur à utiliser des facteurs supplémentaires comme les SMS (s’ils sont configurés) ou de renvoyer les messages d’erreur à l’utilisateur. Ici, nous allons définir l’affichage web, puis écrire ultérieurement le code pour gérer les rappels qui surviendront dans l’affichage web à partir du service d’identité Microsoft.

```objc
-(void)requestOAuth2Access {
    //in order to login to Mircosoft APIs using OAuth2 we must show an embedded browser (UIWebView)
    [[NXOAuth2AccountStore sharedStore] requestAccessToAccountWithType:@"myGraphService"
                                   withPreparedAuthorizationURLHandler:^(NSURL *preparedURL) {
                                       //navigate to the URL returned by NXOAuth2Client
                                       
                                       NSURLRequest *r = [NSURLRequest requestWithURL:preparedURL];
                                       [self.loginView loadRequest:r];
                                   }];
}
```

* Substituer les méthodes d’affichage web pour gérer l’authentification

Nous devons indiquer à l’affichage web le comportement souhaité lorsqu’un utilisateur doit se connecter comme évoqué ci-dessus. Vous pouvez simplement couper et coller le code ci-dessous.

```objc
- (void)resolveUsingUIWebView:(NSURL *)URL {
    
    // We get the auth token from a redirect so we need to handle that in the webview.
    
    if (![NSThread isMainThread]) {
        [self performSelectorOnMainThread:@selector(resolveUsingUIWebView:) withObject:URL waitUntilDone:YES];
        return;
    }
    
    NSURLRequest *hostnameURLRequest = [NSURLRequest requestWithURL:URL cachePolicy:NSURLRequestUseProtocolCachePolicy timeoutInterval:10.0f];
    isRequestBusy = YES;
    [self.loginView loadRequest:hostnameURLRequest];
    
    NSLog(@"resolveUsingUIWebView ready (status: UNKNOWN, URL: %@)", self.loginView.request.URL);
}

- (BOOL)webView:(UIWebView *)webView shouldStartLoadWithRequest:(NSURLRequest *)request navigationType:(UIWebViewNavigationType)navigationType {
    
    NSLog(@"webView:shouldStartLoadWithRequest: %@ (%li)", request.URL, (long)navigationType);
    
    // The webview is where all the communication happens. Slightly complicated.
    
    myLoadedUrl = [webView.request mainDocumentURL];
    NSLog(@"***Loaded url: %@", myLoadedUrl);
    
    //if the UIWebView is showing our authorization URL or consent URL, show the UIWebView control
    if ([request.URL.absoluteString rangeOfString:authURL options:NSCaseInsensitiveSearch].location != NSNotFound) {
        self.loginView.hidden = NO;
    } else if ([request.URL.absoluteString rangeOfString:loginURL options:NSCaseInsensitiveSearch].location != NSNotFound) {
        //otherwise hide the UIWebView, we've left the authorization flow
        self.loginView.hidden = NO;
    } else if ([request.URL.absoluteString rangeOfString:bhh options:NSCaseInsensitiveSearch].location != NSNotFound) {
        //otherwise hide the UIWebView, we've left the authorization flow
        self.loginView.hidden = YES;
        [[NXOAuth2AccountStore sharedStore] handleRedirectURL:request.URL];
    }
    else {
        self.loginView.hidden = NO;
        //read the Location from the UIWebView, this is how Microsoft APIs is returning the
        //authentication code and relation information. This is controlled by the redirect URL we chose to use from Microsoft APIs
        //continue the OAuth2 flow
       // [[NXOAuth2AccountStore sharedStore] handleRedirectURL:request.URL];
    }
    
    return YES;

}
```

* Écrire du code pour gérer le résultat de la demande OAuth2

Nous avons besoin de code qui gère l’URL de redirection renvoyée à partir de l’affichage web. Si elle n’a pas réussi, nous essaierons à nouveau. En attendant, la bibliothèque indique l’erreur que vous pouvez voir dans la console ou gérer de façon asynchrone.

```objc
- (void)handleOAuth2AccessResult:(NSString *)accessResult {
    
    AppData* data = [AppData getInstance];
    
    //parse the response for success or failure
     if (accessResult)
    //if success, complete the OAuth2 flow by handling the redirect URL and obtaining a token
     {
         [[NXOAuth2AccountStore sharedStore] handleRedirectURL:accessResult];
    } else {
        //start over
        [self requestOAuth2Access];
    }
}
```

* Définir le contexte OAuth (appelé magasin de comptes)

Ici, vous pouvez appeler `-[NXOAuth2AccountStore setClientID:secret:authorizationURL:tokenURL:redirectURL:forAccountType:]` sur le magasin de comptes partagé pour chaque service auquel vous souhaitez pouvoir accéder depuis votre application. Le type de compte est une chaîne qui est utilisée comme identificateur pour un certain service. Étant donné que nous accédons à l’API Graph, nous allons continuer et l’appeler `"myGraphService"`. Ensuite, nous allons configurer un observateur qui nous indique les modifications avec le jeton. Une fois que nous obtenons le jeton, nous renvoyons l’utilisateur vers `masterView`.



```objc
- (void)setupOAuth2AccountStore {
    

        AppData* data = [AppData getInstance];
    
    [[NXOAuth2AccountStore sharedStore] setClientID:data.clientId
                                             secret:data.secret
                                              scope:[NSSet setWithObject:scopes]
                                   authorizationURL:[NSURL URLWithString:authURL]
                                           tokenURL:[NSURL URLWithString:tokenURL]
                                        redirectURL:[NSURL URLWithString:data.redirectUriString]
                                      keyChainGroup: keychain
                                     forAccountType:@"myGraphService"];
    
    [[NSNotificationCenter defaultCenter] addObserverForName:NXOAuth2AccountStoreAccountsDidChangeNotification
                                                      object:[NXOAuth2AccountStore sharedStore]
                                                       queue:nil
                                                  usingBlock:^(NSNotification *aNotification) {
                                                      if (aNotification.userInfo) {
                                                          //account added, we have access
                                                          //we can now request protected data
                                                          NSLog(@"Success!! We have an access token.");
                                                          dispatch_async(dispatch_get_main_queue(),^ {
                                                              
                                                              MasterViewController* masterViewController = [self.storyboard instantiateViewControllerWithIdentifier:@"masterView"];
                                                              [self.navigationController pushViewController:masterViewController animated:YES];
                                                          });
                                                      } else {
                                                          //account removed, we lost access
                                                      }
                                                  }];
    
    [[NSNotificationCenter defaultCenter] addObserverForName:NXOAuth2AccountStoreDidFailToRequestAccessNotification
                                                      object:[NXOAuth2AccountStore sharedStore]
                                                       queue:nil
                                                  usingBlock:^(NSNotification *aNotification) {
                                                      NSError *error = [aNotification.userInfo objectForKey:NXOAuth2AccountStoreErrorKey];
                                                      NSLog(@"Error!! %@", error.localizedDescription);
                                                  }];
}
```

## Configurer la vue principale pour rechercher et afficher les utilisateurs de l’API Graph

Une application MVC qui affiche les données retournées dans la grille dépasse le cadre de cette procédure pas à pas ; il existe plusieurs didacticiels en ligne pour expliquer comment en créer une. Nous fournissons tout ce code pour vous dans le fichier squelette. Toutefois, nous devons traiter quelques éléments dans cette application MVC :

* Intercepter lorsqu’un utilisateur saisit des données dans le champ de recherche
* Fournir un objet de données vers la vue principale afin d’afficher les résultats dans la grille

Nous effectuerons ces actions ci-dessous.

* Ajouter une vérification pour voir si nous sommes connectés

L’application ne fait pas grand-chose si l’utilisateur n’est pas connecté ; par conséquent, il est judicieux de vérifier s’il existe déjà un jeton dans notre cache. Si ce n’est pas le cas, nous redirigeons vers la vue de connexion pour que l’utilisateur se connecte. Rappelez-vous : la meilleure façon d’effectuer des actions lorsqu’une vue se charge consiste à utiliser la méthode `viewDidLoad()` fournie par Apple.

```objc
- (void)viewDidLoad {
    [super viewDidLoad];
    
    
    NXOAuth2AccountStore *store = [NXOAuth2AccountStore sharedStore];
    NSArray *accounts = [store accountsWithAccountType:@"myGraphService"];
    
        if (accounts.count == 0) {
        
        dispatch_async(dispatch_get_main_queue(),^ {
            
            LoginViewController* userSelectController = [self.storyboard instantiateViewControllerWithIdentifier:@"LoginUserView"];
            [self.navigationController pushViewController:userSelectController animated:YES];
        });
        }
```

* Mettre à jour la vue de table lors de la réception de données

Lorsque nous obtenons des données à partir de l’API Graph, nous devons afficher les données retournées. Pour plus de simplicité, voici l’ensemble du code permettant de mettre à jour la table. Vous pouvez simplement coller les valeurs appropriées dans votre code réutilisable MVC.

```objc
#pragma mark - Table View

- (NSInteger)numberOfSectionsInTableView:(UITableView *)tableView {
    return 1;
}

- (NSInteger)tableView:(UITableView *)tableView numberOfRowsInSection:(NSInteger)section {

        return [upnArray count];
}

- (UITableViewCell *)tableView:(UITableView *)tableView cellForRowAtIndexPath:(NSIndexPath *)indexPath {
    
    UITableViewCell *cell = [tableView dequeueReusableCellWithIdentifier:@"TaskPrototypeCell" forIndexPath:indexPath];
    
    if ( cell == nil ) {
        cell = [[UITableViewCell alloc] initWithStyle:UITableViewCellStyleDefault reuseIdentifier:@"TaskPrototypeCell"];
    }
    
    User *user = nil;
     user = [upnArray objectAtIndex:indexPath.row];

    
    // Configure the cell
    cell.textLabel.text = user.name;
    [cell setAccessoryType:UITableViewCellAccessoryDisclosureIndicator];
    
    return cell;
}

```

* Fournir un moyen d’appeler l’API Graph lorsqu’un utilisateur tape dans le champ de recherche

Lorsqu’un utilisateur tape une recherche dans la zone, nous devons l’indiquer à l’API Graph. Pour mieux séparer la fonctionnalité de recherche de la présentation, nous créons ci-dessous une autre classe appelée `GraphAPICaller`. Pour l’instant, continuons avec l’écriture du code pour autoriser l’insertion des caractères de recherche dans l’API Graph. Pour ce faire, nous allons fournir une méthode appelée `lookupInGraph` qui accepte la chaîne que nous souhaitons rechercher.

```objc

-(void)lookupInGraph:(NSString *)searchText {
if (searchText.length > 0) {
        
    };
    

    
        [GraphAPICaller searchUserList:searchText completionBlock:^(NSMutableArray* returnedUpns, NSError* error) {
            if (returnedUpns) {
                
                
                upnArray = returnedUpns;
                
                
            }
            else
            {
                UIAlertView *alertView = [[UIAlertView alloc]initWithTitle:nil message:[[NSString alloc]initWithFormat:@"Error : %@", error.localizedDescription] delegate:nil cancelButtonTitle:@"Retry" otherButtonTitles:@"Cancel", nil];
                
                [alertView setDelegate:self];
                
                dispatch_async(dispatch_get_main_queue(),^ {
                    [alertView show];
                });
            }
            
            
        }];
    
    
} 
```

## Écrire une classe d’assistance pour accéder à l’API Graph

Il s’agit de l’essentiel de l’application. Le reste consistait à insérer du code dans le modèle MVC par défaut d’Apple, mais ici nous écrivons du code pour interroger le graphique pendant la saisie de l’utilisateur et nous retournons ces données. Nous afficherons le code, puis nous l’expliquerons en détail.

* Créez un fichier d’en-tête Objective C appelé `GraphAPICaller.h` avec le code suivant :

```objc
@interface GraphAPICaller : NSObject<NSURLConnectionDataDelegate>

+(void) searchUserList:(NSString*)searchString
       completionBlock:(void (^) (NSMutableArray*, NSError* error))completionBlock;

@end
```

Vous voyez ici que nous spécifions une méthode qui prend une chaîne et retourne un completionBlock. Ce completionBlock, comme vous l’avez peut-être deviné, va mettre à jour la table en fournissant un objet avec des données renseignées en temps réel pendant la recherche de l’utilisateur.


* Créer un fichier Objective C appelé `GraphAPICaller.m` et ajoutez la méthode suivante :

```objc
+(void) searchUserList:(NSString*)searchString
       completionBlock:(void (^) (NSMutableArray* Users, NSError* error)) completionBlock
{
    if (!loadedApplicationSettings)
    {
        [self readApplicationSettings];
    }
    
    AppData* data = [AppData getInstance];
    
    NSString *graphURL = [NSString stringWithFormat:@"%@%@/users", data.graphApiUrlString, data.apiversion];
    
    NXOAuth2AccountStore *store = [NXOAuth2AccountStore sharedStore];
    NSDictionary* params = [self convertParamsToDictionary:searchString];
    
    NSArray *accounts = [store accountsWithAccountType:@"myGraphService"];
    [NXOAuth2Request performMethod:@"GET"
                        onResource:[NSURL URLWithString:graphURL]
                   usingParameters:params
                       withAccount:accounts[0]
               sendProgressHandler:^(unsigned long long bytesSend, unsigned long long bytesTotal) {
                   // e.g., update a progress indicator
               }
                   responseHandler:^(NSURLResponse *response, NSData *responseData, NSError *error) {
                       // Process the response
                       if (responseData) {
                           NSError *error;
                           NSDictionary *dataReturned = [NSJSONSerialization JSONObjectWithData:responseData options:0 error:nil];
                           NSLog(@"Graph Response was: %@", dataReturned);
                           
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
                               s.name =[keyValuePairs valueForKey:@"displayName"];
                               s.mail =[keyValuePairs valueForKey:@"mail"];
                               s.businessPhones =[keyValuePairs valueForKey:@"businessPhones"];
                               s.mobilePhones =[keyValuePairs valueForKey:@"mobilePhone"];
                               
                               
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

```

Examinons cette méthode en détail

L’essentiel de ce code se trouve dans la méthode `NXOAuth2Request` qui prend les paramètres que nous avons définis tôt à l’intérieur du fichier settings.plist. Notre première étape consiste à construire l’appel approprié de l’API Graph. Étant donné que nous appelons `/users`, nous l’indiquons en l’ajoutant (ainsi que sa version) à notre ressource API Graph. Il est judicieux de placer ces éléments dans un environnement externe de paramètres, car ceux-ci peuvent changer à mesure que l’API évolue.


```objc
NSString *graphURL = [NSString stringWithFormat:@"%@%@/users", data.graphApiUrlString, data.apiversion];
```

Ensuite, nous devons spécifier les paramètres que nous allons également fournir à l’appel de l’API Graph. Il est *très important* que vous ne placiez pas les paramètres dans le point de terminaison de ressource, car il est modifié pour tous les caractères non URI lors de l’exécution. Tout code de requête doit être fourni dans les paramètres.

```objc

NSDictionary* params = [self convertParamsToDictionary:searchString];
```

Vous remarquerez peut-être que cela appelle une méthode `convertParamsToDictionary` que nous n’avons pas encore écrite. Faisons-le maintenant à la fin du fichier :

```objc
+(NSDictionary*) convertParamsToDictionary:(NSString*)searchString
{
    NSMutableDictionary* dictionary = [[NSMutableDictionary alloc]init];
    
        NSString *query = [NSString stringWithFormat:@"startswith(givenName, '%@')", searchString];
    
           [dictionary setValue:query forKey:@"$filter"];


    
    return dictionary;
}

```
Ensuite, nous utiliserons la méthode `NXOAuth2Request` pour obtenir des données de l’API au format JSON.

```objc
NSArray *accounts = [store accountsWithAccountType:@"myGraphService"];
    [NXOAuth2Request performMethod:@"GET"
                        onResource:[NSURL URLWithString:graphURL]
                   usingParameters:params
                       withAccount:accounts[0]
               sendProgressHandler:^(unsigned long long bytesSend, unsigned long long bytesTotal) {
                   // e.g., update a progress indicator
               }
                   responseHandler:^(NSURLResponse *response, NSData *responseData, NSError *error) {
                       // Process the response
                       if (responseData) {
                           NSError *error;
                           NSDictionary *dataReturned = [NSJSONSerialization JSONObjectWithData:responseData options:0 error:nil];
                           NSLog(@"Graph Response was: %@", dataReturned);
                           
                           // We can grab the top most JSON node to get our graph data.
                           NSArray *graphDataArray = [dataReturned objectForKey:@"value"];
```

Enfin, nous verrons comment nous retournons les données vers MasterViewController. Les données nous reviennent sérialisées et doivent être désérialisées et chargées dans un objet que MainViewController peut consommer. À cet effet, le squelette dispose d’un fichier `User.m/h` qui crée un objet Utilisateur. Nous renseignons cet objet utilisateur avec les informations du graphique.

```objc
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
                               s.name =[keyValuePairs valueForKey:@"displayName"];
                               s.mail =[keyValuePairs valueForKey:@"mail"];
                               s.businessPhones =[keyValuePairs valueForKey:@"businessPhones"];
                               s.mobilePhones =[keyValuePairs valueForKey:@"mobilePhone"];
                               
                               
                               [Users addObject:s];
```


## Exécution de l'exemple

Si vous avez utilisé le squelette ou suivi la procédure pas à pas, votre application doit maintenant s’exécuter. Lancez le simulateur et cliquez sur « Connexion » pour utiliser l’application.

## Obtenir des mises à jour de sécurité pour notre produit

Nous vous encourageons à activer les notifications d’incidents de sécurité en vous rendant sur [cette page](https://technet.microsoft.com/security/dd252948) et en vous abonnant aux alertes d’avis de sécurité.

<!---HONumber=AcomDC_0629_2016-->