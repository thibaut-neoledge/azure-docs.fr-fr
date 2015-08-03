<properties 
	pageTitle="API JavaScript du Kit de développement logiciel (SDK) Application Insights" 
	description="Document de référence" 
	services="application-insights" 
    documentationCenter=".net"
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/21/2015" 
	ms.author="awills"/>
 

# API JavaScript du Kit de développement logiciel (SDK) Application Insights

Le Kit de développement logiciel (SDK) JavaScript est chargé dans votre page web lorsque vous configurez le [suivi de page web](app-insights-javascript.md) dans [Application Insights](https://azure.microsoft.com/services/application-insights/).

[Vue d’ensemble de l’API et exemples](app-insights-api-custom-events-metrics.md)

## Classe AppInsights

Façade du Kit de développement logiciel qui envoie la télémétrie à Application Insights.

Dans une page web dans laquelle vous avez [configuré le suivi de page web](app-insights-javascript.md), vous pouvez utiliser l’instance `appInsights`. Par exemple :
    
    appInsights.trackPageView("page1");



### trackPageView

    trackPageView(name?: string, url?: string, properties?:{[string]:string}, measurements?: {[string]:number})

Journalise l’affichage d’une page ou d’un conteneur similaire à l’intention de l’utilisateur.

 | | 
---|---|---
`name` | `? string` | Nom utilisé pour identifier la page dans le portail. Défini par défaut sur le titre du document.
`url` | `? string` | URL relative ou absolue identifiant la page ou un élément similaire. Défini par défaut sur l’emplacement de la fenêtre.
`properties` | `? {[string]:string}` | Données supplémentaires utilisées pour filtrer les pages et les métriques dans le portail. Vide par défaut.
`measurements` | `? {[string]:number}` | Métriques associés à cette page, affichés dans Metrics Explorer sur le portail. Vide par défaut.


### trackEvent

    trackEvent(name: string, properties?: {[string]:string}, measurements?: {[string]:string})

Journalise une action de l’utilisateur ou une autre occurrence.

Dans le portail, vous pouvez sélectionner des événements par leur nom, et [afficher des graphiques qui les comptabilisent ou qui présentent les mesures associées](app-insights-metrics-explorer.md).

Vous pouvez également rechercher et [afficher des événements individuels](app-insights-diagnostic-search.md).

 | | 
---|---|---
 `name` | `string` | Identifie l’événement. Les événements portant le même nom sont comptabilisés et peuvent être représentés sous forme graphique dans [Metrics Explorer](app-insights-metrics-explorer.md).
`properties` | `? {[string]:string}` | Données supplémentaires utilisées pour filtrer les pages et les métriques dans le portail. Vide par défaut.
`measurements` | `? {[string]:number}` | Métriques associés à cette page, affichés dans Metrics Explorer sur le portail. Vide par défaut.


### trackMetric

    trackMetric(name: string, average: number, sampleCount?: number, min?: number, max?: number)


Journalise une valeur numérique non associée à un événement spécifique. Généralement utilisé pour envoyer des rapports réguliers relatifs aux indicateurs de performance.

Dans le portail, vous pouvez sélectionner des métriques par leur nom pour [représenter leurs valeurs sous forme graphique au fil du temps](app-insights-metrics-explorer.md). Vous ne pouvez pas rechercher ni visualiser des appels trackMetric individuels.

Pour envoyer une mesure unique, utilisez uniquement les deux premiers paramètres. Si vous procédez très fréquemment à des mesures, vous pouvez réduire la bande passante de télémétrie en agrégeant plusieurs mesures et en envoyant la moyenne des résultats à intervalles réguliers.

 | | 
---|---|---
`name` | `string` | Chaîne identifiant le métrique. Dans le portail, vous pouvez sélectionner les métriques à afficher par leur nom.
`average` | ` number` | Mesure unique ou moyenne de plusieurs mesures.
`sampleCount` | `? number` | Nombre de mesures représentées par la moyenne. Défini par défaut sur la valeur 1.
`min` | `? number` | Mesure minimale de l’échantillon. Défini par défaut sur la moyenne.
`max` | `? number` | Mesure maximale de l’échantillon. Défini par défaut sur la moyenne.

### trackException

    trackException(exception: Error, handledAt?: string, properties?: Object, measurements?: Object)

Journalise une exception que vous avez interceptée. (Les exceptions interceptées par le navigateur sont également journalisées.)

Dans le portail, vous pouvez [effectuer une recherche sur un type d’exception et visualiser](app-insights-diagnostic-search.md) le type, le message et l’arborescence des appels de procédure d’instances individuelles.

 | | 
---|---|---
`exception` | `Error` | Élément Error d’une clause catch.  
`handledAt` | `? string` | Défini par défaut sur « unhandled ».
`properties` | `? {[string]:string}` | Données supplémentaires utilisées pour filtrer les pages et les métriques dans le portail. Vide par défaut.
`measurements` | `? {[string]:number}` | Métriques associés à cette page, affichés dans Metrics Explorer sur le portail. Vide par défaut.

### trackTrace

    trackTrace(message: string, properties?: Object, measurements?: Object)

Journalise un événement de diagnostic tel que l’entrée dans une méthode ou la sortie d’une méthode.

Dans le portail, vous pouvez effectuer une recherche sur le contenu d’un message et [afficher les événements trackTrace individuels](app-insights-diagnostic-search.md). (Contrairement à `trackEvent`, vous ne pouvez pas définir de filtre sur le contenu du message dans le portail.)

 | | 
---|---|---
`message` | `string` | Données de diagnostic. Peuvent être bien plus longues qu’un nom.

### flush

    flush()

Envoie immédiatement l’ensemble de la télémétrie placée en file d’attente.

Utilisez cet élément lors de la fermeture de la fenêtre.


### config

    config: IConfig

Valeurs contrôlant le mode d’envoi des données de télémétrie.

    interface IConfig {
        instrumentationKey: string;
        endpointUrl: string;
        accountId: string;
        appUserId: string;
        sessionRenewalMs: number; // 30 mins. 
        sessionExpirationMs: number; // 24h. 
        maxPayloadSizeInBytes: number; // 200k
        maxBatchSizeInBytes: number; // 100k
        maxBatchInterval: number; // 15000
        enableDebug: boolean;
        autoCollectErrors: boolean; // true
        disableTelemetry: boolean; // false
        verboseLogging: boolean;
        diagnosticLogInterval: number; // 10 000
    }

Définissez ces valeurs dans [l’extrait de code](app-insights-javascript-api.md) que vous insérez dans vos pages web. Recherchez la ligne suivante et ajoutez-y des éléments :

    })({
      instrumentationKey: "000...000"
    });

### context

    context: TelemetryContext

Informations concernant l’appareil, l’emplacement et l’utilisateur que le Kit de développement logiciel (SDK) tente d’extraire de l’environnement.


## Classe TelemetryContext




        /**
         * Details of the app you're monitoring.
         */
        public application: Context.Application;

        /**
         * The device the app is running on.
         */
        public device: Context.Device;

        /**
         * The user currently signed in.
         */
        public user: Context.User;

        /**
         * The user session. A session represents a series
         * of user actions. A session starts with a user action.
         * It ends when there is no user activity for 
         * sessionRenewalMs, 
         * or if it lasts longer than sessionExpirationMs.
         */
        public session: Context.Session;

        /**
         * The geographical location of the user's device,
         * if available.
         */
        public location: Context.Location;

        /**
         * Represents the user request. Operation id is used
         * to tie together related events in diagnostic search.
         */
        public operation: Context.Operation;

        /**
         * Default measurements to be attached by default to
         * all events.
         */
        public measurements: any;

        /**
         * Default properties to be attached by default to
         * all events. 
         */
        public properties: any;

        /**
         * Send telemetry object to the endpoint.
         * Returns telemetryObject.
         */
        public track(envelope: Telemetry.Common.Envelope) ;


## Code open source

Découvrez le [code du SDK](https://github.com/Microsoft/ApplicationInsights-js) ou contribuez à ce dernier.

<!---HONumber=July15_HO4-->