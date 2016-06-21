<properties 
	pageTitle="Utilisation du Cache Redis Azure | Microsoft Azure" 
	description="Découvrez comment améliorer les performances de vos applications Azure grâce au Cache Redis Azure" 
	services="redis-cache,app-service" 
	documentationCenter="" 
	authors="steved0x" 
	manager="douge" 
	editor=""/>

<tags 
	ms.service="cache" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="cache-redis" 
	ms.devlang="dotnet" 
	ms.topic="hero-article" 
	ms.date="06/09/2016" 
	ms.author="sdanie"/>

# Utilisation du Cache Redis Azure

> [AZURE.SELECTOR]
- [.NET](cache-dotnet-how-to-use-azure-redis-cache.md)
- [ASP.NET](cache-web-app-howto.md)
- [Node.JS](cache-nodejs-get-started.md)
- [Java](cache-java-get-started.md)
- [Python](cache-python-get-started.md)

Ce guide décrit la prise en main du **Cache Redis Azure**. Le Cache Microsoft Azure Redis se base sur le cache Redis open source connu. Il vous donne accès à un cache Redis dédié et sécurisé, qui est géré par Microsoft. Un cache créé avec Cache Redis Azure est accessible à partir de toutes les applications dans Microsoft Azure.

Cache Redis Microsoft Azure est disponible dans les niveaux suivants :

-	**De base**, avec un seul nœud. Plusieurs tailles jusqu'à 53 Go.
-	**Standard** : avec deux nœuds, principal et réplica. Plusieurs tailles jusqu'à 53 Go. Un contrat SLA de 99,9 %.
-	**Premium** - Deux nœuds (principal / réplica) contenant jusqu’à 10 partitions. Plusieurs tailles de 6 Go à 530 Go (nous contacter pour en savoir plus). Toutes les fonctionnalités du niveau Standard et d’autres, y compris la prise en charge du [cluster Redis](cache-how-to-premium-clustering.md), la [persistance Redis](cache-how-to-premium-persistence.md) et le [réseau virtuel Azure](cache-how-to-premium-vnet.md). Un contrat SLA de 99,9 %.

Chaque option diffère en termes de fonctionnalités et de tarification. Pour plus d’informations sur la tarification, consultez la rubrique [Détails de tarification Cache][].

Ce guide vous montre comment utiliser le client [StackExchange.Redis][] à l’aide du code C#. Les scénarios couverts incluent **la création et la configuration d’un cache**, **la configuration des clients de cache** et **l’ajout et la suppression d’objets dans le cache**. Pour plus d’informations sur l’utilisation du Cache Redis Azure, consultez la section [Étapes suivantes][]. Pour obtenir un didacticiel pas à pas dédié à la création d’une application web ASP.NET MVC avec le Cache Redis, consultez [Création d’une application web avec le Cache Redis](cache-web-app-howto.md).

<a name="getting-started-cache-service"></a>
## Prise en main de Cache Redis Azure

La prise en main de Cache Redis Azure est aisée. Pour commencer, vous mettez en service et configurez un cache. Ensuite, vous configurez les clients du cache pour qu'ils puissent accéder au cache. Une fois les clients du cache configurés, vous pouvez commencer à les utiliser.

-	[Création du cache][]
-	[Configuration des clients de cache][]

<a name="create-cache"></a>
## Création d'un cache

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

### Pour accéder à votre cache après sa création

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-browse.md)]

Pour plus d’informations sur la configuration de votre cache, voir [Configuration de Cache Redis Azure](cache-configure.md).

<a name="NuGet"></a>
## Configuration des clients de cache

[AZURE.INCLUDE [redis-cache-configure](../../includes/redis-cache-configure-stackexchange-redis-nuget.md)]

Une fois que votre projet client est configuré pour la mise en cache, vous pouvez utiliser les techniques décrites dans les sections suivantes pour utiliser votre cache.

<a name="working-with-caches"></a>
## Utilisation des caches

Cette section décrit l'exécution des tâches courantes avec Cache.

-	[Connexion au cache][]
-   [Ajout et récupération d’objets dans le cache][]
-   [Utilisation des objets .NET dans le cache](#work-with-net-objects-in-the-cache)

<a name="connect-to-cache"></a>
## Connexion au cache

Pour utiliser un cache par programmation, vous avez besoin d'une référence au cache. Ajoutez ce qui suit au début des fichiers qui doivent utiliser le client StackExchange.Redis pour accéder à un cache Redis Azure.

    using StackExchange.Redis;

>[AZURE.NOTE] Le client StackExchange.Redis a besoin de .NET Framework 4 ou version ultérieure.

La connexion au Cache Redis Azure est gérée par la classe `ConnectionMultiplexer`. Cette classe est conçue pour être partagée et réutilisée dans toute votre application cliente et ne doit pas être créée pour chaque opération.

Pour vous connecter au Cache Redis Azure et recevoir en retour une instance `ConnectionMultiplexer` connectée, appelez la méthode statique `Connect`, puis passez le point de terminaison et la clé du cache comme dans l’exemple suivant. Utilisez la clé Azure générée sur le portail Azure comme paramètre du mot de passe.

	ConnectionMultiplexer connection = ConnectionMultiplexer.Connect("contoso5.redis.cache.windows.net,abortConnect=false,ssl=true,password=...");

>[AZURE.IMPORTANT] Avertissement : ne stockez jamais d’informations d’identification dans du code source. Pour ne pas alourdir cet exemple, elles sont montrées dans le code source. Consultez [Fonctionnement des chaînes d’application et de connexion][] pour plus d’informations sur le stockage des informations d’identification.

Si vous ne souhaitez pas utiliser SSL, configurez `ssl=false` ou omettez le paramètre `ssl`.

>[AZURE.NOTE] Le port non SSL est désactivé par défaut pour les nouveaux caches. Pour obtenir des instructions sur l’activation du port non-SSL, consultez la section relative aux [ports d’accès](cache-configure.md#access-ports).

Une approche de partage d’une instance `ConnectionMultiplexer` dans votre application est d’avoir une propriété statique qui renvoie une instance connectée, comme dans l'exemple suivant. Cela fournit une méthode thread-safe permettant d’initialiser une seule instance `ConnectionMultiplexer` connectée. Dans ces exemples, `abortConnect` est défini sur false, ce qui signifie que l’appel réussira même si aucune connexion au Cache Redis Azure n’est établie. Une fonctionnalité clé de `ConnectionMultiplexer` est qu’il restaure automatiquement la connectivité au cache une fois que le problème réseau ou d’autres causes sont résolus.

	private static Lazy<ConnectionMultiplexer> lazyConnection = new Lazy<ConnectionMultiplexer>(() =>
	{
	    return ConnectionMultiplexer.Connect("contoso5.redis.cache.windows.net,abortConnect=false,ssl=true,password=...");
	});
	
	public static ConnectionMultiplexer Connection
	{
	    get
	    {
	        return lazyConnection.Value;
	    }
	}

Pour plus d'informations sur les options avancées de configuration de connexion, consultez la rubrique [Modèle de configuration StackExchange.Redis][].

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]

Une fois la connexion établie, renvoyez une référence à la base de données du Cache Redis en appelant la méthode `ConnectionMultiplexer.GetDatabase`. L’objet renvoyé à partir de la méthode `GetDatabase` est un objet direct léger qui n’a pas besoin d’être stocké.

	// Connection refers to a property that returns a ConnectionMultiplexer
	// as shown in the previous example.
	IDatabase cache = Connection.GetDatabase();

	// Perform cache operations using the cache object...
	// Simple put of integral data types into the cache
	cache.StringSet("key1", "value");
	cache.StringSet("key2", 25);

	// Simple get of data types from the cache
	string key1 = cache.StringGet("key1");
	int key2 = (int)cache.StringGet("key2");

Maintenant que vous savez vous connecter à une instance Cache Redis Azure et renvoyer une référence à la base de données du cache, regardons comment utiliser le cache.

<a name="add-object"></a>
## Ajout et récupération d'objets dans le cache

Les objets peuvent être stockés dans le cache et en être extraits en utilisant les méthodes `StringSet` et `StringGet`.

	// If key1 exists, it is overwritten.
	cache.StringSet("key1", "value1");

	string value = cache.StringGet("key1");

Redis stocke la plupart des données sous la forme de chaînes Redis, mais ces chaînes peuvent contenir de nombreux types de données, notamment des données binaires sérialisées, qui peuvent être utilisées lors du stockage d'objets .NET dans le cache.

Lors de l’appel de `StringGet`, si l’objet existe, il est renvoyé ; sinon, `null` est renvoyé. Dans ce cas, vous pouvez extraire la valeur de la source de données de votre choix et la stocker dans le cache pour un usage ultérieur. On parle alors de modèle de type cache-aside.

    string value = cache.StringGet("key1");
    if (value == null)
    {
        // The item keyed by "key1" is not in the cache. Obtain
        // it from the desired data source and add it to the cache.
        value = GetValueFromDataSource();

        cache.StringSet("key1", value);
    }

Pour spécifier l’expiration d’un élément du cache, utilisez le paramètre `TimeSpan` de `StringSet`.

	cache.StringSet("key1", "value1", TimeSpan.FromMinutes(90));

## Utilisation des objets .NET dans le cache

Cache Redis Azure peut mettre en cache des objets .NET comme des types de données primitifs, mais avant qu'un objet .NET puisse être mis en cache, il doit être sérialisé. Cette opération échoit au développeur d’applications, qui a toute latitude pour choisir le sérialiseur.

Une méthode simple pour sérialiser des objets consiste à utiliser les méthodes de sérialisation `JsonConvert` dans [Newtonsoft.Json.NET](https://www.nuget.org/packages/Newtonsoft.Json/8.0.1-beta1) et à sérialiser vers et à partir de JSON. L’exemple suivant montre des méthodes get et set utilisant une instance d’objet `Employee`.


	class Employee
	{
	    public int Id { get; set; }
	    public string Name { get; set; }
	
	    public Employee(int EmployeeId, string Name)
	    {
	        this.Id = EmployeeId;
	        this.Name = Name;
	    }
	}

    // Store to cache
    cache.StringSet("e25", JsonConvert.SerializeObject(new Employee(25, "Clayton Gragg")));

    // Retrieve from cache
    Employee e25 = JsonConvert.DeserializeObject<Employee>(cache.StringGet("e25"));

<a name="next-steps"></a>
## Étapes suivantes

Maintenant que vous connaissez les bases, consultez les liens suivants pour en savoir plus sur le Cache Redis Azure.

-	Découvrez les fournisseurs ASP.NET pour le Cache Redis Azure.
	-	[Fournisseur de l'état de session Redis Azure](cache-aspnet-session-state-provider.md)
	-	[Fournisseur de caches de sortie ASP.NET du Cache Redis Azure](cache-aspnet-output-cache-provider.md)
-	[Activez les diagnostics du cache](cache-how-to-monitor.md#enable-cache-diagnostics) afin de pouvoir [surveiller](cache-how-to-monitor.md) l’intégrité de votre cache. Vous pouvez afficher les mesures dans le portail Azure, puis [les télécharger et les analyser](https://github.com/rustd/RedisSamples/tree/master/CustomMonitoring) à l’aide des outils de votre choix.
-	Consultez la [Documentation du client du cache StackExchange.Redis][].
	-	Le Cache Redis Azure est accessible depuis de nombreux clients Redis et langages de développement. Pour plus d’informations, voir [http://redis.io/clients][].
	-	Le Cache Redis Azure peut également être utilisé avec des services tels que Redsmin. Pour plus d’informations, consultez la [page expliquant comment récupérer une chaîne de connexion Azure Redis pour l’utiliser avec Redsmin][].
-	Consultez la documentation [redis][] et notamment les [types de données Redis][] et [quinze minutes de présentation des types de données Redis][].



<!-- INTRA-TOPIC LINKS -->
[Étapes suivantes]: #next-steps
[Introduction to Azure Redis Cache (Video)]: #video
[What is Azure Redis Cache?]: #what-is
[Create an Azure Cache]: #create-cache
[Which type of caching is right for me?]: #choosing-cache
[Prepare Your Visual Studio Project to Use Azure Caching]: #prepare-vs
[Configure Your Application to Use Caching]: #configure-app
[Get Started with Azure Redis Cache]: #getting-started-cache-service
[Création du cache]: #create-cache
[Configure the cache]: #enable-caching
[Configuration des clients de cache]: #NuGet
[Working with Caches]: #working-with-caches
[Connexion au cache]: #connect-to-cache
[Ajout et récupération d’objets dans le cache]: #add-object
[Specify the expiration of an object in the cache]: #specify-expiration
[Store ASP.NET session state in the cache]: #store-session

  
<!-- IMAGES -->


[StackExchangeNuget]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-stackexchange-redis.png

[NuGetMenu]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-manage-nuget-menu.png

[CacheProperties]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-properties.png

[ManageKeys]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-manage-keys.png

[SessionStateNuGet]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-session-state-provider.png

[BrowseCaches]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-browse-caches.png

[Caches]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-caches.png






   
<!-- LINKS -->
[http://redis.io/clients]: http://redis.io/clients
[Develop in other languages for Azure Redis Cache]: http://msdn.microsoft.com/library/azure/dn690470.aspx
[page expliquant comment récupérer une chaîne de connexion Azure Redis pour l’utiliser avec Redsmin]: https://redsmin.uservoice.com/knowledgebase/articles/485711-how-to-connect-redsmin-to-azure-redis-cache
[Azure Redis Session State Provider]: http://go.microsoft.com/fwlink/?LinkId=398249
[How to: Configure a Cache Client Programmatically]: http://msdn.microsoft.com/library/windowsazure/gg618003.aspx
[Session State Provider for Azure Cache]: http://go.microsoft.com/fwlink/?LinkId=320835
[Azure AppFabric Cache: Caching Session State]: http://www.microsoft.com/showcase/details.aspx?uuid=87c833e9-97a9-42b2-8bb1-7601f9b5ca20
[Output Cache Provider for Azure Cache]: http://go.microsoft.com/fwlink/?LinkId=320837
[Azure Shared Caching]: http://msdn.microsoft.com/library/windowsazure/gg278356.aspx
[Team Blog]: http://blogs.msdn.com/b/windowsazure/
[Azure Caching]: http://www.microsoft.com/showcase/Search.aspx?phrase=azure+caching
[How to Configure Virtual Machine Sizes]: http://go.microsoft.com/fwlink/?LinkId=164387
[Azure Caching Capacity Planning Considerations]: http://go.microsoft.com/fwlink/?LinkId=320167
[Azure Caching]: http://go.microsoft.com/fwlink/?LinkId=252658
[How to: Set the Cacheability of an ASP.NET Page Declaratively]: http://msdn.microsoft.com/library/zd1ysf1y.aspx
[How to: Set a Page's Cacheability Programmatically]: http://msdn.microsoft.com/library/z852zf6b.aspx
[Configure a cache in Azure Redis Cache]: http://msdn.microsoft.com/library/azure/dn793612.aspx

[Modèle de configuration StackExchange.Redis]: http://github.com/StackExchange/StackExchange.Redis/blob/master/Docs/Configuration.md

[Work with .NET objects in the cache]: http://msdn.microsoft.com/library/dn690521.aspx#Objects


[NuGet Package Manager Installation]: http://go.microsoft.com/fwlink/?LinkId=240311
[Détails de tarification Cache]: http://www.windowsazure.com/pricing/details/cache/
[Azure Portal]: https://portal.azure.com/

[Overview of Azure Redis Cache]: http://go.microsoft.com/fwlink/?LinkId=320830
[Azure Redis Cache]: http://go.microsoft.com/fwlink/?LinkId=398247

[Migrate to Azure Redis Cache]: http://go.microsoft.com/fwlink/?LinkId=317347
[Azure Redis Cache Samples]: http://go.microsoft.com/fwlink/?LinkId=320840
[Using Resource groups to manage your Azure resources]: http://azure.microsoft.com/documentation/articles/resource-group-overview/

[StackExchange.Redis]: http://github.com/StackExchange/StackExchange.Redis
[Documentation du client du cache StackExchange.Redis]: http://github.com/StackExchange/StackExchange.Redis#documentation

[Redis]: http://redis.io/documentation
[types de données Redis]: http://redis.io/topics/data-types
[quinze minutes de présentation des types de données Redis]: http://redis.io/topics/data-types-intro

[Fonctionnement des chaînes d’application et de connexion]: http://azure.microsoft.com/blog/2013/07/17/windows-azure-web-sites-how-application-strings-and-connection-strings-work/

<!---HONumber=AcomDC_0615_2016-->