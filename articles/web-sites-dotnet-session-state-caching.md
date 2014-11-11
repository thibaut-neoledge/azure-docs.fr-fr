<properties linkid="video-center-index" urlDisplayName="index" pageTitle="Use ASP.NET session state with Azure Websites" metaKeywords="azure cache service session state" description="Learn how to use the Azure Cache Service to support ASP.NET session state caching." metaCanonical="" services="cache" documentationCenter=".NET" title="How to Use ASP.NET Session State with Azure Websites" authors="tdykstra"  solutions="" manager="wpickett" editor="mollybos"  />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="tdykstra" />

# Utilisation de l'état de session ASP.NET avec Sites Web Azure

*Par [Rick Anderson][Rick Anderson]. Mis à jour le 1er juillet 2014.*

Cette rubrique vous explique comment utiliser le service Cache Redis Azure (Preview) pour l'état de session.

Si votre application web ASP.NET utilise l'état de session, vous devez configurer un fournisseur d'état de session externe (le service Redis Cache ou un fournisseur d'état de session SQL). Si vous utilisez l'état de session mais aucun fournisseur externe, vous êtes limité à une seule instance de votre application web. Le service Redis Cache est la solution la plus rapide et la plus simple.

La procédure de base pour utiliser Cache Service (Preview) pour la mise en cache de l'état de session comprend les étapes suivantes :

-   [Création du cache][Création du cache]
-   [Ajout du package NuGet RedisSessionStateProvider à votre application web][Ajout du package NuGet RedisSessionStateProvider à votre application web]
-   [Modification du fichier web.config][Modification du fichier web.config]
-   [Utilisation de l'objet Session pour stocker et récupérer des éléments mis en cache][Utilisation de l'objet Session pour stocker et récupérer des éléments mis en cache]

## <span id="createcache"></span></a>Créer le cache

Suivez les [instructions indiquées][instructions indiquées] pour créer le cache.

## <span id="configureproject"></span></a>Ajouter le package NuGet RedisSessionStateProvider à votre application web

Installez le package NuGet `RedisSessionStateProvider`. Utilisez la commande suivante pour effectuer l'installation à partir de la console du gestionnaire de package : (**Outils** \> **Gestionnaire de package NuGet** \> **Console du gestionnaire de package**) :

`PM> Install-Package RedisSessionStateProvider -IncludePrerelease`

Pour effectuer l'installation à partir d'**Outils** \> **Gestionnaire de package NuGet** \> **Gérer les packages NuGet pour la solution**, recherchez `RedisSessionStateProvider` en veillant à spécifier **Inclure la version préliminaire**.

Pour plus d'informations, consultez la [page NuGet RedisSessionStateProvider][page NuGet RedisSessionStateProvider] et [Configuration des clients du cache][Configuration des clients du cache].

## <span id="configurewebconfig"></span></a>Modifier le fichier web.config

Outre la création de références d'assembly pour le cache, le package NuGet ajoute des entrées de stub dans le fichier *web.config*.

1.  Ouvrez le fichier *web.config*, puis recherchez l'élément **sessionState**.

2.  Entrez les valeurs appropriées pour `host`, `accessKey`, `port` (le port SSL doit être 6380), puis affectez à `SSL` la valeur `true`. Ces valeurs peuvent être obtenues dans le panneau de la version préliminaire du portail de gestion Azure pour votre instance de cache. Pour plus d'informations, consultez [Connexion au cache][Connexion au cache].
    Le balisage suivant montre les changements apportés au fichier *web.config*.


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


## <span id="usesessionobject"></span></a>Utiliser l'objet Session dans le code

La dernière étape consiste à utiliser l'objet Session dans votre code ASP.NET. Pour ajouter des objets à l'état de session, utilisez la méthode **Session.Add**. Cette méthode utilise des paires clé-valeur pour stocker des éléments dans le cache d'état de session.

    string strValue = "yourvalue";
    Session.Add("yourkey", strValue);

Le code suivant récupère cette valeur dans l'état de session.

    object objValue = Session["yourkey"];
    if (objValue != null)
       strValue = (string)obj;  

Vous pouvez également utiliser le Cache Redis pour mettre en cache des objets dans votre application web. Pour plus d'informations, consultez la page relative à la génération d'une [application MVC dédiée aux films avec Cache Redis Azure en 15 minutes][application MVC dédiée aux films avec Cache Redis Azure en 15 minutes].
Pour plus d'informations sur l'utilisation de l'état de session ASP.NET, consultez [Vue d'ensemble de l'état de session ASP.NET][Vue d'ensemble de l'état de session ASP.NET].

  [Rick Anderson]: https://twitter.com/RickAndMSFT
  [Création du cache]: #createcache
  [Ajout du package NuGet RedisSessionStateProvider à votre application web]: #configureproject
  [Modification du fichier web.config]: #configurewebconfig
  [Utilisation de l'objet Session pour stocker et récupérer des éléments mis en cache]: #usesessionobject
  [instructions indiquées]: http://azure.microsoft.com/fr-fr/documentation/articles/cache-dotnet-how-to-use-azure-redis-cache/#create-cache
  [page NuGet RedisSessionStateProvider]: http://www.nuget.org/packages/Microsoft.Web.RedisSessionStateProvider/
  [Configuration des clients du cache]: http://azure.microsoft.com/fr-fr/documentation/articles/cache-dotnet-how-to-use-azure-redis-cache/#NuGet
  [Connexion au cache]: http://azure.microsoft.com/fr-fr/documentation/articles/cache-dotnet-how-to-use-azure-redis-cache/#connect-to-cache
  [application MVC dédiée aux films avec Cache Redis Azure en 15 minutes]: http://azure.microsoft.com/blog/2014/06/05/mvc-movie-app-with-azure-redis-cache-in-15-minutes/
  [Vue d'ensemble de l'état de session ASP.NET]: http://msdn.microsoft.com/fr-fr/library/ms178581.aspx
