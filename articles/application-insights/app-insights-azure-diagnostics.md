<properties
    pageTitle="Envoyer des journaux Azure Diagnostics vers Application Insights"
    description="Configurez les informations des journaux de diagnostic Azure Cloud Services à envoyer vers le portail Application Insights."
    services="application-insights"
    documentationCenter=".net"
    authors="sbtron"
    manager="douge"/>

<tags
    ms.service="application-insights"
    ms.workload="tbd"
	ms.tgt_pltfrm="ibiza" 
    ms.devlang="na"
    ms.topic="article"
	ms.date="11/17/2015"
    ms.author="awills"/>

# Configuration de la journalisation Azure Diagnostics dans Application Insights

Lorsque vous configurez un projet Cloud Services ou une machine virtuelle dans Microsoft Azure, [ce dernier peut générer un journal de diagnostic](vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines.md). Vous pouvez l’envoyer à Application Insights pour l’analyser en même temps que les données de télémétrie de diagnostic et d’utilisation envoyées depuis l’application par le Kit de développement logiciel (SDK) Application Insights. Le journal Azure inclut les événements liés à la gestion de l’application telles que les démarrages, arrêts et incidents, ainsi que ceux des compteurs de performances. Le journal comporte également les appels de l’application vers System.Diagnostics.Trace.

Cet article décrit en détail la configuration de la capture de diagnostic.

Le SDK 2.8 d’Azure doit être installé dans Visual Studio.

## Obtenir une ressource Application Insights

Pour une expérience optimale, [ajoutez le Kit de développement logiciel (SDK) Application Insights à chaque rôle de votre application de services cloud](app-insights-cloudservices.md) ou [à n’importe quelle application que vous envisagez d’exécuter sur votre machine virtuelle](app-insights-get-started.md). Vous pouvez alors envoyer les données de diagnostic pour qu’elles soient analysées et affichées dans la même ressource Application Insights.

Vous pouvez également, si vous ne souhaitez pas utiliser le Kit de développement logiciel (par exemple si l’application a déjà été publiée), simplement [créer une nouvelle ressource Application Insights](app-insights-create-new-resource.md) dans le portail Azure. Choisissez **Azure Diagnostics** comme type d’application.


## Envoi de diagnostics Azure vers Application Insights

Si vous êtes en mesure de mettre à jour votre projet d’application, dans Visual Studio, sélectionnez chaque rôle, définissez ses propriétés, puis, dans l’onglet Configuration, sélectionnez **Envoyer des diagnostics à Application Insights**.

Si votre application a déjà été publiée, utilisez l’Explorateur de serveurs ou l’Explorateur de services cloud de Visual Studio pour ouvrir les propriétés de l’application. Sélectionnez **Envoyer des diagnostics à Application Insights**.

Dans tous les cas, vous devrez donner des informations sur la ressource Application Insights que vous avez créée.

[Plus d’informations sur la configuration d’Application Insights pour une application de services cloud](app-insights-cloudservices.md).

## Configuration de l’adaptateur de diagnostic Azure

Lisez la suite uniquement si vous souhaitez sélectionner les parties du journal à envoyer à Application Insights. Par défaut, le journal est envoyé dans son intégralité, y compris : les événements Microsoft Azure ; les compteurs de performances ; le suivi des appels de l’application vers System.Diagnostics.Trace.

Les diagnostics Azure stockent les données dans des tables Azure Storage. Toutefois, vous pouvez également traiter tout ou partie des données dans Application Insights en configurant les « récepteurs » et les « canaux » de votre configuration lors de l’utilisation de l’extension d’Azure Diagnostic 1.5 ou version ultérieure.

### Configuration d’Application Insights comme récepteur

Lorsque vous utilisez les propriétés de rôle pour définir l’option « Envoyer des données à Application Insights », le Kit de développement logiciel (SDK) Microsoft Azure (version 2.8 ou ultérieure) ajoute un élément `<SinksConfig>` au [fichier de configuration publique Azure Diagnostics](https://msdn.microsoft.com/library/azure/dn782207.aspx) du rôle.

`<SinksConfig>` définit le récepteur supplémentaire auquel les données des diagnostics Azure peuvent être envoyées. Voici un exemple de `SinksConfig` :

```xml

    <SinksConfig>
     <Sink name="ApplicationInsights">
      <ApplicationInsights>{Insert InstrumentationKey}</ApplicationInsights>
      <Channels>
        <Channel logLevel="Error" name="MyTopDiagData"  />
        <Channel logLevel="Verbose" name="MyLogData"  />
      </Channels>
     </Sink>
    </SinksConfig> 

```

L’élément `ApplicationInsights` spécifie la clé d’instrumentation de la ressource Application Insights à laquelle les données des diagnostics Azure seront envoyées. Lorsque vous sélectionnez la ressource, il est automatiquement défini selon la configuration du service `APPINSIGHTS_INSTRUMENTATIONKEY`. (Si vous souhaitez le définir manuellement, récupérez la clé dans la liste déroulante Essentials de la ressource.)

`Channels` définit les données qui seront envoyées au récepteur. Le canal joue le rôle de filtre. L’attribut `loglevel` vous permet de spécifier le niveau de consignation envoyé par le canal. Les valeurs disponibles sont : `{Verbose, Information, Warning, Error, Critical}`.

### Envoi de données vers le récepteur

Envoyez des données au récepteur Application Insights en ajoutant l’attribut sinks sous le nœud DiagnosticMonitorConfiguration. En ajoutant l’élément sinks à chaque nœud, vous spécifiez que vous voulez envoyer au récepteur spécifié les données collectées à partir de ce nœud et des nœuds inférieurs.

Par exemple, par défaut, le Kit de développement logiciel (SDK) Azure définit l’envoi de toutes les données de diagnostic Azure :

```xml

    <DiagnosticMonitorConfiguration overallQuotaInMB="4096" sinks="ApplicationInsights">
```

Mais si vous souhaitez envoyer uniquement les journaux d’erreurs, ajoutez un nom de canal au nom du récepteur :

```xml

    <DiagnosticMonitorConfiguration overallQuotaInMB="4096" sinks="ApplicationInsights.MyTopDiagdata">
```

Notez que nous utilisons le nom du récepteur que nous avons défini, ainsi que le nom d’un canal défini plus tôt.

Si vous souhaitez envoyer uniquement les journaux d’applications détaillés à Application Insights, vous devez ajouter l’attribut sinks au nœud `Logs`.

```xml

    <Logs scheduledTransferPeriod="PT1M" scheduledTransferLogLevelFilter="Verbose" sinks="ApplicationInsights.MyLogData"/>
```

Vous pouvez également inclure plusieurs récepteurs à la configuration à différents niveaux de la hiérarchie. Dans ce cas, le récepteur spécifié au niveau supérieur de la hiérarchie fonctionne comme un paramètre global et celui spécifié au niveau des éléments écrase ce paramètre global.

Voici un exemple complet de fichier de configuration publique qui envoie toutes les erreurs à Application Insights (spécifié au nœud `DiagnosticMonitorConfiguration`), ainsi que les journaux de niveau détaillé des journaux d’applications (spécifié au nœud `Logs`).

```xml

    <WadCfg>
     <DiagnosticMonitorConfiguration overallQuotaInMB="4096"
       sinks="ApplicationInsights.MyTopDiagData"> <!-- All info below sent to this channel -->
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
        <ApplicationInsights>{Insert InstrumentationKey}</ApplicationInsights>
        <Channels>
          <Channel logLevel="Error" name="MyTopDiagData"  />
          <Channel logLevel="Verbose" name="MyLogData"  />
        </Channels>
      </Sink>
     </SinksConfig>
    </WadCfg>
```

![](./media/app-insights-azure-diagnostics/diagnostics-publicconfig.png)

Il existe certaines limitations à connaître pour cette fonctionnalité :

* Les canaux sont uniquement destinés à fonctionner avec un type journal et non avec des compteurs de performances. Si vous spécifiez un canal comprenant un élément compteur de performances, il sera ignoré. 
* Le niveau de consignation d'un canal ne peut pas dépasser le niveau de consignation de ce qui est collecté par les diagnostics Azure. Par exemple, vous ne pouvez pas collecter les erreurs de consignation des applications dans l'élément Logs et tenter d'envoyer des journaux détaillés à la synchronisation Application Insight. L’attribut scheduledTransferLogLevelFilter doit toujours collecter au moins autant de journaux que ceux que vous essayez d’envoyer à un récepteur. 
* Vous ne pouvez pas envoyer des données blob collectées par l'extension des diagnostics Azure à Application Insights. Par exemple, rien qui soit spécifié sous le nœud Directories. Pour les vidages sur incident, le vidage sur incident réel sera toujours envoyé au stockage d'objets blob et seule une notification de génération du vidage sur incident sera envoyée à Application Insights. 

## Rubriques connexes

* [Surveillance d’Azure Cloud Services avec Application Insights](app-insights-cloudservices.md)
* [Utilisation de PowerShell pour l’envoi de diagnostics Azure vers Application Insights](app-insights-powershell-azure-diagnostics.md)
* [Fichier de configuration Azure Diagnostics](https://msdn.microsoft.com/library/azure/dn782207.aspx)

<!---HONumber=AcomDC_1125_2015-->