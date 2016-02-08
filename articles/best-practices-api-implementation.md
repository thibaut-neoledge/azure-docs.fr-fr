<properties
   pageTitle="Recommandations relatives à l’implémentation de l’API | Microsoft Azure"
   description="Conseils sur l’implémentation d’une API."
   services=""
   documentationCenter="na"
   authors="dragon119"
   manager="masimms"
   editor=""
   tags=""/>

<tags
   ms.service="best-practice"
   ms.devlang="rest-api"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="12/17/2015"
   ms.author="masashin"/>

# Recommandations relatives à l’implémentation de l’API

![](media/best-practices-api-implementation/pnp-logo.png)


Certaines rubriques de ce guide font l’objet de discussions et pourront être modifiées. N’hésitez pas à nous faire part de vos commentaires.

## Vue d’ensemble
Une API Web RESTful soigneusement développée définit les ressources, les relations et les schémas de navigation auxquels ont accès les applications clientes. Lorsque vous implémentez et déployez une API Web, vous devez tenir compte des exigences physiques de l’environnement hébergeant l’API Web et de la configuration de l’API Web, non de la structure logique des données. Ce guide est axé sur les meilleures pratiques relatives à l’implémentation d’une API Web et sa publication dans l’objectif de la rendre disponible aux applications clientes. Les problématiques de sécurité sont évoquées séparément dans le document sur les recommandations en matière de sécurité de l’API. Vous trouverez des informations détaillées sur la conception de l’API Web dans le document sur les recommandations en matière de conception de l’API.

## Considérations relatives à l’implémentation d’une API Web RESTful
Les sections suivantes font état des meilleures pratiques relatives à l’utilisation d’un modèle d’API Web ASP.NET pour créer une API Web RESTful. Pour en savoir plus sur l’utilisation d’un modèle d’API Web, consultez la page [En savoir plus sur l’API Web ASP.NET](http://www.asp.net/web-api) du site Web Microsoft.

## Considérations relatives à l’implémentation du routage des requêtes

Dans un service implémenté à l’aide de l’API Web ASP.NET, chaque requête est routée vers une méthode de classe _contrôleur_. L’infrastructure d’API Web fournit deux options principales d’implémentation du routage : le routage _basé sur des conventions_ et le routage _basé sur des attributs_. Au moment d’identifier la méthode appropriée de routage des requêtes dans votre API Web, tenez compte des points suivants :

- **Maîtrisez les restrictions et les exigences associées au routage basé sur des conventions**.

	Par défaut, l’infrastructure d’API Web utilise le routage basé sur des conventions. L’infrastructure d’API Web crée une table de routage initiale qui contient l’entrée suivante :

	```C#
	config.Routes.MapHttpRoute(
  		name: "DefaultApi",
	  	routeTemplate: "api/{controller}/{id}",
	  	defaults: new { id = RouteParameter.Optional }
	);
	```

	Les itinéraires peuvent être génériques et comporter des littéraux comme _api_ et des variables comme _{controller}_ et _{id}_. Avec le routage basé sur des conventions, certains éléments de l’itinéraire peuvent être facultatifs. L’infrastructure d’API Web détermine la méthode à appeler dans le contrôleur en faisant correspondre la méthode HTTP de la requête à la portion initiale du nom de méthode dans l’API, puis aux éventuels paramètres facultatifs. Par exemple, si un contrôleur nommé _orders_ comporte les méthodes _GetAllOrders()_ ou _GetOrderByInt(int id)_, la requête GET \__http://www.adventure-works.com/api/orders/_ sera dirigée vers la méthode _GetAlllOrders()_ et la requête GET \__http://www.adventure-works.com/api/orders/99_ sera dirigée vers la méthode _GetOrderByInt(int id)_. S’il n’existe aucune méthode correspondante disponible qui présente le préfixe Get dans le contrôleur, l’infrastructure d’API Web répond avec un message HTTP 405 (méthode non autorisée). Par ailleurs, le nom du paramètre (id) spécifié dans la table de routage doit être identique au nom du paramètre de la méthode _GetOrderById_. Si ce n’est pas le cas, l’infrastructure d’API Web répond avec un message HTTP 404 (Non trouvé).

	Les mêmes règles s’appliquent aux requêtes POST, PUT et DELETE HTTP. Une requête PUT qui met à jour les détails d’une commande 101 sera dirigée vers l’URI \__http://www.adventure-works.com/api/orders/101_, le corps du message comprendra les nouveaux détails de la commande et ces informations seront transmises en tant que paramètre à une méthode du contrôleur des commandes sous un nom commençant par le préfixe _Put_, comme _PutOrder_.

	La table de routage par défaut ne fera pas correspondre une requête qui référence les ressources enfants dans une API Web RESTful, comme \__http://www.adventure-works.com/api/customers/1/orders_ (recherchez les détails de l’ensemble des commandes transmises par le client 1). Pour traiter ces cas, vous pouvez ajouter des itinéraires personnalisés à la table de routage :

	```C#
	config.Routes.MapHttpRoute(
	    name: "CustomerOrdersRoute",
	    routeTemplate: "api/customers/{custId}/orders",
	    defaults: new { controller="Customers", action="GetOrdersForCustomer" })
	);
	```

	Cet itinéraire dirige les requêtes correspondant à l’URI vers la méthode _GetOrdersForCustomer_ dans le contrôleur _Customers_. Cette méthode doit accepter un paramètre unique nommé _custI_ :

	```C#
	public class CustomersController : ApiController
	{
	    ...
	    public IEnumerable<Order> GetOrdersForCustomer(int custId)
	    {
	        // Find orders for the specified customer
	        var orders = ...
	        return orders;
	    }
	    ...
	}
	```

	> [AZURE.TIP] Dans la mesure du possible, utilisez le routage par défaut et évitez de définir de nombreux itinéraires personnalisés complexes, afin d’éviter de fragiliser le système (il est très facile d’ajouter des méthodes générant des itinéraires ambigus à un contrôleur) et de réduire les performances (plus la table de routage est importante, plus l’infrastructure d’API Web rencontre des difficultés à identifier l’itinéraire correspondant à une URI donnée). Utilisez une API et des itinéraires simples. Pour en savoir plus, consultez la section Organisation de l’API Web autour des ressources du document sur les recommandations en matière de conception de l’API. Si vous devez définir des itinéraires personnalisés, il est préférable de mettre à profit le routage basé sur les attributs, décrit plus loin dans cette section.

	Pour en savoir plus sur le routage basé sur des conventions, consultez la page [Routage dans l’API Web ASP.NET](http://www.asp.net/web-api/overview/web-api-routing-and-actions/routing-in-aspnet-web-api) du site Web Microsoft.

- **Éliminez toute ambiguïté dans le routage**.

	Le routage basé sur des conventions peut provoquer des ambiguïtés d’acheminement si plusieurs méthodes du contrôleur correspondent au même itinéraire. Dans ces situations, l’infrastructure de l’API Web transmet une réponse HTTP 500 (erreur de serveur interne) contenant un message vous informant que plusieurs actions correspondant à la requête ont été identifiées.

- **Préférez le routage basé sur des attributs**.

	Le routage basé sur des attributs est un autre moyen de lier des itinéraires à des méthodes dans un contrôleur. Au lieu de vous appuyer sur les fonctions de correspondance de modèle du routage basé sur des conventions, vous pouvez annoter de manière explicite les méthodes d’un contrôleur avec les détails de l’itinéraire auquel elles doivent être associées. Cette approche permet d’éliminer les éventuelles ambiguïtés. Par ailleurs, dans la mesure où les routes explicites sont définies lors de la conception, cette approche est plus efficace que le routage basé sur des conventions lors de l’exécution. Le code suivant vous indique comment appliquer l’attribut _Route_ aux méthodes dans le contrôleur Customers. Ces méthodes utilisent également l’attribut HttpGet afin d’indiquer qu’elles doivent répondre aux requêtes _HTTP GET_. Cet attribut vous permet de nommer vos méthodes à l’aide du schéma de désignation de votre choix, non du routage traditionnel basé sur des conventions. Vous pouvez également annoter des méthodes avec les attributs _HttpPost_, _HttpPut_ et _HttpDelete_ afin qu’elles répondent à d’autres types de requêtes HTTP.

	```C#
	public class CustomersController : ApiController
	{
	    ...
	    [Route("api/customers/{id}")]
	    [HttpGet]
	    public Customer FindCustomerByID(int id)
	    {
	        // Find the matching customer
	        var customer = ...
	        return customer;
	    }
	    ...
	    [Route("api/customers/{id}/orders")]
	    [HttpGet]
	    public IEnumerable<Order> FindOrdersForCustomer(int id)
	    {
	        // Find orders for the specified customer
	        var orders = ...
	        return orders;
	    }
	    ...
	}
	```

	Le routage basé sur des attributs présente également l’avantage de servir de documentation, que les développeurs mettent à profit pour conserver le code pour référence ultérieure. L’appartenance des méthodes aux différents itinéraires est immédiatement limpide, et l’attribut _HttpGet_ indique le type de requête HTTP à laquelle répond la méthode.

	Grâce au routage basé sur des attributs, vous pouvez définir des contraintes qui encadrent le mode de mise en correspondance des paramètres. Les contraintes peuvent spécifier le type du paramètre, et dans certains cas, elles indiquent également la plage acceptable des valeurs de paramètres. Dans l’exemple suivant, le paramètre ID associé à la méthode _FindCustomerByID_ doit être un entier non négatif. Si une application soumet une requête HTTP GET avec un numéro de client négatif, l’infrastructure d’API Web répondra par un message de statut HTTP 405 (Méthode non autorisée) :

	```C#
	public class CustomersController : ApiController
	{
	    ...
	    [Route("api/customers/{id:int:min(0)}")]
	    [HttpGet]
	    public Customer FindCustomerByID(int id)
	    {
	        // Find the matching customer
	        var customer = ...
	        return customer;
	    }
	    ...
	}
	```

	Pour plus d’informations sur le routage basé sur les attributs, consultez la page [Routage des attributs dans l’API Web 2](http://www.asp.net/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2) du site Web Microsoft.

- **Prenez en charge les caractères Unicode dans les itinéraires**.

	Les clés utilisées pour identifier les ressources dans les requêtes GET peuvent être des chaînes. Par conséquent, dans une application globale, il vous faudra éventuellement prendre en charge les URI qui comportent des caractères non anglais.

- **Différenciez les méthodes qui ne doivent pas être routées**.

	Si vous utilisez le routage basé sur des conventions, indiquez des méthodes qui ne correspondent pas à des actions HTTP en leur appliquant l’attribut _NonAction_. Cela s’applique généralement aux méthodes d’assistance destinées à être utilisées par d’autres méthodes au sein d’un contrôleur. Cet attribut empêchera la mise en correspondance des méthodes et leur invocation par une requête HTTP errante.

- **Évaluez les avantages et les inconvénients du placement de l’API dans un sous-domaine**.

	Par défaut, l’API Web ASP.NET organise les API dans le répertoire _/api_ d’un domaine, comme \__http://www.adventure-works.com/api/orders_. Ce répertoire se trouve dans le domaine qui héberge les services exposés par le même hôte. Il peut être avantageux de répartir l’API Web dans son propre sous-domaine exécuté sur un hôte séparé, avec des URI comme \__http://api.adventure-works.com/orders_. Cette séparation vous permet de partitionner et de mettre à l’échelle plus efficacement l’API Web sans affecter d’autres applications Web ou services exécutés dans le même domaine _www.adventure-works.com_.

	Toutefois, en plaçant l’API Web dans un sous-domaine différent, vous vous exposez également à des problèmes de sécurité. Toute application Web ou service hébergé(e) dans le domaine _www.adventure-works.com_ qui invoque une API Web exécutée à un autre emplacement peut enfreindre la restriction de sécurité same-origin de nombreux navigateurs Web. Dans ce cas, il peut être nécessaire d’activer le partage des ressources cross-origin (CORS, cross-origin resource sharing) entre les hôtes. Pour plus d’informations, consultez le document sur les recommandations en matière de sécurité de l’API.

## Considérations relatives au traitement des requêtes

Une fois qu’une requête d’une application cliente a été routée vers une méthode d’API Web, elle doit être traitée aussi efficacement que possible. Lorsque vous implémentez le code de traitement des requêtes, tenez compte des points suivants :

- **Les actions GET, PUT, DELETE, HEAD et PATCH doivent être idempotentes**.

	Le code qui implémente ces requêtes ne doit entraîner aucun effet collatéral. Une requête réitérée sur la même ressource doit présenter un état identique. Par exemple, plusieurs requêtes DELETE dirigées vers la même URI doivent présenter le même effet, même si le code de statut HTTP des messages de réponse peut être différent (la première requête DELETE peut renvoyer le code de statut 204 (Aucun contenu), tandis qu’une requête DELETE ultérieure peut renvoyer le code de statut 404 (Non trouvé)).

> [AZURE.NOTE] L’article [Idempotency Patterns](http://blog.jonathanoliver.com/idempotency-patterns/) (Modèles d’idempotence) du blog de Jonathan Oliver propose un aperçu de l’idempotence, et lie ce concept aux opérations de gestion de données.

- **Les actions POST qui créent des ressources doivent procéder sans effets collatéraux sans rapports**.

	Si une requête POST vise à créer une ressource, les effets de la requête doivent se limiter à cette nouvelle ressource (éventuellement à des ressources associées si des liens existent). Par exemple, dans un système de commerce électronique, une requête POST qui crée une commande peut affecter les niveaux d’inventaire et générer des informations de facturation, mais ne doit aucunement modifier les informations qui ne sont pas liées directement à la commande ou qui présentent des effets collatéraux sur l’état général du système.

- **Évitez d’implémenter des opérations POST, PUT et DELETE effectuant de nombreux échanges**.

	Prenez en charge les requêtes POST, PUT et DELETE sur les collections de ressources. Une requête POST peut contenir les détails de plusieurs nouvelles ressources et les ajouter à la même collection, une requête PUT peut remplacer l’intégralité de l’ensemble de ressources dans une collection et une requête DELETE peut supprimer une collection.

	Notez que la prise en charge OData incluse dans l’API 2 Web ASP.NET permet de regrouper les requêtes. Une application cliente peut rassembler plusieurs requêtes d’API Web et les envoyer au serveur en une seule requête HTTP. Par la suite, elle reçoit une réponse HTTP unique, qui contient les informations associées à l’ensemble des requêtes. Pour en savoir plus, consultez la page [Présentation de la prise en charge groupée dans l’API Web et dans l’API Web OData](http://blogs.msdn.com/b/webdev/archive/2013/11/01/introducing-batch-support-in-web-api-and-web-api-odata.aspx) sur le site Web Microsoft.

- **Respectez le protocole HTTP lors de l’envoi d’une réponse à une application cliente**.

	Une API Web doit renvoyer des messages contenant le code de statut HTTP approprié, ce qui permet au client de décider de la manière dont il va traiter les résultats. Les messages doivent également contenir les en-têtes HTTP appropriés, de manière à ce que le client comprenne la nature des valeurs et présenter un corps mis en forme de manière à faciliter leur analyse. Si vous utilisez le modèle de l’API Web ASP.NET, la stratégie par défaut utilisée pour l’implémentation des méthodes répondant aux requêtes POST HTTP consiste en toute simplicité à renvoyer une copie de la ressource nouvellement créée, tel qu’illustré dans l’exemple suivant :

	```C#
	public class CustomersController : ApiController
	{
	    ...
	    [Route("api/customers")]
	    [HttpPost]
	    public Customer CreateNewCustomer(Customer customerDetails)
	    {
	        // Add the new customer to the repository
	        // This method returns a customer with a unique ID allocated
	        // by the repository
	        var newCust = repository.Add(customerDetails);
	        // Return the newly added customer
	        return newCust;
	    }
	    ...
	}
	```

	Si l’opération POST réussit, l’infrastructure d’API Web crée une réponse HTTP présentant le code de statut 200 (OK) ; les détails du client sont dans le corps du message. Toutefois, dans ce cas, conformément au protocole HTTP, une opération POST devrait renvoyer le code de statut 201 (Créé) et le message de réponse doit inclure l’URI de la ressource nouvellement créée dans son en-tête d’emplacement.

	Pour offrir ces fonctions, renvoyez votre propre message de réponse HTTP à l’aide de l’interface `IHttpActionResult`. Cette approche vous donne un contrôle précis sur le code d’état HTTP, les en-têtes dans le message de réponse et même le format des données dans le corps du message de réponse, comme illustré dans l’exemple de code suivant. Cette version de la méthode `CreateNewCustomer` est davantage en conformité avec les attentes d’un client suivant le protocole HTTP. La méthode `Created` de la classe `ApiController` élabore le message de réponse à partir des données spécifiées et ajoute l’en-tête d’emplacement aux résultats :

	```C#
	public class CustomersController : ApiController
	{
	    ...
	    [Route("api/customers")]
	    [HttpPost]
	    public IHttpActionResult CreateNewCustomer(Customer customerDetails)
	    {
	        // Add the new customer to the repository
	        var newCust = repository.Add(customerDetails);

	        // Create a value for the Location header to be returned in the response
	        // The URI should be the location of the customer including its ID,
	        // such as http://adventure-works.com/api/customers/99
	        var location = new Uri(...);

            // Return the HTTP 201 response,
            // including the location and the newly added customer
	        return Created(location, newCust);
	    }
	    ...
	}
	```

- **Prenez en charge la négociation de contenu**.

	Le corps d’un message de réponse peut contenir des réponses sous différents formats. Par exemple, une requête HTTP GET peut renvoyer les données au format JSON ou XML. Lorsque le client transmet une requête, il peut inclure un en-tête Accept définissant les formats de données pouvant être traités. Ces formats sont spécifiés en tant que types de média. Par exemple, un client qui transmet une requête GET qui récupère une image peut définir un en-tête Accept qui répertorie les types de média pouvant être traités par le client, comme « image/jpeg, image/gif, image/png ». Lorsque l’API Web renvoie le résultat, elle doit formater les données à l’aide d’un des types de média et définir le format dans l’en-tête du type de contenu de la réponse.

	Si le client ne définit pas d’en-tête Accept, utilisez un format par défaut pour le corps de la réponse. Par exemple, l’infrastructure de l’API Web ASP.NET est définie par défaut sur le format JSON pour les données textuelles.

	> [AZURE.NOTE] L’infrastructure d’API Web ASP.NET détecte partiellement les en-têtes Accept et les traite en fonction du type de données du corps du message de réponse. Par exemple, si le corps d’un message de réponse contient un objet CLR (common language runtime), l’API Web ASP.NET formate automatiquement la réponse au format JSON avec l’en-tête Content-Type de la réponse défini sur application/json, sauf si le client demande que les résultats soient présentés au format XML. Le cas échéant, l’API Web ASP.NET transmet une réponse au format XML et définit l’en-tête Content-Type de la réponse sur text/xml. Toutefois, il peut être nécessaire de traiter les en-têtes Accept qui spécifient explicitement des types de média différents dans le code d’implémentation d’une opération.

- **Fournissez des liens afin d’assurer la prise en charge de la navigation de type HATEOAS et la découverte des ressources**.

	Les recommandations en matière de conception de l’API expliquent comment l’approche HATEOAS permet à un client de parcourir des ressources et de les découvrir à partir d’un point de démarrage initial. Cette approche est appliquée via l’utilisation de liens contenant les URI. Lorsqu’un client transmet une requête HTTP GET pour obtenir une ressource, la réponse doit comporter l’URI permettant à une application cliente de localiser rapidement les ressources associées. Par exemple, dans une API Web qui prend en charge une solution de commerce électronique, il est possible qu’un client ait effectué plusieurs commandes. Lorsqu’une application cliente récupère les détails relatifs à un client, la réponse doit comporter les liens permettant à l’application cliente d’envoyer les requêtes HTTP GET utilisées pour récupérer ces commandes. Par ailleurs, les liens de types HATEOAS décrivent les autres opérations (POST, PUT, DELETE, etc.) prises en charge par chacune des ressources associées avec l’URI correspondante afin d’exécuter les requêtes. Cette approche est décrite plus en détail dans le document sur les recommandations en matière de conception de l’API.

	Il n’existe actuellement aucune norme qui régule l’implémentation de HATEOAS, mais l’exemple suivant illustre une approche possible. Dans cet exemple, une requête HTTP GET qui identifie les détails associés à un client renvoie une réponse qui inclut des liens HATEOAS référençant les commandes relatives à ce client :

	```HTTP
	GET http://adventure-works.com/customers/2 HTTP/1.1
	Accept: text/json
	...
	```

	```HTTP
	HTTP/1.1 200 OK
	...
	Content-Type: application/json; charset=utf-8
	...
	Content-Length: ...
	{"CustomerID":2,"CustomerName":"Bert","Links":[
	  {"rel":"self",
	   "href":"http://adventure-works.com/customers/2",
	   "action":"GET",
	   "types":["text/xml","application/json"]},
	  {"rel":"self",
	   "href":"http://adventure-works.com/customers/2",
	   "action":"PUT",
	   "types":["application/x-www-form-urlencoded"]},
	  {"rel":"self",
	   "href":"http://adventure-works.com/customers/2",
	   "action":"DELETE",
	   "types":[]},
	  {"rel":"orders",
	   "href":"http://adventure-works.com/customers/2/orders",
	   "action":"GET",
	   "types":["text/xml","application/json"]},
	  {"rel":"orders",
	   "href":"http://adventure-works.com/customers/2/orders",
	   "action":"POST",
	   "types":["application/x-www-form-urlencoded"]}
	]}
	```

	Dans cet exemple, les données du client sont représentées par la classe `Customer` représentée dans l’extrait de code suivant. Les liens HATEOAS sont conservés dans la propriété de collection `Links` :

	```C#
	public class Customer
	{
    	public int CustomerID { get; set; }
    	public string CustomerName { get; set; }
    	public List<Link> Links { get; set; }
    	...
	}

	public class Link
	{
    	public string Rel { get; set; }
    	public string Href { get; set; }
    	public string Action { get; set; }
    	public string [] Types { get; set; }
	}
	```

	L’opération HTTP GET récupère les données du client à partir de l’espace de stockage et construit un objet `Customer`, avant de remplir la collection `Links`. Le résultat est mis en forme sous forme de message de réponse JSON. Chaque lien comprend les champs suivants :

	- La relation entre l’objet renvoyé et l’objet décrit par le lien. Ici, « self » indique que le lien est une référence à l’objet (similaire à un pointeur `this` dans de nombreux langages orientés objets), et « orders » est le nom d’une collection contenant les informations sur la commande associée.

	- Le lien hypertexte (`Href`) associé à l’objet décrit par le lien, sous la forme d’une URI.

	- Le type de requête HTTP (`Action`) qui peut être envoyée à cette URI.

	- Le format des données (`Types`) qui doit être fourni dans la requête HTTP ou qui peut être renvoyé dans la réponse, en fonction du type de la requête.

	Les liens HATEOAS représentés dans l’exemple de réponse HTTP indiquent qu’une application cliente peut exécuter les opérations suivantes :

	- Une requête HTTP GET dirigée vers l’URI \__http://adventure-works.com/customers/2_ afin de récupérer (de nouveau) les détails du client. Les données peuvent être renvoyées au format XML ou JSON.

	- Une requête HTTP PUT dirigée vers l’URI \__http://adventure-works.com/customers/2_ afin de modifier les détails du client. Les nouvelles données doivent être fournies dans le message de la requête au format x-www-form-urlencoded.

	- Une requête HTTP DELETE dirigée vers l’URI \__http://adventure-works.com/customers/2_ afin de supprimer le client. La requête n’attend aucune information supplémentaire ni données renvoyées dans le corps du message de réponse.

	- Une requête HTTP GET dirigée vers l’URI \__http://adventure-works.com/customers/2/orders_ afin de rechercher l’ensemble des commandes destinées au client. Les données peuvent être renvoyées au format XML ou JSON.

	- Une requête HTTP PUT dirigée vers l’URI \__http://adventure-works.com/customers/2/orders_ afin de créer une nouvelle commande pour ce client. Les données doivent être fournies dans le message de requête, sous le format x-www-form-urlencoded.

## Considérations relatives au traitement des exceptions
Par défaut, dans l’infrastructure d’API Web ASP.NET, si une opération lance une exception non interceptée, l’infrastructure renvoie un message de réponse indiquant l’erreur HTTP 500 (Erreur de serveur interne). Dans de nombreux cas, cette approche simpliste ne s’avère pas utile isolément et complique la tâche d’identification de la cause de l’exception. Par conséquent, vous devez adopter une approche plus complète pour le traitement des exceptions, en tenant compte des points suivants :

- **Capturez les exceptions et renvoyez une réponse pertinente aux clients**.

	Le code qui implémente une opération HTTP doit offrir un traitement complet des exceptions, et non laisser les exceptions non interceptées se propager vers l’infrastructure d’API Web. Si une exception entrave l’exécution d’une opération, elle peut être transmise dans le message de réponse, mais une description pertinente de l’erreur qui a provoqué l’exception doit être jointe. L’exception doit également inclure le code de statut HTTP approprié, non pas un code 500 standard pour l’ensemble des situations. Par exemple, si une requête d’utilisateur provoque une mise à jour de base de données qui viole une contrainte (comme une tentative de suppression d’un client présentant des commandes en attente), vous devez renvoyer le code de statut 409 (Conflit) et un corps de message faisant état de la raison du conflit. Si une autre condition rend la requête irréalisable, vous pouvez renvoyer le code de statut 400 (Requête incorrecte). Vous pouvez consulter la liste exhaustive des codes de statut HTTP sur la page de [définition des codes de statut](http://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) du site Web de l’organisme W3C.

	Le code suivant montre un exemple interceptant plusieurs conditions et renvoyant une réponse appropriée.

	```C#
	[HttpDelete]
	[Route("customers/{id:int}")]
	public IHttpActionResult DeleteCustomer(int id)
	{
		try
		{
			// Find the customer to be deleted in the repository
			var customerToDelete = repository.GetCustomer(id);

			// If there is no such customer, return an error response
			// with status code 404 (Not Found)
			if (customerToDelete == null)
			{
					return NotFound();
			}

			// Remove the customer from the repository
			// The DeleteCustomer method returns true if the customer
			// was successfully deleted
			if (repository.DeleteCustomer(id))
			{
				// Return a response message with status code 204 (No Content)
				// To indicate that the operation was successful
				return StatusCode(HttpStatusCode.NoContent);
			}
			else
			{
				// Otherwise return a 400 (Bad Request) error response
				return BadRequest(Strings.CustomerNotDeleted);
			}
		}
		catch
		{
			// If an uncaught exception occurs, return an error response
			// with status code 500 (Internal Server Error)
			return InternalServerError();
		}
	}
	```

	> [AZURE.TIP] N’incluez pas des réponses dont pourrait se servir un agresseur ayant pour objectif de pénétrer dans votre API Web. Pour en savoir plus, consultez la page [Gestion des exceptions dans l’API Web ASP.NET](http://www.asp.net/web-api/overview/error-handling/exception-handling) du site Web Microsoft.

	> [AZURE.NOTE] De nombreux serveurs Web interceptent eux-mêmes les conditions avant qu’elles n’atteignent l’API Web. Par exemple, si vous configurez l’authentification d’un site Web et que l’utilisateur ne communique pas les informations d’identification appropriées, le serveur Web doit transmettre un code de statut 401 (Non autorisé). Une fois que le client a été authentifié, votre code doit effectuer ses propres vérifications afin de vérifier que le client est en mesure d’accéder à la ressource demandée. Si l’autorisation échoue, vous devez renvoyer le code de statut 403 (Interdit).

- **Gérer les exceptions de manière cohérente et consigner les informations sur les erreurs**.

	Pour gérer les exceptions de manière cohérente, envisagez d’implémenter une stratégie globale de traitement des erreurs sur l’ensemble de l’infrastructure d’API Web. Pour ce faire, commencez par créer un filtre d’exceptions qui s’exécute dès qu’un contrôleur génère une exception non traitée qui n’est pas une exception `HttpResponseException`. Cette approche est décrite sur la page [Gestion des exceptions dans l’API Web ASP.NET](http://www.asp.net/web-api/overview/error-handling/exception-handling) du site Web Microsoft.

	Toutefois, il existe plusieurs scénarios où un filtre d’exceptions n’intercepte pas une exception :

	- Les exceptions lancées à partir des constructeurs de contrôleur.

	- Les exceptions lancées à partir des gestionnaires de messages.

	- Les exceptions lancées pendant le routage.

	- Les exceptions générées lors de la sérialisation du contenu d’un message de réponse.

	Pour gérer ces cas, vous devez implémenter une approche davantage personnalisée. Vous devez également intégrer un journal d’erreurs qui consigne l’ensemble des détails des exceptions. Ce journal d’erreurs ne doit en aucun cas être rendu accessible aux clients sur le Web. L’article [Gestion globale des erreurs de l’API Web](http://www.asp.net/web-api/overview/error-handling/web-api-global-error-handling) du site Web Microsoft indique une manière d’exécuter cette tâche.

- **Différenciez les erreurs côté client et côté serveur**.

	Le protocole HTTP fait la distinction entre les erreurs qui sont provoquées par l’application cliente (codes de statut HTTP 4xx) et les erreurs qui sont causées par un dysfonctionnement du serveur (codes de statut HTTP 5xx). Veillez à respecter cette convention dans l’ensemble des messages d’erreur.

<a name="considerations-for-optimizing"></a>
## Considérations relatives à l’optimisation de l’accès aux données côté client

Au sein d’un environnement distribué, comme ceux comportant un serveur Web et des applications clientes, l’une des problématiques prioritaires est le réseau. Il peut agir comme un véritable goulot d’étranglement, en particulier si une application cliente envoie fréquemment des requêtes ou reçoit régulièrement des données. Par conséquent, vous devez veiller à réduire le volume de trafic sur le réseau. Lorsque vous implémentez le code pour récupérer et conserver les données, tenez compte des points suivants :

- **Prenez en charge la mise en cache côté client**.

	Le protocole HTTP 1.1 prend en charge la mise en cache dans les serveurs clients et intermédiaire via lesquels une requête est routée, à l’aide de l’en-tête Cache-Control. Lorsqu’une application cliente envoie une requête HTTP GET à l’API Web, la réponse peut comporter un en-tête Cache-Control. Le cas échéant, celui-ci indique si les données du corps de la réponse peuvent être mises en cache de manière sécurisée par le serveur client ou un serveur intermédiaire via lequel la requête a été routée et stipule le délai d’expiration, au-delà duquel les données seront considérées comme obsolètes. L’exemple suivant représente une requête HTTP GET et la réponse correspondante, qui comporte un en-tête Cache-Control :

	```HTTP
	GET http://adventure-works.com/orders/2 HTTP/1.1
	...
	```

	```HTTP
	HTTP/1.1 200 OK
	...
	Cache-Control: max-age=600, private
	Content-Type: text/json; charset=utf-8
	Content-Length: ...
	{"orderID":2,"productID":4,"quantity":2,"orderValue":10.00}
	```

	Dans cet exemple, l’en-tête Cache-Control indique que les données renvoyées doivent expirer après 600 secondes, conviennent à un client unique et ne doivent pas être stockées dans un cache partagé utilisé par d’autres clients (elles sont _privées_). L’en-tête Cache-Control peut spécifier la valeur _public_ plutôt que _private_, auquel cas les données sont stockées dans un cache partagé. S’il comporte la valeur _no-store_, les données ne doivent **pas** être mises en cache par le client. L’exemple de code suivant représente l’élaboration d’un en-tête Cache-Control dans un message de réponse :

	```C#
	public class OrdersController : ApiController
	{
    	...
    	[Route("api/orders/{id:int:min(0)}")]
    	[HttpGet]
    	public IHttpActionResult FindOrderByID(int id)
    	{
    		// Find the matching order
    		Order order = ...;
    		...
    		// Create a Cache-Control header for the response
    		var cacheControlHeader = new CacheControlHeaderValue();
    		cacheControlHeader.Private = true;
    		cacheControlHeader.MaxAge = new TimeSpan(0, 10, 0);
    		...

    		// Return a response message containing the order and the cache control header
    		OkResultWithCaching<Order> response = new OkResultWithCaching<Order>(order, this)
    		{
    			CacheControlHeader = cacheControlHeader
    		};
    		return response;
    	}
    	...
	}
	```

	Ce code utilise une classe `IHttpActionResult` personnalisée appelée `OkResultWithCaching`. Cette classe permet au contrôleur de définir le contenu de l’en-tête de cache :

	```C#
	public class OkResultWithCaching<T> : OkNegotiatedContentResult<T>
    {
        public OkResultWithCaching(T content, ApiController controller)
            : base(content, controller) { }

        public OkResultWithCaching(T content, IContentNegotiator contentNegotiator, HttpRequestMessage request, IEnumerable<MediaTypeFormatter> formatters)
            : base(content, contentNegotiator, request, formatters) { }

        public CacheControlHeaderValue CacheControlHeader { get; set; }
        public EntityTagHeaderValue ETag { get; set; }

        public override async Task<HttpResponseMessage> ExecuteAsync(CancellationToken cancellationToken)
        {
            HttpResponseMessage response = await base.ExecuteAsync(cancellationToken);

            response.Headers.CacheControl = this.CacheControlHeader;
            response.Headers.ETag = ETag;

            return response;
        }
    }
	```

	> [AZURE.NOTE] Le protocole HTTP définit également la directive _no-cache_ associée à l’en-tête Cache-Control. À l’appellation trompeuse, cette directive ne signifie pas « ne pas mettre en cache » mais plutôt « revalider les informations mises en cache auprès du serveur avant de les renvoyer ». Les données peuvent toujours être mises en cache, mais elles font l’objet d’un contrôle d’actualisation à chaque utilisation.

	Si elle est implémentée correctement, la gestion du cache, qui doit être prise en charge par l’application cliente ou le serveur intermédiaire, permet d’économiser de la bande passante et d’améliorer les performances en éliminant le recours à la récupération des données déjà récupérées.

	La valeur _max-age_ de l’en-tête Cache-Control est une simple indication, qui ne vous garantit pas que les données correspondantes ne seront pas modifiées durant la période spécifiée. L’API Web doit définir l’élément max-age sur une valeur appropriée, dépendante de la volatilité attendue des données. Lorsque le délai expire, le client doit supprimer l’objet du cache.

	> [AZURE.NOTE] La plupart des navigateurs Web modernes prennent en charge la mise en cache côté client, via l’ajout d’en-têtes Cache-control appropriés sur les requêtes et l’examen des en-têtes de résultats, tel que décrit. Toutefois, certains navigateurs plus anciens ne mettent pas en cache les valeurs renvoyées d’une URL comportant une chaîne de requêtes. Cela ne représente généralement pas un problème pour les applications clientes personnalisées, qui implémentent leur propre stratégie de mise en cache suivant le protocole évoqué ici.
	>
	> Quelques proxys antérieurs, qui affichent un comportement identique, peuvent ne pas mettre en cache les requêtes basées sur des URL comportant des chaînes de requêtes. Cela peut constituer un obstacle pour les applications clientes personnalisés qui se connectent à un serveur Web par l’intermédiaire d’un tel proxy.

- **Fournissez des éléments ETags afin d’optimiser le traitement des requêtes**.

	Lorsqu’une application cliente récupère un objet, le message de réponse peut également inclure un élément _ETag_ (Entity Tag, balise d’entité). Un élément ETag est une chaîne opaque qui fait référence à la version d’une ressource, et qui est modifié lors de toute modification de la ressource. Cet élément ETag doit être mis en cache avec les données par l’application cliente. L’exemple de code suivant vous explique comment ajouter un élément ETag dans la réponse à une requête HTTP GET. Ce code utilise la méthode `GetHashCode` d’un objet pour générer une valeur numérique identifiant l’objet (si nécessaire, vous pouvez écraser cette méthode et générer votre propre hachage à l’aide d’un algorithme comme MD5) :

	```C#
	public class OrdersController : ApiController
	{
    	...
    	public IHttpActionResult FindOrderByID(int id)
    	{
    		// Find the matching order
    		Order order = ...;
    		...

    		var hashedOrder = order.GetHashCode();
    		string hashedOrderEtag = String.Format(""{0}"", hashedOrder);
    		var eTag = new EntityTagHeaderValue(hashedOrderEtag);

    		// Return a response message containing the order and the cache control header
    		OkResultWithCaching<Order> response = new OkResultWithCaching<Order>(order, this)
    		{
    			...,
    			ETag = eTag
    		};
    		return response;
    	}
    	...
    }
	```

	Le message de réponse publié par l’API Web se présente comme suit :

	```HTTP
	HTTP/1.1 200 OK
	...
	Cache-Control: max-age=600, private
	Content-Type: text/json; charset=utf-8
	ETag: "2147483648"
	Content-Length: ...
	{"orderID":2,"productID":4,"quantity":2,"orderValue":10.00}
	```

	> [AZURE.TIP] Pour des raisons de sécurité, n’autorisez pas la mise en cache des données sensibles ou des données renvoyées via une connexion authentifiée (HTTPS).

	Une application cliente peut émettre une requête GET par la suite, afin de récupérer cette ressource à tout moment. Si la ressource a été modifiée (élément ETag différent), la version mise en cache doit être ignorée et la nouvelle version doit être ajoutée au cache. Si une ressource est volumineuse et que sa transmission vers le client nécessite une bande passante importante, les requêtes réitérées destinées à récupérer les mêmes données peuvent devenir inefficaces. Pour remédier à ce problème, le protocole HTTP définit le processus suivant dédié à l’optimisation des requêtes GET que vous devez prendre en charge dans une API Web :

	- Le client élabore une requête GET contenant l’élément ETag associé à la version actuellement mise en cache de la ressource référencée dans un en-tête HTTP If-None-Match :

	```HTTP
	GET http://adventure-works.com/orders/2 HTTP/1.1
	If-None-Match: "2147483648"
	...
	```

	- L’opération GET de l’API Web récupère l’élément ETag actuel pour les données demandées (order 2 dans l’exemple ci-dessous) et le compare à la valeur de l’en-tête If-None-Match.

	- Si l’élément ETag actuel des données demandées correspond à l’élément ETag fourni par la requête, la ressource n’a pas été modifiée et l’API Web doit renvoyer une réponse HTTP comportant un corps de message vide et un code de statut 304 (Non modifié).

	- Si l’élément ETag actuel des données demandées ne correspond pas à l’élément ETag fourni par la requête, cela signifie que les données ont été modifiées et que l’API Web doit renvoyer une réponse HTTP comportant les nouvelles données dans le corps du message et un code de statut 200 (OK).

	- Si les données demandées n’existent plus, l’API Web doit renvoyer une réponse HTTP comportant le code de statut 404 (Non trouvé).

	- Le client utilise le code de statut pour conserver le cache. Si les données n’ont pas été modifiées (code de statut 304), l’objet peut être conservé dans le cache et l’application cliente doit continuer à utiliser cette version de l’objet. Si les données ont été modifiées (code de statut 200), l’objet mis en cache doit être retiré et le nouveau doit être inséré. Si les données ne sont plus disponibles (code de statut 404), l’objet doit être supprimé du cache.

	> [AZURE.NOTE] Si la réponse comporte l’en-tête Cache-Control et que la valeur no -store lui est attribuée, l’objet doit toujours être retiré du cache, quel que soit le code de statut HTTP.

	Le code ci-dessous représente la méthode `FindOrderByID` étendue pour prendre en charge l’en-tête If-None-Match. Si l’en-tête If-None-Match est omis, l’ordre spécifié est toujours récupéré :

	```C#
	public class OrdersController : ApiController
	{
   		...
    	[Route("api/orders/{id:int:min(0)}")]
    	[HttpGet]
    	public IHttpActionResult FindOrderById(int id)
        {
            try
            {
                // Find the matching order
    		    Order order = ...;

                // If there is no such order then return NotFound
                if (order == null)
                {
                    return NotFound();
                }

                // Generate the ETag for the order
                var hashedOrder = order.GetHashCode();
                string hashedOrderEtag = String.Format(""{0}"", hashedOrder);

                // Create the Cache-Control and ETag headers for the response
                IHttpActionResult response = null;
                var cacheControlHeader = new CacheControlHeaderValue();
                cacheControlHeader.Public = true;
                cacheControlHeader.MaxAge = new TimeSpan(0, 10, 0);
                var eTag = new EntityTagHeaderValue(hashedOrderEtag);

                // Retrieve the If-None-Match header from the request (if it exists)
                var nonMatchEtags = Request.Headers.IfNoneMatch;

                // If there is an ETag in the If-None-Match header and
                // this ETag matches that of the order just retrieved,
                // then create a Not Modified response message
                if (nonMatchEtags.Count > 0 &&
                    String.Compare(nonMatchEtags.First().Tag, hashedOrderEtag) == 0)
                {
                    response = new EmptyResultWithCaching()
                    {
                        StatusCode = HttpStatusCode.NotModified,
                        CacheControlHeader = cacheControlHeader,
                        ETag = eTag
                    };
                }
                // Otherwise create a response message that contains the order details
                else
                {
                    response = new OkResultWithCaching<Order>(order, this)
                    {
                        CacheControlHeader = cacheControlHeader,
                        ETag = eTag
                    };
                }

                return response;
            }
            catch
            {
                return InternalServerError();
            }
        }
    ...
    }
	```

	Cet exemple intègre une classe `IHttpActionResult` personnalisée supplémentaire appelée `EmptyResultWithCaching`. Cette classe est utilisée comme un wrapper autour d’un objet `HttpResponseMessage` qui ne comporte aucun corps de réponse :

	```C#
    public class EmptyResultWithCaching : IHttpActionResult
    {
        public CacheControlHeaderValue CacheControlHeader { get; set; }
        public EntityTagHeaderValue ETag { get; set; }
        public HttpStatusCode StatusCode { get; set; }
		public Uri Location { get; set; }

        public async Task<HttpResponseMessage> ExecuteAsync(CancellationToken cancellationToken)
        {
            HttpResponseMessage response = new HttpResponseMessage(StatusCode);
            response.Headers.CacheControl = this.CacheControlHeader;
            response.Headers.ETag = this.ETag;
            response.Headers.Location = this.Location;
            return response;
        }
    }
	```

	> [AZURE.TIP] Dans cet exemple, l’élément ETag associé aux données est généré en hachant les données récupérées de la couche de données sous-jacente. Si l’élément ETag peut être calculé d’une autre manière, le processus peut être davantage optimisé et les données ne doivent être récupérées de la source de données uniquement si elles ont été modifiées. Cette approche s’avère particulièrement utile si le volume de données est important ou si l’accès aux ressources est de nature à provoquer une latence considérable (par exemple, si la source de données est une base de données distante).

- **Utiliser les éléments ETag pour prendre en charge l’accès concurrentiel optimiste**.

	Pour permettre les mises à jour des données ultérieurement mises en cache, le protocole HTTP prend en charge une stratégie d’accès concurrentiel optimiste. Si, après la récupération et la mise en cache d’une ressource, l’application cliente transmet une requête PUT ou DELETE pour modifier ou supprimer la ressource, elle doit comporter un en-tête If-Match qui référence l’élément ETag. L’API Web peut ensuite utiliser ces informations afin de déterminer si la ressource a déjà été modifiée par un autre utilisateur depuis sa récupération et renvoyer une réponse appropriée à l’application cliente :

	- Le client élabore une requête PUT contenant les nouveaux détails de la ressource et de l’élément ETag associés à la version actuellement mise en cache de la ressource référencée dans un en-tête HTTP If-Match. L’exemple suivant représente une requête PUT qui met une commande à jour :

	```HTTP
	PUT http://adventure-works.com/orders/1 HTTP/1.1
	If-None-Match: "2282343857"
	Content-Type: application/x-www-form-urlencoded
	...
	Date: Fri, 12 Sep 2014 09:18:37 GMT
	Content-Length: ...
	productID=3&quantity=5&orderValue=250
	```

	- L’opération PUT de l’API Web récupère l’élément ETag actuel pour les données demandées (order 1 dans l’exemple ci-dessus) et le compare à la valeur de l’en-tête If-Match.

	- Si l’élément ETag actuel associé aux données demandées correspond à l’élément ETag fourni par la requête, la ressource n’a pas été modifiée et l’API Web doit effectuer la mise à jour. Si l’opération réussit, elle renvoie un message présentant le code de statut HTTP 204 (Aucun contenu). La réponse peut comporter les en-têtes Cache-Control et ETag pour la version mise à jour de la ressource. La réponse doit toujours inclure l’en-tête Location qui référence l’URI de la ressource nouvellement mise à jour.

	- Si l’élément ETag actuel des données demandées ne correspond pas à l’élément ETag fourni par la requête, les données ont été modifiées par un autre utilisateur depuis leur récupération et l’API Web doit renvoyer une réponse HTTP avec un corps de message vide et un code de statut HTTP 412 (Précondition échouée).

	- Si la ressource à mettre à jour n’existe plus, l’API Web doit renvoyer une réponse HTTP présentant le code de statut 404 (Non trouvé).

	- Le client utilise le code de statut et les en-têtes de la réponse pour conserver le cache. Si les données ont été mises à jour (code de statut 204), l’objet peut être conservé dans le cache (sous réserve que l’en-tête Cache-Control ne soit pas défini sur la valeur no-store), mais l’élément ETag doit être mis à jour. Si les données ont été modifiées par un autre utilisateur (code de statut 412) ou n’ont pas été trouvées (code de statut 404), l’objet mis en cache doit être ignoré.

	L’exemple de code suivant représente une implémentation de l’opération PUT associée au contrôleur Orders :

	```C#
	public class OrdersController : ApiController
	{
   		...
    	[HttpPut]
    	[Route("api/orders/{id:int}")]
    	        public IHttpActionResult UpdateExistingOrder(int id, DTOOrder order)
        {
            try
            {
                var baseUri = Constants.GetUriFromConfig();
                var orderToUpdate = this.ordersRepository.GetOrder(id);
                if (orderToUpdate == null)
                {
                    return NotFound();
                }

                var hashedOrder = orderToUpdate.GetHashCode();
                string hashedOrderEtag = String.Format(""{0}"", hashedOrder);

                // Retrieve the If-Match header from the request (if it exists)
                var matchEtags = Request.Headers.IfMatch;

                // If there is an Etag in the If-Match header and
                // this etag matches that of the order just retrieved,
                // or if there is no etag, then update the Order
                if (((matchEtags.Count > 0 &&
                     String.Compare(matchEtags.First().Tag, hashedOrderEtag) == 0)) ||
                     matchEtags.Count == 0)
                {
                    // Modify the order
                    orderToUpdate.OrderValue = order.OrderValue;
                    orderToUpdate.ProductID = order.ProductID;
                    orderToUpdate.Quantity = order.Quantity;

                    // Save the order back to the data store
                    // ...

                    // Create the No Content response with Cache-Control, ETag, and Location headers
                    var cacheControlHeader = new CacheControlHeaderValue();
                    cacheControlHeader.Private = true;
                    cacheControlHeader.MaxAge = new TimeSpan(0, 10, 0);

                    hashedOrder = order.GetHashCode();
                    hashedOrderEtag = String.Format(""{0}"", hashedOrder);
                    var eTag = new EntityTagHeaderValue(hashedOrderEtag);

                    var location = new Uri(string.Format("{0}/{1}/{2}", baseUri, Constants.ORDERS, id));
                    var response = new EmptyResultWithCaching()
                    {
                        StatusCode = HttpStatusCode.NoContent,
                        CacheControlHeader = cacheControlHeader,
                        ETag = eTag,
                        Location = location
                    };

                    return response;
                }

                // Otherwise return a Precondition Failed response
                return StatusCode(HttpStatusCode.PreconditionFailed);
            }
            catch
            {
                return InternalServerError();
            }
        }
        ...
    }
	```

	> [AZURE.TIP] L’utilisation de l’en-tête If-Match est entièrement facultative. S’il est omis, l’API Web tentera toujours de mettre à jour la commande spécifiée, en écrasant éventuellement à l’aveuglette une mise à jour effectuée par un autre utilisateur. Pour éviter les problèmes associés aux pertes de mises à jour, proposez toujours un en-tête If-Match.

<a name="considerations-for-handling-large"></a>
## Considérations relatives au traitement de requêtes et de réponses de taille importante

Il peut arriver qu’une application cliente doive émettre des requêtes qui envoient ou reçoivent des données présentant une taille de plusieurs mégaoctets (ou supérieure). Il est possible que l’application cliente, soumise au délai d’attente lié à la transmission de ce volume de données, ne réponde pas. Lorsque vous devez traiter des requêtes comportant des volumes importants de données, tenez compte des points suivants :

- **Optimisez les requêtes et les réponses impliquant des objets de taille importante**.

	Certaines ressources peuvent être de gros objets ou inclure des champs de taille importante, comme des images graphiques ou d’autres types de données binaires. Une API Web doit prendre en charge la diffusion en continu et ainsi optimiser le chargement et le téléchargement de ces ressources.

	Le protocole HTTP fournit un mécanisme de chiffrement de transfert de blocs permettant de transmettre des objets de données de taille importante au client. Lorsque le client envoie une requête HTTP GET pour un objet de taille importante, l’API Web peut renvoyer une réponse sous la forme de _blocs_ fragmentaires via une connexion HTTP. La longueur des données de la réponse peut être initialement inconnue (en cas de génération). Aussi, le serveur hébergeant l’API Web doit envoyer un message de réponse présentant l’en-tête Transfer-Encoding: Chunked, non pas Content-Length, avec chaque bloc. L’application cliente peut recevoir chaque bloc successivement afin d’élaborer la réponse complète. Le transfert de données se termine lorsque le serveur envoie un bloc final présentant une taille nulle. Pour implémenter la segmentation dans l’API Web ASP.NET, utilisez la classe `PushStreamContent`.

	L’exemple suivant représente une opération qui répond aux requêtes HTTP GET associées aux images de produits :

	```C#
	public class ProductImagesController : ApiController
	{
    	...
    	[HttpGet]
        [Route("productimages/{id:int}")]
        public IHttpActionResult Get(int id)
        {
            try
            {
                var container = ConnectToBlobContainer(Constants.PRODUCTIMAGESCONTAINERNAME);

                if (!BlobExists(container, string.Format("image{0}.jpg", id)))
                {
                    return NotFound();
                }
                else
                {
                    return new FileDownloadResult()
                    {
                        Container = container,
                        ImageId = id
                    };
                }
            }
            catch
            {
                return InternalServerError();
            }
        }
    	...
	}
	```

	Dans cet exemple, `ConnectBlobToContainer` est une méthode d’assistance qui établit une connexion avec un conteneur spécifique (nom non indiqué) dans le stockage d’objets Blob Microsoft Azure. `BlobExists` est une autre méthode d’assistance qui renvoie une valeur booléenne indiquant si un objet Blob présentant le nom spécifié existe dans le conteneur de stockage d’objets Blob.

	Chaque produit possède sa propre image conservée dans le stockage d’objets Blob. La classe `FileDownloadResult` est une classe `IHttpActionResult` personnalisée qui utilise un objet `PushStreamContent` pour lire les données des images à partir de l’objet Blob approprié et qui les transmet de manière asynchrone en tant que contenu de message de réponse :

	```C#
	public class FileDownloadResult : IHttpActionResult
    {
        public CloudBlobContainer Container { get; set; }
        public int ImageId { get; set; }

        public async Task<HttpResponseMessage> ExecuteAsync(CancellationToken cancellationToken)
        {
            var response = new HttpResponseMessage();
            response.Content = new PushStreamContent(async (outputStream, _, __) =>
            {
                try
                {
                    CloudBlockBlob blockBlob = Container.GetBlockBlobReference(String.Format("image{0}.jpg", ImageId));
                    await blockBlob.DownloadToStreamAsync(outputStream);
                }
                finally
                {
                    outputStream.Close();
                }
            });

            response.StatusCode = HttpStatusCode.OK;
            response.Content.Headers.ContentType = new MediaTypeHeaderValue("image/jpeg");
            return response;
        }
    }
	```

	Vous pouvez également appliquer la diffusion en continue pour charger des opérations si un client a besoin d’appliquer la méthode POST à une nouvelle ressource comportant un objet de taille importante. L’exemple suivant représente la méthode POST associée au contrôleur `ProductImages`. Grâce à cette méthode, le client est en mesure de charger une nouvelle image de produit :

	```C#
	public class ProductImagesController : ApiController
	{
    	...
        [HttpPost]
        [Route("productimages")]
        public async Task<IHttpActionResult> Post()
        {
            try
            {
                if (!Request.Content.Headers.ContentType.MediaType.Equals("image/jpeg"))
                {
                    return StatusCode(HttpStatusCode.UnsupportedMediaType);
                }
                else
                {
                    var id = new Random().Next(); // Use a random int as the key for the new resource. Should probably check that this key has not already been used
                    var container = ConnectToBlobContainer(Constants.PRODUCTIMAGESCONTAINERNAME);
                    return new FileUploadResult()
                    {
                        Container = container,
                        ImageId = id,
                        Request = Request
                    };
                }
            }
            catch
            {
                return InternalServerError();
            }
        }
    	...
	}
	```

	Ce code utilise une autre classe `IHttpActionResult` personnalisée appelée `FileUploadResult`. Cette classe comporte la logique dédiée au chargement asynchrone des données :

	```C#
    public class FileUploadResult : IHttpActionResult
    {
        public CloudBlobContainer Container { get; set; }
        public int ImageId { get; set; }
        public HttpRequestMessage Request { get; set; }

        public async Task<HttpResponseMessage> ExecuteAsync(CancellationToken cancellationToken)
        {
            var response = new HttpResponseMessage();
            CloudBlockBlob blockBlob = Container.GetBlockBlobReference(String.Format("image{0}.jpg", ImageId));
            await blockBlob.UploadFromStreamAsync(await Request.Content.ReadAsStreamAsync());
            var baseUri = string.Format("{0}://{1}:{2}", Request.RequestUri.Scheme, Request.RequestUri.Host, Request.RequestUri.Port);
            response.Headers.Location = new Uri(string.Format("{0}/productimages/{1}", baseUri, ImageId));
            response.StatusCode = HttpStatusCode.OK;
            return response;
        }
    }
	```

	> [AZURE.TIP] Le volume de données pouvant être chargé vers un service Web n’est pas limité par la diffusion en continu, et une requête peut a priori générer un objet de taille importante, consommant un volume considérable de ressources. Si, pendant le processus de diffusion en continu, l’API Web détermine que la taille du volume de données est excessive, l’opération peut être annulée et l’API peut renvoyer un message de réponse présentant le code de statut HTTP 413 (Entité de requête trop volumineuse).

	Vous pouvez réduire la taille des objets volumineux transmis sur le réseau à l’aide de la compression HTTP. Cette approche contribue à réduire le volume de trafic réseau et la latence associée, mais nécessite des efforts de traitement supplémentaires du côté du client et du serveur hébergeant l’API Web. Par exemple, une application cliente qui s’attend à recevoir des données compressées peut comporter un en-tête de requête Accept-Encoding: gzip (d’autres algorithmes de compression de données peuvent également être définis). Si le serveur prend en charge la compression, il doit répondre à l’aide du contenu conservé au format gzip dans le corps du message, avec l’en-tête de réponse Content-Encoding: gzip.

	> [AZURE.TIP] Vous pouvez combiner la compression chiffrée avec la diffusion en continu, compresser les données avant de procéder à leur diffusion et spécifier le chiffrage de contenu gzip et le chiffrage du transfert en bloc dans les en-têtes du message. Notez également que certains serveurs Web (tel qu’Internet Information Server) peuvent être configurés pour compresser automatiquement les réponses HTTP, que l’API Web compresse ou non les données.

- **Implémentez des réponses partielles pour les clients qui ne prennent pas en charge les opérations asynchrones**.

	Quand elle ne recourt pas à la diffusion en continu asynchrone, une application cliente peut rechercher explicitement des gros blocs importants d’objets dans les données, que l’on appelle réponses partielles. L’application cliente envoie une requête HTTP HEAD afin d’obtenir des informations sur l’objet. Si l’API Web prend en charge des réponses partielles, elle doit répondre à la requête HEAD par un message de réponse comportant un en-tête Accept-Ranges et un en-tête Content-Length qui indique la taille totale de l’objet, mais le corps du message doit être vide. L’application cliente peut utiliser ces informations pour élaborer une série de requêtes GET qui définissent une plage d’octets à recevoir. L’API Web doit renvoyer un message de réponse comportant le statut HTTP 206 (Contenu partiel), un en-tête Content-Length qui spécifie le volume réel de données inclus dans le corps du message de réponse et un en-tête Content-Range qui indique la portion (par exemple, des octets 4 000 à 8 000) de l’objet représentée par ces données.

	Les requêtes HTTP HEAD et réponses partielles sont décrites plus en détail dans le document sur les recommandations en matière de conception de l’API.

- **Évitez d’envoyer des messages inutiles de statut Continuer dans les applications clientes**.

	Une application cliente sur le point d’envoyer un volume important de données au serveur peut déterminer dans un premier temps si le serveur va accepter la requête. Avant d’envoyer les données, l’application cliente peut soumettre une requête HTTP présentant un en-tête Expect: 100-Continue, un en-tête Content-Length qui indique la taille des données, mais également un corps de message vide. Si le serveur est prêt à traiter la requête, il doit répondre par un message spécifiant le statut HTTP 100 (Continuer). L’application cliente peut alors poursuivre le processus et envoyer la requête complète comportant les données dans le corps du message.

	Si vous hébergez un service à l’aide d’IIS, le pilote HTTP.sys détecte et traite automatiquement les en-têtes Expect: 100-Continue avant de transmettre les requêtes à votre application Web. Cela signifie que vous avez peu de chances de voir ces en-têtes s’afficher dans le code de votre application, et vous pouvez partir du principe qu’IIS a déjà filtré les messages inappropriés ou dont la taille est jugée trop importante.

	Si vous concevez des applications clientes à l’aide de Microsoft .NET Framework, l’ensemble des messages POST et PUT commenceront par envoyer des messages présentant par défaut l’en-tête Expect: 100-Continue. Du côté du serveur, le processus est géré de manière transparente par Microsoft .NET Framework. Néanmoins, ce processus entraîne 2 allers-retours vers le serveur pour chaque requête POST et PUT, même les plus modestes d’entre elles. Si votre application n’envoie pas de requêtes comportant de gros volumes de données, vous pouvez désactiver cette fonction en utilisant la classe `ServicePointManager` pour créer des objets `ServicePoint` dans l’application cliente. Un objet `ServicePoint` traite les connexions effectuées par le client sur le serveur en fonction des fragments de schéma et d’hôte des URI identifiant les ressources sur le serveur. Vous pouvez définir la propriété `Expect100Continue` de l’objet `ServicePoint` sur false. L’ensemble des requêtes POST et PUT suivantes émises par le client via une URI correspondant aux fragments de schéma et d’hôte de l’objet `ServicePoint` seront envoyées sans l’en-tête Expect: 100-Continue. Le code suivant montre comment configurer un objet `ServicePoint` qui configure l’ensemble des requêtes envoyées aux URI avec un schéma de `http` et un hôte de `www.contoso.com`.

	```C#
	Uri uri = new Uri("http://www.contoso.com/");
	ServicePoint sp = ServicePointManager.FindServicePoint(uri);
	sp.Expect100Continue = false;
	```

	Vous pouvez également définir la propriété statique `Expect100Continue` de la classe `ServicePointManager` pour spécifier la valeur par défaut de cette propriété pour l’ensemble des objets `ServicePoint` créés par la suite. Pour plus d’informations, consultez la page dédiée à la [classe ServicePoint](https://msdn.microsoft.com/library/system.net.servicepoint.aspx) sur le site Web Microsoft.

- **Prenez en charge la pagination pour les requêtes pouvant renvoyer des nombres importants d’objets**.

	Si une collection contient un nombre important de ressources, l’émission d’une requête GET vers l’URI correspondante est susceptible d’entraîner un volume de traitement considérable sur le serveur hébergeant l’API Web affectant les performances et de générer un trafic considérable sur le réseau, et ainsi d’augmenter la latence.

	Pour gérer ces cas, l’API Web doit prendre en charge les chaînes de recherche qui permettent à l’application cliente d’affiner les requêtes ou de rechercher les données par blocs plus gérables, discrets (ou par pages). L’infrastructure d’API Web ASP.NET analyse les chaînes de requêtes et les répartit en séries de paires paramètre/valeur qui sont transmises à la méthode appropriée, conformément aux règles de routage évoquées plus haut. Cette méthode doit être implémentée pour accepter ces paramètres à l’aide des noms spécifiés dans la chaîne de recherche. En outre, ces paramètres doivent être facultatifs (dans les cas où le client omet la chaîne de recherche d’une requête) et présenter des valeurs par défaut pertinentes. Le code ci-dessous représente la méthode `GetAllOrders` dans le contrôleur `Orders`. Cette méthode récupère les détails des commandes. Si cette méthode est sans contraintes, elle peut a priori renvoyer un volume important de données. Les paramètres `limit` et `offset` sont utilisés pour réduire le volume de données à un sous-ensemble plus réduit, ici les 10 premières commandes par défaut :

	```C#
	public class OrdersController : ApiController
	{
    	...
    	[Route("api/orders")]
    	[HttpGet]
    	public IEnumerable<Order> GetAllOrders(int limit=10, int offset=0)
    	{
    	    // Find the number of orders specified by the limit parameter
    	    // starting with the order specified by the offset parameter
    	    var orders = ...
    	    return orders;
    	}
    	...
	}
	```

	Une application cliente peut émettre une requête afin de récupérer 30 commandes à partir de la référence 50, à l’aide de l’URI \__http://www.adventure-works.com/api/orders?limit=30&offset=50_.

	> [AZURE.TIP] Évitez de configurer les applications clientes pour qu’elles définissent des chaînes de recherche résultant en une URI qui présente plus de 2 000 caractères. De nombreux clients et serveurs Web ne peuvent pas prendre en charge les URI de cette longueur.

<a name="considerations-for-maintaining-responsiveness"></a>
## Considérations relatives au maintien de la réactivité, de l’évolutivité et de la disponibilité

Une API Web peut être utilisée par de nombreuses applications clientes de différentes zones géographiques. Il est important de s’assurer que l’API Web implémentée garantisse une réactivité appropriée en cas de charge importante, puisse prendre en charge une charge de travail hautement évolutive et maintienne une disponibilité adéquate pour les clients exécutant des opérations critiques. Lorsque vous déterminez la manière appropriée de répondre à ces exigences, tenez compte des points suivants :

- **Offrez une prise en charge asynchrone pour les requêtes de longue durée**.

	Une requête présentant un délai de traitement important doit être exécutée sans bloquer le client qui la transmet. L’API Web peut effectuer certains contrôles initiaux afin de valider la requête, lancez une tâche séparée pour exécuter la tâche puis renvoyer un message de réponse présentant le code de statut HTTP 202 (Accepté). La tâche peut s’exécuter de manière asynchrone dans le cadre du traitement de l’API Web ou être déchargée vers une tâche Web Microsoft Azure (si l’API Web est hébergée par un site Web Microsoft Azure) ou un rôle de travail (si l’API Web est implémentée en tant que service cloud Microsoft Azure).

	> [AZURE.NOTE] Pour en savoir plus sur l’utilisation des tâches Web avec les Sites Web Microsoft Azure, consultez la page [Exécuter des tâches en arrière-plan avec les tâches Web](web-sites-create-web-jobs.md) du site Web Microsoft.

	L’API Web doit également fournir un mécanisme permettant de renvoyer les résultats du traitement à l’application cliente. À cette fin, dotez-vous d’un mécanisme d’interrogation dédié aux applications clientes, que vous utilisez pour vous informer régulièrement de l’achèvement du traitement et obtenir les résultats ou configurez l’API Web pour qu’elle envoie une notification à l’issue de l’opération.

	Il est possible d’implémenter un mécanisme simple d’interrogation comportant une URI _d’interrogation_ qui joue le rôle d’une ressource virtuelle, en appliquant l’approche suivante :

	1. L’application cliente envoie la requête initiale à l’API web.

	2. L’API web stocke les informations sur la requête dans une table conservée dans le stockage de table ou Microsoft Azure Cache et génère une clé unique pour cette entrée, éventuellement sous la forme d’un élément GUID.

	3. L’API web lance le traitement en tant que tâche séparée. Dans la table, l’API Web consigne l’état _Running_ (En cours d’exécution) pour la tâche.

	4. L’API Web renvoie un message de réponse présentant le code de statut HTTP 202 (Accepté) et l’élément GUID de l’entrée de table dans le corps du message.

	5. Lorsque la tâche est terminée, l’API Web stocke les résultats dans la table, et définit l’état _Complete_ pour la tâche. Si la tâche échoue, l’API peut également stocker des informations sur l’échec et définir le statut sur _Failed_.

	6. Lorsque cette tâche s’exécute, le client peut continuer à effectuer son propre traitement. Il peut régulièrement envoyer une requête à l’URI _/polling/{guid}_, où _{guid}_ est l’élément GUID renvoyé par l’API Web dans le message de réponse 202.

	7. L’API Web de l’URI _/polling{guid}_ interroge l’état de la tâche correspondante dans la table et renvoie un message de réponse présentant le code de statut HTTP 200 (OK) contenant cet état (_Running_, _Complete_, ou _Failed_). Si la tâche est terminée ou a échoué, le message de réponse peut également comporter les résultats du traitement ou toute information disponible relative au motif de l’échec.

	Si vous préférez implémenter des notifications, les options disponibles sont les suivantes :

	- Utilisation d’une unité Notification Hub Microsoft Azure pour transmettre les réponses asynchrones aux applications clientes La page [Notification des utilisateurs via Azure Notification Hubs](notification-hubs-aspnet-backend-windows-dotnet-notify-users.md) du site Web Microsoft fournit des informations détaillées.

	- Utilisation du modèle Comet pour conserver une connexion réseau persistante entre le client et le serveur hébergeant l’API Web, et utilisation de cette connexion pour transmettre les messages entre le serveur et le client. L’article du magazine MSDN [Création d’une application Comet simple dans le Microsoft .NET Framework](https://msdn.microsoft.com/magazine/jj891053.aspx) décrit un exemple de solution.

	- Utilisation de SignalR pour transmettre les données en temps réel du serveur Web au client via une connexion réseau persistante. SignalR est disponible pour les applications Web ASP.NET en tant que package NuGet. Pour en savoir plus, accédez au site Web [ASP.NET SignalR](http://signalr.net/).

	> [AZURE.NOTE] Comet et SignalR utilisent des connexions réseau persistantes entre le serveur Web et l’application cliente. Cette configuration peut affecter l’évolutivité, dans la mesure où un nombre important de clients peuvent solliciter un nombre non moins important de connexions simultanées.

- **Assurez-vous que chaque requête est sans état**.

	L’ensemble des requêtes doivent être considérées comme atomique. Il ne doit exister aucune dépendance entre une requête effectuée par une application cliente et les requêtes suivantes transmises par le même client. Cette approche facilite l’évolutivité ; les instances du service Web peuvent être déployées sur plusieurs serveurs. Les requêtes des clients peuvent être dirigées sur une de ces instances, et les résultats doivent toujours être identiques. Pour la même raison, la disponibilité est également améliorée. En cas de défaillance d’un serveur Web, les requêtes peuvent être acheminées vers une autre instance (à l’aide de Microsoft Azure Traffic Manager) pendant que le serveur est redémarré sans contrepartie d’aucune sorte pour les applications clientes.

- **Suivez les clients et implémentez la limitation afin de réduire les probabilités d’attaques DoS**.

	Si un client spécifique exécute un nombre important de requêtes dans une période définie, il peut monopoliser le service, ce qui affecte, le cas échéant, les performances des autres clients. Pour pallier cette problématique, une API Web peut surveiller les appels provenant des applications clientes, soit en suivant l’adresse IP de l’ensemble des requêtes entrantes, soit en consignant chacun des accès authentifiés. Ces informations peuvent être mises à profit pour limiter l’accès aux ressources. Si un client dépasse la limite définie, l’API Web peut renvoyer un message de réponse présentant le statut 503 (Service indisponible) et inclure un en-tête Retry-After qui indique le moment auquel le client peut envoyer la prochaine requête sans risquer un refus. Cette stratégie permet de réduire les probabilités d’attaques de type DoS (Denial Of Service, déni de service) par un ensemble de clients bloquant le système.

- **Gérez prudemment les connexions HTTP persistantes**.

	Le protocole HTTP prend en charge les connexions HTTP persistantes, quand elles sont disponibles. La spécification HTTP 1.0 a ajouté l’en-tête Connection:Keep-Alive, qui permet à une application cliente d’indiquer au serveur qu’il peut réutiliser la même connexion pour envoyer les requêtes suivantes. La connexion est automatiquement arrêtée si le client ne la réutilise pas pendant une période définie par l’hôte. Il s’agit du comportement par défaut HTTP 1.1 utilisé par les services Microsoft Azure. Ici donc, il n’est pas nécessaire d’inclure des en-têtes Keep-Alive dans les messages.

	Le maintien d’une connexion ouverte peut contribuer à l’amélioration de la réactivité en réduisant la latence et la congestion réseau, mais cela peut affecter l’évolutivité. En effet, en conservant des connexions non nécessaires ouvertes pendant une période prolongée, vous limitez la capacité d’autres clients de se connecter au même moment. Cela peut également affecter la durée de vie de la batterie, si l’application cliente s’exécute sur un appareil mobile. Si l’application transmet des requêtes au serveur à titre occasionnel uniquement, le maintien d’une connexion ouverte peut entraîner une usure prématurée de la batterie. Pour garantir qu’une connexion n’est pas rendue persistante avec HTTP 1.1, le client peut inclure un en-tête Connection:Close dans les messages afin d’écraser le comportement par défaut. De la même manière, si le serveur gère un nombre très important de clients, il peut inclure un en-tête Connection:Close dans les messages de réponse afin de fermer la connexion et d’économiser des ressources serveur.

	> [AZURE.NOTE] Les connexions HTTP persistantes représentent une fonction facultative destinée à alléger la charge du réseau associée à l’établissement répété de canaux de communication. L’API Web et l’application cliente ne doivent pas dépendre de la disponibilité d’une connexion HTTP persistante. N’utilisez pas de connexions HTTP persistantes pour implémenter les systèmes de notification de type Comet. Au lieu de cela, vous devez employer des sockets (ou des sockets Web si disponibles) sur la couche TCP. Enfin, notez que les en-têtes Keep-Alive présentent une utilisation limitée si une application cliente communique avec un serveur via un proxy ; seule la connexion avec le client et le proxy sera persistante.

## Considérations relatives à la publication et à la gestion d’une API Web

Pour rendre une API Web disponible pour les applications clientes, vous devez la déployer au sein d’un environnement d’hôte. Il s’agit généralement d’un serveur Web, mais vous pouvez également utiliser un autre type de processus hôte. Lorsque vous publiez une API Web, vous devez tenir compte des points suivants :

- Toutes les requêtes doivent être authentifiées et autorisées, et le niveau approprié de contrôle d’accès doit être appliqué.
- Une API Web commerciale peut faire l’objet de diverses garanties de qualité concernant les temps de réponse. Vous devez impérativement garantir que l’environnement hôte est évolutif si la charge est susceptible de varier de manière significative au fil du temps.
- Il peut s’avérer nécessaire de mesurer les requêtes, à des fins de monétisation.
- Il vous faudra éventuellement réguler le flux de trafic dirigé vers l’API Web et implémenter une fonctionnalité de limitation pour des clients spécifiques qui ont atteint leurs quotas.
- Les exigences réglementaires peuvent requérir la consignation et l’audit de l’ensemble des requêtes et des réponses.
- Pour garantir la disponibilité, vous devrez éventuellement contrôler l’intégrité du serveur hébergeant l’API Web et le redémarrer, si nécessaire.

Il est utile de pouvoir séparer ces problématiques des dysfonctionnements techniques relatifs à l’implémentation de l’API Web. Pour cette raison, envisagez de créer une [façade](http://en.wikipedia.org/wiki/Facade_pattern), qui s’exécute en tant que processus distinct et route les requêtes vers l’API Web. La façade peut assurer les opérations de gestion et rediriger les requêtes validées vers l’API Web. L’utilisation d’une façade procure de nombreux avantages fonctionnels :

- apport d’un point d’intégration pour plusieurs API Web ;
- transformation des messages et traduction des protocoles de communication pour les clients développés à l’aide de technologies diverses ;
- mise en cache des requêtes et des réponses afin de réduire la charge sur le serveur hébergeant l’API Web.

## Considérations relatives au test d’une API Web
Une API Web doit être testée aussi minutieusement qu’une toute autre composante logicielle. Vous devez envisager de créer des tests unitaires afin de valider la fonctionnalité de chaque opération, comme vous le feriez pour l’ensemble des autres types d’applications. Pour en savoir plus, consultez la page [Vérification du code à l’aide de tests unitaires](https://msdn.microsoft.com/library/dd264975.aspx) du site Web Microsoft.

> [AZURE.NOTE] L’exemple d’API Web fourni avec ces recommandations inclut un projet de test qui vous explique comment effectuer un test unitaire sur les opérations sélectionnées.

Chaque API Web doit faire l’objet de contrôles spécifiques de vérification de son bon fonctionnement. Vous devez prêter une attention particulière aux points suivants :

- Testez l’ensemble des itinéraires afin de vérifier qu’ils invoquent les opérations appropriées. Accordez une attention particulière au code de statut HTTP 405 (Méthode non autorisée). Lorsqu’il est renvoyé de manière imprévue, cela peut indiquer un écart entre un itinéraire et les méthodes HTTP (GET, POST, PUT, DELETE) qui peuvent être réparties sur cet itinéraire.

	Envoyez des requêtes HTTP sur des itinéraires qui ne les prennent pas en charge. Vous pouvez par exemple transmettre une requête HTTP à une ressource spécifique (les requêtes POST doivent être envoyées uniquement aux collections de ressources). Dans ces situations, la seule réponse valide _doit_ être le code de statut 405 (Non autorisé).

- Vérifiez que l’ensemble des itinéraires sont correctement protégés et font l’objet des contrôles d’autorisation et d’authentification appropriés.

	> [AZURE.NOTE] Certains aspects de sécurité comme l’authentification des utilisateurs sont davantage susceptibles d’être la responsabilité de l’environnement hôte, non de l’API Web. Toutefois, il est tout de même nécessaire d’intégrer des tests de sécurité dans le processus de déploiement.

- Testez le traitement des exceptions effectué par chaque opération et vérifiez qu’une réponse HTTP appropriée et pertinente est transmise à l’application cliente.
- Vérifiez que les requêtes et les messages de réponse sont correctement composés. Par exemple, si une requête HTTP POST contient les données associées à une nouvelle ressource sous le format x-www-form-urlencoded, assurez-vous que l’opération correspondante analyse correctement les données, crée les ressources et renvoie une réponse comportant les détails de la nouvelle ressource, notamment l’en-tête Location adéquat.
- Vérifiez l’ensemble des liens et des URI des messages de réponse. Par exemple, un message HTTP POST doit renvoyer l’URI de la ressource nouvellement créée. Tous les liens HATEOAS doivent être valides.

	> [AZURE.IMPORTANT] Si vous publiez l’API Web via un service de gestion des API, ces URI doivent correspondre à l’URL du service de gestion, non celle du serveur hébergeant l’API Web.

- Assurez-vous que chaque opération renvoie les codes de statut appropriés pour différentes combinaisons d’entrée. Par exemple :
	- Si une requête est réussie, elle doit renvoyer le code de statut 200 (OK).
	- Si une ressource est introuvable, l’opération doit renvoyer le code de statut HTTP 404 (Non trouvé).
	- Si le client transmet une requête qui supprime une ressource, le code de statut 204 doit être renvoyé (Aucun contenu).
	- Si le client transmet une requête qui crée une ressource, le code de statut 201 doit être renvoyé (Créé).

Méfiez-vous des codes de statut de réponse inattendus, situés dans la plage 5xx. Ces messages sont généralement signalés par le serveur hôte pour indiquer qu’il n’a pas été en mesure de répondre à une requête valide.

- Testez les différentes combinaisons d’en-têtes de requêtes pouvant être définies par une application cliente et vérifiez que l’API Web renvoie les informations appropriées dans les messages de réponses transmis.

- Testez les chaînes de recherche. Si une opération accepte les paramètres facultatifs (comme les requêtes de pagination), testez les différentes combinaisons et ordres de paramètres.

- Vérifiez que les opérations asynchrones s’effectuent correctement Si l’API Web prend en charge la diffusion en continu pour les requêtes renvoyant des objets binaires de taille importante (comme l’audio ou la vidéo), assurez-vous que les requêtes des clients ne sont pas bloquées pendant la diffusion des données. Si l’API Web implémente une fonctionnalité d’interrogation des opérations de modification de données de longue durée, vérifiez que les opérations signalent correctement leur statut au cours de l’exécution.

Vous devez également créer et exécuter des tests de performances afin de vérifier que l’API Web fonctionne correctement sous contraintes. Pour développer un projet de test de chargement et de performances Web, utilisez Visual Studio Ultimate. Pour plus d’informations, consultez la page [Exécuter des tests de performances sur votre application](https://msdn.microsoft.com/library/dn250793.aspx) du site Web Microsoft.

## Publication et gestion d’une API Web à l’aide du service de gestion des API Microsoft Azure

Microsoft Azure offre le [service de gestion des API](https://azure.microsoft.com/documentation/services/api-management/), que vous pouvez utiliser pour publier et gérer une API Web. À l’aide de cette fonctionnalité, vous générez un service utilisé comme façade pour une ou plusieurs API Web. Il s’agit d’un service pouvant être créé et configuré à l’aide du portail de gestion Microsoft Azure. Il peut être mis à profit pour publier et gérer une API Web comme suit :

1. Déployez l’API Web sur un site Web, un service cloud Microsoft Azure ou une machine virtuelle Microsoft Azure.

2. Connectez le service de gestion des API à l’API Web. Les requêtes envoyées à l’URL de l’API de gestion sont mappées sur les URI de l’API Web. Un service de gestion des API peut router les requêtes vers plusieurs API Web. Cela vous permet d’agréger plusieurs API Web au sein d’un service unique de gestion. De la même manière, une API Web peut être référencée à partir de plusieurs services de gestion des API si vous devez restreindre ou partitionner la fonctionnalité accessible par différentes applications.

	> [AZURE.NOTE] Les URI des liens HATEOAS générées dans la réponse aux requêtes HTTP GET doivent référencer l’URL du service de gestion des API, non le serveur Web hébergeant l’API Web.

3. Pour chaque API Web, spécifiez les opérations HTTP exposées avec les paramètres facultatifs acceptés en tant qu’entrées par les opérations. Vous pouvez également configurer la mise en cache, par le service de gestion des API, de la réponse transmise par l’API Web afin d’optimiser les requêtes répétées pour des données identiques. Enregistrez les détails des réponses HTTP pouvant être générées par chaque opération. Ces informations étant utilisées pour générer la documentation destinée aux développeurs, il est important qu’elles soient précises et exhaustives.

	Pour définir les opérations, vous procédez manuellement à l’aide des assistants du portail de gestion Microsoft Azure ou vous les importez à partir d’un fichier comportant les définitions aux formats WADL ou Swagger.

4. Configurez les paramètres de sécurité des communications entre le service de gestion des API et le serveur Web hébergeant l’API Web. Le service de gestion des API prend actuellement en charge l’authentification de base et l’authentification mutuelle à l’aide de certificats et l’autorisation utilisateur OAuth 2.0.

5. Créez un produit. Un produit est une unité de publication. Vous ajoutez les API Web que vous avez préalablement connectées au service de gestion et au produit. Une fois que le produit est publié, les API Web sont rendues disponibles aux développeurs.

	> [AZURE.NOTE] Avant de publier un produit, vous pouvez également définir des groupes d’utilisateurs disposant d’un accès au produit, et ajouter des utilisateurs à ces groupes. Cela vous octroie un contrôle sur les développeurs et les applications qui peuvent utiliser l’API Web. Si une API Web nécessite une approbation, un développeur souhaitant y accéder doit envoyer une requête à l’administrateur des produits. L’administrateur peut accorder ou refuser l’accès au développeur. Si les circonstances sont modifiées, l’administrateur est en mesure de bloquer des développeurs existants.

6.	Configurez les stratégies associées à chaque API Web. Les stratégies régissent différents aspects, comme l’autorisation des appels interdomaines, la méthode d’authentification des clients, la conversion transparente entre les formats XML et JSON, la restriction des appels provenant d’une plage d’IP considérée, les quotas d’utilisation et la limitation du débit des appels. Les stratégies peuvent être appliquées globalement sur l’ensemble du produit, sur une API Web d’un produit ou sur des opérations individuelles d’une API Web.

Vous trouverez les informations détaillées sur l’exécution de cette tâche sur la page [Gestion des API](https://azure.microsoft.com/services/api-management/) du site Web Microsoft. Le service de gestion des API Microsoft Azure offre également sa propre interface REST. Cette dernière vous permet de développer une interface personnalisée afin de simplifier le processus de configuration d’une API Web. Pour en savoir plus, consultez la page [Informations de référence sur l’API REST de gestion des API Azure](https://msdn.microsoft.com/library/azure/dn776326.aspx) du site Web Microsoft.

> [AZURE.TIP] Microsoft Azure fournit Traffic Manager, à l’aide duquel vous implémentez les fonctionnalités de basculement et d’équilibrage de charge et réduisez la latence sur de multiples instances d’un site Web hébergées dans différentes zones géographiques. Vous pouvez utiliser Microsoft Azure Manager conjointement avec le service de gestion des API, qui peut router les requêtes vers des instances d’un site Web via Microsoft Azure Manager, le cas échéant. Pour plus d’informations, consultez la page [À propos des méthodes d’équilibrage de charge de Traffic Manager](../traffic-manager/traffic-manager-load-balancing-methods.md) du site Web Microsoft.

> Dans cette structure, si vous utilisez des noms DNS personnalisés pour vos sites Web, vous devez configurer l’enregistrement CNAME approprié afin que chaque site Web pointe sur le nom DNS du site Web Microsoft Azure Traffic Manager.

## Soutien des développeurs d’applications clientes
Les développeurs d’applications clientes ont généralement besoin d’informations relatives à l’accès à l’API Web et d’une documentation relatives aux paramètres, aux types de données, aux types de renvoi et aux codes de renvoi associés aux différentes requêtes et réponses échangées entre le service Web et l’application cliente.

### Documentation des opérations REST associées à une API Web
Le service de gestion des API Microsoft Azure comprend un portail dédié aux développeurs qui décrit les opérations REST exposées par une API Web. Une fois qu’un produit a été publié, il apparaît sur ce portail. Les développeurs peuvent requérir un accès sur ce portail. Par la suite, l’administrateur approuve ou refuse la requête. Si elle est approuvée, les développeurs se voient allouer une clé d’abonnement qui est utilisée pour authentifier les appels provenant des applications clientes qu’ils développent. Cette clé est à fournir à chaque appel d’API Web, sans quoi l’émetteur essuie un refus.

Ce portail fournit également les éléments suivants :

- la documentation sur le produit, répertoriant les opérations exposées, les paramètres requis et les différentes réponses pouvant être renvoyées. Notez que ces informations sont générées à partir des détails fournis à l’étape 3 de la liste de la section [Publication d’une API Web à l’aide du service de gestion des API Microsoft Azure](#publishing-a-web-API) ;

- les extraits de code indiquant comment invoquer des opérations de langages différents, notamment JavaScript, C#, Java, Ruby, Python et PHP ;

- une console de développeur, à partir de laquelle ce dernier peut envoyer une requête HTTP afin de tester chaque opération dans le produit et afficher les résultats ;

- une page sur laquelle le développeur peut consigner les problèmes identifiés ;

Le portail de gestion Microsoft Azure vous permet de personnaliser le portail dédié aux développeurs, par exemple en modifiant le style et la présentation en fonction de la représentation de votre entreprise.

### Implémentation d’un kit de développement logiciel (SDK) client
Le développement d’une application invoquant des requêtes REST pour accéder à une API Web nécessite l’écriture d’un volume considérable de code. Ce code sert à élaborer et à formater de manière appropriée les requêtes, à envoyer la requête au serveur hébergeant le service Web, à analyser la réponse utilisée pour déterminer si la requête a abouti ou échoué et à extraire les éventuelles données renvoyées. Pour protéger l’application cliente contre ces problématiques, vous avez tout intérêt à vous doter d’un kit de développement logiciel qui encapsule l’interface REST et replace ces détails de niveau inférieur au sein d’un ensemble de méthodes plus fonctionnel. Une application cliente valorise ces méthodes, qui convertissent de manière transparente les appels en requêtes REST, puis les réponses en valeurs renvoyées de méthode. C’est une technique courante qui est implémentée par de nombreux services, dont Microsoft Azure SDK.

La création d’une solution SDK côté client est une tâche considérable, dans la mesure où l’implémentation doit être cohérente est soigneusement testée. Toutefois, la majorité de ce processus peut être mécanisée ; de nombreux fournisseurs proposent des outils dédiés à l’automatisation de beaucoup des tâches associées.

## Surveillance d’une API Web

Selon la façon dont vous avez publié et déployé votre API Web, vous pouvez la surveiller directement ou collecter les informations d’utilisation et d’intégrité en analysant le trafic qui transite via le service de gestion des API.

### Surveillance directe d’une API Web
Si vous avez implémenté votre API Web à l’aide du modèle d’API Web ASP.NET (soit en tant que projet d’API Web ou en tant que rôle Web dans un service cloud Microsoft Azure) et Visual Studio 2013, vous pouvez collecter les données de disponibilité, de performances et d’utilisation à l’aide d’Application Insights ASP.NET. Application Insights est un package qui suit et consigne de manière transparente les informations sur les requêtes et les réponses lorsque l’API Web est déployée dans le cloud. Une fois que le package est installé et configuré, vous n’avez pas besoin de modifier le code de votre API Web pour l’utiliser. Lorsque vous déployez l’API Web sur un site Web Microsoft Azure, l’ensemble du trafic est examiné et les statistiques suivantes sont collectées :

- temps de réponse du serveur ;

- nombre de requêtes vers le serveur et les détails sur chacune d’entre elles ;

- les requêtes les plus lentes, relativement au temps de réponse moyen ;

- les informations détaillées sur les requêtes mises en échec ;

- le nombre de sessions ouvertes par différents navigateurs et agents utilisateurs ;

- les pages les plus fréquemment consultées (informations essentiellement utiles pour les applications Web, pas pour les API Web) ;

- les différents rôles d’utilisateur accédant à l’API Web.

Vous pouvez afficher ces données en temps réel, à partir du portail de gestion Microsoft Azure. Vous pouvez également créer des tests Web contrôlant l’intégrité de l’API Web. Un test Web transmet une requête périodique à une URI spécifiée dans l’API Web, et collecte la réponse. Vous pouvez spécifier la définition d’une réponse probante (comme un code de statut HTTP 200) et si la requête ne renvoie pas cette réponse, vous pouvez configurer l’envoi d’une alerte à un administrateur. Si nécessaire, l’administrateur peut redémarrer le serveur hébergeant l’API Web, en cas d’échec.

La page [Application Insights - Commencer à analyser l’état d’intégrité et l’utilisation de votre application](app-insights-start-monitoring-app-health-usage/) du site Web Microsoft fournit davantage d’information.

### Surveillance d’une API Web via le service de gestion des API

Si vous avez publié votre API Web à l’aide du service de gestion des API, la page Gestion des API du portail de gestion Microsoft Azure contient un tableau de bord qui vous donne accès aux performances globales du service. La page d’analyse vous procure des informations plus détaillées sur l’utilisation du produit. Cette page comporte les onglets suivants :

- **Utilisation**. Cet onglet fournit des informations sur le nombre d’appels de l’API et sur la bande passante utilisée pour traiter ces appels au fil du temps. Vous pouvez filtrer les détails d’utilisation par produit, API et opération.

- **Intégrité**. Cet onglet vous donne accès aux résultats des requêtes d’API (codes de statut HTTP renvoyés), sur l’efficacité de la stratégie de mise en cache, sur le temps de réponse de l’API et sur le temps de réponse du service. Là aussi, vous pouvez filtrer les données d’intégrité par produit, API et opération.

- **Activité**. Cet onglet comporte une synthèse récapitulant le nombre total d’appels concluants, les appels mis en échec, les appels bloqués, le temps de réponse moyen et les temps de réponse pour chaque produit, API Web et opération. Cette page répertorie également le nombre d’appels effectués par chaque développeur.

- **Aperçu**. Cet onglet fournit un résumé des données de performance, notamment sur les développeurs à l’origine des appels d’API, sur les produits, les API Web et les opérations destinataires de ces appels.

Ces informations peuvent être mises à profit pour déterminer si une API Web ou une opération spécifique provoque un goulot d’étranglement, et si nécessaire mettre à l’échelle l’environnement hôte et ajouter davantage de serveurs. Vous pouvez également vérifier si une ou plusieurs applications utilisent un volume disproportionné de ressources et appliquer les stratégies appropriées pour définir des quotas et limiter les débits d’appels.

> [AZURE.NOTE] Vous pouvez modifier les détails associés à un produit publié ; le cas échéant, les modifications sont appliquées immédiatement. Par exemple, vous pouvez ajouter ou retirer une opération d’une API Web sans qu’il ne vous soit nécessaire de republier le produit contenant l’API Web.

## Modèles associés
- Le modèle [façade](http://en.wikipedia.org/wiki/Facade_pattern) décrit comment doter une API Web d’une interface.

## Plus d’informations
- La page [En savoir plus sur l’API Web ASP.NET](http://www.asp.net/web-api) du site Web Microsoft vous fournit une présentation détaillée sur le développement de services Web RESTful à l’aide de l’API Web.
- La page [Routage dans l’API Web ASP.NET](http://www.asp.net/web-api/overview/web-api-routing-and-actions/routing-in-aspnet-web-api) du site Web Microsoft décrit le fonctionnement du routage basé sur des conventions dans l’infrastructure de l’API Web ASP.NET.
- Pour plus d’informations sur le routage basé sur les attributs, consultez la page [Routage des attributs dans l’API Web 2](http://www.asp.net/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2) du site Web Microsoft.
- La page [Basic Tutorial](http://www.odata.org/getting-started/basic-tutorial/) du site Web OData fournit une introduction sur les fonctionnalités du protocole OData.
- La page [API Web OData ASP.NET](http://www.asp.net/web-api/overview/odata-support-in-aspnet-web-api) du site Web Microsoft contient des exemples et des informations détaillées sur l’implémentation d’une API Web OData à l’aide d’ASP.NET.
- La page [Introduction de la prise en charge de lots dans l’API Web et l’API Web OData](http://blogs.msdn.com/b/webdev/archive/2013/11/01/introducing-batch-support-in-web-api-and-web-api-odata.aspx) du site Web Microsoft décrit comment implémenter les opérations par lot dans une API Web à l’aide d’OData.
- L’article [Idempotency Patterns](http://blog.jonathanoliver.com/idempotency-patterns/) (Modèles d’idempotence) du blog de Jonathan Oliver propose un aperçu de l’idempotence, et lie ce concept aux opérations de gestion de données.
- Vous pouvez consulter la liste exhaustive des codes de statut HTTP et leur description sur la page [Status Code Definitions](http://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) du site Web de l’organisme W3C.
- Pour en savoir plus sur le traitement des exceptions HTTP avec l’API Web ASP.NET, consultez la page [Gestion des exceptions dans l’API Web ASP.NET](http://www.asp.net/web-api/overview/error-handling/exception-handling) du site Web Microsoft.
- L’article [Traitement des erreurs globales de l’API Web](http://www.asp.net/web-api/overview/error-handling/web-api-global-error-handling) du site Web Microsoft décrit comment implémenter une stratégie globale de traitement et de consignation des erreurs associées à l’API Web.
- La page [Exécuter des tâches en arrière-plan avec les tâches Web](web-sites-create-web-jobs.md) du site Web Microsoft fournit des informations et des exemples sur l’utilisation des tâches Web pour effectuer des opérations d’arrière-plan sur un site Web Microsoft Azure.
- La page [Notification des utilisateurs via Azure Notification Hubs](notification-hubs-aspnet-backend-windows-dotnet-notify-users/) du site Web Microsoft vous indique comment utiliser une unité Microsoft Azure Notification Hub pour transmettre des réponses asynchrones aux applications clientes.
- La page [Gestion des API](https://azure.microsoft.com/services/api-management/) du site Web Microsoft décrit comment publier un produit procurant un accès contrôlé et sécurisé à une API Web.
- La page [Informations de référence sur l’API REST de gestion des API Azure](https://msdn.microsoft.com/library/azure/dn776326.aspx) du site Web Microsoft vous décrit comment utiliser une API REST de gestion des API pour développer des applications de gestion personnalisées.
- La page [À propos des méthodes d’équilibrage de charge de Traffic Manager](../traffic-manager/traffic-manager-load-balancing-methods.md) du site Web Microsoft décrit succinctement comme Microsoft Azure Traffic Manager peut être utilisé pour équilibrer la charge des requêtes sur plusieurs instances d’un site Web hébergeant une API Web.
- La page [Application Insights - Commencer à analyser l’état d’intégrité et l’utilisation de votre application](app-insights-start-monitoring-app-health-usage.md) du site Web Microsoft fournit des informations détaillées sur l’installation et la configuration d’Application Insights dans un projet d’API Web ASP.NET.
- La page [Vérification du code à l’aide de tests unitaires](https://msdn.microsoft.com/library/dd264975.aspx) du site Web Microsoft fournit des informations détaillées sur la création et la gestion de tests unitaires à l’aide de Visual Studio.
- La page [Exécuter des tests de performances sur votre application](https://msdn.microsoft.com/library/dn250793.aspx) du site Web Microsoft explique comment utiliser Visual Studio Ultimate pour créer un projet de test de performances Web et de chargement.

<!---HONumber=AcomDC_0128_2016-->