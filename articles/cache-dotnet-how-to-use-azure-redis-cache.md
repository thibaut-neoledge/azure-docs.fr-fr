<properties pageTitle="How to Use Azure Redis Cache" metaKeywords="" description="Learn how to create a use a cache in Azure Redis Cache" metaCanonical="" services="" documentationCenter="API Management" title="How to Use Azure Redis Cache" authors="sdanie" solutions="" manager="" editor="" />

<tags ms.service="cache" ms.workload="tbd" ms.tgt_pltfrm="cache-redis" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="sdanie"></tags>

# Utilisation de Cache Redis Azure

Ce guide décrit la prise en main de
**Cache Redis Azure (version préliminaire)**. Les exemples sont écrits en C# et utilisent l'API .NET. Les scénarios couverts incluent **la création et la configuration d'un cache**, **la configuration des clients de cache**, **l'ajout et la suppression d'objets dans le cache** et **le stockage de l'état de session ASP.NET dans le cache**. Pour plus d'informations sur l'utilisation de Cache Redis Azure, consultez la section [Étapes suivantes][Étapes suivantes].

## Sommaire

-   [Présentation de Cache Redis Azure][Présentation de Cache Redis Azure]
-   [Prise en main de Cache Redis Azure][Prise en main de Cache Redis Azure]
    -   [Création du cache][Création du cache]
    -   [Configuration des clients de cache][Configuration des clients de cache]
-   [Utilisation des caches][Utilisation des caches]
    -   [Connexion au cache][Connexion au cache]
    -   [Ajout et récupération d'objets dans le cache][Ajout et récupération d'objets dans le cache]
    -   [Spécification de l'expiration d'un objet dans le cache][Spécification de l'expiration d'un objet dans le cache]
    -   [Stockage de l'état de session ASP.NET dans le cache][Stockage de l'état de session ASP.NET dans le cache]
-   [Étapes suivantes][Étapes suivantes]

<a name="what-is"></a>

## Présentation de Cache Redis Azure

Cache Redis Microsoft Azure, actuellement en version préliminaire, est basé sur l'open source populaire Cache Redis. Il vous donne accès à un cache Redis dédié et sécurisé, qui est géré par Microsoft. Un cache créé avec Cache Redis Azure est accessible à partir de toutes les applications dans Microsoft Azure.

Cache Redis Microsoft Azure est disponible en deux options :

-   **De base**, avec un seul nœud. Plusieurs tailles jusqu'à 26 Go.
-   **Standard**, avec deux nœuds Master/Slave. Plusieurs tailles jusqu'à 26 Go.

Chaque option diffère en termes de fonctionnalités et de tarification. Les fonctionnalités sont décrites plus loin dans ce guide ; pour plus d'informations sur la tarification, consultez la page [Tarification - Cache][Tarification - Cache].

Ce guide offre un aperçu de la prise en main de Cache Redis Azure. Pour plus d'informations sur les fonctions qui ne sont pas présentées dans ce guide de prise en main, consultez la page [Présentation de Cache Redis Azure][1].

<a name="getting-started-cache-service"></a>

## Prise en main de Cache Redis Azure

La prise en main de Cache Redis Azure est aisée. Pour commencer, vous mettez en service et configurez un cache. Ensuite, vous configurez les clients du cache pour qu'ils puissent accéder au cache. Une fois les clients du cache configurés, vous pouvez commencer à les utiliser.

-   [Création du cache][Création du cache]
-   [Configuration du cache][Configuration du cache]
-   [Configuration des clients de cache][Configuration des clients de cache]

<a name="create-cache"></a>

## Création d'un cache

Pour créer un cache, connectez-vous à la version préliminaire du portail de gestion Azure et cliquez sur **Nouveau**, **Cache Redis (version préliminaire)**.

![New cache][New cache]

Dans le volet **Nouveau Cache Redis (version préliminaire)**, indiquez la configuration souhaitée pour le cache.

![Create cache][Create cache]

Dans **Nom DNS**, entrez le nom de sous-domaine à utiliser pour le point de terminaison du cache. Le point de terminaison doit être une chaîne composée de six à vingt caractères, contenant uniquement des minuscules et des chiffres, et commençant par une lettre.

Utilisez **Option de tarification** pour sélectionner la taille du cache et les fonctionnalités appropriées. Cache Redis est disponible dans les deux options suivantes.

-   **De base**, avec un seul nœud et plusieurs tailles jusqu'à 26 Go.
-   **Standard**, avec deux nœuds master/slave, un niveau de service à 99,9 % (après la version préliminaire), plusieurs tailles jusqu'à 26 Go.

Dans **Abonnement**, sélectionnez l'abonnement Azure que vous voulez utiliser pour le cache.

> Si votre compte a un seul abonnement, il sera automatiquement sélectionné et la liste déroulante Abonnement ne sera pas affichée.

Dans **Groupe de ressources**, sélectionnez ou créez un groupe de ressources pour le cache.

> Pour plus d'informations, consultez la rubrique [Utilisation des groupes de ressources pour gérer vos ressources Azure][Utilisation des groupes de ressources pour gérer vos ressources Azure].

Utilisez la **géolocalisation** pour indiquer l'emplacement géographique de l'hébergement du cache. Pour des performances optimales, Microsoft recommande de créer le cache dans la même région que l'application cliente du cache.

Une fois les options du nouveau cache configurées, cliquez sur **Créer**. La création du cache peut prendre plusieurs minutes. Pour vérifier l'état d'avancement de l'opération, vous pouvez consulter le tableau d'accueil. Après sa création, le nouveau cache a le statut **En cours d'exécution** et il est prêt à fonctionner avec les paramètres par défaut.

![Cache created][Cache created]

Lorsque le cache est créé, vous pouvez y accéder à partir du volet **Parcourir**.

![Browse blade][Browse blade]

Cliquez sur **Caches** pour afficher vos caches.

![Caches][Caches]

<a name="NuGet"></a>

## Configuration des clients de cache

Un cache créé avec Cache Redis Azure est accessible à partir de toutes les applications Azure. Les applications .NET développées dans Visual Studio peuvent utiliser le client du cache **StackExchange.Redis**, qui peut être configuré en utilisant un package NuGet qui simplifie la configuration des applications clientes du cache.

> Pour plus d'informations, consultez la page github [StackExchange.Redis][StackExchange.Redis] et la rubrique [Documentation du client du cache StackExchange.Redis][Documentation du client du cache StackExchange.Redis].

Pour configurer une application cliente dans Visual Studio avec le package NuGet StackExchange.Redis, cliquez avec le bouton droit sur le projet dans l'**Explorateur de solutions** et choisissez **Gérer les packages NuGet**.

![Manage NuGet packages][Manage NuGet packages]

Tapez **StackExchange.Redis** ou **StackExchange.Redis.StrongName** dans la zone de texte **Rechercher en ligne**, sélectionnez la version dans les résultats et cliquez sur **Installer**.

> Si vous préférez utiliser une version avec nom fort de la bibliothèque du client **StackExchange.Redis**, choisissez **StackExchange.Redis.StrongName**. Sinon, choisissez **StackExchange.Redis**.

![StackExchange.Redis NuGet package][StackExchange.Redis NuGet package]

Le package NuGet télécharge et ajoute les références d'assembly nécessaires pour que votre application cliente puisse accéder à Cache Redis Azure avec le client du cache StackExchange.Redis.

Une fois que votre projet client est configuré pour la mise en cache, vous pouvez utiliser les techniques décrites dans les sections suivantes pour utiliser votre cache.

<a name="working-with-caches"></a>

## Utilisation des caches

Cette section décrit l'exécution des tâches courantes avec Cache.

-   [Connexion au cache][Connexion au cache]
-   [Ajout et récupération d'objets dans le cache][Ajout et récupération d'objets dans le cache]
-   [Stockage de l'état de session ASP.NET dans le cache][Stockage de l'état de session ASP.NET dans le cache]

<a name="connect-to-cache"></a>

## Connexion au cache

Pour utiliser un cache par programme, vous avez besoin d'une référence au cache. Ajoutez ce qui suit au début des fichiers qui doivent utiliser le client StackExchange.Redis pour accéder à un cache Redis Azure :

    using StackExchange.Redis;

> Le client StackExchange.Redis a besoin de .NET Framework 4 ou version ultérieure.

La connexion à Cache Redis Azure est gérée par la classe `ConnectionMultiplexer`. Cette classe est conçue pour être partagée et réutilisée dans toute votre application cliente et ne doit pas être créée pour chaque opération.

Pour vous connecter à Cache Redis Azure et recevoir en retour une instance d'une classe `ConnectionMultiplexer` connectée, appelez la méthode statique `Connect` et passez le point de terminaison du cache et la clé comme dans l'exemple suivant. Utilisez la clé Azure générée sur le portail du service comme paramètre du mot de passe.

    ConnectionMultiplexer connection = ConnectionMultiplexer.Connect("contoso5.redis.cache.windows.net,ssl=true,password=...");

> Avertissement : ne stockez jamais des informations d'identification dans du code source. Pour ne pas alourdir cet exemple, elles sont montrées dans le code source. Consultez la rubrique [Sites web Windows Azure : fonctionnement des chaînes d'application et de connexion][Sites web Windows Azure : fonctionnement des chaînes d'application et de connexion] pour plus d'informations sur le stockage des informations d'identification.

Si vous ne souhaitez pas utiliser SSL, définissez `ssl=false` ou passez le point de terminaison et la clé.

    connection = ConnectionMultiplexer.Connect("contoso5.redis.cache.windows.net,password=...");

> Pour plus d'informations sur les options avancées de configuration de connexion, consultez la rubrique [Modèle de configuration StackExchange.Redis][Modèle de configuration StackExchange.Redis].

Le point de terminaison et les clés du cache peuvent être obtenus dans le volet de la version préliminaire du portail de gestion Azure pour votre instance de cache.

![Cache properties][Cache properties]

![Manage keys][Manage keys]

Lorsque la connexion est établie, renvoyez une référence à la base de données du cache Redis en appelant la méthode `ConnectionMultiplexer.GetDatabase`.

    // connection referes to a previously configured ConnectionMultiplexer
    IDatabase cache = connection.GetDatabase();

> L'objet renvoyé à partir de la méthode `GetDatabase` est un objet direct léger qui n'a pas besoin d'être stocké.

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

> Redis stocke la plupart des données sous la forme de chaînes Redis, mais ces chaînes peuvent contenir de nombreux types de données, notamment des données binaires sérialisées, qui peuvent être utilisées lors du stockage d'objets .NET dans le cache.

Lors de l'appel de `StringGet`, si l'objet existe, il est renvoyé ; sinon, la valeur null est renvoyée. Dans ce cas, vous pouvez extraire la valeur de la source de données de votre choix et la stocker dans le cache pour un usage ultérieur. On parle alors de modèle de type cache-aside.

    string value = cache.StringGet("key1");
    if (value == null)
    {
        // The item keyed by "key1" is not in the cache. Obtain
        // it from the desired data source and add it to the cache.
        value = GetValueFromDataSource();

        cache.StringSet("key1", value);
    }

> Cache Redis Azure peut mettre en cache des objets .NET comme des types de données primitifs, mais avant qu'un objet .NET puisse être mis en cache, il doit être sérialisé. Cette opération échoit au développeur d’applications, qui a toute latitude pour choisir le sérialiseur. Pour plus d'informations, consultez la rubrique [Utilisation d'objets .NET dans le cache][Utilisation d'objets .NET dans le cache].

<a name="specify-expiration"></a>

## Spécification de l'expiration d'un élément dans le cache

Pour spécifier l'expiration d'un élément dans le cache, utilisez le paramètre `TimeSpan` `StringSet`.

    cache.StringSet("key1", "value1", TimeSpan.FromMinutes(90));

<a name="store-session"></a>

## Stockage de l'état de session ASP.NET dans le cache

Cache Redis Azure intègre un fournisseur d'état de session, qui vous permet de stocker l'état de votre session dans un cache plutôt qu'en mémoire ou dans une base de données SQL Server. Pour utiliser le
fournisseur d'état de session de la mise en cache, configurez d'abord votre cache, puis configurez votre application ASP.NET pour la mise en cache à l'aide du package NuGet de l'état de session Cache Redis.

Pour configurer une application cliente dans Visual Studio avec le package NuGet de l'état de session Cache Redis, cliquez avec le bouton droit sur l'**Explorateur de solutions** et choisissez **Gérer les packages NuGet**.

![Manage NuGet packages][Manage NuGet packages]

Tapez **RedisSessionStateProvider** dans la zone de texte **Rechercher en ligne**, choisissez parmi les résultats et cliquez sur **Installer**.

![Redis Cache Session State NuGet Package][Redis Cache Session State NuGet Package]

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
          />
        -->
        <add name="MySessionStateStore" type="Microsoft.Web.Redis.RedisSessionStateProvider" host="127.0.0.1" accessKey="" ssl="false" />
      </providers>
    </sessionState>

La section commentée fournit un exemple d'attributs et de paramétrage.

Configurez les attributs avec les valeurs du volet de votre cache sur la version préliminaire du portail et configurez les autres valeurs selon votre choix.

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
          />
        -->
        <add name="MySessionStateStore" type="Microsoft.Web.Redis.RedisSessionStateProvider" host="contoso5.redis.cache.windows.net" 
        accessKey="..." ssl="false" />
      </providers>
    </sessionState>

> Notez que le délai d'expiration de nouvelle tentative est exprimé en millisecondes.

N'oubliez pas de supprimer les marques de commentaire concernant le fournisseur d'état de session standard **InProc**.

    <!-- <sessionState mode="InProc" customProvider="DefaultSessionProvider">
      <providers>
        <add name="DefaultSessionProvider" type="System.Web.Providers.DefaultSessionStateProvider, System.Web.Providers, Version=1.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35" connectionStringName="DefaultConnection" />
      </providers>
    </sessionState> -->

Pour plus d'informations sur la configuration et l'utilisation du fournisseur d'état de session Azure Redis, consultez la rubrique [Fournisseur de l'état de session Azure Redis][Fournisseur de l'état de session Azure Redis].

<a name="next-steps"></a>

## Étapes suivantes

Maintenant que vous avez appris les bases de Cache Redis Azure,
suivez ces liens pour apprendre à exécuter les tâches de mise en cache plus complexes.

-   Pour en savoir plus sur le client StackExchange.Redis, consultez : [Documentation du client du cache StackExchange.Redis][Documentation du client du cache StackExchange.Redis]
-   Consultez la documentation [redis][redis] et notamment les [types de données Redis][types de données Redis] et [quinze minutes de présentation des types de données Redis][quinze minutes de présentation des types de données Redis].
-   Consultez la référence MSDN suivante : [Cache Redis Azure][Cache Redis Azure]



<!-- INTRA-TOPIC LINKS -->

  [Étapes suivantes]: #next-steps
  [Présentation de Cache Redis Azure]: #what-is
  [Prise en main de Cache Redis Azure]: #getting-started-cache-service
  [Création du cache]: #create-cache
  [Configuration des clients de cache]: #NuGet
  [Utilisation des caches]: #working-with-caches
  [Connexion au cache]: #connect-to-cache
  [Ajout et récupération d'objets dans le cache]: #add-object
  [Spécification de l'expiration d'un objet dans le cache]: #specify-expiration
  [Stockage de l'état de session ASP.NET dans le cache]: #store-session

<!-- LINKS -->

  [Tarification - Cache]: http://www.windowsazure.com/fr-fr/pricing/details/cache/
  [1]: http://go.microsoft.com/fwlink/?LinkId=320830
  [Configuration du cache]: #enable-caching
  [New cache]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-new-cache-menu.png
  [Utilisation des groupes de ressources pour gérer vos ressources Azure]: http://azure.microsoft.com/fr-fr/documentation/articles/azure-preview-portal-using-resource-groups/
  [StackExchange.Redis]: http://github.com/StackExchange/StackExchange.Redis
  [Documentation du client du cache StackExchange.Redis]: http://github.com/StackExchange/StackExchange.Redis#documentation
  [Utilisation d'objets .NET dans le cache]: http://msdn.microsoft.com/fr-fr/library/dn690521.aspx#Objects
  [Fournisseur de l'état de session Azure Redis]: http://go.microsoft.com/fwlink/?LinkId=398249
  [redis]: http://redis.io/documentation
  [types de données Redis]: http://redis.io/topics/data-types
  [quinze minutes de présentation des types de données Redis]: http://redis.io/topics/data-types-intro
  [Cache Redis Azure]: http://go.microsoft.com/fwlink/?LinkId=398247

<!-- IMAGES -->

  [Create cache]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-cache-create.png
  [Cache created]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-cache-created.png
  [Browse blade]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-browse-caches.png
  [Caches]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-caches.png
  [Manage NuGet packages]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-manage-nuget-menu.png
  [StackExchange.Redis NuGet package]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-stackexchange-redis.png
  [Cache properties]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-properties.png
  [Manage keys]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-manage-keys.png
  [Redis Cache Session State NuGet Package]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-session-state-provider.png

  [Sites web Windows Azure : fonctionnement des chaînes d'application et de connexion]: http://azure.microsoft.com/blog/2013/07/17/windows-azure-web-sites-how-application-strings-and-connection-strings-work/
  [Modèle de configuration StackExchange.Redis]: http://github.com/StackExchange/StackExchange.Redis/blob/master/Docs/Configuration.md

