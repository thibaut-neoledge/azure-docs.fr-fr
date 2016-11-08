---
title: Application Insights :exemples de code et procédures pas à pas
description: Des exemples que vous pouvez adapter pour vos propres applications.
services: application-insights
documentationcenter: windows
author: alancameronwills
manager: douge

ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 01/05/2016
ms.author: awills

---
# Application Insights :exemples de code et procédures pas à pas
*Application Insights est à l'état de version préliminaire.*

Il s’agit d’une compilation d’exemples de code qui vous montrent comment utiliser [Visual Studio Application Insights](app-insights-overview.md).

## SDK Labs
[SDK Labs](https://www.myget.org/gallery/applicationinsights-sdk-labs) est un groupe de packages NuGet expérimentaux que vous pouvez installer et désinstaller sous forme d’ajouts à votre Kit de développement logiciel (SDK) Application Insights. Essayez-les et envoyez-nous vos commentaires !

## Services web
* [Ajoutez la télémétrie aux rôles de travail et web Azure](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService).
* [Application universelle Windows 8.1](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/Windows%208.1%20Universal/)

## Exportation continue
Pour analyser les données de télémétrie avec vos propres outils, [exportez-les](app-insights-export-telemetry.md) vers un emplacement de stockage, où vous pouvez les analyser et les traiter.

* [Exporter vers Power BI](app-insights-export-power-bi.md) 
* [Analyser des données exportées à l’aide d’un rôle de travail](app-insights-code-sample-export-telemetry-sql-database.md)
* [Exporter vers SQL à l’aide de Stream Analytics](app-insights-code-sample-export-sql-stream-analytics.md)
* [Activation de la télémétrie pour Microsoft Dynamics CRM](app-insights-sample-mscrm.md)

## Automatiser des tâches
* [Utilisation de PowerShell pour l’envoi de diagnostics Azure vers Application Insights](app-insights-powershell.md)
* [Script de création d’une nouvelle ressource Application Insights](app-insights-powershell-script-create-resource.md)
* [Utilisation de PowerShell pour l’envoi de diagnostics Azure vers Application Insights](app-insights-powershell-azure-diagnostics.md)

<!---HONumber=AcomDC_0107_2016-->