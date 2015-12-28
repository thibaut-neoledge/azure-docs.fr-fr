<properties 
	pageTitle="Échantillonnage de données de télémétrie dans Application Insights" 
	description="Comment maintenir sous contrôle le volume de télémétrie." 
	services="application-insights" 
    documentationCenter="windows"
	authors="vgorbenko" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/23/2015" 
	ms.author="awills"/>

#  Échantillonnage dans Application Insights

*Application Insights est à l'état de version préliminaire.*


L’échantillonnage est une fonctionnalité d’Application Insights qui vous permet de collecter et de stocker un ensemble réduit de données de télémétrie tout en assurant une analyse statistique correcte des données d’application. Il réduit le trafic et permet d’éviter les [limitations](app-insights-pricing.md#data-rate). Les données sont filtrées de manière à faire apparaître les éléments associés, afin de vous permettre de naviguer entre les éléments lorsque vous effectuez des enquêtes de diagnostics. Lorsque les données de mesure apparaissent sur le portail, elles sont renormalisées pour tenir compte de l’échantillonnage, afin de réduire tout effet sur les statistiques.

L’échantillonnage adaptatif est activé par défaut dans le Kit de développement logiciel (SDK) Application Insights pour ASP.NET, version 2.0.0-beta3 ou version ultérieure. La fonction d’échantillonnage, actuellement disponible en version bêta, est susceptible d’être modifiée.

Il existe deux autres modules d’échantillonnage :

* L’échantillonnage adaptatif ajuste automatiquement le pourcentage d’échantillonnage de façon à obtenir un volume spécifique de demandes. Actuellement uniquement disponible pour la télémétrie ASP.NET côté serveur.  
* Une option d'échantillonnage à débit fixe est également disponible. Vous spécifiez le pourcentage d'échantillonnage. Disponible pour le code d'application web ASP.NET et les pages Web JavaScript. Le client et le serveur synchronisent l’échantillonnage de façon à ce que, dans Search, vous puissiez naviguer entre les demandes et les affichages de pages associés.

## Activation de l’échantillonnage adaptatif

**Mettez à jour les packages NuGet** de votre projet vers la version *préliminaire* d’Application Insights la plus récente : cliquez avec le bouton droit sur le projet dans l’Explorateur de solutions, sélectionnez Gérer les packages NuGet, cochez **Inclure la version préliminaire** et recherchez Microsoft.ApplicationInsights.Web.

Dans [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md), vous pouvez définir un certain nombre de paramètres dans le nœud `AdaptiveSamplingTelemetryProcessor`. Les chiffres indiqués correspondent aux valeurs par défaut :

* `<MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>`

    Taux cible que l’algorithme adaptatif tente d’atteindre **sur un serveur hôte unique**. Si votre application web s’exécute sur plusieurs hôtes, réduisez cette valeur afin de ne pas dépasser votre débit de trafic cible sur le portail Application Insights.

* `<EvaluationInterval>00:00:15</EvaluationInterval>`

    Intervalle auquel le taux actuel de télémétrie est réévalué. L’évaluation est effectuée sous forme de moyenne mobile. Vous souhaiterez peut-être raccourcir cet intervalle si vos données de télémétrie sont soumises à des pics soudains.

* `<SamplingPercentageDecreaseTimeout>00:02:00</SamplingPercentageDecreaseTimeout>`

    En cas de modification du pourcentage d’échantillonnage, délai avant que le pourcentage d’échantillonnage puisse de nouveau être réduit pour capturer moins de données.

* `<SamplingPercentageIncreaseTimeout>00:15:00</SamplingPercentageDecreaseTimeout>`

    En cas de modification du pourcentage d’échantillonnage, délai avant que le pourcentage d’échantillonnage puisse de nouveau être augmenté pour capturer plus de données.

* `<MinSamplingPercentage>0.1</MinSamplingPercentage>`

    Lors des variations du pourcentage d’échantillonnage, valeur minimale autorisée.

* `<MaxSamplingPercentage>100.0</MaxSamplingPercentage>`

    Lors des variations du pourcentage d’échantillonnage, valeur maximale autorisée.

* `<MovingAverageRatio>0.25</MovingAverageRatio>`

    Lors du calcul de la moyenne mobile, poids affecté à la valeur la plus récente. Utilisez une valeur inférieure ou égale à 1. Plus les valeurs sont petites, moins l’algorithme est réactif en cas de modifications brusques.

* `<InitialSamplingPercentage>100</InitialSamplingPercentage>`

    Valeur affectée lorsque l’application vient de démarrer. Ne diminuez pas cette valeur pendant le débogage.

### Solution alternative : configurer l’échantillonnage adaptatif dans le code

Au lieu d’ajuster l’échantillonnage dans le fichier .config, vous pouvez utiliser le code. Cela vous permet de spécifier une fonction de rappel qui est appelée chaque fois que le taux d’échantillonnage est réévalué. Par exemple, vous pouvez utiliser cette fonction pour savoir quel est le taux d’échantillonnage utilisé.

Supprimer le nœud `AdaptiveSamplingTelemetryProcessor` du fichier .config.



*C#*

```C#

    using Microsoft.ApplicationInsights;
    using Microsoft.ApplicationInsights.Extensibility;
    using Microsoft.ApplicationInsights.WindowsServer.Channel.Implementation;
    using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;
    ...

    var adaptiveSamplingSettings = new SamplingPercentageEstimatorSettings();

    // Optional: here you can adjust the settings from their defaults.

    var builder = TelemetryConfiguration.Active.GetTelemetryProcessorChainBuilder();
    
    builder.UseAdaptiveSampling(
         adaptiveSamplingSettings,

        // Callback on rate re-evaluation:
        (double afterSamplingTelemetryItemRatePerSecond,
         double currentSamplingPercentage,
         double newSamplingPercentage,
         bool isSamplingPercentageChanged,
         SamplingPercentageEstimatorSettings s
        ) =>
        {
          if (isSamplingPercentageChanged)
          {
             // Report the sampling rate.
             telemetryClient.TrackMetric("samplingPercentage", newSamplingPercentage);
          }
      });

    // If you have other telemetry processors:
    builder.Use((next) => new AnotherProcessor(next));

    builder.Build();

```

([En savoir plus sur les processeurs de télémétrie](app-insights-api-filtering-sampling.md#filtering).)


<a name="other-web-pages"></a>
## Échantillonnage pour les pages web avec JavaScript

Vous pouvez configurer des pages Web pour l’échantillonnage à débit fixe à partir de n’importe quel serveur.

Lorsque vous [configurez les pages web pour Application Insights](app-insights-javascript.md), modifiez l’extrait de code que vous obtenez à partir du portail Application Insights. (Dans les applications ASP.NET, l’extrait de code passe généralement dans \_Layout.cshtml.) Insérez une ligne de type `samplingPercentage: 10,` avant la clé d’instrumentation :

    <script>
	var appInsights= ... 
	}({ 


    // Value must be 100/N where N is an integer.
    // Valid examples: 50, 25, 20, 10, 5, 1, 0.1, ...
	samplingPercentage: 10, 

	instrumentationKey:...
	}); 
	
	window.appInsights=appInsights; 
	appInsights.trackPageView(); 
	</script> 

Pour le pourcentage d’échantillonnage, choisissez un pourcentage proche de 100/N, où N est un nombre entier. L’échantillonnage ne prend actuellement en charge aucune autre valeur.

Si vous avez activé l’échantillonnage à débit fixe sur le serveur, les clients et le serveur se synchronisent de façon à ce que, dans Search, vous puissiez naviguer entre les demandes et les affichages de pages associés.


## Activation de l’échantillonnage à débit fixe sur le serveur

1. **Mettez à jour les packages NuGet de votre projet** vers la dernière version *préliminaire* d’Application Insights. Cliquez avec le bouton droit sur le projet dans l’Explorateur de solutions, sélectionnez Gérer les packages NuGet, cochez **Inclure la version préliminaire** et recherchez Microsoft.ApplicationInsights.Web. 

2. **Désactivez l’échantillonnage adaptatif** : dans [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md), supprimez ou commentez le nœud `AdaptiveSamplingTelemetryProcessor`.

    ```xml

    <TelemetryProcessors>
    <!-- Disabled adaptive sampling:
      <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.AdaptiveSamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
        <MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>
      </Add>
    -->
    

    ```

2. **Activez le module d’échantillonnage à débit fixe.** Ajoutez cet extrait de code à [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md) :

    ```XML

    <TelemetryProcessors>
     <Add  Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.SamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">

      <!-- Set a percentage close to 100/N where N is an integer. -->
     <!-- E.g. 50 (=100/2), 33.33 (=100/3), 25 (=100/4), 20, 1 (=100/100), 0.1 (=100/1000) -->
      <SamplingPercentage>10</SamplingPercentage>
      </Add>
    </TelemetryProcessors>

    ```

> [AZURE.NOTE]Pour le pourcentage d’échantillonnage, choisissez un pourcentage proche de 100/N, où N est un nombre entier. L’échantillonnage ne prend actuellement en charge aucune autre valeur.



### Solution alternative : activez l’échantillonnage à taux fixe dans le code serveur


Au lieu de définir le paramètre d’échantillonnage dans le fichier .config, vous pouvez utiliser le code.

*C#*

```C#

    using Microsoft.ApplicationInsights.Extensibility;
    using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;
    ...

    var builder = TelemetryConfiguration.Active.GetTelemetryProcessorChainBuilder();
    builder.UseSampling(10.0); // percentage

    // If you have other telemetry processors:
    builder.Use((next) => new AnotherProcessor(next));

    builder.Build();

```

([En savoir plus sur les processeurs de télémétrie](app-insights-api-filtering-sampling.md#filtering).)

## Quand utiliser l’échantillonnage ?

L’échantillonnage adaptatif est automatiquement activé si vous utilisez la version 2.0.0-beta3 du Kit de développement logiciel (SDK) ASP.NET, ou une version ultérieure.

L’échantillonnage n’est pas utile pour la plupart des applications de taille petite à moyenne. Pour obtenir les informations de diagnostic les plus utiles et les statistiques les plus précises, le mieux est de collecter les données sur toutes vos activités utilisateur.

 
Les principaux avantages de l’échantillonnage sont les suivants :

* Si le service Application Insights supprime (« limite ») des points de données lorsque votre application envoie un taux de télémétrie très élevé dans un court laps de temps. 
* Pour respecter le [quota](app-insights-pricing.md) de points de données pour votre niveau tarifaire. 
* Pour réduire le trafic réseau généré par la collecte de données de télémétrie. 

### Échantillonnage fixe ou adaptatif ?

Utilisez l’échantillonnage à débit fixe si :

* Vous voulez disposer d’un échantillonnage synchronisé entre le client et le serveur afin de pouvoir naviguer entre les événements connexes sur le client et le serveur (tels que les affichages de pages et les requêtes http) lorsque vous étudiez des événements dans [Search](app-insights-diagnostic-search.md).
* Vous êtes sûr du pourcentage d’échantillonnage approprié pour votre application. Il doit être suffisamment élevé pour obtenir des mesures précises, mais inférieur au pourcentage engendrant le dépassement de votre quota de tarification et des limites de limitation. 
* Vous ne déboguez pas votre application. Lorsque vous appuyez sur F5 et testez quelques pages de votre application, vous souhaitez voir l’ensemble des données de télémétrie.

Dans le cas contraire, nous vous recommandons d’utiliser l’échantillonnage adaptatif.

## Comment fonctionne l’échantillonnage ?

Du point de vue de l’application, l’échantillonnage est une fonctionnalité du SDK Application Insights. Vous spécifiez le pourcentage de tous les points de données devant être envoyé au service Application Insights. À partir de la version 2.0.0 du SDK Application Insights, vous pouvez contrôler le pourcentage d’échantillonnage à partir de votre code. (Les versions ultérieures du SDK permettront également de configurer le pourcentage d’échantillonnage à partir du fichier ApplicationInsights.config.)

Le SDK détermine les éléments de télémétrie à supprimer ainsi que ceux que vous devez conserver. La décision d’échantillonnage est fondée sur plusieurs règles visant à préserver l’intégrité de tous les points de données reliés entre eux, en conservant dans Application Insights une expérience de diagnostic qui demeure exploitable et fiable, même avec un jeu de données réduit. En cas d’échec d’une requête, par exemple, si votre application envoie d’autres éléments de télémétrie (tels que les exceptions et les traces enregistrées à partir de cette requête), l’échantillonnage ne fractionnera ni cette requête ni les autres éléments de télémétrie. Il les conservera ou supprimera ensemble. C’est pourquoi, lorsque vous examinez les détails de la requête dans Application Insights, la requête s’affichera toujours avec les éléments de télémétrie qui lui sont associés.

Pour les applications qui définissent « l’utilisateur » (autrement dit, les applications Web les plus courantes), la décision d’échantillonnage est basée sur le hachage de l’identifiant utilisateur, ce qui signifie que tous les éléments de télémétrie associés à n’importe quel utilisateur spécifique seront soit conservés soit supprimés. Pour les types d’applications qui ne définissent pas les utilisateurs (tels que les services Web), la décision d’échantillonnage repose sur l’identifiant d’opération de la requête. Enfin, pour les éléments de télémétrie qui ne sont associés ni à un identifiant utilisateur ni à un identifiant d’opération (par exemple, pour des éléments de télémétrie signalés à partir de threads asynchrones sans contexte http), l’échantillonnage capturera simplement un pourcentage d’éléments de télémétrie de chaque type.

Lorsque les données de télémétrie vous sont restituées, le service Application Insights ajuste les mesures en fonction du même pourcentage d’échantillonnage que celui utilisé au moment de la collecte, de manière à compenser les points de données manquants. Par conséquent, lorsqu’ils consultent les données de télémétrie dans Application Insights, les utilisateurs constatent des approximations correctes d’un point de vue statistique et très proches des chiffres réels.

La précision de l’approximation dépend en grande partie du pourcentage d’échantillonnage configuré. Elle est également plus précise pour les applications qui gèrent un grand nombre de requêtes globalement similaires générées par un grand nombre d’utilisateurs. En revanche, pour les applications qui ne sont pas soumises à une charge importante, l’échantillonnage n’est pas nécessaire, car ces applications peuvent généralement envoyer toutes leurs données de télémétrie sans dépasser leur quota ni entraîner de perte de données liée à une limitation de la bande passante.

Notez qu’Application Insights n’échantillonne pas les données de télémétrie de type Mesures et Sessions car il peut être préférable d’être moins précis pour ces types de données.

### Échantillonnage adaptatif

L’échantillonnage adaptatif ajoute un composant qui contrôle la vitesse de transmission actuelle à partir du Kit de développement logiciel (SDK) et ajuste le pourcentage d’échantillonnage afin de le maintenir en dessous du taux maximal cible. L’ajustement est recalculé à intervalles réguliers en fonction d’une moyenne mobile de la vitesse de transmission sortante.

## Échantillonnage et kit de développement logiciel JavaScript

Le SDK (JavaScript) côté client participe à l’échantillonnage parallèlement au SDK côté serveur. Les pages instrumentés enverront uniquement les données de télémétrie côté client provenant des mêmes utilisateurs que le SDK côté serveur a décidé d’échantillonner. Cette logique est conçue pour préserver l’intégrité de la session utilisateur côté client et côté serveur. En partant de n’importe quel élément de télémétrie dans Application Insights, vous retrouverez donc tous les autres éléments de télémétrie associés à l’utilisateur ou à la session en question.

*Mes données de télémétrie côté client et côté serveur n’affichent pas les échantillons coordonnés que vous décrivez ci-dessus.*

* Vérifiez que vous avez activé l’échantillonnage à la fois sur le serveur et sur le client.
* Assurez-vous que vous utilisez bien le kit de développement logiciel version 2.0 ou ultérieure.
* Vérifiez que vous définissez le même pourcentage d’échantillonnage dans le client et dans le serveur.


## Forum Aux Questions (FAQ) 

*Pourquoi l’échantillonnage ne permet-il pas simplement de « collecter X % de chaque type de télémétrie » ?*

 *  Bien que cette approche de l’échantillonnage puisse générer des approximations métriques d’une très haute précision, elle ne permettrait pas de mettre en corrélation les données de diagnostic par utilisateur, par session et par requête, ce qui est essentiel pour l’établissement de diagnostics. L’échantillonnage est donc plus efficace lorsqu’il revient à « collecter tous les éléments de télémétrie pour X % des utilisateurs de l’application » ou à « collecter tous les éléments de télémétrie pour X % des requêtes de l’application ». Pour les éléments de télémétrie qui ne sont pas associés aux requêtes (dans le cas, par exemple, d’un traitement asynchrone en arrière-plan), la solution consiste à « collecter X % de tous les éléments de chaque type de télémétrie ». 

*Le pourcentage d’échantillonnage peut-il évoluer au fil du temps ?*

 * Oui, l’échantillonnage adaptatif modifie progressivement le pourcentage d’échantillonnage en fonction du volume de données de télémétrie constaté.

*Puis-je déterminer le taux de l’échantillonnage adaptatif ?*

 * Oui, utilisez la méthode de code de configuration d’échantillonnage adaptatif pour fournir un rappel qui obtient le taux d’échantillonnage.

*Si j’utilise l’échantillonnage à débit fixe, comment déterminer le pourcentage d’échantillonnage optimal pour mon application ?*

* Pour cela, vous pouvez commencer par l’échantillonnage adaptatif, identifier le taux obtenu (voir la question précédente) et passer à l’échantillonnage à taux fixe en utilisant ce taux. 

    Autrement, vous devez procéder par tâtonnements. Analysez votre utilisation actuelle des données de télémétrie dans AI, observez les limitations qui s’appliquent, puis estimez le volume de données de télémétrie collectées. Ces trois entrées, combinées au niveau tarifaire sélectionné, vous indiquent dans quelle mesure vous devrez réduire le volume de données de télémétrie collectées. Toutefois, une augmentation du nombre d’utilisateurs ou toute autre modification au niveau du volume des données de télémétrie peut invalider votre estimation.

*Que se passe-t-il si je configure le pourcentage d’échantillonnage à un niveau trop faible ?*

* Un pourcentage d’échantillonnage excessivement faible (échantillonnage trop agressif) aura pour effet de réduire la précision des approximations lorsqu’Application Insights tente de compenser la visualisation des données pour tenir compte de la réduction du volume de données. Il peut également affecter l’expérience de diagnostic puisque l’échantillonnage peut inclure certaines requêtes rarement sujettes à des problèmes d’échec ou de ralentissement.

*Que se passe-t-il si je configure un pourcentage d’échantillonnage trop élevé ?*

* Si vous configurez un pourcentage d’échantillonnage trop élevé (c’est-à-dire pas assez agressif), le volume de données de télémétrie collectées ne sera pas suffisamment réduit. Vous risquez de perdre des données de télémétrie sous l’effet de la limitation de bande passante et de supporter des coûts plus élevés que prévu pour l’utilisation d’Application Insights en raison des frais de dépassement.

*Sur quelles plates-formes puis-je utiliser l’échantillonnage ?*

* Actuellement, l’échantillonnage adaptatif est disponible pour les côtés serveur des applications web ASP.NET (hébergées dans Azure ou sur votre propre serveur). L’échantillonnage à débit fixe est disponible pour toutes les pages web, ainsi que pour les applications web .NET côté client et côté serveur.

*Je souhaite que certains événements rares soient toujours affichés. Comment faire en sorte qu’ils soient disponibles hors du module d’échantillonnage ?*

 * Initialisez une instance distincte de TelemetryClient avec une nouvelle instance TelemetryConfiguration (et non la valeur Active par défaut). Utilisez cette instance pour envoyer vos événements rares.

<!---HONumber=AcomDC_1217_2015-->