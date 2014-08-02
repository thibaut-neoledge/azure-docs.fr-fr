<!-- saved from url=(0022)http://internet.e-mail -->

Utilisation d'Azure Cache Service (Preview)
===========================================

Ce guide décrit la prise en main d'**Azure Cache Service (Preview)**. Les exemples sont écrits en C\# et utilisent l'API .NET. Les scénarios couverts incluent la **création et la configuration d'un cache**, **la configuration des clients du cache**, **l'ajout et la suppression d'objets dans le cache, le stockage de l'état de session ASP.NET dans le cache** et **l'activation de la mise en cache de sortie de pages ASP.NET avec le cache**. Pour plus d'informations sur l'utilisation d'Azure Cache, consultez la section [Étapes suivantes](#next-steps).

Sommaire
--------

-   [Présentation d'Azure Cache](#what-is)
-   [Prise en main de Cache Service (Preview)](#getting-started-cache-service)
    -   [Création du cache](#create-cache)
    -   [Configuration du cache](#enable-caching)
    -   [Configuration des clients du cache](#NuGet)
-   [Utilisation des caches](#working-with-caches)
    -   [Procédure : Création d'un objet DataCache](#create-cache-object)
    -   [Procédure : Ajout et récupération d'un objet dans le cache](#add-object)
    -   [Procédure : Spécification de l'expiration d'un objet dans le cache](#specify-expiration)
    -   [Procédure : Stockage de l'état de session ASP.NET dans le cache](#store-session)
    -   [Procédure : Stockage de la mise en cache de sortie de pages ASP.NET dans le cache](#store-page)
-   [Étapes suivantes](#next-steps)

## Présentation d'Azure Cache

Azure Cache Service (Preview) est une solution distribuée, en mémoire et évolutive qui vous permet de créer des applications hautement évolutives et réactives en fournissant un accès ultra-rapide aux données.

Azure Cache Service (Preview) inclut les fonctionnalités suivantes :

-   Fournisseurs ASP.NET préconfigurés pour la mise en cache de l'état de session et de la sortie de pages, permettant d'accélérer les applications Web sans avoir à modifier le code d'application.
-   Mise en cache de tout objet managé pouvant être sérialisé, par exemple : objets CLR, lignes, XML, données binaires.
-   Modèle de développement cohérent dans Azure et Windows Server AppFabric.

Cache Service (Preview) vous offre l'accès à un cache dédié sécurisé qui est géré par Microsoft. Un cache créé avec Cache Service (Preview) est accessible depuis les applications dans Azure exécutées sur les sites Web, les rôles Web et de travail et les machines virtuelles Azure.

Cache Service (Preview) est disponible en trois options :

-   Basic - Tailles de cache comprises entre 128 Mo et 1 Go
-   Standard - Tailles de cache comprises entre 1 Go et 10 Go
-   Premium - Tailles de cache comprises entre 5 Go et 150 Go

Chaque option diffère en termes de fonctionnalités et de tarification. Les fonctionnalités sont décrites plus loin dans ce guide ; pour plus d'informations sur la tarification, consultez la page [Tarification - Cache](http://www.windowsazure.com/en-us/pricing/details/cache/).

Ce guide fournit un aperçu de la prise en main de Cache Service (Preview). Pour des informations plus détaillées sur ces fonctionnalités qui n'entrent pas dans le cadre du présent guide de prise en main, consultez la page [Présentation d'Azure Cache Service (Preview)](http://go.microsoft.com/fwlink/?LinkId=320830).

## Prise en main de Cache Service (Preview)

La prise en main de Cache Service (Preview) est aisée. Pour commencer, vous mettez en service et configurez un cache. Ensuite, vous configurez les clients du cache pour qu'ils puissent accéder au cache. Une fois les clients du cache configurés, vous pouvez commencer à les utiliser.

-   [Création du cache](#create-cache)
-   [Configuration du cache](#enable-caching)
-   [Configuration des clients du cache](#NuGet)

## Création d'un cache

Pour créer un cache, connectez-vous d'abord au [portail de gestion](https://manage.windowsazure.com/).

> Si vous utilisez Cache Service (Preview) pour la première fois, vous devez demander l'accès au programme d'évaluation de Cache Service. Pour vous inscrire à ce programme, cliquez sur **Nouveau**, **Data Services**, **Cache Preview**, **Programme d'évaluation**. Suivez les invites pour demander l'accès au programme d'évaluation de Cache Service et, une fois l'accès accordé, passez aux étapes suivantes.

Cliquez sur **Nouveau**, **Data Services**, **Cache Preview**, **Quick Create**.

![NewCacheMenu](./media/cache-dotnet-how-to-use-service/CacheServiceNewCacheMenu.png)

![QuickCreate](./media/cache-dotnet-how-to-use-service/CacheServiceQuickCreate.png)

Dans **Point de terminaison**, entrez le nom de sous-domaine à utiliser pour le point de terminaison du cache. Le point de terminaison doit être une chaîne composée de six à vingt caractères, contenant uniquement des minuscules et des chiffres, et commençant par une lettre.

Dans **Région**, sélectionnez la région du cache. Pour des performances optimales, créez le cache dans la même région que l'application cliente du cache.

Dans **Abonnement**, sélectionnez l'abonnement Azure que vous voulez utiliser pour le cache.

> Si votre compte a un seul abonnement, il sera automatiquement sélectionné et la liste déroulante Abonnement ne sera pas affichée.

**Offre de cache** et **Mémoire cache** œuvrent ensemble pour déterminer la taille du cache. Cache Service (Preview) est disponible en trois options :

-   Basic - Tailles de cache comprises entre 128 Mo et 1 Go par incréments de 128 Mo, avec un seul cache nommé par défaut
-   Standard - Tailles de cache comprises entre 1 Go et 10 Go par incréments de 1 Go, avec prise en charge des notifications et de dix caches nommés (maximum)

-   Premium - Tailles de cache comprises entre 5 Go et 150 Go par incréments de 5 Go, avec prise en charge des notifications, de la haute disponibilité et de dix caches nommés (maximum)

Choisissez l'**Offre de cache** et la **Mémoire cache** qui répondent aux besoins de votre application. Notez que certaines fonctionnalités du cache, telles les notifications et la haute disponibilité, ne sont disponibles qu'avec certaines offres de cache. Pour plus d'informations sur le choix de l'offre et de la taille de cache les plus adaptées à votre application, consultez les pages [Offres de cache](http://go.microsoft.com/fwlink/?LinkId=317277) et [Planification de la capacité](http://go.microsoft.com/fwlink/?LinkId=320167).

Une fois les options du nouveau cache configurées, cliquez sur **Créer un nouveau cache**. La création du cache peut prendre plusieurs minutes. Pour vérifier l'état d'avancement de l'opération, vous pouvez contrôler les notifications au bas du portail. Après sa création, le nouveau cache a le statut En cours d'exécution et il est prêt à fonctionner avec les paramètres par défaut. Pour personnaliser la configuration de votre cache, reportez-vous à la section [Configuration du cache](#enable-caching).

## Configuration du cache

L'onglet **Configurer** de Cache dans le portail de gestion vous permet de configurer les options de votre cache. Chaque cache a un cache nommé **par défaut**, et les offres de cache Standard et Premium prennent en charge jusqu'à neuf caches nommés supplémentaires, pour un total de dix. Chaque cache nommé a son propre ensemble d'options, qui vous permettent de configurer votre cache avec une grande souplesse.

![NamedCaches](./media/cache-dotnet-how-to-use-service/CacheServiceNamedCaches.jpg)

Pour créer un cache nommé, tapez le nom du nouveau cache dans la zone **Nom**, spécifiez les options voulues, cliquez sur **Enregistrer**, puis sur **Oui** pour confirmer. Pour annuler les modifications, cliquez sur **Discard**.

Stratégie d'expiration et Durée (min)
-------------------------------------

La **Stratégie d'expiration** fonctionne avec le paramètre **Time (min)** pour déterminer à quel moment les éléments mis en cache expirent. Il existe trois types de stratégies d'expiration : **Absolute**, **Sliding** et **Jamais**.

Lorsque **Absolute** est spécifié, l'intervalle d'expiration spécifié par **Durée (min)** commence au moment où un élément est ajouté au cache. Lorsque l'intervalle spécifié par **Durée (min)** est écoulé, l'élément expire.

Lorsque **Sliding** est spécifié, l'intervalle d'expiration spécifié par **Durée (min)** est réinitialisé chaque fois qu'un élément fait l'objet d'un accès dans le cache. L'élément n'expire pas tant que l'intervalle spécifié par **Durée (min)**, calculé à partir du dernier accès, n'est pas écoulé.

Lorsque **Jamais** est spécifié, **Durée (min)** doit être défini sur **0** et les éléments n'expirent pas.

**Absolute** est la stratégie d'expiration par défaut, et 10 minutes la valeur par défaut pour **Durée (min)**. La stratégie d'expiration est fixée pour chaque élément dans un cache nommé, mais le paramètre **Durée (min)** peut être personnalisé pour chaque élément avec les surcharges des méthodes **Add** et **Put**, qui prennent un paramètre Délai d'expiration.

Pour plus d'informations sur les stratégies d'éviction et d'expiration, consultez la page [Expiration et éviction](http://go.microsoft.com/fwlink/?LinkId=317278).

Notifications
-------------

Les notifications du cache permettent à vos applications de recevoir des notifications asynchrones lorsque diverses opérations de cache ont lieu sur le cluster de cache. Elles fournissent également l'invalidation automatique des objets mis en cache localement. Pour plus d'informations, consultez la page [Notifications](http://go.microsoft.com/fwlink/?LinkId=317276).

> Les notifications sont disponibles dans les offres de cache Standard et Premium, mais pas dans l'offre Basic. Pour plus d'informations, consultez la page [Offres de cache](http://go.microsoft.com/fwlink/?LinkId=317277).

Haute disponibilité
-------------------

Lorsque la haute disponibilité est activée, une copie de sauvegarde de chaque élément ajouté au cache est créée. Si une défaillance inattendue se produit sur la copie principale de l'élément, la copie de sauvegarde reste disponible.

Par définition, l'utilisation de la haute disponibilité multiplie par deux la quantité de mémoire requise pour chaque élément en cache. Tenez compte de cet impact sur la mémoire pendant les tâches de planification de la capacité. Pour plus d'informations, consultez la page [Haute disponibilité](http://go.microsoft.com/fwlink/?LinkId=317329).

> La haute disponibilité est disponible dans l'offre de cache Premium, mais pas dans les offres Basic ou Standard. Pour plus d'informations, consultez la page [Offres de cache](http://go.microsoft.com/fwlink/?LinkId=317277).

Éviction
--------

Pour maintenir la capacité de mémoire disponible à l'intérieur d'un cache, la dernière éviction récemment utilisée (LRU) est prise en charge. Lorsque la consommation de mémoire dépasse le seuil de mémoire, les objets sont expulsés de la mémoire, qu'ils aient expiré ou pas, jusqu'à ce que la pression sur la mémoire se relâche. L'éviction est activée par défaut. Si elle est désactivée, les éléments ne sont pas expulsés du cache lorsque la capacité est atteinte, et les opérations Put et Add échouent.

Pour plus d'informations sur les stratégies d'éviction et d'expiration, consultez la page [Expiration et éviction](http://go.microsoft.com/fwlink/?LinkId=317278).

Une fois le cache configuré, vous pouvez configurer les clients du cache pour autoriser l'accès au cache.

## Configuration des clients du cache

Un cache créé avec Cache Service (Preview) est accessible depuis les applications Azure exécutées sur les sites Web, les rôles Web et de travail, et les machines virtuelles Azure. Un package NuGet est fourni pour simplifier la configuration des applications clientes du cache.

Pour configurer une application cliente avec le package Cache NuGet, cliquez avec le bouton droit sur le projet dans l'**Explorateur de solutions** et choisissez **Manage NuGet Packages**.

![NuGetPackageMenu](./media/cache-dotnet-how-to-use-service/CacheServiceManageNuGetPackagesMenu.png)

Sélectionnez **Azure Caching**, cliquez sur **Installer**, puis cliquez sur J'accepte.

> Si **Azure Caching** n'apparaît pas dans la liste, tapez **WindowsAzure.Caching** dans la zone de texte **Rechercher en ligne** et sélectionnez-le dans les résultats.

![NuGetPackage](./media/cache-dotnet-how-to-use-service/CacheServiceManageNuGetPackage.png)

Le package NuGet effectue plusieurs opérations : il ajoute la configuration requise au fichier de configuration de l'application et il ajoute les références d'assembly requises. Pour les projets Cloud Services, il ajoute également le niveau de diagnostic des clients du cache comme paramètre au fichier ServiceConfiguration.cscfg du service cloud.

> Pour les projets Web ASP.NET, le package Cache NuGet ajoute aussi deux sections placées en commentaire au fichier web.config. La première section permet de stocker l'état de session dans le cache et la seconde section permet de mettre en cache la sortie de pages ASP.NET. Pour plus d'informations, consultez les pages [Stockage de l'état de session ASP.NET dans le cache](#store-session) et [Stockage de la mise en cache de sortie de pages ASP.NET dans le cache](#store-page)

Le package NuGet ajoute les éléments de configuration suivants dans le fichier web.config ou app.config de votre application. Une section **dataCacheClients** et une section **cacheDiagnostics** sont ajoutées sous l'élément **configSections**. Si aucun élément **configSections** n'est présent, un élément configSections enfant de l'élément **configuration** est créé.

    <configSections>
      <!-- Sections existantes omises pour plus de clarté. -->
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
        <!--Pour utiliser la version In-Role d'Azure Caching, 
            définissez l'identificateur sur le nom du rôle du cluster de cache -->
        <!--Pour utiliser Azure Caching Service,
            définissez l'identificateur sur le point de terminaison du cluster de cache -->
        <autoDiscover isEnabled="true" identifier="[Cache role name or Service Endpoint]" />
        <!--<localCache isEnabled="true" sync="TimeoutBased" objectCount="100000" ttlValue="300" />-->
        <!--Utilisez cette section pour spécifier les paramètres de sécurité pour la connexion à votre cache. 
            Cette section n'est pas obligatoire si votre cache est hébergé sur un rôle qui fait partie
            de votre service cloud. -->
        <!--<securityProperties mode="Message" sslEnabled="false">
          <messageSecurity authorizationInfo="[Authentication Key]" />
        </securityProperties>-->
      </dataCacheClient>
    </dataCacheClients>

Après l'ajout de la configuration, remplacez les deux éléments suivants dans la configuration que vous venez d'ajouter.

1.  Remplacez **[Cache role name ou Service Endpoint]** par le point de terminaison, qui est affiché sur le tableau de bord du portail de gestion.

    ![Point de terminaison](./media/cache-dotnet-how-to-use-service/CacheServiceEndpoint.png)

2.  Supprimez les marques de commentaire de la section securityProperties et remplacez **[Authentication Key]** par la clé d'authentification, que vous pouvez trouver dans le portail de gestion en cliquant sur **Manage Keys** dans le tableau de bord du cache.

    ![AccessKeys](./media/cache-dotnet-how-to-use-service/CacheServiceManageAccessKeys.png)

> Ces paramètres doivent être configurés correctement, sinon les clients ne pourront pas accéder au cache.

Pour les projets Cloud Services, le package NuGet ajoute également un paramètre **ClientDiagnosticLevel** à l'élément **ConfigurationSettings** du rôle client du cache dans le fichier ServiceConfiguration.cscfg. L'exemple suivant est la section **WebRole1** d'un fichier ServiceConfiguration.cscfg avec un **ClientDiagnosticLevel** égal à 1, la valeur par défaut pour ce paramètre.

    <Role name="WebRole1">
      <Instances count="1" />
      <ConfigurationSettings>
        <!-- Sections existantes omises pour plus de clarté. -->
        <Setting name="Microsoft.WindowsAzure.Plugins.Caching.ClientDiagnosticLevel" 
                 value="1" />
      </ConfigurationSettings>
    </Role>

> Le niveau de diagnostic des clients configure le niveau des informations de diagnostic de la mise en cache collectées pour les clients du cache. Pour plus d'informations, consultez la page [Dépannage et diagnostics](http://go.microsoft.com/fwlink/?LinkId=320839).

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

## Utilisation des caches

Cette section décrit l'exécution des tâches courantes avec Cache.

-   [Procédure : Création d'un objet DataCache](#create-cache-object)
-   [Procédure : Ajout et récupération d'un objet dans le cache](#add-object)
-   [Procédure : Spécification de l'expiration d'un objet dans le cache](#specify-expiration)
-   [Procédure : Stockage de l'état de session ASP.NET dans le cache](#store-session)
-   [Procédure : Stockage de la mise en cache de sortie de pages ASP.NET dans le cache](#store-page)

## Création d'un objet DataCache

Pour utiliser un cache par programme, vous avez besoin d'une référence au cache. Ajoutez la ligne suivante au début de chaque fichier à partir duquel vous voulez utiliser Azure Cache :

    using Microsoft.ApplicationServer.Caching;

> Si Visual Studio ne reconnaît pas les types dans l'instruction using, même après l'installation du package Cache NuGet, qui ajoute les références nécessaires, assurez-vous que le profil cible du projet est .NET Framework 4 ou une version ultérieure et veillez à sélectionner l'un des profils qui ne spécifient pas **Client Profile**. Pour obtenir des instructions sur la configuration des clients du cache, consultez la page [Configuration des clients du cache](#NuGet).

Il existe plusieurs méthodes pour créer un objet **DataCache**. La première consiste à créer simplement un **DataCache** en transmettant le nom du cache voulu.

    DataCache cache = new DataCache("default");

Une fois que le **DataCache** est instancié, vous pouvez l'utiliser pour interagir avec le cache, comme décrit dans les sections suivantes.

Pour utiliser la seconde méthode, créez un objet **DataCacheFactory** dans votre application à l'aide du constructeur par défaut. Le client du cache utilise alors les paramètres présents dans le fichier de configuration. Appelez soit la méthode **GetDefaultCache** de la nouvelle instance de **DataCacheFactory**, qui renvoie un objet **DataCache**, soit la méthode **GetCache**, puis transmettez le nom de votre cache. Ces méthodes renvoient un objet **DataCache** qui peut être utilisé pour accéder au cache par programme.

    // Client du cache configuré par les paramètres dans le fichier de configuration de l'application.
    DataCacheFactory cacheFactory = new DataCacheFactory();
    DataCache cache = cacheFactory.GetDefaultCache();
    // Ou DataCache cache = cacheFactory.GetCache("MyCache");
    // Le cache peut maintenant être utilisé pour ajouter et récupérer des éléments.   

## Ajout et récupération d'un objet dans le cache

Pour ajouter un élément au cache, vous pouvez utiliser la méthode **Add** ou **Put**. La méthode **Add** ajoute l'objet spécifié au cache, indexé par la valeur du paramètre de clé.

    // Ajouter la chaîne "value" au cache, indexée par "item"
    cache.Add("item", "value");

Si un objet avec la même clé est déjà présent dans le cache, une **DataCacheException** est levée avec le message suivant :

> ErrorCode:SubStatus: tentative de création d'un objet avec une clé qui existe déjà dans le cache. La mise en cache accepte uniquement des valeurs de clé uniques pour les objets.

Pour récupérer un objet avec une clé particulière, vous pouvez utiliser la méthode **Get**. Si l'objet existe, il est renvoyé ; sinon, la valeur null est renvoyée.

    // Ajouter la chaîne "value" au cache, indexée par "key"
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

    // Ajouter la chaîne "value" au cache, indexée par "item". S'il existe,
    // le remplacer.
    cache.Put("item", "value");

## Spécification de l'expiration d'un objet dans le cache

Par défaut, les éléments du cache expirent 10 minutes après qu'ils y ont été placés. Ce délai peut être configuré dans le paramètre **Durée (min)** sous l'onglet Configurer du portail de gestion.

![NamedCaches](./media/cache-dotnet-how-to-use-service/CacheServiceNamedCaches.jpg)

Il existe trois types de **Stratégie d’expiration** : **Jamais**, **Absolue** et **Sliding**. Ces stratégies configurent la façon dont le paramètre **Durée (min)** est utilisé pour déterminer l'expiration. Le **Type d'expiration** par défaut est **Absolute**, ce qui signifie que le compte à rebours pour l'expiration d'un élément commence lorsque l'élément est placé dans le cache. Une fois que le délai spécifié pour un élément est écoulé, l'élément expire. Si **Sliding** est spécifié, le compte à rebours du délai d'expiration d'un élément est réinitialisé chaque fois que l'élément fait l'objet d'un accès dans le cache et l'élément expire lorsque le délai spécifié, calculé à partir du dernier accès, est écoulé. Si **Jamais** est spécifié, **Durée (min)** doit être défini sur **0** ; les éléments n'expirent pas et restent valides tant qu'ils sont dans le cache.

Si vous souhaitez que l'intervalle du délai d'expiration soit plus long ou plus court que celui configuré dans les propriétés du cache, vous pouvez spécifier une durée particulière lorsqu'un élément est ajouté ou mis à jour dans le cache en utilisant la surcharge des méthodes **Add** et **Put**, qui prennent un paramètre **TimeSpan**. Dans l'exemple suivant, la chaîne **value** est ajoutée au cache, indexée par **item**, avec un délai d'expiration de 30 minutes.

    // Ajouter la chaîne "value" au cache, indexée par "item"
    cache.Add("item", "value", TimeSpan.FromMinutes(30));

Pour afficher l'intervalle restant du délai d'expiration d'un élément dans le cache, vous pouvez utiliser la méthode **GetCacheItem** afin de récupérer un objet **DataCacheItem** contenant des informations sur l'élément dans le cache, y compris l'intervalle restant du délai d'expiration.

    // Obtenir un objet DataCacheItem contenant des informations sur
    // "Item" dans le cache. Si aucun objet n'est indexé par "item", null
    // est renvoyé. 
    DataCacheItem item = cache.GetCacheItem("item");
    TimeSpan timeRemaining = item.Timeout;

## Stockage de l'état de session ASP.NET dans le cache

Le fournisseur d'état de session d'Azure Cache est un mécanisme de stockage hors processus pour les applications ASP.NET. Il vous permet de stocker l'état de votre session dans un cache Azure plutôt qu'en mémoire ou dans une base de données SQL Server. Pour utiliser le fournisseur d'état de session de mise en cache, commencez par configurer votre cache, puis configurez votre application ASP.NET pour Cache à l'aide du package Cache NuGet, comme décrit dans la page [Prise en main de Cache Service (Preview)](#getting-started-cache-service). Lorsque le package Cache NuGet est installé, il ajoute dans le fichier web.config une section placée en commentaire contenant la configuration requise pour que votre application ASP.NET utilise le fournisseur d'état de session d'Azure Cache.

    <!--Supprimer les marques de commentaire de cette section pour utiliser Azure Caching pour la mise en cache de l'état de session
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

> Si votre fichier web.config ne contient pas cette section placée en commentaire après l'installation du package Cache NuGet, assurez-vous que le dernier Gestionnaire de package NuGet a été installé à partir de la page [Installation du Gestionnaire de package NuGet](http://go.microsoft.com/fwlink/?LinkId=240311), puis désinstallez et réinstallez le package.

Pour activer le fournisseur d'état de session d'Azure Cache, supprimez les marques de commentaire de la section spécifiée. Le cache par défaut est spécifié dans l'extrait fourni. Pour utiliser un autre cache, spécifiez le cache voulu dans l'attribut **cacheName**.

Pour plus d'informations sur l'utilisation du fournisseur d'état de session du service Cache, consultez la page [Fournisseur d'état de session d'Azure Cache](http://go.microsoft.com/fwlink/?LinkId=320835).

## Stockage de la mise en cache de sortie de pages ASP.NET dans le cache

Le fournisseur de caches de sortie d'Azure Cache est un mécanisme de stockage hors processus pour les données de cache de sortie. Ces données concernent spécialement les réponses HTTP complètes (mise en cache de la sortie de pages). Le fournisseur se rattache au nouveau point d'extensibilité du fournisseur de caches de sortie qui a été introduit dans ASP.NET 4. Pour utiliser le fournisseur de caches de sortie, commencez par configurer votre cluster de cache, puis configurez votre application ASP.NET pour la mise en cache à l'aide du package Cache NuGet, comme décrit dans la page [Prise en main de Cache Service (Preview)](#getting-started-cache-service). Lorsque le package Cache NuGet est installé, il ajoute dans le fichier web.config la section commentée suivante, qui contient la configuration requise pour que votre application ASP.NET utilise le fournisseur de caches de sortie d'Azure Caching.

    <!--Supprimer les marques de commentaire de cette section pour utiliser Azure Caching pour la mise en cache de la sortie
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

> Si votre fichier web.config ne contient pas cette section placée en commentaire après l'installation du package Cache NuGet, assurez-vous que le dernier Gestionnaire de package NuGet a été installé à partir de la page [Installation du Gestionnaire de package NuGet](http://go.microsoft.com/fwlink/?LinkId=240311), puis désinstallez et réinstallez le package.

Pour activer le fournisseur de caches de sortie d'Azure Cache, supprimez les marques de commentaire de la section spécifiée. Le cache par défaut est spécifié dans l'extrait fourni. Pour utiliser un autre cache, spécifiez le cache voulu dans l'attribut **cacheName**.

Ajoutez une directive **OutputCache** à chaque page pour laquelle vous voulez mettre en cache la sortie.

    <%@ OutputCache Duration="60" VaryByParam="*" %>

Dans cet exemple, les données de page mises en cache resteront dans le cache pendant 60 secondes et une version différente de la page sera mise en cache pour chaque combinaison de paramètres. Pour plus d'informations sur les options disponibles, consultez la page [Directive OutputCache](http://go.microsoft.com/fwlink/?LinkId=251979).

Pour plus d'informations sur l'utilisation du fournisseur de caches de sortie d'Azure Cache, consultez la page [Fournisseur de caches de sortie d'Azure Cache](http://go.microsoft.com/fwlink/?LinkId=320837).

## Étapes suivantes

Maintenant que vous avez appris les bases de Cache Service (Preview), suivez ces liens pour apprendre à exécuter les tâches de mise en cache plus complexes.

-   Consultez la référence MSDN suivante : [Cache Service (Preview)](http://go.microsoft.com/fwlink/?LinkId=320830)
-   Apprenez comment migrer vers Cache Service (Preview) : [Migration vers Cache Service (Preview)](http://go.microsoft.com/fwlink/?LinkId=317347)
-   Consultez les exemples : [Exemples de Cache Service (Preview)](http://go.microsoft.com/fwlink/?LinkId=320840)

