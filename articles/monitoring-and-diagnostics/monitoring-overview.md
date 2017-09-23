---
title: Surveillance dans Microsoft Azure | Microsoft Docs
description: Les choix lorsque vous souhaitez analyser quelque chose dans Microsoft Azure. Azure Monitor, Application Insights Log Analytics
author: rboucher
manager: carmonm
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 1b962c74-8d36-4778-b816-a893f738f92d
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/12/2017
ms.author: robb
ms.translationtype: Human Translation
ms.sourcegitcommit: 5e92b1b234e4ceea5e0dd5d09ab3203c4a86f633
ms.openlocfilehash: d4a94a92585420cf92018084437422fd0c66fa2d
ms.contentlocale: fr-fr
ms.lasthandoff: 05/10/2017

---

# <a name="overview-of-monitoring-in-microsoft-azure"></a>Vue d’ensemble de l’analyse dans Microsoft Azure
Cet article fournit une vue d’ensemble des outils disponibles pour la surveillance de Microsoft Azure. S’applique à 
- surveillance des applications en cours d’exécution dans Microsoft Azure 
- outils/services s’exécutant en dehors d’Azure pouvant surveiller des objets dans Azure. 

Aborde les différents produits et services disponibles et leur fonctionnement ensemble. Il peut vous aider à déterminer les outils les plus appropriés pour vous dans différents cas.  

## <a name="why-use-monitoring-and-diagnostics"></a>Pourquoi utiliser la surveillance et les diagnostics ?

Les problèmes de performances dans votre application cloud peuvent affecter votre entreprise. Avec plusieurs composants interconnectés et de nouvelles versions fréquentes, des dégradations peuvent se produire à tout moment. Et si vous développez une application, vos utilisateurs découvrent généralement des problèmes que vous n’avez pas trouvés dans le test. Vous devez prendre connaissance de ces problèmes immédiatement et disposer d’outils pour diagnostiquer et résoudre les problèmes. Microsoft Azure a une gamme d’outils permettant d’identifier ces problèmes.

## <a name="how-do-i-monitor-my-azure-cloud-apps"></a>Comment surveiller mes applications cloud Azure ?

Il existe une gamme d’outils pour la surveillance des services et applications Azure. Certaines de leurs fonctionnalités se chevauchent. C’est en partie pour des raisons historiques et en partie en raison du flou entre le développement et le fonctionnement d’une application. 

Voici les outils principaux :

-   **Azure Monitor** est l’outil de base pour la surveillance des services s’exécutant sur Azure. Il vous fournit des données au niveau de l’infrastructure sur le débit d’un service et l’environnement. Si vous gérez toutes vos applications dans Azure et décidez d’augmenter ou de diminuer les ressources, Azure Monitor vous donne le nécessaire pour démarrer.

-   **Application Insights** peut être utilisé pour le développement et comme solution de surveillance de la production. Il fonctionne en installant un package dans votre application et vous donne donc une vue plus interne de ce qui se passe. Ses données comprennent les temps de réponse des dépendances, les traces des exceptions, le débogage des captures instantanées et les profils d’exécution. Il offre de puissants outils intelligents pour analyser toutes ces données de télémétrie afin de vous aider à déboguer une application et vous aider à comprendre ce que les utilisateurs font avec. Vous pouvez savoir si un pic dans les temps de réponse est causé par un élément dans une application, ou un problème de ressource externe. Si vous utilisez Visual Studio et que l’application est en cause, vous pouvez accéder directement aux lignes de code problématiques pour les corriger.  

-   **Log Analytics** est destiné à ceux qui souhaitent optimiser les performances et planifier la maintenance des applications exécutées en production. Il est basé dans Azure. Il collecte et agrège les données provenant de nombreuses sources, mais avec un délai de 10 à 15 minutes. Il fournit une solution de gestion informatique globale holistique pour Azure, en local et dans les infrastructures cloud tierces (par exemple, Amazon Web Services). Il fournit des outils plus riches pour analyser les données de plusieurs sources, en acceptant des requêtes complexes sur tous les journaux et en pouvant générer des alertes de façon proactive pour des conditions spécifiées.  Vous pouvez même collecter des données personnalisées dans son référentiel central pour les interroger et visualiser. 

-   **System Center Operations Manager (SCOM)** pour gérer et surveiller les installations cloud à grande échelle. Vous le connaissez peut-être déjà comme outil de gestion local pour Windows Server en local et les solutions cloud basées sur Hyper-V, mais il peut également s’intégrer à et gérer des applications Azure. Entre autres choses, il peut installer Application Insights sur des applications en direct existantes.  Si une application est en panne, vous en êtes informé sous quelques secondes. Notez que Log Analytics ne remplace pas SCOM. Les deux fonctionnent bien ensemble.  


## <a name="accessing-monitoring-in-the-azure-portal"></a>Accès à la surveillance dans le portail Azure
Tous les services de surveillance Azure sont maintenant disponibles dans un seul volet de l’interface utilisateur. Pour en savoir plus sur l’accès à cette zone, consultez [Prise en main d’Azure Monitor](monitoring-get-started.md). 

Vous pouvez également accéder à des fonctions de surveillance pour des ressources spécifiques en mettant en surbrillance ces ressources et en explorant leurs options de surveillance. 

## <a name="examples-of-when-to-use-which-tool"></a>Exemples d’utilisation de l’outil 

Les sections suivantes montrent quelques scénarios de base et outils devant être utilisés ensemble. 

### <a name="scenario-1--fix-errors-in-an-azure-application-under-development"></a>Scénario 1 : correction des erreurs dans une application Azure en développement   

**La meilleure option consiste à utiliser conjointement Application Insights, Azure Monitor et Visual Studio**

Azure offre désormais toute la puissance du débogueur Visual Studio dans le cloud. Configurez Azure Monitor pour envoyer la télémétrie à Application Insights. Permettez à Visual Studio d’inclure le kit de développement logiciel Application Insights dans votre application. Une fois dans Application Insights, vous pouvez utiliser la mise en correspondance d’applications pour découvrir visuellement les parties de votre application en cours d’exécution qui sont intègres et celles qui ne le sont pas. Corrigez les parties qui ne sont pas intègres, les erreurs et les exceptions déjà disponibles pour exploration. Vous pouvez utiliser les diverses analyses dans Application Insights pour approfondir la question. Si vous n’êtes pas sûr de l’erreur, vous pouvez utiliser le débogueur de Visual Studio pour effectuer le suivi dans le code et identifier plus précisément un problème. 

Pour plus d’informations, consultez [Surveillance d’applications web](../application-insights/app-insights-azure-web-apps.md) et reportez-vous à la table des matières sur la gauche pour obtenir des instructions sur les différents types d’applications et de langages.  

### <a name="scenario-2--debug-an-azure-net-web-application-for-errors-that-only-show-in-production"></a>Scénario 2 : déboguer une application web Azure .NET pour les erreurs qui apparaissent uniquement en production 

> [!NOTE]
> Ces fonctionnalités sont en version préliminaire. 

**La meilleure option consiste à utiliser Application Insights et si possible de Visual Studio pour une expérience de débogage complète.**

Utilisez le débogueur d’instantanés d’Application Insights pour déboguer votre application. Lorsqu’un certain seuil d’erreur se produit avec des composants de production, le système capture automatiquement les données de télémétrie de Windows dans des « instantanés ». La quantité capturée est sûre pour une solution cloud de production, car elle est suffisamment petite pour ne pas affecter les performances, mais suffisamment significative pour permettre le traçage.  Le système peut capturer plusieurs instantanés. Vous pouvez examiner un moment donné dans le portail Azure ou utiliser Visual Studio pour l’expérience complète. Avec Visual Studio, les développeurs peuvent parcourir cet instantané comme s’ils effectuaient le débogage en temps réel. Les paramètres, la mémoire, les variables locales et les trames sont tous disponibles. Les développeurs doivent avoir accès à ces données de production via un rôle RBAC.  

Pour plus d’informations, consultez [Débogage d’instantané](../application-insights/app-insights-snapshot-debugger.md). 

### <a name="scenario-3--debug-an-azure-application-that-uses-containers-or-microservices"></a>Scénario 3 : déboguer une application Windows Azure qui utilise des conteneurs ou microservices 

**Identique au scénario 1. Utilisez Visual Studio, Application Insights et Azure Monitor ensemble** Application Insights prend également en charge la collecte des données de télémétrie des processus qui s’exécutent dans des conteneurs et microservices (Kubernetes, Docker, Azure Service Fabric). Pour plus d’informations, [regardez cette vidéo sur le débogage des conteneurs et microservices](https://go.microsoft.com/fwlink/?linkid=848184). 


### <a name="scenario-4--fix-performance-issues-in-your-azure-application"></a>Scénario 4 : corriger les problèmes de performances dans votre application Azure

Le [profileur d’Application Insights](../application-insights/app-insights-profiler.md) est conçu pour vous aider à résoudre ces types de problèmes. Vous pouvez identifier et résoudre les problèmes de performances pour les applications qui s’exécutent dans App Service (applications web, applications logiques, applications mobiles, applications d’API) et d’autres ressources de calcul comme les machines virtuelles, les jeux de mise à l’échelle de machines virtuelles, les services de cloud computing et Service Fabric. 

> [!NOTE]
> La fonctionnalité de profilage de machines virtuelles, de jeux de mise à l’échelle de machines virtuelles, de services cloud et de Service Fabric est en version préliminaire.   

De plus, vous êtes proactivement informé par courrier électronique de certains types d’erreurs, comme les temps de chargement de page lents, par l’outil de détection intelligente.  Vous n’avez pas besoin d’effectuer de configuration pour cet outil. Pour plus d’informations, consultez [Détection intelligente - anomalies de performances](../application-insights/app-insights-proactive-performance-diagnostics.md) et [Détection intelligente - anomalies de performances](https://azure.microsoft.com/blog/Enhancments-ApplicationInsights-SmartDetection/preview).



## <a name="next-steps"></a>Étapes suivantes
En savoir plus sur

* [Azure Monitor dans une vidéo de l’Ignite 2016](https://myignite.microsoft.com/videos/4977)
* [Prise en main d’Azure Monitor](monitoring-get-started.md)
* [Azure Diagnostic](../azure-diagnostics.md) si vous tentez de diagnostiquer des problèmes dans votre service cloud, machine virtuelle, jeux de mise à l’échelle de machine virtuelle ou application Service Fabric.
* [Application Insights](https://azure.microsoft.com/documentation/services/application-insights/) si vous essayez de diagnostiquer des problèmes dans votre application web App Service.
* [Log Analytics](https://azure.microsoft.com/documentation/services/log-analytics/) et la solution de surveillance de production [Operations Management Suite](https://www.microsoft.com/oms/)
