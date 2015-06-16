<properties
   pageTitle="Application Insights pour Azure Cloud Services"
   description="Surveillance efficace de vos rôles Web et de travail avec Application Insights"
   services="application-insights"
   documentationCenter=""
   authors="soubhagyadash"
   manager="victormu"
   editor="alancameronwills"/>

<tags
   ms.service="application-insights"
   ms.devlang="na"
   ms.tgt_pltfrm="ibiza"
   ms.topic="article"
   ms.workload="tbd"
   ms.date="05/21/2015"
   ms.author="sdash"/>

# Application Insights pour Azure Cloud Services


*Application Insights est à l'état de version préliminaire*

La disponibilité, les performances, les échecs et l’utilisation des [applications de services de cloud computing Microsoft Azure](http://azure.microsoft.com/services/cloud-services/) peuvent être surveillées par [Visual Studio Application Insights][start].

<!-- For illustration purposes, we have added Application Insights to this [sample Azure cloud service](sample link). This code is available [here](git link), for you to follow along with the steps below. -->

## Création d’une ressource Application Insights pour chaque rôle

Une ressource Application Insights est l’endroit où vos données de télémétrie sont analysées et affichées.

1.  Dans le [portail Azure][portal], créez une ressource Application Insights. Choisissez ASP.NET comme type d’application. 

    ![Cliquez sur Nouveau > Application Insights](./media/app-insights-cloudservices/01-new.png)

2.  Copiez la clé d'instrumentation. Vous en aurez besoin dans peu de temps pour configurer le Kit de développement logiciel (SDK).

    ![Cliquez sur Propriétés, sélectionnez la clé et appuyez sur ctrl + C](./media/app-insights-cloudservices/02-props.png)


Il est généralement préférable de créer une ressource distincte pour les données à partir de chaque rôle Web et de travail.

Vous pouvez également envoyer des données à partir de tous les rôles à une seule ressource, mais définir une [propriété par défaut][apidefaults] afin de pouvoir filtrer ou regrouper les résultats de chaque rôle.

## <a name="sdk"></a>Installation du Kit de développement logiciel (SDK) dans chaque projet


1. Dans Visual Studio, modifiez les packages NuGet de votre projet d'application de bureau. ![Cliquez avec le bouton droit sur le projet et sélectionnez Gérer les packages NuGet](./media/app-insights-cloudservices/03-nuget.png)

2. Installez le Kit de développement logiciel (SDK) Application Insights pour Web Apps.

    ![Sélectionnez **En ligne**, **Inclure la version préliminaire**, puis recherchez « Application Insights »](./media/app-insights-cloudservices/04-ai-nuget.png)

    Vous pouvez également choisir le Kit de développement logiciel (SDK) Application Insights SDK pour les applications Web. Il fournit des données de télémétrie de compteur de performances intégré.

3. Configurez le Kit de développement logiciel (SDK) pour envoyer des données à la ressource Application Insights.

    Ouvrez `ApplicationInsights.config` et insérez la ligne suivante :

    `<InstrumentationKey>` *la clé d’instrumentation que vous avez copiée* `</InstrumentationKey>`

    Utilisez la clé d’instrumentation que vous avez copiée dans la ressource Application Insights.

    Vous pouvez également [définir la clé lors de l’exécution][apidynamicikey]. Cela vous permet de diriger la télémétrie de différents environnements (développement, test, production) vers différentes ressources.

## Exécutez l'application.

Exécutez votre projet en mode débogage sur votre ordinateur de développement, ou publiez-le sur Azure. Utilisez-le pendant quelques minutes pour générer des données de télémétrie.

## <a name="monitor"></a> Affichage de vos données de télémétrie

Revenez au [portail Azure][portal] et accédez à vos ressources Application Insights.


Recherchez des données dans les graphiques de présentation. Au début, seuls un ou deux points s'affichent. Par exemple :

![Cliquez sur d'autres éléments pour afficher plus de données](./media/app-insights-cloudservices/12-first-perf.png)

Cliquez sur un des graphiques pour afficher des métriques plus détaillées. [En savoir plus sur les métriques.][perf]

À présent, publiez votre application et regardez les données s’accumuler.


#### Pas de données ?

* Ouvrez la vignette [Rechercher][diagnostic] pour afficher les événements individuels.
* Utilisez l'application en ouvrant différentes pages pour générer des données de télémétrie.
* Attendez quelques secondes, puis cliquez sur Actualiser.
* Consultez la rubrique [Résolution des problèmes][qna].


## Terminez l’installation

Pour obtenir une vue à 360 degrés de votre application, vous devez faire certaines choses :


* [Ajoutez le Kit de développement logiciel (SDK) JavaScript à vos pages web][client] pour obtenir des données de télémétrie basées sur votre navigateur, comme le nombre d'affichages de vos pages, les délais de chargement de vos pages, les exceptions de script, mais aussi les données de télémétries personnalisées que vous pouvez rédiger dans les scripts de vos pages.
* Ajoutez un suivi des dépendances pour diagnostiquer les problèmes provoqués par les bases de données ou les autres composants utilisés par votre application :
 * [dans votre application web Azure ou une machine virtuelle][azure]
 * [dans votre serveur IIS local][redfield]
* [Capture du suivi du journal][netlogs] dans votre infrastructure de journalisation favorite
* [Suivez des événements et des métriques personnalisés][api] dans votre client ou votre serveur ou les deux, pour en savoir plus sur la façon dont votre application est utilisée.
* [Configurez les tests web][availability] pour vous assurer que votre application est bien active.

#### Suivi des demandes pour les rôles de travail

Vous pouvez capturer les performances des appels aux rôles de travail en effectuant le suivi de la même façon que les requêtes HTTP. Dans Application Insights, le type de demande de télémétrie mesure une unité de travail côté serveur nommée, pouvant être chronométrée et réussir ou échouer indépendamment. Même si les requêtes HTTP sont automatiquement capturées par le Kit de développement logiciel (SDK), vous pouvez insérer votre propre code pour effectuer le suivi des requêtes aux rôles de travail.

Voici une boucle d’exécution typique pour un rôle de travail :

```C#

    while (true)
    {
      Stopwatch s1 = Stopwatch.StartNew();
      var startTime = DateTimeOffset.UtcNow;
      try
      {
        // ... get and process messages ...

        s1.Stop();
        telemetryClient.TrackRequest("CheckItemsTable",
            startTime, s1.Elapsed, SUCCESS_CODE, true);
      }
      catch (Exception ex)
      {
        string err = ex.Message;
        if (ex.InnerException != null)
        {
           err += " Inner Exception: " + ex.InnerException.Message;
        }
        s1.Stop();
        telemetryClient.TrackRequest("CheckItemsTable", 
            startTime, s1.Elapsed, FAILURE_CODE, false);
        telemetryClient.TrackException(ex);

        // Don't flood if we have a bug in queue process loop.
        System.Threading.Thread.Sleep(60 * 1000);
      }
    }
```




[api]: app-insights-api-custom-events-metrics.md
[apidefaults]: app-insights-api-custom-events-metrics.md#default-properties
[apidynamicikey]: app-insights-api-custom-events-metrics.md#dynamic-ikey
[availability]: app-insights-monitor-web-app-availability.md
[azure]: app-insights-azure.md
[client]: app-insights-javascript.md
[diagnostic]: app-insights-diagnostic-search.md
[netlogs]: app-insights-asp-net-trace-logs.md
[perf]: app-insights-web-monitor-performance.md
[portal]: http://portal.azure.com/
[qna]: app-insights-troubleshoot-faq.md
[redfield]: app-insights-monitor-performance-live-website-now.md
[start]: app-insights-get-started.md

<!---HONumber=58--> 