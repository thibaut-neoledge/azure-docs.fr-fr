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
	ms.date="03/02/2016"
	ms.author="glenga"/>

# Utilisation du client géré pour Azure Mobile Apps

[AZURE.INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

##Vue d’ensemble

Ce guide vous montre comment mettre en place des scénarios courants à l’aide de la bibliothèque cliente gérée pour Azure App Service Mobile Apps pour les applications Windows et Xamarin. Si vous débutez avec Mobile Apps, suivez le didacticiel [Démarrage rapide avec Mobile Apps](app-service-mobile-windows-store-dotnet-get-started.md). Dans ce guide, nous nous concentrons sur le Kit de développement logiciel (SDK) géré côté client. Pour plus d’informations sur les kits de développement côté serveur pour Mobile Apps, consultez les articles [Utiliser le Kit de développement logiciel (SDK) de serveur principal .NET pour Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) ou [Comment utiliser le Kit de développement logiciel Node.js dans Azure Mobile Apps](app-service-mobile-node-backend-how-to-use-server-sdk.md).

## Documentation de référence

La documentation de référence du Kit de développement logiciel (SDK) client se trouve ici : [Référence du client .NET Azure Mobile Apps](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.aspx).

##<a name="setup"></a>Configuration et conditions préalables

Nous supposons que vous avez déjà créé et publié votre projet de backend Mobile Apps et qu'il comprend au moins une table. Dans le code utilisé dans cette rubrique, la table s'intitule `TodoItem` et contient les colonnes suivantes : `Id`, `Text` et `Complete`. Il s’agit de la table créée lors du [didacticiel de démarrage rapide](app-service-mobile-windows-store-dotnet-get-started.md).

Le type côté client typé en C# correspondant est le suivant :


	public class TodoItem
	{
		public string Id { get; set; }

		[JsonProperty(PropertyName = "text")]
		public string Text { get; set; }

		[JsonProperty(PropertyName = "complete")]
		public bool Complete { get; set; }
	}

L’attribut [JsonPropertyAttribute](http://www.newtonsoft.com/json/help/html/Properties_T_Newtonsoft_Json_JsonPropertyAttribute.htm) est utilisé pour définir le mappage *PropertyName* entre le type client et la table.

Pour savoir comment créer des tables dans votre backend Mobile Apps, consultez [Définir un contrôleur de table](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#how-to-define-a-table-controller) (backend .NET) ou [Définir des tables à l’aide d’un schéma dynamique](app-service-mobile-node-backend-how-to-use-server-sdk.md#TableOperations) (backend Node.js). Pour un backend Node.js, vous pouvez également utiliser les **Easy Tables** dans le [portail Azure](https://portal.azure.com/).

##<a name="create-client"></a>Création du client Mobile Apps

Le code suivant permet de créer l’objet `MobileServiceClient` utilisé pour accéder à votre backend Mobile Apps.

	MobileServiceClient client = new MobileServiceClient("MOBILE_APP_URL");

Dans le code ci-dessus, remplacez `MOBILE_APP_URL` par l’URL du backend Mobile Apps, qui se trouve dans le panneau de votre backend Mobile Apps du [portail Azure](https://portal.azure.com/).

##<a name="instantiating"></a>Procédure : création d'une référence de table

L’ensemble du code permettant d’accéder aux données d’une table du backend ou de les modifier appelle des fonctions sur l’objet `MobileServiceTable`. Pour obtenir une référence à la table, appelez la méthode [GetTable](https://msdn.microsoft.com/library/azure/jj554275.aspx) sur une instance du `MobileServiceClient`, comme suit :

    IMobileServiceTable<TodoItem> todoTable =
		client.GetTable<TodoItem>();

Il s'agit du modèle de sérialisation typé. Les modèles de sérialisation non typés sont également pris en charge. Le code suivant crée une référence à une table non typée :

	// Get an untyped table reference
	IMobileServiceTable untypedTodoTable = client.GetTable("TodoItem");

Dans les requêtes non typées, vous devez spécifier la chaîne de requête OData sous-jacente.

##<a name="querying"></a>Interrogation des données à partir de votre application mobile

Cette section explique comment émettre des requêtes à destination du backend Mobile Apps, qui inclut les fonctionnalités suivantes :

- [Filtrer les données renvoyées]
- [Trier les données renvoyées]
- [Renvoyer les données de pages]
- [Sélectionner des colonnes spécifiques]
- [Rechercher des données par ID]

>[AZURE.NOTE] Une taille de page gérée par le serveur est imposée pour empêcher le renvoi de toutes les lignes. Cela permet d'éviter que les requêtes par défaut associées à des jeux de données volumineux aient un impact négatif sur le service. Pour obtenir le renvoi de plus de 50 lignes, utilisez la méthode `Take`, comme décrit dans la section [Renvoyer les données de pages].

### <a name="filtering"></a>Procédure : filtrage des données renvoyées

Le code suivant montre comment filtrer des données en incluant une clause `Where` dans une requête. Il renvoie tous les éléments de `todoTable` dont la propriété `Complete` est égale à `false`. La fonction `Where` applique un prédicat de filtrage de ligne à la requête au niveau de la table.

	// This query filters out completed TodoItems and
	// items without a timestamp.
	List<TodoItem> items = await todoTable
	   .Where(todoItem => todoItem.Complete == false)
	   .ToListAsync();

Vous pouvez afficher l’URI de la requête envoyée au backend en utilisant un logiciel d’inspection des messages, par exemple les outils destinés aux développeurs de navigateurs ou [Fiddler]. Si vous examinez l'URI de requête ci-dessous, vous remarquerez que la chaîne de requête elle-même est modifiée :

	GET /tables/todoitem?$filter=(complete+eq+false) HTTP/1.1

Cette requête serait normalement convertie en requête SQL côté Azure similaire à celle-ci :

	SELECT *
	FROM TodoItem
	WHERE ISNULL(complete, 0) = 0

La fonction transmise à la méthode `Where` peut avoir un nombre de conditions arbitraire. Par exemple, la ligne ci-dessous :

	// This query filters out completed TodoItems where Text isn't null
	List<TodoItem> items = await todoTable
	   .Where(todoItem => todoItem.Complete == false
		   && todoItem.Text != null)
	   .ToListAsync();

Serait approximativement convertie (pour la même requête indiquée plus haut) comme suit

	SELECT *
	FROM TodoItem
	WHERE ISNULL(complete, 0) = 0
	      AND ISNULL(text, 0) = 0

L'instruction `where` ci-dessus recherche les éléments dont l'état `Complete` a la valeur false et la colonne `Text` la valeur non null.

Cela aurait pu également être écrit sur plusieurs lignes :

	List<TodoItem> items = await todoTable
	   .Where(todoItem => todoItem.Complete == false)
	   .Where(todoItem => todoItem.Text != null)
	   .ToListAsync();

Les deux méthodes sont équivalentes et peuvent être utilisées de manière interchangeable. La dernière option, qui consiste à concaténer plusieurs prédicats dans une même requête, est plus compacte. C’est celle que nous recommandons.

La clause `where` prend en charge les opérations traduites dans le sous-ensemble OData. Cela inclut les opérateurs relationnels (==, !=, <, <=, >, >=), les opérateurs arithmétiques (+, -, /, *, %), la précision des nombres (Math.Floor, Math.Ceiling), les fonctions de chaîne (Length, Substring, Replace, IndexOf, StartsWith, EndsWith), les propriétés de date (Year, Month, Day, Hour, Minute, Second), les propriétés d'accès d'un objet, ainsi que les expressions qui combinent tous ces éléments.

### <a name="sorting"></a>Procédure : tri des données renvoyées

Le code suivant montre comment trier les données en incluant une fonction `OrderBy` ou `OrderByDescending` dans la requête. Il renvoie des éléments de `todoTable`, triés par ordre croissant dans le champ `Text`.

	// Sort items in ascending order by Text field
	MobileServiceTableQuery<TodoItem> query = todoTable
					.OrderBy(todoItem => todoItem.Text)
 	List<TodoItem> items = await query.ToListAsync();

	// Sort items in descending order by Text field
	MobileServiceTableQuery<TodoItem> query = todoTable
					.OrderByDescending(todoItem => todoItem.Text)
 	List<TodoItem> items = await query.ToListAsync();

### <a name="paging"></a>Procédure : renvoi de données dans les pages

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

>[AZURE.NOTE]Pour remplacer la limite de 50 lignes dans un backend Mobile Apps, vous devez également appliquer l’attribut [EnableQueryAttribute](https://msdn.microsoft.com/library/system.web.http.odata.enablequeryattribute.aspx) à la méthode GET publique et spécifier le comportement de pagination. Lorsqu'il est appliqué à la méthode, l'exemple suivant définit le nombre maximal de lignes renvoyées à 1 000 :

    [EnableQuery(MaxTop=1000)]


### <a name="selecting"></a>Procédure : sélection de colonnes spécifiques

Vous pouvez indiquer le jeu de propriétés à inclure dans les résultats en ajoutant une clause `Select` à la requête. Par exemple, le code suivant montre comment sélectionner un seul champ et comment sélectionner et mettre en forme plusieurs champs :

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

La fonction `LookupAsync` permet de rechercher des objets dans la base de données à partir d'un ID particulier.

	// This query filters out the item with the ID of 37BBF396-11F0-4B39-85C8-B319C729AF6D
	TodoItem item = await todoTable.LookupAsync("37BBF396-11F0-4B39-85C8-B319C729AF6D");

### <a name="lookingup"></a>Exécution de requêtes non typées

Lorsque vous exécutez une requête avec un objet de table non typé, vous devez spécifier explicitement la chaîne de requête OData, comme dans l'exemple suivant :

	// Lookup untyped data using OData
	JToken untypedItems = await untypedTodoTable.ReadAsync("$filter=complete eq 0&$orderby=text");

Vous obtenez en retour des valeurs JSON que vous pouvez utiliser comme conteneur de propriétés. Pour plus d'informations sur JToken et Json.NET, consultez la page [Json.NET](http://json.codeplex.com/)

##<a name="inserting"></a>Insertion de données dans un backend Mobile Apps

Tous les types clients doivent contenir un membre nommé **Id**, par défaut une chaîne. Cet **Id** est requis pour effectuer des opérations CRUD et hors connexion. Le code suivant montre comment insérer de nouvelles lignes dans une table. Le paramètre contient les données à insérer sous forme d'objet .NET.

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

+ Les ID sont générés sans effectuer d’aller-retour vers la base de données.
+ Il est plus facile de fusionner des enregistrements de plusieurs tables ou bases de données.
+ Les valeurs d’ID peuvent mieux s’intégrer à la logique d’une application.

Lorsque la valeur d’ID d’une chaîne n’est pas définie sur un enregistrement inséré, le backend Mobile Apps génère une valeur unique pour l’ID. Vous pouvez utiliser la méthode `Guid.NewGuid()` pour générer vos propres valeurs d’ID, sur le client ou dans le backend.

##<a name="modifying"></a>Modification de données dans un backend Mobile Apps

Le code suivant montre comment mettre à jour une instance existante avec le même ID avec des informations nouvelles. Le paramètre contient les données à mettre à jour sous forme d'objet .NET.

	await todoTable.UpdateAsync(todoItem);

Pour insérer des données non typées, vous pouvez utiliser Json.NET ainsi : JObject jo = new JObject(); jo.Add(“Id”, “37BBF396-11F0-4B39-85C8-B319C729AF6D”); jo.Add(“Text”, “Hello World”); jo.Add(“Complete”, false); var inserted = await table.UpdateAsync(jo);

Vous devez spécifier un ID lorsque vous effectuez une mise à jour. C'est grâce à cela que le backend identifie l'instance à mettre à jour. L'ID peut être obtenu à partir du résultat de l'appel `InsertAsync`. Si vous essayez de mettre à jour un élément sans fournir de valeur « Id », une `ArgumentException` se déclenche.


##<a name="deleting"></a>Suppression de données dans un backend Mobile Apps

Le code suivant montre comment supprimer une instance existante. L'instance est identifiée par le champ « Id » défini au niveau de `todoItem`.

	await todoTable.DeleteAsync(todoItem);

Pour supprimer des données non typées, vous pouvez utiliser Json.NET ainsi :

	JObject jo = new JObject();
	jo.Add("Id", "37BBF396-11F0-4B39-85C8-B319C729AF6D");
	await table.DeleteAsync(jo);

Vous devez spécifier un ID lorsque vous effectuez une requête de suppression. Les autres propriétés ne sont pas transmises au service ou sont ignorées au niveau du service. Le résultat d’un appel `DeleteAsync` a généralement la valeur `null`. L'ID à transmettre peut être obtenu à partir du résultat de l'appel `InsertAsync`. Si vous essayez de supprimer un élément sans avoir au préalable défini le champ « Id », une `MobileServiceInvalidOperationException` est renvoyée par le backend.

##<a name="#custom-api"></a>Procédure : appel d'une API personnalisée

Une API personnalisée vous permet de définir des points de terminaison exposant une fonctionnalité de serveur qui ne mappe pas vers une opération d'insertion, de mise à jour, de suppression ou de lecture. En utilisant une API personnalisée, vous pouvez exercer davantage de contrôle sur la messagerie, notamment lire et définir des en-têtes de message HTTP et définir un format de corps de message autre que JSON.

Vous appelez une API personnalisée en appelant l'une des surcharges de la méthode [InvokeApiAsync] sur le client. Par exemple, la ligne de code suivante envoie une requête POST à l’API **completeAll** sur le backend :

    var result = await App.MobileService
        .InvokeApiAsync<MarkAllResult>("completeAll",
        System.Net.Http.HttpMethod.Post, null);

Notez qu'il s'agit d'un appel de méthode typé pour lequel le type de renvoi de **MarkAllResult** doit être défini. Les méthodes typées et non typées sont toutes deux prises en charge. Cet exemple est presque trivial car il est typé, n'envoie pas de charges utiles, n'a aucun paramètre de requête et ne modifie pas les en-têtes de requête. Pour des exemples plus réalistes et une discussion plus élaborée sur [InvokeApiAsync], consultez la section [API personnalisée dans les kits de développement logiciel (SDK) clients pour Azure Mobile Services].

##Procédure : inscription aux notifications push

Le client Mobile Apps permet de s’inscrire aux notifications Push avec Azure Notification Hubs. Lors de l'inscription, vous obtenez un handle à partir de spécifique à la plate-forme Push Notification Service (PNS). Vous fournissez ensuite cette valeur, ainsi que toutes les balises lorsque vous créez l'inscription. Le code suivant inscrit votre application Windows aux notifications push avec le service de notification Windows (Windows Notification Service, WNS) :

		private async void InitNotificationsAsync()
		{
		    // Request a push notification channel.
		    var channel =
		        await PushNotificationChannelManager
		            .CreatePushNotificationChannelForApplicationAsync();

		    // Register for notifications using the new channel and a tag collection.
			var tags = new List<string>{ "mytag1", "mytag2"};
		    await MobileService.GetPush().RegisterNativeAsync(channel.Uri, tags);
		}

Notez que dans cet exemple, deux balises sont incluses dans l'inscription. Pour plus d'informations sur les applications Windows, y compris l'enregistrement pour les inscriptions de modèle, consultez la page [Ajout de notifications push à votre application](app-service-mobile-windows-store-dotnet-get-started-push.md).

Les applications Xamarin nécessitent du code supplémentaire pour pouvoir enregistrer une application s'exécutant respectivement sur une application iOS ou Android avec Apple Push Notification Service (APNS) et les services Google Cloud Messaging (GCM). Pour plus d’informations, consultez **Ajout de notifications Push à votre application** ([Xamarin.iOS](partner-xamarin-mobile-services-ios-get-started-push.md#add-push) | [Xamarin.Android](partner-xamarin-mobile-services-android-get-started-push.md#add-push)).

## Inscription de modèles de notifications push pour envoyer des notifications multiplateforme

Pour inscrire des modèles, transmettez-les simplement avec votre méthode **MobileService.GetPush().RegisterAsync()** dans votre application cliente.

        MobileService.GetPush().RegisterAsync(channel.Uri, newTemplates());

Vos modèles seront de type JObject et pourront contenir plusieurs modèles au format JSON suivant :

        public JObject newTemplates()
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

Pour envoyer des notifications à l’aide de ces modèles inscrits, utilisez les [API Notification Hubs](https://msdn.microsoft.com/library/azure/dn495101.aspx).

##<a name="optimisticconcurrency"></a>Procédure : utilisation de l'accès concurrentiel optimiste

Dans certains scénarios, plusieurs clients peuvent écrire à un même moment des modifications dans un même élément. En l'absence de détection de conflits, la dernière écriture remplace les mises à jour précédentes, même si cela n'était pas le but recherché. Le *contrôle d'accès concurrentiel optimiste* considère que chaque transaction peut être validée et, qu’à ce titre, elle ne fait appel à aucun verrouillage de ressources. Avant de valider une transaction, le contrôle d'accès concurrentiel optimiste vérifie qu'aucune autre transaction n'a modifié les données. Si les données ont été modifiées, la transaction de validation est annulée.

Mobile Apps prend en charge le contrôle d'accès concurrentiel optimiste en suivant les modifications apportées à chaque élément à l'aide de la colonne de la propriété système `__version` définie pour chaque table de votre serveur principal Mobile Apps. Chaque fois qu’un enregistrement est mis à jour, Mobile Apps attribue une nouvelle valeur à la propriété `__version` de cet enregistrement. À chaque demande de mise à jour, la propriété `__version` de l'enregistrement inclus dans la demande est comparée à celle de l'enregistrement basé sur le serveur. Si la version transmise avec la demande ne correspond pas à celle du serveur principal, la bibliothèque cliente déclenche une `MobileServicePreconditionFailedException<T>`. Le type inclus avec l’exception est l’enregistrement du backend contenant la version serveur de l’enregistrement. À partir de cette information, l’application peut décider ou non d’exécuter à nouveau la requête de mise à jour avec la valeur `__version` correcte du serveur principal pour valider les modifications.

Pour activer l'accès concurrentiel optimiste, l'application définit une colonne sur la classe table de la propriété système `__version`. La définition suivante en fournit un exemple.

    public class TodoItem
    {
        public string Id { get; set; }

        [JsonProperty(PropertyName = "text")]
        public string Text { get; set; }

        [JsonProperty(PropertyName = "complete")]
        public bool Complete { get; set; }

		// *** Enable Optimistic Concurrency *** //
        [JsonProperty(PropertyName = "__version")]
        public byte[] Version { set; get; }
    }


Dans le cas des applications utilisant des tables non typées, l'accès concurrentiel optimiste est activé en définissant l'indicateur `Version` au niveau de la propriété `SystemProperties` de la table, comme suit.

	//Enable optimistic concurrency by retrieving __version
	todoTable.SystemProperties |= MobileServiceSystemProperties.Version;


Le code suivant montre comment résoudre un conflit d'écriture une fois qu'il est détecté. La valeur `__version` correcte doit être incluse dans l'appel de `UpdateAsync()` pour valider une résolution.

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
	    MessageDialog msgDialog = new MessageDialog(String.Format("Server Text: "{0}" \nLocal Text: "{1}"\n",
        	                                        serverItem.Text, localItem.Text),
                                                	"CONFLICT DETECTED - Select a resolution:");

	    UICommand localBtn = new UICommand("Commit Local Text");
    	UICommand ServerBtn = new UICommand("Leave Server Text");
    	msgDialog.Commands.Add(localBtn);
	    msgDialog.Commands.Add(ServerBtn);

    	localBtn.Invoked = async (IUICommand command) =>
	    {
        	// To resolve the conflict, update the version of the
	        // item being committed. Otherwise, you will keep
        	// catching a MobileServicePreConditionFailedException.
	        localItem.Version = serverItem.Version;

    	    // Updating recursively here just in case another
        	// change happened while the user was making a decision
	        UpdateToDoItem(localItem);
    	};

	    ServerBtn.Invoked = async (IUICommand command) =>
    	{
	        RefreshTodoItems();
    	};

	    await msgDialog.ShowAsync();
	}

Pour en savoir plus, consultez [Synchronisation des données hors connexion dans Azure Mobile Apps](app-service-mobile-offline-data-sync.md).


##<a name="binding"></a>Procédure : liaison de données Mobile Apps à une interface utilisateur Windows

Cette section montre comment afficher des objets de données renvoyés à l'aide d'éléments d'interface utilisateur dans une application Windows. Pour lancer une requête sur les éléments incomplets de `todoTable` et les afficher dans une liste très simple, vous pouvez exécuter l'exemple de code suivant pour lier la source de la liste à une requête. L’utilisation de `MobileServiceCollection` crée une collection de liaisons prenant en charge Mobile Apps.

	// This query filters out completed TodoItems.
	MobileServiceCollection<TodoItem, TodoItem> items = await todoTable
		.Where(todoItem => todoItem.Complete == false)
		.ToCollectionAsync();

	// itemsControl is an IEnumerable that could be bound to a UI list control
	IEnumerable itemsControl  = items;

	// Bind this to a ListBox
	ListBox lb = new ListBox();
	lb.ItemsSource = items;

Certains contrôles dans l’exécution gérée prennent en charge une interface appelée [ISupportIncrementalLoading](http://msdn.microsoft.com/library/windows/apps/Hh701916). Cette interface permet aux contrôles de demander des données supplémentaires lorsque l'utilisateur fait défiler l'écran. Il existe une prise en charge intégrée de cette interface pour les applications Windows 8.1 au moyen de `MobileServiceIncrementalLoadingCollection`. Elle traite automatiquement les appels en provenance des contrôles. Pour utiliser `MobileServiceIncrementalLoadingCollection` dans des applications Windows, procédez comme suit :

			MobileServiceIncrementalLoadingCollection<TodoItem,TodoItem> items;
		items =  todoTable.Where(todoItem => todoItem.Complete == false)
					.ToIncrementalLoadingCollection();

		ListBox lb = new ListBox();
		lb.ItemsSource = items;


Pour utiliser la nouvelle collection sur les applications Windows Phone 8 et « Silverlight », utilisez les méthodes d’extension `ToCollection` au niveau de `IMobileServiceTableQuery<T>` et `IMobileServiceTable<T>`. Pour charger réellement les données, appelez `LoadMoreItemsAsync()`.

	MobileServiceCollection<TodoItem, TodoItem> items = todoTable.Where(todoItem => todoItem.Complete==false).ToCollection();
	await items.LoadMoreItemsAsync();

Lorsque vous utilisez la collection créée par l'appel de `ToCollectionAsync` ou `ToCollection`, vous obtenez une collection qui peut être liée aux contrôles d'interface utilisateur. Cette collection prend en charge la pagination, ce qui signifie qu'un contrôle peut demander à la collection de « charger plus d'éléments », ce qu'elle fera pour le contrôle. À ce stade, aucun code utilisateur n'intervient ; c'est le contrôle qui démarre le flux. Toutefois, sachant que la collection charge les données à partir du réseau, ce chargement peut parfois échouer. Pour gérer ces échecs, vous pouvez ignorer la méthode `OnException` au niveau de `MobileServiceIncrementalLoadingCollection` pour traiter les exceptions résultant des appels de `LoadMoreItemsAsync` émis par les contrôles.

Enfin, imaginez que votre table contient de nombreux champs, mais que vous ne souhaitez en afficher qu'une partie dans votre contrôle. Vous pouvez suivre les instructions fournies plus haut dans la section « [Sélectionner des colonnes spécifiques](#selecting) » pour sélectionner les colonnes à afficher dans l’interface utilisateur.

## <a name="adal"></a>Procédure : authentifier des utilisateurs avec la bibliothèque Active Directory Authentication Library

Vous pouvez utiliser la bibliothèque d’authentification Active Directory (ADAL) pour authentifier des utilisateurs dans votre application à l’aide d’Azure Active Directory. Cette approche est souvent préférable à l’utilisation des méthodes `loginAsync()`, car elle offre une interface UX native plus simple et permet une personnalisation supplémentaire.

1. Si vous souhaitez configurer le serveur principal de votre application mobile pour utiliser la connexion AAD, suivez le didacticiel [Configurer votre application App Service pour utiliser la connexion Azure Active Directory](app-service-mobile-how-to-configure-active-directory-authentication.md). Bien que cette étape soit facultative, veillez à inscrire une application cliente native.

2. Dans Visual Studio ou Xamarin Studio, ouvrez votre projet et ajoutez une référence au package NuGet `Microsoft.IdentityModel.CLients.ActiveDirectory`. Au cours de la recherche, incluez les versions préliminaires.

3. Ajoutez le code ci-dessous à votre application, en fonction de la plateforme utilisée. Dans chaque cas, effectuez les remplacements suivants :

* Remplacez **INSERT-AUTHORITY-HERE** par le nom du client dans lequel vous avez déployé votre application. Vous devez utiliser le format https://login.windows.net/contoso.onmicrosoft.com. Cette valeur peut être copiée depuis l’onglet Domaine de votre Azure Active Directory dans le [portail Azure Classic].

* Remplacez **INSERT-RESOURCE-ID-HERE** par l’ID client du serveur principal de votre application mobile. Vous pouvez obtenir cet identifiant sur le portail, sous l’onglet **Avancé** du menu **Paramètres Azure Active Directory**.

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

## <a name="package-sid"></a>Obtention d'un SID de package Windows Store

Pour les applications Windows, un SID de package est nécessaire pour l'activation des notifications Push. Pour obtenir cette valeur :

1. Dans l’Explorateur de solutions de Visual Studio, cliquez avec le bouton droit sur le projet d’application Windows Store, puis cliquez sur **Store** > **Associer l’application au Windows Store...**.
2. Dans l’Assistant, cliquez sur **Suivant**, connectez-vous à votre compte Microsoft, saisissez un nom pour votre application dans **Réserver un nouveau nom d’application**, puis cliquez sur **Réserver**.
3. Une fois l’inscription de l’application créée, sélectionnez le nouveau nom d’application, cliquez sur **Suivant**, puis sur **Associer**. Cela ajoute les informations d'inscription Windows Store requises au manifeste de l'application.
4. Connectez-vous au [Centre de développement Windows](https://dev.windows.com/fr-FR/overview) à l’aide de votre compte Microsoft. Sous **Mes applications**, cliquez sur l’inscription de l’application que vous venez de créer.
5. Cliquez sur **Gestion des applications** > **Identité d'application**, puis faites défiler jusqu'à trouver votre **SID de package**.

De nombreuses utilisations du SID de package traitent ce dernier comme une URI, auquel cas vous devrez utiliser _ms-app://_ comme schéma. Prenez note de la version de votre package SID formé en concaténant cette valeur comme préfixe.

<!--- We want to just point to the authentication topic when it's done
##<a name="authentication"></a>How to: Authenticate users

Mobile Apps supports authenticating and authorizing app users using a variety of external identity providers: Facebook, Google, Microsoft Account, Twitter, and Azure Active Directory. You can set permissions on tables to restrict access for specific operations to only authenticated users. You can also use the identity of authenticated users to implement authorization rules in server scripts. For more information, see the tutorial [Add authentication to your app].

Two authentication flows are supported: a _server flow_ and a _client flow_. The server flow provides the simplest authentication experience, as it relies on the provider's web authentication interface. The client flow allows for deeper integration with device-specific capabilities as it relies on provider-specific device-specific SDKs.

###Server flow
To have App Service manage the authentication process in your Windows apps,
you must register your app with your identity provider. Then in your mobile App backed, you need to configure the application ID and secret provided by your provider. For more information, see the tutorial [Add authentication to your app].

Once you have registered your identity provider, simply call the [LoginAsync method] with the [MobileServiceAuthenticationProvider] value of your provider. For example, the following code initiates a server flow sign-in by using Facebook.

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

If you are using an identity provider other than Facebook, change the value of [MobileServiceAuthenticationProvider] above to the value for your provider.

In this case, App Service manages the OAuth 2.0 authentication flow by displaying the sign-in page of the selected provider and generating an App Service authentication token after successful sign-on with the identity provider. The [LoginAsync method] returns a [MobileServiceUser], which provides both the [userId] of the authenticated user and the [MobileServiceAuthenticationToken], as a JSON web token (JWT). This token can be cached and re-used until it expires. For more information, see [Caching the authentication token].

###Client flow

Your app can also independently contact the identity provider and then provide the returned token to App Service for authentication. This client flow enables you to provide a single sign-in experience for users or to retrieve additional user data from the identity provider.

####Single sign-in using a token from Facebook or Google

In the most simplified form, you can use the client flow as shown in this snippet for Facebook or Google.

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
				user = await client
					.LoginAsync(MobileServiceAuthenticationProvider.Facebook, token);
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


####Single sign-in using Microsoft Account with the Live SDK

To be able to authenticate users, you must register your app at the Microsoft account Developer Center. You must then connect this registration with your Mobile App backend. Complete the steps in [Register your app to use a Microsoft account login](app-service-mobile-how-to-configure-microsoft-authentication.md) to create a Microsoft account registration and connect it to your Mobile App backend. If you have both Windows Store and Windows Phone 8/Silverlight versions of your app, register the Windows Store version first.

The following code authenticates using Live SDK and uses the returned token to sign-in to your Mobile App backend.

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
			// The wl.basic scope is requested.
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


###<a name="caching"></a>Caching the authentication token
In some cases, the call to the login method can be avoided after the first time the user authenticates. You can use [PasswordVault] for Windows Store apps to cache the current user identity the first time they log in and every subsequent time you check whether you already have the user identity in our cache. When the cache is empty, you still need to send the user through the login process.

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


For Windows Phone apps, you may encrypt and cache data using the [ProtectedData] class and store sensitive information in isolated storage.

-->

##<a name="errors"></a>Procédure : gestion des erreurs

L'exemple suivant montre comment gérer une exception renvoyée par le backend :

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


##<a name="unit-testing"></a>Procédure : conception de tests unitaires

La valeur renvoyée par `MobileServiceClient.GetTable` et les requêtes sont des interfaces. Il est donc facile de les simuler à des fins de test. Vous pouvez ainsi créer une `MyMockTable : IMobileServiceTable<TodoItem>` qui implémente votre logique de test.

##<a name="customizing"></a>Procédure : personnalisation du client

Cette section indique de quelle façon vous pouvez personnaliser les en-têtes de demande, ainsi que la sérialisation d'objets JSON de la réponse.

### <a name="headers"></a>Procédure : personnalisation des en-têtes de demande

Pour prendre en charge votre scénario d’application en particulier, vous devrez peut-être personnaliser la communication avec le backend Mobile Apps. Par exemple, il est possible que vous vouliez ajouter un en-tête personnalisé à chaque demande sortante ou même modifier le code d'état des réponses. Pour cela, fournissez un [DelegatingHandler] personnalisé, comme dans l’exemple suivant :

    public async Task CallClientWithHandler()
    {
        MobileServiceClient client = new MobileServiceClient(
            "AppUrl", "", "",
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
            // Add a custom header to the request.
            request.Headers.Add("x-my-header", "my value");
            var response = await base.SendAsync(request, cancellationToken);
            // Set a differnt response status code.
            response.StatusCode = HttpStatusCode.ServiceUnavailable;
            return response;
        }
    }

Ce code ajoute un nouvel en-tête **x-my-header** à la demande et définit arbitrairement la réponse de code comme indisponible. Dans un scénario réel, vous devez définir le code d'état de la réponse selon une logique personnalisée requise par votre application.

### <a name="serialization"></a>Procédure : personnalisation de la sérialisation

La bibliothèque cliente Mobile Apps utilise Json.NET pour convertir une réponse JSON en objets .NET sur le client. Vous pouvez configurer le comportement de cette sérialisation entre les types .NET et JSON dans les messages. La classe [MobileServiceClient] expose une propriété `SerializerSettings` de type [JsonSerializerSettings](http://james.newtonking.com/projects/json/help/?topic=html/T_Newtonsoft_Json_JsonSerializerSettings.htm).

À l'aide de cette propriété, vous pouvez définir l'une des nombreuses propriétés Json.NET, tels que les éléments suivants :

	var settings = new JsonSerializerSettings();
	settings.ContractResolver = new CamelCasePropertyNamesContractResolver();
	client.SerializerSettings = settings;

Cette propriété convertit toutes les propriétés en minuscules lors de la sérialisation.

<!-- Anchors. -->
[Filtrer les données renvoyées]: #filtering
[Trier les données renvoyées]: #sorting
[Renvoyer les données de pages]: #paging
[Sélectionner des colonnes spécifiques]: #selecting
[Rechercher des données par ID]: #lookingup

<!-- Images. -->



<!-- URLs. -->
[Add authentication to your app]: app-service-mobile-windows-store-dotnet-get-started-users.md
[Utiliser le Kit de développement logiciel (SDK) de serveur principal .NET pour Azure Mobile Apps]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[PasswordVault]: http://msdn.microsoft.com/library/windows/apps/windows.security.credentials.passwordvault.aspx
[ProtectedData]: http://msdn.microsoft.com/library/system.security.cryptography.protecteddata%28VS.95%29.aspx
[LoginAsync method]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceclientextensions.loginasync.aspx
[MobileServiceAuthenticationProvider]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceauthenticationprovider.aspx
[MobileServiceUser]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.aspx
[UserID]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.userid.aspx
[MobileServiceAuthenticationToken]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.mobileserviceauthenticationtoken.aspx
[ASCII control codes C0 and C1]: http://en.wikipedia.org/wiki/Data_link_escape_character#C1_set
[MobileServiceClient]: http://msdn.microsoft.com/library/microsoft.windowsazure.mobileservices.mobileserviceclient.aspx
[IncludeTotalCount]: http://msdn.microsoft.com/library/windowsazure/dn250560.aspx
[Skip]: http://msdn.microsoft.com/library/windowsazure/dn250573.aspx
[Take]: http://msdn.microsoft.com/library/windowsazure/dn250574.aspx
[Fiddler]: http://www.telerik.com/fiddler
[API personnalisée dans les kits de développement logiciel (SDK) clients pour Azure Mobile Services]: http://blogs.msdn.com/b/carlosfigueira/archive/2013/06/19/custom-api-in-azure-mobile-services-client-sdks.aspx
[InvokeApiAsync]: http://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.invokeapiasync.aspx
[DelegatingHandler]: https://msdn.microsoft.com/library/system.net.http.delegatinghandler(v=vs.110).aspx

<!---HONumber=AcomDC_0302_2016-->