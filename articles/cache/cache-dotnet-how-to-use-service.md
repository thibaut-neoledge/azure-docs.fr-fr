---
title: "Utilisation de Service de cache géré Azure"
description: "Découvrez comment améliorer les performances de vos applications Azure grâce au Service de cache géré d&quot;Azure"
services: cache
documentationcenter: 
author: steved0x
manager: douge
editor: 
ms.assetid: 80552353-6682-4b96-a4cc-4e975a25980c
ms.service: cache
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 01/20/2017
ms.author: sdanie
translationtype: Human Translation
ms.sourcegitcommit: ec9e12667bc82043fbdc0b3f113533cf518f39be
ms.openlocfilehash: 66907ccb97dedc176c5fe76826abd3b31f4ccdb9


---
# <a name="how-to-use-azure-managed-cache-service"></a>Utilisation de Service de cache géré Azure
> [!IMPORTANT]
> Conformément à notre [annonce](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/) de l’année dernière, le service de cache géré d’Azure et le service In-Role Cache d’Azure **ont été supprimés** le 30 novembre 2016. Nous vous recommandons d’utiliser [Cache Redis Azure](https://azure.microsoft.com/services/cache/). Pour obtenir des informations sur la migration, consultez [Migrer un Service de cache géré vers le Cache Redis Azure](../redis-cache/cache-migrate-to-redis.md).
> 
> 

Ce guide décrit la prise en main du **Service de cache géré Azure**. Les exemples sont écrits en C\# et utilisent l’API .NET. Les scénarios couverts incluent la **création et la configuration d’un cache**, la **configuration des clients du cache**, l’**ajout et la suppression d’objets dans le cache, le stockage de l’état de session ASP.NET dans le cache**, et l’**activation de la mise en cache de sortie de pages ASP.NET avec le cache**. Pour plus d'informations sur l’utilisation d’Azure Cache, consultez la section [Étapes suivantes][Next Steps].



<a name="what-is"></a>

## <a name="what-is-azure-managed-cache-service"></a>Qu'est-ce que le Service de cache géré Azure ?
Le Service de cache géré Azure est une solution distribuée, en mémoire et évolutive qui vous permet de créer des applications hautement évolutives et réactives en fournissant un accès ultra-rapide aux données.

Le Service de cache géré Azure inclut les fonctionnalités suivantes :

* Fournisseurs ASP.NET préconfigurés pour la mise en cache de l'état de session et de la sortie de pages, permettant d'accélérer les applications Web sans avoir à modifier le code d'application.
* Mise en cache de tout objet géré, pouvant être sérialisé, par exemple : objets CLR, lignes, XML, données binaires.
* Modèle de développement cohérent dans Azure et Windows Server AppFabric.

Service de cache géré vous offre l'accès à un cache dédié sécurisé qui est géré par Microsoft. Un cache créé avec Service de cache géré est accessible depuis les applications dans Azure exécutées sur les sites Web, les rôles Web et de travail et les machines virtuelles Azure.

Service de cache géré est disponible en trois options :

* Basic - Tailles de cache comprises entre 128 Mo et 1 Go
* Standard - Tailles de cache comprises entre 1 Go et 10 Go
* Premium - Tailles de cache comprises entre 5 Go et 150 Go

Chaque option diffère en termes de fonctionnalités et de tarification. Les fonctionnalités sont décrites plus loin dans ce guide. Pour plus d'informations sur la tarification, consultez [Cache Redis Tarification][Cache Pricing Details].

Ce guide fournit un aperçu de la prise en main de Service de cache géré. Pour plus d’informations sur ces fonctionnalités qui ne sont pas décrites dans ce guide de prise en main, consultez [Documentation Cache Redis][Overview of Azure Managed Cache Service].

<a name="getting-started-cache-service"></a>

## <a name="getting-started-with-cache-service"></a>Prise en main du Service de cache
La prise en main de Service de cache géré est aisée. Pour commencer, vous mettez en service et configurez un cache. Ensuite, vous configurez les clients du cache pour qu'ils puissent accéder au cache. Une fois les clients du cache configurés, vous pouvez commencer à les utiliser.

* [Création du cache][Create the cache]
* [Configuration du cache][Configure the cache]
* [Configuration des clients de cache][Configure the cache clients]

<a name="create-cache"></a>

## <a name="create-a-cache"></a>Création d'un cache
Les instances de cache dans Service de cache géré sont créées à l'aide d’applets de commande PowerShell. 

> Lorsqu’une instance du service de cache géré est créée à l’aide des applets de commande PowerShell, vous pouvez l’afficher et la configurer dans le [portail Azure Classic][Azure Classic Portal].
> 
> 

Pour créer une instance du service de cache géré, ouvrez une fenêtre de commande Azure PowerShell.

> Pour plus d’informations sur l’installation et l’utilisation d’Azure PowerShell, consultez [Installation et configuration d’Azure PowerShell][How to install and configure Azure PowerShell].
> 
> 

Appelez l’applet de commande [Add-AzureAccount][Add-AzureAccount], puis entrez l’adresse électronique et le mot de passe associés à votre compte. Un abonnement est choisi par défaut et s’affiche lorsque vous appelez l’applet de commande [Add-AzureAccount][Add-AzureAccount]. Pour changer d’abonnement, appelez l’applet de commande [Select-AzureSubscription][Select-AzureSubscription].

> Si vous avez configuré Azure PowerShell avec un certificat pour votre compte, vous pouvez sauter cette étape. Pour plus d’informations sur la connexion d’Azure PowerShell à votre compte Azure, consultez [Installation et configuration d’Azure PowerShell][How to install and configure Azure PowerShell].
> 
> 

Un abonnement est choisi par défaut et s’affiche. Pour changer d’abonnement, appelez l’applet de commande [Select-AzureSubscription][Select-AzureSubscription].

Appelez l’applet de commande [New-AzureManagedCache][New-AzureManagedCache], puis spécifiez le nom, la région, l’offre de cache et la taille de la mémoire cache.

Dans **Nom**, entrez le nom de sous-domaine à utiliser pour le point de terminaison du cache. Le point de terminaison doit être une chaîne composée de six à vingt caractères, contenant uniquement des minuscules et des chiffres, et commençant par une lettre.

Dans **Emplacement**, spécifiez la région du cache. Pour des performances optimales, créez le cache dans la même région que l'application cliente du cache.

**Référence (SKU)** et **Mémoire** fonctionnent ensemble pour déterminer la taille du cache. Le service de cache géré est disponible dans les trois niveaux suivants.

* Basic - Tailles de cache comprises entre 128 Mo et 1 Go par incréments de 128 Mo, avec un seul cache nommé par défaut
* Standard - Tailles de cache comprises entre 1 Go et 10 Go par incréments de 1 Go, avec prise en charge des notifications et de dix caches nommés (maximum)
* Premium - Tailles de cache comprises entre 5 Go et 150 Go par incréments de 5 Go, avec prise en charge des notifications, de la haute disponibilité et de dix caches nommés (maximum)

Choisissez les valeurs de **Référence (SKU)** et de **Mémoire** qui répondent aux besoins de votre application. Notez que certaines fonctionnalités du cache, telles les notifications et la haute disponibilité, ne sont disponibles qu'avec certaines offres de cache. Pour plus d’informations sur le choix de l’offre et de la taille de cache la plus adaptée à votre application, consultez [Offres de cache pour service de cache géré Azure][Cache offerings].

 Dans l'exemple suivant, un cache de base de 128 Mo est créé avec le nom contosocache, dans le Sud du centre des États-Unis.

    New-AzureManagedCache -Name contosocache -Location "South Central US" -Sku Basic -Memory 128MB

> Pour obtenir la liste complète des paramètres et valeurs utilisables lors de la création d’un cache, consultez la documentation de l’applet de commande [New-AzureManagedCache][New-AzureManagedCache].
> 
> 

Une fois l'applet de commande PowerShell appelée, la création du cache peut prendre plusieurs minutes. Après sa création, le cache a l’état `Running` et est prêt à fonctionner avec les paramètres par défaut. En outre, vous pouvez l’afficher et le configurer dans le [portail Azure Classic][Azure Classic Portal]. Pour personnaliser la configuration de votre cache, consultez la section [Configuration du cache][Configure the cache].

Vous pouvez surveiller l'avancement de la création dans la fenêtre Azure PowerShell. Une fois le cache prêt à l’emploi, l’applet de commande [New-AzureManagedCache][New-AzureManagedCache] affiche les informations relatives à ce dernier, comme dans l’exemple suivant.

    PS C:\> Add-AzureAccount
    VERBOSE: Account "user@domain.com" has been added.
    VERBOSE: Subscription "MySubscription" is selected as the default subscription.
    VERBOSE: To view all the subscriptions, please use Get-AzureSubscription.
    VERBOSE: To switch to a different subscription, please use Select-AzureSubscription.
    PS C:\> New-AzureManagedCache -Name contosocache -Location "South Central US" -Sku Basic -Memory 128MB
    VERBOSE: Intializing parameters...
    VERBOSE: Creating prerequisites...
    VERBOSE: Verify cache service name...
    VERBOSE: Creating cache service...
    VERBOSE: Waiting for cache service to be in ready state...


    Name     : contosocache
    Location : South Central US
    State    : Active
    Sku      : Basic
    Memory   : 128MB



    PS C:\>




<a name="enable-caching"></a>

## <a name="configure-the-cache"></a>Configuration du cache
L’onglet **Configurer** de Cache dans le portail Azure Classic vous permet de configurer les options de votre cache. Chaque cache a un cache nommé **par défaut** , et les offres de cache Standard et Premium prennent en charge jusqu'à neuf caches nommés supplémentaires, pour un total de dix. Chaque cache nommé a son propre ensemble d'options, qui vous permettent de configurer votre cache avec une grande souplesse.

![NamedCaches][NamedCaches]

Pour créer un cache nommé, tapez le nom du nouveau cache dans la zone **Nom**, spécifiez les options voulues, cliquez sur **Enregistrer**, puis sur **Oui** pour confirmer. Pour annuler les modifications, cliquez sur **Discard**.

## <a name="expiry-policy-and-time-min"></a>Stratégie d'expiration et Durée (min)
La **Stratégie d’expiration** fonctionne avec le paramètre **Time (min)** pour déterminer à quel moment les éléments mis en cache expirent. Il existe trois types de stratégies d’expiration : **Absolu**, **Fenêtre coulissante** et **Jamais**. 

Quand vous spécifiez **Absolute**, l’intervalle d’expiration indiqué par **Durée (min)** commence au moment où un élément est ajouté au cache. Lorsque l'intervalle spécifié par **Durée (min)** est écoulé, l'élément expire. 

Quand vous spécifiez **Sliding**, l’intervalle d’expiration spécifié par **Durée (min)** est réinitialisé chaque fois qu’un élément fait l’objet d’un accès dans le cache. L'élément n'expire pas tant que l'intervalle spécifié par **Durée (min)** , calculé à partir du dernier accès, n'est pas écoulé.

Quand vous spécifiez **Jamais**, **Durée (min)** doit avoir la valeur **0** et les éléments n’expirent pas.

**Absolute** est la stratégie d’expiration par défaut, et 10 minutes la valeur par défaut pour **Durée (min)**. La stratégie d’expiration est fixée pour chaque élément dans un cache nommé, mais le paramètre **Durée (min)** peut être personnalisé pour chaque élément avec les surcharges des méthodes **Add** et **Put**, qui prennent un paramètre Délai d’expiration.

Pour plus d’informations sur les stratégies d’éviction et d’expiration, consultez la page [Expiration et éviction pour le service de cache géré Azure][Expiration and Eviction].

## <a name="notifications"></a>Notifications
Les notifications du cache permettent à vos applications de recevoir des notifications asynchrones lorsque diverses opérations de cache ont lieu sur le cluster de cache. Elles fournissent également l'invalidation automatique des objets mis en cache localement. Pour plus d’informations, consultez [Notifications][Notifications].

> Les notifications sont disponibles dans les offres de cache Standard et Premium, mais pas dans l'offre Basic. Pour plus d’informations, consultez [Offres de cache pour service de cache géré Azure][Cache offerings].
> 
> 

## <a name="high-availability"></a>Haute disponibilité
Lorsque la haute disponibilité est activée, une copie de sauvegarde de chaque élément ajouté au cache est créée. Si une défaillance inattendue se produit sur la copie principale de l'élément, la copie de sauvegarde reste disponible.

Par définition, l'utilisation de la haute disponibilité multiplie par deux la quantité de mémoire requise pour chaque élément en cache. Tenez compte de cet impact sur la mémoire pendant les tâches de planification de la capacité. Pour plus d’informations, consultez la page [Haute disponibilité pour le service de cache géré Azure][High Availability].

> La haute disponibilité est disponible dans l'offre de cache Premium, mais pas dans les offres Basic ou Standard. Pour plus d’informations, consultez [Offres de cache pour service de cache géré Azure][Cache offerings].
> 
> 

## <a name="eviction"></a>Éviction
Pour maintenir la capacité de mémoire disponible à l'intérieur d'un cache, la dernière éviction récemment utilisée (LRU) est prise en charge. Lorsque la consommation de mémoire dépasse le seuil de mémoire, les objets sont expulsés de la mémoire, qu'ils aient expiré ou pas, jusqu'à ce que la pression sur la mémoire se relâche.
L'éviction est activée par défaut. Si elle est désactivée, les éléments ne sont pas expulsés du cache lorsque la capacité est atteinte, et les opérations Put et Add échouent.

Pour plus d’informations sur les stratégies d’éviction et d’expiration, consultez la page [Expiration et éviction pour le service de cache géré Azure][Expiration and Eviction].

Une fois le cache configuré, vous pouvez configurer les clients du cache pour autoriser l'accès au cache.

<a name="NuGet"></a>

## <a name="configure-the-cache-clients"></a>Configuration des clients de cache
Un cache créé avec Service de cache géré est accessible depuis les applications Azure exécutées sur les sites Web, les rôles Web et de travail et les machines virtuelles Azure. Un package NuGet est fourni pour simplifier la configuration des applications clientes du cache. 

Pour configurer une application cliente avec le package Cache NuGet, cliquez avec le bouton droit sur le projet dans l’**Explorateur de solutions** et choisissez **Gérer les packages NuGet**. 

![NuGetPackageMenu][NuGetPackageMenu]

Tapez **WindowsAzure.Caching** dans la zone de texte **Rechercher en ligne**, et choisissez **Windows**  
**Azure** **Cache** parmi les résultats. Cliquez sur **Installer**, puis sur **J’accepte**.

![NuGetPackage][NuGetPackage]

Le package NuGet fait plusieurs choses : il ajoute la configuration requise au fichier de configuration de l'application, et il ajoute les références d'assembly nécessaires. Pour les projets Services cloud, il ajoute également le niveau de diagnostic des clients du cache comme paramètre au fichier ServiceConfiguration.cscfg du service cloud.

> Pour les projets web ASP.NET, le package Cache NuGet ajoute également deux sections commentées au fichier web.config. La première section active l'état de session à stocker dans le cache, et la seconde section active la mise en cache de la sortie de pages ASP.NET. Pour plus d’informations, consultez les rubriques [Stockage de l’état de session ASP.NET dans le cache] et [Stockage de la mise en cache de sortie de pages ASP.NET dans le cache][How To: Store ASP.NET Page Output Caching in the Cache].
> 
> 

Le package NuGet ajoute les éléments de configuration suivants au fichier web.config ou app.config de votre application. Une section **dataCacheClients** et une section **cacheDiagnostics** sont ajoutées sous l’élément **configSections**. Si aucun élément **configSections** n’est présent, un élément configSections enfant de l’élément **configuration** est créé.

    <configSections>
      <!-- Existing sections omitted for clarity. -->
      <section name="dataCacheClients" 
        type="Microsoft.ApplicationServer.Caching.DataCacheClientsSection,
              Microsoft.ApplicationServer.Caching.Core" 
        allowLocation="true" 
        allowDefinition="Everywhere" />

    <section name="cacheDiagnostics" 
        type="Microsoft.ApplicationServer.Caching.AzureCommon.DiagnosticsConfigurationSection,
              Microsoft.ApplicationServer.Caching.AzureCommon" 
        allowLocation="true" 
        allowDefinition="Everywhere" />


Ces nouvelles sections incluent des références à un élément **dataCacheClients**, qui est également ajouté à l’élément **configuration**.

    <dataCacheClients>
      <dataCacheClient name="default">
        <!--To use the in-role flavor of Azure Caching, 
            set identifier to be the cache cluster role name -->
        <!--To use the Azure Caching Service,
            set identifier to be the endpoint of the cache cluster -->
        <autoDiscover isEnabled="true" identifier="[Cache role name or Service Endpoint]" />
        <!--<localCache isEnabled="true" sync="TimeoutBased" objectCount="100000" ttlValue="300" />-->
        <!--Use this section to specify security settings for connecting to your cache. 
            This section is not required if your cache is hosted on a role that is a part
            of your cloud service. -->
        <!--<securityProperties mode="Message" sslEnabled="false">
          <messageSecurity authorizationInfo="[Authentication Key]" />
        </securityProperties>-->
      </dataCacheClient>
    </dataCacheClients>


Après l'ajout de la configuration, remplacez les deux éléments suivants dans la configuration que vous venez d'ajouter.

1. Remplacez **[Cache role name or Service Endpoint]** par le point de terminaison qui est affiché sur le tableau de bord dans le portail Azure Classic.
   
    ![Point de terminaison][Endpoint]
2. Supprimez les marques de commentaire de la section securityProperties et remplacez **[Authentication Key]** par la clé d’authentification, que vous pouvez trouver dans le portail Azure Classic en cliquant sur **Gérer les clés** dans le tableau de bord du cache.
   
    ![AccessKeys][AccessKeys]

> Ces paramètres doivent être configurés correctement, sinon les clients ne pourront pas accéder au cache.
> 
> 

Pour les projets Services cloud, le package NuGet ajoute également un paramètre **ClientDiagnosticLevel** à la section **ConfigurationSettings** du rôle de client de cache dans ServiceConfiguration.cscfg. L’exemple suivant représente la section **WebRole1** d’un fichier ServiceConfiguration.cscfg, où **ClientDiagnosticLevel** a la valeur 1, ce qui correspond à la valeur par défaut de **ClientDiagnosticLevel**.

    <Role name="WebRole1">
      <Instances count="1" />
      <ConfigurationSettings>
        <!-- Existing settings omitted for clarity. -->
        <Setting name="Microsoft.WindowsAzure.Plugins.Caching.ClientDiagnosticLevel" 
                 value="1" />
      </ConfigurationSettings>
    </Role>

> Le niveau de diagnostic des clients configure le niveau des informations de diagnostic de la mise en cache collectées pour les clients du cache. Pour plus d’informations, consultez [Dépannage et diagnostics pour les applications service caché managé][Troubleshooting and Diagnostics].
> 
> 

Le package NuGet ajoute également des références aux assemblys suivants :

* Microsoft.ApplicationServer.Caching.Client.dll
* Microsoft.ApplicationServer.Caching.Core.dll
* Microsoft.WindowsFabric.Common.dll
* Microsoft.WindowsFabric.Data.Common.dll
* Microsoft.ApplicationServer.Caching.AzureCommon.dll
* Microsoft.ApplicationServer.Caching.AzureClientHelper.dll

Si votre projet est un projet Web, la référence d'assembly suivante est également ajoutée :

* Microsoft.Web.DistributedCache.dll.

Une fois que votre projet client est configuré pour la mise en cache, vous pouvez utiliser les techniques décrites dans les sections suivantes pour utiliser votre cache.

<a name="working-with-caches"></a>

## <a name="working-with-caches"></a>Utilisation des caches
Cette section décrit l'exécution des tâches courantes avec Cache.

* [Création d’un objet DataCache][How To: Create a DataCache Object]
* [Ajout et récupération d’un objet dans le cache][How To: Add and Retrieve an Object from the Cache]
* [Spécification de l’expiration d’un objet dans le cache][How To: Specify the Expiration of an Object in the Cache]
* [Stockage de l’état de session ASP.NET dans le cache][Stockage de l’état de session ASP.NET dans le cache]
* [Stockage de la mise en cache de sortie de pages ASP.NET dans le cache][How To: Store ASP.NET Page Output Caching in the Cache]

<a name="create-cache-object"></a>

## <a name="how-to-create-a-datacache-object"></a>Création d'un objet DataCache
Pour utiliser un cache par programmation, vous avez besoin d'une référence au cache. Ajoutez la ligne suivante au début de chaque fichier à partir duquel vous voulez utiliser Azure Cache :

    using Microsoft.ApplicationServer.Caching;

> Si Visual Studio ne reconnaît pas les types dans l'instruction using, même après l'installation du package Cache NuGet, qui ajoute les références nécessaires, assurez-vous que le profil cible du projet est .NET Framework 4 ou une version ultérieure et veillez à sélectionner l'un des profils qui ne spécifient pas **Client Profile**. Pour obtenir des instructions sur la configuration des clients du cache, consultez [Configuration des clients du cache][Configure the cache clients].
> 
> 

Il existe plusieurs méthodes pour créer un objet **DataCache** . La première consiste à créer simplement un **DataCache**en transmettant le nom du cache voulu.

    DataCache cache = new DataCache("default");

Une fois que le **DataCache** est instancié, vous pouvez l'utiliser pour interagir avec le cache, comme décrit dans les sections suivantes.

Pour utiliser la seconde méthode, créez un objet **DataCacheFactory** dans votre application à l'aide du constructeur par défaut. Le client du cache utilise alors les paramètres présents dans le fichier de configuration. Appelez soit la méthode **GetDefaultCache** de la nouvelle instance de **DataCacheFactory**, qui retourne un objet **DataCache**, soit la méthode **GetCache**, puis transmettez le nom de votre cache. Ces méthodes renvoient un objet **DataCache** qui peut être utilisé pour accéder au cache par programme.

    // Cache client configured by settings in application configuration file.
    DataCacheFactory cacheFactory = new DataCacheFactory();
    DataCache cache = cacheFactory.GetDefaultCache();
    // Or DataCache cache = cacheFactory.GetCache("MyCache");
    // cache can now be used to add and retrieve items.    

<a name="add-object"></a>

## <a name="how-to-add-and-retrieve-an-object-from-the-cache"></a>Ajout et récupération d'un objet dans le cache
Pour ajouter un élément au cache, vous pouvez utiliser la méthode **Add** ou **Put**. La méthode **Add** ajoute l'objet spécifié au cache, indexé par la valeur du paramètre de clé.

    // Add the string "value" to the cache, keyed by "item"
    cache.Add("item", "value");

Si un objet avec la même clé est déjà présent dans le cache, une **DataCacheException** est levée avec le message suivant :

> ErrorCode:SubStatus: tentative de création d'un objet avec une clé qui existe déjà dans le cache. La mise en cache accepte uniquement des valeurs de clé uniques pour les objets.
> 
> 

Pour récupérer un objet avec une clé particulière, vous pouvez utiliser la méthode **Get** . Si l'objet existe, il est renvoyé ; sinon, la valeur null est renvoyée.

    // Add the string "value" to the cache, keyed by "key"
    object result = cache.Get("Item");
    if (result == null)
    {
        // "Item" not in cache. Obtain it from specified data source
        // and add it.
        string value = GetItemValue(...);
        cache.Add("item", value);
    }
    else
    {
        // "Item" is in cache, cast result to correct type.
    }

La méthode **Put** ajoute l'objet avec la clé spécifiée au cache s'il n'existe pas ou remplace l'objet s'il existe.

    // Add the string "value" to the cache, keyed by "item". If it exists,
    // replace it.
    cache.Put("item", "value");

<a name="specify-expiration"></a>

## <a name="how-to-specify-the-expiration-of-an-object-in-the-cache"></a>Spécification de l'expiration d'un objet dans le cache
Par défaut, les éléments du cache expirent 10 minutes après qu'ils y ont été placés. Ce délai peut être configuré dans le paramètre **Durée (min)** sous l’onglet Configurer de Cache dans le portail Azure Classic.

![NamedCaches][NamedCaches]

Il existe trois types de **stratégie d’expiration** : **Jamais**, **Absolu** et **Fenêtre coulissante**. Ces stratégies configurent la façon dont le paramètre **Durée (min)** est utilisé pour déterminer l'expiration. Le **Type d’expiration** par défaut est **Absolute**, ce qui signifie que le compte à rebours pour l’expiration d’un élément commence quand l’élément est placé dans le cache. Une fois que le délai spécifié pour un élément est écoulé, l'élément expire. Si **Sliding** est spécifié, le compte à rebours du délai d'expiration d'un élément est réinitialisé chaque fois que l'élément fait l'objet d'un accès dans le cache et l'élément expire lorsque le délai spécifié, calculé à partir du dernier accès, est écoulé. Si vous spécifiez **Jamais**, **Durée (min)** doit avoir la valeur **0** ; les éléments n’expirent pas et restent valides tant qu’ils sont dans le cache.

Si vous souhaitez que l’intervalle du délai d’expiration soit plus long ou plus court que celui configuré dans les propriétés du cache, vous pouvez spécifier une durée particulière quand un élément est ajouté ou mis à jour dans le cache en utilisant la surcharge des méthodes **Add** et **Put**, qui prennent un paramètre **TimeSpan**. Dans l’exemple suivant, la chaîne **value** est ajoutée au cache, indexée par **item**, avec un délai d’expiration de 30 minutes.

    // Add the string "value" to the cache, keyed by "item"
    cache.Add("item", "value", TimeSpan.FromMinutes(30));

Pour afficher l’intervalle restant du délai d’expiration d’un élément dans le cache, vous pouvez utiliser la méthode **GetCacheItem** pour récupérer un objet **DataCacheItem** contenant des informations sur l’élément dans le cache, notamment l’intervalle restant du délai d’expiration.

    // Get a DataCacheItem object that contains information about
    // "item" in the cache. If there is no object keyed by "item" null
    // is returned. 
    DataCacheItem item = cache.GetCacheItem("item");
    TimeSpan timeRemaining = item.Timeout;

<a name="store-session"></a>

## <a name="how-to-store-aspnet-session-state-in-the-cache"></a>Stockage de l'état de session ASP.NET dans le cache
Le fournisseur d'état de session d'Azure Cache est un mécanisme de stockage hors processus pour les applications ASP.NET. Il vous permet de stocker l'état de votre session dans un cache Azure plutôt qu'en mémoire ou dans une base de données SQL Server. Pour utiliser le fournisseur d’état de session de mise en cache, configurez votre cache puis votre application ASP.NET pour Cache à l’aide du package Cache NuGet, comme indiqué dans [Prise en main du Service de cache][Getting Started with Managed Cache Service]. Lorsque le package Cache NuGet est installé, il ajoute dans le fichier web.config une section placée en commentaire contenant la configuration requise pour que votre application ASP.NET utilise le fournisseur d'état de session d'Azure Cache.

    <!--Uncomment this section to use Azure Caching for session state caching
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

> Si votre fichier web.config ne contient pas cette section mise en commentaire après l’installation du package Cache NuGet, assurez-vous que le dernier Gestionnaire de package NuGet a été installé à partir de la page [Installation du Gestionnaire de package NuGet][NuGet Package Manager Installation], puis désinstallez et réinstallez le package.
> 
> 

Pour activer le fournisseur d'état de session d'Azure Cache, supprimez les marques de commentaire de la section spécifiée. Le cache par défaut est spécifié dans l'extrait fourni. Pour utiliser un autre cache, spécifiez le cache voulu dans l'attribut **cacheName** .

Pour plus d’informations sur l’utilisation du fournisseur d’état de session du Service de cache géré, consultez [Fournisseur d’état de session pour service de cache géré Azure][Session State Provider for Azure Cache].

<a name="store-page"></a>

## <a name="how-to-store-aspnet-page-output-caching-in-the-cache"></a>Stockage de la mise en cache de sortie de pages ASP.NET dans le cache
Le fournisseur de caches de sortie d'Azure Cache est un mécanisme de stockage hors processus pour les données de cache de sortie. Ces données concernent spécialement les réponses HTTP complètes (mise en cache de la sortie de pages). Le fournisseur se connecte au nouveau point d'extension du fournisseur de caches de sortie introduit dans ASP.NET 4. Pour utiliser le fournisseur de caches de sortie, configurez votre cluster de cache puis votre application ASP.NET pour la mise en cache à l’aide du package Cache NuGet, comme indiqué dans [Prise en main du Service de cache géré][Getting Started with Managed Cache Service]. Lorsque le package Cache NuGet est installé, il ajoute dans le fichier web.config la section commentée suivante, qui contient la configuration requise pour que votre application ASP.NET utilise le fournisseur de caches de sortie d'Azure Caching.

    <!--Uncomment this section to use Azure Caching for output caching
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

> Si votre fichier web.config ne contient pas cette section mise en commentaire après l’installation du package Cache NuGet, assurez-vous que le dernier Gestionnaire de package NuGet a été installé à partir de la page [Installation du Gestionnaire de package NuGet][NuGet Package Manager Installation], puis désinstallez et réinstallez le package.
> 
> 

Pour activer le fournisseur de caches de sortie d'Azure Cache, supprimez les marques de commentaire de la section spécifiée. Le cache par défaut est spécifié dans l'extrait fourni. Pour utiliser un autre cache, spécifiez le cache voulu dans l'attribut **cacheName** .

Ajoutez une directive **OutputCache** à chaque page pour laquelle vous voulez mettre en cache la sortie.

    <%@ OutputCache Duration="60" VaryByParam="*" %>

Dans cet exemple, les données de page mises en cache resteront dans le cache pendant 60 secondes et une version différente de la page sera mise en cache pour chaque combinaison de paramètres. Pour plus d’informations sur les options disponibles, consultez [Directive OutputCache][OutputCache Directive].

Pour plus d’informations sur l’utilisation du fournisseur de caches de sortie d’Azure Cache, consultez [Fournisseur de caches de sortie d’Azure Cache][Output Cache Provider for Azure Cache].

<a name="next-steps"></a>

## <a name="next-steps"></a>Étapes suivantes
À présent que vous avez appris les bases du Service de cache géré, suivez ces liens pour apprendre à exécuter les tâches de mise en cache plus complexes.

* Consultez la référence MSDN : [Service de cache géré][Managed Cache Service]
* Découvrez comment migrer vers le Service de cache géré : [Migration vers un service de cache géré Azure][Migrate to Managed Cache Service]
* Consultez les exemples : [Exemples de services de cache géré Azure][Managed Cache Service Samples]

<!-- INTRA-TOPIC LINKS -->
[Next Steps]: #next-steps
[What is Azure Managed Cache Service?]: #what-is
[Create an Azure Cache]: #create-cache
[Which type of caching is right for me?]: #choosing-cache
[Prepare Your Visual Studio Project to Use Azure Caching]: #prepare-vs
[Configure Your Application to Use Caching]: #configure-app
[Getting Started with Managed Cache Service]: #getting-started-cache-service
[Create the cache]: #create-cache
[Configure the cache]: #enable-caching
[Configure the cache clients]: #NuGet
[Working with Caches]: #working-with-caches
[How To: Create a DataCache Object]: #create-cache-object
[How To: Add and Retrieve an Object from the Cache]: #add-object
[How To: Specify the Expiration of an Object in the Cache]: #specify-expiration
[Stockage de l’état de session ASP.NET dans le cache]: #store-session
[How To: Store ASP.NET Page Output Caching in the Cache]: #store-page
[Target a Supported .NET Framework Profile]: #prepare-vs-target-net

<!-- IMAGES -->
[NewCacheMenu]: ./media/cache-dotnet-how-to-use-service/CacheServiceNewCacheMenu.png

[QuickCreate]: ./media/cache-dotnet-how-to-use-service/CacheServiceQuickCreate.png

[Endpoint]: ./media/cache-dotnet-how-to-use-service/CacheServiceEndpoint.png

[AccessKeys]: ./media/cache-dotnet-how-to-use-service/CacheServiceManageAccessKeys.png

[NuGetPackage]: ./media/cache-dotnet-how-to-use-service/CacheServiceManageNuGetPackage.png

[NuGetPackageMenu]: ./media/cache-dotnet-how-to-use-service/CacheServiceManageNuGetPackagesMenu.png

[NamedCaches]: ./media/cache-dotnet-how-to-use-service/CacheServiceNamedCaches.jpg


<!-- LINKS -->
[Azure Classic Portal]: https://manage.windowsazure.com/
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
[Overview of Azure Managed Cache Service]: http://go.microsoft.com/fwlink/?LinkId=320830
[Managed Cache Service]: http://go.microsoft.com/fwlink/?LinkId=320830
[OutputCache Directive]: http://go.microsoft.com/fwlink/?LinkId=251979
[Troubleshooting and Diagnostics]: http://go.microsoft.com/fwlink/?LinkId=320839
[NuGet Package Manager Installation]: http://go.microsoft.com/fwlink/?LinkId=240311
[Cache Pricing Details]: http://www.windowsazure.com/pricing/details/cache/
[Cache offerings]: http://go.microsoft.com/fwlink/?LinkId=317277
[Capacity planning]: http://go.microsoft.com/fwlink/?LinkId=320167
[Expiration and Eviction]: http://go.microsoft.com/fwlink/?LinkId=317278
[High Availability]: http://go.microsoft.com/fwlink/?LinkId=317329
[Notifications]: http://go.microsoft.com/fwlink/?LinkId=317276
[Migrate to Managed Cache Service]: http://go.microsoft.com/fwlink/?LinkId=317347
[Managed Cache Service Samples]: http://go.microsoft.com/fwlink/?LinkId=320840
[New-AzureManagedCache]: http://go.microsoft.com/fwlink/?LinkId=400495
[Azure Managed Cache Cmdlets]: http://go.microsoft.com/fwlink/?LinkID=398555
[How to install and configure Azure PowerShell]: http://go.microsoft.com/fwlink/?LinkId=400494
[Add-AzureAccount]: http://msdn.microsoft.com/library/dn495128.aspx
[Select-AzureSubscription]: http://msdn.microsoft.com/library/dn495203.aspx

[Which Azure Cache offering is right for me?]: cache-faq.md#which-azure-cache-offering-is-right-for-me




<!--HONumber=Jan17_HO3-->


