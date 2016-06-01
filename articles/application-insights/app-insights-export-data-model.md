<properties 
	pageTitle="Modèle de données Application Insights" 
	description="Décrit les propriétés exportées à partir de l’exportation continue dans JSON et utilisées comme filtres." 
	services="application-insights" 
    documentationCenter=""
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/21/2016" 
	ms.author="awills"/>

# Modèle d’exportation de données Application Insights

Cette table répertorie les propriétés de télémétrie envoyées à partir des Kits de développement logiciel (SDK) [Application Insights](app-insights-overview.md) au portail. Vous verrez ces propriétés dans les données issues d’une [exportation continue](app-insights-export-telemetry.md). Elles apparaissent également dans les filtres de propriétés, dans [Metrics Explorer](app-insights-metrics-explorer.md) et dans [Recherche de diagnostic](app-insights-diagnostic-search.md).

Points à noter :

* `[0]` dans ces tables désigne un point dans le chemin d’accès au niveau duquel vous devez insérer un index ; il ne s’agit pas toujours de 0.
* Les durées sont énoncées en dixièmes de microsecondes, donc 10000000 = 1 seconde.
* Les dates et les heures sont indiquées au format UTC, et respectent la norme ISO `yyyy-MM-DDThh:mm:ss.sssZ`

Plusieurs [exemples](app-insights-export-telemetry.md#code-samples) illustrent comment les utiliser.



## Exemple

    // A server report about an HTTP request
    {
    "request": [ 
      {
        "urlData": { // derived from 'url'
          "host": "contoso.org",
          "base": "/",
          "hashTag": "" 
        },
        "responseCode": 200, // Sent to client
        "success": true, // Default == responseCode<400
        // Request id becomes the operation id of child events 
        "id": "fCOhCdCnZ9I=",  
        "name": "GET Home/Index",
        "count": 1, // 100% / sampling rate
        "durationMetric": {
          "value": 1046804.0, // 10000000 == 1 second
          // Currently the following fields are redundant:
          "count": 1.0,
          "min": 1046804.0,
          "max": 1046804.0,
          "stdDev": 0.0,
          "sampledValue": 1046804.0
        },
        "url": "/"
      }
    ],
    "internal": {
      "data": {
        "id": "7f156650-ef4c-11e5-8453-3f984b167d05",
        "documentVersion": "1.61"
      }
    },
    "context": {
      "device": { // client browser
        "type": "PC",
        "screenResolution": { },
        "roleInstance": "WFWEB14B.fabrikam.net"
      },
      "application": { },
      "location": { // derived from client ip
        "continent": "North America",
        "country": "United States",
        // last octagon is anonymized to 0 at portal:
        "clientip": "168.62.177.0", 
        "province": "",
        "city": ""
      },
      "data": {
        "isSynthetic": true, // we identified source as a bot
        // percentage of generated data sent to portal:
        "samplingRate": 100.0, 
        "eventTime": "2016-03-21T10:05:45.7334717Z" // UTC
      },
      "user": {
        "isAuthenticated": false,
        "anonId": "us-tx-sn1-azr", // bot agent id
        "anonAcquisitionDate": "0001-01-01T00:00:00Z",
        "authAcquisitionDate": "0001-01-01T00:00:00Z",
        "accountAcquisitionDate": "0001-01-01T00:00:00Z"
      },
      "operation": {
        "id": "fCOhCdCnZ9I=",
        "parentId": "fCOhCdCnZ9I=",
        "name": "GET Home/Index"
      },
      "cloud": { },
      "serverDevice": { },
      "custom": { // set by custom fields of track calls
        "dimensions": [ ],
        "metrics": [ ]
      },
      "session": {
        "id": "65504c10-44a6-489e-b9dc-94184eb00d86",
        "isFirst": true
      }
    }
  }




## Context

Tous les types de données de télémétrie sont accompagnés d’une section de contexte. Tous ces champs ne sont pas transmis avec tous les points de données.



|Chemin|Type|Remarques|
|---|---|---|
| context.custom.dimensions [0] | objet [ ] | Paires clé-valeur définies par le paramètre des propriétés personnalisées. Longueur maximale de clé 100, longueur maximale de valeur 1024. Plus de 100 valeurs uniques, la propriété peut faire l’objet d’une recherche, mais ne peut pas être utilisée pour la segmentation. 200 clés maximum par ikey. |
| context.custom.metrics [0] | objet [ ] | Paires clé-valeur définies par le paramètre des mesures personnalisées et par TrackMetrics. Longueur maximale de clé 100, les valeurs peuvent être numériques. |
| context.data.eventTime | string | UTC |
| context.data.isSynthetic | booléenne | Requête transmise par un robot ou un test web. |
| context.data.samplingRate | number | Pourcentage de télémétrie générée par le Kit de développement logiciel qui est envoyé vers le portail. Plage 0.0-100.0.|
| context.device | objet | Appareil client |
| context.device.browser | string | IE, Chrome, ... |
| context.device.browserVersion | string | Chrome 48.0, ... |
| context.device.deviceModel | string | |
| context.device.deviceName | string | |
| context.device.id | string | |
| context.device.locale | string | en-GB, de-DE, ... |
| context.device.network | string | |
| context.device.oemName | string | |
| context.device.osVersion | string | Système d’exploitation hôte |
| context.device.roleInstance | string | ID de l’hôte du serveur |
| context.device.roleName | string | |
| context.device.type | string | PC, navigateur... |
| context.location | objet | Dérivé de clientip. |
| context.location.city | string | Dérivé de clientip, si connu |
| context.location.clientip | string | Dernier octogone anonyme (0). |
| context.location.continent | string | |
| context.location.country | string | |
| context.location.province | string | État ou province |
| context.operation.id | string | Les éléments qui affichent le même ID d’opération sont représentés en tant qu’éléments associés dans le portail. Généralement l’ID de requête. |
| context.operation.name | string | nom d’URL ou de requête |
| context.operation.parentId | string | Autorise les éléments liés imbriqués. |
| context.session.id | string | ID d’un groupe d’opérations de la même source. Une période de 30 minutes sans opération signale la fin d’une session. |
| context.session.isFirst | booléenne | |
| context.user.accountAcquisitionDate | string | |
| context.user.anonAcquisitionDate | string | |
| context.user.anonId | string | |
| context.user.authAcquisitionDate | string | [Utilisateur authentifié](app-insights-api-custom-events-metrics.md#authenticated-users) |
| context.user.isAuthenticated | booléenne | |
| internal.data.documentVersion | string | |
| internal.data.id | string | |



## Événements

Événements personnalisés générés par [TrackEvent()](app-insights-api-custom-events-metrics.md#track-event).


|Chemin|Type|Remarques|
|---|---|---|
| event [0] count | integer | 100 / (taux d’[échantillonnage](app-insights-sampling.md)). Par exemple 4 =&gt; 25%. |
| event [0] name | string | Nom de l’événement. Longueur maximale 250. |
| event [0] url | string | |
| event [0] urlData.base | string | |
| event [0] urlData.host | string | |

## Exceptions

Signale des [exceptions](app-insights-asp-net-exceptions.md) sur le serveur et dans le navigateur.


|Chemin|Type|Remarques|
|---|---|---|
| basicException [0] assembly | string | |
| basicException [0] count | integer | 100 / (taux d’[échantillonnage](app-insights-sampling.md)). Par exemple 4 =&gt; 25%. |
| basicException [0] exceptionGroup | string | |
| basicException [0] exceptionType | string | |string | |
| basicException [0] failedUserCodeMethod | string | |
| basicException [0] failedUserCodeAssembly | string | |
| basicException [0] handledAt | string | |
| basicException [0] hasFullStack | booléenne | |
| basicException [0] id | string | |
| basicException [0] method | string | |
| basicException [0] message | string | Message d’exception. Longueur maximale 10 000.|
| basicException [0] outerExceptionMessage | string | |
| basicException [0] outerExceptionThrownAtAssembly | string | |
| basicException [0] outerExceptionThrownAtMethod | string | |
| basicException [0] outerExceptionType | string | |
| basicException [0] outerId | string | |
| basicException [0] parsedStack [0] assembly | string | |
| basicException [0] parsedStack [0] fileName | string | |
| basicException [0] parsedStack [0] level | integer | |
| basicException [0] parsedStack [0] line | integer | |
| basicException [0] parsedStack [0] method | string | |
| basicException [0] stack | string | Longueur maximale 10 000|
| basicException [0] typeName | string | |



## Messages de suivi

Envoyé par [TrackTrace](app-insights-api-custom-events-metrics.md#track-trace) et par les [adaptateurs de journalisation](app-insights-asp-net-trace-logs.md).


|Chemin|Type|Remarques|
|---|---|---|
| message [0] loggerName | string ||
| message [0] parameters | string ||
| message [0] raw | string | Le message du fichier journal, longueur maximale 10k. |
| message [0] severityLevel | string | |



## Dépendance distante

Envoyé par TrackDependency. Utilisé pour consigner les performances et l’utilisation des [appels aux dépendances](app-insights-asp-net-dependencies.md) dans le serveur, et des appels AJAX dans le navigateur.

|Chemin|Type|Remarques|
|---|---|---|
| remoteDependency [0] async | booléenne | |
| remoteDependency [0] baseName | string | |
| remoteDependency [0] commandName | string | Par exemple, « home/index » |
| remoteDependency [0] count | integer | 100 / (taux d’[échantillonnage](app-insights-sampling.md)). Par exemple 4 =&gt; 25%. |
| remoteDependency [0] dependencyTypeName | string | HTTP, SQL, ... |
| remoteDependency [0] durationMetric.value | number | Délai de l’appel à la fin de la réponse par la dépendance |
| remoteDependency [0] id | string | |
| remoteDependency [0] name | string | Url. Longueur maximale 250.|
| remoteDependency [0] resultCode | string | à partir de la dépendance HTTP |
| remoteDependency [0] success | booléenne | |
| remoteDependency [0] type | string | HTTP, SQL, ... |
| remoteDependency [0] url | string | Longueur maximale 2 000 |
| remoteDependency [0] urlData.base | string | Longueur maximale 2 000 |
| remoteDependency [0] urlData.hashTag | string | |
| remoteDependency [0] urlData.host | string | Longueur maximale 200 |


## Demandes

Envoyées par [TrackRequest](app-insights-api-custom-events-metrics.md#track-request). Les modules standard les utilisent pour consigner le temps de réponse du serveur, mesuré sur le serveur.


|Chemin|Type|Remarques|
|---|---|---|
| request [0] count | integer | 100 / (taux d’[échantillonnage](app-insights-sampling.md)). Par exemple : 4 =&gt; 25%. |
| request [0] durationMetric.value | number | Délai entre l’arrivée de la requête et la réponse. 1e7 = 1s |
| request [0] id | string | ID d’opération |
| request [0] name | string | GET/POST + base d’URL Longueur maximale 250 |
| request [0] responseCode | integer | Réponse HTTP envoyée au client |
| request [0] success | booléenne | Par défaut == (responseCode &lt; 400) |
| request [0] url | string | Sans hôte |
| request [0] urlData.base | string | |
| request [0] urlData.hashTag | string | |
| request [0] urlData.host | string | |


## Performances d’affichage de la page

Envoyées par le navigateur. Mesure le temps de traitement d’une page, du lancement de la requête par l’utilisateur à l’affichage complet (sans les appels asynchrones AJAX).

Les valeurs de contexte représentent la version de système d’exploitation et de navigateur du client.


|Chemin|Type|Remarques|
|---|---|---|
| clientPerformance [0] clientProcess.value | integer | Délai entre la fin de la réception du code HTML et l’affichage de la page. |
| clientPerformance [0] name | string | |
| clientPerformance [0] networkConnection.value | integer | Temps nécessaire pour l’établissement d’une connexion réseau. |
| clientPerformance [0] receiveRequest.value | integer | Délai entre la fin de l’envoi d’une requête et la réception du code HTML en réponse. |
| clientPerformance [0] sendRequest.value | integer | Délai nécessaire à l’envoi de la requête HTTP. |
| clientPerformance [0] total.value | integer | Délai entre le début d’envoi de la requête et l’affichage de la page. |
| clientPerformance [0] url | string | URL de cette requête. |
| clientPerformance [0] urlData.base | string | |
| clientPerformance [0] urlData.hashTag | string | |
| clientPerformance [0] urlData.host | string | |
| clientPerformance [0] urlData.protocol | string | |

## Affichages de pages

Envoyé par trackPageView() ou [stopTrackPage](app-insights-api-custom-events-metrics.md#page-view)

|Chemin|Type|Remarques|
|---|---|---|
| view [0] count | integer | 100 / (taux d’[échantillonnage](app-insights-sampling.md)). Par exemple 4 =&gt; 25%. |
| view [0] durationMetric.value | integer | Valeur éventuellement définie dans trackPageView() ou par startTrackPage() - stopTrackPage(). Pas identique aux valeurs clientPerformance. |
| view [0] name | string | Titre de la page. Longueur maximale 250 |
| view [0] url | string | |
| view [0] urlData.base | string | |
| view [0] urlData.hashTag | string | |
| view [0] urlData.host | string | |



## Availability

Consigne les [tests web de disponibilité](app-insights-monitor-web-app-availability.md).

|Chemin|Type|Remarques|
|---|---|---|
| availability [0] availabilityMetric.name | string | availability |
| availability [0] availabilityMetric.value | number |1\.0 ou 0.0 |
| availability [0] count | integer | 100 / (taux d’[échantillonnage](app-insights-sampling.md)). Par exemple 4 =&gt; 25%. |
| availability [0] dataSizeMetric.name | string | |
| availability [0] dataSizeMetric.value | integer | |
| availability [0] durationMetric.name | string | |
| availability [0] durationMetric.value | number | Durée du test. 1e7 = 1s |
| availability [0] message | string | Diagnostic de défaillance |
| availability [0] result | string | Réussite/Échec |
| availability [0] runLocation | string | Source géographique de la requête HTTP |
| availability [0] testName | string | |
| availability [0] testRunId | string | |
| availability [0] testTimestamp | string | |




## Mesures

Généré par TrackMetric().

La valeur de la métrique se trouve dans context.custom.metrics[0]

Par exemple :

    {
     "metric": [ ],
     "context": {
     ...
     "custom": {
        "dimensions": [
          { "ProcessId": "4068" }
        ],
        "metrics": [
          {
            "dispatchRate": {
              "value": 0.001295,
              "count": 1.0,
              "min": 0.001295,
              "max": 0.001295,
              "stdDev": 0.0,
              "sampledValue": 0.001295,
              "sum": 0.001295
            }
          }
         } ] }
    }

## À propos des valeurs de mesure

Les valeurs de mesure, dans les rapports de mesure et ailleurs, sont consignées avec une structure d’objet standard. Par exemple :

      "durationMetric": {
        "name": "contoso.org",
        "type": "Aggregation",
        "value": 468.71603053650279,
        "count": 1.0,
        "min": 468.71603053650279,
        "max": 468.71603053650279,
        "stdDev": 0.0,
        "sampledValue": 468.71603053650279
      }

Actuellement (cela peut changer à l’avenir), dans l’ensemble des valeurs consignées des modules standard du Kit de développement logiciel (SDK), `count==1` et uniquement les champs `name` et `value` sont utiles. Le seul cas où la situation serait différente serait une configuration où vous écririez vos propres appels TrackMetric, dans lesquels vous définiriez les autres paramètres.

Les autres champs ont vocation à autoriser l’agrégation des mesures dans le Kit de développement logiciel, afin de réduire le trafic dans le portail. Par exemple, vous pouvez décider d’accumuler un nombre défini de valeurs successives avant d’envoyer chaque rapport de mesure. Vous calculeriez ensuite la valeur minimale, la valeur maximale, l’écart standard et la valeur agrégée (somme ou moyenne) et définir le décompte sur le nombre de valeurs représentées par le rapport.

Dans les tableaux ci-dessus, nous avons volontairement omis les champs rarement utilisés count, min, max, stdDev et sampledValue.

Au lieu de pré-agréger les mesures, vous pouvez utiliser l’[échantillonnage](app-insights-sampling.md) si vous avez besoin de réduire le volume de télémétrie.


### Durées

Sauf mention contraire, les durées sont indiquées en dixièmes de microseconde. Ainsi, 10000000.0 représente 1s.



## Voir aussi

* [Application Insights](app-insights-overview.md) 
* [Exportation continue](app-insights-export-telemetry.md)
* [Exemples de code](app-insights-export-telemetry.md#code-samples)

<!---HONumber=AcomDC_0518_2016-->