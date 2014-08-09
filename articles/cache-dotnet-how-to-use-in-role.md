<properties linkid="Contact - Support" urlDisplayName="Caching" pageTitle="How to use In-Role Cache (.NET) - Azure feature guide" metaKeywords="Azure cache, Azure caching, Azure cache, Azure caching, Azure store session state, Azure cache .NET, Azure cache C#" description="Learn how to use Azure In-Role Cache. The samples are written in C# code and use the .NET API." metaCanonical="" services="cache" documentationCenter=".NET" title="How to Use In-Role Cache for Azure Cache" authors="" solutions="" manager="" editor="" />

Utilisation de In-Role Cache pour Azure Cache
=============================================

Ce guide décrit la prise en main de **In-Role Cache pour Azure Cache**. Les exemples sont écrits en C\# et utilisent l'API .NET. Les scénarios présentés comprennent la **configuration d'un cluster de cache**, la **configuration de clients de cache**, l'**ajout et la suppression d'objets dans le cache, le stockage de l'état de session ASP.NET dans le cache** et l'**activation du cache de la sortie de pages ASP.NET avec le cache**. Pour plus d'informations sur l'utilisation de In-Role Cache, consultez la section [Étapes suivantes](#next-steps).

Sommaire
--------

-   [Présentation de In-Role Cache](#what-is)
-   [Prise en main de In-Role Cache](#getting-started-cache-role-instance)
    -   [Configuration du cluster de cache](#enable-caching)
    -   [Configuration des clients du cache](#NuGet)
-   [Utilisation des caches](#working-with-caches)
    -   [Procédure : Création d'un objet DataCache](#create-cache-object)
    -   [Procédure : Ajout et récupération d'un objet dans le cache](#add-object)
    -   [Procédure : Spécification de l'expiration d'un objet dans le cache](#specify-expiration)
    -   [Procédure : Stockage de l'état de session ASP.NET dans le cache](#store-session)
    -   [Procédure : Stockage de la mise en cache de sortie de pages ASP.NET dans le cache](#store-page)
-   [Étapes suivantes](#next-steps)

## Présentation de In-Role Cache

In-Role Cache offre une couche de mise en cache à vos applications Azure. La mise en cache améliore les performances en stockant de façon temporaire les informations en mémoire à partir d'autres sources principales. Elle peut réduire les coûts associés aux transactions de bases de données dans le cloud. In-Role Cache comprend les fonctionnalités suivantes :

-   Fournisseurs ASP.NET préconfigurés pour la mise en cache de l'état de session et de la sortie de pages, permettant d'accélérer les applications Web sans avoir à modifier le code d'application.
-   Mise en cache de tout objet managé pouvant être sérialisé, par exemple : objets CLR, lignes, XML, données binaires.
-   Modèle de développement cohérent dans Azure et Windows Server AppFabric.

In-Role Cache est un nouveau moyen d'assurer la mise en cache en utilisant une portion de la mémoire des machines virtuelles qui hébergent les instances de rôle dans vos services cloud Azure (aussi appelés services hébergés). Vous avez ainsi une plus grande flexibilité en termes de déploiement : les caches peuvent être très volumineux et ne pas avoir de quotas spécifiques.

La mise en cache sur les instances de rôles présente certains avantages :

-   Vous ne payez pas de frais supplémentaires pour la mise en cache. Vous ne payez que pour les ressources de calcul qui hébergent le cache.
-   Les limitations et les quotas sont éliminés.
-   Le contrôle et l'isolement sont de meilleur niveau.
-   Les performances sont améliorées.
-   La taille des caches est automatiquement ajustée lorsque les rôles sont étendus ou réduits. La mémoire disponible pour le cache est étendue ou réduite de façon efficace lorsque des instances de rôle sont ajoutées ou supprimées.
-   Débogage complet au moment du développement.
-   Prend en charge le protocole memcache.

En outre, la mise en cache sur les instances de rôle comporte ces options configurables :

-   Configuration d'un rôle dédié à la mise en cache ou colocation de la mise en cache dans des rôles existants.
-   Mise à disposition du cache pour plusieurs clients dans le même déploiement de service cloud.
-   Création de plusieurs caches nommés avec des propriétés différentes.
-   Configuration facultative de la haute disponibilité sur certains caches.
-   Utilisation de capacités étendues de mise en cache, comme les régions, les balises et les notifications.

Ce guide offre un aperçu de la prise en main de In-Role Cache. Pour plus d'informations sur les fonctions qui ne sont pas présentées dans ce guide de prise en main, consultez la page [À propos de In-Role Cache pour le Cache Windows Azure](http://go.microsoft.com/fwlink/?LinkId=254172).

## Prise en main de In-Role Cache

In-Role Cache permet d'activer la mise en cache à l'aide de la mémoire qui se trouve sur les machines virtuelles qui hébergent vos instances de rôle. Les instances de rôle qui hébergent vos caches sont appelées **clusters de cache**. Il existe deux topologies de déploiement pour la mise en cache sur les instances de rôle :

-   **Rôle dédié** : les instances de rôle sont utilisées exclusivement pour la mise en cache.
-   **Rôle en colocation** : le cache partage les ressources de la machine virtuelle (bande passante, processeur, mémoire) avec l'application.

Pour utiliser la mise en cache sur les instances de rôle, vous devez configurer un cluster de cache, puis configurer les clients du cache afin qu'ils puissent accéder au cluster de cache.

-   [Configuration du cluster de cache](#enable-caching)
-   [Configuration des clients du cache](#NuGet)

## Configuration du cluster de cache

Pour configurer un cluster de cache **Rôle en colocation**, sélectionnez le rôle dans lequel vous voulez héberger le cluster de cache. Cliquez avec le bouton droit sur les propriétés du rôle dans **Explorateur de solutions** et sélectionnez **Propriétés**.

![RoleCache1](./media/cache-dotnet-how-to-use-in-role/cache8.png)

Passez dans l'onglet **Caching**, activez la case à cocher **Enable Caching** et spécifiez les options de mise en cache souhaitées. Lorsque la mise en cache est activée sur un **Rôle de travail** ou sur un **Rôle Web ASP.NET**, la configuration par défaut est la mise en cache **Rôle en colocation** avec 30 % de la mémoire des instances de rôle alloué à la mise en cache. Un cache par défaut est configuré automatiquement, et d'autres caches nommés supplémentaires peuvent être créés si nécessaire. Ces caches se partageront la mémoire allouée.

![RoleCache2](./media/cache-dotnet-how-to-use-in-role/cache9.png)

Pour configurer un cluster de cache **Rôle dédié**, ajoutez un **Rôle de travail de cache** à votre projet.

![RoleCache7](./media/cache-dotnet-how-to-use-in-role/cache14.png)

Lorsqu'un **Rôle de travail de cache** est ajouté à un projet, la configuration par défaut est la mise en cache **Rôle dédié**.

![RoleCache8](./media/cache-dotnet-how-to-use-in-role/cache15.png)

Une fois que la mise en cache est activée, le compte de stockage du cluster de cache peut être configuré. In-Role Cache nécessite un compte de stockage Azure. Ce compte de stockage est utilisé pour contenir les données de configuration sur le cluster de cache, accessible depuis toutes les machines virtuelles qui composent le cluster de cache. Ce compte de stockage est spécifié sur l'onglet **Caching** de la page des propriétés du rôle de cluster de cache, juste au-dessus de **Named Cache Settings**.

![RoleCache10](./media/cache-dotnet-how-to-use-in-role/cache17.png)

> Si ce compte de stockage n'est pas configuré, les rôles ne peuvent pas démarrer.

La taille du cache est déterminée par la taille de la machine virtuelle du rôle, le nombre d'instances du rôle, et si le cluster de cache est configuré comme rôle dédié ou comme cluster de cache de rôle en colocation.

> Cette section contient une présentation simplifiée sur la configuration de la taille du cache. Pour plus d'informations sur la taille du cache et sur d'autres facteurs de planification de la capacité, consultez la page [Éléments à prendre en considération pour la planification de capacité In-Role Cache](http://go.microsoft.com/fwlink/?LinkId=252651).

Pour configurer la taille de la machine virtuelle et le nombre d'instances de rôle, cliquez avec le bouton droit sur les propriétés du rôle dans **Explorateur de solutions** et choisissez **Propriétés**.

![RoleCache1](./media/cache-dotnet-how-to-use-in-role/cache8.png)

Passez sur l'onglet **Configuration**. La valeur par défaut de **Instance count** est 1 et la taille **VM size** par défaut est **Small**.

![RoleCache3](./media/cache-dotnet-how-to-use-in-role/cache10.png)

La mémoire totale par taille de machine virtuelle se présente comme suit :

-   **Small** : 1,75 Go
-   **Medium** : 3,5 Go
-   **Large** : 7 Go
-   **ExtraLarge** : 14 Go

> Cette taille représente la quantité totale de mémoire disponible pour la machine virtuelle, partagée par le système d'exploitation, le processus de cache, les données en cache et l'application. Pour plus d'informations sur la configuration de la taille des machines virtuelles, consultez la page [Configuration de la taille des machines virtuelles](http://go.microsoft.com/fwlink/?LinkId=164387). Notez que le cache n'est pas pris en charge sur les machines virtuelles de taille **ExtraSmall**.

Lorsque la mise en cache **Rôle en colocation** est spécifiée, la taille du cache est déterminée par le pourcentage spécifié de mémoire de la machine virtuelle. Lorsque la mise en cache **Rôle dédié** est spécifiée, toute la mémoire disponible sur la machine virtuelle est utilisée pour la mise en cache. Si deux instances de rôle sont configurées, la mémoire combinée des machines virtuelles est utilisée. Ceci forme un cluster de cache dans lequel la mémoire de cache disponible est distribuée sur plusieurs instances de rôle, mais présentée aux clients du cache comme une seule et même ressource. La taille du cache augmente de façon similaire lorsque vous configurez d'autres instances de rôle. Afin de déterminer les paramètres nécessaires pour fournir un cache de la taille souhaitée, vous pouvez utiliser la feuille de calcul de prévision de la capacité qui est présentée dans la page [Éléments à prendre en considération pour la planification de capacité In-Role Cache](http://go.microsoft.com/fwlink/?LinkId=252651).

Une fois le cluster de cache configuré, vous pouvez configurer les clients du cache pour permettre l'accès au cache.

## Configuration des clients du cache

Pour accéder à un cache In-Role Cache, les clients doivent se trouver dans le même déploiement. Si le cluster de cache est un cluster de cache de rôle dédié, les clients sont d'autres rôles dans le déploiement. Si le cluster de cache est un cluster de cache de rôle en colocation, les clients peuvent être soit d'autres rôles dans le déploiement, soit les rôles qui hébergent le cluster de cache. Le package NuGet fourni permet de configurer chaque rôle client qui accède au cache. Pour configurer un rôle pour qu'il accède à un cluster de cache à l'aide du package Caching NuGet, cliquez avec le bouton droit sur le projet de rôle dans **Explorateur de solutions** et choisissez **Manage NuGet Packages**.

![RoleCache4](./media/cache-dotnet-how-to-use-in-role/cache11.png)

Sélectionnez **In-Role Cache**, cliquez sur **Install**, puis sur **I Accept**.

> Si **In-Role Cache** ne figure pas dans la liste, tapez **WindowsAzure.Caching** dans la zone de texte **Search Online** et sélectionnez-le dans les résultats.

![RoleCache5](./media/cache-dotnet-how-to-use-in-role/cache12.png)

Le package NuGet effectue plusieurs opérations : il ajoute la configuration nécessaire aux fichiers de configuration du rôle, il ajoute un paramètre de niveau de diagnostic du client de cache au fichier ServiceConfiguration.cscfg de l'application Azure et il ajoute également les références d'assembly nécessaires.

> Pour les rôles Web ASP.NET, le package Caching NuGet ajoute également deux sections commentées au fichier web.config. La première section indique que l'état de la session doit être stocké dans le cache et la deuxième active la mise en cache de la sortie de pages ASP.NET. Pour plus d'informations, consultez les pages [Stockage de l'état de session ASP.NET dans le cache](#store-session) et [Stockage de la mise en cache de sortie de pages ASP.NET dans le cache](#store-page)

Le package NuGet ajoute les éléments de configuration suivants au fichier web.config ou app.config du rôle. Une section **dataCacheClients** et une section **cacheDiagnostics** sont ajoutées sous l'élément **configSections**. Si aucun élément **configSections** n'est présent, un élément configSections enfant de l'élément **configuration** est créé.

    <configSections>
      <!-- Sections existantes omises pour plus de clarté. -->

      <section name="dataCacheClients" 
               type="Microsoft.ApplicationServer.Caching.DataCacheClientsSection, Microsoft.ApplicationServer.Caching.Core" 
               allowLocation="true" 
               allowDefinition="Everywhere" />

      <section name="cacheDiagnostics" 
               type="Microsoft.ApplicationServer.Caching.AzureCommon.DiagnosticsConfigurationSection, Microsoft.ApplicationServer.Caching.AzureCommon" 
               allowLocation="true" 
               allowDefinition="Everywhere" />
    </configSections>

Ces nouvelles sections comprennent des références à un élément **dataCacheClients** et à un élément **cacheDiagnostics**. Ces éléments sont également ajoutés à l'élément **configuration**.

    <dataCacheClients>
      <dataCacheClient name="default">
        <autoDiscover isEnabled="true" identifier="[cache cluster role name]" />
        <!--<localCache isEnabled="true" sync="TimeoutBased" objectCount="100000" ttlValue="300" />-->
      </dataCacheClient>
    </dataCacheClients>
    <cacheDiagnostics>
      <crashDump dumpLevel="Off" dumpStorageQuotaInMB="100" />
    </cacheDiagnostics>

Une fois la configuration ajoutée, remplacez **[cache cluster role name]** par le nom du rôle qui héberge le cluster de cache.

> Si **[cache cluster role name]** n'est pas remplacé par le nom du rôle qui héberge le cluster de cache, une exception **TargetInvocationException** est retournée lors de l'accès au cache avec une exception interne **DatacacheException** avec le message « No such role exists ».

Le package NuGet ajoute également un paramètre **ClientDiagnosticLevel** dans les paramètres **ConfigurationSettings** du rôle client du cache dans ServiceConfiguration.cscfg. L'exemple qui suit est la section **WebRole1** d'un fichier ServiceConfiguration.cscfg avec le niveau 1 de **ClientDiagnosticLevel**, qui est le niveau **ClientDiagnosticLevel** par défaut.

    <Role name="WebRole1">
      <Instances count="1" />
      <ConfigurationSettings>
        <!-- Sections existantes omises pour plus de clarté. -->
        <Setting name="Microsoft.WindowsAzure.Plugins.Caching.ClientDiagnosticLevel" 
                 value="1" />
      </ConfigurationSettings>
    </Role>

> In-Role Cache comporte un niveau de diagnostic au niveau du serveur de cache et du client de cache. Le niveau de diagnostic est un paramètre unique qui configure le niveau des informations de diagnostic collectées pour la mise en cache. Pour plus d'informations, consultez la page [Résolution des problèmes et diagnostic pour In-Role Cache](http://msdn.microsoft.com/fr-fr/library/windowsazure/hh914135.aspx)

Le package NuGet ajoute également des références aux assemblys suivants :

-   Microsoft.ApplicationServer.Caching.Client.dll
-   Microsoft.ApplicationServer.Caching.Core.dll
-   Microsoft.WindowsFabric.Common.dll
-   Microsoft.WindowsFabric.Data.Common.dll
-   Microsoft.ApplicationServer.Caching.AzureCommon.dll
-   Microsoft.ApplicationServer.Caching.AzureClientHelper.dll

Si votre projet est un rôle Web ASP.NET, la référence d'assembly suivante est également ajoutée :

-   Microsoft.Web.DistributedCache.dll.

> Ces assemblys se trouvent dans le dossier C:\\Program Files\\Microsoft SDKs\\Windows Azure\\.NET SDK\\2012-10\\ref\\Caching\\.

Une fois que votre projet client est configuré pour la mise en cache, vous pouvez utiliser les techniques décrites dans les sections suivantes pour utiliser votre cache.

## Utilisation des caches

Les étapes de cette section décrivent des tâches courantes avec la mise en cache.

-   [Procédure : Création d'un objet DataCache](#create-cache-object)
-   [Procédure : Ajout et récupération d'un objet dans le cache](#add-object)
-   [Procédure : Spécification de l'expiration d'un objet dans le cache](#specify-expiration)
-   [Procédure : Stockage de l'état de session ASP.NET dans le cache](#store-session)
-   [Procédure : Stockage de la mise en cache de sortie de pages ASP.NET dans le cache](#store-page)

## Création d'un objet DataCache

Pour utiliser un cache par programme, vous avez besoin d'une référence au cache. Ajoutez l'élément suivant en haut de tout fichier dont vous souhaitez qu'il utilise In-Role Cache :

    using Microsoft.ApplicationServer.Caching;

> Si Visual Studio ne reconnaît pas les types dans l'instruction d'utilisation même après installation du package Caching NuGet, qui ajoute les références nécessaires, assurez-vous que le profil cible du projet est .NET Framework 4.0 ou version ultérieure et sélectionnez un des profils qui ne spécifie pas **Client Profile**. Pour obtenir des instructions sur la configuration des clients du cache, consultez la page [Configuration des clients du cache](#NuGet).

Il existe plusieurs méthodes pour créer un objet **DataCache**. La première consiste à créer simplement un **DataCache** en transmettant le nom du cache voulu.

    DataCache cache = new DataCache("default");

Une fois que le **DataCache** est instancié, vous pouvez l'utiliser pour interagir avec le cache, comme décrit dans les sections suivantes.

Pour utiliser la seconde méthode, créez un objet **DataCacheFactory** dans votre application à l'aide du constructeur par défaut. Le client du cache utilise alors les paramètres présents dans le fichier de configuration. Appelez soit la méthode **GetDefaultCache** de la nouvelle instance de **DataCacheFactory**, qui renvoie un objet **DataCache**, soit la méthode **GetCache**, puis transmettez le nom de votre cache. Ces méthodes renvoient un objet **DataCache** qui peut être utilisé pour accéder au cache par programme.

    // Client de cache configuré par les paramètres du fichier de configuration de l'application.
    DataCacheFactory cacheFactory = new DataCacheFactory();
    DataCache cache = cacheFactory.GetDefaultCache();
    // Ou DataCache cache = cacheFactory.GetCache("MyCache");
    // Le cache peut maintenant être utilisé pour ajouter et récupérer des éléments.   

## Ajout et récupération d'un objet dans le cache

Pour ajouter un élément au cache, vous pouvez utiliser la méthode **Add** ou **Put**. La méthode **Add** ajoute l'objet spécifié au cache, indexé par la valeur du paramètre de clé.

    // Ajouter la chaîne "value" au cache, avec la clé "item"
    cache.Add("item", "value");

Si un objet avec la même clé est déjà présent dans le cache, une **DataCacheException** est levée avec le message suivant :

> ErrorCode:SubStatus: tentative de création d'un objet avec une clé qui existe déjà dans le cache. La mise en cache accepte uniquement des valeurs de clé uniques pour les objets.

Pour récupérer un objet avec une clé particulière, vous pouvez utiliser la méthode **Get**. Si l'objet existe, il est renvoyé ; sinon, la valeur null est renvoyée.

    // Ajouter la chaîne "value" au cache, avec la clé "key"
    object result = cache.Get("Item");
    if (result == null)
    {
        // "Item" non présent dans le cache. L'obtenir à partir de la source de données spécifiée
        // et l'ajouter.
        string value = GetItemValue(...);
        cache.Add("item", value);
    }
    else
    {
        // "Item" est en cache, convertir le résultat dans le type correct.
    }

La méthode **Put** ajoute l'objet avec la clé spécifiée au cache s'il n'existe pas ou remplace l'objet s'il existe.

    // Ajouter la chaîne "value" au cache, avec la clé "item". S'il existe,
    // le remplacer.
    cache.Put("item", "value");

## Spécification de l'expiration d'un objet dans le cache

Par défaut, les éléments du cache expirent 10 minutes après qu'ils y ont été placés. Cette valeur peut être configurée dans le paramètre **Time to Live (min)** des propriétés du rôle qui héberge le cluster de cache.

![RoleCache6](./media/cache-dotnet-how-to-use-in-role/cache13.png)

Il existe trois types de **Expiration Type** : **None**, **Absolute** et **Sliding Window**. Ils définissent comment **Time to Live (min)** est utilisé pour déterminer l'expiration. Le **Type d'expiration** par défaut est **Absolute**, ce qui signifie que le compte à rebours pour l'expiration d'un élément commence lorsque l'élément est placé dans le cache. Une fois que le délai spécifié pour un élément est écoulé, l'élément expire. Si **Sliding Window** est spécifié, le décompte de l'expiration d'un élément est réinitialisé à chaque accès de l'élément dans le cache et l'élément n'expire pas tant que la durée spécifiée depuis son dernier accès n'est pas écoulée. Si **None** est spécifié, alors **Time to Live (min)** soit être défini sur **0**. Les éléments n'expirent pas et restent valides tant qu'ils se trouvent dans le cache.

Si vous souhaitez une durée d'expiration plus longue ou plus courte que celle configurée dans les propriétés du rôle, cette durée spécifique peut être indiquée lors de l'ajout ou de la mise à jour d'un élément dans le cache à l'aide de la surcharge **Add** et **Put**, qui peuvent prendre le paramètre **TimeSpan**. Dans l'exemple suivant, la chaîne **value** est ajoutée au cache, indexée par **item**, avec un délai d'expiration de 30 minutes.

    // Ajouter la chaîne "value" au cache, avec la clé "item"
    cache.Add("item", "value", TimeSpan.FromMinutes(30));

Pour afficher l'intervalle restant du délai d'expiration d'un élément dans le cache, vous pouvez utiliser la méthode **GetCacheItem** afin de récupérer un objet **DataCacheItem** contenant des informations sur l'élément dans le cache, y compris l'intervalle restant du délai d'expiration.

    // Obtenir un objet DataCacheItem qui contient des informations sur
    // "Item" dans le cache. Si aucun objet n'est indexé par "item", null
    // est renvoyé. 
    DataCacheItem item = cache.GetCacheItem("item");
    TimeSpan timeRemaining = item.Timeout;

## Stockage de l'état de session ASP.NET dans le cache

Le fournisseur de l'état de session pour In-Role Cache est un mécanisme de stockage externe aux processus pour les applications ASP.NET. Il vous permet de stocker l'état de votre session dans un cache Azure plutôt qu'en mémoire ou dans une base de données SQL Server. Pour utiliser le fournisseur de l'état de session de la mise en cache, configurez d'abord votre cluster de cache, puis configurez votre application ASP.NET pour la mise en cache à l'aide du package Caching NuGet, comme décrit dans [Prise en main de In-Role Cache](#getting-started-cache-role-instance). Lorsque le package Cache NuGet est installé, il ajoute dans le fichier web.config une section placée en commentaire contenant la configuration requise pour que votre application ASP.NET utilise le fournisseur d'état de session pour In-Role Cache.

    <!--Supprimez les commentaires de cette section pour utiliser In-Role Cache pour la mise en cache de l'état de la session
    <system.web>
      <sessionState mode="Custom" customProvider="AFCacheSessionStateProvider">
        <providers>
          <add name="AFCacheSessionStateProvider" 
            type="Microsoft.Web.DistributedCache.DistributedCacheSessionStateStoreProvider,
                  Microsoft.Web.DistributedCache" 
            cacheName="default" 
            dataCacheClientName="default"/>
        </providers>
      </sessionState>
    </system.web>-->

> Si votre fichier web.config ne contient pas cette section commentée après l'installation du package Caching NuGet, assurez-vous que la dernière version de NuGet Package Manager est installée depuis [NuGet Package Manager Installation](http://go.microsoft.com/fwlink/?LinkId=240311), puis désinstallez et réinstallez le package.

Pour activer le fournisseur de l'état de session pour In-Role Cache, supprimez les commentaires de le section spécifiée. Le cache par défaut est spécifié dans l'extrait fourni. Pour utiliser un autre cache, spécifiez le cache voulu dans l'attribut **cacheName**.

Pour plus d'informations sur l'utilisation du fournisseur de l'état de session du service de mise en cache, consultez la page [Fournisseur de l'état de session pour In-Role Cache](http://msdn.microsoft.com/fr-fr/library/windowsazure/gg185668.aspx).

## Stockage de la mise en cache de sortie de pages ASP.NET dans le cache

Le fournisseur de caches de sortie pour In-Role Cache est un mécanisme de stockage hors processus pour les données de cache de sortie. Ces données concernent spécialement les réponses HTTP complètes (mise en cache de la sortie de pages). Le fournisseur se connecte sur le nouveau point d'extensibilité du fournisseur de caches de sortie introduit dans ASP.NET 4. Pour utiliser ce fournisseur de caches de sortie, configurez d'abord votre cluster de cache, puis configurez votre application ASP.NET pour la mise en cache à l'aide du package Caching NuGet, comme décrit dans [Prise en main de In-Role Cache](#getting-started-cache-role-instance). Lorsque le package Cache NuGet est installé, il ajoute dans le fichier web.config la section commentée suivante, qui contient la configuration requise pour que votre application ASP.NET utilise le fournisseur de caches de sortie pour In-Role Cache.

    <!--Supprimez les commentaires de cette section pour utiliser In-Role Cache pour la mise en cache de la sortie
    <caching>
      <outputCache defaultProvider="AFCacheOutputCacheProvider">
        <providers>
          <add name="AFCacheOutputCacheProvider" 
            type="Microsoft.Web.DistributedCache.DistributedCacheOutputCacheProvider,
                  Microsoft.Web.DistributedCache" 
            cacheName="default" 
            dataCacheClientName="default" />
        </providers>
      </outputCache>
    </caching>-->

> Si votre fichier web.config ne contient pas cette section commentée après l'installation du package Caching NuGet, assurez-vous que la dernière version de NuGet Package Manager est installée depuis [NuGet Package Manager Installation](http://go.microsoft.com/fwlink/?LinkId=240311), puis désinstallez et réinstallez le package.

Pour activer le fournisseur de caches de sortie pour In-Role Cache, supprimez les commentaires de la section spécifiée. Le cache par défaut est spécifié dans l'extrait fourni. Pour utiliser un autre cache, spécifiez le cache voulu dans l'attribut **cacheName**.

Ajoutez une directive **OutputCache** à chaque page pour laquelle vous voulez mettre en cache la sortie.

    <%@ OutputCache Duration="60" VaryByParam="*" %>

Dans cet exemple, les données de page mises en cache resteront dans le cache pendant 60 secondes et une version différente de la page sera mise en cache pour chaque combinaison de paramètres. Pour plus d'informations sur les options disponibles, consultez la page [Directive OutputCache](http://go.microsoft.com/fwlink/?LinkId=251979).

Pour plus d'informations sur l'utilisation du fournisseur de caches de sortie pour In-Role Cache, consultez la page [Fournisseur de caches de sortie pour In-Role Cache](http://msdn.microsoft.com/fr-fr/library/windowsazure/gg185662.aspx).

## Étapes suivantes

Maintenant que vous avez appris les bases de In-Role Cache, suivez ces liens pour apprendre des tâches de mise en cache plus complexes.

-   Consultez la référence MSDN suivante : [In-Role Cache](http://www.microsoft.com/en-us/showcase/Search.aspx?phrase=azure+caching)
-   Découvrez comment effectuer la migration vers In-Role Cache : [Migration vers In-Role Cache](http://msdn.microsoft.com/fr-fr/library/hh914163.aspx)
-   Consultez les exemples : [Exemples In-Role Cache](http://msdn.microsoft.com/fr-fr/library/jj189876.aspx)
-   Regardez la session [Performances maximales : accélérez vos applications de service cloud avec Azure Caching](http://channel9.msdn.com/Events/TechEd/NorthAmerica/2013/WAD-B326#fbid=kmrzkRxQ6gU) du TechEd 2013 sur In-Role Cache

