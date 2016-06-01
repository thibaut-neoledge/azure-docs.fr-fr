<properties 
	pageTitle="Analyse des performances et de l’utilisation pour les applications de bureau Windows" 
	description="Analysez l’utilisation et les performances de votre application de bureau Windows avec HockeyApp et Application Insights." 
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
	ms.date="04/27/2016" 
	ms.author="awills"/>

# Analyse des performances et de l’utilisation dans les applications de bureau Windows

*Application Insights est à l'état de version préliminaire.*

[Visual Studio Application Insights](app-insights-get-started.md) et [HockeyApp](https://hockeyapp.net) vous permettent d’analyser vos applications déployées en termes d’utilisation et de performances.

> [AZURE.IMPORTANT] Nous vous recommandons [HockeyApp](https://hockeyapp.net) pour distribuer et surveiller des applications de bureau et pour périphérique. Avec HockeyApp, vous pouvez gérer la distribution, le test direct et les commentaires des utilisateurs, de même que surveiller l’utilisation et les rapports d’incident.

> Bien que les données de télémétrie puissent être envoyées à Application Insights à partir d’une application de bureau, ceci est principalement utile à des fins de débogage et d’expérimentation.


## Pour envoyer la télémétrie à Application Insights à partir d’une application Windows

1. Dans le [portail Azure](https://portal.azure.com), créez une ressource Application Insights. Choisissez ASP.NET comme type d’application.
2. Copiez la clé d'instrumentation. Recherchez la clé dans la liste déroulante Essentials de la nouvelle ressource que vous venez de créer. Fermez le plan de l’application ou faites défiler vers la gauche vers le volet Vue d’ensemble de la ressource.
3. Dans Visual Studio, modifiez les packages NuGet de votre projet d’application et ajoutez Microsoft.ApplicationInsights.WindowsServer. (Ou choisissez Microsoft.ApplicationInsights si vous souhaitez simplement l’API nue, sans modules de collecte de télémétrie standard.)
4. Définissez la clé d’instrumentation dans votre code :

    `TelemetryConfiguration.Active.InstrumentationKey = "` *votre clé* `";`

    ou dans ApplicationInsights.config (si vous avez installé l’un des packages de télémétrie standard) :
 
    `<InstrumentationKey>`*votre clé*`</InstrumentationKey>`

    Si vous utilisez ApplicationInsights.config, assurez-vous que ses propriétés dans l’Explorateur de solutions sont définies sur **Build Action = Content, Copy to Output Directory = Copy**.
5. [Utilisez l’API](app-insights-api-custom-events-metrics.md) pour envoyer les données de télémétrie.
6. Exécutez votre application et consultez la télémétrie dans la ressource que vous avez créée dans le portail Azure.

## <a name="telemetry"></a>Exemple de code

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
            tc.Context.User.Id = Environment.UserName;
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

                // Allow time for flushing:
                System.Threading.Thread.Sleep(1000);
            }
            base.OnClosing(e);
        }

```


 

<!---HONumber=AcomDC_0518_2016-->