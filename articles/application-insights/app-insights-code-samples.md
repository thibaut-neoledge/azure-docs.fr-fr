---
title: "Application Insights :exemples de code et procédures pas à pas"
description: Des exemples que vous pouvez adapter pour vos propres applications.
services: application-insights
documentationcenter: windows
author: alancameronwills
manager: douge
ms.assetid: 586d0fb2-5286-449b-b327-5c9493632fc1
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 01/05/2016
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: d84ab993b1d9489ca9d2edaa1cb9672d9bced899
ms.openlocfilehash: 2a9cb8062e1dc505706c285269bbbd1eeab72064


---
# <a name="application-insights-code-samples-and-walkthroughs"></a>Application Insights :exemples de code et procédures pas à pas


Il s’agit d’une compilation d’exemples de code qui vous montrent comment utiliser [Azure Application Insights](app-insights-overview.md).

## <a name="sdk-labs"></a>SDK Labs
[SDK Labs](https://www.myget.org/gallery/applicationinsights-sdk-labs) est un groupe de packages NuGet expérimentaux que vous pouvez installer et désinstaller sous forme d’ajouts à votre Kit de développement logiciel (SDK) Application Insights. Essayez-les et envoyez-nous vos commentaires !

## <a name="web-services"></a>Services web
* [Ajoutez la télémétrie aux rôles de travail et web Azure](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService).
* [Application universelle Windows 8.1](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/Windows%208.1%20Universal/)

## <a name="continuous-export"></a>Exportation continue
Pour analyser les données de télémétrie avec vos propres outils, [exportez-les](app-insights-export-telemetry.md) vers un emplacement de stockage, où vous pouvez les analyser et les traiter.

* [Exporter vers Power BI](app-insights-export-power-bi.md) 
* [Analyser des données exportées à l’aide d’un rôle de travail](app-insights-code-sample-export-telemetry-sql-database.md)
* [Exporter vers SQL à l’aide de Stream Analytics](app-insights-code-sample-export-sql-stream-analytics.md)
* [Activation de la télémétrie pour Microsoft Dynamics CRM](app-insights-sample-mscrm.md)

## <a name="automate-tasks"></a>Automatiser des tâches
* [Utilisation de PowerShell pour l’envoi de diagnostics Azure vers Application Insights](app-insights-powershell.md)
* [Script de création d’une nouvelle ressource Application Insights](app-insights-powershell-script-create-resource.md)
* [Utilisation de PowerShell pour l’envoi de diagnostics Azure vers Application Insights](app-insights-powershell-azure-diagnostics.md)




<!--HONumber=Nov16_HO3-->


