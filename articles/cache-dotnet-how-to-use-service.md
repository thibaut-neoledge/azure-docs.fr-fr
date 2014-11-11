<properties linkid="manage-services-cache-net-how-to-cache-service" urlDisplayName="" pageTitle="" metaKeywords="" description="" metaCanonical="" services="cache" documentationCenter="" title="How to Use Azure Managed Cache Service" authors="sdanie" solutions="" manager="" editor="" />

<tags ms.service="cache" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="sdanie" />

# Utilisation de Service de cache géré Azure

Ce guide décrit la prise en main du
**Service de cache géré Azure**. Les exemples sont écrits en C# et
utilisent l'API .NET. Les scénarios présentés comprennent la **création et la configuration d'un cache**, **la configuration de clients de cache**, **l'ajout et la suppression
d'objets dans le cache, le stockage de l'état de session ASP.NET dans le cache**
 et **l'activation du cache de la sortie de pages ASP.NET avec le cache**. Pour plus
d'informations sur l'utilisation d'Azure Cache, consultez la section [Étapes suivantes][Étapes suivantes].

> Pour obtenir des informations sur le choix de l'offre Azure Cache appropriée pour votre application, consultez [Choix de l'offre Azure Cache appropriée][Choix de l'offre Azure Cache appropriée].

## Sommaire

-   [Qu'est-ce que le Service de cache géré Azure ?][Qu'est-ce que le Service de cache géré Azure ?]
-   [Prise en main du Service de cache géré][Prise en main du Service de cache géré]
    -   [Création du cache][Création du cache]
    -   [Configuration du cache][Configuration du cache]
    -   [Configuration des clients de cache][Configuration des clients de cache]
-   [Utilisation des caches][Utilisation des caches]
    -   [Création d'un objet DataCache][Création d'un objet DataCache]
    -   [Ajout et récupération d'un objet dans le cache][Ajout et récupération d'un objet dans le cache]
    -   [Spécification de l'expiration d'un objet dans le cache][Spécification de l'expiration d'un objet dans le cache]
    -   [Stockage de l'état de session ASP.NET dans le cache][Stockage de l'état de session ASP.NET dans le cache]
    -   [Stockage de la mise en cache de sortie de pages ASP.NET dans le cache][Stockage de la mise en cache de sortie de pages ASP.NET dans le cache]
-   [Étapes suivantes][Étapes suivantes]

<a name="what-is"></a>

## Qu'est-ce que le Service de cache géré Azure ?

Le Service de cache géré Azure est une solution distribuée, en mémoire et évolutive qui vous permet de créer des applications hautement évolutives et réactives en fournissant un accès ultra-rapide aux données.

Azure Cache Service (Preview) inclut les fonctionnalités
suivantes :

-   Fournisseurs ASP.NET préconfigurés pour la mise en cache de l'état de session et de
    la sortie de pages, permettant d'accélérer les applications Web sans avoir à
    modifier le code d'application.
-   Mise en cache de tout objet managé pouvant être sérialisé, par exemple : objets CLR, lignes, XML,
    données binaires.
-   Modèle de développement cohérent dans Azure et Windows
    Server AppFabric.

Service de cache géré vous offre l'accès à un cache dédié sécurisé qui est géré par Microsoft. Un cache créé avec Service de cache géré est accessible depuis les applications dans Azure exécutées sur les sites Web, les rôles Web et de travail et les machines virtuelles Azure.

Service de cache géré est disponible en trois options :

-   Basic - Tailles de cache comprises entre 128 Mo et 1 Go
-   Standard - Tailles de cache comprises entre 1 Go et 10 Go
-   Premium - Tailles de cache comprises entre 5 Go et 150 Go

Chaque option diffère en termes de fonctionnalités et de tarification. Les fonctionnalités sont décrites plus loin dans ce guide ; pour plus d'informations sur la tarification, consultez la page [Tarification - Cache][Tarification - Cache].

Ce guide fournit un aperçu de la prise en main de Service de cache géré. Pour plus d'informations sur les fonctions qui ne sont pas présentées dans ce guide de prise en main, consultez la page [Présentation de Service de cache géré Azure][Présentation de Service de cache géré Azure].

<a name="getting-started-cache-service"></a>

## Prise en main du Service de cache

La prise en main de Service de cache géré est aisée. Pour commencer, vous mettez en service et configurez un cache. Ensuite, vous configurez les clients du cache pour qu'ils puissent accéder au cache. Une fois les clients du cache configurés, vous pouvez commencer à les utiliser.

-   [Création du cache][Création du cache]
-   [Configuration du cache][Configuration du cache]
-   [Configuration des clients de cache][Configuration des clients de cache]

<a name="create-cache"></a>

## Création d'un cache

Les instances de cache dans Service de cache géré sont créées à l'aide de cmdlets PowerShell.

> Une fois qu'une instance de Service de cache géré a été créée à l'aide de cmdlets PowerShell, celle-ci peut être affichée et configurée dans le [Portail de gestion Azure][Portail de gestion Azure].

Pour créer une instance de Service de cache géré, ouvrez la fenêtre de commande Azure PowerShell.

> Pour des instructions sur l'installation et l'utilisation d'Azure PowerShell, consultez [Installation et configuration d'Azure PowerShell][Installation et configuration d'Azure PowerShell].

Appelez le cmdlet [Add-AzureAccount][Add-AzureAccount], puis saisissez l'adresse électronique et le mot de passe associés à votre compte. Un abonnement est choisi par défaut et s'affiche après l'appel du cmdlet [Add-AzureAccount][Add-AzureAccount]. Pour modifier l'abonnement, appelez le cmdlet [Select-AzureSubscription][Select-AzureSubscription].

> Si vous avez configuré Azure PowerShell avec un certificat pour votre compte, vous pouvez passer cette étape. Pour plus d'informations sur la connexion d'Azure PowerShell avec votre compte Azure, consultez [Installation et configuration d'Azure PowerShell][Installation et configuration d'Azure PowerShell].

Un abonnement est choisi par défaut et s'affiche. Pour modifier l'abonnement, appelez le cmdlet [Select-AzureSubscription][Select-AzureSubscription].

Appelez le cmdlet [New-AzureManagedCache][New-AzureManagedCache] et spécifiez le nom, la région, l'offre de cache, et la taille de cache.

Dans **Nom**, entrez le nom de sous-domaine à utiliser pour le point de terminaison du cache. Le point de terminaison doit être une chaîne composée de six à vingt caractères, contenant uniquement des minuscules et des chiffres, et commençant par une lettre.

Dans **Emplacement**, sélectionnez la région du cache. Pour des performances optimales, créez le cache dans la même région que l'application cliente du cache.

**Sku** et **Mémoire** œuvrent ensemble pour déterminer la taille du cache. Service de cache géré est disponible dans les trois options suivantes :

-   Basic - Tailles de cache comprises entre 128 Mo et 1 Go par incréments de 128 Mo, avec un seul cache nommé par défaut
-   Standard - Tailles de cache comprises entre 1 Go et 10 Go par incréments de 1 Go, avec prise en charge des notifications et de dix caches nommés (maximum)
-   Premium - Tailles de cache comprises entre 5 Go et 150 Go par incréments de 5 Go, avec prise en charge des notifications, de la haute disponibilité et de dix caches nommés (maximum)

Choisissez le **Sku** et la **Mémoire** qui répondent aux besoins de votre application. Notez que certaines fonctionnalités du cache, telles les notifications et la haute disponibilité, ne sont disponibles qu'avec certaines offres de cache. Pour plus d'informations sur le choix de l'offre et de la taille de cache les plus adaptées à votre application, consultez la page [Offres de cache][Offres de cache].

Dans l'exemple suivant, un cache Basic de 128 Mo est créé avec le nom contosocache dans la région du centre-sud des États-Unis.

    New-AzureManagedCache -Name contosocache -Location "South Central US" -Sku Basic -Memory 128MB

> Pour obtenir la liste complète des paramètres et des valeurs pouvant être utilisés lors de la création d'un cache, consultez la documentation du cmdlet [New-AzureManagedCache][New-AzureManagedCache].

Une fois le cmdlet PowerShell appelé, la création du cache peut prendre quelques minutes. Une fois le cache créé, votre nouveau cache prend l'état `Running`. Il peut alors être utilisé avec les paramètres par défaut ou être affiché et configuré dans le [Portail de gestion Azure][Portail de gestion Azure]. Pour personnaliser la configuration de votre cache, reportez-vous à la section [Configuration du cache][Configuration du cache].

Vous pouvez suivre la progression de la création dans la fenêtre Azure PowerShell. Une fois le cache prêt à être utilisé, le cmdlet [New-AzureManagedCache][New-AzureManagedCache] affichera les informations de cache, comme illustré dans l'exemple suivant.

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

## Configuration du cache

L'onglet **Configurer** de Cache dans le portail de gestion vous permet de configurer les options de votre cache. Chaque cache a un cache nommé **par défaut**, et les offres de cache Standard et Premium prennent en charge jusqu'à neuf caches nommés supplémentaires, pour un total de dix. Chaque cache nommé a son propre ensemble d'options, qui vous permettent de configurer votre cache avec une grande souplesse.

![NamedCaches][NamedCaches]

Pour créer un cache nommé, tapez le nom du nouveau cache dans la zone **Nom**, spécifiez les options voulues, cliquez sur **Enregistrer**, puis sur **Oui** pour confirmer. Pour annuler les modifications, cliquez sur **Discard**.

## Stratégie d'expiration et Durée (min)

La **Stratégie d'expiration** fonctionne avec le paramètre **Time (min)** pour déterminer à quel moment les éléments mis en cache expirent. Il existe trois types de stratégies d'expiration : **Absolute**, **Sliding** et **Jamais**.

Lorsque **Absolute** est spécifié, l'intervalle d'expiration spécifié par **Durée (min)** commence au moment où un élément est ajouté au cache. Lorsque l'intervalle spécifié par **Durée (min)** est écoulé, l'élément expire.

Lorsque **Sliding** est spécifié, l'intervalle d'expiration spécifié par **Durée (min)** est réinitialisé chaque fois qu'un élément fait l'objet d'un accès dans le cache. L'élément n'expire pas tant que l'intervalle spécifié par **Durée (min)**, calculé à partir du dernier accès, n'est pas écoulé.

Lorsque **Jamais** est spécifié, **Durée (min)** doit être défini sur **0** et les éléments n'expirent pas.

**Absolute** est la stratégie d'expiration par défaut, et 10 minutes la valeur par défaut pour **Durée (min)**. La stratégie d'expiration est fixée pour chaque élément dans un cache nommé, mais le paramètre **Durée (min)** peut être personnalisé pour chaque élément avec les surcharges des méthodes **Add** et **Put**, qui prennent un paramètre Délai d'expiration.

Pour plus d'informations sur les stratégies d'éviction et d'expiration, consultez la page [Expiration et éviction][Expiration et éviction].

## Notifications

Les notifications du cache permettent à vos applications de recevoir des notifications asynchrones lorsque diverses opérations de cache ont lieu sur le cluster de cache. Elles fournissent également l'invalidation automatique des objets mis en cache localement. Pour plus d'informations, consultez la page [Notifications][Notifications].

> Les notifications sont disponibles dans les offres de cache Standard et Premium, mais pas dans l'offre Basic. Pour plus d'informations, consultez la page [Offres de cache][Offres de cache].

## Haute disponibilité

Lorsque la haute disponibilité est activée, une copie de sauvegarde de chaque élément ajouté au cache est créée. Si une défaillance inattendue se produit sur la copie principale de l'élément, la copie de sauvegarde reste disponible.

Par définition, l'utilisation de la haute disponibilité multiplie par deux la quantité de mémoire requise pour chaque élément en cache. Tenez compte de cet impact sur la mémoire pendant les tâches de planification de la capacité. Pour plus d'informations, consultez la page [Haute disponibilité][Haute disponibilité].

> La haute disponibilité est disponible dans l'offre de cache Premium, mais pas dans les offres Basic ou Standard. Pour plus d'informations, consultez la page [Offres de cache][Offres de cache].

## Éviction

Pour maintenir la capacité de mémoire disponible à l'intérieur d'un cache, la dernière éviction récemment utilisée (LRU) est prise en charge. Lorsque la consommation de mémoire dépasse le seuil de mémoire, les objets sont expulsés de la mémoire, qu'ils aient expiré ou pas, jusqu'à ce que la pression sur la mémoire se relâche.
L'éviction est activée par défaut. Si elle est désactivée, les éléments ne sont pas expulsés du cache lorsque la capacité est atteinte, et les opérations Put et Add échouent.

Pour plus d'informations sur les stratégies d'éviction et d'expiration, consultez la page [Expiration et éviction][Expiration et éviction].

Une fois le cache configuré, vous pouvez configurer les clients du cache pour autoriser l'accès au cache.

<a name="NuGet"></a>

## Configuration des clients de cache

Un cache créé avec Service de cache géré est accessible depuis les applications Azure exécutées sur les sites Web, les rôles Web et de travail et les machines virtuelles Azure. Un package NuGet est fourni pour simplifier la configuration des applications clientes du cache.

Pour configurer une application cliente avec le package Cache NuGet, cliquez avec le bouton droit sur le projet dans l'**Explorateur de solutions** et choisissez **Manage NuGet Packages**.

![NuGetPackageMenu][NuGetPackageMenu]

Tapez **WindowsAzure.Caching** dans la zone de texte **Rechercher en ligne**, et choisissez **Windows Azure Cache** parmi les résultats. Cliquez sur **Installer**, puis sur **J'accepte**.

![NuGetPackage][NuGetPackage]

Le package NuGet effectue plusieurs opérations : il ajoute la configuration requise au fichier de configuration de l'application et il ajoute les références d'assembly requises. Pour les projets Cloud Services, il ajoute également le niveau de diagnostic des clients du cache comme paramètre au fichier ServiceConfiguration.cscfg du service cloud.

> Pour les projets Web ASP.NET, le package Cache NuGet ajoute aussi deux sections placées en commentaire au fichier web.config. La première section permet de stocker l'état de session dans le cache et la seconde section permet de mettre en cache la sortie de pages ASP.NET. Pour plus d'informations, consultez les pages [Stockage de l'état de session ASP.NET dans le cache][Stockage de l'état de session ASP.NET dans le cache] et [Stockage de la mise en cache de sortie de pages ASP.NET dans le cache][Stockage de la mise en cache de sortie de pages ASP.NET dans le cache]

Le package NuGet ajoute les éléments de configuration suivants dans le fichier web.config ou app.config de votre application. Une section **dataCacheClients** et une section **cacheDiagnostics** sont ajoutées sous l'élément **configSections**. Si aucun élément **configSections** n'est présent, un élément configSections enfant de l'élément **configuration** est créé.

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

Ces nouvelles sections incluent des références à un élément **dataCacheClients**, qui est également ajouté à l'élément **configuration**.

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

1.  Remplacez **[Cache role name ou Service Endpoint]** par le point de terminaison, qui est affiché sur le tableau de bord du portail de gestion.

    ![Point de terminaison][Point de terminaison]

2.  Supprimez les marques de commentaire de la section securityProperties et remplacez **[Authentication Key]** par la clé d'authentification, que vous pouvez trouver dans le portail de gestion en cliquant sur **Manage Keys** dans le tableau de bord du cache.

    ![AccessKeys][AccessKeys]

> Ces paramètres doivent être configurés correctement, sinon les clients ne pourront pas accéder au cache.

Pour les projets Cloud Services, le package NuGet ajoute également un paramètre **ClientDiagnosticLevel** à l'élément **ConfigurationSettings** du rôle client du cache dans le fichier ServiceConfiguration.cscfg. L'exemple suivant est la section **WebRole1** d'un fichier ServiceConfiguration.cscfg avec un **ClientDiagnosticLevel** égal à 1, la valeur par défaut pour ce paramètre.

    <Role name="WebRole1">
      <Instances count="1" />
      <ConfigurationSettings>
        <!-- Existing settings omitted for clarity. -->
        <Setting name="Microsoft.WindowsAzure.Plugins.Caching.ClientDiagnosticLevel" 
                 value="1" />
      </ConfigurationSettings>
    </Role>

> Le niveau de diagnostic des clients configure le niveau des informations de diagnostic de la mise en cache collectées pour les clients du cache. Pour plus d'informations, consultez la page [Dépannage et diagnostics][Dépannage et diagnostics].

Le package NuGet ajoute également des références aux assemblys suivants :

-   Microsoft.ApplicationServer.Caching.Client.dll
-   Microsoft.ApplicationServer.Caching.Core.dll
-   Microsoft.WindowsFabric.Common.dll
-   Microsoft.WindowsFabric.Data.Common.dll
-   Microsoft.ApplicationServer.Caching.AzureCommon.dll
-   Microsoft.ApplicationServer.Caching.AzureClientHelper.dll

Si votre projet est un projet Web, la référence d'assembly suivante est également ajoutée :

-   Microsoft.Web.DistributedCache.dll.

> Ces assemblys se trouvent dans le dossier C:\\Program Files\\Microsoft SDKs\\Windows Azure\\.NET SDK\\[sdk version]\\ref\\Caching\\.

Une fois que votre projet client est configuré pour la mise en cache, vous pouvez utiliser les techniques décrites dans les sections suivantes pour utiliser votre cache.

<a name="working-with-caches"></a>

## Utilisation des caches

Cette section décrit l'exécution des tâches courantes avec Cache.

-   [Création d'un objet DataCache][Création d'un objet DataCache]
-   [Ajout et récupération d'un objet dans le cache][Ajout et récupération d'un objet dans le cache]
-   [Spécification de l'expiration d'un objet dans le cache][Spécification de l'expiration d'un objet dans le cache]
-   [Stockage de l'état de session ASP.NET dans le cache][Stockage de l'état de session ASP.NET dans le cache]
-   [Stockage de la mise en cache de sortie de pages ASP.NET dans le cache][Stockage de la mise en cache de sortie de pages ASP.NET dans le cache]

<a name="create-cache-object"></a>

## Désactivation Création d'un objet DataCache

Pour utiliser un cache par programme, vous avez besoin d'une référence au cache. Ajoutez la ligne suivante au début de chaque fichier à partir duquel vous voulez utiliser
Azure Cache :

    using Microsoft.ApplicationServer.Caching;

> Si Visual Studio ne reconnaît pas les types dans l'instruction
> d'utilisation même après installation du package Caching NuGet, qui ajoute les références nécessaires, assurez-vous que le profil
> cible du projet est .NET Framework 4 ou version ultérieure et sélectionnez un des profils qui ne spécifie pas **Client Profile**. Pour obtenir des instructions sur la configuration des clients du cache, consultez la page [Configuration des clients du cache][Configuration des clients de cache].

Il existe plusieurs méthodes pour créer un objet **DataCache**. La première consiste à créer simplement un **DataCache** en transmettant le nom du cache voulu.

    DataCache cache = new DataCache("default");

Une fois que le **DataCache** est instancié, vous pouvez l'utiliser pour interagir avec le cache, comme décrit dans les sections suivantes.

Pour utiliser la seconde méthode, créez un objet **DataCacheFactory** dans votre application à l'aide du constructeur par défaut. Le client du cache utilise alors les paramètres présents dans le fichier de configuration. Appelez soit la méthode **GetDefaultCache** de la nouvelle instance de **DataCacheFactory**, qui renvoie un objet **DataCache**, soit la méthode **GetCache**, puis transmettez le nom de votre cache. Ces méthodes renvoient un objet **DataCache** qui peut être utilisé pour accéder au cache par programme.

    // Cache client configured by settings in application configuration file.
    DataCacheFactory cacheFactory = new DataCacheFactory();
    DataCache cache = cacheFactory.GetDefaultCache();
    // Or DataCache cache = cacheFactory.GetCache("MyCache");
    // cache can now be used to add and retrieve items. 

<a name="add-object"></a>

## Désactivation Ajout et récupération d'un objet dans le cache

Pour ajouter un élément au cache, vous pouvez utiliser la méthode **Add** ou **Put**.
 La méthode **Add** ajoute l'objet spécifié au cache,
indexé par la valeur du paramètre de clé.

    // Add the string "value" to the cache, keyed by "item"
    cache.Add("item", "value");

Si un objet avec la même clé est déjà présent dans le cache, une
**DataCacheException** est levée avec le message suivant :

> ErrorCode:SubStatus: tentative de création d'un objet avec
> une clé qui existe déjà dans le cache. La mise en cache accepte uniquement
> des valeurs de clé uniques pour les objets.

Pour récupérer un objet avec une clé particulière, vous pouvez utiliser la méthode **Get**. Si l'objet existe, il est
renvoyé ; sinon, la valeur null est renvoyée.

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

La méthode **Put** ajoute l'objet avec la clé spécifiée au cache
s'il n'existe pas ou remplace l'objet s'il existe.

    // Add the string "value" to the cache, keyed by "item". If it exists,
    // replace it.
    cache.Put("item", "value");

<a name="specify-expiration"></a>

## Désactivation Spécification de l'expiration d'un objet dans le cache

Par défaut, les éléments du cache expirent 10 minutes après qu'ils y ont été placés. Ce délai peut être configuré dans le paramètre **Durée (min)** sous l'onglet Configurer du portail de gestion.

![NamedCaches][NamedCaches]

Il existe trois types de **Stratégie d’expiration** : **Jamais**, **Absolue** et **Sliding**. Ces stratégies configurent la façon dont le paramètre **Durée (min)** est utilisé pour déterminer l'expiration. Le **Type d'expiration** par défaut est **Absolute**, ce qui signifie que le compte à rebours pour l'expiration d'un élément commence lorsque l'élément est placé dans le cache. Une fois que le délai spécifié pour un élément est écoulé, l'élément expire. Si **Sliding** est spécifié, le compte à rebours du délai d'expiration d'un élément est réinitialisé chaque fois que l'élément fait l'objet d'un accès dans le cache et l'élément expire lorsque le délai spécifié, calculé à partir du dernier accès, est écoulé. Si **Jamais** est spécifié, **Durée (min)** doit être défini sur **0** ; les éléments n'expirent pas et restent valides tant qu'ils sont dans le cache.

Si vous souhaitez une durée d'expiration plus longue ou plus courte que celle configurée dans les propriétés du cache, cette durée spécifique peut être indiquée lors de l'ajout ou de la mise à jour d'un élément dans le cache à l'aide de
la surcharge **Add** et **Put**, qui peuvent prendre le paramètre **TimeSpan**. Dans
l'exemple suivant, la chaîne **value** est ajoutée au cache, indexée par
**item**, avec un délai d'expiration de 30 minutes.

    // Add the string "value" to the cache, keyed by "item"
    cache.Add("item", "value", TimeSpan.FromMinutes(30));

Pour afficher l'intervalle restant du délai d'expiration d'un élément dans le cache, vous pouvez utiliser la méthode
**GetCacheItem** afin de récupérer un objet **DataCacheItem**
 contenant des informations sur l'élément dans le cache, y compris
l'intervalle restant du délai d'expiration.

    // Get a DataCacheItem object that contains information about
    // "item" in the cache. If there is no object keyed by "item" null
    // is returned. 
    DataCacheItem item = cache.GetCacheItem("item");
    TimeSpan timeRemaining = item.Timeout;

<a name="store-session"></a>

## Désactivation Stockage de l'état de session ASP.NET dans le cache

Le fournisseur d'état de session d'Azure Cache est un
mécanisme de stockage hors processus pour les applications ASP.NET. Il vous
permet de stocker l'état de votre session dans un cache Azure plutôt
qu'en mémoire ou dans une base de données SQL Server. Pour utiliser le fournisseur
d'état de session de mise en cache, commencez par configurer votre cache, puis configurez votre application ASP.NET pour Cache à l'aide du package Cache NuGet, comme décrit dans la page [Prise en main du Service de cache géré][Prise en main du Service de cache géré]. Lorsque le package Cache NuGet est installé, il ajoute dans le fichier web.config une section placée en commentaire contenant la configuration requise pour que votre application ASP.NET utilise le fournisseur d'état de session d'Azure Cache.

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

> Si votre fichier web.config ne contient pas cette section placée en commentaire après l'installation du package Cache NuGet, assurez-vous que le dernier Gestionnaire de package NuGet a été installé à partir de la page [Installation du Gestionnaire de package NuGet][Installation du Gestionnaire de package NuGet], puis désinstallez et réinstallez le package.

Pour activer le fournisseur d'état de session d'Azure Cache, supprimez les marques de commentaire de la section spécifiée. Le cache par défaut est spécifié dans l'extrait fourni. Pour utiliser un autre cache, spécifiez le cache voulu dans l'attribut **cacheName**.

Pour plus d'informations sur l'utilisation du fournisseur d'état de session du
Service de cache géré, consultez la page [Fournisseur d'état de session d'Azure Cache][Fournisseur d'état de session d'Azure Cache].

<a name="store-page"></a>

## Désactivation Stockage de la mise en cache de sortie de pages ASP.NET dans le cache

Le fournisseur de caches de sortie d'Azure Cache est un mécanisme de stockage hors processus pour les données de cache de sortie. Ces données concernent spécialement les réponses
HTTP complètes (mise en cache de la sortie de pages). Le fournisseur se connecte sur le nouveau
point d'extensibilité du fournisseur de caches de sortie introduit dans ASP.NET 4. Pour
utiliser ce fournisseur de caches de sortie, configurez d'abord votre cluster de cache, puis configurez votre application ASP.NET pour la mise en cache à l'aide du package Cache NuGet, comme décrit dans [Prise en main du Service de cache géré][Prise en main du Service de cache géré]. Lorsque le package Cache NuGet est installé, il ajoute dans le fichier web.config la section commentée suivante, qui contient la configuration requise pour que votre application ASP.NET utilise le fournisseur de caches de sortie d'Azure Caching.

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

> Si votre fichier web.config ne contient pas cette section placée en commentaire après l'installation du package Cache NuGet, assurez-vous que le dernier Gestionnaire de package NuGet a été installé à partir de la page [Installation du Gestionnaire de package NuGet][Installation du Gestionnaire de package NuGet], puis désinstallez et réinstallez le package.

Pour activer le fournisseur de caches de sortie d'Azure Cache, supprimez les marques de commentaire de la section spécifiée. Le cache par défaut est spécifié dans l'extrait fourni. Pour utiliser un autre cache, spécifiez le cache voulu dans l'attribut **cacheName**.

Ajoutez une directive **OutputCache** à chaque page pour laquelle vous voulez mettre en cache la sortie.

    <%@ OutputCache Duration="60" VaryByParam="*" %>

Dans cet exemple, les données de page mises en cache resteront dans le cache pendant 60 secondes et une version différente de la page sera mise en cache pour chaque combinaison de paramètres. Pour plus d'informations sur les options disponibles, consultez la page [Directive OutputCache][Directive OutputCache].

Pour plus d'informations sur l'utilisation du fournisseur de caches de sortie d'Azure Cache, consultez la page [Fournisseur de caches de sortie d'Azure Cache][Fournisseur de caches de sortie d'Azure Cache].

<a name="next-steps"></a>

## Étapes suivantes

Maintenant que vous avez appris les bases du Service de cache géré,
suivez ces liens pour apprendre à exécuter les tâches de mise en cache plus complexes.

-   Consultez la référence MSDN suivante : [Service de cache géré][Présentation de Service de cache géré Azure]
-   Apprenez comment migrer vers Service de cache géré : [Migrer vers Service de cache géré][Migrer vers Service de cache géré]
-   Consultez les exemples : [Exemples de Service de cache géré][Exemples de Service de cache géré]


  [Étapes suivantes]: #next-steps
  [Choix de l'offre Azure Cache appropriée]: http://msdn.microsoft.com/fr-fr/library/azure/dn766201.aspx
  [Prise en main du Service de cache géré]: #getting-started-cache-service
  [Création du cache]: #create-cache
  [Configuration du cache]: #enable-caching
  [Configuration des clients de cache]: #NuGet
  [Utilisation des caches]: #working-with-caches
  [Création d'un objet DataCache]: #create-cache-object
  [Ajout et récupération d'un objet dans le cache]: #add-object
  [Spécification de l'expiration d'un objet dans le cache]: #specify-expiration
  [Stockage de l'état de session ASP.NET dans le cache]: #store-session
  [Stockage de la mise en cache de sortie de pages ASP.NET dans le cache]: #store-page
  [Tarification - Cache]: http://www.windowsazure.com/fr-fr/pricing/details/cache/
  [Présentation de Service de cache géré Azure]: http://go.microsoft.com/fwlink/?LinkId=320830
  [Portail de gestion Azure]: https://manage.windowsazure.com/
  [Installation et configuration d'Azure PowerShell]: http://go.microsoft.com/fwlink/?LinkId=400494
  [Add-AzureAccount]: http://msdn.microsoft.com/fr-fr/library/dn495128.aspx
  [Select-AzureSubscription]: http://msdn.microsoft.com/fr-fr/library/dn495203.aspx
  [New-AzureManagedCache]: http://go.microsoft.com/fwlink/?LinkId=400495
  [Offres de cache]: http://go.microsoft.com/fwlink/?LinkId=317277
  [NamedCaches]: ./media/cache-dotnet-how-to-use-service/CacheServiceNamedCaches.jpg
  [Expiration et éviction]: http://go.microsoft.com/fwlink/?LinkId=317278
  [Notifications]: http://go.microsoft.com/fwlink/?LinkId=317276
  [Haute disponibilité]: http://go.microsoft.com/fwlink/?LinkId=317329
  [NuGetPackageMenu]: ./media/cache-dotnet-how-to-use-service/CacheServiceManageNuGetPackagesMenu.png
  [NuGetPackage]: ./media/cache-dotnet-how-to-use-service/CacheServiceManageNuGetPackage.png
  [Point de terminaison]: ./media/cache-dotnet-how-to-use-service/CacheServiceEndpoint.png
  [AccessKeys]: ./media/cache-dotnet-how-to-use-service/CacheServiceManageAccessKeys.png
  [Dépannage et diagnostics]: http://go.microsoft.com/fwlink/?LinkId=320839
  [Installation du Gestionnaire de package NuGet]: http://go.microsoft.com/fwlink/?LinkId=240311
  [Fournisseur d'état de session d'Azure Cache]: http://go.microsoft.com/fwlink/?LinkId=320835
  [Directive OutputCache]: http://go.microsoft.com/fwlink/?LinkId=251979
  [Fournisseur de caches de sortie d'Azure Cache]: http://go.microsoft.com/fwlink/?LinkId=320837
  [Migrer vers Service de cache géré]: http://go.microsoft.com/fwlink/?LinkId=317347
  [Exemples de Service de cache géré]: http://go.microsoft.com/fwlink/?LinkId=320840
