<properties
   pageTitle="Configurer les diagnostics Azure pour envoyer des données à Application Insights | Microsoft Azure"
   description="Mettez à jour la configuration publique des diagnostics Azure pour envoyer des données à Application Insights."
   services="multiple"
   documentationCenter=".net"
   authors="sbtron"
   manager=""
   editor="" />
<tags
   ms.service="application-insights"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="12/15/2015"
   ms.author="saurabh" />

# Configurer les diagnostics Azure pour envoyer des données à Application Insights

Les diagnostics Azure stockent les données dans des tables Azure Storage. Toutefois, vous pouvez également traiter tout ou partie des données dans Application Insights en configurant les « récepteurs » et les « canaux » de votre configuration lors de l'utilisation de l'extension des diagnostics Azure 1.5 ou version ultérieure.

Cet article décrit comment créer la configuration publique de l'extension des diagnostics Azure afin qu'elle puisse envoyer des données à Application Insights.

## Configuration d'Application Insights comme récepteur

L'extension des diagnostics Azure 1.5 introduit l'élément **<SinksConfig>** dans la configuration publique. Ceci définit le *récepteur* supplémentaire où les données des diagnostics Azure peuvent être envoyées. Vous pouvez spécifier les détails de la ressource Application Insights où vous souhaitez envoyer les données des diagnostics Azure dans ce **<SinksConfig>**. Par exemple, **SinksConfig** peut ressembler à ceci :

	<SinksConfig>
        <Sink name="ApplicationInsights">
          <ApplicationInsights>{Insert InstrumentationKey}</ApplicationInsights>
          <Channels>
            <Channel logLevel="Error" name="MyTopDiagData"  />
            <Channel logLevel="Verbose" name="MyLogData"  />
          </Channels>
        </Sink>
      </SinksConfig>

Pour l'élément **Récepteur**, l'attribut *name* spécifie une valeur de chaîne qui servira de référence unique au récepteur. L'élément **ApplicationInsights** spécifie la clé d'instrumentation de la ressource Application Insights où les données des diagnostics Azure seront envoyées. Si vous ne disposez pas d'une ressource Application Insights, consultez [Créer une ressource Application Insights](./application-insights/app-insights-create-new-resource.md) pour plus d'informations sur la création d'une ressource et l'obtention de la clé d'instrumentation.

Si vous développez un projet de service cloud avec le Kit de développement logiciel (SDK) Azure 2.8, cette clé d'instrumentation est automatiquement propagée dans la configuration publique basée sur le paramètre de configuration de service **APPINSIGHTS\_INSTRUMENTATIONKEY** lors de l'empaquetage du projet de service cloud. Consultez [Utiliser Application Insights avec les diagnostics Azure pour résoudre les problèmes de service cloud](./cloud-services/cloud-services-dotnet-diagnostics-applicationinsights.md).

L'élément **Canaux** vous permet de définir un ou plusieurs éléments **Canal** pour les données qui seront envoyées au récepteur. Le canal fonctionne comme un filtre et vous permet de sélectionner les niveaux de consignation que vous souhaitez envoyer au récepteur. Par exemple, vous pouvez collecter des journaux détaillés et les envoyer au système de stockage. Vous pouvez également choisir de définir un canal avec le niveau de consignation Erreur : lorsque vous envoyez des journaux à travers ce canal, seuls les journaux des erreurs seront envoyés à ce récepteur. Pour un **Canal**, l'attribut *name* sert de référence unique à ce canal. L'attribut *loglevel* vous permet de spécifier le niveau de consignation autorisé par le canal. Les niveaux de consignation disponibles sont, du plus informatif au moins informatif :
 - Détaillé
 - Information
 - Avertissement
 - Erreur
 - Critique

## Envoyer des données au récepteur Application Insights
Une fois le récepteur Application Insights défini, vous pouvez envoyer des données à ce récepteur en ajoutant l'attribut *sink* aux éléments sous le nœud **DiagnosticMonitorConfiguration**. En ajoutant l'élément *sinks* à chaque nœud, vous spécifiez que vous voulez envoyer au récepteur spécifié les données collectées à partir de ce nœud et des nœuds inférieurs.

Par exemple, si vous souhaitez envoyer toutes les données collectées par les diagnostics Azure, vous pouvez ajouter directement l'attribut *sink* au nœud **DiagnosticMonitorConfiguration**. Donnez à *sinks* la valeur du nom du récepteur spécifié dans **SinkConfig**.

	<DiagnosticMonitorConfiguration overallQuotaInMB="4096" sinks="ApplicationInsights">

Si vous souhaitez envoyer uniquement les journaux des erreurs au récepteur Application Insights, vous pouvez donner à *sinks* la valeur du nom du récepteur, suivi du nom du canal, séparés par un point (« . »). Par exemple, pour envoyer uniquement les journaux des erreurs au récepteur Application Insights, utilisez le canal MyTopDiagdata précédemment défini dans SinksConfig.

	<DiagnosticMonitorConfiguration overallQuotaInMB="4096" sinks="ApplicationInsights.MyTopDiagdata">

Si vous souhaitez envoyer uniquement les journaux d'applications détaillés à Application Insights, vous devez ajouter l'attribut *sinks* au nœud **Logs**.

	<Logs scheduledTransferPeriod="PT1M" scheduledTransferLogLevelFilter="Verbose" sinks="ApplicationInsights.MyLogData"/>

Vous pouvez également inclure plusieurs récepteurs à la configuration à différents niveaux de la hiérarchie. Dans ce cas, le récepteur spécifié au niveau supérieur de la hiérarchie fonctionne comme un paramètre global et celui spécifié au niveau des éléments écrase ce paramètre global.

Voici un exemple complet de fichier de configuration publique qui envoie toutes les erreurs à Application Insights (spécifié au nœud **DiagnosticMonitorConfiguration**), ainsi que les journaux de niveau détaillé des journaux d'applications (spécifié au nœud **Logs**).

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
                sinks="ApplicationInsights.MyLogData"/> <!-- This specific info sent to this channel -->
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

![Configuration publique des diagnostics](./media/azure-diagnostics-configure-applicationinsights/diagnostics-publicconfig.png)

Il existe certaines limitations à connaître pour cette fonctionnalité

- Les canaux sont uniquement destinés à fonctionner avec un type journal et non avec des compteurs de performances. Si vous spécifiez un canal comprenant un élément compteur de performances, il sera ignoré.
- Le niveau de consignation d'un canal ne peut pas dépasser le niveau de consignation de ce qui est collecté par les diagnostics Azure. Par exemple, vous ne pouvez pas collecter les erreurs de consignation des applications dans l’élément Journaux et tenter d’envoyer des journaux détaillés vers le journal de suivi Application Insight. L'attribut *scheduledTransferLogLevelFilter* doit toujours collecter au moins autant de journaux que ceux que vous essayez d'envoyer à un récepteur.
- Vous ne pouvez pas envoyer des données blob collectées par l'extension des diagnostics Azure à Application Insights. Par exemple, rien qui soit spécifié sous le nœud *Directories*. Pour les vidages sur incident, le vidage sur incident réel sera toujours envoyé au stockage d'objets blob et seule une notification de génération du vidage sur incident sera envoyée à Application Insights.


## Étapes suivantes

- Utilisez [PowerShell](./cloud-services/cloud-services-diagnostics-powershell.md) pour activer l'extension des diagnostics Azure pour votre application. 
- Utilisez [Visual Studio](vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines.md) pour activer l'extension des diagnostics Azure pour votre application

<!---HONumber=AcomDC_0413_2016-->