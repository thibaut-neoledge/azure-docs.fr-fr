---
title: Envoyer des journaux Azure Diagnostics vers Application Insights
description: "Configurez les informations des journaux de diagnostic Azure Cloud Services à envoyer vers le portail Application Insights."
services: application-insights
documentationcenter: .net
author: sbtron
manager: douge
ms.assetid: a67dd20a-fc5d-4391-ba63-bfe164fb62f7
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 11/17/2015
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 2843ff25c118afe5d5421b322b529e9a9f2d5036


---
# <a name="configure-azure-diagnostic-logging-to-application-insights"></a>Configuration de la journalisation Azure Diagnostics dans Application Insights
Lorsque vous configurez un projet Cloud Services ou une machine virtuelle dans Microsoft Azure, [ce dernier peut générer un journal de diagnostic](../vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines.md). Vous pouvez l’envoyer à Application Insights pour l’analyser en même temps que les données de télémétrie de diagnostic et d’utilisation envoyées depuis l’application par le Kit de développement logiciel (SDK) Application Insights. Le journal Azure inclut les événements liés à la gestion de l’application telles que les démarrages, arrêts et incidents, ainsi que ceux des compteurs de performances. Le journal comporte également les appels de l’application vers System.Diagnostics.Trace.

Cet article décrit en détail la configuration de la capture de diagnostic.

Le SDK 2.8 ou ultérieur d’Azure doit être installé dans Visual Studio.

## <a name="get-an-application-insights-resource"></a>Obtenir une ressource Application Insights
Pour une expérience optimale, [ajoutez le Kit de développement logiciel (SDK) Application Insights à chaque rôle de votre application de services cloud](app-insights-cloudservices.md) ou [à n’importe quelle application que vous envisagez d’exécuter sur votre machine virtuelle](app-insights-overview.md). Vous pouvez alors envoyer les données de diagnostic pour qu’elles soient analysées et affichées dans la même ressource Application Insights.

Vous pouvez également, si vous ne souhaitez pas utiliser le Kit de développement logiciel (par exemple si l’application a déjà été publiée), simplement [créer une nouvelle ressource Application Insights](app-insights-create-new-resource.md) dans le portail Azure. Choisissez **Azure Diagnostics** comme type d’application.

## <a name="send-azure-diagnostics-to-application-insights"></a>Envoi de diagnostics Azure vers Application Insights
Si vous êtes en mesure de mettre à jour votre projet d’application, dans Visual Studio, sélectionnez chaque rôle, définissez ses propriétés, puis, dans l’onglet Configuration, sélectionnez **Envoyer des diagnostics à Application Insights**.

Lorsque vous utilisez la commande Publier pour télécharger votre application, vous pouvez également choisir l’option Application Insights dans la page Diagnostics.

Si votre application a déjà été publiée, utilisez l’Explorateur de serveurs ou l’Explorateur de services cloud de Visual Studio pour ouvrir les propriétés de l’application. Sélectionnez **Envoyer des diagnostics à Application Insights**.

Dans tous les cas, vous devrez donner des informations sur la ressource Application Insights que vous avez créée.

Nous vous recommandons d'envoyer les données de rôles distincts afin de séparer les ressources. Vous pouvez afficher les graphiques de mesures côte à côte dans le portail en créant un [tableau de bord](app-insights-dashboards.md).

[Plus d’informations sur la configuration d’Application Insights pour une application de services cloud](app-insights-cloudservices.md).

## <a name="configuring-the-azure-diagnostics-adapter"></a>Configuration de l’adaptateur de diagnostic Azure
Lisez la suite uniquement si vous souhaitez sélectionner les parties du journal à envoyer à Application Insights. Par défaut, le journal est envoyé dans son intégralité, y compris : les événements Microsoft Azure ; les compteurs de performances ; le suivi des appels de l’application vers System.Diagnostics.Trace.

Lorsque vous modifiez les options de diagnostic dans l’éditeur de propriétés de rôle ou l'assistant de publication, vous modifiez en fait le contenu de deux ensembles de fichiers :

* Les [fichiers de configuration de diagnostic](https://msdn.microsoft.com/library/azure/dn782207.aspx) du rôle. Vous les trouverez dans l’Explorateur de solutions sous `<Your Service>/Roles/*/diagnostics.wadcfgx`
* Les fichiers de configuration de service, `ServiceConfiguration.*.cscfg`.

Modifiez ces fichiers directement afin de définir plus spécifiquement les options que ne le permettent les Assistants. Lisez la suite pour plus de détails. 

## <a name="separate-development-and-production-resources"></a>Ressources de développement et de production distinctes
Vous pouvez envoyer les données de télémétrie à partir des horodatages de développement et de production de votre application vers différentes ressources Application Insights. Cela évite que vos données de télémétrie de développement ne soient submergées par les données de télémétrie en direct. 

1. [Créez une ressource Application Insights](app-insights-create-new-resource.md) pour chaque horodatage. Obtenez la clé d’instrumentation à partir de l’onglet Essentials de chaque ressource.
2. Modifiez les deux fichiers .cscfg et insérez des clés d’instrumentation différentes.

## <a name="choose-the-priority-levels-to-send"></a>Choisir les niveaux de priorité d'envoi
Dans le fichier de configuration de diagnostics `diagnostics.wadcfgx` de chaque rôle, vous pouvez filtrer par niveaux les messages du journal.

### <a name="define-a-sink"></a>Définition d'un récepteur
`<SinksConfig>` définit le récepteur supplémentaire auquel les données des diagnostics Azure peuvent être envoyées.  Voici un exemple de `SinksConfig` :

```xml

    <SinksConfig>
     <Sink name="ApplicationInsights">
      <ApplicationInsights/>
      <Channels>
        <Channel logLevel="Error" name="MyTopDiagData"  />
        <Channel logLevel="Verbose" name="MyLogData"  />
      </Channels>
     </Sink>
    </SinksConfig>

```

`Channels` nomme un flux de données qui sera envoyé au récepteur. Le canal joue le rôle de filtre. L’attribut `loglevel` vous permet de spécifier le niveau de consignation envoyé par le canal. Les valeurs disponibles sont : `{Verbose, Information, Warning, Error, Critical}`.

### <a name="send-data-to-the-sink"></a>Envoi de données vers le récepteur
Envoyez des données au récepteur Application Insights en ajoutant l’attribut sinks sous le nœud DiagnosticMonitorConfiguration ou n'importe quel nœud sous-jacent. En ajoutant l’élément sinks à chaque nœud, vous spécifiez que vous voulez envoyer au récepteur spécifié les données collectées à partir de ce nœud et des nœuds inférieurs.

Par exemple, par défaut, le Kit de développement logiciel (SDK) Azure définit l’envoi de toutes les données de diagnostic Azure :

```xml

    <DiagnosticMonitorConfiguration overallQuotaInMB="4096" sinks="ApplicationInsights">
```

Mais si vous souhaitez envoyer uniquement les journaux d’erreurs, ajoutez un nom de canal au nom du récepteur :

```xml

    <DiagnosticMonitorConfiguration overallQuotaInMB="4096" sinks="ApplicationInsights.MyTopDiagdata">
```

Notez que nous utilisons le nom du récepteur que nous avons défini, ainsi que le nom d’un canal défini plus tôt.

Si vous souhaitez envoyer uniquement les journaux d’applications détaillés à Application Insights, vous devez ajouter l’attribut sinks au nœud `Logs` .

```xml

    <Logs scheduledTransferPeriod="PT1M" scheduledTransferLogLevelFilter="Verbose" sinks="ApplicationInsights.MyLogData"/>
```

Vous pouvez également inclure plusieurs récepteurs à la configuration à différents niveaux de la hiérarchie. Dans ce cas, le récepteur spécifié au niveau supérieur de la hiérarchie fonctionne comme un paramètre global et celui spécifié au niveau des éléments écrase ce paramètre global.

Voici un exemple complet de fichier de configuration publique qui envoie toutes les erreurs à Application Insights (spécifié au nœud `DiagnosticMonitorConfiguration`), ainsi que les journaux de niveau détaillé des journaux d’applications (spécifié au nœud `Logs`).

```xml

    <WadCfg>
     <DiagnosticMonitorConfiguration overallQuotaInMB="4096"
       sinks="ApplicationInsights.MyTopDiagData">
       <!-- All info below sent to this channel -->
      <DiagnosticInfrastructureLogs />
      <PerformanceCounters>
        <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT3M" sinks="ApplicationInsights.MyLogData/>
        <PerformanceCounterConfiguration counterSpecifier="\Memory\Available MBytes" sampleRate="PT3M" />
        <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\Bytes Total/Sec" sampleRate="PT3M" />
      </PerformanceCounters>
      <WindowsEventLog scheduledTransferPeriod="PT1M">
        <DataSource name="Application!*" />
      </WindowsEventLog>
      <Logs scheduledTransferPeriod="PT1M" scheduledTransferLogLevelFilter="Verbose"
            sinks="ApplicationInsights.MyLogData"/>
       <!-- This specific info sent to this channel -->
     </DiagnosticMonitorConfiguration>

     <SinksConfig>
      <Sink name="ApplicationInsights">
        <ApplicationInsights/>
        <Channels>
          <Channel logLevel="Error" name="MyTopDiagData"  />
          <Channel logLevel="Verbose" name="MyLogData"  />
        </Channels>
      </Sink>
     </SinksConfig>
    </WadCfg>
```


Il existe certaines limitations à connaître pour cette fonctionnalité :

* Les canaux sont uniquement destinés à fonctionner avec un type journal et non avec des compteurs de performances. Si vous spécifiez un canal comprenant un élément compteur de performances, il sera ignoré.
* Le niveau de consignation d'un canal ne peut pas dépasser le niveau de consignation de ce qui est collecté par les diagnostics Azure. Par exemple, vous ne pouvez pas collecter les erreurs de consignation des applications dans l'élément Logs et tenter d'envoyer des journaux détaillés à la synchronisation Application Insight. L’attribut scheduledTransferLogLevelFilter doit toujours collecter au moins autant de journaux que ceux que vous essayez d’envoyer à un récepteur.
* Vous ne pouvez pas envoyer des données blob collectées par l'extension des diagnostics Azure à Application Insights. Par exemple, rien qui soit spécifié sous le nœud Directories. Pour les vidages sur incident, le vidage sur incident réel sera toujours envoyé au stockage d'objets blob et seule une notification de génération du vidage sur incident sera envoyée à Application Insights.

## <a name="next-steps"></a>Étapes suivantes
* [Surveillance d’Azure Cloud Services avec Application Insights](app-insights-cloudservices.md)
* [Utilisation de PowerShell pour l’envoi de diagnostics Azure vers Application Insights](app-insights-powershell-azure-diagnostics.md)
* [Fichier de configuration Azure Diagnostics](https://msdn.microsoft.com/library/azure/dn782207.aspx)




<!--HONumber=Nov16_HO3-->


