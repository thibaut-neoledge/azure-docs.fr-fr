<properties linkid="obile-services-how-to-dotnet-client" urlDisplayName=".NET Client Library" pageTitle="Working with the Mobile Services .NET Client Library" metaKeywords="Azure Mobile Services, Mobile Service .NET client, .NET client" description="Learn how to use an .NET client for Azure Mobile Services." metaCanonical="" services="" documentationCenter="Mobile" title="How to use a .NET client for Azure Mobile Services" authors="krisragh" solutions="" manager="" editor="" />

Utilisation d'un client .NET pour Azure Mobile Services
=======================================================

[.NET Framework](/en-us/develop/mobile/how-to-guides/work-with-net-client-library/ ".NET Framework") [HTML/JavaScript](/en-us/develop/mobile/how-to-guides/work-with-html-js-client/ "HTML/JavaScript")[iOS](/en-us/develop/mobile/how-to-guides/work-with-ios-client-library/ "iOS")[Android](/en-us/develop/mobile/how-to-guides/work-with-android-client-library/ "Android")[Xamarin](/en-us/develop/mobile/how-to-guides/work-with-xamarin-client-library/ "Xamarin")

Ce guide vous montre comment exécuter les scénarios courants à l'aide d'un client .NET pour Azure Mobile Services, dans les applications Windows Store et Windows Phone. Les scénarios traités incluent l'interrogation des données, l'insertion, la mise à jour et la suppression des données, l'authentification des utilisateurs et la gestion des erreurs. Si vous débutez avec Mobile Services, nous vous invitons à suivre d'abord le didacticiel « Démarrage rapide Mobile Services » ([Didacticiel Démarrage rapide Windows Store](http://www.windowsazure.com/fr-fr/develop/mobile/tutorials/get-started/)/[Didacticiel Démarrage rapide Windows Phone](http://www.windowsazure.com/fr-fr/develop/mobile/tutorials/get-started-wp8/)) et le didacticiel « Prise en main des données dans .NET » ([Didacticiel sur les données Windows Store](http://www.windowsazure.com/fr-fr/develop/mobile/tutorials/get-started-with-data-dotnet/)/[Didacticiel sur les données Windows Phone](http://www.windowsazure.com/fr-fr/develop/mobile/tutorials/get-started-with-data-wp8/)). Le didacticiel de démarrage rapide, qui nécessite le [Kit de développement logiciel (SDK) Mobile Services](http://nuget.org/packages/WindowsAzure.MobileServices/), vous aide à configurer votre compte et à créer votre premier service mobile.

Sommaire
--------

-   [Présentation de Mobile Services](#what-is)
-   [Concepts](#concepts)
-   [Création du client Mobile Services](#create-client)
-   [Création d'une référence de table](#instantiating)
-   [Interrogation des données à partir d'un service mobile](#querying)
    -   [Filtrage des données renvoyées](#filtering)
    -   [Tri des données renvoyées](#sorting)
    -   [Renvoi de données dans les pages](#paging)
    -   [Sélection de colonnes spécifiques](#selecting)
    -   [Recherche des données par ID](#lookingup)
-   [Insertion de données dans un service mobile](#inserting)
-   [Modification de données dans un service mobile](#modifying)
-   [Suppression de données dans un service mobile](#deleting)
-   [Utilisation de l'accès concurrentiel optimiste](#optimisticconcurrency)
-   [Liaison de données à l'interface utilisateur d'un service mobile](#binding)
-   [Authentification des utilisateurs](#authentication)
-   [Gestion des erreurs](#errors)
-   [Utilisation de données non typées](#untyped)
-   [Conception de tests unitaires](#unit-testing)
-   [Personnalisation du client](#customizing)
    -   [Personnalisation des en-têtes de requête](#headers)
    -   [Personnalisation de la sérialisation](#serialization)
-   [Étapes suivantes](#nextsteps)

[WACOM.INCLUDE [mobile-services-concepts](../includes/mobile-services-concepts.md)]

ConfigurationConfiguration et conditions préalables
---------------------------------------------------

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

Création du client Mobile Services Création du client Mobile Services
---------------------------------------------------------------------

Le code suivant permet de créer l'objet `MobileServiceClient` utilisé pour accéder à votre service mobile.

    MobileServiceClient client = new MobileServiceClient( 
        "AppUrl", 
        "AppKey" 
    ); 

Dans le code ci-dessus, remplacez `AppUrl` et `AppKey` par l'URL et la clé d'application du service mobile, dans cet ordre. Tous deux sont disponibles sur le portail de gestion Azure. Pour y accéder, sélectionnez votre service mobile, puis cliquez sur « Tableau de bord ».

Création d'une référence de table Création d'une référence de table
-------------------------------------------------------------------

L'ensemble du code qui permet d'accéder aux données de la table Mobile Services ou de les modifier appelle des fonctions sur l'objet `MobileServiceTable`. Pour obtenir une référence à la table, appelez la fonction [GetTable](http://msdn.microsoft.com/fr-fr/library/windowsazure/jj554275.aspx) sur une instance du `MobileServiceClient`.

    IMobileServiceTable<TodoItem> todoTable = 
        client.GetTable<TodoItem>();

Il s'agit du modèle de sérialisation typé ; consultez la section traitant du [modèle de sérialisation non typé](#untyped) ci-après.

Interrogation des données Interrogation des données à partir d'un service mobile
--------------------------------------------------------------------------------

Cette section explique comment émettre des requêtes à destination du service mobile. Les sous-sections décrivent différents aspects, tels que le tri, le filtrage et la pagination.

> [WACOM.NOTE] Une taille de page pilotée par le serveur est utilisée par défaut pour empêcher le renvoi de toutes les lignes. Cela permet d'éviter que les requêtes par défaut associées à des jeux de données volumineux aient un impact négatif sur le service. Pour obtenir le renvoi de plus de 50 lignes, utilisez la méthode `Take`, comme décrit dans la section [Renvoi de données dans les pages](#paging).

### Filtrage des données renvoyées

Le code suivant montre comment filtrer des données en incluant une clause `Where` dans une requête. Elle renvoie tous les éléments de `todoTable` dont la propriété `Complete` a la valeur `false`. La fonction `Where` applique un prédicat de filtrage de ligne à la requête au niveau de la table.

    // Cette requête filtre les éléments TodoItems terminés et 
    // les éléments sans horodatage. 
    List<TodoItem> items = await todoTable
       .Where(todoItem => todoItem.Complete == false)
       .ToListAsync();

Vous pouvez afficher l'URI de la requête envoyée au service mobile en utilisant un logiciel d'inspection des messages, tel que les outils destinés aux développeurs de navigateurs ou Fiddler. Si vous examinez l'URI de requête ci-dessous, vous remarquerez que la chaîne de requête elle-même est modifiée :

    GET /tables/todoitem?$filter=(complete+eq+false) HTTP/1.1                   

Cette requête serait normalement convertie approximativement sous forme de requête SQL côté serveur, comme suit :

    SELECT * 
    FROM TodoItem           
    WHERE ISNULL(complete, 0) = 0

La fonction transmise à la méthode `Where` peut avoir un nombre de conditions arbitraire. Par exemple, la ligne ci-dessous :

    // Cette requête filtre les éléments TodoItems terminés lorsque Text n'a pas la valeur null
    List<TodoItem> items = await todoTable
       .Where(todoItem => todoItem.Complete == false
           && todoItem.Text != null)
       .ToListAsync();

Serait approximativement convertie (pour la même requête indiquée plus haut) comme suit

    SELECT * 
    FROM TodoItem 
    WHERE ISNULL(complete, 0) = 0
          AND ISNULL(text, 0) = 0

L'instruction `where` ci-dessus recherche les éléments dont l'état `Complete` a la valeur false et la colonne `Text` a la valeur non null.

Cela aurait pu également être écrit sur plusieurs lignes :

    List<TodoItem> items = await todoTable
       .Where(todoItem => todoItem.Complete == false)
       .Where(todoItem => todoItem.Text != null)
       .ToListAsync();

Les deux méthodes sont équivalentes et peuvent être utilisées de manière interchangeable. La dernière option, qui consiste à concaténer plusieurs prédicats dans une même requête, est plus compacte et celle que nous recommandons.

La clause `where` prend en charge les opérations traduites dans le sous-ensemble OData Mobile Services. Cela inclut les opérateurs relationnels (==, !=, &lt;, &lt;=, \>, \>=), les opérateurs arithmétiques (+, -, /, \*, %), la précision des nombres (Math.Floor, Math.Ceiling), les fonctions de chaîne (Length, Substring, Replace, IndexOf, StartsWith, EndsWith), les propriétés de date (Year, Month, Day, Hour, Minute, Second), les propriétés d'accès d'un objet, ainsi que les expressions qui combinent tous ces éléments.

### Tri des données renvoyées

Le code suivant montre comment trier les données en incluant une fonction `OrderBy` ou `OrderByDescending` dans la requête. Il renvoie des éléments de `todoTable`, triés dans l'ordre croissant par le champ `Text`.

    // Trie les éléments par ordre croissant en fonction du champ Text
    MobileServiceTableQuery<TodoItem> query = todoTable
                    .OrderBy(todoItem => todoItem.Text)       
	List items = await query.ToListAsync();

    // Trie les éléments par ordre décroissant en fonction du champ Text
    MobileServiceTableQuery<TodoItem> query = todoTable
                    .OrderByDescending(todoItem => todoItem.Text)       
	List items = await query.ToListAsync();

### Renvoi de données dans les pages

Par défaut, le serveur renvoie uniquement les 50 premières lignes. Vous pouvez augmenter le nombre de lignes renvoyées en appelant la méthode [Take](http://msdn.microsoft.com/fr-fr/library/windowsazure/dn250574.aspx). Associez `Take` à la méthode [Skip](http://msdn.microsoft.com/fr-fr/library/windowsazure/dn250573.aspx) pour demander une « page » spécifique du jeu de données total renvoyé par la requête. Lorsqu'elle est exécutée, la requête suivante renvoie les trois premiers éléments de la table.

    // Définit une requête filtrée qui renvoie les 3 premiers éléments.
    MobileServiceTableQuery<TodoItem> query = todoTable
                    .Take(3);                              
    List<TodoItem> items = await query.ToListAsync();

La requête révisée ci-dessous ignore les trois premiers résultats et renvoie les trois résultats suivants. Il s'agit en fait de la deuxième « page » de données, dont la taille est de trois éléments.

    // Definit une requête filtrée qui ignore les 3 premiers éléments et renvoie les trois suivants. 
    MobileServiceTableQuery<TodoItem> query = todoTable
                    .Skip(3)
                    .Take(3);                              
    List<TodoItem> items = await query.ToListAsync();

Vous pouvez également utiliser la méthode [IncludeTotalCount](http://msdn.microsoft.com/fr-fr/library/windowsazure/dn250560.aspx) pour faire en sorte que la requête obtienne le nombre total de *tous* les enregistrements qui auraient été renvoyés, en ignorant toute clause de pagination/limite spécifiée :

    query = query.IncludeTotalCount();

Il s'agit d'un scénario simplifié dans lequel les valeurs de pagination codées en dur sont transmises aux méthodes `Take` et `Skip`. Dans une application réelle, vous pouvez utiliser des requêtes semblables à celles indiquées plus haut avec un contrôle pager ou une interface utilisateur comparable pour permettre aux utilisateurs d'accéder aux pages précédentes et suivantes.

### Sélection de colonnes spécifiques

Vous pouvez indiquer le jeu de propriétés à inclure dans les résultats en ajoutant une clause `Select` à la requête. Par exemple, le code suivant montre comment sélectionner un seul champ et comment sélectionner et mettre en forme plusieurs champs :

    // Sélection d'un seul champ (Text)
    MobileServiceTableQuery<TodoItem> query = todoTable
                    .Select(todoItem => todoItem.Text);
    List<string> items = await query.ToListAsync();

    // Sélection de plusieurs champs (Complete et Text)
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

### Recherche de données par ID

La fonction `LookupAsync` permet de rechercher des objets dans la base de données à partir d'un ID particulier.

    // Cette requête filtre l'élément associé à l'ID 37BBF396-11F0-4B39-85C8-B319C729AF6D
    TodoItem item = await todoTable.LookupAsync("37BBF396-11F0-4B39-85C8-B319C729AF6D");

Insertion de donnéesInsertion de données dans un service mobile
---------------------------------------------------------------

**Remarque**

Si vous souhaitez effectuer des opérations d'insertion, de recherche, de suppression ou de mise à jour sur un type, vous devez créer un membre appelé **Id**. C'est pourquoi l'exemple de classe **TodoItem** contient un membre nommé **Id**. Les opérations de mise à jour et de suppression doivent toujours comporter une valeur d'ID valide.

Le code suivant montre comment insérer de nouvelles lignes dans une table. Le paramètre contient les données à insérer sous forme d'objet .NET.

    await todoTable.InsertAsync(todoItem);

Si aucune valeur d'ID personnalisée unique n'est incluse dans l'objet `todoItem` transmis à l'appel `todoTable.InsertAsync`, une valeur d'ID est générée par le serveur défini dans l'objet `todoItem` renvoyé au client.

Mobile Services prend en charge les valeurs de chaîne personnalisées uniques pour l'ID de table. Les applications peuvent ainsi utiliser des valeurs personnalisées, telles que des adresses de messagerie ou des noms d'utilisateur, pour la colonne d'ID d'une table Mobile Services. Si aucune valeur d'ID de chaîne n'est fournie lors de l'insertion de nouveaux enregistrements dans une table, Mobile Services génère une valeur d'ID unique.

La prise en charge des ID de chaîne fournit les avantages suivants aux développeurs :

-   Les ID peuvent être générés sans effectuer d'aller-retour vers la base de données.
-   Il est plus facile de fusionner des enregistrements de plusieurs tables ou bases de données.
-   Les valeurs d'ID peuvent mieux s'intégrer à la logique d'une application.

Vous pouvez également utiliser des scripts serveur pour définir des valeurs d'ID. L'exemple de script ci-dessous génère un GUID personnalisé et l'attribue à l'ID d'un nouvel enregistrement. Il est semblable à la valeur d'ID qui serait générée par Mobile Services si vous ne transmettiez pas de valeur pour l'ID d'un enregistrement.

    //Exemple de génération d'ID. Il ne s'agit pas d'une étape obligatoire dans le sens où Mobile Services
    //génère un ID si vous n'en transmettez pas un.
    item.id = item.id || newGuid();
    request.execute();

    function newGuid() {
        var pad4 = function(str) { return "0000".substring(str.length) + str; };
        var hex4 = function () { return pad4(Math.floor(Math.random() * 0x10000 /* 65536 */ ).toString(16)); };
        return (hex4() + hex4() + "-" + hex4() + "-" + hex4() + "-" + hex4() + "-" + hex4() + hex4() + hex4());
    }

Si une application fournit la valeur d'un ID, Mobile Services la stocke en l'état. Les espaces de début et de fin sont également inclus. Ils ne sont pas supprimés de la valeur.

La valeur d'`id` doit être unique et ne contenir aucun caractère présent dans les ensembles suivants :

-   Caractères de contrôle : [0x0000-0x001F] et [0x007F-0x009F]. Pour plus d'informations, consultez la page [Codes de contrôle ASCII C0 et C1](http://en.wikipedia.org/wiki/Data_link_escape_character#C1_set).
-   Caractères imprimables : **"**(0x0022), **+** (0x002B), **/** (0x002F), **?** (0x003F), **\\** (0x005C), **\`** (0x0060)
-   Les ID « . » et « .. »

Vous pouvez également utiliser des ID d'entier pour vos tables. Pour utiliser un ID d'entier, vous devez créer votre table à l'aide de la commande `mobile table create` en utilisant l'option `--integerId`. Cette commande s'utilise avec l'interface de ligne de commande (CLI) pour Azure. Pour plus d'informations sur l'utilisation de l'interface de ligne de commande, consultez la page [Interface de ligne de commande pour la gestion des tables Mobile Services](http://www.windowsazure.com/fr-fr/manage/linux/other-resources/command-line-tools/#Mobile_Tables).

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

Modification de donnéesModification de données dans un service mobile
---------------------------------------------------------------------

Le code suivant montre comment mettre à jour une instance existante avec le même ID avec des informations nouvelles. Le paramètre contient les données à mettre à jour sous forme d'objet .NET.

    await todoTable.UpdateAsync(todoItem);

Pour insérer des données non typées, vous pouvez tirer parti de Json.NET de cette façon. Notez que pour effectuer une mise à jour, il est nécessaire de spécifier un ID, car c'est ainsi que le service mobile identifie l'instance à mettre à jour. L'ID peut être obtenu à partir du résultat de l'appel `InsertAsync`.

    JObject jo = new JObject(); 
    jo.Add("Id", "37BBF396-11F0-4B39-85C8-B319C729AF6D");
    jo.Add("Text", "Hello World"); 
    jo.Add("Complete", false);
    var inserted = await table.UpdateAsync(jo);

Si vous tentez de mettre à jour un élément sans fournir de valeur « Id », le service n'a aucun moyen de déterminer quelle instance mettre à jour. Dans ce cas, le Kit de développement logiciel (SDK) Mobile Services lève une exception `ArgumentException`.

Suppression de donnéesSuppression de données dans un service mobile
-------------------------------------------------------------------

Le code suivant montre comment supprimer une instance existante. L'instance est identifiée par le champ « Id » défini au niveau de `todoItem`.

    await todoTable.DeleteAsync(todoItem);

Pour supprimer des données non typées, vous pouvez tirer parti de Json.NET de cette façon. Notez que pour effectuer une demande de suppression, il est nécessaire de spécifier un ID, car c'est ainsi que le service mobile identifie l'instance à supprimer. Une demande de suppression n'a besoin que de l'ID ; les autres propriétés ne sont pas transmises au service, et si c'est le cas, celui-ci n'en tient pas compte. De même, le résultat d'un appel `DeleteAsync` a généralement la valeur `null`. L'ID à transmettre peut être obtenu à partir du résultat de l'appel `InsertAsync`.

    JObject jo = new JObject(); 
    jo.Add("Id", "37BBF396-11F0-4B39-85C8-B319C729AF6D");
    await table.DeleteAsync(jo);

Si vous essayez de supprimer un élément sans que le champ « Id » ne soit déjà défini, le service n'a aucun moyen de déterminer quelle instance supprimer. Dans ce cas, une exception `MobileServiceInvalidOperationException` vous est renvoyée par le service. De la même manière, si vous essayez de supprimer un élément non typé sans que le champ « Id » ne soit déjà défini, le service vous renvoie à nouveau une exception `MobileServiceInvalidOperationException`.

Accès concurrentiel optimisteUtilisation de l'accès concurrentiel optimiste
---------------------------------------------------------------------------

Dans certains scénarios, plusieurs clients peuvent écrire à un même moment des modifications dans un même élément. En l'absence de détection de conflits, la dernière écriture remplace les mises à jour précédentes, même si cela n'était pas le but recherché. Le contrôle d'accès concurrentiel optimiste considère que chaque transaction peut être validée et qu'à ce titre, elle ne fait appel à aucun verrouillage de ressources. Avant de valider une transaction, le contrôle d'accès concurrentiel optimiste vérifie qu'aucune autre transaction n'a modifié les données. Si les données ont été modifiées, la transaction de validation est annulée.

Mobile Services prend en charge le contrôle d'accès concurrentiel optimiste en suivant les modifications apportées à chaque élément à l'aide de la colonne de la propriété système `__version` définie pour chaque table créée par Mobile Services. Chaque fois qu'un enregistrement est mis à jour, Mobile Services attribue une nouvelle valeur à la propriété `__version` de cet enregistrement. À chaque demande de mise à jour, la propriété `__version` de l'enregistrement inclus dans la demande est comparée à celle de l'enregistrement basé sur le serveur. Si la version transmise avec la demande ne correspond pas à celle du serveur, la bibliothèque cliente .NET de Mobile Services lève une exception `MobileServicePreconditionFailedException<T>`. Le type inclus avec l'exception est l'enregistrement du serveur contenant la version. À partir de cette information, l'application peut ensuite décider ou non de réexécuter la demande de mise à jour avec la valeur correcte de `__version` du serveur pour valider les modifications.

Pour activer l'accès concurrentiel optimiste, l'application définit une colonne dans la classe table pour la propriété système `__version`. La définition suivante en fournit un exemple.

    public class TodoItem
    {
        public string Id { get; set; }

        [JsonProperty(PropertyName = "text")]
        public string Text { get; set; }

        [JsonProperty(PropertyName = "complete")]
        public bool Complete { get; set; }

        // *** Activation de l'accès concurrentiel optimiste *** //
        [JsonProperty(PropertyName = "__version")]
        public byte[] Version { set; get; }
    }

Dans le cas des applications utilisant des tables non typées, l'accès concurrentiel optimiste est activé en définissant l'indicateur `Version` au niveau des propriétés `SystemProperties` de la table, comme suit.

    //Activez l'accès concurrentiel optimiste en récupérant __version 
    todoTable.SystemProperties |= MobileServiceSystemProperties.Version;

Le code suivant montre comment résoudre un conflit d'écriture une fois qu'il est détecté. La valeur correcte de `__version` doit être incluse dans l'appel `UpdateAsync()` pour valider une résolution.

	private async void UpdateToDoItem(TodoItem item)
	{
    	MobileServicePreconditionFailedException<TodoItem> exception = null;         
		
	    try
    	{
	        //mise à jour au niveau de la table distante
    	    await todoTable.UpdateAsync(item);
    	}
    	catch (MobileServicePreconditionFailedException<TodoItem> writeException)
	    {
        	exception = writeException;
	    }
		
    	if (exception != null)
    	{
			// Conflit détecté, l'élément a changé depuis la dernière requête
            // Résolution du conflit entre l'élément local et l'élément serveur
	        await ResolveConflict(item, exception.Item);
    	}
	}


	private async Task ResolveConflict(TodoItem localItem, TodoItem serverItem)
	{
    	//Demande à l'utilisateur de définir la résolution entre les versions
	    MessageDialog msgDialog = new MessageDialog(String.Format("Server Text: \"{0}\" \nLocal Text: \"{1}\"\n", 
        	                                        serverItem.Text, localItem.Text), 
                                                	"CONFLICT DETECTED - Select a resolution:");

	    UICommand localBtn = new UICommand("Commit Local Text");
    	UICommand ServerBtn = new UICommand("Leave Server Text");
    	msgDialog.Commands.Add(localBtn);
	    msgDialog.Commands.Add(ServerBtn);          

    	localBtn.Invoked = async (IUICommand command) =>
	    {
        	// Pour résoudre le conflit, mettez à jour la version de 
            // l'élément à valider. Sinon, vous continuerez d'obtenir
            // une exception MobileServicePreConditionFailedException.
	        localItem.Version = serverItem.Version;             

    	    // Une mise à jour récursive est ici opérée au cas où une autre 
            // modification se serait produite pendant que l'utilisateur prenait une décision
	        UpdateToDoItem(localItem);
    	};          
		
	    ServerBtn.Invoked = async (IUICommand command) =>
    	{
	        RefreshTodoItems();
    	};          
		
	    await msgDialog.ShowAsync();
	}

Pour obtenir un exemple plus complet d'utilisation de l'accès concurrentiel optimiste pour Mobile Services, consultez le [didacticiel Accès concurrentiel optimiste](http://www.windowsazure.com/fr-fr/develop/mobile/tutorials/handle-database-write-conflicts-dotnet/).

Affichage de donnéesLiaison de données à l'interface utilisateur d'un service mobile
------------------------------------------------------------------------------------

Cette section montre comment afficher des objets de données renvoyés à l'aide d'éléments d'interface utilisateur. Pour lancer une requête sur `todoTable` afin d'extraire les éléments incomplets et de les afficher dans une liste très simple, vous pouvez exécuter l'exemple de code suivant pour lier la source de la liste à une requête. L'utilisation de `MobileServiceCollection` permet de créer une collection de liaisons prenant en charge les services mobiles.

    // Cette requête filtre les éléments TodoItems terminés. 
    MobileServiceCollection<TodoItem, TodoItem> items = await todoTable
        .Where(todoItem => todoItem.Complete == false)
        .ToCollectionAsync();

    // itemsControl est un contrôle de type IEnumerable qui pourrait être lié à un contrôle de liste d'interface utilisateur
    IEnumerable itemsControl  = items;            
            
    // Liez ceci à un ListBox
    ListBox lb = new ListBox();
    lb.ItemsSource = items;

Certains contrôles de Windows Runtime prennent en charge une interface appelée [ISupportIncrementalLoading](http://msdn.microsoft.com/fr-fr/library/windows/apps/Hh701916). Cette interface permet aux contrôles de demander des données supplémentaires lorsque l'utilisateur fait défiler l'écran. Une prise en charge de cette interface peut être intégrée aux applications Windows Store via `MobileServiceIncrementalLoadingCollection`, qui traite automatiquement les appels en provenance des contrôles. Pour utiliser `MobileServiceIncrementalLoadingCollection` dans les applications Windows Store, procédez comme suit :

         MobileServiceIncrementalLoadingCollection<TodoItem,TodoItem> items;
        items =  todoTable.Where(todoItem => todoItem.Complete == false)
                    .ToIncrementalLoadingCollection();

        ListBox lb = new ListBox();
        lb.ItemsSource = items;

Pour utiliser la nouvelle collection sur Windows Phone, appliquez les méthodes d'extension `ToCollection` à `IMobileServiceTableQuery<T>` et `IMobileServiceTable<T>`. Pour charger réellement les données, appelez `LoadMoreItemsAsync()`.

    MobileServiceCollection<TodoItem, TodoItem> items = todoTable.Where(todoItem => todoItem.Complete==false).ToCollection(); 
    await items.LoadMoreItemsAsync();         

Lorsque vous utilisez la collection créée par l'appel de `ToCollectionAsync` ou `ToCollection`, vous obtenez une collection qui peut être liée à des contrôles d'interface utilisateur. Cette collection prend en charge la pagination, ce qui signifie qu'un contrôle peut demander à la collection de « charger plus d'éléments », ce qu'elle fera pour le contrôle. À ce stade, aucun code utilisateur n'intervient ; c'est le contrôle qui démarre le flux. Toutefois, sachant que la collection charge les données à partir du réseau, ce chargement peut parfois échouer. Face à ces échecs, vous pouvez ignorer la méthode `OnException` au niveau de `MobileServiceIncrementalLoadingCollection` pour traiter les exceptions résultant des appels à `LoadMoreItemsAsync` émis par les contrôles.

Enfin, imaginez que votre table contient de nombreux champs, mais que vous ne souhaitez en afficher qu'une partie dans votre contrôle. Vous pouvez suivre les instructions fournies plus haut dans la section [Sélection de colonnes spécifiques](#selecting) pour sélectionner les colonnes à afficher dans l'interface utilisateur.

AuthentificationAuthentification des utilisateurs
-------------------------------------------------

Mobile Services permet aux utilisateurs d'applications de s'authentifier par l'intermédiaire de divers fournisseurs d'identité externes : Facebook, Google, Compte Microsoft, Twitter et Azure Active Directory. Vous pouvez définir des autorisations sur les tables pour limiter l'accès à certaines opérations aux seuls utilisateurs authentifiés. Vous pouvez également utiliser l'identité des utilisateurs authentifiés pour implémenter des règles d'autorisation dans les scripts serveur. Pour plus d'informations, consultez le didacticiel « Prise en main de l'authentification » ([Windows Store](http://www.windowsazure.com/fr-fr/develop/mobile/tutorials/get-started-with-users-dotnet/)/[Windows Phone](http://www.windowsazure.com/fr-fr/develop/mobile/tutorials/get-started-with-users-wp8/)).

Deux flux d'authentification sont pris en charge : un *flux serveur* et un *flux client*. Le flux serveur fournit l'authentification la plus simple, car il repose sur l'interface d'authentification Web du fournisseur. En revanche, le flux client est celui qui s'intègre le plus profondément aux fonctionnalités propres à l'appareil, car il s'appuie sur les Kits de développement logiciel (SDK) propres au fournisseur et à l'appareil.

### Flux serveur

Pour que Mobile Services puisse gérer le processus d'authentification dans votre application Windows Store ou Windows Phone, vous devez inscrire votre application auprès de votre fournisseur d'identité. Ensuite, dans votre service mobile, vous devez configurer l'ID d'application et le secret fournis par votre fournisseur. Pour plus d'informations, consultez le didacticiel « Prise en main de l'authentification » ([Windows Store](http://www.windowsazure.com/fr-fr/develop/mobile/tutorials/get-started-with-users-dotnet/)/[Windows Phone](http://www.windowsazure.com/fr-fr/develop/mobile/tutorials/get-started-with-users-wp8/)).

Une fois que vous avez inscrit votre fournisseur d'identité, il vous suffit d'appeler la [méthode LoginAsync](http://msdn.microsoft.com/fr-fr/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceclientextensions.loginasync.aspx) avec la valeur [MobileServiceAuthenticationProvider](http://msdn.microsoft.com/fr-fr/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceauthenticationprovider.aspx) de votre fournisseur. Par exemple, le code suivant initie une connexion de flux serveur via Facebook.

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

Si vous utilisez un fournisseur d'identité différent de Facebook, remplacez la valeur de [MobileServiceAuthenticationProvider](http://msdn.microsoft.com/fr-fr/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceauthenticationprovider.aspx) ci-dessus par la valeur de votre fournisseur.

Dans ce cas, Mobile Services gère le flux d'authentification OAuth 2.0 en affichant la page de connexion du fournisseur sélectionné et en générant un jeton d'authentification Mobile Services après avoir établi une connexion avec le fournisseur d'identité. La [méthode LoginAsync](http://msdn.microsoft.com/fr-fr/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceclientextensions.loginasync.aspx) renvoie un [MobileServiceUser](http://msdn.microsoft.com/fr-fr/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.aspx), qui fournit à la fois l'[userId](http://msdn.microsoft.com/fr-fr/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.userid.aspx) de l'utilisateur authentifié et le [MobileServiceAuthenticationToken](http://msdn.microsoft.com/fr-fr/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.mobileserviceauthenticationtoken.aspx), sous la forme d'un jeton Web JSON (JWT). Ce jeton peut être mis en cache et réutilisé jusqu'à ce qu'il arrive à expiration. Pour plus d'informations, consultez la section [Mise en cache du jeton d'authentification](#caching).

**Application Windows Store**

Lorsque vous utilisez le fournisseur de connexion du compte Microsoft pour authentifier les utilisateurs de votre application Windows Store, vous devez également inscrire le package de l'application auprès de Mobile Services. Lorsque vous inscrivez les informations du package de votre application Windows Store auprès de Mobile Services, le client peut réutiliser les informations d'identification du compte Microsoft pour fournir une authentification unique. Si vous ne le faites pas, vos utilisateurs se connectant via le compte Microsoft seront invités à se connecter à chaque appel de la méthode de connexion. Pour savoir comment inscrire votre package d'application Windows Store, consultez la rubrique [Inscription du package de votre application Windows Store pour l'authentification Microsoft](/en-us/develop/mobile/how-to-guides/register-windows-store-app-package/). Une fois les informations du package inscrites auprès de Mobile Services, appelez la méthode [LoginAsync](http://go.microsoft.com/fwlink/p/?LinkId=311594) en fournissant la valeur **true** au paramètre *useSingleSignOn* pour réutiliser les informations d'identification.

### Flux client

Votre application peut également contacter le fournisseur d'identité de manière indépendante, puis fournir le jeton renvoyé à Mobile Services à des fins d'authentification. Le flux client permet de proposer l'authentification unique aux utilisateurs ou de récupérer d'autres données utilisateur auprès du fournisseur d'identité.

Dans la forme la plus simple, vous pouvez utiliser le flux client comme indiqué dans cet extrait de code pour Facebook ou Google.

    var token = new JObject();
    // Remplacez access_token_value par la valeur réelle du jeton d'accès que vous avez obtenu 
    // à l'aide du SDK Facebook ou Google.
    token.Add("access_token", "access_token_value");
            
    private MobileServiceUser user;
    private async System.Threading.Tasks.Task Authenticate()
    {
        while (user == null)
        {
            string message;
            try
            {
                // Remplacez MobileServiceAuthenticationProvider.Facebook 
                // par MobileServiceAuthenticationProvider.Google si vous utilisez l'auth. Google
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

     // Remplacez authentication_token_value par la valeur réelle du jeton d'authentification Microsoft que vous avez obtenu via le SDK Live
    user = await client
        .LoginWithMicrosoftAccountAsync(authentication_token_value);

Pour obtenir un exemple d'utilisation de compte Microsoft pour fournir une expérience d'authentification unique, consultez le didacticiel « Authentification de votre application avec l'authentification unique » ([Windows Store](/en-us/develop/mobile/tutorials/single-sign-on-windows-8-dotnet/)/[Windows Phone](/en-us/develop/mobile/tutorials/single-sign-on-wp8/)).

### Mise en cache du jeton d'authentification

Dans certains cas, l'appel à la méthode de connexion peut être évité après la première authentification de l'utilisateur. Vous pouvez utiliser [PasswordVault](http://msdn.microsoft.com/fr-fr/library/windows/apps/windows.security.credentials.passwordvault.aspx) pour les applications Windows Store afin de mettre en cache l'identité de l'utilisateur actif lors de sa première connexion et, par la suite, à chaque fois que vous vérifiez si son identité est présente dans le cache. Lorsque le cache est vide, vous devez toujours soumettre l'utilisateur au processus de connexion.

    // Après la connexion
    PasswordVault vault = new PasswordVault();
    vault.Add(new PasswordCredential("Facebook", user.UserId, user.MobileServiceAuthenticationToken));

    // Connexion 
    var creds = vault.FindAllByResource("Facebook").FirstOrDefault();
    if (creds != null)
    {
        user = new MobileServiceUser(creds.UserName);
        user.MobileServiceAuthenticationToken = vault.Retrieve("Facebook", creds.UserName).Password;
    }
    else
    {
        // Flux de connexion habituel
        user = new MobileServiceuser( await client
            .LoginAsync(MobileServiceAuthenticationProvider.Facebook, token);
        var token = new JObject();
        // Remplacez access_token_value par la valeur réelle de votre jeton d'accès
        token.Add("access_token", "access_token_value");
    }
            
     // Déconnexion
    client.Logout();
    vault.Remove(vault.Retrieve("Facebook", user.UserId));

Dans le cas des applications Windows Phone, vous pouvez chiffrer et mettre en cache les données à l'aide de la classe [ProtectedData](http://msdn.microsoft.com/fr-fr/library/system.security.cryptography.protecteddata%28VS.95%29.aspx) et stocker les informations sensibles dans un stockage isolé.

Gestion des erreursGestion des erreurs
--------------------------------------

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
        // Ce code insère un nouvel élément TodoItem dans la base de données. Une fois que l'opération est terminée
        // et que Mobile Services a affecté un ID, l'élément est ajouté à CollectionView
        try
        {
            await todoTable.InsertAsync(todoItem);
            items.Add(todoItem);
        }
        catch (MobileServiceInvalidOperationException e)
        {
            // Traitement de l'erreur
        }
    }

Utilisation de données non typéesUtilisation de données non typées
------------------------------------------------------------------

Le client .NET a été conçu pour des scénarios fortement typés. Toutefois, une expérience plus légèrement typée peut parfois s'avérer pratique ; tel peut-être le cas lorsque, par exemple, il s'agit de traiter des objets avec un schéma ouvert. Ce scénario est mis en œuvre comme suit. Dans les requêtes, vous renoncez à LINQ pour utiliser le format wire.

    // Obtenez une référence de table non typée
    IMobileServiceTable untypedTodoTable = client.GetTable("TodoItem");           

    // Recherchez des données non typées à l'aide d'OData
    JToken untypedItems = await untypedTodoTable.ReadAsync("$filter=complete eq 0&$orderby=text");

Vous obtenez en retour des valeurs JSON que vous pouvez utiliser comme conteneur de propriétés. Pour plus d'informations sur JToken et Json.NET, consultez la page [Json.NET](http://json.codeplex.com/)

Conception de testsConception de tests unitaires
------------------------------------------------

La valeur renvoyée par `MobileServiceClient.GetTable` et les requêtes sont des interfaces. Il est donc facile de les simuler à des fins de test. Vous pouvez ainsi créer `MyMockTable : IMobileServiceTable<TodoItem>` qui implémente votre logique de test.

Personnalisation du clientPersonnalisation du client
----------------------------------------------------

### Personnalisation des en-têtes de requête

Vous pouvez joindre un en-tête personnalisé à chaque requête sortante ou modifier le code d'état des réponses. Pour ce faire, vous pouvez configurer un DelegatingHandler de la façon suivante :

    public async Task CallClientWithHandler()
    {
        MobileServiceClient client = new MobileServiceClient( 
            "AppUrl", 
            "AppKey"
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

### Personnalisation de la sérialisation

La classe [MobileServiceClient](http://msdn.microsoft.com/fr-fr/library/microsoft.windowsazure.mobileservices.mobileserviceclient.aspx) expose une propriété `SerializerSettings` de type [JsonSerializerSettings](http://james.newtonking.com/projects/json/help/?topic=html/T_Newtonsoft_Json_JsonSerializerSettings.htm)

Cette propriété vous permet de définir des propriétés Json.NET (il en existe beaucoup), notamment celle qui permet de convertir toutes les propriétés en minuscules :

    var settings = new JsonSerializerSettings();
    settings.ContractResolver = new CamelCasePropertyNamesContractResolver();
    client.SerializerSettings = settings;

Étapes suivantes
----------------

Maintenant que vous avez consulté ce guide de fonctionnement, découvrez en détail comment effectuer des tâches importantes dans Mobile Services :

-   [Prise en main de Mobile Services](/en-us/develop/mobile/tutorials/get-started)
    Découvrez les bases de l'utilisation de Mobile Services.

-   [Prise en main des données](/en-us/develop/mobile/tutorials/get-started-with-data-dotnet/)
    En savoir plus sur le stockage et l'interrogation des données à l'aide de Mobile Services.

-   [Prise en main de l'authentification](/en-us/develop/mobile/tutorials/get-started-with-users-dotnet)
    En savoir plus sur l'authentification des utilisateurs de votre application avec un fournisseur d'identité.

-   [Validation et modification des données avec des scripts](/en-us/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet)
    En savoir plus sur l'utilisation des scripts serveur dans Mobile Services pour valider et modifier les données envoyées à partir de votre application.

-   [Affinage des requêtes à la pagination](/en-us/develop/mobile/tutorials/add-paging-to-data-dotnet)
    En savoir plus sur l'utilisation de la pagination dans les requêtes pour contrôler la quantité de données traitées dans une seule requête.

-   [Autorisation des utilisateurs avec des scripts](/en-us/develop/mobile/tutorials/authorize-users-in-scripts-dotnet)
    Découvrez comment prendre la valeur d'ID utilisateur fournie par Mobile Services en fonction de l'utilisateur authentifié et l'utiliser pour filtrer les données renvoyées par Mobile Services.


