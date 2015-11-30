<properties
   pageTitle="Application Insights pour Azure Cloud Services"
   description="Surveillance efficace de vos rôles Web et de travail avec Application Insights"
   services="application-insights"
   documentationCenter=""
   authors="soubhagyadash"
   manager="douge"
   editor="alancameronwills"/>

<tags
   ms.service="application-insights"
   ms.devlang="na"
   ms.tgt_pltfrm="ibiza"
   ms.topic="article"
   ms.workload="tbd"
   ms.date="11/15/2015"
   ms.author="sdash"/>

# Application Insights pour Azure Cloud Services


*Application Insights est à l'état de version préliminaire*

La disponibilité, les performances, les échecs et l’utilisation des [applications de services de cloud computing Microsoft Azure](http://azure.microsoft.com/services/cloud-services/) peuvent être surveillées par [Visual Studio Application Insights][start]. Avec les retours que vous obtenez sur les performances et l’efficacité de votre application dans la nature, vous pouvez prendre des décisions avisées sur la direction de la conception de chaque cycle de développement.

![Exemple](./media/app-insights-cloudservices/sample.png)

Tout d’abord, vous avez besoin d’un abonnement à [Microsoft Azure](http://azure.com). Connectez-vous avec un compte Microsoft, que vous pouvez avoir pour Windows, XBox Live ou d’autres services cloud de Microsoft.


#### Exemple d’application instrumentée avec Application Insights

Examinons l’[exemple d’application](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService) dans lequel Application Insights est ajouté à un service cloud avec deux rôles de travail hébergés dans Azure.

Les informations suivantes vous indiqueront comment adapter votre propre projet de service cloud de la même façon.

## Création d’une ressource Application Insights pour chaque rôle

Une ressource Application Insights est l’endroit où vos données de télémétrie sont analysées et affichées.

1.  Dans le [portail Azure][portal], créez une ressource Application Insights. Choisissez ASP.NET comme type d’application. 

    ![Cliquez sur Nouveau > Application Insights](./media/app-insights-cloudservices/01-new.png)

2.  Copiez la clé d'instrumentation. Vous en aurez besoin dans peu de temps pour configurer le Kit de développement logiciel (SDK).

    ![Cliquez sur Propriétés, sélectionnez la clé et appuyez sur ctrl + C](./media/app-insights-cloudservices/02-props.png)


Il est généralement préférable de créer une ressource distincte pour les données à partir de chaque rôle Web et de travail.

Vous pouvez également envoyer des données à partir de tous les rôles à une seule ressource, mais définir une [propriété par défaut][apidefaults] afin de pouvoir filtrer ou regrouper les résultats de chaque rôle.

## <a name="sdk"></a>Installation du Kit de développement logiciel (SDK) dans chaque projet


1. Dans Visual Studio, modifiez les packages NuGet de votre projet d’application cloud.

    ![Cliquez avec le bouton droit sur le projet et sélectionnez Gérer les packages NuGet](./media/app-insights-cloudservices/03-nuget.png)

2. Ajoutez le package NuGet [Application Insights pour le Web](http://www.nuget.org/packages/Microsoft.ApplicationInsights.Web). Cette version du Kit de développement logiciel (SDK) inclut des modules qui ajoutent du contexte de serveur tel que des informations de rôle. Pour les rôles de travail, utilisez Application Insights pour les services Windows.

    ![Recherchez « Application Insights »](./media/app-insights-cloudservices/04-ai-nuget.png)


3. Configurez le Kit de développement logiciel (SDK) pour envoyer des données à la ressource Application Insights.

    Définissez la clé d'instrumentation comme un paramètre de configuration dans le fichier `ServiceConfiguration.Cloud.cscfg`. ([Exemple de code](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/AzureEmailService/ServiceConfiguration.Cloud.cscfg)).
 
    ```XML
     
     <Role name="WorkerRoleA"> 
      <Setting name="Telemetry.AI.InstrumentationKey" value="YOUR IKEY" /> 
     </Role>
    ```
 
    Dans une fonction de démarrage appropriée, définissez la clé d'instrumentation à partir du paramètre de configuration :

    ```C#

     TelemetryConfiguration.Active.InstrumentationKey = RoleEnvironment.GetConfigurationSettingValue("Telemetry.AI.InstrumentationKey");
    ```

    Faites cela pour chaque rôle de votre application. Consultez les exemples :
 
 * [Rôle Web](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/Global.asax.cs#L27)
 * [Rôle de travail](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/WorkerRoleA.cs#L232)
 * [Pour les pages Web](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/Views/Shared/_Layout.cshtml#L13)   

4. Définissez le fichier ApplicationInsights.config pour que celui-ci soit toujours copié dans le répertoire de sortie. 

    (Dans le fichier .config, vous verrez des messages vous demandant de placer la clé d'instrumentation là. Toutefois, pour les applications cloud, il est préférable de la définir à partir du fichier .cscfg. Ceci garantit que le rôle est identifié correctement dans le portail.)

## Activer les diagnostics Azure

Les diagnostics Azure envoient des compteurs de performances, des journaux des événements Windows et des journaux de suivi de votre application à Application Insights.

Dans l’Explorateur de solutions, ouvrez les propriétés de chaque rôle. Activez **Envoyer des diagnostics à Application Insights**.

![Dans les propriétés, sélectionnez Activer les diagnostics, puis Envoyer à Application Insights.](./media/app-insights-cloudservices/05-wad.png)

Faites de même pour les autres rôles.

### Activer les diagnostics Azure dans une application en ligne ou une machine virtuelle Azure

Vous pouvez également activer les diagnostics lorsque l’application est déjà en cours d’exécution sur Azure en ouvrant ses propriétés dans l’Explorateur de serveurs ou Cloud Explorer dans Visual Studio.


## Utilisez le Kit de développement logiciel (SDK) pour signaler la télémétrie
### Signaler des requêtes
 * Dans les rôles Web, le module des requêtes collecte automatiquement les données relatives aux requêtes HTTP. Consultez l’[exemple MVCWebRole](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/MvcWebRole) pour obtenir des exemples vous indiquant comment remplacer le comportement de regroupement par défaut. 
 * Vous pouvez capturer les performances des appels aux rôles de travail en effectuant le suivi de la même façon que les requêtes HTTP. Dans Application Insights, le type de demande de télémétrie mesure une unité de travail côté serveur nommée, pouvant être chronométrée et réussir ou échouer indépendamment. Même si les requêtes HTTP sont automatiquement capturées par le Kit de développement logiciel (SDK), vous pouvez insérer votre propre code pour effectuer le suivi des requêtes aux rôles de travail.
 * Consultez les deux exemples de rôles de travail instrumentés pour signaler des requêtes : [WorkerRoleA](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/WorkerRoleA) et [WorkerRoleB](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/WorkerRoleB)

### Signaler des dépendances
  * Le Kit de développement logiciel (SDK) Application Insights peut signaler des appels que votre application a effectués vers des dépendances externes telles que les API REST et les serveurs SQL. Ceci vous permet de voir si une dépendance spécifique est à l'origine des réponses lentes ou des défaillances.
  * Pour suivre les dépendances, vous devez configurer le rôle Web/de travail avec [Application Insights Agent](app-insights-monitor-performance-live-website-now.md), également appelé « Status Monitor ».
  * Pour utiliser Application Insights Agent avec vos rôles Web/de travail :
    * Ajoutez le dossier [AppInsightsAgent](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/WorkerRoleA/AppInsightsAgent), accompagné des deux fichiers, à vos projets de rôle Web/de travail. Assurez-vous de définir leurs propriétés de conception de manière à ce qu'elles soient toujours copiées dans le répertoire de sortie. Ces fichiers installent l'agent.
    * Ajoutez la tâche de démarrage au fichier CSDEF, comme indiqué [ici](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/AzureEmailService/ServiceDefinition.csdef#L18).
    * Remarque : les *rôles de travail* requièrent trois variables d'environnement, comme indiqué [ici](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/AzureEmailService/ServiceDefinition.csdef#L44). Ceci n’est pas obligatoire pour pouvoir utiliser SSMSE.

Voici un exemple de ce que vous voyez sur le portail Application Insights :

* Diagnostics enrichis avec des dépendances et des requêtes automatiquement liées :

    ![](./media/app-insights-cloudservices/SMxacy4.png)

* Performances du rôle Web, avec les informations de dépendance :

    ![](./media/app-insights-cloudservices/6yOBtKu.png)

* Voici une capture d'écran sur les requêtes et les informations de dépendance d’un rôle de travail :

    ![](./media/app-insights-cloudservices/a5R0PBk.png)

### Déclaration des exceptions

* Consultez la rubrique [Analyse des exceptions dans Application Insights](app-insights-asp-net-exceptions.md) pour découvrir comment collecter des exceptions non gérées depuis différents types d'applications Web.
* L’exemple du rôle Web dispose de contrôleurs MVC5 et API Web 2. Les exceptions non gérées à partir de 2 sont capturées avec les éléments suivants :
    * [AiHandleErrorAttribute](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/Telemetry/AiHandleErrorAttribute.cs) configurer [ici](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/App_Start/FilterConfig.cs#L12) pour les contrôleurs MVC5
    * [AiWebApiExceptionLogger](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/Telemetry/AiWebApiExceptionLogger.cs) configurer [ici](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/App_Start/WebApiConfig.cs#L25) pour les contrôleurs de l'API Web 2
* Pour les rôles de travail : il existe deux façons d'effectuer le suivi des exceptions.
    * TrackException(ex)
    * Si vous avez ajouté le package NuGet de l’écouteur de suivi Application Insights, vous pouvez utiliser System.Diagnostics.Trace pour journaliser les exceptions. [Exemple de code.](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/WorkerRoleA.cs#L107)

### Compteurs de performance

Les compteurs suivants sont collectés par défaut :

    * \Process(??APP_WIN32_PROC??)\% Processor Time
	* \Memory\Available Bytes
	* \.NET CLR Exceptions(??APP_CLR_PROC??)# of Exceps Thrown / sec
	* \Process(??APP_WIN32_PROC??)\Private Bytes
	* \Process(??APP_WIN32_PROC??)\IO Data Bytes/sec
	* \Processor(_Total)\% Processor Time

De plus, les éléments suivants sont également collectés pour les rôles Web :

	* \ASP.NET Applications(??APP_W3SVC_PROC??)\Requests/Sec	
	* \ASP.NET Applications(??APP_W3SVC_PROC??)\Request Execution Time
	* \ASP.NET Applications(??APP_W3SVC_PROC??)\Requests In Application Queue

Vous pouvez spécifier des personnalisations supplémentaires ou d’autres compteurs de performance Windows, comme indiqué [ici](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/ApplicationInsights.config#L14)

  ![](./media/app-insights-cloudservices/OLfMo2f.png)

### Télémétrie liée aux rôles de travail

Elle représente une riche expérience de diagnostic, lorsque vous pouvez découvrir ce qui a provoqué l’échec de la demande ou bien une forte latence. Grâce aux rôles Web, le Kit de développement logiciel (SDK) définit automatiquement la corrélation entre les télémétries connexes. Pour les rôles de travail, vous pouvez utiliser un initialiseur de télémétrie personnalisée pour définir un attribut de contexte Operation.Id commun pour toutes les télémétries afin d’y parvenir. Ceci vous permettra de découvrir très rapidement si le problème de latence/d'échec était dû à une dépendance ou à votre code !

Voici comment procéder :

* Définissez l'ID de corrélation dans un CallContext, comme indiqué [ici](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/WorkerRoleA.cs#L36). Dans ce cas, nous utilisons l'ID de la demande comme id de corrélation
* Ajoutez une implémentation personnalisée de TelemetryInitializer, qui définira l’Operation.Id sur le correlationId mentionné ci-dessus. Indiqué ici : [ItemCorrelationTelemetryInitializer](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/Telemetry/ItemCorrelationTelemetryInitializer.cs#L13)
* Ajoutez l'initialiseur de télémétrie personnalisée. Effectuez cette opération dans le fichier ApplicationInsights.config ou dans le code mentionné [ici](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/WorkerRoleA.cs#L233)

Et voilà ! L’expérience du portail est déjà intégrée pour vous permettre de découvrir très rapidement toutes les télémétries qui lui sont associées :

![](./media/app-insights-cloudservices/bHxuUhd.png)

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



## Exemple

[L'exemple](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService) analyse un service qui dispose d’un rôle Web et de deux rôles de travail.



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
[start]: app-insights-overview.md

<!---HONumber=Nov15_HO4-->