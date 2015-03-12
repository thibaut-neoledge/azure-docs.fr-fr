<properties 
	pageTitle="Utilisation du client Xamarin Component - Guide des fonctionnalités Azure Mobile Services" 
	description="Découvrez comment utiliser le client Xamarin Component pour Azure Mobile Services." 
	authors="lindydonna" 
	manager="dwrede" 
	editor="" 
	services="mobile-services" 
	documentationCenter=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-xamarin" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="11/11/2014" 
	ms.author="donnam"/>

# Utilisation du client Xamarin Component pour Azure Mobile Services

<div class="dev-center-tutorial-selector sublanding"> 
  <a href="/fr-fr/develop/mobile/how-to-guides/work-with-net-client-library/" title=".NET Framework">.NET Framework</a>
  	<a href="/fr-fr/develop/mobile/how-to-guides/work-with-html-js-client/" title="HTML/JavaScript">HTML/JavaScript</a><a href="/fr-fr/develop/mobile/how-to-guides/work-with-ios-client-library/" title="iOS">iOS</a><a href="/fr-fr/develop/mobile/how-to-guides/work-with-android-client-library/" title="Android">Android</a><a href="/fr-fr/develop/mobile/how-to-guides/work-with-xamarin-client-library/" title="Xamarin" class="current">Xamarin</a>
</div>


Ce guide vous montre comment exécuter les scénarios courants à l'aide du client Xamarin Component pour Azure Mobile Services, dans les applications Xamarin pour iOS et Android. Les scénarios traités incluent l'interrogation des données, l'insertion, la mise à jour et la suppression des données, l'authentification des utilisateurs et la gestion des erreurs. Si vous démarrez avec Mobile Services,nous vous invitons d'abord à suivre le didacticiel " Démarrage rapide Mobile Services "([Xamarin.iOS][Didacticiel de démarrage rapide Xamarin.iOS]/[Xamarin.Android][Prise en main de Mobile Services Android]) et le didacticiel " Prise en main des données dans .NET " ([Xamarin.iOS][Didacticiel de données Xamarin.iOS]/[Xamarin.Android][Prise en main des données Android]). Le didacticiel de démarrage rapide nécessite [Xamarin][téléchargement Xamarin] et le [Kit de développement logiciel (SDK) Mobile Services] et vous aide à créer votre compte et à créer votre premier service mobile.


## Sommaire

- [Présentation de Mobile Services]
- [Concepts]
- [Procédure : Création du client Mobile Services]
- [Procédure : Création d'une référence de table]
- [Procédure : Interroger des données à partir d'un service mobile]
	- [Filtrage des données renvoyées]
    - [Tri des données renvoyées]
	- [Renvoi des données dans les pages]
	- [Sélection de colonnes spécifiques]
	- [Rechercher des données par ID]
- [Procédure : Insérer des données dans un service mobile]
- [Procédure : Modifier des données dans un service mobile]
- [Procédure : Supprimer des données dans un service mobile]
- [Procédure : Authentification des utilisateurs]
- [Procédure : Gérer les erreurs]
- [Procédure : Utilisation de données non typées]
- [Procédure : Conception de tests]
- [Étapes suivantes]
	
[AZURE.INCLUDE [mobile-services-concepts](../includes/mobile-services-concepts.md)]

<h2><a name="setup"></a>Configuration et conditions préalables</h2>

Nous partons du principe que vous avez créé un service mobile et une table. Pour plus d'informations, consultez la page [Créer une table](http://go.microsoft.com/fwlink/?LinkId=298592). Dans le code utilisé dans cette rubrique, la table s'intitule  `TodoItem` et contient les colonnes suivantes : `id`, `Text`, et  `Complete`.

Le type .NET côté client typé correspondant est le suivant :


	public class TodoItem
	{
		public string id { get; set; }

		[JsonProperty(PropertyName = "text")]
		public string Text { get; set; }

		[JsonProperty(PropertyName = "complete")]
		public bool Complete { get; set; }
	}
	
Lorsque le schéma dynamique est activé, Azure Mobile Services génère automatiquement de nouvelles colonnes basées sur l'objet des demandes d'insertion ou de mise à jour. Pour plus d'informations, consultez la page [Schéma dynamique](http://go.microsoft.com/fwlink/?LinkId=296271).

<h2><a name="create-client"></a>Procédure : Création du client Mobile Services</h2>

Le code suivant permet de créer l'objet  `MobileServiceClient` utilisé pour accéder à votre service mobile. 
			
	MobileServiceClient client = new MobileServiceClient( 
		"AppUrl", 
		"AppKey" 
	); 

Dans le code ci-dessus, remplacez  `AppUrl` et  `AppKey` par l'URL et la clé d'application du service mobile, dans cet ordre. Tous deux sont disponibles dans le portail de gestion Azure. Pour y accéder, sélectionnez votre service mobile, puis cliquez sur " Tableau de bord ".

<h2><a name="instantiating"></a>Procédure : Création d'une référence de table</h2>

L'ensemble du code qui permet d'accéder aux données de la table Mobile Services ou de les modifier appelle des fonctions sur l'objet  `MobileServiceTable`. Pour obtenir une référence à la table, appelez la fonction [GetTable](http://msdn.microsoft.com/library/windowsazure/jj554275.aspx) sur une instance du  `MobileServiceClient`. 

    IMobileServiceTable<TodoItem> todoTable = 
		client.GetTable<TodoItem>();

Il s'agit du modèle de sérialisation typé ; consultez la section traitant du <a href="#untyped">modèle de sérialisation non typé</a> ci-après.
			
<h2><a name="querying"></a>Procédure : Interroger des données à partir d'un service mobile</h2>

Cette section explique comment émettre des requêtes à destination du service mobile. Les sous-sections décrivent différents aspects, tels que le tri, le filtrage et la pagination. 
			
### <a name="filtering"></a>Procédure : des données renvoyées

Le code suivant montre comment filtrer des données en incluant une clause  `Where` dans une requête. Il renvoie tous les éléments de  `todoTable` dont la propriété  `Complete` est égale à  `false`. La fonction  `Where` applique un prédicat de filtrage de ligne à la requête au niveau de la table. 
	

	// This query filters out completed TodoItems and 
	// items without a timestamp. 
	List<TodoItem> items = await todoTable
	   .Where(todoItem => todoItem.Complete == false)
	   .ToListAsync();

Vous pouvez afficher l'URI de la requête envoyée au service mobile en utilisant un logiciel d'inspection des messages, tels que les outils destinés aux développeurs de navigateurs ou Fiddler. Si vous examinez l'URI de demande ci-dessous, vous remarquerez que la chaîne de requête proprement dite est modifiée :

	GET /tables/todoitem?$filter=(complete+eq+false) HTTP/1.1				   
Cette requête serait normalement convertie approximativement sous forme de requête SQL côté serveur, comme suit :
			
	SELECT * 
	FROM TodoItem 			
	WHERE ISNULL(complete, 0) = 0
			
La fonction transmise à la méthode  `Where` peut avoir un nombre de conditions arbitraire. Par exemple, la ligne ci-dessous :

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

L'instruction  `where` ci-dessus recherche les éléments dont l'état  `Complete` a la valeur false et la colonne  `Text` a la valeur non null.

Cela aurait pu également être écrit sur plusieurs lignes :

	List<TodoItem> items = await todoTable
	   .Where(todoItem => todoItem.Complete == false)
	   .Where(todoItem => todoItem.Text != null)
	   .ToListAsync();

Les deux méthodes sont équivalentes et peuvent être utilisées de manière interchangeable.  La dernière option, qui consiste à concaténer plusieurs prédicats dans une même requête, est plus compacte. C'est celle que nous recommandons.

La clause  `where` prend en charge les opérations traduites dans le sous-ensemble OData Mobile Service. Cela inclut les opérateurs relationnels (==, !=, <, <=, >, >=), les opérateurs arithmétiques (+, -, /, *, %), la précision des nombres (Math.Floor, Math.Ceiling), les fonctions de chaîne (Length, Substring, Replace, IndexOf, StartsWith, EndsWith), les propriétés de date (Year, Month, Day, Hour, Minute, Second), les propriétés d'accès d'un objet, ainsi que les expressions qui combinent tous ces éléments.

### <a name="sorting"></a>Procédure : Tri des données renvoyées

Le code suivant montre comment trier des données en incluant une fonction  `OrderBy` ou  `OrderByDescending` dans la requête. Il renvoie des éléments de  `todoTable`, triés dans l'ordre croissant par le champ  `Text`. Par défaut, le serveur renvoie uniquement les 50 premiers éléments. 

> [AZURE.NOTE] Une taille de page pilotée par un serveur est utilisée par défaut pour empêcher le renvoi de tous les éléments. Cela permet d'éviter que les requêtes par défaut associées à des jeux de données volumineux aient un impact négatif sur le service.

Vous pouvez augmenter le nombre d'élémentsà renvoyer en appelant  `Take`, comme décrit dans la section qui suit.

	// Sort items in ascending order by Text field
	MobileServiceTableQuery<TodoItem> query = todoTable
					.OrderBy(todoItem => todoItem.Text)       
 	List<TodoItem> items = await query.ToListAsync();

	// Sort items in descending order by Text field
	MobileServiceTableQuery<TodoItem> query = todoTable
					.OrderByDescending(todoItem => todoItem.Text)       
 	List<TodoItem> items = await query.ToListAsync();			

### <a name="paging"></a>Procédure : Renvoi de données dans les pages

Le code suivant montre comment implémenter la pagination des données renvoyées à l'aide des clauses  `Take` et  `Skip` dans la requête.  Lorsqu'elle est exécutée, la requête suivante renvoie les trois premiers éléments de la table. 

	// Define a filtered query that returns the top 3 items.
	MobileServiceTableQuery<TodoItem> query = todoTable
					.Take(3);                              
	List<TodoItem> items = await query.ToListAsync();

La requête révisée ci-dessous ignore les trois premiers résultats et renvoie les trois résultats suivants. Il s'agit en fait de la deuxième " page " de données, dont la taille est de trois éléments.

	// Define a filtered query that skips the top 3 items and returns the next 3 items.
	MobileServiceTableQuery<TodoItem> query = todoTable
					.Skip(3)
					.Take(3);                              
	List<TodoItem> items = await query.ToListAsync();
			
Vous pouvez également utiliser la méthode [IncludeTotalCount](http://msdn.microsoft.com/library/windowsazure/jj730933.aspx) pour faire en sorte que la requête obtienne le nombre total de <i>tous</i> les enregistrements qui auraient été renvoyés, en ignorant toute clause de pagination/limite spécifiée :

	query = query.IncludeTotalCount();

Il s'agit d'un scénario simplifié dans lequel les valeurs de pagination codées en dur sont transmises aux méthodes  `Take` et  `Skip`. Dans une application réelle, vous pouvez utiliser des requêtes semblables à celles indiquées plus haut avec un contrôle pager ou une interface utilisateur comparable pour permettre aux utilisateurs d'accéder aux pages précédentes et suivantes. 

### <a name="selecting"></a>Procédure : Sélection de colonnes spécifiques

Vous pouvez indiquer le jeu de propriétés à inclure dans les résultats en ajoutant une clause  `Select` à la requête. Par exemple, le code suivant montre comment sélectionner un seul champ et comment sélectionner et mettre en forme plusieurs champs :

	// Select one field -- just the Text
	MobileServiceTableQuery<TodoItem> query = todoTable
					.Select(todoItem => todoItem.Text);
	List<string> items = await query.ToListAsync();
	
	// Select multiple fields -- both Complete and Text info
	MobileServiceTableQuery<TodoItem> query = todoTable
					.Select(todoItem => string.Format("{0} -- {1}", todoItem.Text.PadRight(30), todoItem.Complete ? "Now complete!" : "Incomplete!"));
	List<string> items = await query.ToListAsync();
			
Toutes les fonctions décrites jusqu'ici étant cumulatives, nous pouvons continuer de les appeler, ce qui aura à chaque fois des répercussions sur la requête. Un exemple supplémentaire :

	MobileServiceTableQuery<TodoItem> query = todoTable
					.Where(todoItem => todoItem.Complete == false)
					.Select(todoItem => todoItem.Text)
					.Skip(3).
					.Take(3);
	List<string> items = await query.ToListAsync();
	
### <a name="lookingup"></a>Procédure : Recherche des données par ID

La fonction  `LookupAsync` permet de rechercher des objets dans la base de données à partir d'un ID particulier. 

	// This query filters out the item with the ID of 25
	TodoItem item25 = await todoTable.LookupAsync(25);

<h2><a name="inserting"></a>Procédure : Insérer des données dans un service mobile</h2>

> [AZURE.NOTE] Si vous souhaitez effectuer des opérations d'insertion, de recherche, de suppression ou de mise à jour sur un type, vous devez créer un membre appelé **Id** (quelle que soit la classe). C'est pourquoi l'exemple de classe **TodoItem** contient un membre nommé **Id**. La valeur de l'ID ne doit pas être définie sur une autre valeur que celle par défaut pendant les opérations d'insertion. En revanche, la valeur d'ID doit toujours être présente et être définie sur une valeur différente de celle par défaut dans les opérations de mise à jour et de suppression.

Le code suivant montre comment insérer de nouvelles lignes dans une table. Le paramètre contient les données à insérer sous forme d'objet .NET.

	await todoTable.InsertAsync(todoItem);

Après le retour de l'appel  `todoTable.InsertAsync`, l'ID de l'objet sur le serveur est inséré dans l'objet  `todoItem` dans le client. 

Pour insérer des données non typées, vous pouvez tirer parti de Json.NET comme indiqué ci-dessous. De même, notez que l'ID ne doit pas être spécifié lors de l'insertion d'un objet.

	JObject jo = new JObject(); 
	jo.Add("Text", "Hello World"); 
	jo.Add("Complete", false);
	var inserted = await table.InsertAsync(jo);

Si vous essayez d'insérer un élément sans que le champ " Id " ne soit déjà défini, le service vous renvoie une exception  `MobileServiceInvalidOperationException`. 

<h2><a name="modifying"></a>Procédure : Modifier des données dans un service mobile</h2>

Le code suivant montre comment mettre à jour une instance existante ayant le même ID avec de nouvelles informations. Le paramètre contient les données à mettre à jour sous forme d'objet .NET.

	await todoTable.UpdateAsync(todoItem);


Pour insérer des données non typées, vous pouvez tirer parti de Json.NET de cette façon. Notez que pour effectuer une mise à jour, il est nécessaire de spécifier un ID, car c'est ainsi que le service mobile identifie l'instance à mettre à jour. L'ID peut être obtenu à partir du résultat de l'appel  `InsertAsync`.

	JObject jo = new JObject(); 
	jo.Add("Id", 52);
	jo.Add("Text", "Hello World"); 
	jo.Add("Complete", false);
	var inserted = await table.UpdateAsync(jo);
			
Si vous essayez de mettre à jour un élément sans que le champ " Id " ne soit déjà défini, le service n'a aucun moyen de déterminer quelle instance mettre à jour. Dans ce cas, une exception  `MobileServiceInvalidOperationException` vous est renvoyée par le service. De la même manière, si vous essayez de mettre à jour un élément non typé sans que le champ " Id " ne soit déjà défini, le service vous renvoie à nouveau une exception  `MobileServiceInvalidOperationException`. 
			
			
<h2><a name="deleting"></a>Procédure : Supprimer des données dans un service mobile</h2>

Le code suivant montre comment supprimer une instance existante. L'instance est identifiée par le champ " Id " défini au niveau de  `todoItem`.

	await todoTable.DeleteAsync(todoItem);

Pour supprimer des données non typées, vous pouvez tirer parti de Json.NET de cette façon. Notez que pour effectuer une demande de suppression, il est nécessaire de spécifier un ID, car c'est ainsi que le service mobile identifie l'instance à supprimer. Une demande de suppression n'a besoin que de l'ID ; les autres propriétés ne sont pas transmises au service, et si c'est le cas, celui-ci n'en tient pas compte. De même, le résultat d'un appel  `DeleteAsync` a généralement la valeur  `null`. L'ID à transmettre peut être obtenu à partir du résultat de l'appel  `InsertAsync`.

	JObject jo = new JObject(); 
	jo.Add("Id", 52);
	await table.DeleteAsync(jo);
			
Si vous essayez de supprimer un élément sans que le champ " Id " ne soit déjà défini, le service n'a aucun moyen de déterminer quelle instance supprimer. Dans ce cas, une exception  `MobileServiceInvalidOperationException` vous est renvoyée par le service. De la même manière, si vous essayez de supprimer un élément non typé sans que le champ " Id " ne soit déjà défini, le service vous renvoie à nouveau une exception  `MobileServiceInvalidOperationException`. 
		


<h2><a name="authentication"></a>Procédure : Authentification des utilisateurs</h2>

Mobile Services permet aux utilisateurs d'applications de s'authentifier par l'intermédiaire de divers fournisseurs d'identité externes : Facebook, Google, Compte Microsoft, Twitter et Azure Active Directory. Vous pouvez définir des autorisations sur les tables pour limiter l'accès à certaines opérations aux seuls utilisateurs authentifiés. Vous pouvez également utiliser l'identité des utilisateurs authentifiés pour implémenter des règles d'autorisation dans les scripts serveur. Pour plus d'informations, consultez le didacticiel " Prise en main de l'authentification "([Xamarin.iOS][authentification Xamarin.iOS]/[Xamarin.Android][authentification Xamarin.Android]).

Deux flux d'authentification sont pris en charge : un _flux serveur_ et un _flux client_. Le flux serveur fournit l'authentification la plus simple, car il repose sur l'interface d'authentification Web du fournisseur. En revanche, le flux client est celui qui s'intègre le plus profondément aux fonctionnalités propres à l'appareil, car il s'appuie sur les Kits de développement logiciel (SDK) propres au fournisseur et à l'appareil.

<h3>Flux serveur</h3>
Pour que Mobile Services puisse gérer le processus d'authentification dans votre application Windows Store ou Windows Phone, vous devez inscrire votre application auprès de votre fournisseur d'identité. Ensuite, dans votre service mobile, vous devez configurer l'ID d'application et le secret fournis par votre fournisseur. Pour plus d'informations, consultez le didacticiel " Prise en main de l'authentification "([Xamarin.iOS][authentification Xamarin.iOS]/[Xamarin.Android][authentification Xamarin.Android]).

Une fois que vous avez inscrit votre fournisseur d'identité, appelez simplement la [méthode LoginAsync] avec la valeur [MobileServiceAuthenticationProvider] de votre fournisseur. Par exemple, le code suivant initie une connexion de flux serveur via Facebook. 

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

Dans ce cas, Mobile Services gère le flux d'authentification OAuth 2.0 en affichant la page de connexion du fournisseur sélectionné et en générant un jeton d'authentification Mobile Services après avoir établi une connexion avec le fournisseur d'identité. La [méthode LoginAsync] renvoie un [MobileServiceUser], qui fournit à la fois [l'userId] de l'utilisateur authentifié et le [MobileServiceAuthenticationToken], sous la forme d'un jeton Web JSON (JWT). Ce jeton peut être mis en cache et réutilisé jusqu'à ce qu'il arrive à expiration. Pour plus d'informations, consultez la section [Mise en cache du jeton d'authentification].

<h3>Flux client</h3>

Votre application peut également contacter le fournisseur d'identité de manière indépendante, puis fournir le jeton renvoyé à Mobile Services à des fins d'authentification. Le flux client permet de proposer l'authentification unique aux utilisateurs ou de récupérer d'autres données utilisateur auprès du fournisseur d'identité. 

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

<h3><a name="caching"></a>Mise en cache du jeton d'authentification</h3>
Dans certains cas, l'appel à la méthode de connexion peut être évité après la première authentification de l'utilisateur. Vous pouvez utiliser un magasin local sécurisé(par exemple [Xamarin.Auth][composant Xamarin.Auth]) afin de mettre en cache l'identité de l'utilisateur actif lors de sa première connexion et, par la suite, à chaque fois que vous vérifiez si son identité est présente dans le cache. Lorsque le cache est vide, vous devez toujours soumettre l'utilisateur au processus de connexion. 

	using Xamarin.Auth;
	var accountStore = AccountStore.Create(); // Xamarin.iOS
	//var accountStore = AccountStore.Create(this); // Xamarin.Android

	// After logging in
	var account = new Account (user.UserId, new Dictionary<string,string> {{"token",user.MobileServiceAuthenticationToken}});
	accountStore.Save(account, "Facebook");

	// Log in 
	var accounts = accountStore.FindAccountsForService ("Facebook").ToArray();
	if (accounts.Count != 0)
	{
		user = new MobileServiceUser (accounts[0].Username);
		user.MobileServiceAuthenticationToken = accounts[0].Properties["token"];
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
	accountStore.Delete(account, "Facebook");


<h2><a name="errors"></a>Procédure : Gérer les erreurs</h2>

Il existe plusieurs méthodes pour détecter, valider et résoudre les erreurs dans Mobile Services. 

Par exemple, il est possible d'utiliser les scripts serveur inscrits dans un service mobile pour effectuer diverses opérations sur les données insérées et mises à jour, qu'il s'agisse de les valider ou de les modifier. Vous pouvez définir et inscrire un script serveur qui valide et modifie des données comme suit :

	function insert(item, user, request) 
	{
	   if (item.text.length > 10) {
		  request.respond(statusCodes.BAD_REQUEST, { error: "Text cannot exceed 10 characters" });
	   } else {
		  request.execute();
	   }
	}

Ce script côté serveur valide la longueur des données de chaîne envoyées au service mobile et refuse les chaînes trop longues, en l'occurrence, celles qui font plus de 10 caractères.

Comme le service mobile valide les données et envoie des réponses d'erreur côté serveur, vous pouvez mettre à jour votre application .NET de manière à ce qu'elle traite les réponses d'erreur de la validation.

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

<h2><a name="untyped"></a>Procédure : Utilisation de données non typées</h2>

Le client Xamarin Component a été conçu pour des scénarios fortement typés. Toutefois, une expérience plus légèrement typée peut parfois s'avérer pratique ; tel peut-être le cas lorsque, par exemple, il s'agit de traiter des objets avec un schéma ouvert. Ce scénario est mis en œuvre comme suit. Dans les requêtes, vous renoncez à LINQ pour utiliser le format wire.

	// Get an untyped table reference
	IMobileServiceTable untypedTodoTable = client.GetTable("TodoItem");			

	// Lookup untyped data using OData
	JToken untypedItems = await untypedTodoTable.ReadAsync("$filter=complete eq 0&$orderby=text");

Vous obtenez en retour des valeurs JSON que vous pouvez utiliser comme conteneur de propriétés. Pour plus d'informations sur JToken et Json.NET, consultez la page [Json.NET](http://json.codeplex.com/)

<h2><a name="unit-testing"></a>Procédure : Conception de tests</h2>

La valeur renvoyée par  `MobileServiceClient.GetTable` et les requêtes sont des interfaces. Il est donc facile de les simuler à des fins de test. Vous pouvez donc créer une  `MyMockTable : IMobileServiceTable<TodoItem>` qui implémente votre logique de test.

<h2><a name="nextsteps"></a>Étapes suivantes</h2>

Maintenant que vous avez consulté ce guide de fonctionnement, découvrez en détail comment effectuer des tâches importantes dans Mobile Services :

* Prise en main de Mobile Services ([Xamarin.iOS][Prise en main de Mobile Services iOS]/[Xamarin.Android][Prise en main de Mobile Services Android])
  <br/>Découvrez les règles de base d'utilisation de Mobile Services.

* Prise en main des données ([Xamarin.iOS][Prise en main des données iOS]/[Xamarin.Android][Prise en main des données Android])
  <br/>En savoir plus sur le stockage et l'interrogation des données à l'aide de Mobile Services.

* Prise en main d'authentification ([Xamarin.iOS][Prise en main de l'authentification iOS]/[Xamarin.Android][Authentification Xamarin.Android])
  <br/>En savoir plus sur l'authentification des utilisateurs de votre application avec un fournisseur d'identité.

* Validation et modification des données avec des scripts ([Xamarin.iOS][Validation et modification des données avec des scripts iOS]/[Xamarin.Android][Validation et modification des données avec des scripts Android])
  <br/>Obtenez plus d'informations sur l'utilisation des scripts serveur dans Mobile Services pour valider et modifier les données envoyées à partir de votre application.

* Affinage des requêtes à la pagination ([Xamarin.iOS][Affinage des requêtes à la pagination iOS]/[Xamarin.Android][Affinage des requêtes à la pagination Android])
  <br/>Apprenez à utiliser la pagination dans les requêtes pour contrôler la quantité de données traitées dans une seule requête.

* Autorisation des utilisateurs avec des scripts ([Xamarin.iOS][Autorisation des utilisateurs avec des scripts iOS]/[Xamarin.Android][Autorisation des utilisateurs avec des scripts Android])
  <br/>Découvrez comment prendre la valeur d'ID utilisateur fournie par Mobile Services en fonction de l'utilisateur authentifié et l'utiliser pour filtrer les données renvoyées par Mobile Services. 

<!-- Anchors. -->
[Présentation de Mobile Services]: #what-is
[Concepts]: #concepts
[Procédure : Création du client Mobile Services]: #create-client
[Procédure : Création d'une référence de table]: #instantiating
[Procédure : Interroger des données à partir d'un service mobile]: #querying
[Filtrage des données renvoyées]: #filtering
[Tri des données renvoyées]: #sorting
[Renvoi des données dans les pages]: #paging
[Sélection de colonnes spécifiques]: #selecting
[Rechercher des données par ID]: #lookingup
[Procédure : Liaison de données à l'interface utilisateur d'un service mobile]: #binding
[Procédure : Insérer des données dans un service mobile]: #inserting
[Procédure : Modifier des données dans un service mobile]: #modifying
[Procédure : Supprimer des données dans un service mobile]: #deleting
[Procédure : Authentification des utilisateurs]: #authentication
[Procédure : Gérer les erreurs]: #errors
[Procédure : Conception de tests]: #unit-testing 
[Procédure : Interroger des données à partir d'un service mobile]: #querying
[Procédure : Personnalisation du client]: #customizing
[Procédure : Utilisation de données non typées]: #untyped
[Personnalisation des en-têtes de requête]: #headers
[Personnalisation de la sérialisation]: #serialization
[Étapes suivantes]: #nextsteps
[Mise en cache du jeton d'authentification]: #caching

<!-- URLs. -->
[Prise en main de Mobile Services iOS]: /fr-fr/develop/mobile/tutorials/get-started-xamarin-ios
[Prise en main de Mobile Services Android]: /fr-fr/develop/mobile/tutorials/get-started-xamarin-android
[Téléchargement de Xamarin]: http://xamarin.com/download/
[Kit de développement logiciel (SDK) Mobile Services]: http://go.microsoft.com/fwlink/?LinkId=257545
[Didacticiel de démarrage rapide Xamarin.iOS]: /fr-fr/develop/mobile/tutorials/get-started-xamarin-ios/
[Didacticiel de démarrage rapideXamarin.Android]: /fr-fr/develop/mobile/tutorials/get-started-xamarin-android/
[Didacticiel de données Xamarin.iOS]: /fr-fr/develop/mobile/tutorials/get-started-with-data-xamarin-ios/
[Didacticiel de données Xamarin.Android]: /fr-fr/develop/mobile/tutorials/get-started-with-data-xamarin-android/
[Authentification Xamarin.iOS]: /fr-fr/develop/mobile/tutorials/get-started-with-users-xamarin-ios/
[Authentification Xamarin.Android]: /fr-fr/develop/mobile/tutorials/get-started-with-users-xamarin-android/
[Kit de développement logiciel (SDK) Mobile Services]: http://go.microsoft.com/fwlink/?LinkId=257545
[Composant Xamarin.Auth]: https://components.xamarin.com/view/xamarin.auth

[Kit de développement logiciel (SDK) Mobile Services]: http://nuget.org/packages/WindowsAzure.MobileServices/
[Prise en main des données iOS]: /fr-fr/develop/mobile/tutorials/get-started-with-data-xamarin-ios
[Prise en main des données Android]: /fr-fr/develop/mobile/tutorials/get-started-with-data-xamarin-android
[Prise en main de l'authentification iOS]: /fr-fr/develop/mobile/tutorials/get-started-with-users-xamarin-ios
[Prise en main de l'authentification Android]: /fr-fr/develop/mobile/tutorials/get-started-with-users-xamarin-android
[Validation et modification des données avec des scripts iOS]: /fr-fr/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-ios
[Validation et modification des données avec des scripts Android]: /fr-fr/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-android
[Affinage des requêtes à la pagination iOS]: /fr-fr/develop/mobile/tutorials/add-paging-to-data-xamarin-ios
[Affinage des requêtes à la pagination Android]: /fr-fr/develop/mobile/tutorials/add-paging-to-data-xamarin-android
[Autorisation des utilisateurs avec des scripts iOS]: /fr-fr/develop/mobile/tutorials/authorize-users-in-scripts-xamarin-ios
[Autorisation des utilisateurs avec des scripts Android]: /fr-fr/develop/mobile/tutorials/authorize-users-in-scripts-xamarin-android
[Méthode LoginAsync]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceclientextensions.loginasync.aspx
[MobileServiceAuthenticationProvider]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceauthenticationprovider.aspx
[MobileServiceUser]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.aspx
[l'userId]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.userid.aspx
[MobileServiceAuthenticationToken]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.mobileserviceauthenticationtoken.aspx


<!--HONumber=42-->
