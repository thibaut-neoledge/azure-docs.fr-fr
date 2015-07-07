<properties 
	pageTitle="Configuration du kit de développement logiciel (SDK) Application Insights à l’aide du fichier ApplicationInsights.config ou .xml" 
	description="Activer ou désactiver les modules de collecte de données et ajouter des compteurs de performances et d’autres paramètres" 
	services="application-insights"
    documentationCenter="" 
	authors="alancameronwills" 
	manager="ronmart"/>
 
<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/04/2015" 
	ms.author="awills"/>

# Configuration du kit de développement logiciel (SDK) Application Insights à l’aide du fichier ApplicationInsights.config ou .xml

Le kit de développement logiciel (SDK) Application Insights se compose d’un certain nombre de modules. Le module de base fournit l’API de base qui envoie les données de télémétrie au portail Application Insights. Les modules supplémentaires collectent les données de votre application et de son contexte. La modification du fichier de configuration permet d’activer ou de désactiver les modules et de définir les paramètres pour certains d’entre eux.

Le fichier de configuration est nommé `ApplicationInsights.config` ou `ApplicationInsights.xml`, selon le type de votre application. Il est automatiquement ajouté à votre projet lors de l’[installation du kit de développement logiciel (SDK)][start]. Il est également ajouté à une application web par [Status Monitor sur un serveur IIS][redfield], ou lorsque vous [sélectionnez l’extension Appplication Insights pour un site web ou une machine virtuelle Azure][azure].

Il n’existe pas de fichier équivalent permettant le contrôle du [kit de développement logiciel (SDK) dans une page web][client].


## Modules de télémétrie

Il existe un nœud dans le fichier de configuration pour chaque module. Pour désactiver un module, supprimez le nœud ou commentez-le.

#### Implementation.Tracing.DiagnosticsTelemetryModule

Signale les erreurs dans le kit de développement logiciel (SDK). Par exemple, si le kit de développement logiciel (SDK) ne peut pas accéder aux compteurs de performances ou si un TelemetryInitializer personnalisé renvoie une exception.

Les données apparaissent dans [Recherche de diagnostic][diagnostic].

#### RuntimeTelemetry.RemoteDependencyModule

Collecte des données sur la réactivité des composants externes utilisés par votre application. Pour permettre à ce module travailler dans un serveur IIS, vous devez [installer Status Monitor][redfield]. Pour l’utiliser dans des applications web ou des machines virtuelles Azure, [sélectionnez l’extension Application Insights][azure].

#### Web.WebApplicationLifecycleModule

Tente de vider toutes les mémoires tampons des données de télémétrie afin qu’elles ne soient pas perdues lors de l’arrêt du processus.

#### Web.RequestTracking.TelemetryModules.WebRequestTrackingTelemetryModule

Comptabilise le nombre de demandes reçues par votre application web et mesure les temps de réponse.

#### Web.RequestTracking.TelemetryModules.WebExceptionTrackingTelemetryModule

Comptabilise le nombre d’exceptions non traitées dans votre application web. Voir [Échecs et exceptions][exceptions].



#### Web.TelemetryModules.DeveloperModeWithDebuggerAttachedTelemetryModule



## Module collecteur de performances

#### PerfCollector.PerformanceCollectorModule

Par défaut, ce module collecte divers compteurs de performances Windows. Vous pouvez voir ces compteurs lorsque vous ouvrez le panneau Filtres dans Metrics Explorer.

Vous pouvez surveiller des compteurs de performances supplémentaires : les compteurs Windows standard et tout autre compteur ajouté.
      
Pour collecter des compteurs de performances supplémentaires, utilisez la syntaxe suivante :
      
      <Counters>
        <Add PerformanceCounter="\MyCategory\MyCounter" />
        <Add PerformanceCounter="\Process(??APP_WIN32_PROC??)\Handle Count" ReportAs="Process handle count" />
        ...
      </Counters>
      
`PerformanceCounter` doit être `\CategoryName(InstanceName)\CounterName` ou `\CategoryName\CounterName`
      
Si vous fournissez un attribut `ReportAs`, il sera utilisé comme nom affiché dans Application Insights.

À des fins de reporting dans Application Insights, les noms de compteurs doivent inclure uniquement : des lettres, des parenthèses, des barres obliques, des tirets, des traits de soulignement, des espaces et des points.

Vous devez utiliser ReportAs si le compteur que vous souhaitez surveiller contient des caractères non valides tels que « # » ou des chiffres.
      
Les espaces réservés suivants sont pris en charge en tant que `InstanceName` :

    ?APP_WIN32_PROC?? - instance name of the application process  for Win32 counters.
    ??APP_W3SVC_PROC?? - instance name of the application IIS worker process for IIS/ASP.NET counters.
    ??APP_CLR_PROC?? - instance name of the application CLR process for .NET counters.

## Paramètres de canal (Java)

Ces paramètres ont un effet sur la manière dont le kit de développement logiciel (SDK) doit stocker et vider les données de télémétrie qu’il collecte.

#### MaxTelemetryBufferCapacity

Nombre d’éléments de télémétrie qui peuvent être stockés dans la mémoire du kit de développement logiciel (SDK). Une fois ce nombre atteint, la mémoire tampon de télémétrie est vidée : les éléments de télémétrie sont envoyés au serveur Application Insights.

-	Min : 1
-	Max : 1000
-	Valeur par défaut : 500

    <ApplicationInsights> ... <Channel> <MaxTelemetryBufferCapacity>100</MaxTelemetryBufferCapacity> </Channel> ... </ApplicationInsights>

#### FlushIntervalInSeconds 

Détermine la fréquence à laquelle les données stockées en mémoire doivent être vidées (envoyées à Application Insights).

-	Min : 1
-	Max : 300
-	Valeur par défaut : 5

    <ApplicationInsights> ... <Channel> <FlushIntervalInSeconds>100</FlushIntervalInSeconds> </Channel> ... </ApplicationInsights>

#### MaxTransmissionStorageCapacityInMB

Détermine la taille maximale en Mo allouée au stockage persistant sur le disque local. Ce stockage est utilisé pour les éléments de télémétrie persistants qui n’ont pas pu être transmis au point de terminaison Application Insights. Une fois la capacité de stockage atteinte, les nouveaux éléments de télémétrie sont ignorés.

-	Min : 1
-	Max : 100
-	Valeur par défaut : 10

    <ApplicationInsights> ... <Channel> <MaxTransmissionStorageCapacityInMB>50</MaxTransmissionStorageCapacityInMB> </Channel> ... </ApplicationInsights>


## Initialiseurs de contexte

Ces composants collectent les données issues de la plateforme. Les données sont collectées dans l’[objet TelemetryContext][api].

#### BuildInfoConfigComponentVersionContextInitializer

#### DeviceContextInitializer

#### MachineNameContextInitializer

#### ComponentContextInitializer

#### Web.AzureRoleEnvironmentContextInitializer

#### Web.DomainNameRoleInstanceContextInitializer

#### Web.BuildInfoConfigComponentVersionContextInitializer

#### Web.DeviceContextInitializer



## Initialiseurs de télémétrie

Ces composants ajoutent des données à chaque événement de télémétrie envoyé à Application Insights.


#### Web.TelemetryInitializers.WebSyntheticTelemetryInitializer

Ce composant identifie les demandes HTTP qui semblent provenir de robots, tels que des moteurs de recherche et des tests web. Il définit TelemetryClient.Context.Operation.SyntheticSource.

#### Web.TelemetryInitializers.WebOperationNameTelemetryInitializer

Ajoute un nom d’opération à chaque élément de télémétrie. Pour les applications web, « opération » signifie une demande HTTP. Définit TelemetryClient.Context.Operation.Name

#### Web.TelemetryInitializers.WebOperationIdTelemetryInitializer

Ce composant active la fonctionnalité « Rechercher des événements dans la même demande » dans [Recherche de diagnostic][diagnostic]. Définit TelemetryClient.Context.Operation.Id

Ajoute un ID d’opération à chaque élément de données envoyé à Application Insights. Pour les applications web, une « opération » est une demande HTTP. Ainsi, par exemple, la demande et tous les événements et suivis personnalisés qui font partie du traitement de la demande comportent tous le même ID d’opération.

#### Web.TelemetryInitializers.WebUserTelemetryInitializer

Ajoute un id d’utilisateur anonyme à chaque élément de télémétrie. Cela vous permet de filtrer uniquement les événements liés aux activités d’un utilisateur dans la recherche de diagnostic. Par exemple, si une exception est signalée, vous pouvez analyser ce que faisait cet utilisateur.

Définit telemetryClient.Context.User

#### Web.TelemetryInitializers.WebSessionTelemetryInitializer

Ajoute un id de session à chaque événement. Définit telemetryClient.Context.Session

## Initialiseurs personnalisés


Si les initialiseurs standard ne conviennent pas à votre application, vous pouvez créer les vôtres.

Les initialiseurs de contexte permettent de définir les valeurs qui seront utilisées pour initialiser chaque client de télémétrie. Par exemple, si vous avez publié plusieurs versions de votre application, vous pouvez vous assurer de la séparation des données en filtrant sur une propriété personnalisée :

    plublic class MyContextInitializer: IContextInitializer
    {
        public void Initialize(TelemetryContext context)
        {
          context.Properties["AppVersion"] = "v2.1";
        }
    }

Utilisez les initialiseurs de télémétrie pour ajouter le traitement à chaque événement. Par exemple, le kit de développement logiciel (SDK) web indique l’échec de toute demande dont le code de réponse est supérieur ou égal à 400. Vous pourriez ignorer ce comportement :

    public class MyTelemetryInitializer : ITelemetryInitializer
    {
        public void Initialize(ITelemetry telemetry)
        {
            var requestTelemetry = telemetry as RequestTelemetry;
            if (requestTelemetry == null) return;
            int code;
            bool parsed = Int32.TryParse(requestTelemetry.ResponseCode, out code);
            if (!parsed) return;
            if (code >= 400 && code < 500)
            {
                requestTelemetry.Success = true;
                requestTelemetry.Context.Properties["Overridden400s"] = "true";
            }            
        }
    }
 
Pour installer vos initialiseurs, ajoutez des lignes dans ApplicationInsights.config :

    <TelemetryInitializers> <!-- or ContextInitializers -->
    <Add Type="MyNamespace.MyTelemetryInitializer, MyAssemblyName" />


Vous pouvez également écrire du code pour installer l’initialiseur au tout début de l’exécution de votre application. Par exemple :


    // In the app initializer such as Global.asax.cs:

    protected void Application_Start()
    {
      TelemetryConfiguration.Active.TelemetryInitializers.Add(
                new MyTelemetryInitializer());
            ...




## InstrumentationKey

Ce paramètre détermine la ressource Application Insights dans laquelle vos données s’affichent. En général, vous créez une ressource séparée, avec une clé distincte, pour chacune de vos applications.

Si vous souhaitez définir la clé de manière dynamique, par exemple si vous souhaitez transmettre des résultats de votre application vers différentes ressources, vous pouvez omettre la clé du fichier de configuration, et la définir plutôt dans le code.

Définissez la clé dans TelemetryConfiguration.Active pour toutes les instances de TelemetryClient, y compris les modules standard de télémétrie. Définissez-la dans une méthode d’initialisation, telle que global.aspx.cs dans un service ASP.NET :

```C#

    protected void Application_Start()
    {
      Microsoft.ApplicationInsights.Extensibility.
        TelemetryConfiguration.Active.InstrumentationKey = 
          // - for example -
          WebConfigurationManager.Settings["ikey"];
      //...
```

Si vous souhaitez simplement envoyer un ensemble spécifique d’événements à une autre ressource, vous pouvez définir la clé pour un TelemetryClient spécifique :

```C#

    var tc = new TelemetryClient();
    tc.Context.InstrumentationKey = "----- my key ----";
    tc.TrackEvent("myEvent");
    // ...

```

[En savoir plus sur l’API][api]

Pour obtenir une nouvelle clé, [créez une nouvelle ressource dans le portail Application Insights][new].

<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[azure]: ../insights-perf-analytics.md
[client]: app-insights-javascript.md
[diagnostic]: app-insights-diagnostic-search.md
[exceptions]: app-insights-web-failures-exceptions.md
[netlogs]: app-insights-asp-net-trace-logs.md
[new]: app-insights-create-new-resource.md
[redfield]: app-insights-monitor-performance-live-website-now.md
[start]: app-insights-get-started.md

 

<!---HONumber=62-->