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
	ms.date="09/28/2015"
	ms.author="krisragh"/>

# Utilisation de la bibliothèque cliente iOS pour Azure Mobile Apps

[AZURE.INCLUDE [app-service-mobile-selector-offline-preview](../../includes/app-service-mobile-selector-client-library.md)]
&nbsp;  
[AZURE.INCLUDE [app-service-mobile-note-mobile-services-preview](../../includes/app-service-mobile-note-mobile-services-preview.md)]

Ce guide indique le déroulement de scénarios courants dans le cadre de l'utilisation du dernier [Kit de développement logiciel (SDK) iOS Azure Mobile Apps](https://go.microsoft.com/fwLink/?LinkID=266533&clcid=0x409). Si vous ne connaissez pas Azure Mobile Apps, consultez d'abord la section [Démarrage rapide d'Azure Mobile Apps] pour créer un serveur principal, créez une table et téléchargez un projet Xcode iOS prédéfini. Dans ce guide, nous nous concentrons sur le SDK iOS côté client. Pour en savoir plus sur le Kit de développement logiciel côté serveur .NET pour le serveur principal, consultez [Fonctionnement avec le serveur principal .NET](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md)

##<a name="Setup"></a>Configuration et conditions préalables

Ce guide part du principe que vous avez créé un serveur principal avec une table. Ce guide suppose que la table a le même schéma que les tables dans ces didacticiels. Ce guide suppose également que dans votre code, vous référencez `WindowsAzureMobileServices.framework` et importez `WindowsAzureMobileServices/WindowsAzureMobileServices.h`.

##<a name="create-client"></a>Création du client

Pour accéder à un serveur principal Azure Mobile Apps dans votre projet, créez un `MSClient`. Remplacez `AppUrl` par l'URL de l'application. Vous pouvez laisser `gatewayURLString` et `applicationKey` vides. Si vous avez configuré une passerelle pour l'authentification, renseignez `gatewayURLString` avec l'URL de la passerelle.

```
MSClient *client = [MSClient clientWithApplicationURLString:@"AppUrl" gatewayURLString:@"" applicationKey:@""];
```

##<a name="table-reference"></a>Procédure : création d'une référence de table

Pour accéder aux données ou les mettre à jour, créez une référence à la table principale. Remplacez `TodoItem` par le nom de votre table.

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
[Démarrage rapide d'Azure Mobile Apps]: app-service-mobile-dotnet-backend-ios-get-started-preview.md

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
[Gestionnaire de conflits]: mobile-services-ios-handling-conflicts-offline-data.md#add-conflict-handling

<!----HONumber=Oct15_HO3-->