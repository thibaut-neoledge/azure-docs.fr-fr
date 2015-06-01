<properties 
	pageTitle="Application Insights pour les applications de bureau Windows" 
	description="Analysez l'utilisation et les performances de votre application Windows avec Application Insights." 
	services="application-insights" 
    documentationCenter="windows"
	authors="alancameronwills" 
	manager="keboyd"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/04/2015" 
	ms.author="awills"/>

# Application Insights sur des applications de bureau Windows

*Application Insights est à l'état de version préliminaire.*

[AZURE.INCLUDE [app-insights-selector-get-started](../includes/app-insights-selector-get-started.md)]

Application Insights vous permet d'analyser l’utilisation et les performances de vos applications déployées.

*Bien que le Kit de développement logiciel (SDK) Application Insights peut être adapté à une application de bureau, ce type de scénario n'est pas pris en charge pour le moment. Mais si vous souhaitez l’expérimenter, voici quelques conseils pour y parvenir.*




## <a name="add"></a> Création d’une ressource Application Insights


1.  Dans le [portail Azure][portal], créez une ressource Application Insights. Sélectionnez ASP.NET ou Windows Store comme type d’application. 

    ![Cliquez sur Nouveau > Application Insights](./media/app-insights-windows-get-started/01-new.png)

    Le choix du type d'application définit le contenu du volet Vue d'ensemble et les propriétés disponibles dans [Metrics Explorer][metrics].

2.  Copiez la clé d'instrumentation.

    ![Cliquez sur Propriétés, sélectionnez la clé et appuyez sur ctrl + C](./media/app-insights-windows-get-started/02-props.png)

## <a name="sdk"></a>Installation du Kit de développement logiciel (SDK) dans votre application


1. Dans Visual Studio, modifiez les packages NuGet de votre projet d'application de bureau. ![Cliquez avec le bouton droit sur le projet et sélectionnez Gérer les packages NuGet](./media/app-insights-windows-get-started/03-nuget.png)

2. Installez le principal Kit de développement logiciel (SDK) Application Insights.

    ![Sélectionnez **En ligne**, **Inclure la version préliminaire**, puis recherchez « Application Insights »](./media/app-insights-windows-get-started/04-ai-nuget.png)

    Vous pouvez également choisir le Kit de développement logiciel (SDK) Application Insights SDK pour les applications Web. Il fournit des données de télémétrie de compteur de performances intégré.

3. Modifiez ApplicationInsights.config (qui a été ajouté par l'installation NuGet). Insérez ceci juste avant la balise de fermeture :

    &lt;InstrumentationKey&gt;*la clé que vous avez copiée*&lt;/InstrumentationKey&gt;

    En guise d'alternative, vous pouvez obtenir le même résultat avec ce code :
    
    `TelemetryConfiguration.Active.InstrumentationKey = "your key";`

4. Si vous avez installé le Kit de développement logiciel (SDK) Applications web, vous souhaiterez également commenter les modules de télémétrie web à partir de ApplicationInsights.config

## <a name="telemetry"></a>Insertion d’appels de télémétrie

Créez une instance de `TelemetryClient` puis [utilisez-la pour envoyer des données de télémétrie][track].

Utilisez `TelemetryClient.Flush` pour envoyer des messages avant de fermer l'application. Ceci est déconseillé pour les autres types d'application.

Par exemple, dans une application Windows Forms, vous pouvez écrire :

```C#

    public partial class Form1 : Form
    {
        private TelemetryClient tc = new TelemetryClient();
        ...
        private void Form1_Load(object sender, EventArgs e)
        {
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

Utilisez l’une des [API Application Insights][track] pour envoyer des données de télémétrie. Dans les applications de bureau Windows, aucune donnée de télémétrie n'est envoyée automatiquement. En général, vous allez utiliser :

* TrackPageView(pageName) sur le passage des formulaires, des pages ou des onglets
* TrackEvent(eventName) pour d'autres actions utilisateur
* TrackTrace(logEvent) pour la [journalisation des diagnostics][diagnostic]
* TrackException(exception) dans des clauses Catch
* TrackMetric(name, value) dans une tâche en arrière-plan pour envoyer des rapports de métriques réguliers qui ne sont pas liés à des événements spécifiques.

Pour consulter le nombre d'utilisateurs et de sessions, définissez un initialiseur de contexte :

    class TelemetryInitializer: IContextInitializer
    {
        public void Initialize(TelemetryContext context)
        {
            context.User.Id = Environment.UserName;
            context.Session.Id = DateTime.Now.ToFileTime().ToString();
            context.Session.IsNewSession = true;
        }
    }

    static class Program
    {
        ...
        static void Main()
        {
            TelemetryConfiguration.Active.ContextInitializers.Add(
                new TelemetryInitializer());
            ...



## <a name="run"></a>Exécution de votre projet

[Exécutez votre application en appuyant sur F5](http://msdn.microsoft.com/library/windows/apps/bg161304.aspx) et utilisez-la pour générer des données de télémétrie.

Un décompte des événements envoyés s'affiche dans Visual Studio.

![](./media/appinsights/appinsights-09eventcount.png)



## <a name="monitor"></a>Affichage des données d’analyse

Revenez au volet de votre application dans le portail Azure.

Les premiers événements s’affichent dans Recherche de diagnostic.

Après quelques secondes, cliquez sur Actualiser pour obtenir des données supplémentaires.

Si vous avez utilisé TrackMetric ou le paramètre de mesure de TrackEvent, ouvrez [Metrics Explorer][metrics] et le volet Filtres, où vous pouvez consulter vos métriques.



## <a name="usage"></a>Étapes suivantes

[Suivi de l'utilisation de votre application][track]

[Capture et recherche des journaux de diagnostic][diagnostic]

[Résolution des problèmes][qna]




<!--Link references-->

[diagnostic]: app-insights-diagnostic-search.md
[metrics]: app-insights-metrics-explorer.md
[portal]: http://portal.azure.com/
[qna]: app-insights-troubleshoot-faq.md
[track]: app-insights-custom-events-metrics-api.md


<!--HONumber=54-->