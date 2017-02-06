---
title: "Application Insights pour les applications web qui sont déjà actives"
description: "Commencez à surveiller une application web qui est déjà en cours d’exécution sur votre serveur."
services: application-insights
documentationcenter: java
author: harelbr
manager: douge
ms.assetid: 12f3dbb9-915f-4087-87c9-807286030b0b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 11/10/2016
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: 42e682eb8e0a740393648e9fe49244c3a02a9867
ms.openlocfilehash: eb6bce9be34467e472fbae6cbf154f3b789b6ddc


---
# <a name="application-insights-for-java-web-apps-that-are-already-live"></a>Application Insights pour les applications web qui sont déjà actives


Si vous disposez d’une application web qui est déjà en cours d’exécution sur votre serveur J2EE, vous pouvez commencer à la surveiller avec [Application Insights](app-insights-overview.md) sans avoir à modifier le code ni à recompiler votre projet. Grâce à cette option, vous obtenez des informations sur les requêtes HTTP envoyées à votre serveur, les exceptions non gérées et les compteurs de performances.

Vous devrez vous abonner à [Microsoft Azure](https://azure.com).

> [!NOTE]
> La procédure décrite ici ajoute le Kit de développement logiciel (SDK) à votre application web au moment de l’exécution. Cette instrumentation du runtime est utile si vous ne souhaitez pas mettre à jour ni régénérer votre code source. Mais si vous le pouvez, nous vous recommandons plutôt d’ [ajouter le Kit de développement logiciel au code source](app-insights-java-get-started.md) . Cette approche vous offre davantage d’options, telles que l’écriture de code pour effectuer le suivi de l’activité des utilisateurs.
> 
> 

## <a name="1-get-an-application-insights-instrumentation-key"></a>1. Obtenir une clé d'instrumentation Application Insights
1. Se connecter au [portail Microsoft Azure](https://portal.azure.com)
2. Créez une ressource Application Insights et paramétrez le type d’application sur application web Java.
   
    ![Indiquez le nom, choisissez l’application web Java, puis cliquez sur Créer.](./media/app-insights-java-live/02-create.png)

    La ressource est créée en quelques secondes.

4. Ouvrez la nouvelle ressource et obtenez sa clé d’instrumentation. Vous devrez la coller rapidement dans le code de votre projet.
   
    ![Dans la nouvelle vue d'ensemble des ressources, cliquez sur Propriétés et copiez la clé d'instrumentation.](./media/app-insights-java-live/03-key.png)

## <a name="2-download-the-sdk"></a>2. Télécharger le Kit de développement logiciel (SDK)
1. Téléchargez le [Kit de développement logiciel (SDK) Application Insights pour Java](https://aka.ms/aijavasdk). 
2. Sur votre serveur, extrayez le contenu du Kit de développement logiciel dans le répertoire à partir duquel les fichiers binaires de votre projet sont chargés. Si vous utilisez Tomcat, ce répertoire se trouve généralement sous `webapps/<your_app_name>/WEB-INF/lib`

Notez que vous devez répéter cette opération pour chaque application sur chaque instance de serveur.

## <a name="3-add-an-application-insights-xml-file"></a>3. Ajouter un fichier xml Application Insights
Créez le fichier ApplicationInsights.xml dans le dossier auquel vous avez ajouté le Kit de développement logiciel. Placez-y le code XML suivant.

Remplacez la clé d'instrumentation que avez obtenue sur le portail Azure.

```XML

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
```

* La clé d'instrumentation est envoyée avec chaque élément de télémétrie et indique à Application Insights de l'afficher dans votre ressource.
* Le composant de demande HTTP est facultatif. Il envoie automatiquement la télémétrie concernant les demandes et les temps de réponse au portail.
* La corrélation des événements est un complément au composant de demande HTTP. Il assigne un identificateur à chaque demande reçue par le serveur et l'ajoute comme propriété de chaque élément de télémétrie en tant que propriété « Operation.Id ». Il vous permet de mettre en corrélation la télémétrie associée à chaque demande en définissant un filtre dans [recherche de diagnostic](app-insights-diagnostic-search.md).

## <a name="4-add-an-http-filter"></a>4. Ajouter un filtre HTTP
Recherchez et ouvrez le fichier web.xml dans votre projet et fusionnez l'extrait de code suivant sous le nœud de l'application web, où vos filtres d'application sont configurés.

Pour obtenir des résultats plus précis, le filtre doit être mappé avant tous les autres filtres.

```XML

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
```

## <a name="5-check-firewall-exceptions"></a>5. Vérifier les exceptions de pare-feu
Vous devrez peut-être [définir des exceptions pour envoyer les données sortantes](app-insights-ip-addresses.md).

## <a name="6-restart-your-web-app"></a>6. Redémarrer votre application web
## <a name="7-view-your-telemetry-in-application-insights"></a>7. Voir votre télémétrie dans Application Insights
Revenez à votre ressource Application Insights sur le [portail Microsoft Azure](https://portal.azure.com).

La télémétrie des demandes HTTP apparaît dans le panneau Vue d’ensemble. (Si elles n’y sont pas, attendez quelques secondes et cliquez sur Actualiser).

![Exemples de données](./media/app-insights-java-live/5-results.png)

Cliquez sur un des graphiques pour afficher des mesures plus détaillées. 

![](./media/app-insights-java-live/6-barchart.png)

Et lorsque vous affichez les propriétés d'une demande, vous voyez les événements de télémétrie associés, par exemple les demandes et les exceptions.

![](./media/app-insights-java-live/7-instance.png)

[En savoir plus sur les mesures.](app-insights-metrics-explorer.md)

## <a name="next-steps"></a>Étapes suivantes
* [Ajoutez la télémétrie à vos pages web](app-insights-web-track-usage.md) pour surveiller les affichages de pages et les mesures relatives à l’utilisateur.
* [Configurez les tests web](app-insights-monitor-web-app-availability.md) pour vous assurer que votre application est bien active.
* [Capture le suivi des journaux](app-insights-java-trace-logs.md)
* [Recherchez les événements et les journaux](app-insights-diagnostic-search.md) pour diagnostiquer les problèmes.




<!--HONumber=Feb17_HO1-->


