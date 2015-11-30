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
	ms.date="10/20/2015" 
	ms.author="awills"/>

#  Échantillonnage dans Application Insights

*Application Insights est à l'état de version préliminaire.*


L’échantillonnage est une option d’Application Insights qui vous permet de collecter et stocker un ensemble réduit de données de télémétrie tout en assurant une analyse statistique correcte des données d’application. Vous utiliserez généralement cette fonctionnalité dans le but de réduire le trafic et d’éviter la [limitation de bande passante](app-insights-pricing.md#data-rate). Les données sont filtrées de telle manière à faire apparaître les éléments associés, afin de vous permettre d’effectuer des enquêtes de diagnostics sur un ensemble réduit de données. Les données côté client et côté serveur sont automatiquement coordonnées pour filtrer les éléments associés. Lorsque les données de mesure apparaissent sur le portail, elles sont renormalisées pour tenir compte de l’échantillonnage, afin de réduire tout effet sur les statistiques.


La fonction d’échantillonnage, actuellement disponible en version bêta, est susceptible d’être modifiée.

## Configuration de l’échantillonnage pour votre application

L’échantillonnage est actuellement disponible pour le kit de développement logiciel (SDK) ASP.NET ou pour [n’importe quelle page Web](#other-web-pages).

### Serveur ASP.NET

1. Mettez à jour les packages NuGet de votre projet vers la dernière version *préliminaire* d’Application Insights. Cliquez avec le bouton droit sur le projet dans l’Explorateur de solutions, sélectionnez Gérer les packages NuGet, cochez **Inclure la version préliminaire** et recherchez Microsoft.ApplicationInsights.Web. 

2. Ajoutez cet extrait de code à [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md) :

```XML

    <TelemetryProcessors>
     <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.SamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">

     <!-- Set a percentage close to 100/N where N is an integer. -->
     <!-- E.g. 50 (=100/2), 33.33 (=100/3), 25 (=100/4), 20, 1 (=100/100), 0.1 (=100/1000) -->
     <SamplingPercentage>10</SamplingPercentage>
     </Add>
   </TelemetryProcessors>

```

> [AZURE.NOTE]Pour le pourcentage d’échantillonnage, choisissez un pourcentage proche de 100/N, où N est un nombre entier. L’échantillonnage ne prend actuellement en charge aucune autre valeur.

<a name="other-web-pages"></a>
### Pages Web avec JavaScript

Vous pouvez configurer des pages Web pour l’échantillonnage à partir de n’importe quel serveur. Pour les serveurs ASP.NET, configurez vos pages Web côté client et côté serveur.

Lorsque vous [configurez les pages web pour Application Insights](app-insights-javascript.md), modifiez l'extrait de code que vous obtenez à partir du portail Application Insights. (dans ASP.NET, cet extrait de code se trouve dans \_Layout.cshtml). Insérez une ligne de type `samplingPercentage: 10,` avant la clé d'instrumentation :

    <script>
	var appInsights= ... 
	}({ 

	samplingPercentage: 10, 

	instrumentationKey:...
	}); 
	
	window.appInsights=appInsights; 
	appInsights.trackPageView(); 
	</script> 

Veillez à fournir le même pourcentage d’échantillonnage dans JavaScript que celui indiqué côté serveur.

[En savoir plus sur l’API](app-insights-api-custom-events-metrics.md)


### Autre solution : définir l’échantillonnage dans le code serveur


Au lieu de définir le paramètre d’échantillonnage dans le fichier .config, vous pouvez utiliser le code. Cette méthode vous permet d’activer et de désactiver l’échantillonnage.

*C#*

```C#

    using Microsoft.ApplicationInsights.Extensibility;
    using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;

    // It's recommended to set SamplingPercentage in the .config file instead.

    // This configures sampling percentage at 10%:
    TelemetryConfiguration.Active.TelemetryChannel = new TelemetryChannelBuilder().UseSampling(10.0).Build();

```


## Quand utiliser l’échantillonnage ?

L’échantillonnage n’est pas utile pour la plupart des applications de taille petite à moyenne. Pour obtenir les informations de diagnostic les plus utiles et les statistiques les plus précises, le mieux est de collecter les données sur toutes vos activités utilisateur.

 
L’échantillonnage est essentiellement utilisé pour les raisons suivantes :


* Si le service Application Insights supprime (« limite ») des points de données lorsque votre application envoie un taux de télémétrie très élevé dans un court laps de temps. 
* Vous souhaitez respecter le [quota](app-insights-pricing.md) de points de données pour votre niveau tarifaire. 
* Pour réduire le trafic réseau généré par la collecte de données de télémétrie. 

## Comment fonctionne l’échantillonnage ?

Du point de vue de l’application, l’échantillonnage est une fonctionnalité du SDK Application Insights. Vous spécifiez le pourcentage de tous les points de données devant être envoyé au service Application Insights. À partir de la version 2.0.0 du SDK Application Insights, vous pouvez contrôler le pourcentage d’échantillonnage à partir de votre code. (Les versions ultérieures du SDK permettront également de configurer le pourcentage d’échantillonnage à partir du fichier ApplicationInsights.config.)

Le SDK détermine les éléments de télémétrie à supprimer ainsi que ceux que vous devez conserver. La décision d’échantillonnage est fondée sur plusieurs règles visant à préserver l’intégrité de tous les points de données reliés entre eux, en conservant dans Application Insights une expérience de diagnostic qui demeure exploitable et fiable, même avec un jeu de données réduit. En cas d’échec d’une requête, par exemple, si votre application envoie d’autres éléments de télémétrie (tels que les exceptions et les traces enregistrées à partir de cette requête), l’échantillonnage ne fractionnera ni cette requête ni les autres éléments de télémétrie. Il les conservera ou supprimera ensemble. C’est pourquoi, lorsque vous examinez les détails de la requête dans Application Insights, la requête s’affichera toujours avec les éléments de télémétrie qui lui sont associés.

Pour les applications qui définissent « l’utilisateur » (autrement dit, les applications Web les plus courantes), la décision d’échantillonnage est basée sur le hachage de l’identifiant utilisateur, ce qui signifie que tous les éléments de télémétrie associés à n’importe quel utilisateur spécifique seront soit conservés soit supprimés. Pour les types d’applications qui ne définissent pas les utilisateurs (tels que les services Web), la décision d’échantillonnage repose sur l’identifiant d’opération de la requête. Enfin, pour les éléments de télémétrie qui ne sont associés ni à un identifiant utilisateur ni à un identifiant d’opération (par exemple, pour des éléments de télémétrie signalés à partir de threads asynchrones sans contexte http), l’échantillonnage capturera simplement un pourcentage d’éléments de télémétrie de chaque type.

Lorsque les données de télémétrie vous sont restituées, le service Application Insights ajuste les mesures en fonction du même pourcentage d’échantillonnage que celui utilisé au moment de la collecte, de manière à compenser les points de données manquants. Par conséquent, lorsqu’ils consultent les données de télémétrie dans Application Insights, les utilisateurs constatent des approximations correctes d’un point de vue statistique et très proches des chiffres réels.

La précision de l’approximation dépend en grande partie du pourcentage d’échantillonnage configuré. Elle est également plus précise pour les applications qui gèrent un grand nombre de requêtes globalement similaires générées par un grand nombre d’utilisateurs. En revanche, pour les applications qui ne sont pas soumises à une charge importante, l’échantillonnage n’est pas nécessaire, car ces applications peuvent généralement envoyer toutes leurs données de télémétrie sans dépasser leur quota ni entraîner de perte de données liée à une limitation de la bande passante.

Notez qu’Application Insights n’échantillonne pas les données de télémétrie de type Mesures et Sessions car il peut être préférable d’être moins précis pour ces types de données.

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

 * Dans l’implémentation actuelle, vous ne serez généralement pas amené à modifier le pourcentage d’échantillonnage après l’avoir configuré au démarrage de l’application. Même si vous pouvez contrôler l’exécution du pourcentage d’échantillonnage, il n’existe aucun moyen de déterminer le pourcentage d’échantillonnage optimal qui pourra collecter « le volume de données parfaitement adéquat » avant que la logique de limitation n’intervienne ou que le quota de volume de données mensuel soit atteint. Les versions ultérieures du kit de développement logiciel Application Insights incluront un échantillonnage adaptatif qui ajustera à la volée le pourcentage d’échantillonnage en fonction du volume de données de télémétrie actuellement observé ainsi que d’autres facteurs. 

*Comment déterminer le pourcentage d’échantillonnage optimal pour mon application ?*

* Pour l’heure, vous devez procéder par tâtonnements. Analysez votre utilisation actuelle des données de télémétrie dans AI, observez les suppressions de données associées à la limitation, puis estimez le volume de données de télémétrie collectées. Ces trois entrées, combinées au niveau tarifaire sélectionné, vous indiqueront dans quelle mesure vous devrez réduire le volume de données de télémétrie collectées. Un changement de modèle au niveau du volume de télémétrie peut toutefois invalider le pourcentage d’échantillonnage optimal configuré (en cas d’augmentation du nombre de vos utilisateurs, par exemple). Une fois implémenté, l’échantillonnage adaptatif ajuste automatiquement le pourcentage d’échantillonnage à son niveau optimal, compte tenu du volume de télémétrie observé.

*Que se passe-t-il si je configure le pourcentage d’échantillonnage à un niveau trop faible ?*

* Un pourcentage d’échantillonnage excessivement faible (échantillonnage trop agressif) aura pour effet de réduire la précision des approximations lorsqu’Application Insights tente de compenser la visualisation des données pour tenir compte de la réduction du volume de données. Il peut également affecter l’expérience de diagnostic puisque l’échantillonnage peut inclure certaines requêtes rarement sujettes à des problèmes d’échec ou de ralentissement.

*Que se passe-t-il si je configure un pourcentage d’échantillonnage trop élevé ?*

* Si vous configurez un pourcentage d’échantillonnage trop élevé (c’est-à-dire pas assez agressif), le volume de données de télémétrie collectées ne sera pas suffisamment réduit. Vous risquez de perdre des données de télémétrie sous l’effet de la limitation de bande passante et de supporter des coûts plus élevés que prévu pour l’utilisation d’Application Insights en raison des frais de dépassement.

*Sur quelles plates-formes puis-je utiliser l’échantillonnage ?*

* L’échantillonnage est actuellement disponible pour toutes les pages Web ainsi que pour les applications Web .NET côté client et côté serveur.

*Puis-je utiliser l’échantillonnage avec des applications d’appareils mobiles (Windows Phone, iOS ou Android) ou avec des applications de bureau ?*

* Non, l’échantillonnage pour les applications d’appareils mobiles ou de bureau n’est pas pris en charge actuellement. 

>>>>>>> 36f8b905a3f60271ee6dc3a17c3ca431937287dc

<!---HONumber=Nov15_HO4-->