<properties
	pageTitle="Utilisation du Kit de développement logiciel (SDK) iOS pour Azure Mobile Apps"
	description="Utilisation du Kit de développement logiciel (SDK) iOS pour Azure Mobile Apps"
	services="app-service\mobile"
	documentationCenter="ios"
	authors="krisragh"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="objective-c"
	ms.topic="article"
	ms.date="03/09/2016"
	ms.author="krisragh"/>

# Utilisation de la bibliothèque cliente iOS pour Azure Mobile Apps

[AZURE.INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

Ce guide indique le déroulement de scénarios courants dans le cadre de l’utilisation du dernier [Kit de développement logiciel (SDK) iOS Azure Mobile Apps](https://go.microsoft.com/fwLink/?LinkID=266533&clcid=0x409). Si vous ne connaissez pas Azure Mobile Apps, consultez d’abord la section [Démarrage rapide d’Azure Mobile Apps] pour créer un backend, créer une table et télécharger un projet Xcode iOS prédéfini. Dans ce guide, nous nous concentrons sur le SDK iOS côté client. Pour en savoir plus sur le Kit de développement logiciel (SDK) côté serveur .NET pour le backend, consultez [Fonctionnement avec le serveur principal .NET](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md)

## Documentation de référence

La documentation de référence du Kit de développement logiciel (SDK) client iOS se trouve ici : [Référence du client iOS Azure Mobile Apps](http://azure.github.io/azure-mobile-services/iOS/v3/).

##<a name="Setup"></a>Configuration et conditions préalables

Ce guide part du principe que vous avez créé un serveur principal avec une table. Ce guide suppose que la table a le même schéma que les tables dans ces didacticiels. Ce guide suppose également que dans votre code, vous référencez `MicrosoftAzureMobile.framework` et importez `MicrosoftAzureMobile/MicrosoftAzureMobile.h`.

##<a name="create-client"></a>Création du client

Pour accéder à un backend Azure Mobile Apps dans votre projet, créez un `MSClient`. Remplacez `AppUrl` par l’URL de l’application. Vous pouvez laisser `gatewayURLString` et `applicationKey` vides. Si vous avez configuré une passerelle pour l’authentification, renseignez `gatewayURLString` avec l’URL de la passerelle.

**Objective-C** :

```
MSClient *client = [MSClient clientWithApplicationURLString:@"AppUrl"];
```

**Swift** :

```
let client = MSClient(applicationURLString: "AppUrl")
```


##<a name="table-reference"></a>Procédure : création d'une référence de table

Pour accéder aux données ou les mettre à jour, créez une référence à la table principale. Remplacez `TodoItem` par le nom de votre table.

**Objective-C** :

```
MSTable *table = [client tableWithName:@"TodoItem"];
```

**Swift** :

```
let table = client.tableWithName("TodoItem")
```


##<a name="querying"></a>Procédure : interrogation des données

Pour créer une requête de base de données, interrogez l'objet `MSTable`. La requête suivante obtient tous les éléments dans `TodoItem` et enregistre le texte de chaque élément.

**Objective-C** :

```
[table readWithCompletion:^(MSQueryResult *result, NSError *error) {
		if(error) { // error is nil if no error occured
				NSLog(@"ERROR %@", error);
		} else {
				for(NSDictionary *item in result.items) { // items is NSArray of records that match query
						NSLog(@"Todo Item: %@", [item objectForKey:@"text"]);
				}
		}
}];
```

**Swift** :

```
table.readWithCompletion { (result, error) in
    if let err = error {
        print("ERROR ", err)
    } else if let items = result?.items {
        for item in items {
            print("Todo Item: ", item["text"])
        }
    }
}
```

##<a name="filtering"></a>Procédure : filtrage des données renvoyées

Pour filtrer les résultats, il existe de nombreuses options.

Pour filtrer à l'aide d'un prédicat, utilisez un `NSPredicate` et `readWithPredicate`. Les filtres suivants retournent des données pour rechercher uniquement les éléments Todo incomplets.

**Objective-C** :

```
// Create a predicate that finds items where complete is false
NSPredicate * predicate = [NSPredicate predicateWithFormat:@"complete == NO"];
// Query the TodoItem table
[table readWithPredicate:predicate completion:^(MSQueryResult *result, NSError *error) {
		if(error) {
				NSLog(@"ERROR %@", error);
		} else {
				for(NSDictionary *item in result.items) {
						NSLog(@"Todo Item: %@", [item objectForKey:@"text"]);
				}
		}
}];
```

**Swift** :

```
// Create a predicate that finds items where complete is false
let predicate =  NSPredicate(format: "complete == NO")
// Query the TodoItem table
table.readWithPredicate(predicate) { (result, error) in
    if let err = error {
        print("ERROR ", err)
    } else if let items = result?.items {
        for item in items {
            print("Todo Item: ", item["text"])
        }
    }
}
```

##<a name="query-object"></a>Procédure : utilisation de MSQuery

Pour effectuer une requête complexe (y compris le tri et la pagination), créez un objet `MSQuery`, directement ou en utilisant un prédicat :

**Objective-C** :

```
MSQuery *query = [table query];
MSQuery *query = [table queryWithPredicate: [NSPredicate predicateWithFormat:@"complete == NO"]];
```

**Swift** :

```
let query = table.query()
let query = table.queryWithPredicate(NSPredicate(format: "complete == NO"))
```

`MSQuery` vous permet de contrôler plusieurs comportements de requête, y compris les suivants. Exécutez une requête `MSQuery` en appelant `readWithCompletion`, comme illustré dans l’exemple suivant.
* Spécifier l’ordre des résultats
* Limiter les champs à renvoyer
* Limiter le nombre d’enregistrements à renvoyer
* Spécifier le nombre total dans la réponse
* Spécifier des paramètres de chaîne de requête personnalisés dans la requête
* Appliquer des fonctions supplémentaires


## <a name="sorting"></a>Procédure : trier des données avec MSQuery

Pour trier les résultats, examinons un exemple. Pour trier dans l'ordre croissant sur le champ `text`, puis décroissant sur le champ `completion`, appelez `MSQuery` comme suit :

**Objective-C** :

```
[query orderByAscending:@"text"];
[query orderByDescending:@"complete"];
[query readWithCompletion:^(MSQueryResult *result, NSError *error) {
		if(error) {
				NSLog(@"ERROR %@", error);
		} else {
				for(NSDictionary *item in result.items) {
						NSLog(@"Todo Item: %@", [item objectForKey:@"text"]);
				}
		}
}];
```

**Swift** :

```
query.orderByAscending("text")
query.orderByDescending("complete")
query.readWithCompletion { (result, error) in
    if let err = error {
        print("ERROR ", err)
    } else if let items = result?.items {
        for item in items {
            print("Todo Item: ", item["text"])
        }
    }
}
```


## <a name="selecting"></a><a name="parameters"></a>Procédure : limitation des champs et développement des paramètres de chaîne de requête avec MSQuery

Pour limiter les champs à retourner dans une requête, spécifiez les noms des champs dans la propriété **selectFields**. Le code suivant renvoie uniquement les champs text et completed :

**Objective-C** :

```
query.selectFields = @[@"text", @"complete"];
```

**Swift** :

```
query.selectFields = ["text", "complete"]
```

Pour inclure des paramètres de chaîne de requête supplémentaires dans la demande serveur (par exemple, si un script côté serveur personnalisé les utilise), remplissez `query.parameters` comme suit :

**Objective-C** :

```
query.parameters = @{
	@"myKey1" : @"value1",
	@"myKey2" : @"value2",
};
```

**Swift** :

```
query.parameters = ["myKey1": "value1", "myKey2": "value2"]
```

##<a name="inserting"></a>Procédure : insertion de données

Pour insérer une nouvelle ligne de table, créez un `NSDictionary` et appelez `table insert`. Mobile Services génère automatiquement de nouvelles colonnes basées sur le `NSDictionary` si le [schéma dynamique] n'est pas désactivé.

Si `id` n'est pas fourni, le backend génère automatiquement un nouvel ID unique. Fournissez votre propre `id` pour utiliser les adresses de messagerie électronique, les noms d'utilisateurs, ou vos propres valeurs personnalisées sous la forme d'ID. Fournir son propre ID peut faciliter les jointures et la logique de base de données orientée métier.

L’élément `result` contient le nouvel élément qui a été inséré ; selon la logique du serveur, il peut afficher des données supplémentaires ou modifiées par rapport à ce qui a été transmis au serveur.

**Objective-C** :

```
NSDictionary *newItem = @{@"id": @"custom-id", @"text": @"my new item", @"complete" : @NO};
[table insert:newItem completion:^(NSDictionary *result, NSError *error) {
	if(error) {
		NSLog(@"ERROR %@", error);
	} else {
		NSLog(@"Todo Item: %@", [result objectForKey:@"text"]);
	}
}];
```

**Swift** :

```
let newItem = ["id": "custom-id", "text": "my new item", "complete": false]
table.insert(newItem) { (result, error) in
    if let err = error {
        print("ERROR ", err)
    } else if let item = result {
        print("Todo Item: ", item["text"])
    }
}
```

##<a name="modifying"></a>Procédure : modification des données

Pour mettre à jour une ligne existante, modifiez un élément et appelez `update` :

**Objective-C** :

```
NSMutableDictionary *newItem = [oldItem mutableCopy]; // oldItem is NSDictionary
[newItem setValue:@"Updated text" forKey:@"text"];
[table update:newItem completion:^(NSDictionary *result, NSError *error) {
	if(error) {
		NSLog(@"ERROR %@", error);
	} else {
		NSLog(@"Todo Item: %@", [result objectForKey:@"text"]);
	}
}];
```

**Swift** :

```
if let newItem = oldItem.mutableCopy() as? NSMutableDictionary {
    newItem["text"] = "Updated text"
    table2.update(newItem as [NSObject: AnyObject], completion: { (result, error) -> Void in
        if let err = error {
            print("ERROR ", err)
        } else if let item = result {
            print("Todo Item: ", item["text"])
        }
    })
}
```

Vous pouvez également fournir l'ID de la ligne et le champ mis à jour :

**Objective-C** :

```
[table update:@{@"id":@"custom-id", @"text":"my EDITED item"} completion:^(NSDictionary *result, NSError *error) {
	if(error) {
		NSLog(@"ERROR %@", error);
	} else {
		NSLog(@"Todo Item: %@", [result objectForKey:@"text"]);
	}
}];
```

**Swift** :

```
table.update(["id": "custom-id", "text": "my EDITED item"]) { (result, error) in
    if let err = error {
        print("ERROR ", err)
    } else if let item = result {
        print("Todo Item: ", item["text"])
    }
}
```

Au minimum, l'attribut `id` doit être défini quand vous effectuez des mises à jour.

##<a name="deleting"></a>Procédure : suppression de données

Pour supprimer un élément, appelez `delete` avec l'élément :

**Objective-C** :

```
[table delete:item completion:^(id itemId, NSError *error) {
	if(error) {
		NSLog(@"ERROR %@", error);
	} else {
		NSLog(@"Todo Item ID: %@", itemId);
	}
}];
```

**Swift** :

```
table.delete(newItem as [NSObject: AnyObject]) { (itemId, error) in
    if let err = error {
        print("ERROR ", err)
    } else {
        print("Todo Item ID: ", itemId)
    }
}
```

Vous pouvez également effectuer la suppression en fournissant un ID de ligne :

**Objective-C** :

```
[table deleteWithId:@"37BBF396-11F0-4B39-85C8-B319C729AF6D" completion:^(id itemId, NSError *error) {
	if(error) {
		NSLog(@"ERROR %@", error);
	} else {
		NSLog(@"Todo Item ID: %@", itemId);
	}
}];
```

**Swift** :

```
table.deleteWithId("37BBF396-11F0-4B39-85C8-B319C729AF6D") { (itemId, error) in
    if let err = error {
        print("ERROR ", err)
    } else {
        print("Todo Item ID: ", itemId)
    }
}
```

Au minimum, l'attribut `id` doit être défini quand vous effectuez des suppressions.

##<a name="customapi"></a>Procédure : appel d’une API personnalisée

Une API personnalisée vous permet d’exposer toutes les fonctionnalités du serveur principal. Il n’est pas nécessaire de la mapper à une opération de table. Non seulement vous avez davantage de contrôle sur la messagerie, mais vous pouvez également lire/définir des en-têtes et modifier le format du corps de réponse. Pour savoir comment créer une API personnalisée sur le serveur principal, consultez la rubrique [API personnalisées](app-service-mobile-node-backend-how-to-use-server-sdk.md#work-easy-apis)

Pour appeler une API personnalisée, appelez la commande `MSClient.invokeAPI` comme indiqué ci-dessous. Le contenu de la requête et de la réponse est traité au format JSON. Pour utiliser d’autres types de média, [utilisez l’autre surcharge de `invokeAPI`](http://azure.github.io/azure-mobile-services/iOS/v3/Classes/MSClient.html#//api/name/invokeAPI:data:HTTPMethod:parameters:headers:completion:)

Pour exécuter une requête `GET` à la place d’une requête `POST`, définissez le paramètre `HTTPMethod` sur `"GET"` et le paramètre `body` sur `nil` (étant donné que les requêtes GET ne comportent pas de corps de message). Si votre API personnalisée prend en charge les autres verbes HTTP, modifiez `HTTPMethod` en conséquence.

**Objective-C** :
```
    [self.client invokeAPI:@"sendEmail"
                      body:@{ @"contents": @"Hello world!" }
                HTTPMethod:@"POST"
                parameters:@{ @"to": @"bill@contoso.com", @"subject" : @"Hi!" }
                   headers:nil
                completion: ^(NSData *result, NSHTTPURLResponse *response, NSError *error) {
                    if(error) {
                        NSLog(@"ERROR %@", error);
                    } else {
                        // Do something with result
                    }
                }];
```

**Swift** :

```
client.invokeAPI("sendEmail",
            body: [ "contents": "Hello World" ],
            HTTPMethod: "POST",
            parameters: [ "to": "bill@contoso.com", "subject" : "Hi!" ],
            headers: nil)
            {
                (result, response, error) -> Void in
                if let err = error {
                    print("ERROR ", err)
                } else if let res = result {
                          // Do something with result
                }
        }
```

##<a name="templates"></a>Inscription de modèles de notifications Push pour envoyer des notifications multiplateforme

Pour inscrire des modèles, transmettez-les simplement avec votre méthode **client.push registerDeviceToken** dans votre application cliente.

**Objective-C** :

```
[client.push registerDeviceToken:deviceToken template:iOSTemplate completion:^(NSError *error) {
	if(error) {
		NSLog(@"ERROR %@", error);
	}
}];
```

**Swift** :

```
    client.push?.registerDeviceToken(NSData(), template: iOSTemplate, completion: { (error) in
        if let err = error {
            print("ERROR ", err)
        }
    })
```

Vos modèles sont de type NSDictionary et peuvent contenir plusieurs modèles au format suivant :

**Objective-C** :

```
NSDictionary *iOSTemplate = @{ @"templateName": @{ @"body": @{ @"aps": @{ @"alert": @"$(message)" } } } };
```

**Swift** :

```
let iOSTemplate = ["templateName": ["body": ["aps": ["alert": "$(message)"]]]]
```

Notez que toutes les balises seront supprimées pour des raisons de sécurité. Pour ajouter des balises à des installations ou des modèles dans des installations, consultez [Utiliser le Kit de développement logiciel (SDK) de serveur principal .NET pour Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#tags).

Pour envoyer des notifications à l’aide de ces modèles inscrits, utilisez les [API Notification Hubs](https://msdn.microsoft.com/library/azure/dn495101.aspx).

##<a name="errors"></a>Procédure : gestion des erreurs

Quand vous appelez un service mobile, le bloc completion contient un paramètre `NSError`. Si une erreur se produit, ce paramètre est non-nil. Vous devez vérifier ce paramètre dans votre code et gérer les erreurs, le cas échéant, comme indiqué dans les extraits de code ci-dessus

Le fichier [`<WindowsAzureMobileServices/MSError.h>`](https://github.com/Azure/azure-mobile-services/blob/master/sdk/iOS/src/MSError.h) définit les constantes `MSErrorResponseKey`, `MSErrorRequestKey` et `MSErrorServerItemKey` pour accéder à davantage d’informations sur l’erreur, comme indiqué ci-après :

**Objective-C** :

```
NSDictionary *serverItem = [error.userInfo objectForKey:MSErrorServerItemKey];
```

**Swift** :

```
let serverItem = error.userInfo[MSErrorServerItemKey]
```

En outre, le fichier définit des constantes pour chaque code d'erreur, comme indiqué ci-après :

**Objective-C** :

```
if (error.code == MSErrorPreconditionFailed) {
```

**Swift** :

```
if (error.code == MSErrorPreconditionFailed) {
```

## <a name="adal"></a>Procédure : authentifier des utilisateurs avec la bibliothèque Active Directory Authentication Library

Vous pouvez utiliser la bibliothèque d’authentification Active Directory (ADAL) pour authentifier des utilisateurs dans votre application à l’aide d’Azure Active Directory. Cette approche est souvent préférable à l’utilisation des méthodes `loginAsync()`, car elle offre une interface UX native plus simple et permet une personnalisation supplémentaire.

1. Si vous souhaitez configurer le backend de votre application mobile pour utiliser la connexion AAD, suivez le didacticiel [Configurer votre application App Service pour utiliser la connexion Azure Active Directory](app-service-mobile-how-to-configure-active-directory-authentication.md). Bien que cette étape soit facultative, veillez à inscrire une application cliente native. Pour iOS, il est recommandé (mais pas obligatoire) d’utiliser un URI de redirection au format `<app-scheme>://<bundle-id>`. Pour plus d’informations, consultez la [procédure de démarrage rapide d’ADAL pour iOS](active-directory-devquickstarts-ios.md#em1-determine-what-your-redirect-uri-will-be-for-iosem).

2. Installez la bibliothèque ADAL à l’aide de Cocoapods. Modifiez votre podfile pour inclure les éléments suivants, en remplaçant **YOUR-PROJECT** par le nom de votre projet Xcode :

		source 'https://github.com/CocoaPods/Specs.git'
		link_with ['YOUR-PROJECT']
		xcodeproj 'YOUR-PROJECT'
et le pod :

		pod 'ADALiOS'

3. À l’aide du Terminal, exécutez `pod install` à partir du répertoire contenant votre projet, puis ouvrez l’espace de travail Xcode généré (et non le projet).

4. Ajoutez le code ci-dessous à votre application, en fonction du langage utilisé. Dans chaque cas, effectuez les remplacements suivants :

* Remplacez **INSERT-AUTHORITY-HERE** par le nom du client dans lequel vous avez déployé votre application. Vous devez utiliser le format https://login.windows.net/contoso.onmicrosoft.com. Cette valeur peut être copiée depuis l’onglet Domaine de votre Azure Active Directory dans le [portail Azure Classic].

* Remplacez **INSERT-RESOURCE-ID-HERE** par l’ID client du serveur principal de votre application mobile. Vous pouvez obtenir cet identifiant sur le portail, sous l’onglet **Avancé** du menu **Paramètres Azure Active Directory**.

* Remplacez **INSERT-CLIENT-ID-HERE** par l’ID client que vous avez copié depuis l’application cliente native.

* Remplacez **INSERT-REDIRECT-URI-HERE** par le point de terminaison _/.auth/login/done_ de votre site, en utilisant le modèle HTTPS. Cette valeur doit être similaire à \__https://contoso.azurewebsites.net/.auth/login/done_.

**Objective-C** :

	#import <ADALiOS/ADAuthenticationContext.h>
	#import <ADALiOS/ADAuthenticationSettings.h>
	// ...
	- (void) authenticate:(UIViewController*) parent
	           completion:(void (^) (MSUser*, NSError*))completionBlock;
	{
	    NSString *authority = @"INSERT-AUTHORITY-HERE";
	    NSString *resourceId = @"INSERT-RESOURCE-ID-HERE";
	    NSString *clientId = @"INSERT-CLIENT-ID-HERE";
	    NSURL *redirectUri = [[NSURL alloc]initWithString:@"INSERT-REDIRECT-URI-HERE"];
	    ADAuthenticationError *error;
	    ADAuthenticationContext authContext = [ADAuthenticationContext authenticationContextWithAuthority:authority error:&error];
	    authContext.parentController = parent;
	    [ADAuthenticationSettings sharedInstance].enableFullScreen = YES;
	    [authContext acquireTokenWithResource:resourceId
	                                 clientId:clientId
	                              redirectUri:redirectUri
	                          completionBlock:^(ADAuthenticationResult *result) {
	                              if (result.status != AD_SUCCEEDED)
	                              {
	                                  completionBlock(nil, result.error);;
	                              }
	                              else
	                              {
	                                  NSDictionary *payload = @{
	                                                            @"access_token" : result.tokenCacheStoreItem.accessToken
	                                                            };
	                                  [client loginWithProvider:@"aad" token:payload completion:completionBlock];
	                              }
	                          }];
	}


**Swift** :

	// add the following imports to your bridging header:
	//		#import <ADALiOS/ADAuthenticationContext.h>
	//		#import <ADALiOS/ADAuthenticationSettings.h>

	func authenticate(parent: UIViewController, completion: (MSUser?, NSError?) -> Void) {
		let authority = "INSERT-AUTHORITY-HERE"
		let resourceId = "INSERT-RESOURCE-ID-HERE"
		let clientId = "INSERT-CLIENT-ID-HERE"
		let redirectUri = NSURL(string: "INSERT-REDIRECT-URI-HERE")
		var error: AutoreleasingUnsafeMutablePointer<ADAuthenticationError?> = nil
		let authContext = ADAuthenticationContext(authority: authority, error: error)
		authContext.parentController = parent
		ADAuthenticationSettings.sharedInstance().enableFullScreen = true
		authContext.acquireTokenWithResource(resourceId, clientId: clientId, redirectUri: redirectUri) { (result) in
		        if result.status != AD_SUCCEEDED {
		            completion(nil, result.error)
		        }
		        else {
		            let payload: [String: String] = ["access_token": result.tokenCacheStoreItem.accessToken]
		            client.loginWithProvider("aad", token: payload, completion: completion)
		        }
    		}
	}


## <a name="facebook-sdk"></a>Procédure : authentifier les utilisateurs avec le kit de développement logiciel (SDK) Facebook pour iOS

Vous pouvez utiliser le kit de développement logiciel (SDK) Facebook pour iOS pour identifier les utilisateurs sur votre application utilisant Facebook. Cette approche est souvent préférable à l’utilisation des méthodes `loginAsync()`, car elle offre une interface UX native plus simple et permet une personnalisation supplémentaire.

1. Si vous souhaitez configurer le backend de votre application mobile pour utiliser la connexion Facebook, suivez le didacticiel [Configurer votre application App Service pour utiliser la connexion Facebook](app-service-mobile-how-to-configure-facebook-authentication.md).

2. Installez le kit de développement logiciel (SDK) Facebook pour iOS en suivant la documentation [Kit de développement logiciel (SDK) Facebook pour iOS : prise en main](https://developers.facebook.com/docs/ios/getting-started). Au lieu de créer une nouvelle application, vous pouvez ajouter la plateforme iOS à votre inscription existante.

    La documentation de Facebook comprend du code en Objective-C dans le délégué de l’application. Si vous utilisez **Swift**, vous pouvez vous servir des conversions suivantes pour AppDelegate.swift :
  
		// Add the following import to your bridging header:
		//		#import <FBSDKCoreKit/FBSDKCoreKit.h>
		
		func application(application: UIApplication, didFinishLaunchingWithOptions launchOptions: [NSObject : AnyObject]?) -> Bool {
			FBSDKApplicationDelegate.sharedInstance().application(application, didFinishLaunchingWithOptions: launchOptions)
			// Add any custom logic here.
			return true
		}

		func application(application: UIApplication, openURL url: NSURL, sourceApplication: String?, annotation: AnyObject?) -> Bool {
			let handled = FBSDKApplicationDelegate.sharedInstance().application(application, openURL: url, sourceApplication: sourceApplication, annotation: annotation)
			// Add any custom logic here.
			return handled
		}

3. En plus d’ajouter `FBSDKCoreKit.framework` à votre projet, ajoutez également une référence à `FBSDKLoginKit.framework` de la même façon.

4. Ajoutez le code ci-dessous à votre application, en fonction du langage utilisé.

**Objective-C** :

	#import <FBSDKLoginKit/FBSDKLoginKit.h>
	#import <FBSDKCoreKit/FBSDKAccessToken.h>
	// ...
	- (void) authenticate:(UIViewController*) parent
	           completion:(void (^) (MSUser*, NSError*)) completionBlock;
	{	    
	    FBSDKLoginManager *loginManager = [[FBSDKLoginManager alloc] init];
	    [loginManager
	     logInWithReadPermissions: @[@"public_profile"]
	     fromViewController:parent
	     handler:^(FBSDKLoginManagerLoginResult *result, NSError *error) {
	         if (error) {
	             completionBlock(nil, error);
	         } else if (result.isCancelled) {
	             completionBlock(nil, error);
	         } else {
	             NSDictionary *payload = @{
	                                       @"access_token":result.token.tokenString
	                                       };
	             [client loginWithProvider:@"facebook" token:payload completion:completionBlock];
	         }
	     }];
	}


**Swift** :

	// Add the following imports to your bridging header:
	//		#import <FBSDKLoginKit/FBSDKLoginKit.h>
	//		#import <FBSDKCoreKit/FBSDKAccessToken.h>
	
	func authenticate(parent: UIViewController, completion: (MSUser?, NSError?) -> Void) {
		let loginManager = FBSDKLoginManager()
		loginManager.logInWithReadPermissions(["public_profile"], fromViewController: parent) { (result, error) in
			if (error != nil) {
				completion(nil, error)
			}
			else if result.isCancelled {
				completion(nil, error)
			}
			else {
				let payload: [String: String] = ["access_token": result.token.tokenString]
				client.loginWithProvider("facebook", token: payload, completion: completion)
			}
		}
	}

## <a name="twitter-fabric"></a>Procédure : authentifier les utilisateurs avec Twitter Fabric pour iOS

Vous pouvez utiliser Twitter Fabric pour iOS pour identifier les utilisateurs sur votre application utilisant Twitter. Cette approche est souvent préférable à l’utilisation des méthodes `loginAsync()`, car elle offre une interface UX native plus simple et permet une personnalisation supplémentaire.

1. Si vous souhaitez configurer le backend de votre application mobile pour utiliser la connexion Twitter, suivez le didacticiel [Configurer votre application App Service pour utiliser la connexion Twitter](app-service-mobile-how-to-configure-twitter-authentication.md).

2. Ajoutez Fabric à votre projet en suivant la documentation [Fabric pour iOS : prise en main](https://docs.fabric.io/ios/fabric/getting-started.html) et en configurant TwitterKit.

    > [AZURE.NOTE] Par défaut, Fabric créera une nouvelle application Twitter pour vous. Vous pouvez modifier cela en enregistrant la clé et la clé secrète du client que vous avez créées plus tôt avec les extraits de code ci-dessous. Vous pouvez également remplacer les valeurs de clé et de clé secrète du client que vous fournissez à App Service avec les valeurs que vous voyez dans le [Tableau de bord de Fabric](https://www.fabric.io/home). Si vous choisissez cette option, veillez à définir l’URL de rappel sur une valeur d’espace réservé, par exemple `https://<yoursitename>.azurewebsites.net/.auth/login/twitter/callback`.

	Si vous choisissez d’utiliser les clés secrètes que vous avez créées précédemment, ajoutez ce qui suit à votre délégué d’application :
	
	**Objective-C** :

		#import <Fabric/Fabric.h>
		#import <TwitterKit/TwitterKit.h>
		// ...
		- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
		{
		    [[Twitter sharedInstance] startWithConsumerKey:@"your_key" consumerSecret:@"your_secret"];
		    [Fabric with:@[[Twitter class]]];
			// Add any custom logic here.
		    return YES;
		}
		
	**Swift** :
	
		import Fabric
		import TwitterKit
		// ...
		func application(application: UIApplication, didFinishLaunchingWithOptions launchOptions: [NSObject : AnyObject]?) -> Bool {
			Twitter.sharedInstance().startWithConsumerKey("your_key", consumerSecret: "your_secret")
			Fabric.with([Twitter.self])
			// Add any custom logic here.
			return true
		}
	
3. Ajoutez le code ci-dessous à votre application, en fonction du langage utilisé.

**Objective-C** :

	#import <TwitterKit/TwitterKit.h>
	// ...
	- (void)authenticate:(UIViewController*)parent completion:(void (^) (MSUser*, NSError*))completionBlock
	{
		[[Twitter sharedInstance] logInWithCompletion:^(TWTRSession *session, NSError *error) {
			if (session) {
				NSDictionary *payload = @{
											@"access_token":session.authToken,
											@"access_token_secret":session.authTokenSecret
										};
				[client loginWithProvider:@"twitter" token:payload completion:completionBlock];
			} else {
				completionBlock(nil, error);
			}
	    }];
	}

**Swift** :

	import TwitterKit
	// ...
	func authenticate(parent: UIViewController, completion: (MSUser?, NSError?) -> Void) {
		let client = self.table!.client
		Twitter.sharedInstance().logInWithCompletion { session, error in
			if (session != nil) {
				let payload: [String: String] = ["access_token": session!.authToken, "access_token_secret": session!.authTokenSecret]
				client.loginWithProvider("twitter", token: payload, completion: completion)
			} else {
				completion(nil, error)
			}
		}
	}

<!-- Anchors. -->

[What is Mobile Services]: #what-is
[Concepts]: #concepts
[Setup and Prerequisites]: #Setup
[How to: Create the Mobile Services client]: #create-client
[How to: Create a table reference]: #table-reference
[How to: Query data from a mobile service]: #querying
[Filter returned data]: #filtering
[Sort returned data]: #sorting
[Return data in pages]: #paging
[Select specific columns]: #selecting
[How to: Bind data to the user interface]: #binding
[How to: Insert data into a mobile service]: #inserting
[How to: Modify data in a mobile service]: #modifying
[How to: Authenticate users]: #authentication
[Cache authentication tokens]: #caching-tokens
[How to: Upload images and large files]: #blobs
[How to: Handle errors]: #errors
[How to: Design unit tests]: #unit-testing
[How to: Customize the client]: #customizing
[Customize request headers]: #custom-headers
[Customize data type serialization]: #custom-serialization
[Next Steps]: #next-steps
[How to: Use MSQuery]: #query-object

<!-- Images. -->

<!-- URLs. -->
[Démarrage rapide d’Azure Mobile Apps]: app-service-mobile-ios-get-started.md

[Add Mobile Services to Existing App]: /develop/mobile/tutorials/get-started-data
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started-ios
[Validate and modify data in Mobile Services by using server scripts]: /develop/mobile/tutorials/validate-modify-and-augment-data-ios
[Mobile Services SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[Authentication]: /develop/mobile/tutorials/get-started-with-users-ios
[iOS SDK]: https://developer.apple.com/xcode

[Handling Expired Tokens]: http://go.microsoft.com/fwlink/p/?LinkId=301955
[Live Connect SDK]: http://go.microsoft.com/fwlink/p/?LinkId=301960
[Permissions]: http://msdn.microsoft.com/library/windowsazure/jj193161.aspx
[Service-side Authorization]: mobile-services-javascript-backend-service-side-authorization.md
[Use scripts to authorize users]: /develop/mobile/tutorials/authorize-users-in-scripts-ios
[schéma dynamique]: http://go.microsoft.com/fwlink/p/?LinkId=296271
[How to: access custom parameters]: /develop/mobile/how-to-guides/work-with-server-scripts#access-headers
[Create a table]: http://msdn.microsoft.com/library/windowsazure/jj193162.aspx
[NSDictionary object]: http://go.microsoft.com/fwlink/p/?LinkId=301965
[ASCII control codes C0 and C1]: http://en.wikipedia.org/wiki/Data_link_escape_character#C1_set
[CLI to manage Mobile Services tables]: ../virtual-machines-command-line-tools.md#Mobile_Tables
[Conflict-Handler]: mobile-services-ios-handling-conflicts-offline-data.md#add-conflict-handling

<!---HONumber=AcomDC_0518_2016-->