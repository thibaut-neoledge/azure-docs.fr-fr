<properties 
	pageTitle="Configuration d’alertes dans Application Insights" 
	description="Obtenez des courriers électroniques sur les pannes, les exceptions et les modifications des métriques." 
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
	ms.date="09/14/2015" 
	ms.author="awills"/>
 
# Configuration d’alertes dans Application Insights

[Visual Studio Application Insights][start] peut vous signaler les modifications des métriques de performances ou d’utilisation de votre application.

Application Insights analyse votre application en direct sur un [large éventail de plateformes][platforms] pour vous aider à diagnostiquer les problèmes de performances et comprendre les schémas d’utilisation.

Il existe deux types d’alertes :
 
* Les **tests Web** indiquent quand votre site est indisponible sur Internet ou répond lentement. [En savoir plus][availability].
* Les **alertes de métrique** indiquent quand une métrique dépasse une valeur seuil pendant une certaine période, comme le nombre d’échecs, la mémoire ou les affichages de page. 

Il existe une [page distincte sur les tests Web][availability], nous allons donc nous concentrer ici sur les alertes de métrique.

## Alertes de métrique

Si vous n’avez pas configuré Application Insights pour votre application, [commencez par le faire][start].

Pour obtenir un message électronique lorsqu’une métrique dépasse un seuil, démarrez dans Metrics Explorer ou à partir de la vignette des Règles d’alerte sur le panneau Vue d’ensemble.

![Dans le panneau Règles d’alerte, cliquez sur Ajouter une alerte. Définissez votre application comme ressource à mesurer, fournissez un nom pour l’alerte et sélectionnez une métrique.](./media/app-insights-alerts/01-set-metric.png)

Définissez la ressource avant les autres propriétés. **Choisissez la ressource « (composants) »**si vous souhaitez définir des alertes sur les mesures de performances ou d'utilisation.

Veillez à noter les unités dans lesquelles vous êtes invité à entrer la valeur seuil.

Le nom que vous donnez à l’alerte doit être unique dans le groupe de ressources (pas uniquement dans votre application).

*Je ne vois pas le bouton Ajouter une alerte.* Utilisez-vous un compte professionnel ? Vous pouvez définir des alertes si vous avez un accès propriétaire ou collaborateur à cette ressource d’application. Examinez Paramètres -> Utilisateurs. [En savoir plus sur le contrôle d’accès][roles].

## Consultez vos alertes

Vous recevez un e-mail lorsqu’une alerte bascule entre les états inactive et active.

L’état actuel de chaque alerte est indiqué dans le panneau Règles d’alerte.

L’historique des modifications d’état figure dans le journal des événements d’opérations :

![Sur le panneau Vue d’ensemble, près du bas de la page, cliquez sur « Événements de la semaine dernière »](./media/app-insights-alerts/09-alerts.png)

*Ces « événements » sont-ils liés à des événements de télémétrie ou à des événements personnalisés ?*

* Non. Ces événements opérationnels sont simplement un journal des opérations qui ont eu lieu dans cette ressource d’application. 

## Alertes de disponibilité

Vous pouvez définir des tests Web qui testent n’importe quel site web à partir de points du monde entier. [En savoir plus][availability].

## Quelles sont les alertes à définir ?

Cela dépend de votre application. Pour commencer, il est préférable de ne pas définir un trop grand nombre de métriques. Prenez le temps d’examiner vos graphiques de métriques pendant l’exécution de votre application, pour mieux comprendre comment elle se comporte normalement. Cela vous aidera à trouver des moyens d’améliorer ses performances. Définissez ensuite des alertes qui vous indiquent lorsque les métriques quittent la zone normale.

Les alertes les plus appréciées sont les suivantes :

* Les [tests Web][availability] sont importants si votre application est un site Web ou un service Web qui est visible sur l’Internet public. Ils vous indiquent si votre site tombe en panne ou répond lentement : même si le problème est plutôt dû au transporteur qu’à votre application. Ce sont des tests synthétiques, qui ne mesurent pas l’expérience réelle de vos utilisateurs.
* Les [mesures de navigateur][client], surtout les temps de chargement des pages de navigateur, sont efficaces pour les applications Web. Si votre page comporte un grand nombre de scripts, il peut être intéressant de rechercher des exceptions du navigateur. Pour obtenir ces métriques et alertes, vous devez configurer la [surveillance de page Web][client].
* Temps de réponse de serveur et échec des demandes pour le côté serveur des applications Web. Outre la définition des alertes, gardez un œil sur ces métriques pour voir si elles varient énormément en cas de taux de demandes élevés : cela peut indiquer que votre application manque de ressources.

## Définition d’alertes à l’aide de PowerShell

Dans la plupart des cas, il suffit de définir des alertes manuellement. En revanche, si vous souhaitez créer des alertes métriques automatiquement, vous pouvez le faire à l’aide de PowerShell.

#### Installation unique

Si vous n’avez pas utilisé précédemment PowerShell avec votre abonnement Azure :

1. Installez le module Azure Powershell sur l’ordinateur sur lequel vous souhaitez exécuter les scripts. 
 * Installez le programme [Microsoft Web Platform Installer (v5 ou version ultérieure)](http://www.microsoft.com/web/downloads/platform.aspx).
 * Utilisez-le pour installer Microsoft Azure PowerShell.
2. Démarrez Azure PowerShell et [connectez-vous à votre abonnement](powershell-install-configure.md) :

    ```
    Add-AzureAccount
    ```

#### Obtention d’alertes

    Get-AlertRule -ResourceGroup "Fabrikam" [-Name "My rule"] [-DetailedOutput]

#### Ajout d’alerte


    Add-AlertRule  -Name "{ALERT NAME}" -Description "{TEXT}" `
     -ResourceGroup "{GROUP NAME}" `
     -ResourceId "/subscriptions/{SUBSCRIPTION ID}/resourcegroups/{GROUP NAME}/providers/microsoft.insights/components/{APP RESOURCE NAME}" `
     -MetricName "{METRIC NAME}" `
     -Operator GreaterThan  `
     -Threshold {NUMBER}   `
     -WindowSize {HH:MM:SS}  `
     [-SendEmailToServiceOwners] `
     [-CustomEmails "EMAIL1@X.COM","EMAIL2@Y.COM" ] `
     -Location "East US"
     -RuleType Metric



#### Exemple 1

M’envoyer un message électronique si la réponse du serveur aux demandes HTTP, en moyenne calculée sur 5 minutes, est inférieure à 1 seconde. Ma ressource Application Insights est appelée IceCreamWebApp et se trouve dans le groupe de ressources Fabrikam. Je suis le propriétaire de l’abonnement Azure.

Le GUID est l’ID d’abonnement (et non la clé d’instrumentation de l’application).

    Add-AlertRule -Name "slow responses" `
     -Description "email me if the server responds slowly" `
     -ResourceGroup "Fabrikam" `
     -ResourceId "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/Fabrikam/providers/microsoft.insights/components/IceCreamWebApp" `
     -MetricName "request.duration" `
     -Operator GreaterThan `
     -Threshold 1 `
     -WindowSize 00:05:00 `
     -SendEmailToServiceOwners `
     -Location "East US" -RuleType Metric

#### Exemple 2

J’ai une application dans laquelle j’utilise [TrackMetric()](app-insights-api-custom-events-metrics.md#track-metric) pour signaler une métrique nommée « salesPerHour ». Envoyer un message électronique à mes collègues si la métrique « salesPerHour » est inférieure à 100, en moyenne calculée sur 24 heures.

    Add-AlertRule -Name "poor sales" `
     -Description "slow sales alert" `
     -ResourceGroup "Fabrikam" `
     -ResourceId "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/Fabrikam/providers/microsoft.insights/components/IceCreamWebApp" `
     -MetricName "salesPerHour" `
     -Operator LessThan `
     -Threshold 100 `
     -WindowSize 24:00:00 `
     -CustomEmails "satish@fabrikam.com","lei@fabrikam.com" `
     -Location "East US" -RuleType Metric

La même règle peut être utilisée pour la métrique signalée à l’aide du [paramètre de mesure](app-insights-api-custom-events-metrics.md#properties) d’un autre appel de suivi tel que TrackEvent ou trackPageView.

#### Noms de métrique

Nom de métrique | Nom d’écran | Description
---|---|---
`basicExceptionBrowser.count`|Exceptions du navigateur|Nombre d’exceptions non interceptées levées dans le navigateur.
`basicExceptionServer.count`|Exceptions de serveur|Nombre d’exceptions non gérées levées par l’application
`clientPerformance.clientProcess.value`|Temps de traitement du client|Temps écoulé entre la réception du dernier octet d’un document et le chargement du modèle DOM. Les demandes asynchrones peuvent encore être en cours de traitement.
`clientPerformance.networkConnection.value`|Temps de connexion au réseau pour le chargement de page| Temps pris par le navigateur pour se connecter au réseau. Peut être 0 en cas de mise en cache.
`clientPerformance.receiveRequest.value`|Temps de réception de réponse| Temps écoulé entre l’envoi d’une demande par le navigateur et le début de la réception d’une réponse.
`clientPerformance.sendRequest.value`|Temps d’envoi de demande| Temps pris par le navigateur pour envoyer la demande.
`clientPerformance.total.value`|Temps de chargement de la page de navigateur|Temps s’écoulant entre la demande de l’utilisateur et le chargement du DOM, des feuilles de style, des scripts et des images.
`performanceCounter.available_bytes.value`|Mémoire disponible|Mémoire physique immédiatement disponible pour un processus ou pour une utilisation du système.
`performanceCounter.io_data_bytes_per_sec.value`|Taux d’E/S du processus|Nombre total d’octets par seconde lus et écrits sur des fichiers, un réseau et des appareils.
`performanceCounter.number_of_exceps_thrown_per_sec`|Taux d’exceptions|Exceptions levées par seconde.
`performanceCounter.percentage_processor_time.value`|Processeur de processus|Temps écoulé en pourcentage pour l’ensemble des threads de traitement utilisés par le processeur pour exécuter le processus des applications.
`performanceCounter.percentage_processor_total.value`|Temps processeur|Temps en pourcentage que le processus a passé dans des threads actifs.
`performanceCounter.process_private_bytes.value`|Octets privés du processus|Mémoire allouée exclusivement aux processus de l’application analysée.
`performanceCounter.request_execution_time.value`|Durée d’exécution de la demande ASP.NET|Durée d’exécution de la demande la plus récente.
`performanceCounter.requests_in_application_queue.value`|Demandes ASP.NET en file d’attente d’exécution|Longueur de la file d'attente des demandes d'application.
`performanceCounter.requests_per_sec`|Taux de demandes ASP.NET|Taux par seconde de l'ensemble des demandes à l'application provenant d'ASP.NET.
`remoteDependencyFailed.durationMetric.count`|Défaillances de dépendance|Nombre d'appels de l'application serveur aux ressources externes ayant échoué.
`request.duration`|Temps de réponse du serveur|Temps écoulé entre la réception d'une requête HTTP et la fin de l'envoi de la réponse.
`request.rate`|Taux de demandes|Taux par seconde de l'ensemble des demandes à l'application.
`requestFailed.count`|Demandes ayant échoué|Nombre de requêtes HTTP qui ont abouti au code de réponse > = 400 
`view.count`|Affichages de page|Nombre de demandes d’utilisateur client pour une page web. Le trafic synthétique est filtré.
{nom de votre mesure personnalisée}|{nom de votre mesure}|Votre valeur métrique signalée par [TrackMetric](app-insights-api-custom-events-metrics.md#track-metric) ou dans le [paramètre de mesures d’un appel de suivi](app-insights-api-custom-events-metrics.md#properties).

Les mesures sont envoyées par différents modules de télémétrie :

Groupe de mesures | Module du collecteur
---|---
basicExceptionBrowser,<br/>clientPerformance,<br/>view | [JavaScript du navigateur](app-insights-javascript.md)
performanceCounter | [Performances](app-insights-configuration-with-applicationinsights-config.md#nuget-package-3)
remoteDependencyFailed| [Dépendance](app-insights-configuration-with-applicationinsights-config.md#nuget-package-1)
request,<br/>requestFailed|[Demande serveur](app-insights-configuration-with-applicationinsights-config.md#nuget-package-2)


<!--Link references-->

[availability]: app-insights-monitor-web-app-availability.md
[client]: app-insights-javascript.md
[platforms]: app-insights-platforms.md
[roles]: app-insights-resources-roles-access-control.md
[start]: app-insights-get-started.md

 

<!---HONumber=Sept15_HO4-->