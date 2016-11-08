---
title: État de session avec Cache Redis Azure dans Azure App Service
description: Découvrez comment utiliser Azure Cache Service pour prendre en charge la mise en cache de l’état de session ASP.NET.
services: app-service\web
documentationcenter: .net
author: Rick-Anderson
manager: wpickett
editor: none

ms.service: app-service-web
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: get-started-article
ms.date: 06/27/2016
ms.author: riande

---
# État de session avec Cache Redis Azure dans Azure App Service
Cette rubrique explique comment utiliser le service Cache Redis Azure pour l’état de session.

Si votre application web ASP.NET utilise l'état de session, vous devez configurer un fournisseur d'état de session externe (le service Redis Cache ou un fournisseur d'état de session SQL). Si vous utilisez l'état de session mais aucun fournisseur externe, vous êtes limité à une seule instance de votre application web. Le service Redis Cache est la solution la plus rapide et la plus simple.

[!INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## <a id="createcache"></a>Créer le cache
Suivez les [instructions indiquées](../redis-cache/cache-dotnet-how-to-use-azure-redis-cache.md#create-cache) pour créer le cache.

## <a id="configureproject"></a>Ajouter le package NuGet RedisSessionStateProvider à votre application web
Installez le package NuGet `RedisSessionStateProvider`. Utilisez la commande suivante pour effectuer l'installation à partir de la console du gestionnaire de package : (**Outils** > **Gestionnaire de package NuGet** > **Console du gestionnaire de package**) :

  `PM> Install-Package Microsoft.Web.RedisSessionStateProvider`

Pour effectuer l’installation à partir d’**Outils** > **Gestionnaire de package NuGet** > **Gérer les packages NuGet** pour la solution, recherchez `RedisSessionStateProvider`.

Pour plus d'informations, consultez la [page NuGet RedisSessionStateProvider](http://www.nuget.org/packages/Microsoft.Web.RedisSessionStateProvider/) et [Configuration des clients du cache](../redis-cache/cache-dotnet-how-to-use-azure-redis-cache.md#NuGet).

## <a id="configurewebconfig"></a>Modifier le fichier web.config
Outre la création de références d'assembly pour le cache, le package NuGet ajoute des entrées de stub dans le fichier *web.config*.

1. Ouvrez le fichier *web.config*, puis recherchez l'élément **sessionState**.
2. Entrez les valeurs appropriées pour `host`, `accessKey`, `port` (le port SSL doit être 6380), puis affectez à `SSL` la valeur `true`. Ces valeurs peuvent être obtenues à partir du panneau du [portail Azure](http://go.microsoft.com/fwlink/?LinkId=529715) pour votre instance de cache. Pour plus d’informations, consultez [Connexion au cache](../redis-cache/cache-dotnet-how-to-use-azure-redis-cache.md#connect-to-cache). Notez que le port non-SSL est désactivé par défaut pour les nouveaux caches. Pour plus d’informations sur l’activation du port non SSL, consultez la section relative aux [ports d’accès](https://msdn.microsoft.com/library/azure/dn793612.aspx#AccessPorts) dans la rubrique [Configuration d’un cache dans le Cache Redis Azure](https://msdn.microsoft.com/library/azure/dn793612.aspx). Le balisage suivant indique les modifications apportées au fichier *web.config*, en particulier les modifications apportées à *port*, *host*, accessKey* et *ssl*.
   
          <system.web>;
            <customErrors mode="Off" />;
            <authentication mode="None" />;
            <compilation debug="true" targetFramework="4.5" />;
            <httpRuntime targetFramework="4.5" />;
            <sessionState mode="Custom" customProvider="RedisSessionProvider">;
              <providers>;  
                  <!--<add name="RedisSessionProvider" 
                    host = "127.0.0.1" [String]
                    port = "" [number]
                    accessKey = "" [String]
                    ssl = "false" [true|false]
                    throwOnError = "true" [true|false]
                    retryTimeoutInMilliseconds = "0" [number]
                    databaseId = "0" [number]
                    applicationName = "" [String]
                  />;-->;
                 <add name="RedisSessionProvider" 
                      type="Microsoft.Web.Redis.RedisSessionStateProvider" 
                      port="6380"
                      host="movie2.redis.cache.windows.net" 
                      accessKey="m7PNV60CrvKpLqMUxosC3dSe6kx9nQ6jP5del8TmADk=" 
                      ssl="true" />;
              <!--<add name="MySessionStateStore" type="Microsoft.Web.Redis.RedisSessionStateProvider" host="127.0.0.1" accessKey="" ssl="false" />;-->;
              </providers>;
            </sessionState>;
          </system.web>;

## <a id="usesessionobject"></a> Utiliser l’objet Session dans le code
La dernière étape consiste à utiliser l'objet Session dans votre code ASP.NET. Pour ajouter des objets à l'état de session, utilisez la méthode **Session.Add**. Cette méthode utilise des paires clé-valeur pour stocker des éléments dans le cache d'état de session.

    string strValue = "yourvalue";
    Session.Add("yourkey", strValue);

Le code suivant récupère cette valeur dans l'état de session.

    object objValue = Session["yourkey"];
    if (objValue != null)
       strValue = (string)objValue;    

Vous pouvez également utiliser le Cache Redis pour mettre en cache des objets dans votre application web. Pour plus d’informations, consultez [Application MVC dédiée aux films avec Cache Redis Azure en 15 minutes](https://azure.microsoft.com/blog/2014/06/05/mvc-movie-app-with-azure-redis-cache-in-15-minutes/). (en anglais) Pour plus d'informations sur l'utilisation de l'état de session ASP.NET, consultez la page [Vue d'ensemble de l'état de session ASP.NET][Vue d'ensemble de l'état de session ASP.NET].

> [!NOTE]
> Si vous voulez vous familiariser avec Azure App Service avant d’ouvrir un compte Azure, accédez à la page [Essayer App Service](http://go.microsoft.com/fwlink/?LinkId=523751). Vous pourrez créer immédiatement et gratuitement une application de départ temporaire dans App Service. Aucune carte de crédit n’est requise ; vous ne prenez aucun engagement.
> 
> 

## Changements apportés
* Pour obtenir un guide présentant les modifications apportées dans le cadre de la transition entre Sites Web et App Service, consultez la page [Azure App Service et les services Azure existants](http://go.microsoft.com/fwlink/?LinkId=529714).
  
  *Par [Rick Anderson](https://twitter.com/RickAndMSFT)*

[installed the latest]: http://www.windowsazure.com/downloads/?sdk=net
[Vue d'ensemble de l'état de session ASP.NET]: http://msdn.microsoft.com/library/ms178581.aspx

[NewIcon]: ./media/web-sites-dotnet-session-state-caching/CacheScreenshot_NewButton.png
[NewCacheDialog]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_CreateOptions.png
[CacheIcon]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_CacheIcon.png
[NuGetDialog]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_NuGet.png
[OutputConfig]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_OC_WebConfig.png
[CacheConfig]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_CacheConfig.png
[EndpointURL]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_EndpointURL.png
[ManageKeys]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_ManageAccessKeys.png


<!---HONumber=AcomDC_0706_2016-->