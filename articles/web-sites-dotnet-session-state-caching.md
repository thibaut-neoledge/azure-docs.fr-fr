<properties urlDisplayName="index" pageTitle="Utilisation de l'état de session ASP.NET avec Sites Web Azure" metaKeywords="azure cache service session state" description="Découvrez comment utiliser Azure Cache Service pour prendre en charge la mise en cache de l'état de session ASP.NET." metaCanonical="" services="cache" documentationCenter=".NET" title="How to Use ASP.NET Session State with Azure Websites" authors="riande"  solutions="" manager="wpickett" editor="mollybos"  />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="10/13/2014" ms.author="riande" />


# Utilisation de l'état de session ASP.NET avec Sites Web Azure

*Par [Rick Anderson](https://twitter.com/RickAndMSFT). Mis à jour le 1er juillet 2014.*

Cette rubrique vous explique comment utiliser le service Cache Redis Azure (Preview) pour l'état de session.

Si votre application web ASP.NET utilise l'état de session, vous devez configurer un fournisseur d'état de session externe (le service Redis Cache ou un fournisseur d'état de session SQL). Si vous utilisez l'état de session mais aucun fournisseur externe, vous êtes limité à une seule instance de votre application web. Le service Redis Cache est la solution la plus rapide et la plus simple.

La procédure de base pour utiliser Cache Service (Preview) pour la mise en cache de l'état de session comprend les étapes suivantes :

* [Création du cache](#createcache)
* [Ajout du package NuGet RedisSessionStateProvider à votre application web](#configureproject)
* [Modification du fichier web.config](#configurewebconfig)
* [Utilisation de l'objet Session pour stocker et récupérer des éléments mis en cache](#usesessionobject)

<h2><a id="createcache"></a>Création du cache</h2>
Suivez les [instructions indiquées](http://azure.microsoft.com/fr-fr/documentation/articles/cache-dotnet-how-to-use-azure-redis-cache/#create-cache) pour créer le cache.

<h2><a id="configureproject"></a>Ajout du package NuGet RedisSessionStateProvider à votre application web</h2>
Installez le package NuGet " RedisSessionStateProvider ".  Utilisez la commande suivante pour effectuer l'installation à partir de la console du gestionnaire de package : (**Outils** > **Gestionnaire de package NuGet** > **Console du gestionnaire de package**) :

  `PM> Install-Package RedisSessionStateProvider -IncludePrerelease`
  
Pour effectuer l'installation à partir d'**Outils** > **Gestionnaire de package NuGet** > **Gérer les packages NuGet pour la solution**, recherchez " RedisSessionStateProvider " en veillant à spécifier **Inclure la version préliminaire**.

Pour plus d'informations, consultez la [page NuGet RedisSessionStateProvider](http://www.nuget.org/packages/Microsoft.Web.RedisSessionStateProvider/ ) et [Configuration des clients du cache](http://azure.microsoft.com/fr-fr/documentation/articles/cache-dotnet-how-to-use-azure-redis-cache/#NuGet).

<h2><a id="configurewebconfig"></a>Modification du fichier web.config</h2>
Outre la création de références d'assembly pour le cache, le package NuGet ajoute des entrées de stub dans le fichier *web.config*. 

1. Ouvrez le fichier *web.config*, puis recherchez l'élément **sessionState**.

1. Entrez les valeurs pour " host ", " accessKey ", " port " (le port SSL doit avoir la valeur 6380), et définissez " SSL " sur " true ". Ces valeurs peuvent être obtenues dans le panneau de la version préliminaire du portail de gestion Azure pour votre instance de cache. Pour plus d'informations, consultez la page [Connexion au cache](http://azure.microsoft.com/fr-fr/documentation/articles/cache-dotnet-how-to-use-azure-redis-cache/#connect-to-cache).
Le balisage suivant indique les modifications apportées au fichier *web.config*.


  <pre class="prettyprint">  
    <system.web>
    <customErrors mode="Off" />
    <authentication mode="None" />
    <compilation debug="true" targetFramework="4.5" />
    <httpRuntime targetFramework="4.5" />
  <sessionState mode="Custom" customProvider="RedisSessionProvider">
      <providers>  
          <!--<add name="RedisSessionProvider" 
            host = "127.0.0.1" [String]
            port = "" [number]
            accessKey = "" [String]
            ssl = "false" [true|false]
            throwOnError = "true" [true|false]
            retryTimeoutInMilliseconds = "0" [number]
            databaseId = "0" [number]
            applicationName = "" [String]
          />-->
         <add name="RedisSessionProvider" 
              type="Microsoft.Web.Redis.RedisSessionStateProvider" 
              <mark>port="6380"
              host="movie2.redis.cache.windows.net" 
              accessKey="m7PNV60CrvKpLqMUxosC3dSe6kx9nQ6jP5del8TmADk=" 
              ssl="true"</mark> />
      <!--<add name="MySessionStateStore" type="Microsoft.Web.Redis.RedisSessionStateProvider" host="127.0.0.1" accessKey="" ssl="false" />-->
      </providers>
    </sessionState>
  </system.web></pre>


<h2><a id="usesessionobject"></a>Utilisation de l'objet Session dans le code</h2>
La dernière étape consiste à utiliser l'objet Session dans votre code ASP.NET. Pour ajouter des objets à l'état de session, utilisez la méthode **Session.Add**. Cette méthode utilise des paires clé-valeur pour stocker des éléments dans le cache d'état de session.

    string strValue = "yourvalue";
	Session.Add("yourkey", strValue);

Le code suivant récupère cette valeur dans l'état de session.

    object objValue = Session["yourkey"];
    if (objValue != null)
       strValue = (string)obj;	

Vous pouvez également utiliser le Cache Redis pour mettre en cache des objets dans votre application web. Pour plus d'informations, consultez la page [Application MVC dédiée aux films avec Cache Redis Azure en 15 minutes](http://azure.microsoft.com/blog/2014/06/05/mvc-movie-app-with-azure-redis-cache-in-15-minutes/).
Pour plus d'informations sur l'utilisation de l'état de session ASP.NET, consultez la page [Vue d'ensemble de l'état de session ASP.NET][].

  
  
  [installé la dernière version]: http://www.windowsazure.com/fr-fr/downloads/?sdk=net  
  [Vue d'ensemble de l'état de session ASP.NET]: http://msdn.microsoft.com/fr-fr/library/ms178581.aspx

  [NewIcon]: ./media/web-sites-dotnet-session-state-caching/CacheScreenshot_NewButton.png
  [NewCacheDialog]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_CreateOptions.png
  [CacheIcon]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_CacheIcon.png
  [NuGetDialog]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_NuGet.png
  [OutputConfig]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_OC_WebConfig.png
  [CacheConfig]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_CacheConfig.png
  [EndpointURL]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_EndpointURL.png
  [ManageKeys]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_ManageAccessKeys.png
