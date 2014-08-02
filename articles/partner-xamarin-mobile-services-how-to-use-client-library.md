<properties linkid="mobile-services-how-to-xamarin-client" urlDisplayName="Xamarin" pageTitle="How to use the Xamarin Component client - Azure Mobile Services feature guide" metaKeywords="Azure Mobile Services, Xamarin, iOS, Android, .NET client" description="Learn how to use the Xamarin Component client for Azure Mobile Services." metaCanonical="" disqusComments="1" umbracoNaviHide="0" title="How to use the Xamarin Component client for Azure Mobile Services" authors="" />

Utilisation du client Xamarin Component pour Azure Mobile Services
==================================================================

[.NET Framework](/en-us/develop/mobile/how-to-guides/work-with-net-client-library/ ".NET Framework") [HTML/JavaScript](/en-us/develop/mobile/how-to-guides/work-with-html-js-client/ "HTML/JavaScript")[iOS](/en-us/develop/mobile/how-to-guides/work-with-ios-client-library/ "iOS")[Android](/en-us/develop/mobile/how-to-guides/work-with-android-client-library/ "Android")[Xamarin](/en-us/develop/mobile/how-to-guides/work-with-xamarin-client-library/ "Xamarin")

Ce guide vous montre comment exécuter les scénarios courants à l'aide du client Xamarin Component pour Azure Mobile Services, dans les applications Xamarin pour iOS et Android. Les scénarios traités incluent l'interrogation des données, l'insertion, la mise à jour et la suppression des données, l'authentification des utilisateurs et la gestion des erreurs. Si vous découvrez Mobile Services, pensez d'abord à consulter le didacticiel « Prise en main de Mobile Services » ([Xamarin.iOS](/en-us/develop/mobile/tutorials/get-started-xamarin-ios/)/[Xamarin.Android](/en-us/develop/mobile/tutorials/get-started-xamarin-android/)), puis le didacticiel « Prise en main des données dans .NET » ([Xamarin.iOS](/en-us/develop/mobile/tutorials/get-started-with-data-xamarin-ios/)/[Xamarin.Android](/en-us/develop/mobile/tutorials/get-started-with-data-xamarin-android/)). Le didacticiel de démarrage rapide, qui nécessite [Xamarin](http://xamarin.com/download/) et le [Kit de développement logiciel (SDK) Mobile Services](http://nuget.org/packages/WindowsAzure.MobileServices/), vous aide à configurer votre compte et à créer votre premier service mobile.

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
-   [Modification des données d'un service mobile](#modifying)
-   [Suppression des données d'un service mobile](#deleting)
-   [Authentification des utilisateurs](#authentication)
-   [Gestion des erreurs](#errors)
-   [Utilisation de données non typées](#untyped)
-   [Conception de tests](#unit-testing)
-   [Étapes suivantes](#nextsteps)

[WACOM.INCLUDE [mobile-services-concepts](../includes/mobile-services-concepts.md)]

ConfigurationConfiguration et conditions préalables
---------------------------------------------------

Nous partons du principe que vous avez créé un service mobile et une table. Pour plus d'informations, consultez la page [Créer une table](http://go.microsoft.com/fwlink/?LinkId=298592). Dans le code utilisé dans cette rubrique, la table s'intitule `TodoItem` et contient les colonnes suivantes : `id`, `Text` et `Complete`.

Le type .NET côté client typé correspondant est le suivant :

    public class TodoItem
    {
        public string id { get; set; }

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

L'ensemble du code qui permet d'accéder aux données de la table Mobile Services ou de les modifier appelle des fonctions sur l'objet `MobileServiceTable`. Pour obtenir une référence à la table, appelez la fonction [GetTable](http://msdn.microsoft.com/en-us/library/windowsazure/jj554275.aspx) sur une instance du `MobileServiceClient`.

    IMobileServiceTable<TodoItem> todoTable = 
        client.GetTable<TodoItem>();

Il s'agit du modèle de sérialisation typé ; consultez la section traitant du [modèle de sérialisation non typé](#untyped) ci-après.

Interrogation des données Interrogation des données à partir d'un service mobile
--------------------------------------------------------------------------------

Cette section explique comment émettre des requêtes à destination du service mobile. Les sous-sections décrivent différents aspects, tels que le tri, le filtrage et la pagination.

### Filtrage des données renvoyées

Le code suivant montre comment filtrer des données en incluant une clause `Where` dans une requête. Il renvoie tous les éléments de `todoTable` dont la propriété `Complete` est égale à `false`. La fonction `Where` applique un prédicat de filtrage de ligne à la requête au niveau de la table.

    // Cette requête élimine les éléments TodoItems terminés et 
    // les éléments sans horodatage. 
    List<TodoItem> items = await todoTable
       .Where(todoItem => todoItem.Complete == false)
       .ToListAsync();

Vous pouvez afficher l'URI de la requête envoyée au service mobile en utilisant un logiciel d'inspection des messages, tel que les outils destinés aux développeurs de navigateurs ou Fiddler. Si vous examinez l'URI de requête ci-dessous, vous remarquerez que la chaîne de requête elle-même est modifiée :

    GET /tables/todoitem$filter=(complete+eq+false) HTTP/1.1                   

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

Les deux méthodes sont équivalentes et peuvent être utilisées de manière interchangeable. La dernière option, qui consiste à concaténer plusieurs prédicats dans une même requête, est plus compacte. C'est celle que nous recommandons.

La clause `where` prend en charge les opérations traduites dans le sous-ensemble OData Mobile Services. Cela inclut les opérateurs relationnels (==, !=, &lt;, &lt;=, \>, \>=), les opérateurs arithmétiques (+, -, /, \*, %), la précision des nombres (Math.Floor, Math.Ceiling), les fonctions de chaîne (Length, Substring, Replace, IndexOf, StartsWith, EndsWith), les propriétés de date (Year, Month, Day, Hour, Minute, Second), les propriétés d'accès d'un objet, ainsi que les expressions qui combinent tous ces éléments.

### Tri des données renvoyées

Le code suivant montre comment trier les données en incluant une fonction `OrderBy` ou `OrderByDescending` dans la requête. Il renvoie des éléments de `todoTable`, triés dans l'ordre croissant sur le champ `Text`. Par défaut, le serveur renvoie uniquement les 50 premiers éléments.
**Remarque**

Une taille de page pilotée par un serveur est utilisée par défaut pour empêcher le renvoi de tous les éléments. Cela permet d'éviter que les requêtes par défaut associées à des jeux de données volumineux aient un impact négatif sur le service.

Vous pouvez augmenter le nombre d'éléments à renvoyer en appelant `Take`, comme décrit dans la section qui suit.

    // Triez les éléments par ordre croissant sur le champ Text
    MobileServiceTableQuery<TodoItem> query = todoTable
                    .OrderBy(todoItem => todoItem.Text)       
    List items = await query.ToListAsync();

    // Triez les éléments par ordre décroissant sur le champ Text
    MobileServiceTableQuery<TodoItem> query = todoTable
                    .OrderByDescending(todoItem => todoItem.Text)       
    List items = await query.ToListAsync();

### Renvoi de données dans les pages

Le code suivant montre comment implémenter la pagination des données renvoyées à l'aide des clauses `take` et `skip` dans la requête. Lorsqu'elle est exécutée, la requête suivante renvoie les trois premiers éléments de la table.

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

Vous pouvez également utiliser la méthode [IncludeTotalCount](http://msdn.microsoft.com/en-us/library/windowsazure/jj730933.aspx) pour faire en sorte que la requête obtienne le nombre total de *tous* les enregistrements qui auraient été renvoyés, en ignorant toute clause de pagination/limite spécifiée :

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
                    .Select(todoItem => string.Format("{0} -- {1}", todoItem.Text.PadRight(30), todoItem.Complete 
                     "Now complete!" : "Incomplete!"));
    List<string> items = await query.ToListAsync();

Toutes les fonctions décrites jusqu'ici étant cumulatives, nous pouvons continuer de les appeler, ce qui aura à chaque fois des répercussions sur la requête. Un exemple supplémentaire :

    MobileServiceTableQuery<TodoItem> query = todoTable
                    .Where(todoItem => todoItem.Complete == false)
                    .Select(todoItem => todoItem.Text)
                    .Skip(3).
                    .Take(3);
    List<string> items = await query.ToListAsync();

### Recherche des données par ID

La fonction `LookupAsync` permet de rechercher des objets dans la base de données à partir d'un ID particulier.

    // Cette requête filtre l'élément associé à l'ID 25
    TodoItem item25 = await todoTable.LookupAsync(25);

Insertion de données Insertion de données dans un service mobile
----------------------------------------------------------------

**Remarque**

Si vous souhaitez effectuer des opérations d'insertion, de recherche, de suppression ou de mise à jour sur un type, vous devez créer un membre appelé **Id** (quelle que soit la classe). C'est pourquoi l'exemple de classe **TodoItem** contient un membre nommé **Id**. La valeur de l'ID ne doit pas être définie sur une autre valeur que celle par défaut pendant les opérations d'insertion. En revanche, la valeur d'ID doit toujours être présente et être définie sur une valeur différente de celle par défaut dans les opérations de mise à jour et de suppression.

Le code suivant montre comment insérer de nouvelles lignes dans une table. Le paramètre contient les données à insérer sous forme d'objet .NET.

    await todoTable.InsertAsync(todoItem);

Après le retour de l'appel `todoTable.InsertAsync`, l'ID de l'objet sur le serveur est inséré dans l'objet `todoItem` dans le client.

Pour insérer des données non typées, vous pouvez tirer parti de Json.NET comme indiqué ci-dessous. De même, notez que l'ID ne doit pas être spécifié lors de l'insertion d'un objet.

    JObject jo = new JObject(); 
    jo.Add("Text", "Hello World"); 
    jo.Add("Complete", false);
    var inserted = await table.InsertAsync(jo);

Si vous essayez d'insérer un élément sans que le champ « Id » ne soit déjà défini, le service vous renvoie une exception `MobileServiceInvalidOperationException`.

Modification des données Modification des données d'un service mobile
---------------------------------------------------------------------

Le code suivant montre comment mettre à jour une instance existante avec le même ID avec des informations nouvelles. Le paramètre contient les données à mettre à jour sous forme d'objet .NET.

    await todoTable.UpdateAsync(todoItem);

Pour insérer des données non typées, vous pouvez tirer parti de Json.NET de cette façon. Notez que pour effectuer une mise à jour, il est nécessaire de spécifier un ID, car c'est ainsi que le service mobile identifie l'instance à mettre à jour. L'ID peut être obtenu à partir du résultat de l'appel `InsertAsync`.

    JObject jo = new JObject(); 
    jo.Add("Id", 52);
    jo.Add("Text", "Hello World"); 
    jo.Add("Complete", false);
    var inserted = await table.UpdateAsync(jo);

Si vous essayez de mettre à jour un élément sans que le champ « Id » ne soit déjà défini, le service n'a aucun moyen de déterminer quelle instance mettre à jour. Dans ce cas, une exception `MobileServiceInvalidOperationException` vous est renvoyée par le service. De même, si vous essayez de mettre à jour un élément non typé sans que le champ « Id » ne soit déjà défini, le service vous renvoie à nouveau une exception `MobileServiceInvalidOperationException`.

Suppression de données Suppression des données d'un service mobile
------------------------------------------------------------------

Le code suivant montre comment supprimer une instance existante. L'instance est identifiée par le champ « Id » défini au niveau de `todoItem`.

    await todoTable.DeleteAsync(todoItem);

Pour supprimer des données non typées, vous pouvez tirer parti de Json.NET de cette façon. Notez que pour effectuer une demande de suppression, il est nécessaire de spécifier un ID, car c'est ainsi que le service mobile identifie l'instance à supprimer. Une demande de suppression n'a besoin que de l'ID ; les autres propriétés ne sont pas transmises au service, et si c'est le cas, celui-ci n'en tient pas compte. De même, le résultat d'un appel `DeleteAsync` a généralement la valeur `null`. L'ID à transmettre peut être obtenu à partir du résultat de l'appel `InsertAsync`.

    JObject jo = new JObject(); 
    jo.Add("Id", 52);
    await table.DeleteAsync(jo);

Si vous essayez de supprimer un élément sans que le champ « Id » ne soit déjà défini, le service n'a aucun moyen de déterminer quelle instance supprimer. Dans ce cas, une exception `MobileServiceInvalidOperationException` vous est renvoyée par le service. De la même manière, si vous essayez de supprimer un élément non typé sans que le champ « Id » ne soit déjà défini, le service vous renvoie à nouveau une exception `MobileServiceInvalidOperationException`.

Authentification Authentification des utilisateurs
--------------------------------------------------

Mobile Services permet aux utilisateurs d'applications de s'authentifier par l'intermédiaire de divers fournisseurs d'identité externes : Facebook, Google, Compte Microsoft, Twitter et Azure Active Directory. Vous pouvez définir des autorisations sur les tables pour limiter l'accès à certaines opérations aux seuls utilisateurs authentifiés. Vous pouvez également utiliser l'identité des utilisateurs authentifiés pour implémenter des règles d'autorisation dans les scripts serveur. Pour plus d'informations, consultez le didacticiel « Prise en main de l'authentification » ([Xamarin.iOS](/en-us/develop/mobile/tutorials/get-started-with-users-xamarin-ios/)/[Xamarin.Android](/en-us/develop/mobile/tutorials/get-started-with-users-xamarin-android/)).

Deux flux d'authentification sont pris en charge : un *flux serveur* et un *flux client*. Le flux serveur fournit l'authentification la plus simple, car il repose sur l'interface d'authentification Web du fournisseur. En revanche, le flux client est celui qui s'intègre le plus profondément aux fonctionnalités propres à l'appareil, car il s'appuie sur les Kits de développement logiciel (SDK) propres au fournisseur et à l'appareil.

### Flux serveur

Pour que Mobile Services puisse gérer le processus d'authentification dans votre application Windows Store ou Windows Phone, vous devez inscrire votre application auprès de votre fournisseur d'identité. Ensuite, dans votre service mobile, vous devez configurer l'ID d'application et le secret fournis par votre fournisseur. Pour plus d'informations, consultez le didacticiel « Prise en main de l'authentification » ([Xamarin.iOS](/en-us/develop/mobile/tutorials/get-started-with-users-xamarin-ios/)/[Xamarin.Android](/en-us/develop/mobile/tutorials/get-started-with-users-xamarin-android/)).

Une fois que vous avez inscrit votre fournisseur d'identité, il vous suffit d'appeler la [méthode LoginAsync](http://msdn.microsoft.com/en-us/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceclientextensions.loginasync.aspx) avec la valeur [MobileServiceAuthenticationProvider](http://msdn.microsoft.com/en-us/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceauthenticationprovider.aspx) de votre fournisseur. Par exemple, le code suivant initie une connexion de flux serveur via Facebook.

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

Si vous utilisez un fournisseur d'identité différent de Facebook, remplacez la valeur de [MobileServiceAuthenticationProvider](http://msdn.microsoft.com/en-us/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceauthenticationprovider.aspx) ci-dessus par la valeur de votre fournisseur.

Dans ce cas, Mobile Services gère le flux d'authentification OAuth 2.0 en affichant la page de connexion du fournisseur sélectionné et en générant un jeton d'authentification Mobile Services après avoir établi une connexion avec le fournisseur d'identité. La [méthode LoginAsync](http://msdn.microsoft.com/en-us/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceclientextensions.loginasync.aspx) renvoie un [MobileServiceUser](http://msdn.microsoft.com/en-us/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.aspx), qui fournit à la fois l'[userId](http://msdn.microsoft.com/en-us/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.userid.aspx) de l'utilisateur authentifié et le [MobileServiceAuthenticationToken](http://msdn.microsoft.com/en-us/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.mobileserviceauthenticationtoken.aspx), sous la forme d'un jeton Web JSON (JWT). Ce jeton peut être mis en cache et réutilisé jusqu'à ce qu'il arrive à expiration. Pour plus d'informations, consultez la section [Mise en cache du jeton d'authentification](#caching).

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

### Mise en cache du jeton d'authentification

Dans certains cas, l'appel à la méthode de connexion peut être évité après la première authentification de l'utilisateur. Vous pouvez utiliser un magasin local sécurisé (par exemple [Xamarin.Auth](https://components.xamarin.com/view/xamarin.auth)) afin de mettre en cache l'identité de l'utilisateur actif lors de sa première connexion et, par la suite, à chaque fois que vous vérifiez si son identité est présente dans le cache. Lorsque le cache est vide, vous devez toujours soumettre l'utilisateur au processus de connexion.

    using Xamarin.Auth;
    var accountStore = AccountStore.Create(); // Xamarin.iOS
    //var accountStore = AccountStore.Create(this); // Xamarin.Android

    // Après connexion
    var account = new Account (user.UserId, new Dictionary<string,string> {{"token",user.MobileServiceAuthenticationToken}});
    accountStore.Save(account, "Facebook");

    // Connexion 
    var accounts = accountStore.FindAccountsForService ("Facebook").ToArray();
    if (accounts.Count != 0)
    {
        user = new MobileServiceUser (accounts[0].Username);
        user.MobileServiceAuthenticationToken = accounts[0].Properties["token"];
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
    accountStore.Delete(account, "Facebook");

Gestion des erreurs Gestion des erreurs
---------------------------------------

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

Utilisation de données non typées Utilisation de données non typées
-------------------------------------------------------------------

Le client Xamarin Component a été conçu pour des scénarios fortement typés. Toutefois, une expérience plus légèrement typée peut parfois s'avérer pratique ; tel peut-être le cas lorsque, par exemple, il s'agit de traiter des objets avec un schéma ouvert. Ce scénario est mis en œuvre comme suit. Dans les requêtes, vous renoncez à LINQ pour utiliser le format wire.

    // Obtenez une référence de table non typée
    IMobileServiceTable untypedTodoTable = client.GetTable("TodoItem");           

    // Recherchez des données non typées à l'aide d'OData
    JToken untypedItems = await untypedTodoTable.ReadAsync("$filter=complete eq 0&$orderby=text");

Vous obtenez en retour des valeurs JSON que vous pouvez utiliser comme conteneur de propriétés. Pour plus d'informations sur JToken et Json.NET, consultez la page [Json.NET](http://json.codeplex.com/)

Conception de tests Conception de tests
---------------------------------------

La valeur renvoyée par `MobileServiceClient.GetTable` et les requêtes sont des interfaces. Il est donc facile de les simuler à des fins de test. Vous pouvez ainsi créer `MyMockTable: IMobileServiceTable<TodoItem>` qui implémente votre logique de test.

Étapes suivantes
----------------

Maintenant que vous avez consulté ce guide de fonctionnement, découvrez en détail comment effectuer des tâches importantes dans Mobile Services :

-   Prise en main de Mobile Services ([Xamarin.iOS](/en-us/develop/mobile/tutorials/get-started-xamarin-ios)/[Xamarin.Android](/en-us/develop/mobile/tutorials/get-started-xamarin-android))
    <br/>Découvrez les bases de l'utilisation de Mobile Services.

-   Prise en main des données ([Xamarin.iOS](/en-us/develop/mobile/tutorials/get-started-with-data-xamarin-ios)/[Xamarin.Android](/en-us/develop/mobile/tutorials/get-started-with-data-xamarin-android))
    <br/>En savoir plus sur le stockage et l'interrogation des données à l'aide de Mobile Services.

-   Prise en main de l'authentification ([Xamarin.iOS](/en-us/develop/mobile/tutorials/get-started-with-users-xamarin-ios)/[Xamarin.Android](/en-us/develop/mobile/tutorials/get-started-with-users-xamarin-android))
    <br/>En savoir plus sur l'authentification des utilisateurs de votre application avec un fournisseur d'identité.

-   Validation et modification des données avec des scripts ([Xamarin.iOS](/en-us/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-ios)/[Xamarin.Android](/en-us/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-android))
    <br/>En savoir plus sur l'utilisation des scripts serveur dans Mobile Services pour valider et modifier les données envoyées à partir de votre application.

-   Affinage des requêtes à la pagination ([Xamarin.iOS](/en-us/develop/mobile/tutorials/add-paging-to-data-xamarin-ios)/[Xamarin.Android](/en-us/develop/mobile/tutorials/add-paging-to-data-xamarin-android))
    <br/>En savoir plus sur l'utilisation de la pagination dans les requêtes pour contrôler la quantité de données traitées dans une seule requête.

-   Autorisation des utilisateurs avec des scripts ([Xamarin.iOS](/en-us/develop/mobile/tutorials/authorize-users-in-scripts-xamarin-ios)/[Xamarin.Android](/en-us/develop/mobile/tutorials/authorize-users-in-scripts-xamarin-android))
    <br/>Découvrez comment prendre la valeur d'ID utilisateur fournie par Mobile Services en fonction de l'utilisateur authentifié et l'utiliser pour filtrer les données renvoyées par Mobile Services.


