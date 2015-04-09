<properties 
	pageTitle="État de session avec Cache Redis Azure dans Azure App Service" 
	description="Découvrez comment utiliser Azure Cache Service pour prendre en charge la mise en cache de l'état de session ASP.NET." 
	services="app-service\web" 
	documentationCenter=".net" 
 	authors="Rick-Anderson" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="03/24/2015" 
	ms.author="riande"/>


# État de session avec Cache Redis Azure dans Azure App Service


Cette rubrique explique comment utiliser le service Cache Redis Azure pour l'état de session.

Si votre application web ASP.NET utilise l'état de session, vous devez configurer un fournisseur d'état de session externe (le service Redis Cache ou un fournisseur d'état de session SQL). Si vous utilisez l'état de session mais aucun fournisseur externe, vous êtes limité à une seule instance de votre application web. Le service Redis Cache est la solution la plus rapide et la plus simple.

<h2><a id="createcache"></a>Créer le cache</h2>
Suivez [les instructions fournies](cache-dotnet-how-to-use-azure-redis-cache.md#create-cache) pour créer le cache.

<h2><a id="configureproject"></a>Ajouter le package NuGet RedisSessionStateProvider à votre application web</h2>
Installez le package NuGet `RedisSessionStateProvider`.  Utilisez la commande suivante pour effectuer l'installation à partir de la console du gestionnaire de package (**Outils** > **Gestionnaire de package NuGet** > **Console du Gestionnaire de package**) :

  `PM> Install-Package Microsoft.Web.RedisSessionStateProvider`
  
Pour effectuer l'installation à partir d'**Outils** > **Gestionnaire de package NuGet** > **Gérer les packages NugGet pour la solution**, recherchez `RedisSessionStateProvider`.

Pour plus d'informations, voir la [page NuGet RedisSessionStateProvider (en anglais)](http://www.nuget.org/packages/Microsoft.Web.RedisSessionStateProvider/ ) et [Configurer les clients du cache](cache-dotnet-how-to-use-azure-redis-cache.md#NuGet).

<h2><a id="configurewebconfig"></a>Modifier le fichier Web.Config</h2>
Outre la création de références d'assembly pour le cache, le package NuGet ajoute des entrées de stub dans le fichier *web.config*. 

1. Ouvrez le fichier *web.config*, puis recherchez l'élément **sessionState**.

1. Entrez les valeurs appropriées pour `host`, `accessKey`, `port` (le port SSL doit être 6380), puis définissez `SSL` sur `true`. Ces valeurs peuvent être obtenues à partir du panneau du [portail Azure](http://go.microsoft.com/fwlink/?LinkId=529715) pour votre instance de cache. Pour plus d'informations, voir [Se connecter au cache](cache-dotnet-how-to-use-azure-redis-cache.md#connect-to-cache). Notez que le port non-SSL est désactivé par défaut pour les nouveaux caches. Pour plus d'informations sur l'activation du port non-SSL, voir la section relative aux [ports d'accès](https://msdn.microsoft.com/library/azure/dn793612.aspx#AccessPorts) dans la rubrique [Configurer un cache dans Cache Redis Azure (en anglais)](https://msdn.microsoft.com/library/azure/dn793612.aspx). Le balisage suivant présente les modifications apportées au fichier *web.config*.


  <pre class="prettyprint">  
    &lt;system.web&gt;
    &lt;customErrors mode="Off" /&gt;
    &lt;authentication mode="None" /&gt;
    &lt;compilation debug="true" targetFramework="4.5" /&gt;
    &lt;httpRuntime targetFramework="4.5" /&gt;
  &lt;sessionState mode="Custom" customProvider="RedisSessionProvider"&gt;
      &lt;providers&gt;  
          &lt;!--&lt;add name="RedisSessionProvider" 
            host = "127.0.0.1" [String]
            port = "" [number]
            accessKey = "" [String]
            ssl = "false" [true|false]
            throwOnError = "true" [true|false]
            retryTimeoutInMilliseconds = "0" [number]
            databaseId = "0" [number]
            applicationName = "" [String]
          /&gt;--&gt;
         &lt;add name="RedisSessionProvider" 
              type="Microsoft.Web.Redis.RedisSessionStateProvider" 
              <mark>port="6380"
              host="movie2.redis.cache.windows.net" 
              accessKey="m7PNV60CrvKpLqMUxosC3dSe6kx9nQ6jP5del8TmADk=" 
              ssl="true"</mark> /&gt;
      &lt;!--&lt;add name="MySessionStateStore" type="Microsoft.Web.Redis.RedisSessionStateProvider" host="127.0.0.1" accessKey="" ssl="false" /&gt;--&gt;
      &lt;/providers&gt;
    &lt;/sessionState&gt;
  &lt;/system.web&gt;</pre>


<h2><a id="usesessionobject"></a>Utiliser l'objet Session dans le code</h2>
La dernière étape consiste à utiliser l'objet Session dans votre code ASP.NET. Vous ajoutez des objets à l'état de session à l'aide de la méthode **Session.Add**. Cette méthode utilise des paires clé-valeur pour stocker des éléments dans le cache d'état de session.

    string strValue = "yourvalue";
	Session.Add("yourkey", strValue);

The following code retrieves this value from session state.

    object objValue = Session["yourkey"];
    if (objValue != null)
       strValue = (string)objValue;	

Vous pouvez également utiliser le Cache Redis pour mettre en cache des objets dans votre application web. Pour plus d'informations, voir [Application MVC dédiée aux films avec Cache Redis Azure en 15 minutes (en anglais)](http://azure.microsoft.com/blog/2014/06/05/mvc-movie-app-with-azure-redis-cache-in-15-minutes/).
Pour plus d'informations sur l'utilisation de l'état de session ASP.NET, voir [Vue d'ensemble de l'état de session ASP.NET][].

>[AZURE.NOTE] Si vous souhaitez commencer à utiliser Azure App Service avant d'ouvrir un compte Azure, accédez au site permettant d'[essayer App Service](http://go.microsoft.com/fwlink/?LinkId=523751), où vous pourrez créer immédiatement une application web de départ de courte durée dans App Service. Aucune carte de crédit n'est requise, et vous ne prenez aucun engagement.

## Nouveautés
* Pour plus d'informations sur le remplacement de Sites Web par App Service, voir : [Azure App Service et son impact sur les services Azure existants (en anglais)](http://go.microsoft.com/fwlink/?LinkId=529714)
* Pour plus d'informations sur le remplacement de l'ancien portail par le nouveau portail, voir : [Référence en matière de navigation dans le portail en version préliminaire (en anglais)](http://go.microsoft.com/fwlink/?LinkId=529715)

  *Par [Rick Anderson](https://twitter.com/RickAndMSFT)*
  
  [installation de la dernière version]: http://www.windowsazure.com/downloads/?sdk=net  
  [Vue d'ensemble de l'état de session ASP.NET]: http://msdn.microsoft.com/library/ms178581.aspx

  [NewIcon]: ./media/web-sites-dotnet-session-state-caching/CacheScreenshot_NewButton.png
  [NewCacheDialog]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_CreateOptions.png
  [CacheIcon]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_CacheIcon.png
  [NuGetDialog]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_NuGet.png
  [OutputConfig]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_OC_WebConfig.png
  [CacheConfig]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_CacheConfig.png
  [EndpointURL]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_EndpointURL.png
  [ManageKeys]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_ManageAccessKeys.png

<!--HONumber=49-->