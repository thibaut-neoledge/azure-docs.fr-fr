<properties
	pageTitle="Comment utiliser la bibliothèque cliente iOS pour Azure Mobile Services"
	description="Comment utiliser la bibliothèque cliente iOS pour Mobile Services"
	services="mobile-services"
	documentationCenter="ios"
	authors="krisragh"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="objective-c"
	ms.topic="article"
	ms.date="10/01/2015"
	ms.author="krisragh"/>

# Comment utiliser la bibliothèque cliente iOS pour Azure Mobile Services

[AZURE.INCLUDE [mobile-services-selector-client-library](../../includes/mobile-services-selector-client-library.md)]

Ce guide indique le déroulement de scénarios courants dans le cadre de l'utilisation du [Kit de développement logiciel (SDK) iOS] Azure Mobile Services. Si vous débutez avec Mobile Services, vous devez d'abord suivre le didacticiel [Démarrage rapide de Mobile Services] ou [Ajout de Mobile Services à une application existante] pour configurer votre compte, créer une table et créer un service mobile.

> [AZURE.NOTE]Ce guide utilise le dernier [Kit de développement logiciel (SDK) iOS Mobile Services](https://go.microsoft.com/fwLink/?LinkID=266533&clcid=0x409). Si votre projet utilise une version antérieure du Kit de développement, mettez d'abord à niveau l'infrastructure dans Xcode.

[AZURE.INCLUDE [mobile-services-concepts](../../includes/mobile-services-concepts.md)]

##<a name="Setup"></a>Configuration et conditions préalables

Ce guide part du principe que vous avez créé un service mobile avec une table. Pour plus d'informations, consultez la page [Création d'une table] ou réutilisez la table `TodoItem` créée pendant le didacticiel [Démarrage rapide de Mobile Services] ou [Ajout de Mobile Services à une application existante]. Ce guide suppose que la table a le même schéma que les tables dans ces didacticiels. Il suppose également que votre Xcode référence `WindowsAzureMobileServices.framework` et importe `WindowsAzureMobileServices/WindowsAzureMobileServices.h`.

##<a name="create-client"></a>Procédure : Création du client Mobile Services

Pour accéder à un service mobile Azure dans votre projet, créez un objet client `MSClient`. Remplacez `AppUrl` et `AppKey` par les valeurs de tableau de bord de l'URL de service mobile et de la clé d'application, respectivement.

```
MSClient *client = [MSClient clientWithApplicationURLString:@"AppUrl" applicationKey:@"AppKey"];
```

##<a name="table-reference"></a>Procédure : création d'une référence de table

Pour accéder aux données de votre service mobile Azure, ou pour les mettre à jour, créez une référence à la table. Remplacez `TodoItem` par le nom de votre table.

```
	MSTable *table = [client tableWithName:@"TodoItem"];
```

##<a name="querying"></a>Procédure : interrogation des données

Pour créer une requête de base de données, interrogez l'objet `MSTable`. La requête suivante obtient tous les éléments dans `TodoItem` et enregistre le texte de chaque élément.

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

##<a name="filtering"></a>Procédure : filtrage des données renvoyées

Pour filtrer les résultats, il existe de nombreuses options.

Pour filtrer à l'aide d'un prédicat, utilisez un `NSPredicate` et `readWithPredicate`. Les filtres suivants retournent des données pour rechercher uniquement les éléments Todo incomplets.

```
// Create a predicate that finds items where complete is false
NSPredicate * predicate = [NSPredicate predicateWithFormat:@"complete == NO"];
// Query the TodoItem table and update the items property with the results from the service
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

##<a name="query-object"></a>Procédure : utilisation de MSQuery

Pour effectuer une requête complexe (y compris le tri et la pagination), créez un objet `MSQuery`, directement ou en utilisant un prédicat :

```
    MSQuery *query = [table query];
    MSQuery *query = [table queryWithPredicate: [NSPredicate predicateWithFormat:@"complete == NO"]];
```

`MSQuery` vous permet de contrôler plusieurs comportements de requête, y compris les suivants. Exécuter une requête `MSQuery` en appelant `readWithCompletion` sur celle-ci, comme illustré dans l'exemple suivant. * Spécifier l'ordre des résultats * Limiter les champs à retourner * Limiter le nombre d'enregistrements à retourner * Spécifier le nombre total dans la réponse * Spécifier les paramètres de chaîne de requête personnalisés dans la requête * Appliquer des fonctions supplémentaires


## <a name="sorting"></a>Procédure : trier des données avec MSQuery

Pour trier les résultats, examinons un exemple. Pour trier dans l'ordre croissant sur le champ `text`, puis décroissant sur le champ `completion`, appelez `MSQuery` comme suit :

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

## <a name="paging"></a>Procédure : retour de données dans les pages avec MSQuery

Mobile Services limite le nombre d'enregistrements qui sont renvoyés dans une réponse unique. Pour contrôler le nombre d'enregistrements affichés pour vos utilisateurs, vous devez mettre en œuvre un système de pagination. La pagination est effectuée à l'aide des trois propriétés suivantes de l'objet **MSQuery** :

```
+	`BOOL includeTotalCount`
+	`NSInteger fetchLimit`
+	`NSInteger fetchOffset`
```

Dans l'exemple suivant, une fonction simple demande 5 enregistrements au serveur et les ajoute à l'ensemble local des enregistrements précédemment chargés :

```
// Create and initialize these properties
@property (nonatomic, strong)   NSMutableArray *loadedItems; // Init via [[NSMutableArray alloc] init]
@property (nonatomic)   				BOOL moreResults;
```

```
-(void)loadResults
{
    MSQuery *query = [self.table query];

    query.includeTotalCount = YES;
    query.fetchLimit = 5;
    query.fetchOffset = self.loadedItems.count;


    [query readWithCompletion:^(MSQueryResult *result, NSError *error) {
        if(!error) {
            // Add the items to our local copy
            [self.loadedItems addObjectsFromArray:result.items];

            // Set a flag to keep track if there are any additional records we need to load
            self.moreResults = (self.loadedItems.count <= result.totalCount);
        }
    }];
}

```

## <a name="selecting"></a><a name="parameters"></a>Procédure : limitation des champs et développement des paramètres de chaîne de requête avec MSQuery

Pour limiter les champs à retourner dans une requête, spécifiez les noms des champs dans la propriété **selectFields**. Le code suivant renvoie uniquement les champs text et completed :

```
	query.selectFields = @[@"text", @"completed"];
```

Pour inclure des paramètres de chaîne de requête supplémentaires dans la demande serveur (par exemple, si un script côté serveur personnalisé les utilise), remplissez `query.parameters` comme suit :

```
	query.parameters = @{
		@"myKey1" : @"value1",
		@"myKey2" : @"value2",
	};
```

##<a name="inserting"></a>Procédure : insertion de données

Pour insérer une nouvelle ligne de table, créez un `NSDictionary` et appelez `table insert`. Mobile Services génère automatiquement de nouvelles colonnes basées sur le `NSDictionary` si le [schéma dynamique] n'est pas désactivé.

Si `id` n'est pas fourni, le backend génère automatiquement un nouvel ID unique. Fournissez votre propre `id` pour utiliser les adresses de messagerie électronique, les noms d'utilisateurs, ou vos propres valeurs personnalisées sous la forme d'ID. Fournir son propre ID peut faciliter les jointures et la logique de base de données orientée métier.

```
	NSDictionary *newItem = @{@"id": @"custom-id", @"text": @"my new item", @"complete" : @NO};
	[self.table insert:newItem completion:^(NSDictionary *result, NSError *error) {
		// The result contains the new item that was inserted,
		// depending on your server scripts it may have additional or modified
		// data compared to what was passed to the server.
		if(error) {
				NSLog(@"ERROR %@", error);
		} else {
						NSLog(@"Todo Item: %@", [result objectForKey:@"text"]);
		}
	}];
```

##<a name="modifying"></a>Procédure : modification des données

Pour mettre à jour une ligne existante, modifiez un élément et appelez `update` :

```
	NSMutableDictionary *newItem = [oldItem mutableCopy]; // oldItem is NSDictionary
	[newItem setValue:@"Updated text" forKey:@"text"];
	[self.table update:newItem completion:^(NSDictionary *item, NSError *error) {
		// Handle error or perform additional logic as needed
	}];
```

Vous pouvez également fournir l'ID de la ligne et le champ mis à jour :

```
	[self.table update:@{@"id":@"37BBF396-11F0-4B39-85C8-B319C729AF6D", @"Complete":@YES} completion:^(NSDictionary *item, NSError *error) {
		// Handle error or perform additional logic as needed
	}];
```

Au minimum, l'attribut `id` doit être défini quand vous effectuez des mises à jour.

##<a name="deleting"></a>Procédure : suppression de données

Pour supprimer un élément, appelez `delete` avec l'élément :

```
	[self.table delete:item completion:^(id itemId, NSError *error) {
		// Handle error or perform additional logic as needed
	}];
```

Vous pouvez également effectuer la suppression en fournissant un ID de ligne :

```
	[self.table deleteWithId:@"37BBF396-11F0-4B39-85C8-B319C729AF6D" completion:^(id itemId, NSError *error) {
		// Handle error or perform additional logic as needed
	}];
```

Au minimum, l'attribut `id` doit être défini quand vous effectuez des suppressions.

##<a name="#custom-api"></a>Procédure : appel d'une API personnalisée

Une API personnalisée vous permet de définir des points de terminaison exposant une fonctionnalité de serveur qui ne mappe pas vers une opération d'insertion, de mise à jour, de suppression ou de lecture. En utilisant une API personnalisée, vous pouvez exercer davantage de contrôle sur la messagerie, notamment lire et définir des en-têtes de message HTTP et définir un format de corps de message autre que JSON. Pour obtenir un exemple montrant comment créer une API personnalisée dans votre service mobile, consultez [Procédure : définition d’un point de terminaison dans une API personnalisée](mobile-services-dotnet-backend-define-custom-api.md).

[AZURE.INCLUDE [mobile-services-ios-call-custom-api](../../includes/mobile-services-ios-call-custom-api.md)]


##<a name="authentication"></a>Procédure : authentification des utilisateurs

Azure Mobile Services prend en charge plusieurs fournisseurs d'identité. Pour suivre un didacticiel de base, consultez [Authentification].

Azure Mobile Services prend en charge deux workflows d'authentification :

- **Connexion gérée par le serveur** : Azure Mobile Services gère le processus de connexion pour le compte de votre application. Il affiche une page de connexion spécifique au fournisseur et s'authentifie auprès du fournisseur choisi.

- **Connexion gérée par le client** : l'_application_ demande un jeton au fournisseur d'identité et le présente à Azure Mobile Services pour authentification.

Quand l'authentification réussit, vous obtenez un objet utilisateur avec une valeur d'ID utilisateur et le jeton d'authentification. Pour utiliser cet ID utilisateur pour autoriser les utilisateurs, consultez [Autorisation côté service]. Pour restreindre l'accès aux tables aux seuls utilisateurs authentifiés, consultez [Autorisations].

### Connexion gérée par serveur

Voici comment vous pouvez ajouter la connexion gérée par le serveur au projet [Démarrage rapide de Mobile Services] ; vous pouvez utiliser un code similaire pour vos autres projets. Pour plus d'informations et pour voir un exemple de bout en bout en action, consultez [Authentification].

[AZURE.INCLUDE [mobile-services-ios-authenticate-app](../../includes/mobile-services-ios-authenticate-app.md)]

### Connexion gérée par client (authentification unique)

Vous pouvez effectuer le processus de connexion à l'extérieur du client Mobile Services, soit pour activer l'authentification unique ou si votre application contacte le fournisseur d'identité directement. Dans les cas de ce type, vous pouvez vous connecter à Mobile Services en fournissant un jeton obtenu indépendamment auprès d'un fournisseur d'identité pris en charge.

L'exemple suivant utilise le [Kit de développement logiciel (SDK) Live Connect] permettant d'activer l'authentification unique pour les applications iOS. Il part du principe qu'il existe une instance **LiveConnectClient** nommée `liveClient` dans le contrôleur et que l'utilisateur est connecté.

```
	[client loginWithProvider:@"microsoftaccount"
		token:@{@"authenticationToken" : self.liveClient.session.authenticationToken}
		completion:^(MSUser *user, NSError *error) {
				// Handle success and errors
	}];
```

##<a name="caching-tokens"></a>Procédure : mise en cache des jetons d'authentification

Voyons comment vous pouvez mettre en cache des jetons dans le projet [Démarrage rapide de Mobile Services] ; vous pouvez appliquer des étapes similaires à n'importe quel projet.[AZURE.INCLUDE [mobile-services-ios-authenticate-app-with-token](../../includes/mobile-services-ios-authenticate-app-with-token.md)]

##<a name="errors"></a>Procédure : gestion des erreurs

Quand vous appelez un service mobile, le bloc completion contient un paramètre `NSError *error`. Si une erreur se produit, ce paramètre est non-nil. Vous devez vérifier ce paramètre dans votre code et gérer les erreurs, le cas échéant.

Le fichier [`<WindowsAzureMobileServices/MSError.h>`](https://github.com/Azure/azure-mobile-services/blob/master/sdk/iOS/src/MSError.h) définit les constantes `MSErrorResponseKey`, `MSErrorRequestKey`, et `MSErrorServerItemKey` pour obtenir plus d'informations sur l'erreur. En outre, le fichier définit des constantes pour chaque code d'erreur. Pour obtenir un exemple d'utilisation de ces constantes, consultez le [Gestionnaire de conflits] et son utilisation de `MSErrorServerItemKey` et `MSErrorPreconditionFailed`.

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
[Ajout de Mobile Services à une application existante]: /develop/mobile/tutorials/get-started-data
[Démarrage rapide de Mobile Services]: /develop/mobile/tutorials/get-started-ios
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started-ios
[Validate and modify data in Mobile Services by using server scripts]: /develop/mobile/tutorials/validate-modify-and-augment-data-ios
[Mobile Services SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[Authentification]: /develop/mobile/tutorials/get-started-with-users-ios
[Kit de développement logiciel (SDK) iOS]: https://developer.apple.com/xcode

[Handling Expired Tokens]: http://go.microsoft.com/fwlink/p/?LinkId=301955
[Kit de développement logiciel (SDK) Live Connect]: http://go.microsoft.com/fwlink/p/?LinkId=301960
[Autorisations]: http://msdn.microsoft.com/library/windowsazure/jj193161.aspx
[Autorisation côté service]: mobile-services-javascript-backend-service-side-authorization.md
[Use scripts to authorize users]: /develop/mobile/tutorials/authorize-users-in-scripts-ios
[schéma dynamique]: http://go.microsoft.com/fwlink/p/?LinkId=296271
[How to: access custom parameters]: /develop/mobile/how-to-guides/work-with-server-scripts#access-headers
[Création d'une table]: http://msdn.microsoft.com/library/windowsazure/jj193162.aspx
[NSDictionary object]: http://go.microsoft.com/fwlink/p/?LinkId=301965
[ASCII control codes C0 and C1]: http://en.wikipedia.org/wiki/Data_link_escape_character#C1_set
[CLI to manage Mobile Services tables]: ../virtual-machines-command-line-tools.md#Mobile_Tables
[Gestionnaire de conflits]: mobile-services-ios-handling-conflicts-offline-data.md#add-conflict-handling

<!---HONumber=Oct15_HO1-->