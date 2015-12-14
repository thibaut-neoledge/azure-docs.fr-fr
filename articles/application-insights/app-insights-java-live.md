<properties 
	pageTitle="Application Insights pour les applications web qui sont déjà actives" 
	description="Commencez à surveiller une application web qui est déjà en cours d’exécution sur votre serveur." 
	services="application-insights" 
    documentationCenter="java"
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/24/2015" 
	ms.author="awills"/>
 
# Application Insights pour les applications web qui sont déjà actives

*Application Insights est à l'état de version préliminaire.*

Si vous disposez d’une application web qui est déjà en cours d’exécution sur votre serveur J2EE, vous pouvez commencer à la surveiller avec [Application Insights](app-insights-overview.md) sans avoir à modifier le code ni à recompiler votre projet. Grâce à cette option, vous obtenez des informations sur les requêtes HTTP envoyées à votre serveur, les exceptions non gérées et les compteurs de performances.

Vous devrez vous abonner à [Microsoft Azure](https://azure.com).

> [AZURE.NOTE]La procédure décrite ici ajoute le Kit de développement logiciel (SDK) à votre application web au moment de l’exécution. Cette opération est utile si vous ne souhaitez pas mettre à jour ni régénérer votre code source. Mais si vous le pouvez, nous vous recommandons plutôt d’[ajouter le Kit de développement logiciel au code source](app-insights-java-get-started.md). Cette approche vous offre davantage d’options, telles que l’écriture de code pour effectuer le suivi de l’activité des utilisateurs.

## 1\. Obtenir une clé d'instrumentation Application Insights

1. Connectez-vous au [portail Microsoft Azure](https://portal.azure.com).
2. Créer une ressource Application Insights dans Azure

    ![Cliquez sur + et choisissez Ajouter Application Insights](./media/app-insights-java-live/01-create.png)
3. Définissez le type d’application sur Application web Java.

    ![Indiquez le nom, choisissez l’application web Java, puis cliquez sur Créer.](./media/app-insights-java-live/02-create.png)
4. Obtenez la clé d'instrumentation de la nouvelle ressource. Vous devrez la coller rapidement dans le code de votre projet.

    ![Dans la nouvelle vue d'ensemble des ressources, cliquez sur Propriétés et copiez la clé d'instrumentation.](./media/app-insights-java-live/03-key.png)

## 2\. Télécharger le Kit de développement logiciel (SDK)

1. Téléchargez le [Kit de développement logiciel (SDK) Application Insights pour Java](https://azuredownloads.blob.core.windows.net/applicationinsights/sdk.html). 
2. Sur votre serveur, extrayez le contenu du Kit de développement logiciel dans le répertoire à partir duquel les fichiers binaires de votre projet sont chargés. Si vous utilisez Tomcat, le contenu est généralement extrait sous `webapps<your_app_name>\WEB-INF\lib`.


## 3\. Ajouter un fichier xml Application Insights

Créez le fichier ApplicationInsights.xml dans le dossier auquel vous avez ajouté le Kit de développement logiciel. Placez-y le code XML suivant.

Remplacez la clé d'instrumentation que avez obtenue sur le portail Azure.

    <?xml version="1.0" encoding="utf-8"?>
    <ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings" schemaVersion="2014-05-30">


      <!-- The key from the portal: -->

      <InstrumentationKey>** Your instrumentation key **</InstrumentationKey>


      <!-- HTTP request component (not required for bare API) -->

      <TelemetryModules>
        <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebRequestTrackingTelemetryModule"/>
        <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebSessionTrackingTelemetryModule"/>
        <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebUserTrackingTelemetryModule"/>
      </TelemetryModules>

      <!-- Events correlation (not required for bare API) -->
      <!-- These initializers add context data to each event -->

      <TelemetryInitializers>
        <Add   type="com.microsoft.applicationinsights.web.extensibility.initializers.WebOperationIdTelemetryInitializer"/>
        <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebOperationNameTelemetryInitializer"/>
        <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebSessionTelemetryInitializer"/>
        <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebUserTelemetryInitializer"/>
        <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebUserAgentTelemetryInitializer"/>

      </TelemetryInitializers>
    </ApplicationInsights>


* La clé d'instrumentation est envoyée avec chaque élément de télémétrie et indique à Application Insights de l'afficher dans votre ressource.
* Le composant de demande HTTP est facultatif. Il envoie automatiquement la télémétrie concernant les demandes et les temps de réponse au portail.
* La corrélation des événements est un complément au composant de demande HTTP. Il assigne un identificateur à chaque demande reçue par le serveur et l'ajoute comme propriété de chaque élément de télémétrie en tant que propriété « Operation.Id ». Il vous permet de mettre en corrélation la télémétrie associée à chaque demande en définissant un filtre dans [recherche de diagnostic](app-insights-diagnostic-search.md).


## 4\. Ajouter un filtre HTTP

Recherchez et ouvrez le fichier web.xml dans votre projet et fusionnez l'extrait de code suivant sous le nœud de l'application web, où vos filtres d'application sont configurés.

Pour obtenir des résultats plus précis, le filtre doit être mappé avant tous les autres filtres.

    <filter>
      <filter-name>ApplicationInsightsWebFilter</filter-name>
      <filter-class>
        com.microsoft.applicationinsights.web.internal.WebRequestTrackingFilter
      </filter-class>
    </filter>
    <filter-mapping>
       <filter-name>ApplicationInsightsWebFilter</filter-name>
       <url-pattern>/*</url-pattern>
    </filter-mapping>

## 5\. Redémarrer votre application web

## 6\. Voir votre télémétrie dans Application Insights

Revenez à votre ressource Application Insights sur le [portail Microsoft Azure](https://portal.azure.com).

Les données des demandes HTTP apparaissent dans le panneau Vue d’ensemble. (Si elles n’y sont pas, attendez quelques secondes et cliquez sur Actualiser).

![Exemples de données](./media/app-insights-java-live/5-results.png)
 

Cliquez sur un des graphiques pour afficher des mesures plus détaillées.

![](./media/app-insights-java-live/6-barchart.png)

 

Et lorsque vous affichez les propriétés d'une demande, vous voyez les événements de télémétrie associés, par exemple les demandes et les exceptions.
 
![](./media/app-insights-java-live/7-instance.png)


[En savoir plus sur les mesures.](app-insights-metrics-explorer.md)



## Étapes suivantes

* [Ajoutez la télémétrie à vos pages web](app-insights-web-track-usage.md) pour surveiller les affichages de pages et les mesures relatives à l’utilisateur.
* [Configurez les tests web](app-insights-monitor-web-app-availability.md) pour vous assurer que votre application est bien active.
* [Capture le suivi des journaux](app-insights-java-trace-logs.md)
* [Recherchez les événements et les journaux](app-insights-diagnostic-search.md) pour diagnostiquer les problèmes.


 

<!---HONumber=AcomDC_1203_2015-->