<properties 
	pageTitle="Utilisation du Cache Redis Azure" 
	description="Découvrez comment améliorer les performances de vos applications Azure grâce au Cache Redis Azure" 
	services="redis-cache" 
	documentationCenter="" 
	authors="steved0x" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="cache" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="cache-redis" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="05/20/2015" 
	ms.author="sdanie"/>

# Utilisation du Cache Redis Azure

Ce guide décrit la prise en main du **Cache Redis Azure**. Les exemples ont été écrits en code C# et utilisent le client [StackExchange.Redis][]. Les scénarios couverts incluent **la création et la configuration d'un cache**, **la configuration des clients de cache**, **l'ajout et la suppression d'objets dans le cache** et **le stockage de l'état de session ASP.NET dans le cache**. Pour plus d’informations sur l’utilisation du Cache Redis Azure, consultez la section [Étapes suivantes][].

<a name="what-is"></a>
## Présentation du Cache Redis Azure

Le Cache Microsoft Azure Redis se base sur le cache Redis open source connu. Il vous donne accès à un cache Redis dédié et sécurisé, qui est géré par Microsoft. Un cache créé avec Cache Redis Azure est accessible à partir de toutes les applications dans Microsoft Azure.

Cache Redis Microsoft Azure est disponible en deux options :

-	**De base**, avec un seul nœud. Plusieurs tailles jusqu'à 53 Go.
-	**Standard** : avec deux nœuds, principal et réplica. Plusieurs tailles jusqu'à 53 Go. Un contrat SLA de 99,9 %.

Chaque option diffère en termes de fonctionnalités et de tarification. Les fonctionnalités sont décrites plus loin dans ce guide ; pour plus d'informations sur la tarification, consultez la page [Tarification - Cache][].

Ce guide offre un aperçu de la prise en main de Cache Redis Azure. Pour plus d'informations sur les fonctions qui ne sont pas présentées dans ce guide de prise en main, consultez la page [Présentation de Cache Redis Azure][].

<a name="getting-started-cache-service"></a>
## Prise en main de Cache Redis Azure

La prise en main de Cache Redis Azure est aisée. Pour commencer, vous mettez en service et configurez un cache. Ensuite, vous configurez les clients du cache pour qu'ils puissent accéder au cache. Une fois les clients du cache configurés, vous pouvez commencer à les utiliser.

-	[Création du cache][]
-	[Configuration des clients de cache][]

<a name="create-cache"></a>
## Création d'un cache

Pour créer un cache, connectez-vous au [portail Microsoft Azure en version préliminaire][] et cliquez sur **Nouveau**, **Stockage + Données**, **Cache Redis**.

![New cache][NewCacheMenu]

>[AZURE.NOTE]Si vous ne possédez pas de compte Azure, vous pouvez créer un compte d’évaluation gratuit en quelques minutes. Pour plus d'informations, consultez la page [Version d'évaluation gratuite d'Azure][].

Dans le panneau **Nouveau cache Redis**, spécifiez la configuration souhaitée pour le cache.

![Create cache][CacheCreate]

Dans **Nom DNS**, entrez le nom de sous-domaine à utiliser pour le point de terminaison du cache. Le point de terminaison doit être une chaîne composée de six à vingt caractères, contenant uniquement des minuscules et des chiffres, et commençant par une lettre.

Utilisez **Option de tarification** pour sélectionner la taille du cache et les fonctionnalités appropriées. Les caches **De base** ont un seul nœud de plusieurs tailles, jusqu’à 53 Go. Les caches **Standard** présentent une configuration principal/réplica, avec un contrat SLA de 99,9 % et plusieurs tailles de nœuds, jusqu’à 53 Go.

Dans **Groupe de ressources**, sélectionnez ou créez un groupe de ressources pour le cache.

>[AZURE.NOTE]Pour plus d'informations, consultez la rubrique [Utilisation des groupes de ressources pour gérer vos ressources Azure][].

Dans **Abonnement**, sélectionnez l'abonnement Azure que vous voulez utiliser pour le cache. Si votre compte a un seul abonnement, il sera automatiquement sélectionné et la liste déroulante **Abonnement** ne sera pas affichée.

Utilisez la **Emplacement** pour indiquer l’emplacement géographique de l’hébergement de votre cache. Pour des performances optimales, Microsoft recommande de créer le cache dans la même région que l'application cliente du cache.

Une fois les options du nouveau cache configurées, cliquez sur **Créer**. La création du cache peut prendre plusieurs minutes. Pour vérifier l'état d'avancement de l'opération, vous pouvez consulter le tableau d'accueil. Après sa création, le nouveau cache a le statut **En cours d'exécution** et il est prêt à fonctionner avec les paramètres par défaut.

![Cache created][CacheCreated]

Lorsque le cache est créé, vous pouvez y accéder à partir du volet **Parcourir**.

![Browse blade][BrowseCaches]

Cliquez sur **Caches Redis** pour afficher vos caches.

![Caches][Caches]

<a name="NuGet"></a>
## Configuration des clients de cache

Un cache créé avec Cache Redis Azure est accessible à partir de toutes les applications Azure. Les applications .NET développées dans Visual Studio peuvent utiliser le client du cache **StackExchange.Redis**, qui peut être configuré en utilisant un package NuGet qui simplifie la configuration des applications clientes du cache.

>[AZURE.NOTE]Pour plus d’informations, consultez la page github [StackExchange.Redis][] et la [documentation du client de cache StackExchange.Redis][].

Pour configurer une application cliente dans Visual Studio avec le package NuGet StackExchange.Redis, cliquez avec le bouton droit sur le projet dans l'**Explorateur de solutions** et choisissez **Gérer les packages NuGet**.

![Manage NuGet packages][NuGetMenu]

Tapez **StackExchange.Redis** ou **StackExchange.Redis.StrongName** dans la zone de texte **Rechercher en ligne**, sélectionnez la version dans les résultats et cliquez sur **Installer**.

>[AZURE.NOTE]Si vous préférez utiliser une version avec nom fort de la bibliothèque du client **StackExchange.Redis**, choisissez **StackExchange.Redis.StrongName**. Sinon, choisissez **StackExchange.Redis**.

![StackExchange.Redis NuGet package][StackExchangeNuget]

Le package NuGet télécharge et ajoute les références d'assembly nécessaires pour que votre application cliente puisse accéder à Cache Redis Azure avec le client du cache StackExchange.Redis.

Une fois que votre projet client est configuré pour la mise en cache, vous pouvez utiliser les techniques décrites dans les sections suivantes pour utiliser votre cache.

<a name="working-with-caches"></a>
## Utilisation des caches

Cette section décrit l'exécution des tâches courantes avec Cache.

-	[Connexion au cache][]
-   [Ajout et récupération d’objets dans le cache][]
-   [Stockage de l’état de session ASP.NET dans le cache][]

<a name="connect-to-cache"></a>
## Connexion au cache

Pour utiliser un cache par programmation, vous avez besoin d'une référence au cache. Ajoutez ce qui suit au début des fichiers qui doivent utiliser le client StackExchange.Redis pour accéder à un cache Redis Azure :

    using StackExchange.Redis;

>[AZURE.NOTE]Le client StackExchange.Redis a besoin de .NET Framework 4 ou version ultérieure.

La connexion au Cache Redis Azure est gérée par la classe `ConnectionMultiplexer`. Cette classe est conçue pour être partagée et réutilisée dans toute votre application cliente et ne doit pas être créée pour chaque opération.

Pour vous connecter au Cache Redis Azure et recevoir en retour une instance `ConnectionMultiplexer` connectée, appelez la méthode statique `Connect`, puis passez le point de terminaison et la clé du cache comme dans l’exemple suivant. Utilisez la clé Azure générée sur le portail comme paramètre du mot de passe.

	ConnectionMultiplexer connection = ConnectionMultiplexer.Connect("contoso5.redis.cache.windows.net,ssl=true,password=...");

>[AZURE.IMPORTANT]Avertissement : ne stockez jamais d’informations d’identification dans du code source. Pour ne pas alourdir cet exemple, elles sont montrées dans le code source. Consultez [Fonctionnement des chaînes d’application et de connexion][] pour plus d’informations sur le stockage des informations d’identification.

Si vous ne souhaitez pas utiliser SSL, définissez `ssl=false` ou passez le point de terminaison et la clé.

>[AZURE.NOTE]Le port non SSL est désactivé par défaut pour les nouveaux caches. Pour obtenir des instructions sur l’activation du port non SSL, consultez la section relative aux ports d’accès dans la rubrique [Configurer un cache dans Cache Redis Azure][].

	connection = ConnectionMultiplexer.Connect("contoso5.redis.cache.windows.net,password=...");

Pour plus d'informations sur les options avancées de configuration de connexion, consultez la rubrique [Modèle de configuration StackExchange.Redis][].

Le point de terminaison et les clés du cache peuvent être obtenus dans le panneau **Cache Redis** de votre instance de cache.

![Cache properties][CacheProperties]

![Manage keys][ManageKeys]

Une fois la connexion établie, renvoyez une référence à la base de données du Cache Redis en appelant la méthode `ConnectionMultiplexer.GetDatabase`.

	// connection referes to a previously configured ConnectionMultiplexer
	IDatabase cache = connection.GetDatabase();

>[AZURE.NOTE]L’objet renvoyé à partir de la méthode `GetDatabase` est un objet direct léger qui n’a pas besoin d’être stocké.

	ConnectionMultiplexer connection = ConnectionMultiplexer.Connect("contoso5.redis.cache.windows.net,ssl=true,password=...");

	IDatabase cache = connection.GetDatabase();

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

>[AZURE.NOTE]Redis stocke la plupart des données sous la forme de chaînes Redis, mais ces chaînes peuvent contenir de nombreux types de données, notamment des données binaires sérialisées, qui peuvent être utilisées lors du stockage d'objets .NET dans le cache.

Lors de l’appel de `StringGet`, si l’objet existe, il est renvoyé ; sinon, la valeur Null est renvoyée. Dans ce cas, vous pouvez extraire la valeur de la source de données de votre choix et la stocker dans le cache pour un usage ultérieur. On parle alors de modèle de type cache-aside.

    string value = cache.StringGet("key1");
    if (value == null)
    {
        // The item keyed by "key1" is not in the cache. Obtain
        // it from the desired data source and add it to the cache.
        value = GetValueFromDataSource();

        cache.StringSet("key1", value);
    }

>[AZURE.NOTE]Cache Redis Azure peut mettre en cache des objets .NET comme des types de données primitifs, mais avant qu'un objet .NET puisse être mis en cache, il doit être sérialisé. Cette opération échoit au développeur d’applications, qui a toute latitude pour choisir le sérialiseur. Pour plus d'informations, consultez la rubrique [Utilisation d'objets .NET dans le cache][].

<a name="specify-expiration"></a>
## Spécification de l'expiration d'un élément dans le cache

Pour spécifier l’expiration d’un élément du cache, utilisez le paramètre `TimeSpan` de `StringSet`.

	cache.StringSet("key1", "value1", TimeSpan.FromMinutes(90));


<a name="store-session"></a>
## Stockage de l'état de session ASP.NET dans le cache

Cache Redis Azure intègre un fournisseur d'état de session, qui vous permet de stocker l'état de votre session dans un cache plutôt qu'en mémoire ou dans une base de données SQL Server. Pour utiliser le fournisseur d’état de session de la mise en cache, configurez d’abord votre cache, puis configurez votre application ASP.NET pour la mise en cache à l’aide du package NuGet de l’état de session Cache Redis.

Pour configurer une application cliente dans Visual Studio avec le package NuGet de l'état de session Cache Redis, cliquez avec le bouton droit sur l'**Explorateur de solutions** et choisissez **Gérer les packages NuGet**.

![Manage NuGet packages][NuGetMenu]

Tapez **RedisSessionStateProvider** dans la zone de texte **Rechercher en ligne**, choisissez parmi les résultats et cliquez sur **Installer**.

![Redis Cache Session State NuGet Package][SessionStateNuGet]

Le package NuGet télécharge et ajoute les références d'assembly nécessaires et ajoute la section suivante dans le fichier web.config qui contient la configuration requise pour que votre application ASP.NET utilise le fournisseur d'état de session Cache Redis.

    <sessionState mode="Custom" customProvider="MySessionStateStore">
      <providers>
        <!--
          <add name="MySessionStateStore" 
            host = "127.0.0.1" [String]
            port = "" [number]
            accessKey = "" [String]
            ssl = "false" [true|false]
            throwOnError = "true" [true|false]
            retryTimeoutInMilliseconds = "0" [number]
            databaseId = "0" [number]
            applicationName = "" [String]
            connectionTimeoutInMilliseconds = "5000" [number]
            operationTimeoutInMilliseconds = "5000" [number]
          />
        -->
        <add name="MySessionStateStore" type="Microsoft.Web.Redis.RedisSessionStateProvider" host="127.0.0.1" accessKey="" ssl="false" />
      </providers>
    </sessionState>

La section commentée fournit un exemple d'attributs et de paramétrage.

Configurez les attributs avec les valeurs du panneau de votre cache sur le portail et configurez les autres valeurs selon votre choix.

	<sessionState mode="Custom" customProvider="MySessionStateStore">
      <providers>
        <!--
          <add name="MySessionStateStore" 
            host = "127.0.0.1" [String]
            port = "" [number]
            accessKey = "" [String]
            ssl = "false" [true|false]
            throwOnError = "true" [true|false]
            retryTimeoutInMilliseconds = "0" [number]
            databaseId = "0" [number]
            applicationName = "" [String]
            connectionTimeoutInMilliseconds = "5000" [number]
            operationTimeoutInMilliseconds = "5000" [number]
          />
        -->
        <add name="MySessionStateStore" type="Microsoft.Web.Redis.RedisSessionStateProvider" host="contoso5.redis.cache.windows.net" 
		accessKey="..." ssl="true" />
      </providers>
    </sessionState>

N'oubliez pas de supprimer les marques de commentaire concernant le fournisseur d'état de session standard **InProc**.

    <!-- <sessionState mode="InProc" customProvider="DefaultSessionProvider">
      <providers>
        <add name="DefaultSessionProvider" type="System.Web.Providers.DefaultSessionStateProvider, System.Web.Providers, Version=1.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35" connectionStringName="DefaultConnection" />
      </providers>
    </sessionState> -->

Pour plus d’informations sur la configuration de ces paramètres et l’utilisation du fournisseur d’état de session Redis Azure, consultez [Fournisseur de l’état de session Redis Azure][].

<a name="next-steps"></a>
## Étapes suivantes

Maintenant que vous avez appris les bases du Cache Redis Azure, suivez ces liens pour apprendre à exécuter les tâches de mise en cache plus complexes.

-	[Activez les diagnostics du cache](https://msdn.microsoft.com/library/azure/dn763945.aspx#EnableDiagnostics) afin de pouvoir [surveiller](https://msdn.microsoft.com/library/azure/dn763945.aspx) l’intégrité de votre cache. Vous pouvez afficher les mesures dans le portail, ainsi que les [télécharger et les analyser](https://github.com/rustd/RedisSamples/tree/master/CustomMonitoring) à l’aide des outils de votre choix.
-	Consultez la [Documentation du client du cache StackExchange.Redis][].
	-	Le Cache Redis Azure est accessible depuis de nombreux clients Redis et langages de développement. Pour plus d’informations, consultez [http://redis.io/clients][] et [Développement dans d’autres langages pour le cache Azure Redis][].
	-	Le Cache Redis Azure peut également être utilisé avec des services tels que Redsmin. Pour plus d’informations, consultez la [page expliquant comment récupérer une chaîne de connexion Azure Redis pour l’utiliser avec Redsmin][].
-	Consultez la documentation [redis][] et notamment les [types de données Redis][] et [quinze minutes de présentation des types de données Redis][].
-   Consultez la référence MSDN du [Cache Redis Azure][]. 


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
[Stockage de l’état de session ASP.NET dans le cache]: #store-session

  
<!-- IMAGES -->
[NewCacheMenu]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-new-cache-menu.png

[CacheCreate]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-cache-create.png

[StackExchangeNuget]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-stackexchange-redis.png

[NuGetMenu]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-manage-nuget-menu.png

[CacheProperties]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-properties.png

[ManageKeys]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-manage-keys.png

[SessionStateNuGet]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-session-state-provider.png

[BrowseCaches]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-browse-caches.png

[Caches]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-caches.png

[CacheCreated]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-cache-created.png




   
<!-- LINKS -->
[http://redis.io/clients]: http://redis.io/clients
[Développement dans d’autres langages pour le cache Azure Redis]: http://msdn.microsoft.com/library/azure/dn690470.aspx
[page expliquant comment récupérer une chaîne de connexion Azure Redis pour l’utiliser avec Redsmin]: https://redsmin.uservoice.com/knowledgebase/articles/485711-how-to-connect-redsmin-to-azure-redis-cache
[Fournisseur de l’état de session Redis Azure]: http://go.microsoft.com/fwlink/?LinkId=398249
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
[Configurer un cache dans Cache Redis Azure]: http://msdn.microsoft.com/library/azure/dn793612.aspx

[Modèle de configuration StackExchange.Redis]: http://github.com/StackExchange/StackExchange.Redis/blob/master/Docs/Configuration.md

[Utilisation d'objets .NET dans le cache]: http://msdn.microsoft.com/library/dn690521.aspx#Objects


[NuGet Package Manager Installation]: http://go.microsoft.com/fwlink/?LinkId=240311
[Tarification - Cache]: http://www.windowsazure.com/pricing/details/cache/
[portail Microsoft Azure en version préliminaire]: https://portal.azure.com/

[Présentation de Cache Redis Azure]: http://go.microsoft.com/fwlink/?LinkId=320830
[Cache Redis Azure]: http://go.microsoft.com/fwlink/?LinkId=398247

[Migrate to Azure Redis Cache]: http://go.microsoft.com/fwlink/?LinkId=317347
[Azure Redis Cache Samples]: http://go.microsoft.com/fwlink/?LinkId=320840
[Utilisation des groupes de ressources pour gérer vos ressources Azure]: http://azure.microsoft.com/documentation/articles/resource-group-overview/

[StackExchange.Redis]: http://github.com/StackExchange/StackExchange.Redis
[Documentation du client du cache StackExchange.Redis]: http://github.com/StackExchange/StackExchange.Redis#documentation
[documentation du client de cache StackExchange.Redis]: http://github.com/StackExchange/StackExchange.Redis#documentation

[Redis]: http://redis.io/documentation
[types de données Redis]: http://redis.io/topics/data-types
[quinze minutes de présentation des types de données Redis]: http://redis.io/topics/data-types-intro

[Fonctionnement des chaînes d’application et de connexion]: http://azure.microsoft.com/blog/2013/07/17/windows-azure-web-sites-how-application-strings-and-connection-strings-work/

[Version d'évaluation gratuite d'Azure]: http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=redis_cache_hero

<!---HONumber=July15_HO3-->