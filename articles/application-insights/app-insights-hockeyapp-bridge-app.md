---
title: "Exploration des données HockeyApp dans Application Insights | Microsoft Docs"
description: "Analysez l’utilisation et les performances de votre application Azure avec Application Insights."
services: application-insights
documentationcenter: windows
author: alancameronwills
manager: douge
ms.assetid: 97783cc6-67d6-465f-9926-cb9821f4176e
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 08/25/2016
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: 41ce9b0e323c0938b6db98b99d8d687d1ed0f0ef
ms.openlocfilehash: b83f9c59e44348cb23a4786da907f3c6d8530683


---
# <a name="exploring-hockeyapp-data-in-application-insights"></a>Exploration des données HockeyApp dans Application Insights
[HockeyApp](https://azure.microsoft.com/services/hockeyapp/) est la plate-forme recommandée pour l’analyse des applications mobiles et de bureau actives. À partir de HockeyApp, vous pouvez envoyer de la télémétrie de trace et personnalisée pour surveiller l’utilisation et faciliter le diagnostic (en plus de l’obtention de données de panne). Ce flux de données de télémétrie peut être interrogé à l’aide de la puissante fonctionnalité [Analytics](app-insights-analytics.md) [d’Azure Application Insights](app-insights-overview.md). En outre, vous pouvez [exporter la télémétrie personnalisée et de trace](app-insights-export-telemetry.md). Pour activer ces fonctionnalités, vous configurez un pont qui transfère les données HockeyApp vers Application Insights.

## <a name="the-hockeyapp-bridge-app"></a>Application de pont HockeyApp
Cette application est la fonction principale qui vous permet d’accéder à vos données HockeyApp dans Application Insights via les fonctionnalités d’analyse et d’exportation continue. Toutes les données collectées par HockeyApp après la création de l’application HockeyApp Bridge seront accessibles à partir de ces fonctionnalités. Nous allons voir comment configurer une de ces applications de pont.

Dans HockeyApp, ouvrez Paramètres de compte, [Jetons d’API](https://rink.hockeyapp.net/manage/auth_tokens). Créez un nouveau jeton ou réutilisez un jeton existant. Les droits minimaux requis sont « lecture seule ». Effectuez une copie du jeton d’API.

![Obtenir un jeton d’API HockeyApp](./media/app-insights-hockeyapp-bridge-app/01.png)

Ouvrez le portail Microsoft Azure et [créez une ressource Application Insights](app-insights-create-new-resource.md). Définissez la valeur Type d’application sur « Application de pont HockeyApp » :

![Nouvelle ressource Application Insights](./media/app-insights-hockeyapp-bridge-app/02.png)

Vous n’avez pas besoin de définir de nom. Celui-ci sera automatiquement configuré à partir du nom de HockeyApp.

Les champs du pont HockeyApp s’affichent. 

![Renseignez les champs de pont](./media/app-insights-hockeyapp-bridge-app/03.png)

Entrez le jeton HockeyApp que vous avez noté précédemment. Cette action remplit le menu déroulant « Application HockeyApp » avec toutes vos applications HockeyApp. Sélectionnez celle que vous souhaitez utiliser et complétez le reste des champs. 

Ouvrez la nouvelle ressource. 

Notez que les données prennent un certain temps avant de commencer à circuler.

![Ressource Application Insights en attente de données](./media/app-insights-hockeyapp-bridge-app/04.png)

Et voilà ! Toutes les données collectées dans votre application instrumentée HockeyApp à partir de ce point sont désormais également disponibles dans les fonctionnalités d’analyse et d’exportation continue.

Nous allons brièvement passer en revue chacune de ces fonctionnalités, qui sont désormais à votre disposition.

## <a name="analytics"></a>Analyse
Analytique est un outil puissant pour l’interrogation ad hoc de vos données, qui vous permet de diagnostiquer et d’analyser vos données de télémétrie et de déceler rapidement les causes et les modèles.

![Analyse](./media/app-insights-hockeyapp-bridge-app/05.png)

* [En savoir plus sur Analyse](app-insights-analytics-tour.md)
* [Vidéo de présentation](https://channel9.msdn.com/events/Build/2016/T666)
* [Vidéo de concepts avancés](https://channel9.msdn.com/Events/Build/2016/P591)

## <a name="continuous-export"></a>Exportation continue
Exportation continue vous permet d’exporter des données dans un conteneur de stockage Blob Azure. Cela est très utile si vous souhaitez conserver vos données plus longtemps que la période de rétention actuellement proposée par Application Insights. Vous pouvez conserver les données dans le stockage Blob, le traiter dans une base de données SQL ou votre solution d’entrepôt de données préférée.

[En savoir plus sur l’exportation continue](app-insights-export-telemetry.md)

## <a name="next-steps"></a>Étapes suivantes
* [Appliquer Analytics à vos données](app-insights-analytics-tour.md)




<!--HONumber=Nov16_HO3-->


