---
title: "Résoudre les problèmes de services cloud à l’aide d’Application Insights | Microsoft Docs"
description: "Découvrez comment résoudre les problèmes de service cloud en utilisant Application Insights pour traiter les données des diagnostics Azure."
services: cloud-services
documentationcenter: .net
author: sbtron
manager: timlt
editor: tysonn
ms.assetid: e93f387b-ef29-4731-ae41-0676722accb6
ms.service: cloud-services
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/15/2015
ms.author: saurabh
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: d6e668f5ceffc6e78ac19f83b6022118d5abbb55


---
# <a name="troubleshoot-cloud-services-using-application-insights"></a>Résoudre les problèmes de Cloud Services à l’aide d’Application Insights
Avec le [Kit de développement logiciel (SDK) Azure 2.8](https://azure.microsoft.com/downloads/) et l'extension des diagnostics Azure 1.5, vous pouvez désormais envoyer directement à Application Insights les données des diagnostics Azure de votre service cloud. Les différents types de journaux collectés par les diagnostics Azure, notamment les journaux des applications, les journaux des événements Windows, les journaux ETW et les compteurs de performances, peuvent maintenant être envoyés à Application Insights et affichés dans l'interface utilisateur du portail Application Insights. Si vous utilisez également le Kit de développement logiciel (SDK) Application Insights, vous pouvez désormais obtenir des analyses des métriques et des journaux provenant de votre application, ainsi que les données de niveau système et infrastructure provenant des diagnostics Azure.

## <a name="configure-azure-diagnostics-to-send-data-to-application-insights"></a>Configurer les diagnostics Azure pour envoyer des données à Application Insights
Suivez ces étapes pour configurer votre projet de service cloud afin qu'il envoie des données des diagnostics Azure à Application Insights.

1) Dans l’Explorateur de solutions Visual Studio, cliquez avec le bouton droit sur un rôle, puis sélectionnez **Propriétés** pour ouvrir le Concepteur de rôle

![Propriétés de rôle de l'Explorateur de solutions][1]

2) Dans le Concepteur de rôle, sous la section des diagnostics, cochez la case **Envoyer les données des diagnostics à Application Insights**

![Le Concepteur de rôle envoie les données des diagnostics à Application Insights][2]

3) Dans la boîte de dialogue qui s’affiche, sélectionnez la ressource Application Insights à laquelle vous souhaitez envoyer les données des diagnostics Azure. La boîte de dialogue vous permet de sélectionner une ressource Application Insights dans votre abonnement ou de spécifier manuellement la clé d'instrumentation d'une ressource Application Insights. Si vous ne disposez pas d'une ressource Application Insights, vous pouvez en créer une en cliquant sur le lien **Créer une ressource** , qui ouvre une fenêtre du navigateur sur le portail Azure, Classic dans lequel vous pouvez créer une ressource Application Insights. Pour plus d'informations sur la création d'une ressource Application Insights, consultez [Créer une ressource Application Insights](../application-insights/app-insights-create-new-resource.md)

![sélectionner une ressource Application Insights][3]

4) Une fois que vous avez ajouté la ressource Application Insights, la clé d’instrumentation de la ressource est stockée comme paramètre de configuration de service avec le nom **APPINSIGHTS_INSTRUMENTATIONKEY**. Vous pouvez modifier ce paramètre de configuration pour chaque configuration de service ou environnement en sélectionnant une autre configuration dans la liste déroulante Configuration de service et en spécifiant une nouvelle clé d'instrumentation pour cette configuration.

![sélectionner une configuration de service][4]

Le paramètre de configuration **APPINSIGHTS_INSTRUMENTATIONKEY** est utilisé par Visual Studio pour configurer l’extension des diagnostics avec les informations appropriées de la ressource Application Insights lors de la publication. Le paramètre de configuration est un moyen pratique de définir une clé d'instrumentation différente pour chaque configuration de service. Visual Studio traduira ce paramètre et l'insérera dans la configuration publique de l'extension des diagnostics lors de la publication. Pour simplifier le processus de configuration de l'extension des diagnostics avec PowerShell, la sortie du package à partir de Visual Studio contient également le fichier XML de configuration publique comprenant la clé d'instrumentation Application Insights appropriée. Les fichiers de configuration publique sont créés dans le dossier Extensions et suivent le modèle PaaSDiagnostics.<RoleName>.PubConfig.xml. Tout déploiement basé sur PowerShell peut utiliser ce modèle pour mapper chaque configuration à un rôle.

5) L’activation de l’option **Envoyer les données des diagnostics à Application Insights** configure automatiquement les diagnostics Azure de sorte qu’ils envoient à Application Insights tous les compteurs de performances et les journaux de niveau de consignation Erreur collectés par l’agent des diagnostics Azure. Si vous souhaitez configurer plus précisément les données envoyées à Application Insights, vous devez modifier manuellement le fichier *diagnostics.wadcfgx* pour chaque rôle. Consultez [Configurer les diagnostics Azure pour envoyer des données à Application Insights](../azure-diagnostics-configure-applicationinsights.md) pour en savoir plus sur la mise à jour manuelle de la configuration.

Une fois le service cloud configuré pour envoyer les données des diagnostics Azure à Application Insights, vous pouvez le déployer sur Azure normalement, en veillant à ce que l'extension des diagnostics Azure soit activée. Consultez [Publication d'un service cloud avec Visual Studio](../vs-azure-tools-publishing-a-cloud-service.md).  

## <a name="viewing-azure-diagnostics-data-in-application-insights"></a>Affichage des données des diagnostics Azure dans Application Insights
La télémétrie des diagnostics Azure s'affichera dans la ressource Application Insights configurée pour votre service cloud.

Voici comment les différents types de diagnostics Azure sont mappés aux concepts Application Insights :  

* Les compteurs de performances s'affichent comme mesures personnalisées dans Application Insights
* Les journaux des événements Windows s'affichent comme traces et événements personnalisés dans Application Insights
* Les journaux des applications, les journaux ETW et tous les journaux d'infrastructure des diagnostics s'affichent comme traces dans Application Insights.

Pour afficher les données des diagnostics Azure dans Application Insights :

* Utilisez [Metrics Explorer](../application-insights/app-insights-metrics-explorer.md) pour visualiser les éventuels compteurs de performances personnalisés ou le nombre de types différents d'événements du journal des événements Windows.

![Métriques personnalisées dans Metrics Explorer][5]

* Utilisez [Search](../application-insights/app-insights-diagnostic-search.md) pour effectuer des recherches dans les différents journaux de suivi envoyés par Azure Diagnostics. Par exemple, si une exception non prise en charge dans un rôle a provoqué un incident et le recyclage du rôle, cette information s’affiche dans le canal *Application* du *Journal des événements Windows*. Vous pouvez utiliser la fonctionnalité de recherche pour rechercher l'erreur dans le journal des événements Windows et obtenir l'arborescence complète des appels de procédure de l'exception, ce qui vous permet de trouver la cause racine du problème.

![Rechercher dans les traces][6]

## <a name="next-steps"></a>Étapes suivantes
* [Ajouter le Kit de développement logiciel (SDK) Application Insights à votre service cloud](../application-insights/app-insights-cloudservices.md) pour envoyer des données sur les demandes, les exceptions, les dépendances et la télémétrie personnalisée à partir de votre application. Avec les données des diagnostics Azure, vous pouvez obtenir une vue complète de votre application et du système dans la même ressource Application Insight.  

<!--Image references-->
[1]: ./media/cloud-services-dotnet-diagnostics-applicationinsights/solution-explorer-properties.png
[2]: ./media/cloud-services-dotnet-diagnostics-applicationinsights/role-designer-sendtoappinsights.png
[3]: ./media/cloud-services-dotnet-diagnostics-applicationinsights/select-appinsights-resource.png
[4]: ./media/cloud-services-dotnet-diagnostics-applicationinsights/role-designer-appinsights-serviceconfig.png
[5]: ./media/cloud-services-dotnet-diagnostics-applicationinsights/metrics-explorer-custom-metrics.png
[6]: ./media/cloud-services-dotnet-diagnostics-applicationinsights/search-windowseventlog-error.png



<!--HONumber=Nov16_HO3-->


