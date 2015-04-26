<properties 
	pageTitle="Utilisation de la bibliothèque cliente iOS - Azure Mobile Services" 
	description="Découvrez comment utiliser la bibliothèque cliente iOS pour Azure Mobile Services." 
	services="" 
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
	ms.date="10/10/2014" 
	ms.author="krisragh"/>




# Utilisation de la bibliothèque cliente iOS pour Mobile Services
<div class="dev-center-tutorial-selector sublanding">
  <a href="/fr-fr/develop/mobile/how-to-guides/work-with-net-client-library/" title=".NET Framework">.NET Framework</a><a href="/fr-fr/develop/mobile/how-to-guides/work-with-html-js-client/" title="HTML/JavaScript">HTML/JavaScript</a><a href="/fr-fr/develop/mobile/how-to-guides/work-with-ios-client-library/" title="iOS" class="current">iOS</a><a href="/fr-fr/develop/mobile/how-to-guides/work-with-android-client-library/" title="Android">Android</a><a href="/fr-fr/develop/mobile/how-to-guides/work-with-xamarin-client-library/" title="Xamarin">Xamarin</a>
</div>

Ce guide décrit le déroulement de scénarios courants dans le cadre de l'utilisation du client iOS pour Azure Mobile Services. Les exemples sont écrits en objective-C et nécessitent le [Kit de développement logiciel (SDK) Mobile Services].  Ce didacticiel requiert également le [Kit de développement logiciel (SDK) iOS]. Les scénarios traités incluent l'interrogation, l'insertion, la mise à jour et la suppression de données, l'authentification des utilisateurs et la gestion des erreurs. Si vous débutez avec Mobile Services, suivez le didacticiel [Démarrage rapide Mobile Services][Prise en main de Mobile Services]. Ces didacticiels de démarrage rapide vous aideront à configurer et à créer votre premier service mobile.

## Sommaire

- [Présentation de Mobile Services][]
- [Concepts][]
- [Configuration et conditions préalables][]
- [Procédure : Création du client Mobile Services][]
- [Procédure : Création d'une référence de table][]
- [Procédure : Interrogation des données à partir d'un service mobile][]
	- [Filtrage des données renvoyées]
    - [Utilisation de l'objet MSQuery][Procédure : Utilisation de MSQuery]
	- [Sélection de colonnes spécifiques]
- [Procédure : Insertion de données dans un service mobile]
- [Procédure : Modification des données d'un service mobile]
- [Procédure : Liaison des données dans l'interface utilisateur]
- [Procédure : Authentification des utilisateurs]
- [Procédure : Gestion des erreurs]

<!--- [Procédure : Conception de tests]
- [Procédure : Personnalisation du client]
	- [Personnalisation des en-têtes de requête]
	- [Personnalisation de la sérialisation du type de données]
- [Étapes suivantes][]-->

[AZURE.INCLUDE [mobile-services-concepts](../includes/mobile-services-concepts.md)]

##<a name="Setup"></a>Configuration et conditions préalables

Ce guide part du principe que vous avez créé un service mobile avec une table.  Pour plus d'informations, consultez la page [Création d'une table] ou réutilisez la table `ToDoItem` créée lors du didacticiel [Prise en main de Mobile Services]. Les exemples de cette rubrique utilisent une table nommée `ToDoItem`, qui contient les colonnes suivantes :

+ `id`
+ `text`
+ `complete`
+ `duration`


Si vous créez une application iOS pour la première fois, veillez à ajouter `WindowsAzureMobileServices.framework` dans le paramètre [**Link Binary With Libraries**](https://developer.apple.com/library/ios/recipes/xcode_help-project_editor/Articles/AddingaLibrarytoaTarget.html) de votre application. Au cours de cette étape, cliquez sur " Ajouter un autre...", accédez à l'emplacement du Kit de développement logiciel (SDK) Windows Azure Mobile Services, puis sélectionnez-le.

En outre, vous devez ajouter la référence suivante dans les fichiers appropriés ou dans le fichier .pch de votre application.

	#import <WindowsAzureMobileServices/WindowsAzureMobileServices.h>

<h2><a name="create-client"></a>Procédure : Création du client Mobile Services</h2>

Le code suivant permet de créer l'objet client du service mobile utilisé pour accéder à votre service mobile.

	MSClient *client = [MSClient clientWithApplicationURLString:@"MobileServiceUrl" applicationKey:@"AppKey"]

Dans le code ci-dessus, remplacez `MobileServiceUrl` et `AppKey` par l'URL et la clé d'application de votre service mobile. Afin de déterminer ces paramètres pour votre service mobile, sélectionnez ce dernier dans le portail de gestion Azure, puis cliquez sur **Tableau de bord**.

Vous pouvez également créer votre client à partir d'un objet **NSURL** qui est l'URL du service, comme suit :

	MSClient *client = [MSClient clientWithApplicationURL:[NSURL URLWithString:@"MobileServiceUrl"] applicationKey:@"AppKey"];

<h2><a name="table-reference"></a>Procédure : Création d'une référence de table</h2>

Avant de pouvoir accéder aux données depuis votre service mobile, vous devez obtenir une référence pointant vers la table à partir de laquelle vous souhaitez interroger, mettre à jour ou supprimer des éléments. Dans l'exemple suivant, `ToDoItem` est le nom de la table :

	MSTable *table = [client tableWithName:@"ToDoItem"];


<h2><a name="querying"></a>Procédure : Interrogation des données à partir d'un service mobile</h2>

Une fois que vous disposez d'un objet MSTable, vous pouvez créer une requête.  La requête simple suivante extrait tous les éléments dans notre table ToDoItem.

	[table readWithCompletion:^(NSArray *items, NSInteger totalCount, NSError *error) {
		if(error) {
			NSLog(@"ERROR %@", error);
		} else {
			for(NSDictionary *item in items) {
				NSLog(@"Todo Item: %@", [item objectForKey:@"text"]);
			}
		}
	}];

Notez que, dans ce cas, nous écrivons simplement le texte de la tâche dans le journal.

Les paramètres suivants sont fournis dans le rappel :

+ _items_: objet **NSArray** des enregistrements qui correspondent à votre requête.
+ _totalCount_: nombre total d'éléments dans toutes les pages de la requête (pas seulement ceux renvoyés dans la page courante). Cette valeur est définie sur -1, sauf si vous demandez explicitement le nombre total dans votre requête. Pour plus d'informations, consultez la section [Renvoi de données dans les pages].
+ _error_: toute erreur qui s'est produite ; sinon `nil`.

### <a name="filtering"></a>Procédure : Filtrage des données renvoyées

Si vous souhaitez filtrer vos résultats, un certain nombre d'options s'offrent à vous.

Le scénario le plus courant consiste à utiliser un NSPredicate pour filtrer les résultats.

	[table readWithPredicate:(NSPredicate *)predicate completion:(MSReadQueryBlock)completion];

Le prédicat suivant renvoie seulement les éléments incomplets dans notre table ToDoItem :

	NSPredicate *predicate = [NSPredicate predicateWithFormat:@"complete == NO"];
	[table readWithPredicate:predicate completion:^(NSArray *items, NSInteger totalCount, NSError *error) {
		if(error) {
			NSLog(@"ERROR %@", error);
		} else {
			for(NSDictionary *item in items) {
				NSLog(@"Todo Item: %@", [item objectForKey:@"text"]);
			}
		}
	}];

Un enregistrement unique peut être extrait à l'aide de son ID.

	[table readWithId:[@"37BBF396-11F0-4B39-85C8-B319C729AF6D"] completion:^(NSDictionary *item, NSError *error) {
		if(error) {
			NSLog(@"ERROR %@", error);
		} else {
				NSLog(@"Todo Item: %@", [item objectForKey:@"text"]);
		}
	}];

Notez que, dans ce cas, les paramètres de rappel sont légèrement différents.  Au lieu d'obtenir un tableau des résultats et un nombre en option, vous extrayez seulement l'enregistrement.

### <a name="query-object"></a>Utilisation de l'objet MSQuery

Utilisez l'objet **MSQuery** lorsque vous avez besoin d'émettre une requête plus complexe que le simple filtrage de lignes, c'est-à-dire le changement d'ordre de tri de vos résultats ou la réduction du nombre d'enregistrements de données que vous récupérez, par exemple. Les deux exemples suivants montrent comment créer une instance d'objet MSQuery :

    MSQuery *query = [table query];

    MSQuery *query = [table queryWithPredicate: [NSPredicate predicateWithFormat:@"complete == NO"]];

L'objet MSQuery vous permet de contrôler les comportements de requête suivants :

* spécifier l'ordre dans lequel les résultats sont renvoyés ;
* limiter les champs qui sont renvoyés ;
* limiter le nombre d'enregistrements renvoyés ;
* spécifier si le nombre total doit être inclus dans la réponse ;
* spécifier des paramètres de chaîne de requête personnalisés dans la requête.

Vous continuez de définir une requête en appliquant une ou plusieurs fonctions. Une fois que la requête est définie, elle est exécutée en appelant la fonction **readWithCompletion**.

#### <a name="sorting"></a>Tri des données renvoyées

Les fonctions suivantes permettent d'indiquer les champs utilisés pour le tri :

	-(void) orderByAscending:(NSString *)field
	-(void) orderByDescending:(NSString *)field

Cette requête trie d'abord les résultats en fonction de la durée, puis selon que la tâche a été ou non exécutée :

	[query orderByAscending:@"duration"];
	[query orderByAscending:@"complete"];
	[query readWithCompletion:^(NSArray *items, NSInteger totalCount, NSError *error) {
		//code to parse results here
	}];

#### <a name="paging"></a>Renvoi de données dans les pages

Mobile Services limite le nombre d'enregistrements qui sont renvoyés dans une réponse unique. Pour contrôler le nombre d'enregistrements affichés pour vos utilisateurs, vous devez mettre en œuvre un système de pagination.  La pagination est effectuée à l'aide des trois propriétés suivantes de l'objet **MSQuery** :

+	`BOOL includeTotalCount`
+	`NSInteger fetchLimit`
+	`NSInteger fetchOffset`


Dans l'exemple suivant, une fonction simple demande 20 enregistrements au serveur et les ajoute à l'ensemble local des enregistrements précédemment chargés :

	- (bool) loadResults() {
		MSQuery *query = [table query];

		query.includeTotalCount = YES;
		query.fetchLimit = 20;
		query.fetchOffset = self.loadedIte
	ms.count;

		[query readWithCompletion:^(NSArray *items, NSInteger totalCount, NSError *error) {
			if(!error) {
				// Add the items to our local copy
				[self.loadedItems addObjectsFromArray:items];

				// Set a flag to keep track if there are any additional records we need to load
				self.moreResults = (self.loadedIte
	ms.count < totalCount);
			}
		}];
	}

#### <a name="selecting"></a>Limitation des champs renvoyés

Pour limiter les champs devant être renvoyés en réponse à votre requête, il suffit de spécifier les noms des champs de votre choix dans la propriété **selectFields**. L'exemple suivant renvoie uniquement les champs text et completed :

	query.selectFields = @[@"text", @"completed"];

#### <a name="parameters"></a>Spécification de paramètres de chaîne de requête supplémentaires

La bibliothèque cliente rend possible l'inclusion de paramètres de chaîne de requête supplémentaires dans la demande adressée au serveur. Ces paramètres peuvent être requis par vos scripts côté serveur. L'exemple suivant ajoute deux paramètres de chaîne de requête à la requête :

	query.parameters = @{
		@"myKey1" : @"value1",
		@"myKey2" : @"value2",
	};

Ces paramètres sont ajoutés à l'URI de la requête sous la forme `myKey1=value1&myKey2=value2`.
Pour plus d'informations, consultez la page [Accès aux paramètres personnalisés].

<h2><a name="inserting"></a>Procédure : Insertion de données dans un service mobile</h2>

Pour insérer une nouvelle ligne dans la table, créez un objet [NSDictionary] et transmettez-le à la fonction insert. Le code suivant permet d'insérer un nouvel élément todo dans la table :

	NSDictionary *newItem = @{@"text": @"my new item", @"complete" : @NO};
	[table insert:newItem completion:^(NSDictionary *result, NSError *error) {
		// The result contains the new item that was inserted,
		// depending on your server scripts it may have additional or modified
		// data compared to what was passed to the server.
	}];

Mobile Services prend en charge les valeurs de chaîne personnalisées uniques pour l'ID de table. Les applications peuvent ainsi utiliser des valeurs personnalisées, telles que des adresses de messagerie ou des noms d'utilisateur, pour la colonne d'ID d'une table Mobile Services. Par exemple, si vous voulez identifier chaque enregistrement par une adresse électronique, vous pouvez utiliser l'objet JSON suivant.

	NSDictionary *newItem = @{@"id": @"myemail@emaildomain.com", @"text": @"my new item", @"complete" : @NO};
	[table insert:newItem completion:^(NSDictionary *result, NSError *error) {
		// The result contains the new item that was inserted,
		// depending on your server scripts it may have additional or modified
		// data compared to what was passed to the server.
	}];

Si aucune valeur d'ID de chaîne n'est fournie lors de l'insertion de nouveaux enregistrements dans une table, Mobile Services génère une valeur d'ID unique.

La prise en charge des ID de chaîne fournit les avantages suivants aux développeurs :

+ Les ID peuvent être générés sans effectuer d'aller-retour vers la base de données.
+ Il est plus facile de fusionner des enregistrements de plusieurs tables ou bases de données.
+ Les valeurs d'ID peuvent mieux s'intégrer à la logique d'une application.

Vous pouvez également utiliser des scripts serveur pour définir des valeurs d'ID. L'exemple de script ci-dessous génère un GUID personnalisé et l'attribue à l'ID d'un nouvel enregistrement. Il est semblable à la valeur d'ID qui serait générée par Mobile Services si vous ne transmettiez pas de valeur pour l'ID d'un enregistrement.

	//Example of generating an id. This is not required since Mobile Services
	//will generate an id if one is not passed in.
	item.id = item.id || newGuid();
	request.execute();

	function newGuid() {
		var pad4 = function(str) { return "0000".substring(str.length) + str; };
		var hex4 = function () { return pad4(Math.floor(Math.random() * 0x10000 /* 65536 */ ).toString(16)); };
		return (hex4() + hex4() + "-" + hex4() + "-" + hex4() + "-" + hex4() + "-" + hex4() + hex4() + hex4());
	}


Si une application fournit la valeur d'un ID, Mobile Services la stocke en l'état. Les espaces de début et de fin sont également inclus. Ils ne sont pas supprimés de la valeur.

La valeur d' `id` doit être unique et ne contenir aucun caractère présent dans les ensembles suivants :

+ Caractères de contrôle : [0x0000-0x001F] et [0x007F-0x009F]. Pour plus d'informations, consultez la page [Codes de contrôle ASCII C0 et C1].
+  Caractères imprimables : **"**(0x0022), **\+** (0x002B), **/** (0x002F), **?** (0x003F), **\\** (0x005C), **`** (0x0060)
+  Les ID " . " et " .. "

Vous pouvez également utiliser des ID d'entier pour vos tables. Pour pouvoir utiliser un ID d'entier, vous devez créer votre table avec la commande `mobile table create` à l'aide de l'option --integerId. Cette commande s'utilise avec l'interface de ligne de commande (CLI) pour Azure. Pour plus d'informations sur l'utilisation de l'interface de ligne de commande, consultez la page [Interface de ligne de commande pour la gestion des tables Mobile Services].

Lorsqu'un schéma dynamique est activé, Mobile Services génère automatiquement de nouvelles colonnes basées sur les champs de l'objet dans la demande d'insertion ou de mise à jour. Pour plus d'informations, consultez la page [Schéma dynamique].

<h2><a name="modifying"></a>Procédure : Modification des données d'un service mobile</h2>

Mettez à jour un objet existant en modifiant un élément renvoyé dans le résultat d'une requête précédente, puis en appelant la fonction **update**.

	NSMutableDictionary *item = [self.results.item objectAtIndex:0];
	[item setObject:@YES forKey:@"complete"];
	[table update:item completion:^(NSDictionary *item, NSError *error) {
		//handle errors or any additional logic as needed
	}];

Dans le cadre d'une mise à jour, il vous suffit d'indiquer le champ en cours de mise à jour, ainsi que l'ID de la ligne, comme dans l'exemple suivant :

	[table update:@{@"id" : @"37BBF396-11F0-4B39-85C8-B319C729AF6D", @"Complete": @YES} completion:^(NSDictionary *item, NSError *error) {
		//handle errors or any additional logic as needed
	}];


Pour supprimer un élément de la table, transmettez simplement l'élément à la méthode delete, comme suit :

	[table delete:item completion:^(id itemId, NSError *error) {
		//handle errors or any additional logic as needed
	}];

Vous pouvez également uniquement supprimer un enregistrement en utilisant directement son ID, comme dans l'exemple suivant :

	[table deleteWithId:@"37BBF396-11F0-4B39-85C8-B319C729AF6D" completion:^(id itemId, NSError *error) {
		//handle errors or any additional logic as needed
	}];

Notez que, au minimum, l'attribut `id` doit être défini lorsque vous effectuez des mises à jour et des suppressions.

<h2><a name="authentication"></a>Procédure : Authentification des utilisateurs</h2>

Mobile Services vous permet d'utiliser les fournisseurs d'identité suivants pour authentifier les utilisateurs :

- Facebook
- Google
- Compte Microsoft
- Twitter
- Azure Active Directory

Pour plus d'informations sur la configuration d'un fournisseur d'identité, consultez la page [Prise en main de l'authentification].

Mobile Services prend en charge les deux workflows d'authentification suivants :

- Sur une connexion gérée par le serveur, Mobile Services gère le processus de connexion pour le compte de votre application. La bibliothèque cliente affiche une page de connexion spécifique au fournisseur et Mobile Services procède à l'authentification avec le fournisseur choisi.

- Sur une connexion gérée par le client, l'application doit demander un jeton au fournisseur d'identité, puis le présenter à Mobile Services pour authentification.

Une fois l'authentification réussie, un objet utilisateur qui contient la valeur d'ID utilisateur attribuée et le jeton d'authentification est renvoyé. Cet ID utilisateur peut être utilisé dans les scripts serveur pour valider ou modifier les requêtes. Pour plus d'informations, consultez la page [Utilisation de scripts pour autoriser les utilisateurs]. Le jeton lui-même peut être mis en cache de manière sécurisée pour utilisation lors des connexions suivantes.

Vous pouvez également définir des autorisations sur les tables pour limiter l'accès à certaines opérations aux seuls utilisateurs authentifiés. Pour plus d'informations, consultez la page [Autorisations].

### Connexion gérée par serveur

Voici un exemple de méthode de connexion utilisant un compte Microsoft. Ce code peut être appelé dans le ViewDidLoad de votre contrôleur ou déclenché manuellement via un UIButton. Une interface utilisateur standard permettant de consigner le fournisseur d'identité apparaît à l'écran.

	[client loginWithProvider:@"MicrosoftAccount" controller:self animated:YES
		completion:^(MSUser *user, NSError *error) {
			NSString *msg;
			if(error) {
				msg = [@"An error occured: " stringByAppendingString:error.description];
			} else {
				msg = [@"You are logged in as " stringByAppendingString:user.userId];
			}

			UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Login"
								  message:msg
								  delegate:nil
								  cancelButtonTitle:@"OK"
								  otherButtonTitles: nil];
			[alert show];
	}];

Remarque : Si vous utilisez un fournisseur d'identité différent de celui du compte Microsoft, remplacez la valeur transmise à la méthode login ci-dessus par l'une des valeurs suivantes : `facebook`, `twitter`, `google` ou `windowsazureactivedirectory`.

Vous pouvez également obtenir une référence pointant vers l'objet MSLoginController et l'afficher vous-même en utilisant :

	-(MSLoginController *)loginViewControllerWithProvider:(NSString *)provider completion:(MSClientLoginBlock)completion;

### Connexion gérée par client (authentification unique)

Dans certains cas, le processus de connexion est réalisé hors du client Mobile Services. Vous pouvez choisir cette méthode pour activer une fonctionnalité d'authentification unique ou si votre application doit contacter le fournisseur d'identité directement pour obtenir les informations utilisateur. Dans ces cas-là, vous pouvez vous connecter à Mobile Services en fournissant un jeton obtenu indépendamment auprès d'un fournisseur d'identité pris en charge.

L'exemple suivant utilise le [Kit de développement logiciel (SDK) Live Connect] permettant d'activer l'authentification unique pour les applications iOS.

	[client loginWithProvider:@"microsoftaccount"
		token:@{@"authenticationToken" : self.liveClient.session.authenticationToken}
		completion:^(MSUser *user, NSError *error) {
			self.loggedInUser = user;
			NSString *msg = [@"You are logged in as " stringByAppendingString:user.userId];
			UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Login"
				message:msg
				delegate:nil
				cancelButtonTitle:@"OK"
				otherButtonTitles: nil];
			[alert show];
	}];

Ce code part du principe que vous avez déjà créé une instance **LiveConnectClient** nommée `liveClient` dans le contrôleur et que l'utilisateur est connecté.

###<a name="caching-tokens"></a>Procédure : Mise en cache des jetons d'authentification

Pour éviter aux utilisateurs de s'authentifier chaque fois qu'ils exécutent l'application, vous pouvez mettre en cache leur identité actuelle une fois qu'ils sont connectés. Vous pouvez alors utiliser ces informations pour créer directement les utilisateurs et ignorer le processus de connexion.  Pour ce faire, vous devez stocker l'ID utilisateur et le jeton d'authentification en local. Dans l'exemple suivant, le jeton est mis en cache de manière sécurisée dans le [Trousseau d'accès] :

	- (NSMutableDictionary *) createKeyChainQueryWithClient:(MSClient *)client andIsSearch:(bool)isSearch
	{
		NSMutableDictionary *query = [[NSMutableDictionary alloc] init];
		[query setObject:(__bridge id)kSecClassInternetPassword forKey:(__bridge id)(kSecClass)];
		[query setObject:client.applicationURL.absoluteString forKey:(__bridge id)(kSecAttrServer)];

		if(isSearch) {
			// Use the proper search constants, return only the attributes of the first match.
			[query setObject:(__bridge id)kSecMatchLimitOne forKey:(__bridge id)kSecMatchLimit];
			[query setObject:(id)kCFBooleanTrue forKey:(__bridge id)kSecReturnAttributes];
			[query setObject:(id)kCFBooleanTrue forKey:(__bridge id)kSecReturnData];
		}

		return query;
	}

	- (IBAction)loginUser:(id)sender {
		NSMutableDictionary *query = [self createKeyChainQueryWithClient:self.todoService.client andIsSearch:YES];
		CFDictionaryRef cfresult;

		OSStatus status = SecItemCopyMatching((__bridge CFDictionaryRef)query, (CFTypeRef *)&cfresult);
		if (status == noErr) {
			NSDictionary * result = (__bridge_transfer NSDictionary*) cfresult;

			//create an MSUser object
			MSUser *user = [[MSUser alloc] initWithUserId:[result objectForKey:(__bridge id)(kSecAttrAccount)]];
			NSData *data = [result objectForKey:(__bridge id)(kSecValueData)];
			user.mobileServiceAuthenticationToken = [[NSString alloc] initWithData:data encoding:NSUTF8StringEncoding];
			[self.todoService.client setCurrentUser:user];

		} else if (status == errSecItemNotFound) {
			//we need to log the user in
			[self.todoService.client loginWithProvider:@"MicrosoftAccount" controller:self animated:YES
				completion:^(MSUser *user, NSError *error) {
					NSString *msg = [@"You are logged in as " stringByAppendingString:user.userId];
					UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Login"
											message:msg delegate:nil
											cancelButtonTitle:@"OK"
											otherButtonTitles: nil];
					[alert show];

					//save the user id and token to the KeyChain
					NSMutableDictionary *newItem = [self createKeyChainQueryWithClient:self.todoService.client
															andIsSearch:NO];
					[newItem setObject:user.userId forKey:(__bridge id)kSecAttrAccount];
					[newItem setObject:[user.mobileServiceAuthenticationToken dataUsingEncoding:NSUTF8StringEncoding]
                                                    forKey:(__bridge id)kSecValueData];

					OSStatus status = SecItemAdd((__bridge CFDictionaryRef)newItem, NULL);
					if(status != errSecSuccess) {
						//handle error as needed
						NSAssert(NO, @"Error caching password.");
					}
			}];
		}

> [AZURE.NOTE] Les jetons sont des données sensibles qui doivent être chiffrées avant d'être stockées (ce qui évitera ainsi tout préjudice en cas de perte ou de vol de l'appareil).

Si un jeton mis en cache est utilisé, l'utilisateur n'a besoin de se connecter à nouveau que si le jeton expire. Lorsqu'un utilisateur tente de se connecter avec un jeton arrivé à expiration, une réponse avec le code 401 (Non autorisé) lui est renvoyée. Dans ce cas, l'utilisateur doit à nouveau se connecter pour obtenir un nouveau jeton, qui peut à nouveau être mis en cache. Vous pouvez utiliser des filtres pour éviter d'avoir à écrire du code qui gère les jetons arrivés à expiration chaque fois que votre application appelle le service mobile.  Les filtres vous permettent ainsi d'intercepter les appels passés à votre service mobile et ses réponses. Le code figurant dans le filtre vérifie si la réponse est associée à un code 401, déclenche le processus de connexion si le jeton est arrivé à expiration, puis relance la requête qui a généré le code 401. Pour plus d'informations, consultez la page [Gestion des jetons arrivés à expiration].

<h2><a name="errors"></a>Procédure : Gestion des erreurs</h2>

Lorsqu'un appel est passé au service mobile, le bloc completion contient un paramètre `NSError *error`. Si une erreur se produit, une valeur non nulle est renvoyée pour ce paramètre. Vous devez vérifier ce paramètre dans votre code et gérer les erreurs, le cas échéant.

En cas d'erreur, vous pouvez obtenir plus d'informations en incluant le fichier MSError.h dans le code :

    #import <WindowsAzureMobileServices/MSError.h>

Ce fichier définit les constantes suivantes que vous pouvez utiliser pour accéder à des données complémentaires dans [error userInfo] :

+ **MSErrorResponseKey** : données de réponse HTTP associées à l'erreur
* **MSErrorRequestKey** : données de requête HTTP associées à l'erreur

En outre, une constante est définie pour chaque code d'erreur. Le fichier MSError.h contient une description de ces codes.

Pour obtenir un exemple de processus et de gestion de validation, consultez la rubrique [Validation et modification de données dans Mobile Services à l'aide de scripts serveur]. Dans cette rubrique, la validation côté serveur est mise en œuvre à l'aide de scripts serveur. Si des données incorrectes sont soumises, une réponse indiquant une erreur est renvoyée et cette réponse est gérée par le client.

<!--
<h2><a name="#unit-testing"></a>Procédure : Conception de tests</h2>

_(Facultatif) Cette section explique comment écrire un test unitaire lors de l'utilisation de la bibliothèque cliente (informations de Yavor)._

<h2><a name="#customizing"></a>Procédure : Personnalisation du client</h2>

_(Facultatif) Cette section explique comment envoyer des comportements client personnalisés._

###<a name="custom-headers"></a>Procédure : Personnalisation des en-têtes de requête

_(Facultatif) Cette section explique comment envoyer des en-têtes de demande personnalisés._

Pour plus d'informations, consultez la nouvelle rubrique relative au traitement des en-têtes côté serveur.

###<a name="custom-serialization"></a>Procédure : Personnalisation de la sérialisation

_(Facultatif) Cette section explique comment utiliser des attributs pour personnaliser la sérialisation des types de données._

Pour plus d'informations, consultez la nouvelle rubrique relative au traitement des en-têtes côté serveur.

## <a name="next-steps"></a>Étapes suivantes
-->

<!-- Anchors. -->

[Présentation de Mobile Services]: #what-is
[Concepts]: #concepts
[Configuration et conditions préalables]: #Setup
[Procédure : Création du client Mobile Services]: #create-client
[Procédure : Création d'une référence de table]: #table-reference
[Procédure : Interrogation des données à partir d'un service mobile]: #querying
[Filtrage des données renvoyées]: #filtering
[Tri des données renvoyées]: #sorting
[Renvoi de données dans les pages]: #paging
[Sélection de colonnes spécifiques]: #selecting
[Procédure : Liaison des données dans l'interface utilisateur]: #binding
[Procédure : Insertion de données dans un service mobile]: #inserting
[Procédure : Modification des données d'un service mobile]: #modifying
[Procédure : Authentification des utilisateurs]: #authentication
[Mise en cache des jetons d'authentification]: #caching-tokens
[Procédure : Téléchargement d'images et de fichiers volumineux]: #blobs
[Procédure : Gestion des erreurs]: #errors
[Procédure : Conception de tests]: #unit-testing
[Procédure : Personnalisation du client]: #customizing
[Personnalisation des en-têtes de requête]: #custom-headers
[Personnalisation de la sérialisation du type de données]: #custom-serialization
[Étapes suivantes]: #next-steps
[Procédure : Utilisation de MSQuery]: #query-object

<!-- Images. -->

<!-- URLs. -->
[Prise en main de Mobile Services]: /fr-fr/develop/mobile/tutorials/get-started-ios
[Validation et modification de données dans Mobile Services à l'aide de scripts serveur]: /fr-fr/develop/mobile/tutorials/validate-modify-and-augment-data-ios
[Kit de développement logiciel (SDK) Mobile Services]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[Prise en main de l'authentification]: /fr-fr/develop/mobile/tutorials/get-started-with-users-ios
[Kit de développement logiciel (SDK) iOS]: https://developer.apple.com/xcode

[Gestion des jetons arrivés à expiration]: http://go.microsoft.com/fwlink/p/?LinkId=301955
[Kit de développement logiciel (SDK) Live Connect]: http://go.microsoft.com/fwlink/p/?LinkId=301960
[Autorisations]: http://msdn.microsoft.com/library/windowsazure/jj193161.aspx
[Utilisation de scripts pour autoriser des utilisateurs]: /fr-fr/develop/mobile/tutorials/authorize-users-in-scripts-ios
[Schéma dynamique]: http://go.microsoft.com/fwlink/p/?LinkId=296271
[Accès aux paramètres personnalisés]: /fr-fr/develop/mobile/how-to-guides/work-with-server-scripts#access-headers
[Création d'une table]: http://msdn.microsoft.com/library/windowsazure/jj193162.aspx
[Objet NSDictionary]: http://go.microsoft.com/fwlink/p/?LinkId=301965
[Codes de contrôle ASCII C0 et C1]: http://en.wikipedia.org/wiki/Data_link_escape_character#C1_set
[Interface de ligne de commande pour la gestion des tables Mobile Services]: http://azure.microsoft.com/manage/linux/other-resources/command-line-tools/#Mobile_Tables



<!--HONumber=42-->
