<properties
	pageTitle="Utilisation de la bibliothèque cliente gérée App Service Mobile Apps (Windows | Xamarin) | Microsoft Azure"
	description="Apprenez à utiliser un client .NET pour Azure App Service Mobile Apps avec des applications Windows et Xamarin."
	services="app-service\mobile"
	documentationCenter=""
	authors="ggailey777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-multiple"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="03/08/2016"
	ms.author="glenga"/>

# Utilisation du client géré pour Azure Mobile Apps

[AZURE.INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

##Vue d’ensemble

Ce guide vous montre comment mettre en place des scénarios courants à l’aide de la bibliothèque cliente gérée pour Azure App Service Mobile Apps pour les applications Windows et Xamarin. Si vous débutez avec Mobile Apps, suivez le didacticiel [Démarrage rapide avec Azure Mobile Apps]. Dans ce guide, nous nous concentrons sur le Kit de développement logiciel (SDK) géré côté client. Pour plus d’informations sur les Kits de développement logiciel (SDK) côté serveur pour Mobile Apps, consultez la documentation du [Kit de développement logiciel (SDK) .NET Server](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) ou du [Kit de développement logiciel (SDK) Node.js Server](app-service-mobile-node-backend-how-to-use-server-sdk.md).

## Documentation de référence

La documentation de référence du Kit de développement logiciel (SDK) client se trouve ici : [Référence du client .NET Azure Mobile Apps]. Vous trouverez plusieurs exemples de client dans le [référentiel GitHub d’exemples Azure].

##<a name="setup"></a>Configuration et conditions préalables

Nous supposons que vous avez déjà créé et publié votre projet de backend Mobile Apps et qu'il comprend au moins une table. Dans le code utilisé dans cette rubrique, la table s'intitule `TodoItem` et contient les colonnes suivantes : `Id`, `Text` et `Complete`. Il s’agit de la table créée lors du [démarrage rapide d’Azure Mobile Apps].

Le type côté client typé en C# correspondant est le suivant :

	public class TodoItem
	{
		public string Id { get; set; }

		[JsonProperty(PropertyName = "text")]
		public string Text { get; set; }

		[JsonProperty(PropertyName = "complete")]
		public bool Complete { get; set; }
	}

L’attribut [JsonPropertyAttribute] est utilisé pour définir le mappage *PropertyName* entre le type client et la table.

Pour savoir comment créer des tables dans votre backend Mobile Apps, consultez les informations contenues dans les [instructions pour le Kit de développement logiciel (SDK) .NET Server](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#define-table-controller) ou [instructions pour le Kit de développement logiciel (SDK) Node.js Server](app-service-mobile-node-backend-how-to-use-server-sdk.md#howto-dynamicschema). Si vous avez créé votre backend Mobile Apps dans le portail Azure avec le démarrage rapide, vous pouvez également utiliser le paramètre **Tables facile** dans le [portail Azure].

###<a name="symbolsource"></a>Instructions : Utilisation des symboles de débogage dans Visual Studio

Les symboles de l’espace de noms Microsoft.Azure.Mobile sont disponibles sur [SymbolSource]. Consultez les [instructions SymbolSource] pour intégrer SymbolSource à Visual Studio.

##<a name="create-client"></a>Création du client Mobile Apps

Le code suivant permet de créer l’objet [MobileServiceClient] utilisé pour accéder à votre backend Mobile Apps.

	MobileServiceClient client = new MobileServiceClient("MOBILE_APP_URL");

Dans le code ci-dessus, remplacez `MOBILE_APP_URL` par l’URL du backend Mobile Apps, qui se trouve dans le panneau de votre backend Mobile Apps du [portail Azure]. Il est normal et recommandé que l’instance du client soit un Singleton.

## Utilisation des tables

La section suivante explique comment rechercher et récupérer les enregistrements et modifier les données dans la table. Cet article contient les rubriques suivantes :

* [Création d'une référence de table](#instantiating)
* [Données de requête](#querying)
* [Filtrer les données renvoyées](#filtering)
* [Trier les données renvoyées](#sorting)
* [Renvoyer les données de pages](#paging)
* [Sélectionner des colonnes spécifiques](#selecting)
* [Rechercher un enregistrement par ID](#lookingup)
* [Traitement des requêtes non typées](#untypedqueries)
* [Insertion de données](#inserting)
* [Mise à jour des données](#updating)
* [Suppression de données](#deleting)
* [Résolution des conflits et accès concurrentiel optimiste](#optimisticconcurrency)
* [Liaison à une Interface utilisateur Windows](#binding)

###<a name="instantiating"></a>Procédure : création d'une référence de table

L’ensemble du code permettant d’accéder aux données d’une table du backend ou de les modifier appelle des fonctions sur l’objet `MobileServiceTable`. Pour obtenir une référence à la table, appelez la méthode [GetTable] sur une instance du `MobileServiceClient`, comme suit :

    IMobileServiceTable<TodoItem> todoTable = client.GetTable<TodoItem>();

Il s'agit du modèle de sérialisation typé. Les modèles de sérialisation non typés sont également pris en charge. Le code suivant [crée une référence à une table non typée] :

	// Get an untyped table reference
	IMobileServiceTable untypedTodoTable = client.GetTable("TodoItem");

Dans les requêtes non typées, vous devez spécifier la chaîne de requête OData sous-jacente.

###<a name="querying"></a>Interrogation des données à partir de votre application mobile

Cette section explique comment émettre des requêtes à destination du backend Mobile Apps, qui inclut les fonctionnalités suivantes :

- [Filtrer les données renvoyées]
- [Trier les données renvoyées]
- [Renvoyer les données de pages]
- [Sélectionner des colonnes spécifiques]
- [Rechercher des données par ID]

>[AZURE.NOTE] Une taille de page gérée par le serveur est imposée pour empêcher le renvoi de toutes les lignes. Cela permet d'éviter que les requêtes par défaut associées à des jeux de données volumineux aient un impact négatif sur le service. Pour obtenir le renvoi de plus de 50 lignes, utilisez la méthode `Take`, comme décrit dans la section [Renvoyer les données de pages].

###<a name="filtering"></a>Procédure : filtrage des données renvoyées

Le code suivant montre comment filtrer des données en incluant une clause `Where` dans une requête. Il renvoie tous les éléments de `todoTable` dont la propriété `Complete` est égale à `false`. La fonction [Where] applique un prédicat de filtrage de ligne à la requête au niveau de la table.

	// This query filters out completed TodoItems and items without a timestamp.
	List<TodoItem> items = await todoTable
	   .Where(todoItem => todoItem.Complete == false)
	   .ToListAsync();

Vous pouvez afficher l’URI de la requête envoyée au backend en utilisant un logiciel d’inspection des messages, par exemple les outils destinés aux développeurs de navigateurs ou [Fiddler]. Si vous examinez l’URI de requête ci-dessous, vous remarquerez que la chaîne de requête elle-même est modifiée :

	GET /tables/todoitem?$filter=(complete+eq+false) HTTP/1.1

Cette requête OData est traduite en requête SQL par le Kit de développement logiciel (SDK) de serveur qui est similaire à ceci :

	SELECT *
	FROM TodoItem
	WHERE ISNULL(complete, 0) = 0

La fonction transmise à la méthode `Where` peut avoir un nombre de conditions arbitraire. Par exemple, la ligne ci-dessous :

	// This query filters out completed TodoItems where Text isn't null
	List<TodoItem> items = await todoTable
	   .Where(todoItem => todoItem.Complete == false && todoItem.Text != null)
	   .ToListAsync();

Serait traduite en requête SQL par le Kit de développement logiciel (SDK) de serveur qui est similaire à ceci :

	SELECT *
	FROM TodoItem
	WHERE ISNULL(complete, 0) = 0
	      AND ISNULL(text, 0) = 0

L'instruction `WHERE` ci-dessus recherche les éléments dont l'état `Complete` a la valeur false et la colonne `Text` la valeur non null.

Cette requête peut également être fractionnée en plusieurs clauses :

	List<TodoItem> items = await todoTable
	   .Where(todoItem => todoItem.Complete == false)
	   .Where(todoItem => todoItem.Text != null)
	   .ToListAsync();

Les deux méthodes sont équivalentes et peuvent être utilisées de manière interchangeable. La dernière option, qui consiste à concaténer plusieurs prédicats dans une même requête, est plus compacte. C’est celle que nous recommandons.

La clause `Where` prend en charge les opérations traduites dans le sous-ensemble OData. Cela inclut les opérateurs relationnels (==, !=, <, <=, >, >=), les opérateurs arithmétiques (+, -, /, *, %), la précision des nombres (Math.Floor, Math.Ceiling), les fonctions de chaîne (Length, Substring, Replace, IndexOf, StartsWith, EndsWith), les propriétés de date (Year, Month, Day, Hour, Minute, Second), les propriétés d'accès d'un objet, ainsi que les expressions qui combinent tous ces éléments. Lorsque vous envisagez ce que le Kit de développement logiciel (SDK) de serveur prend en charge, vous pouvez consulter la [documentation OData v3].

###<a name="sorting"></a>Procédure : tri des données renvoyées

Le code suivant montre comment trier les données en incluant une fonction [OrderBy] ou [OrderByDescending] dans la requête. Il renvoie des éléments de `todoTable`, triés par ordre croissant dans le champ `Text`.

	// Sort items in ascending order by Text field
	MobileServiceTableQuery<TodoItem> query = todoTable
					.OrderBy(todoItem => todoItem.Text)
 	List<TodoItem> items = await query.ToListAsync();

	// Sort items in descending order by Text field
	MobileServiceTableQuery<TodoItem> query = todoTable
					.OrderByDescending(todoItem => todoItem.Text)
 	List<TodoItem> items = await query.ToListAsync();

###<a name="paging"></a>Procédure : renvoi de données dans les pages

Par défaut, le backend renvoie uniquement les 50 premières lignes. Vous pouvez augmenter le nombre de lignes renvoyées en appelant la méthode [Take]. Associez `Take` à la méthode [Skip] pour demander une « page » spécifique du jeu de données total renvoyé par la requête. Lorsqu'elle est exécutée, la requête suivante renvoie les trois premiers éléments de la table.

	// Define a filtered query that returns the top 3 items.
	MobileServiceTableQuery<TodoItem> query = todoTable
					.Take(3);
	List<TodoItem> items = await query.ToListAsync();

La requête révisée ci-dessous ignore les trois premiers résultats et renvoie les trois résultats suivants. Il s'agit en fait de la deuxième « page » de données, dont la taille est de trois éléments.

	// Define a filtered query that skips the top 3 items and returns the next 3 items.
	MobileServiceTableQuery<TodoItem> query = todoTable
					.Skip(3)
					.Take(3);
	List<TodoItem> items = await query.ToListAsync();

Vous pouvez également utiliser la méthode [IncludeTotalCount] pour que la requête obtienne le nombre total de <i>tous</i> les enregistrements qui auront été renvoyés, en ignorant toute clause de pagination/limite spécifiée :

	query = query.IncludeTotalCount();

Il s'agit d'un scénario simplifié dans lequel les valeurs de pagination codées en dur sont transmises aux méthodes `Take` et `Skip`. Dans une application réelle, vous pouvez utiliser des requêtes semblables à celles indiquées plus haut avec un contrôle pager ou une interface utilisateur comparable pour permettre aux utilisateurs d'accéder aux pages précédentes et suivantes.

>[AZURE.NOTE]Pour remplacer la limite de 50 lignes dans un backend Mobile Apps, vous devez également appliquer l’attribut [EnableQueryAttribute] à la méthode GET publique et spécifier le comportement de pagination. Lorsqu'il est appliqué à la méthode, l'exemple suivant définit le nombre maximal de lignes renvoyées à 1 000 :
>
>    [EnableQuery(MaxTop=1000)]

### <a name="selecting"></a>Procédure : sélection de colonnes spécifiques

Vous pouvez indiquer le jeu de propriétés à inclure dans les résultats en ajoutant une clause [Select] à la requête. Par exemple, le code suivant montre comment sélectionner un seul champ et comment sélectionner et mettre en forme plusieurs champs :

	// Select one field -- just the Text
	MobileServiceTableQuery<TodoItem> query = todoTable
					.Select(todoItem => todoItem.Text);
	List<string> items = await query.ToListAsync();

	// Select multiple fields -- both Complete and Text info
	MobileServiceTableQuery<TodoItem> query = todoTable
					.Select(todoItem => string.Format("{0} -- {1}",
						todoItem.Text.PadRight(30), todoItem.Complete ?
						"Now complete!" : "Incomplete!"));
	List<string> items = await query.ToListAsync();

Toutes les fonctions décrites jusqu'ici étant cumulatives, nous pouvons continuer de les appeler, ce qui aura à chaque fois des répercussions sur la requête. Un exemple supplémentaire :

	MobileServiceTableQuery<TodoItem> query = todoTable
					.Where(todoItem => todoItem.Complete == false)
					.Select(todoItem => todoItem.Text)
					.Skip(3).
					.Take(3);
	List<string> items = await query.ToListAsync();

### <a name="lookingup"></a>Procédure : recherche de données par ID

La fonction [LookupAsync] permet de rechercher des objets dans la base de données à partir d'un ID particulier.

	// This query filters out the item with the ID of 37BBF396-11F0-4B39-85C8-B319C729AF6D
	TodoItem item = await todoTable.LookupAsync("37BBF396-11F0-4B39-85C8-B319C729AF6D");

### <a name="untypedqueries"></a>Exécution de requêtes non typées

Lorsque vous exécutez une requête avec un objet de table non typé, vous devez spécifier explicitement la chaîne de requête OData en appelant [ReadAsync], comme dans l'exemple suivant :

	// Lookup untyped data using OData
	JToken untypedItems = await untypedTodoTable.ReadAsync("$filter=complete eq 0&$orderby=text");

Vous obtenez en retour des valeurs JSON que vous pouvez utiliser comme conteneur de propriétés. Pour plus d’informations sur JToken et Newtonsoft Json.NET, consultez le site [Json.NET]

### <a name="inserting"></a>Insertion de données dans un backend Mobile Apps

Tous les types clients doivent contenir un membre nommé **Id**, par défaut une chaîne. Cet **Id** est requis pour effectuer des opérations CRUD et hors connexion. Le code suivant montre comment utiliser la méthode [InsertAsync] pour insérer de nouvelles lignes dans une table. Le paramètre contient les données à insérer sous forme d'objet .NET.

	await todoTable.InsertAsync(todoItem);

Si aucune valeur d'ID personnalisée unique n'est incluse dans l'objet `todoItem` transmis à l'appel de `todoTable.InsertAsync`, une valeur d'ID est générée par le serveur défini dans l'objet `todoItem` renvoyé au client.

Pour insérer des données non typées, vous pouvez tirer parti de Json.NET comme indiqué ci-dessous.

	JObject jo = new JObject();
	jo.Add("Text", "Hello World");
	jo.Add("Complete", false);
	var inserted = await table.InsertAsync(jo);

Voici un exemple utilisant une adresse de messagerie comme ID de chaîne unique.

	JObject jo = new JObject();
	jo.Add("id", "myemail@emaildomain.com");
	jo.Add("Text", "Hello World");
	jo.Add("Complete", false);
	var inserted = await table.InsertAsync(jo);

###Utilisation de valeurs d'ID

Mobile Apps prend en charge des valeurs de chaîne personnalisées uniques pour la colonne **id** de la table. Cela permet aux applications d’utiliser des valeurs personnalisées telles que les adresses de messagerie ou des noms d’utilisateur pour l’ID.

Les ID de chaîne fournissent les avantages suivants :

* Les ID sont générés sans effectuer d’aller-retour vers la base de données.
* Il est plus facile de fusionner des enregistrements de plusieurs tables ou bases de données.
* Les valeurs d’ID peuvent mieux s’intégrer à la logique d’une application.

Lorsque la valeur d’ID d’une chaîne n’est pas définie sur un enregistrement inséré, le backend Mobile Apps génère une valeur unique pour l’ID. Vous pouvez utiliser la méthode [Guid.NewGuid] pour générer vos propres valeurs d’ID, sur le client ou dans le backend.

    JObject jo = new JObject();
    jo.Add("id", Guid.NewGuid().ToString("N"));

###<a name="modifying"></a>Modification de données dans un backend Mobile Apps

Le code suivant montre comment utiliser la méthode [UpdateAsync] pour mettre à jour un enregistrement existant avec le même ID avec des informations nouvelles. Le paramètre contient les données à mettre à jour sous forme d'objet .NET.

	await todoTable.UpdateAsync(todoItem);

Pour insérer des données non typées, vous pouvez utiliser [Json.NET] ainsi :

	JObject jo = new JObject();
	jo.Add("id", "37BBF396-11F0-4B39-85C8-B319C729AF6D");
	jo.Add("Text", "Hello World");
	jo.Add("Complete", false);
	var inserted = await table.UpdateAsync(jo);

Vous devez spécifier un champ `id` lors d’une mise à jour. C'est grâce à cela que le backend identifie l'instance à mettre à jour. Le champ `id` peut être obtenu à partir du résultat de l’appel de `InsertAsync`. Si vous essayez de mettre à jour un élément sans fournir de valeur `id`, une `ArgumentException` se déclenche.

###<a name="deleting"></a>Suppression de données dans un backend Mobile Apps

Le code suivant montre comment utiliser la méthode [DeleteAsync] pour supprimer une instance existante. L’instance est identifiée par le champ `id` défini au niveau de `todoItem`.

	await todoTable.DeleteAsync(todoItem);

Pour supprimer des données non typées, vous pouvez utiliser Json.NET ainsi :

	JObject jo = new JObject();
	jo.Add("id", "37BBF396-11F0-4B39-85C8-B319C729AF6D");
	await table.DeleteAsync(jo);

Vous devez spécifier un ID lorsque vous effectuez une requête de suppression. Les autres propriétés ne sont pas transmises au service ou sont ignorées au niveau du service. Le résultat d’un appel `DeleteAsync` a généralement la valeur `null`. L'ID à transmettre peut être obtenu à partir du résultat de l'appel `InsertAsync`. Une `MobileServiceInvalidOperationException` est levée lorsque vous essayez de supprimer un élément sans spécifier le champ `id`.

###<a name="optimisticconcurrency"></a>Instructions : Utilisation de l’accès concurrentiel optimiste pour résoudre les conflits

Plusieurs clients peuvent écrire à un même moment des modifications dans un même élément. En l'absence de détection de conflits, la dernière écriture remplace les mises à jour précédentes, même si cela n'était pas le but recherché. Le *contrôle d'accès concurrentiel optimiste* considère que chaque transaction peut être validée et, qu’à ce titre, elle ne fait appel à aucun verrouillage de ressources. Avant de valider une transaction, le contrôle d'accès concurrentiel optimiste vérifie qu'aucune autre transaction n'a modifié les données. Si les données ont été modifiées, la transaction de validation est annulée.

Mobile Apps prend en charge le contrôle d'accès concurrentiel optimiste en suivant les modifications apportées à chaque élément à l'aide de la colonne de la propriété système `version` définie pour chaque table de votre serveur principal Mobile Apps. Chaque fois qu’un enregistrement est mis à jour, Mobile Apps attribue une nouvelle valeur à la propriété `version` de cet enregistrement. À chaque demande de mise à jour, la propriété `\version` de l'enregistrement inclus dans la demande est comparée à celle de l'enregistrement basé sur le serveur. Si la version transmise avec la demande ne correspond pas à celle du serveur principal, la bibliothèque cliente déclenche une exception `MobileServicePreconditionFailedException<T>`. Le type inclus avec l’exception est l’enregistrement du backend contenant la version serveur de l’enregistrement. À partir de cette information, l’application peut décider ou non d’exécuter à nouveau la requête de mise à jour avec la valeur `version` correcte du serveur principal pour valider les modifications.

Pour activer l’accès concurrentiel optimiste, l’application définit une colonne sur la classe table de la propriété système `version`, par exemple :

    public class TodoItem
    {
        public string Id { get; set; }

        [JsonProperty(PropertyName = "text")]
        public string Text { get; set; }

        [JsonProperty(PropertyName = "complete")]
        public bool Complete { get; set; }

		// *** Enable Optimistic Concurrency *** //
        [JsonProperty(PropertyName = "version")]
        public byte[] Version { set; get; }
    }


Dans le cas des applications utilisant des tables non typées, l'accès concurrentiel optimiste est activé en définissant l'indicateur `Version` au niveau de la propriété `SystemProperties` de la table, comme suit.

	//Enable optimistic concurrency by retrieving version
	todoTable.SystemProperties |= MobileServiceSystemProperties.Version;

Outre l’activation de l’accès concurrentiel optimiste, vous devez également intercepter l’exception `MobileServicePreconditionFailedException<T>` dans votre code lors de l’appel [UpdateAsync]. Résolvez le conflit en appliquant la `version` correcte pour l’enregistrement mis à jour et appelez [UpdateAsync] avec l’enregistrement résolu. Le code suivant montre comment résoudre un conflit d’écriture une fois qu’il est détecté :

	private async void UpdateToDoItem(TodoItem item)
	{
    	MobileServicePreconditionFailedException<TodoItem> exception = null;

	    try
    	{
	        //update at the remote table
    	    await todoTable.UpdateAsync(item);
    	}
    	catch (MobileServicePreconditionFailedException<TodoItem> writeException)
	    {
        	exception = writeException;
	    }

    	if (exception != null)
    	{
			// Conflict detected, the item has changed since the last query
        	// Resolve the conflict between the local and server item
	        await ResolveConflict(item, exception.Item);
    	}
	}


	private async Task ResolveConflict(TodoItem localItem, TodoItem serverItem)
	{
    	//Ask user to choose the resoltion between versions
	    MessageDialog msgDialog = new MessageDialog(
            String.Format("Server Text: "{0}" \nLocal Text: "{1}"\n",
            serverItem.Text, localItem.Text),
            "CONFLICT DETECTED - Select a resolution:");

	    UICommand localBtn = new UICommand("Commit Local Text");
    	UICommand ServerBtn = new UICommand("Leave Server Text");
    	msgDialog.Commands.Add(localBtn);
	    msgDialog.Commands.Add(ServerBtn);

    	localBtn.Invoked = async (IUICommand command) =>
	    {
        	// To resolve the conflict, update the version of the item being committed. Otherwise, you will keep
        	// catching a MobileServicePreConditionFailedException.
	        localItem.Version = serverItem.Version;

    	    // Updating recursively here just in case another change happened while the user was making a decision
	        UpdateToDoItem(localItem);
    	};

	    ServerBtn.Invoked = async (IUICommand command) =>
    	{
	        RefreshTodoItems();
    	};

	    await msgDialog.ShowAsync();
	}

Pour en savoir plus, consultez la rubrique [Synchronisation des données hors connexion dans Azure Mobile Apps].

###<a name="binding"></a>Procédure : liaison de données Mobile Apps à une interface utilisateur Windows

Cette section montre comment afficher des objets de données renvoyés à l'aide d'éléments d'interface utilisateur dans une application Windows. Pour lancer une requête sur les éléments incomplets de `todoTable` et les afficher dans une liste très simple, vous pouvez exécuter l’exemple de code suivant pour lier la source de la liste à une requête. [MobileServiceCollection] permet de créer une collection de liaisons prenant en charge Mobile Apps.

	// This query filters out completed TodoItems.
	MobileServiceCollection<TodoItem, TodoItem> items = await todoTable
		.Where(todoItem => todoItem.Complete == false)
		.ToCollectionAsync();

	// itemsControl is an IEnumerable that could be bound to a UI list control
	IEnumerable itemsControl  = items;

	// Bind this to a ListBox
	ListBox lb = new ListBox();
	lb.ItemsSource = items;

Certains contrôles dans l’exécution gérée prennent en charge une interface appelée [ISupportIncrementalLoading]. Cette interface permet aux contrôles de demander des données supplémentaires lorsque l'utilisateur fait défiler l'écran. Une prise en charge de cette interface peut être intégrée aux applications Windows universelles via [MobileServiceIncrementalLoadingCollection], qui traite automatiquement les appels en provenance des contrôles. Pour utiliser `MobileServiceIncrementalLoadingCollection` dans des applications Windows, procédez comme suit :

    MobileServiceIncrementalLoadingCollection<TodoItem,TodoItem> items;
    items = todoTable.Where(todoItem => todoItem.Complete == false).ToIncrementalLoadingCollection();

    ListBox lb = new ListBox();
    lb.ItemsSource = items;

Pour utiliser la nouvelle collection sur les applications Windows Phone 8 et « Silverlight », utilisez les méthodes d’extension `ToCollection` au niveau de `IMobileServiceTableQuery<T>` et `IMobileServiceTable<T>`. Pour charger réellement les données, appelez `LoadMoreItemsAsync()`.

	MobileServiceCollection<TodoItem, TodoItem> items = todoTable.Where(todoItem => todoItem.Complete==false).ToCollection();
	await items.LoadMoreItemsAsync();

Lorsque vous utilisez la collection créée par l'appel de `ToCollectionAsync` ou `ToCollection`, vous obtenez une collection qui peut être liée aux contrôles d'interface utilisateur. Cette collection prend en charge la pagination, ce qui signifie qu'un contrôle peut demander à la collection de « charger plus d'éléments », ce qu'elle fera pour le contrôle. À ce stade, aucun code utilisateur n'intervient ; c'est le contrôle qui démarre le flux. Toutefois, sachant que la collection charge les données à partir du réseau, ce chargement peut parfois échouer. Pour gérer ces échecs, vous pouvez ignorer la méthode `OnException` au niveau de `MobileServiceIncrementalLoadingCollection` pour traiter les exceptions résultant des appels de `LoadMoreItemsAsync` émis par les contrôles.

Enfin, imaginez que votre table contient de nombreux champs, mais que vous ne souhaitez en afficher qu'une partie dans votre contrôle. Vous pouvez suivre les instructions fournies plus haut dans la section « [Sélectionner des colonnes spécifiques](#selecting) » pour sélectionner les colonnes à afficher dans l’interface utilisateur.

##<a name="#customapi"></a>Utilisation d’une API personnalisée

Une API personnalisée vous permet de définir des points de terminaison exposant une fonctionnalité de serveur qui ne mappe pas vers une opération d'insertion, de mise à jour, de suppression ou de lecture. En utilisant une API personnalisée, vous pouvez exercer davantage de contrôle sur la messagerie, notamment lire et définir des en-têtes de message HTTP et définir un format de corps de message autre que JSON.

Vous appelez une API personnalisée en appelant l'une des surcharges de la méthode [InvokeApiAsync] sur le client. Par exemple, la ligne de code suivante envoie une requête POST à l’API **completeAll** sur le backend :

    var result = await client.InvokeApiAsync<MarkAllResult>("completeAll", System.Net.Http.HttpMethod.Post, null);

Notez qu'il s'agit d'un appel de méthode typé pour lequel le type de renvoi de **MarkAllResult** doit être défini. Les méthodes typées et non typées sont toutes deux prises en charge.


##<a name="authentication"></a>Authentification des utilisateurs

Mobile Apps prend en charge l’authentification et l’autorisation des utilisateurs d’applications via divers fournisseurs d’identité externes : Facebook, Google, Microsoft Account, Twitter et Azure Active Directory. Vous pouvez définir des autorisations sur les tables pour limiter l'accès à certaines opérations aux seuls utilisateurs authentifiés. Vous pouvez également utiliser l'identité des utilisateurs authentifiés pour implémenter des règles d'autorisation dans les scripts serveur. Pour plus d'informations, consultez le didacticiel [Ajout de l'authentification à votre application].

Deux flux d'authentification sont pris en charge : un _flux serveur_ et un _flux client_. Le flux serveur fournit l'authentification la plus simple, car il repose sur l'interface d'authentification Web du fournisseur. En revanche, le flux client est celui qui s'intègre le plus profondément aux fonctionnalités propres à l'appareil, car il s'appuie sur les Kits de développement logiciel (SDK) propres au fournisseur et à l'appareil.

Dans tous les cas, vous devez inscrire votre application auprès de votre fournisseur d’identité. Le fournisseur d’identité vous communique un ID client et une clé secrète client. Vous devez ensuite configurer l’authentification / l’autorisation Azure App Service avec l’ID client et la clé secrète client fournis par votre fournisseur d’identité. Pour plus d’informations, suivez les instructions détaillées dans le didacticiel [Ajout d’une fonction d’authentification à votre application].

###<a name="serverflow"></a>Flux serveur
Une fois que vous avez inscrit votre fournisseur d’identité, appelez MobileServiceClient.[méthode LoginAsync] avec la valeur [MobileServiceAuthenticationProvider] de votre fournisseur. Par exemple, le code suivant initie une connexion de flux serveur via Facebook.

	private MobileServiceUser user;
	private async System.Threading.Tasks.Task Authenticate()
	{
		while (user == null)
		{
			string message;
			try
			{
				user = await client
					.LoginAsync(MobileServiceAuthenticationProvider.Facebook);
				message =
					string.Format("You are now logged in - {0}", user.UserId);
			}
			catch (InvalidOperationException)
			{
				message = "You must log in. Login Required";
			}

			var dialog = new MessageDialog(message);
			dialog.Commands.Add(new UICommand("OK"));
			await dialog.ShowAsync();
		}
	}

Si vous utilisez un fournisseur d'identité différent de Facebook, remplacez la valeur de [MobileServiceAuthenticationProvider] ci-dessus par la valeur de votre fournisseur.

Dans un flux serveur, Azure App Service gère le flux d’authentification OAuth 2.0 en affichant la page de connexion du fournisseur sélectionné et en générant un jeton d’authentification App Service après avoir établi une authentification avec le fournisseur d’identité. La [méthode LoginAsync] renvoie un [MobileServiceUser], qui fournit à la fois l’[UserId] de l’utilisateur authentifié et le [MobileServiceAuthenticationToken], sous la forme d’un jeton Web JSON (JWT). Ce jeton peut être mis en cache et réutilisé jusqu'à ce qu'il arrive à expiration. Pour plus d'informations, consultez la section [Mise en cache du jeton d'authentification](#caching).

###<a name="client-flow"></a>Flux client

Votre application peut également contacter le fournisseur d’identité de manière indépendante, puis fournir le jeton renvoyé à App Service à des fins d’authentification. Le flux client permet de proposer l'authentification unique aux utilisateurs ou de récupérer d'autres données utilisateur auprès du fournisseur d'identité.

####Authentification unique à l’aide d’un jeton Facebook ou Google

Dans la forme la plus simple, vous pouvez utiliser le flux client comme indiqué dans cet extrait de code pour Facebook ou Google.

	var token = new JObject();
	// Replace access_token_value with actual value of your access token obtained
	// using the Facebook or Google SDK.
	token.Add("access_token", "access_token_value");

	private MobileServiceUser user;
	private async System.Threading.Tasks.Task Authenticate()
	{
		while (user == null)
		{
			string message;
			try
			{
				// Change MobileServiceAuthenticationProvider.Facebook
				// to MobileServiceAuthenticationProvider.Google if using Google auth.
				user = await client.LoginAsync(MobileServiceAuthenticationProvider.Facebook, token);
				message = string.Format("You are now logged in - {0}", user.UserId);
			}
			catch (InvalidOperationException)
			{
				message = "You must log in. Login Required";
			}

			var dialog = new MessageDialog(message);
			dialog.Commands.Add(new UICommand("OK"));
			await dialog.ShowAsync();
		}
	}

####Authentification unique à l’aide d’un compte Microsoft avec le Kit de développement logiciel (SDK) Live

Pour pouvoir authentifier les utilisateurs, vous devez inscrire votre application auprès du Centre des développeurs de compte Microsoft. Vous devez ensuite connecter cette inscription à votre backend Mobile Apps. Effectuez les étapes décrites dans la rubrique [Inscrire votre application pour utiliser un compte Microsoft pour l’authentification] pour créer une inscription de compte Microsoft et la connecter à votre backend Mobile Apps. Si vous disposez des versions Windows Store et Windows Phone 8/Silverlight de votre application, inscrivez d'abord la version Windows Store.

Le code suivant s’authentifie à l’aide du SDK Live et utilise le jeton retourné pour se connecter à votre backend Mobile Apps.

	private LiveConnectSession session;
 	//private static string clientId = "<microsoft-account-client-id>";
    private async System.Threading.Tasks.Task AuthenticateAsync()
    {

        // Get the URL the Mobile App backend.
        var serviceUrl = App.MobileService.ApplicationUri.AbsoluteUri;

        // Create the authentication client for Windows Store using the service URL.
        LiveAuthClient liveIdClient = new LiveAuthClient(serviceUrl);
        //// Create the authentication client for Windows Phone using the client ID of the registration.
        //LiveAuthClient liveIdClient = new LiveAuthClient(clientId);

        while (session == null)
        {
            // Request the authentication token from the Live authentication service.
			// The wl.basic scope should always be requested.  Other scopes can be added
            LiveLoginResult result = await liveIdClient.LoginAsync(new string[] { "wl.basic" });
            if (result.Status == LiveConnectSessionStatus.Connected)
            {
                session = result.Session;

                // Get information about the logged-in user.
                LiveConnectClient client = new LiveConnectClient(session);
                LiveOperationResult meResult = await client.GetAsync("me");

                // Use the Microsoft account auth token to sign in to App Service.
                MobileServiceUser loginResult = await App.MobileService
                    .LoginWithMicrosoftAccountAsync(result.Session.AuthenticationToken);

                // Display a personalized sign-in greeting.
                string title = string.Format("Welcome {0}!", meResult.Result["first_name"]);
                var message = string.Format("You are now logged in - {0}", loginResult.UserId);
                var dialog = new MessageDialog(message, title);
                dialog.Commands.Add(new UICommand("OK"));
                await dialog.ShowAsync();
            }
            else
            {
                session = null;
                var dialog = new MessageDialog("You must log in.", "Login Required");
                dialog.Commands.Add(new UICommand("OK"));
                await dialog.ShowAsync();
            }
        }
    }

Consultez la documentation pour obtenir plus d’informations sur le [Kit de développement logiciel (SDK) Windows Live].

###<a name="caching"></a>Mise en cache du jeton d'authentification
Dans certains cas, l'appel à la méthode de connexion peut être évité après la première authentification de l'utilisateur. Vous pouvez utiliser [PasswordVault] pour les applications Windows Store afin de mettre en cache l'identité de l'utilisateur actif lors de sa première connexion et, par la suite, à chaque fois que vous vérifiez si son identité est présente dans le cache. Lorsque le cache est vide, vous devez toujours soumettre l'utilisateur au processus de connexion.

	// After logging in
	PasswordVault vault = new PasswordVault();
	vault.Add(new PasswordCredential("Facebook", user.UserId, user.MobileServiceAuthenticationToken));

	// Log in
	var creds = vault.FindAllByResource("Facebook").FirstOrDefault();
	if (creds != null)
	{
		user = new MobileServiceUser(creds.UserName);
		user.MobileServiceAuthenticationToken = vault.Retrieve("Facebook", creds.UserName).Password;
	}
	else
	{
		// Regular login flow
		user = new MobileServiceuser( await client
			.LoginAsync(MobileServiceAuthenticationProvider.Facebook, token);
		var token = new JObject();
		// Replace access_token_value with actual value of your access token
		token.Add("access_token", "access_token_value");
	}

	 // Log out
	client.Logout();
	vault.Remove(vault.Retrieve("Facebook", user.UserId));


Dans le cas des applications Windows Phone, vous pouvez chiffrer et mettre en cache les données à l'aide de la classe [ProtectedData] et stocker les informations sensibles dans un stockage isolé.

-->

### <a name="adal"></a>Authentification des utilisateurs avec la bibliothèque ADAL (Active Directory Authentication Library)

Vous pouvez utiliser la bibliothèque d’authentification Active Directory (ADAL) pour authentifier des utilisateurs dans votre application à l’aide d’Azure Active Directory. Cette approche est souvent préférable à l’utilisation des méthodes `loginAsync()`, car elle offre une interface UX native plus simple et permet une personnalisation supplémentaire.

1. Si vous souhaitez configurer le backend de votre application mobile pour utiliser la connexion AAD, suivez le didacticiel [Configurer votre application App Service pour utiliser la connexion Azure Active Directory]. Bien que cette étape soit facultative, veillez à inscrire une application cliente native.

2. Dans Visual Studio ou Xamarin Studio, ouvrez votre projet et ajoutez une référence au package NuGet `Microsoft.IdentityModel.CLients.ActiveDirectory`. Au cours de la recherche, incluez les versions préliminaires.

3. Ajoutez le code ci-dessous à votre application, en fonction de la plateforme utilisée. Dans chaque cas, effectuez les remplacements suivants :

* Remplacez **INSERT-AUTHORITY-HERE** par le nom du client dans lequel vous avez approvisionné votre application. Vous devez utiliser le format https://login.windows.net/contoso.onmicrosoft.com. Cette valeur peut être copiée depuis l’onglet Domaine de votre annuaire Azure Active Directory dans le [portail Azure Classic].

* Remplacez **INSERT-RESOURCE-ID-HERE** par l’ID client du backend de votre application mobile. Vous pouvez obtenir cet identifiant sur le portail, sous l’onglet **Avancé** du menu **Paramètres Azure Active Directory**.

* Remplacez **INSERT-CLIENT-ID-HERE** par l’ID client que vous avez copié depuis l’application cliente native.

* Remplacez **INSERT-REDIRECT-URI-HERE** par le point de terminaison _/.auth/login/done_ de votre site, en utilisant le modèle HTTPS. Cette valeur doit être similaire à \__https://contoso.azurewebsites.net/.auth/login/done_.

Voici le code nécessaire pour chaque plateforme :

**Windows :**

    private MobileServiceUser user;
    private async Task AuthenticateAsync()
    {
        string authority = "INSERT-AUTHORITY-HERE";
        string resourceId = "INSERT-RESOURCE-ID-HERE";
        string clientId = "INSERT-CLIENT-ID-HERE";
        string redirectUri = "INSERT-REDIRECT-URI-HERE";
        while (user == null)
        {
            string message;
            try
            {
                AuthenticationContext ac = new AuthenticationContext(authority);
                AuthenticationResult ar = await ac.AcquireTokenAsync(resourceId, clientId, new Uri(redirectUri), new PlatformParameters(PromptBehavior.Auto, false) );
                JObject payload = new JObject();
                payload["access_token"] = ar.AccessToken;
                user = await App.MobileService.LoginAsync(MobileServiceAuthenticationProvider.WindowsAzureActiveDirectory, payload);
                message = string.Format("You are now logged in - {0}", user.UserId);
            }
            catch (InvalidOperationException)
            {
                message = "You must log in. Login Required";
            }
            var dialog = new MessageDialog(message);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
        }
    }

**Xamarin.iOS**

    private MobileServiceUser user;
    private async Task AuthenticateAsync(UIViewController view)
    {
        string authority = "INSERT-AUTHORITY-HERE";
        string resourceId = "INSERT-RESOURCE-ID-HERE";
        string clientId = "INSERT-CLIENT-ID-HERE";
        string redirectUri = "INSERT-REDIRECT-URI-HERE";
        try
        {
            AuthenticationContext ac = new AuthenticationContext(authority);
            AuthenticationResult ar = await ac.AcquireTokenAsync(resourceId, clientId, new Uri(redirectUri), new PlatformParameters(view));
            JObject payload = new JObject();
            payload["access_token"] = ar.AccessToken;
            user = await client.LoginAsync(MobileServiceAuthenticationProvider.WindowsAzureActiveDirectory, payload);
        }
        catch (Exception ex)
        {
            Console.Error.WriteLine(@"ERROR - AUTHENTICATION FAILED {0}", ex.Message);
        }
    }

**Xamarin.Android**

    private MobileServiceUser user;
    private async Task AuthenticateAsync()
    {
        string authority = "INSERT-AUTHORITY-HERE";
        string resourceId = "INSERT-RESOURCE-ID-HERE";
        string clientId = "INSERT-CLIENT-ID-HERE";
        string redirectUri = "INSERT-REDIRECT-URI-HERE";
        try
        {
            AuthenticationContext ac = new AuthenticationContext(authority);
            AuthenticationResult ar = await ac.AcquireTokenAsync(resourceId, clientId, new Uri(redirectUri), new PlatformParameters(this));
            JObject payload = new JObject();
            payload["access_token"] = ar.AccessToken;
            user = await client.LoginAsync(MobileServiceAuthenticationProvider.WindowsAzureActiveDirectory, payload);
        }
        catch (Exception ex)
        {
            AlertDialog.Builder builder = new AlertDialog.Builder(this);
            builder.SetMessage(ex.Message);
            builder.SetTitle("You must log in. Login Required");
            builder.Create().Show();
        }
    }
    protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
    {
        base.OnActivityResult(requestCode, resultCode, data);
        AuthenticationAgentContinuationHelper.SetAuthenticationAgentContinuationEventArgs(requestCode, resultCode, data);
    }

##<a name="pushnotifications">Notifications Push

Les rubriques suivantes traitent des notifications Push :

* [Inscription aux notifications Push](#register-for-push)
* [Obtention d’un SID de package Windows Store](#package-sid)
* [Inscription avec des modèles inter-plateformes](#register-xplat)

###<a name="register-for-push"></a>Procédure : inscription aux notifications Push

Le client Mobile Apps permet de s’inscrire aux notifications Push avec Azure Notification Hubs. Lors de l'inscription, vous obtenez un handle à partir de spécifique à la plate-forme Push Notification Service (PNS). Vous fournissez ensuite cette valeur, ainsi que toutes les balises lorsque vous créez l'inscription. Le code suivant inscrit votre application Windows aux notifications push avec le service de notification Windows (Windows Notification Service, WNS) :

    private async void InitNotificationsAsync()
    {
        // Request a push notification channel.
        var channel =await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

        // Register for notifications using the new channel.
        await MobileService.GetPush().RegisterNativeAsync(channel.Uri, null);
    }

Si vous déployez vers WNS, alors vous DEVEZ obtenir un SID de package Windows Store (voir ci-dessous). Notez que dans cet exemple, deux balises sont incluses dans l'inscription. Pour plus d'informations sur les applications Windows, y compris l'enregistrement pour les inscriptions de modèle, consultez la page [Ajout de notifications push à votre application].

Notez que la requête de balises à partir du client n’est pas prise en charge. Les requêtes de balises sont supprimées de manière silencieuse à partir de l’inscription. Si vous souhaitez inscrire votre appareil avec des balises, créez une API personnalisée qui utilise l’API Notification Hubs pour effectuer l’inscription de votre part. [Appelez l’API personnalisée](#customapi) au lieu de la méthode `RegisterNativeAsync()`.

###<a name="package-sid"></a>Obtention d'un SID de package Windows Store

Un SID de package est nécessaire pour l’activation des notifications Push dans les applications Windows Store. Le SID de package est également utilisé pour d’autres choses (par exemple, authentification Windows unique). Vous devez enregistrer votre application auprès du Windows Store afin de recevoir un SID de package.

Pour obtenir cette valeur :

1. Dans l’Explorateur de solutions de Visual Studio, cliquez avec le bouton droit sur le projet d’application Windows Store, puis cliquez sur **Store** > **Associer l’application au Windows Store...**.
2. Dans l’Assistant, cliquez sur **Suivant**, connectez-vous à votre compte Microsoft, saisissez un nom pour votre application dans **Réserver un nouveau nom d’application**, puis cliquez sur **Réserver**.
3. Une fois l’inscription de l’application créée, sélectionnez le nouveau nom d’application, cliquez sur **Suivant**, puis sur **Associer**. Cela ajoute les informations d'inscription Windows Store requises au manifeste de l'application.
4. Connectez-vous au [Centre de développement Windows] à l’aide de votre compte Microsoft. Sous **Mes applications**, cliquez sur l’inscription de l’application que vous venez de créer.
5. Cliquez sur **Gestion des applications** > **Identité d'application**, puis faites défiler jusqu'à trouver votre **SID de package**.

De nombreuses utilisations du SID de package traitent ce dernier comme une URI, auquel cas vous devrez utiliser _ms-app://_ comme schéma. Prenez note de la version de votre package SID formé en concaténant cette valeur comme préfixe.

Les applications Xamarin nécessitent du code supplémentaire pour pouvoir enregistrer une application s'exécutant respectivement sur une application iOS ou Android avec Apple Push Notification Service (APNS) et les services Google Cloud Messaging (GCM). Pour plus d’informations, consultez la rubrique pour votre plateforme :

* [Xamarin.Android](app-service-mobile-xamarin-android-get-started-push.md#add-push)
* [Xamarin.iOS](app-service-mobile-xamarin-ios-get-started-push.md#add-push)

###<a name="register-xplat"></a>Inscription de modèles de notifications Push pour envoyer des notifications multiplateforme

Pour enregistrer les modèles, utilisez la méthode `RegisterAsync()` avec les modèles, comme suit :

        JObject templates = myTemplates();
        MobileService.GetPush().RegisterAsync(channel.Uri, templates);

Vos modèles seront de type JObject et pourront contenir plusieurs modèles au format JSON suivant :

        public JObject myTemplates()
        {
            // single template for Windows Notification Service toast
            var template = "<toast><visual><binding template="ToastText01"><text id="1">$(message)</text></binding></visual></toast>";

            var templates = new JObject
            {
                ["generic-message"] = new JObject
                {
                    ["body"] = template,
                    ["headers"] = new JObject
                    {
                        ["X-WNS-Type"] = "wns/toast"
                    },
                    ["tags"] = new JArray()
                },
                ["more-templates"] = new JObject {...}
            };
            return templates;
        }

La méthode **RegisterAsync()** accepte également les mosaïques secondaires :

        MobileService.GetPush().RegisterAsync(string channelUri, JObject templates, JObject secondaryTiles);

Notez que toutes les balises seront supprimées pour des raisons de sécurité. Pour ajouter des balises à des installations ou des modèles dans des installations, consultez [Utiliser le Kit de développement logiciel (SDK) de serveur principal .NET pour Azure Mobile Apps].

Pour envoyer des notifications à l’aide de ces modèles inscrits, consultez les [API Notification Hubs].

##<a name="misc"></a>Rubriques diverses

###<a name="errors"></a>Procédure : gestion des erreurs

Lorsqu’une erreur se produit sur le backend, le Kit de développement logiciel (SDK) client déclenche une `MobileServiceInvalidOperationException`. L’exemple suivant montre comment gérer une exception renvoyée par le backend :

	private async void InsertTodoItem(TodoItem todoItem)
	{
		// This code inserts a new TodoItem into the database. When the operation completes
		// and App Service has assigned an Id, the item is added to the CollectionView
		try
		{
			await todoTable.InsertAsync(todoItem);
			items.Add(todoItem);
		}
		catch (MobileServiceInvalidOperationException e)
		{
			// Handle error
		}
	}

Vous trouverez un autre exemple de traitement des conditions d’erreur dans l’[exemple de fichiers Mobile Apps] ; l’exemple [LoggingHandler] fournit un gestionnaire de délégué de journalisation (voir ci-dessous) pour consigner les requêtes envoyées au backend. Ceci permet de déboguer plus facilement les applications Xamarin plutôt que de compter sur Fiddler.

###<a name="headers"></a>Procédure de personnalisation des en-têtes de requête

Pour prendre en charge votre scénario d’application en particulier, vous devrez peut-être personnaliser la communication avec le backend Mobile Apps. Par exemple, il est possible que vous vouliez ajouter un en-tête personnalisé à chaque demande sortante ou même modifier le code d'état des réponses. Pour cela, fournissez un [DelegatingHandler] personnalisé, comme dans l’exemple suivant :

    public async Task CallClientWithHandler()
    {
        HttpResponseMessage[]
        MobileServiceClient client = new MobileServiceClient("AppUrl",
            new MyHandler()
            );
        IMobileServiceTable<TodoItem> todoTable = client.GetTable<TodoItem>();
        var newItem = new TodoItem { Text = "Hello world", Complete = false };
        await todoTable.InsertAsync(newItem);
    }

    public class MyHandler : DelegatingHandler
    {
        protected override async Task<HttpResponseMessage>
            SendAsync(HttpRequestMessage request, CancellationToken cancellationToken)
        {
            // Change the request-side here based on the HttpRequestMessage
            request.Headers.Add("x-my-header", "my value");

            // Do the request
            var response = await base.SendAsync(request, cancellationToken);

            // Change the response-side here based on the HttpResponseMessage

            // Return the modified response
            return response;
        }
    }


<!-- Anchors. -->
[Filtrer les données renvoyées]: #filtering
[Trier les données renvoyées]: #sorting
[Renvoyer les données de pages]: #paging
[Sélectionner des colonnes spécifiques]: #selecting
[Rechercher des données par ID]: #lookingup

<!-- Images. -->

<!-- Internal URLs. -->
[Démarrage rapide avec Azure Mobile Apps]: app-service-mobile-windows-store-dotnet-get-started.md
[démarrage rapide d’Azure Mobile Apps]: app-service-mobile-windows-store-dotnet-get-started.md
[Ajout de l'authentification à votre application]: app-service-mobile-windows-store-dotnet-get-started-users.md
[Ajout d’une fonction d’authentification à votre application]: app-service-mobile-windows-store-dotnet-get-started-users.md
[Work with .NET backend SDK]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Utiliser le Kit de développement logiciel (SDK) de serveur principal .NET pour Azure Mobile Apps]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[How to use the Node.js backend SDK]: app-service-mobile-node-backend-how-to-use-server-sdk.md
[How to: Define a table controller]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#how-to-define-a-table-controller
[Define Tables using a Dynamic Schema]: app-service-mobile-node-backend-how-to-use-server-sdk.md#TableOperations
[Synchronisation des données hors connexion dans Azure Mobile Apps]: app-service-mobile-offline-data-sync.md
[Ajout de notifications push à votre application]: app-service-mobile-windows-store-dotnet-get-started-push.md
[Inscrire votre application pour utiliser un compte Microsoft pour l’authentification]: app-service-mobile-how-to-configure-microsoft-authentication.md
[Configurer votre application App Service pour utiliser la connexion Azure Active Directory]: app-service-mobile-how-to-configure-active-directory-authentication.md

<!-- Microsoft URLs. -->
[Référence du client .NET Azure Mobile Apps]: https://msdn.microsoft.com/fr-FR/library/azure/mt419521(v=azure.10).aspx
[MobileServiceClient]: https://msdn.microsoft.com/fr-FR/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient(v=azure.10).aspx
[MobileServiceCollection]: https://msdn.microsoft.com/fr-FR/library/azure/dn250636(v=azure.10).aspx
[MobileServiceIncrementalLoadingCollection]: https://msdn.microsoft.com/fr-FR/library/azure/dn268408(v=azure.10).aspx
[MobileServiceAuthenticationProvider]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceauthenticationprovider(v=azure.10).aspx
[MobileServiceUser]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser(v=azure.10).aspx
[MobileServiceAuthenticationToken]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.mobileserviceauthenticationtoken(v=azure.10).aspx
[GetTable]: https://msdn.microsoft.com/fr-FR/library/azure/jj554275(v=azure.10).aspx
[crée une référence à une table non typée]: https://msdn.microsoft.com/fr-FR/library/azure/jj554278(v=azure.10).aspx
[DeleteAsync]: https://msdn.microsoft.com/fr-FR/library/azure/dn296407(v=azure.10).aspx
[IncludeTotalCount]: https://msdn.microsoft.com/fr-FR/library/azure/dn250560(v=azure.10).aspx
[InsertAsync]: https://msdn.microsoft.com/fr-FR/library/azure/dn296400(v=azure.10).aspx
[InvokeApiAsync]: https://msdn.microsoft.com/fr-FR/library/azure/dn268343(v=azure.10).aspx
[LoginAsync]: https://msdn.microsoft.com/fr-FR/library/azure/dn296411(v=azure.10).aspx
[LookupAsync]: https://msdn.microsoft.com/fr-FR/library/azure/jj871654(v=azure.10).aspx
[OrderBy]: https://msdn.microsoft.com/fr-FR/library/azure/dn250572(v=azure.10).aspx
[OrderByDescending]: https://msdn.microsoft.com/fr-FR/library/azure/dn250568(v=azure.10).aspx
[ReadAsync]: https://msdn.microsoft.com/fr-FR/library/azure/mt691741(v=azure.10).aspx
[Take]: https://msdn.microsoft.com/fr-FR/library/azure/dn250574(v=azure.10).aspx
[Select]: https://msdn.microsoft.com/fr-FR/library/azure/dn250569(v=azure.10).aspx
[Skip]: https://msdn.microsoft.com/fr-FR/library/azure/dn250573(v=azure.10).aspx
[UpdateAsync]: https://msdn.microsoft.com/fr-FR/library/azure/dn250536.(v=azure.10)aspx
[UserID]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.userid(v=azure.10).aspx
[Where]: https://msdn.microsoft.com/fr-FR/library/azure/dn250579(v=azure.10).aspx
[portail Azure]: https://portal.azure.com/
[portail Azure Classic]: https://manage.windowsazure.com/
[EnableQueryAttribute]: https://msdn.microsoft.com/library/system.web.http.odata.enablequeryattribute.aspx
[Guid.NewGuid]: https://msdn.microsoft.com/fr-FR/library/system.guid.newguid(v=vs.110).aspx
[ISupportIncrementalLoading]: http://msdn.microsoft.com/library/windows/apps/Hh701916.aspx
[Centre de développement Windows]: https://dev.windows.com/fr-FR/overview
[DelegatingHandler]: https://msdn.microsoft.com/library/system.net.http.delegatinghandler(v=vs.110).aspx
[Kit de développement logiciel (SDK) Windows Live]: https://msdn.microsoft.com/fr-FR/library/bb404787.aspx
[PasswordVault]: http://msdn.microsoft.com/library/windows/apps/windows.security.credentials.passwordvault.aspx
[ProtectedData]: http://msdn.microsoft.com/library/system.security.cryptography.protecteddata%28VS.95%29.aspx
[API Notification Hubs]: https://msdn.microsoft.com/library/azure/dn495101.aspx
[exemple de fichiers Mobile Apps]: https://github.com/Azure-Samples/app-service-mobile-dotnet-todo-list-files
[LoggingHandler]: https://github.com/Azure-Samples/app-service-mobile-dotnet-todo-list-files/blob/master/src/client/MobileAppsFilesSample/Helpers/LoggingHandler.cs#L63
[référentiel GitHub d’exemples Azure]: https://github.com/Azure-Samples

<!-- External URLs -->
[JsonPropertyAttribute]: http://www.newtonsoft.com/json/help/html/Properties_T_Newtonsoft_Json_JsonPropertyAttribute.htm
[documentation OData v3]: http://www.odata.org/documentation/odata-version-3-0/
[Fiddler]: http://www.telerik.com/fiddler
[Json.NET]: http://www.newtonsoft.com/json
[SymbolSource]: http://www.symbolsource.org/
[instructions SymbolSource]: http://www.symbolsource.org/Public/Wiki/Using

<!---HONumber=AcomDC_0420_2016-->