<properties 
	pageTitle="Application Insights pour les services et applications de bureau Windows"
	description="Analysez l’utilisation et les performances de votre application de bureau Windows avec Application Insights."
	services="application-insights"
	documentationCenter="windows"
	authors="alancameronwills"
	manager="douge"/>

<tags 
	ms.service="application-insights"
	ms.workload="tbd"
	ms.tgt_pltfrm="ibiza"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/19/2015"
	ms.author="awills"/>

# Application Insights sur des rôles de travail, des services et des applications de bureau Windows

*Application Insights est à l'état de version préliminaire.*

[AZURE.INCLUDE [app-insights-selector-get-started](../../includes/app-insights-selector-get-started.md)]

Application Insights vous permet d'analyser l’utilisation et les performances de vos applications déployées.

Toutes les applications Windows (y compris les applications de bureau, les services d’arrière-plan et les rôles de travail) peuvent utiliser le Kit de développement logiciel (SDK) principal du logiciel Application Insights pour envoyer des données de télémétrie à Application Insights. La Kit de développement logiciel (SDK) principal fournit simplement une API : contrairement aux Kits de développement logiciel (SDK) web ou pour appareil, il n’inclut pas tous les modules qui collectent les données automatiquement. Vous devez donc écrire du code pour envoyer vos propres données de télémétrie.


## <a name="add"></a> Création d’une ressource Application Insights


1.  Dans le [portail Azure][portal], créez une ressource Application Insights. Choisissez le type d’application Windows Store. 

    ![Cliquez sur Nouveau > Application Insights](./media/app-insights-windows-desktop/01-new.png)

    Le choix du type d'application définit le contenu du volet Vue d'ensemble et les propriétés disponibles dans [Metrics Explorer][metrics].

2.  Copiez la clé d'instrumentation.

    ![Cliquez sur Propriétés, sélectionnez la clé et appuyez sur ctrl + C](./media/app-insights-windows-desktop/02-props.png)

## <a name="sdk"></a>Installation du Kit de développement logiciel (SDK) dans votre application


1. Dans Visual Studio, modifiez les packages NuGet de votre projet d’application de bureau.

    ![Cliquez avec le bouton droit sur le projet et sélectionnez Gérer les packages NuGet](./media/app-insights-windows-desktop/03-nuget.png)

2. Installez le package d’API Core Application Insights.

    ![Recherchez « Application Insights »](./media/app-insights-windows-desktop/04-core-nuget.png)

    Vous pouvez installer d’autres packages tels que le compteur de performances ou des packages de collecte de journaux si vous souhaitez utiliser les fonctionnalités correspondantes.

3. Définissez votre élément InstrumentationKey dans le code, par exemple dans main().

    `TelemetryConfiguration.Active.InstrumentationKey = "your key";`

*Pourquoi n’existe-t-il aucun fichier ApplicationInsights.config ?*

* Le fichier .config n’est pas installé par le package d’API Core, qui permet uniquement de configurer les collecteurs de télémétrie. Vous écrivez donc votre propre code pour définir la clé d’instrumentation et envoyer les données de télémétrie.
* Si vous avez installé l’un des autres packages, vous disposerez d’un fichier .config. Vous pouvez y placer votre clé d’instrumentation au lieu de la définir dans le code.

*Puis-je utiliser un autre package NuGet ?*

* Oui, vous pouvez utiliser le package de serveur web (Microsoft.ApplicationInsights.Web), qui installe des collecteurs pour différents modules de collecte, tels que les compteurs de performances. Ce package installe un fichier .config dans lequel vous devrez placer votre clé d’instrumentation. Utilisez [ApplicationInsights.config pour désactiver les modules que vous ne souhaitez pas utiliser](app-insights-configuration-with-applicationinsights-config.md), par exemple le collecteur de demande HTTP. 
* Si vous souhaitez utiliser les [packages de collecteurs de journaux ou de traces](app-insights-asp-net-trace-logs.md), démarrez avec le package de serveur web. 

## <a name="telemetry"></a>Insertion d’appels de télémétrie

Créez une instance de `TelemetryClient` puis [utilisez-la pour envoyer des données de télémétrie][api].


Par exemple, dans une application Windows Forms, vous pouvez écrire :

```C#

    public partial class Form1 : Form
    {
        private TelemetryClient tc = new TelemetryClient();
        ...
        private void Form1_Load(object sender, EventArgs e)
        {
            // Alternative to setting ikey in config file:
            tc.InstrumentationKey = "key copied from portal";

            // Set session data:
            tc.Context.User.Id = Environment.GetUserName();
            tc.Context.Session.Id = Guid.NewGuid().ToString();
            tc.Context.Device.OperatingSystem = Environment.OSVersion.ToString();

            // Log a page view:
            tc.TrackPageView("Form1");
            ...
        }

        protected override void OnClosing(CancelEventArgs e)
        {
            stop = true;
            if (tc != null)
            {
                tc.Flush(); // only for desktop apps
            }
            base.OnClosing(e);
        }

```

Utilisez l’une des [API Application Insights][api] pour envoyer des données de télémétrie. Dans les applications de bureau Windows, aucune donnée de télémétrie n'est envoyée automatiquement. En général, vous allez utiliser :

* TrackPageView(pageName) sur le passage des formulaires, des pages ou des onglets
* TrackEvent(eventName) pour d'autres actions utilisateur
* TrackMetric(name, value) dans une tâche en arrière-plan pour envoyer des rapports de métriques réguliers qui ne sont pas liés à des événements spécifiques.
* TrackTrace(logEvent) pour la [journalisation des diagnostics][diagnostic]
* TrackException(exception) dans des clauses Catch


Pour vous assurer que toutes les données de télémétrie sont envoyées avant la fermeture de l’application, utilisez `TelemetryClient.Flush()`. Les données de télémétrie sont généralement traitées par lot et envoyées à intervalles réguliers. (Le vidage est recommandé uniquement si vous utilisez simplement l’API de base). Les Kits de développement logiciel (SDK) web et pour appareil implémentent automatiquement ce comportement.)


#### Initialiseurs de contexte

Pour afficher le nombre d'utilisateurs et de sessions, vous pouvez définir les valeurs de chaque instance `TelemetryClient`. Vous pouvez également utiliser un initialiseur de contexte afin d’effectuer cet ajout pour tous les clients :

```C#

    class UserSessionInitializer: IContextInitializer
    {
        public void Initialize(TelemetryContext context)
        {
            context.User.Id = Environment.UserName;
            context.Session.Id = Guid.NewGuid().ToString();
        }
    }

    static class Program
    {
        ...
        static void Main()
        {
            TelemetryConfiguration.Active.ContextInitializers.Add(
                new UserSessionInitializer());
            ...

```



## <a name="run"></a>Exécution de votre projet

[Exécutez votre application en appuyant sur F5](http://msdn.microsoft.com/library/windows/apps/bg161304.aspx) et utilisez-la pour générer des données de télémétrie.

Un décompte des événements envoyés s'affiche dans Visual Studio.

![](./media/app-insights-windows-desktop/appinsights-09eventcount.png)



## <a name="monitor"></a>Affichage des données d’analyse

Revenez au volet de votre application dans le portail Azure.

Les premiers événements s’affichent dans Recherche de diagnostic.

Après quelques secondes, cliquez sur Actualiser pour obtenir des données supplémentaires.

Si vous avez utilisé TrackMetric ou le paramètre de mesure de TrackEvent, ouvrez [Metrics Explorer][metrics] et le volet Filtres, où vous pouvez consulter vos métriques.



## <a name="usage"></a>Étapes suivantes

[Suivi de l'utilisation de votre application][knowUsers]

[Capture et recherche des journaux de diagnostic][diagnostic]

[Résolution des problèmes][qna]




<!--Link references-->

[diagnostic]: app-insights-diagnostic-search.md
[metrics]: app-insights-metrics-explorer.md
[portal]: http://portal.azure.com/
[qna]: app-insights-troubleshoot-faq.md
[knowUsers]: app-insights-overview-usage.md
[api]: app-insights-api-custom-events-metrics.md
[CoreNuGet]: https://www.nuget.org/packages/Microsoft.ApplicationInsights
 

<!---HONumber=August15_HO8-->