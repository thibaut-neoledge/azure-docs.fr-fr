<properties
	pageTitle="Utilisation de la bibliothèque cliente .NET pour Mobile Services"
	description="Découvrez comment utiliser un client .NET pour Azure Mobile Services."
	services="mobile-services"
	documentationCenter="windows"
	authors="ggailey777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-windows"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="08/18/2015" 
	ms.author="glenga"/>

# Utilisation d'un client .NET pour Azure Mobile Services

[AZURE.INCLUDE [mobile-services-selector-client-library](../../includes/mobile-services-selector-client-library.md)]

##Vue d'ensemble

Ce guide vous montre comment exécuter les scénarios courants à l'aide d'un client .NET pour Azure Mobile Services, dans les applications Windows Store et Windows Phone. Les scénarios traités incluent l'interrogation des données, l'insertion, la mise à jour et la suppression des données, l'authentification des utilisateurs et la gestion des erreurs. Si vous débutez avec Mobile Services, suivez l'un des deux didacticiels [Démarrage rapide Mobile Services]() ou [Ajouter Mobile Services à une application existante]().

[AZURE.INCLUDE [mobile-services-concepts](../../includes/mobile-services-concepts.md)]

##<a name="setup"></a>Configuration et conditions préalables

Nous partons du principe que vous avez créé un service mobile et une table. Pour plus d'informations, consultez la page [Créer une table](http://go.microsoft.com/fwlink/?LinkId=298592). Dans le code utilisé dans cette rubrique, la table s'intitule `TodoItem` et contient les colonnes suivantes : `Id`, `Text` et `Complete`.

Le type .NET côté client typé correspondant est le suivant :


	public class TodoItem
	{
		public string Id { get; set; }

		[JsonProperty(PropertyName = "text")]
		public string Text { get; set; }

		[JsonProperty(PropertyName = "complete")]
		public bool Complete { get; set; }
	}

Lorsqu'un schéma dynamique est activé, Azure Mobile Services génère automatiquement de nouvelles colonnes basées sur l'objet des requêtes d'insertion ou de mise à jour. Pour plus d'informations, consultez la page [Schéma dynamique](http://go.microsoft.com/fwlink/?LinkId=296271).

##<a name="create-client"></a>Procédure : création du client Mobile Services

Le code suivant permet de créer l'objet `MobileServiceClient` utilisé pour accéder à votre service mobile.


	MobileServiceClient client = new MobileServiceClient(
		"AppUrl",
		"AppKey"
	);

Dans le code ci-dessus, remplacez `AppUrl` et `AppKey` par l'URL et la clé d'application du service mobile, dans cet ordre. Tous deux sont disponibles sur le portail de gestion Azure. Pour y accéder, sélectionnez votre service mobile, puis cliquez sur « Tableau de bord ».

>[AZURE.IMPORTANT]La clé d'application est destinée à filtrer la demande aléatoire sur votre service mobile ; elle est distribuée avec l'application. Cette clé n'étant pas chiffrée, elle ne peut pas être considérée comme sécurisée. Pour vraiment sécuriser l'accès aux données de votre service mobile, vous devez authentifier les utilisateurs avant de leur autoriser l'accès. Pour plus d'informations, consultez [Procédure : authentification des utilisateurs](#authentication).

##<a name="instantiating"></a>Procédure : création d'une référence de table

L'ensemble du code qui permet d'accéder aux données de la table Mobile Services ou de les modifier appelle des fonctions sur l'objet `MobileServiceTable`. Pour obtenir une référence à la table, appelez la fonction [GetTable](http://msdn.microsoft.com/library/windowsazure/jj554275.aspx) sur une instance du `MobileServiceClient`.

    IMobileServiceTable<TodoItem> todoTable =
		client.GetTable<TodoItem>();

Il s'agit du modèle de sérialisation typé ; consultez la section traitant du <a href="#untyped">modèle de sérialisation non typé</a> ci-après.

##<a name="querying"></a>Procédure : interrogation des données à partir d'un service mobile

Cette section explique comment émettre des requêtes à destination du service mobile, qui inclut les fonctionnalités suivantes :

- [Filtrer les données renvoyées]
- [Trier les données renvoyées]
- [Renvoyer les données de pages]
- [Sélectionner des colonnes spécifiques]
- [Rechercher des données par ID]

>[AZURE.NOTE]Une taille de page gérée par le serveur est imposée pour empêcher le renvoi de toutes les lignes. Cela permet d'éviter que les requêtes par défaut associées à des jeux de données volumineux aient un impact négatif sur le service. Pour obtenir le renvoi de plus de 50 lignes, utilisez la méthode `Take`, comme décrit dans la section [Renvoyer les données de pages].

### <a name="filtering"></a>Procédure : filtrage des données renvoyées

Le code suivant montre comment filtrer des données en incluant une clause `Where` dans une requête. Il renvoie tous les éléments de `todoTable` dont la propriété `Complete` est égale à `false`. La fonction `Where` applique un prédicat de filtrage de ligne à la requête au niveau de la table.

	// This query filters out completed TodoItems and
	// items without a timestamp.
	List<TodoItem> items = await todoTable
	   .Where(todoItem => todoItem.Complete == false)
	   .ToListAsync();

Vous pouvez afficher l'URI de la requête envoyée au service mobile en utilisant un logiciel d'inspection des messages, tel que les outils destinés aux développeurs de navigateurs ou [Fiddler]. Si vous examinez l'URI de requête ci-dessous, vous remarquerez que la chaîne de requête elle-même est modifiée :

	GET /tables/todoitem?$filter=(complete+eq+false) HTTP/1.1
Cette requête serait normalement convertie approximativement sous forme de requête SQL côté serveur, comme suit :

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

Les deux méthodes sont équivalentes et peuvent être utilisées de manière interchangeable. La dernière option, qui consiste à concaténer plusieurs prédicats dans une même requête, est plus compacte. C'est celle que nous recommandons.

La clause `where` prend en charge les opérations traduites dans le sous-ensemble OData Mobile Services. Cela inclut les opérateurs relationnels (==, !=, <, <=, >, >=), les opérateurs arithmétiques (+, -, /, *, %), la précision des nombres (Math.Floor, Math.Ceiling), les fonctions de chaîne (Length, Substring, Replace, IndexOf, StartsWith, EndsWith), les propriétés de date (Year, Month, Day, Hour, Minute, Second), les propriétés d'accès d'un objet, ainsi que les expressions qui combinent tous ces éléments.

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

### <a name="paging"></a>Procédure : renvoi des données de pages

Par défaut, le serveur renvoie uniquement les 50 premières lignes. Vous pouvez augmenter le nombre de lignes renvoyées en appelant la méthode [Take]. Associez `Take` à la méthode [Skip] pour demander une « page » spécifique du jeu de données total renvoyé par la requête. Lorsqu'elle est exécutée, la requête suivante renvoie les trois premiers éléments de la table.

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

####Considérations relatives à la pagination d'un service mobile de backend .NET

Pour remplacer la limite de 50 lignes d'un service mobile de backend .NET, vous devez également appliquer l'attribut [EnableQueryAttribute](https://msdn.microsoft.com/library/system.web.http.odata.enablequeryattribute.aspx) à la méthode GET publique et spécifier le comportement de pagination. Lorsqu'il est appliqué à la méthode, l'exemple suivant définit le nombre maximal de lignes renvoyées à 1 000 :

    [EnableQuery(MaxTop=1000)]


### <a name="selecting"></a>Procédure : sélection de colonnes spécifiques

Vous pouvez indiquer le jeu de propriétés à inclure dans les résultats en ajoutant une clause `Select` à la requête. Par exemple, le code suivant montre comment sélectionner un seul champ et comment sélectionner et mettre en forme plusieurs champs :

	// Select one field -- just the Text
	MobileServiceTableQuery<TodoItem> query = todoTable
					.Select(todoItem => todoItem.Text);
	List<string> items = await query.ToListAsync();

	// Select multiple fields -- both Complete and Text info
	MobileServiceTableQuery<TodoItem> query = todoTable
					.Select(todoItem => string.Format("{0} -- {1}", todoItem.Text.PadRight(30), todoItem.Complete ? "Now complete!" : "Incomplete!"));
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

##<a name="inserting"></a>Procédure : insertion de données dans un service mobile

> [AZURE.NOTE]Si vous souhaitez effectuer des opérations d'insertion, de recherche, de suppression ou de mise à jour sur un type, vous devez créer un membre appelé **Id**. C'est pourquoi l'exemple de classe **TodoItem** contient un membre nommé **Id**. Les opérations de mise à jour et de suppression doivent toujours comporter une valeur d'ID valide.

Le code suivant montre comment insérer de nouvelles lignes dans une table. Le paramètre contient les données à insérer sous forme d'objet .NET.

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

Mobile Services prend en charge les valeurs de chaîne personnalisée uniques pour la colonne **id** de la table. Cela permet aux applications d’utiliser des valeurs personnalisées telles que les adresses de messagerie ou des noms d’utilisateur pour l’ID.

Les ID de chaîne fournissent les avantages suivants :

+ Les ID sont générés sans effectuer d’aller-retour vers la base de données.
+ Il est plus facile de fusionner des enregistrements de plusieurs tables ou bases de données.
+ Les valeurs d’ID peuvent mieux s’intégrer à la logique d’une application.

Lorsqu'une valeur d'ID de chaîne n'est pas définie sur un enregistrement inséré, Mobile Services génère une valeur unique pour l'ID. Vous pouvez utiliser la méthode `Guid.NewGuid()` pour générer vos propres valeurs d'ID, sur le client ou dans un service mobile de backend .NET. Pour en savoir plus sur la génération de GUID dans un service mobile de backend JavaScript, consultez [Procédure : génération de valeurs d'ID uniques](mobile-services-how-to-use-server-scripts.md#generate-guids).

Vous pouvez également utiliser des ID d’entier pour vos tables. Pour pouvoir utiliser un ID d'entier, vous devez créer votre table avec la commande `mobile table create` et l'option `--integerId`. Cette commande s'utilise avec l'interface de ligne de commande (CLI) pour Azure. Pour plus d'informations sur l'utilisation de l'interface de ligne de commande, consultez la page [Interface de ligne de commande pour la gestion des tables Mobile Services](../virtual-machines-command-line-tools.md#Mobile_Tables).

##<a name="modifying"></a>Procédure : modification de données dans un service mobile

Le code suivant montre comment mettre à jour une instance existante avec le même ID avec des informations nouvelles. Le paramètre contient les données à mettre à jour sous forme d'objet .NET.

	await todoTable.UpdateAsync(todoItem);


Pour insérer des données non typées, vous pouvez tirer parti de Json.NET de cette façon. Notez que pour effectuer une mise à jour, il est nécessaire de spécifier un ID, car c'est ainsi que le service mobile identifie l'instance à mettre à jour. L'ID peut être obtenu à partir du résultat de l'appel `InsertAsync`.

	JObject jo = new JObject();
	jo.Add("Id", "37BBF396-11F0-4B39-85C8-B319C729AF6D");
	jo.Add("Text", "Hello World");
	jo.Add("Complete", false);
	var inserted = await table.UpdateAsync(jo);

Si vous tentez de mettre à jour un élément sans fournir de valeur « Id », le service n'a aucun moyen de déterminer l'instance à mettre à jour. Dans ce cas, le Kit de développement logiciel (SDK) Mobile Services lève une exception `ArgumentException`.


##<a name="deleting"></a>Procédure : suppression de données dans un service mobile

Le code suivant montre comment supprimer une instance existante. L'instance est identifiée par le champ « Id » défini au niveau de `todoItem`.

	await todoTable.DeleteAsync(todoItem);

Pour supprimer des données non typées, vous pouvez tirer parti de Json.NET de cette façon. Notez que pour effectuer une demande de suppression, il est nécessaire de spécifier un ID, car c'est ainsi que le service mobile identifie l'instance à supprimer. Une demande de suppression n'a besoin que de l'ID ; les autres propriétés ne sont pas transmises au service, et si c'est le cas, celui-ci n'en tient pas compte. De même, le résultat d'un appel `DeleteAsync` a généralement la valeur `null`. L'ID à transmettre peut être obtenu à partir du résultat de l'appel `InsertAsync`.

	JObject jo = new JObject();
	jo.Add("Id", "37BBF396-11F0-4B39-85C8-B319C729AF6D");
	await table.DeleteAsync(jo);

Si vous essayez de supprimer un élément sans que le champ « Id » ne soit déjà défini, le service n'a aucun moyen de déterminer quelle instance supprimer. Dans ce cas, une exception `MobileServiceInvalidOperationException` vous est renvoyée par le service. De la même manière, si vous essayez de supprimer un élément non typé sans que le champ « Id » ne soit déjà défini, le service renvoie à nouveau une exception `MobileServiceInvalidOperationException`.

##<a name="#custom-api"></a>Procédure : appel d'une API personnalisée

Une API personnalisée vous permet de définir des points de terminaison exposant une fonctionnalité de serveur qui ne mappe pas vers une opération d'insertion, de mise à jour, de suppression ou de lecture. En utilisant une API personnalisée, vous pouvez exercer davantage de contrôle sur la messagerie, notamment lire et définir des en-têtes de message HTTP et définir un autre format de corps de message que JSON. Pour voir un exemple complet, incluant la création d'une API personnalisée dans votre service mobile, consultez la section [Appel d'une API personnalisée à partir du client].

Vous appelez une API personnalisée en appelant l'une des surcharges de la méthode [InvokeApiAsync] sur le client. Par exemple, la ligne de code suivante envoie une requête POST à l'API **completeAll** sur le service mobile :

    var result = await App.MobileService
        .InvokeApiAsync<MarkAllResult>("completeAll",
        System.Net.Http.HttpMethod.Post, null);

Notez qu'il s'agit d'un appel de méthode typé pour lequel le type de renvoi de **MarkAllResult** doit être défini. Les méthodes typées et non typées sont toutes deux prises en charge. Cet exemple est presque trivial car il est typé, n'envoie pas de charges utiles, n'a aucun paramètre de requête et ne modifie pas les en-têtes de requête. Pour des exemples plus réalistes et une discussion plus élaborée sur [InvokeApiAsync], consultez la section [API personnalisée dans les kits de développement logiciel (SDK) clients pour Azure Mobile Services].

##Procédure : inscription aux notifications push

Le client Mobile Services permet de s'inscrire aux notifications push avec Azure Notification Hubs. Lors de l'inscription, vous obtenez un URI de canal à partir de la plateforme Windows. Vous fournissez ensuite cette valeur, ainsi que toutes les balises lorsque vous créez l'inscription. Le code suivant inscrit aux notifications push d'une application Windows Store qui utilise le service de notification Windows :

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

Notez que dans cet exemple, deux balises sont incluses dans l'inscription. Pour plus d'informations, consultez [Ajout de notifications push à votre application](mobile-services-dotnet-backend-windows-universal-dotnet-get-started-push.md).

>[AZURE.NOTE]Lorsque vous devez envoyer des notifications à des utilisateurs inscrits spécifiques, il est important d'exiger une authentification avant l'inscription, puis de vérifier que l'utilisateur est autorisé à s'inscrire avec une balise spécifique. Par exemple, vous devez vous assurer qu'un utilisateur ne s'inscrit pas avec une balise qui est l'ID utilisateur d'une autre personne. Pour plus d'informations, consultez [Envoyer des notifications push aux utilisateurs authentifiés](mobile-services-dotnet-backend-windows-store-dotnet-push-notifications-app-users.md).


##<a name="optimisticconcurrency"></a>Procédure : utilisation de l'accès concurrentiel optimiste

Dans certains scénarios, plusieurs clients peuvent écrire à un même moment des modifications dans un même élément. En l'absence de détection de conflits, la dernière écriture remplace les mises à jour précédentes, même si cela n'était pas le but recherché. Le contrôle d'accès concurrentiel optimiste considère que chaque transaction peut être validée et qu'à ce titre, elle ne fait appel à aucun verrouillage de ressources. Avant de valider une transaction, le contrôle d'accès concurrentiel optimiste vérifie qu'aucune autre transaction n'a modifié les données. Si les données ont été modifiées, la transaction de validation est annulée.

Mobile Services prend en charge le contrôle d'accès concurrentiel optimiste en suivant les modifications apportées à chaque élément à l'aide de la colonne de la propriété système `__version` définie pour chaque table créée par Mobile Services. Chaque fois qu'un enregistrement est mis à jour, Mobile Services attribue une nouvelle valeur à la propriété `__version` de cet enregistrement. À chaque demande de mise à jour, la propriété `__version` de l'enregistrement inclus dans la demande est comparée à celle de l'enregistrement basé sur le serveur. Si la version transmise avec la demande ne correspond pas à celle du serveur, la bibliothèque cliente .NET de Mobile Services lève une exception `MobileServicePreconditionFailedException<T>`. Le type inclus avec l'exception est l'enregistrement du serveur contenant la version. À partir de cette information, l'application peut décider ou non d'exécuter à nouveau la demande de mise à jour avec la valeur `__version` correcte du serveur pour valider les modifications.

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


Pour obtenir un exemple plus complet d'utilisation de l'accès concurrentiel optimiste pour Mobile Services, consultez le [didacticiel Accès concurrentiel optimiste].


##<a name="binding"></a>Procédure : liaison de données à l'interface utilisateur d'un service mobile

Cette section montre comment afficher des objets de données renvoyés à l'aide d'éléments d'interface utilisateur. Pour lancer une requête sur les éléments incomplets de `todoTable` et les afficher dans une liste très simple, vous pouvez exécuter l'exemple de code suivant pour lier la source de la liste à une requête. L'utilisation de `MobileServiceCollection` permet de créer une collection de liaisons prenant en charge les services mobiles.

	// This query filters out completed TodoItems.
	MobileServiceCollection<TodoItem, TodoItem> items = await todoTable
		.Where(todoItem => todoItem.Complete == false)
		.ToCollectionAsync();

	// itemsControl is an IEnumerable that could be bound to a UI list control
	IEnumerable itemsControl  = items;

	// Bind this to a ListBox
	ListBox lb = new ListBox();
	lb.ItemsSource = items;

Certains contrôles de Windows Runtime prennent en charge une interface appelée [ISupportIncrementalLoading](http://msdn.microsoft.com/library/windows/apps/Hh701916). Cette interface permet aux contrôles de demander des données supplémentaires lorsque l'utilisateur fait défiler l'écran. Il existe une prise en charge intégrée de cette interface pour les applications du Windows Store via `MobileServiceIncrementalLoadingCollection`, qui traite automatiquement les appels en provenance des contrôles. Pour utiliser `MobileServiceIncrementalLoadingCollection` dans les applications du Windows Store, procédez comme suit :

			MobileServiceIncrementalLoadingCollection<TodoItem,TodoItem> items;
		items =  todoTable.Where(todoItem => todoItem.Complete == false)
					.ToIncrementalLoadingCollection();

		ListBox lb = new ListBox();
		lb.ItemsSource = items;


Pour utiliser la nouvelle collection sur Windows Phone, utilisez les méthodes d'extension `ToCollection` au niveau de `IMobileServiceTableQuery<T>` et `IMobileServiceTable<T>`. Pour charger réellement les données, appelez `LoadMoreItemsAsync()`.

	MobileServiceCollection<TodoItem, TodoItem> items = todoTable.Where(todoItem => todoItem.Complete==false).ToCollection();
	await items.LoadMoreItemsAsync();

Lorsque vous utilisez la collection créée par l'appel de `ToCollectionAsync` ou `ToCollection`, vous obtenez une collection qui peut être liée aux contrôles d'interface utilisateur. Cette collection prend en charge la pagination, ce qui signifie qu'un contrôle peut demander à la collection de « charger plus d'éléments », ce qu'elle fera pour le contrôle. À ce stade, aucun code utilisateur n'intervient ; c'est le contrôle qui démarre le flux. Toutefois, sachant que la collection charge les données à partir du réseau, ce chargement peut parfois échouer. Pour gérer ces échecs, vous pouvez ignorer la méthode `OnException` au niveau de `MobileServiceIncrementalLoadingCollection` pour traiter les exceptions résultant des appels de `LoadMoreItemsAsync` émis par les contrôles.

Enfin, imaginez que votre table contient de nombreux champs, mais que vous ne souhaitez en afficher qu'une partie dans votre contrôle. Vous pouvez suivre les instructions fournies plus haut dans la section <a href="#selecting">« Sélection de colonnes spécifiques »</a> pour sélectionner les colonnes à afficher dans l'interface utilisateur.

##<a name="authentication"></a>Procédure : authentification des utilisateurs

Mobile Services prend en charge l'authentification et l'autorisation des utilisateurs d'applications via divers fournisseurs d'identité externes : Facebook, Google, Microsoft Account, Twitter et Azure Active Directory. Vous pouvez définir des autorisations sur les tables pour limiter l'accès à certaines opérations aux seuls utilisateurs authentifiés. Vous pouvez également utiliser l'identité des utilisateurs authentifiés pour implémenter des règles d'autorisation dans les scripts serveur. Pour plus d'informations, consultez le didacticiel [Ajout de l'authentification à votre application].

Deux flux d'authentification sont pris en charge : un _flux serveur_ et un _flux client_. Le flux serveur fournit l'authentification la plus simple, car il repose sur l'interface d'authentification Web du fournisseur. En revanche, le flux client est celui qui s'intègre le plus profondément aux fonctionnalités propres à l'appareil, car il s'appuie sur les Kits de développement logiciel (SDK) propres au fournisseur et à l'appareil.

###Flux serveur
Pour que Mobile Services puisse gérer le processus d'authentification dans votre application Windows Store ou Windows Phone, vous devez inscrire votre application auprès de votre fournisseur d'identité. Ensuite, dans votre service mobile, vous devez configurer l'ID d'application et le secret fournis par votre fournisseur. Pour plus d'informations, consultez le didacticiel [Ajout de l'authentification à votre application].

Une fois que vous avez inscrit votre fournisseur d'identité, il vous suffit d'appeler la [méthode LoginAsync] avec la valeur [MobileServiceAuthenticationProvider] de votre fournisseur. Par exemple, le code suivant initie une connexion de flux serveur via Facebook.

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

Dans ce cas, Mobile Services gère le flux d'authentification OAuth 2.0 en affichant la page de connexion du fournisseur sélectionné et en générant un jeton d'authentification Mobile Services après avoir établi une connexion avec le fournisseur d'identité. La [méthode LoginAsync] renvoie un [MobileServiceUser], qui fournit à la fois l'[userId] de l'utilisateur authentifié et le [MobileServiceAuthenticationToken], sous la forme d'un jeton Web JSON (JWT). Ce jeton peut être mis en cache et réutilisé jusqu'à ce qu'il arrive à expiration. Pour plus d'informations, consultez la section [Mise en cache du jeton d'authentification].

> [AZURE.NOTE]**Application du Windows Store** Lorsque vous utilisez le fournisseur de connexion du compte Microsoft pour authentifier les utilisateurs de votre application du Windows Store, vous devez également inscrire le package de l'application auprès de Mobile Services. Lorsque vous inscrivez les informations du package de votre application Windows Store auprès de Mobile Services, le client peut réutiliser les informations d'identification du compte Microsoft pour fournir une authentification unique. Si vous ne le faites pas, vos utilisateurs se connectant via le compte Microsoft seront invités à se connecter à chaque appel de la méthode de connexion. Pour savoir comment inscrire votre package d'application Windows Store, consultez la rubrique [Inscription du package de votre application Windows Store pour l'authentification Microsoft](/develop/mobile/how-to-guides/register-windows-store-app-package/%20target="_blank"). Une fois les informations du package inscrites auprès de Mobile Services, appelez la méthode [LoginAsync](http://go.microsoft.com/fwlink/p/?LinkId=311594%20target="_blank") en fournissant la valeur **true** au paramètre _useSingleSignOn_ pour réutiliser les informations d'identification.

###Flux client

Votre application peut également contacter le fournisseur d'identité de manière indépendante, puis fournir le jeton renvoyé à Mobile Services à des fins d'authentification. Le flux client permet de proposer l'authentification unique aux utilisateurs ou de récupérer d'autres données utilisateur auprès du fournisseur d'identité.

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

Si vous utilisez un compte Microsoft, connectez-vous de la façon suivante :

	// Replace authentication_token_value with actual value of your Microsoft authentication token obtained through the Live SDK
	user = await client
		.LoginWithMicrosoftAccountAsync(authentication_token_value);

Pour obtenir un exemple d'utilisation de compte Microsoft pour fournir une expérience d'authentification unique, consultez le didacticiel « Authentification de votre application avec l'authentification unique » ([Windows Store](/develop/mobile/tutorials/single-sign-on-windows-8-dotnet/)/[Windows Phone](/develop/mobile/tutorials/single-sign-on-wp8/)).

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

##<a name="errors"></a>Procédure : gestion des erreurs

Il existe plusieurs méthodes pour détecter, valider et résoudre les erreurs dans Mobile Services.

Par exemple, il est possible d'utiliser les scripts serveur inscrits dans un service mobile pour effectuer diverses opérations sur les données insérées et mises à jour, qu'il s'agisse de les valider ou de les modifier. Vous pouvez définir et inscrire un script serveur qui valide et modifie des données comme suit :

	function insert(item, user, request)
	{
	   if (item.text.length > 10) {
		  request.respond(statusCodes.BAD_REQUEST, { error: "Text cannot exceed 10 characters" });
	   } else {
		  request.execute();
	   }
	}

Ce script côté serveur valide la longueur des données de chaîne envoyées au service mobile et refuse les chaînes trop longues, en l'occurrence, celles qui font plus de 10 caractères.

Maintenant que le service mobile valide les données et envoie des réponses en cas d'erreur côté serveur, vous pouvez mettre à jour votre application .NET pour lui permettre de traiter les réponses indiquant des erreurs de validation.

	private async void InsertTodoItem(TodoItem todoItem)
	{
		// This code inserts a new TodoItem into the database. When the operation completes
		// and Mobile Services has assigned an Id, the item is added to the CollectionView
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

##<a name="untyped"></a>Procédure : utilisation de données non typées

Le client .NET a été conçu pour des scénarios fortement typés. Toutefois, une expérience plus légèrement typée peut parfois s'avérer pratique ; tel peut-être le cas lorsque, par exemple, il s'agit de traiter des objets avec un schéma ouvert. Ce scénario est mis en œuvre comme suit. Dans les requêtes, vous renoncez à LINQ pour utiliser le format wire.

	// Get an untyped table reference
	IMobileServiceTable untypedTodoTable = client.GetTable("TodoItem");

	// Lookup untyped data using OData
	JToken untypedItems = await untypedTodoTable.ReadAsync("$filter=complete eq 0&$orderby=text");

Vous obtenez en retour des valeurs JSON que vous pouvez utiliser comme conteneur de propriétés. Pour plus d'informations sur JToken et Json.NET, consultez la page [Json.NET](http://json.codeplex.com/)

##<a name="unit-testing"></a>Procédure : conception de tests unitaires

La valeur renvoyée par `MobileServiceClient.GetTable` et les requêtes sont des interfaces. Il est donc facile de les simuler à des fins de test. Vous pouvez ainsi créer une `MyMockTable : IMobileServiceTable<TodoItem>` qui implémente votre logique de test.

##<a name="customizing"></a>Procédure : personnalisation du client

Cette section indique de quelle façon vous pouvez personnaliser les en-têtes de demande, ainsi que la sérialisation d'objets JSON de la réponse.

### <a name="headers"></a>Procédure : personnalisation des en-têtes de demande

Pour prendre en charge votre scénario d'application spécifique, vous devrez peut-être personnaliser la communication avec le service mobile. Par exemple, il est possible que vous vouliez ajouter un en-tête personnalisé à chaque demande sortante ou même modifier le code d'état des réponses. Pour cela, fournissez un DelegatingHandler personnalisé, comme dans l'exemple suivant :

	public async Task CallClientWithHandler()
	{
		MobileServiceClient client = new MobileServiceClient(
			"AppUrl",
			"AppKey" ,
			new MyHandler()
			);
		IMobileServiceTable<TodoItem> todoTable = client.GetTable<TodoItem>();
		var newItem = new TodoItem { Text = "Hello world", Complete = false };
		await table.InsertAsync(newItem);
	}

	public class MyHandler : DelegatingHandler
	{
		protected override async Task<HttpResponseMessage> SendAsync(HttpRequestMessage request, CancellationToken cancellationToken)
		{
			request.Headers.Add("x-my-header", "my value");
			var response = awaitbase.SendAsync(request, cancellationToken);
			response.StatusCode = HttpStatusCode.ServiceUnavailable;
			return response;
		}
	}

Ce code ajoute un nouvel en-tête **x-my-header** à la demande et définit arbitrairement la réponse de code comme indisponible. Dans un scénario réel, vous devez définir le code d'état de la réponse selon une logique personnalisée requise par votre application.

### <a name="serialization"></a>Procédure : personnalisation de la sérialisation

La bibliothèque cliente Mobile Services utilise Json.NET pour convertir une réponse JSON en objets .NET sur le client. Vous pouvez configurer le comportement de cette sérialisation entre les types .NET et JSON dans les messages. La classe [MobileServiceClient](http://msdn.microsoft.com/library/microsoft.windowsazure.mobileservices.mobileserviceclient.aspx) expose une propriété `SerializerSettings` de type [JsonSerializerSettings](http://james.newtonking.com/projects/json/help/?topic=html/T_Newtonsoft_Json_JsonSerializerSettings.htm).

À l'aide de cette propriété, vous pouvez définir l'une des nombreuses propriétés Json.NET, tels que les éléments suivants :

	var settings = new JsonSerializerSettings();
	settings.ContractResolver = new CamelCasePropertyNamesContractResolver();
	client.SerializerSettings = settings;

Cette propriété convertit toutes les propriétés en minuscules lors de la sérialisation.

<!-- Anchors. -->
[What is Mobile Services]: #what-is
[Concepts]: #concepts
[How to: Create the Mobile Services client]: #create-client
[How to: Create a table reference]: #instantiating
[How to: Query data from a mobile service]: #querying
[Filtrer les données renvoyées]: #filtering
[Trier les données renvoyées]: #sorting
[Renvoyer les données de pages]: #paging
[Sélectionner des colonnes spécifiques]: #selecting
[Rechercher des données par ID]: #lookingup
[How to: Bind data to user interface in a mobile service]: #binding
[How to: Insert data into a mobile service]: #inserting
[How to: Modify data in a mobile service]: #modifying
[How to: Delete data in a mobile service]: #deleting
[How to: Use Optimistic Concurrency]: #optimisticconcurrency
[How to: Authenticate users]: #authentication
[How to: Handle errors]: #errors
[How to: Design unit tests]: #unit-testing
[How to: Query data from a mobile service]: #querying
[How to: Customize the client]: #customizing
[How to: Work with untyped data]: #untyped
[Customize request headers]: #headers
[Customize serialization]: #serialization
[Next steps]: #nextsteps
[Mise en cache du jeton d'authentification]: #caching
[How to: Call a custom API]: #custom-api

<!-- Images. -->



<!-- URLs. -->
[Ajout de l'authentification à votre application]: mobile-services-dotnet-backend-windows-universal-dotnet-get-started-users.md
[PasswordVault]: http://msdn.microsoft.com/library/windows/apps/windows.security.credentials.passwordvault.aspx
[ProtectedData]: http://msdn.microsoft.com/library/system.security.cryptography.protecteddata%28VS.95%29.aspx
[méthode LoginAsync]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceclientextensions.loginasync.aspx
[MobileServiceAuthenticationProvider]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceauthenticationprovider.aspx
[MobileServiceUser]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.aspx
[UserID]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.userid.aspx
[MobileServiceAuthenticationToken]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.mobileserviceauthenticationtoken.aspx
[ASCII control codes C0 and C1]: http://en.wikipedia.org/wiki/Data_link_escape_character#C1_set
[CLI to manage Mobile Services tables]: ../virtual-machines-command-line-tools.md/#Commands_to_manage_mobile_services
[didacticiel Accès concurrentiel optimiste]: mobile-services-windows-store-dotnet-handle-database-conflicts.md

[IncludeTotalCount]: http://msdn.microsoft.com/library/windowsazure/dn250560.aspx
[Skip]: http://msdn.microsoft.com/library/windowsazure/dn250573.aspx
[Take]: http://msdn.microsoft.com/library/windowsazure/dn250574.aspx
[Fiddler]: http://www.telerik.com/fiddler
[API personnalisée dans les kits de développement logiciel (SDK) clients pour Azure Mobile Services]: http://blogs.msdn.com/b/carlosfigueira/archive/2013/06/19/custom-api-in-azure-mobile-services-client-sdks.aspx
[Appel d'une API personnalisée à partir du client]: mobile-services-dotnet-backend-windows-store-dotnet-call-custom-api.md
[InvokeApiAsync]: http://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.invokeapiasync.aspx

<!---HONumber=August15_HO8-->