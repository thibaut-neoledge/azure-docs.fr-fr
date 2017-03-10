---
title: "Vue d’ensemble d’Azure Monitor | Microsoft Docs"
description: "Azure Monitor collecte des statistiques qui peuvent être utilisées dans les alertes, les webhooks, la mise à l’échelle automatique et l’automatisation. L’article liste également les autres options de surveillance de Microsoft."
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
ms.date: 02/01/2017
ms.author: robb
translationtype: Human Translation
ms.sourcegitcommit: 3693b90509646fd7292861979cd7c2d1c0100d68
ms.openlocfilehash: 30e92d17a99a0c751397e448108d488aefdd557a
ms.lasthandoff: 02/22/2017


---

# <a name="overview-of-azure-monitor"></a>Vue d’ensemble d’Azure Monitor
Cet article fournit une vue d’ensemble conceptuelle de la surveillance des ressources Azure. Il propose des références vers des informations sur des types de ressources spécifiques.  Pour obtenir des informations générales sur l’analyse de votre application depuis une autre plate-forme qu’Azure, consultez [Guide de surveillance et de diagnostic](../best-practices-monitoring.md).

Une vidéo de procédure pas à pas d’Azure Monitor est disponible à l’adresse  
[Prise en main d’Azure Monitor](https://channel9.msdn.com/Blogs/Azure-Monitoring/Get-Started-with-Azure-Monitor). Une vidéo supplémentaire décrivant un scénario dans lequel vous pouvez utiliser Azure Monitor est disponible sur [Découvrir Microsoft Azure Monitoring et Diagnostic](https://channel9.msdn.com/events/Ignite/2016/BRK2234).  

Les applications cloud sont complexes, et se composent de nombreux éléments mobiles. L’analyse fournit des données visant à garantir que votre application reste opérationnelle et soit exécutée en toute intégrité. Elle vous permet également de parer à des problèmes potentiels ou de résoudre des problèmes déjà survenus. En outre, vous pouvez utiliser les données d’analyse pour obtenir des informations détaillées sur votre application. Ces connaissances peuvent vous aider à améliorer les performances de l’application ou sa facilité de gestion, ou à automatiser des actions qui exigeraient normalement une intervention manuelle.

Le diagramme suivant présente une vue conceptuelle de l’analyse Azure, notamment le type de journaux que vous pouvez collecter et ce que vous pouvez faire avec ces données.   

![Modèle logique pour l’analyse et le diagnostic pour les ressources non liées au calcul](./media/monitoring-overview/MonitoringAzureResources-non-compute_v3.png)

Figure 1 : modèle conceptuel pour l’analyse et le diagnostic pour les ressources non liées au calcul

<br/>

![Modèle logique pour l’analyse et le diagnostic pour les ressources de calcul](./media/monitoring-overview/MonitoringAzureResources-compute_v3.png)

Figure 2 : modèle conceptuel pour l’analyse et le diagnostic pour les ressources liées au calcul

## <a name="monitoring-sources"></a>Sources d’analyse
### <a name="activity-logs"></a>Journaux d’activité
Vous pouvez rechercher le journal d’activité (précédemment appelé journal opérationnel ou d’audit) pour obtenir des informations sur vos ressources telles qu’elles sont analysées par l’infrastructure Azure. Ce journal contient des informations telles que les dates et heures de création ou de destruction de ressources.  

### <a name="host-vm"></a>Ordinateur virtuel hôte
**Calcul uniquement**

Certaines ressources de calcul, telles que les services cloud, Virtual Machines et Service Fabric disposent d’un ordinateur virtuel hôte dédié avec lequel elles interagissent. L’ordinateur virtuel hôte est l’équivalent de la racine virtuelle dans le modèle d’hyperviseur Hyper-V. Dans ce cas, vous pouvez collecter les métriques sur le seul ordinateur hôte virtuel en plus du système d’exploitation invité.  

Le routage 1:1 entre votre ressource et un ordinateur virtuel spécifique n’est pas appliqué dans tous les services Azure, les mesures de machines virtuelles hôtes ne sont donc pas disponibles.

### <a name="resource---metrics-and-diagnostics-logs"></a>Ressources - Métriques et journaux de diagnostics
Le type de métriques que vous pouvez générer varie selon le type de ressources. Par exemple, les machines virtuelles fournissent des statistiques sur l’E/S disque et le pourcentage UC. Mais ces statistiques n’existent pas pour une file d’attente Service Bus, qui fournit à la place des métriques telles que la taille de la file d’attente et le débit des messages.

Pour les ressources de calcul, vous pouvez obtenir les métriques sur le SE invité, ainsi que des modules de diagnostic tels que Azure Diagnostics. Azure Diagnostics aide à collecter et à router des données de diagnostic vers d’autres emplacements, notamment dans le stockage Azure.

Une liste des métriques actuellement collectables est disponible sur [Mesures prises en charge](monitoring-supported-metrics.md).

### <a name="application---diagnostics-logs-application-logs-and-metrics"></a>Application - Journaux de diagnostic, journaux d’Application et métriques
**Calcul uniquement**

Les applications peuvent fonctionner parallèlement au SE invité dans le modèle de calcul. Elles émettent leur propre ensemble de journaux et de métriques.

Les types de métriques fournis sont les suivants :

* Compteurs de performances
* Journaux d’application
* Journaux des événements Windows
* .NET EventSource
* Journaux IIS
* ETW basé sur les manifestes
* Vidages sur incident
* Journaux d'erreurs client

## <a name="uses-for-monitoring-data"></a>Utilisations de l’analyse de données
### <a name="visualize"></a>Visualisation
En affichant les données analysées sous forme de graphiques et de diagrammes, vous êtes en mesure d’identifier des tendances beaucoup plus rapidement qu’en lisant simplement les données brutes vous-même.  

Il existe quelques méthodes de visualisation, qui sont les suivantes :

* Utilisation du portail Azure
* Routage de données vers Azure Application Insights
* Routage de données vers Microsoft PowerBI
* Routage de données vers un outil de visualisation tiers par streaming en direct ou par lecture de l’outil à partir d’une archive située dans le stockage Azure

### <a name="archive"></a>Archivage
De manière générale, les données analysées sont écrites dans le stockage Azure, où elles sont conservées jusqu’à ce que vous les supprimiez.

Quelques façons d’utiliser ces données :

* Une fois les données écrites, vous pouvez les lire et les traiter à l’aide d’outils internes ou externes à Azure.
* Vous devez télécharger les données localement pour les archiver localement ou modifier votre stratégie de rétention dans le cloud pour les conserver sur une période prolongée.  
* <a name="you-leave-the-data-in-azure-storage-indefinitely-though-you-have-to-pay-for-azure-storage-based-on-the-amount-of-data-you-keep"></a>Vous laissez les données dans Azure Storage indéfiniment, même si vous devez payer pour le stockage Azure sur la base de la quantité de données que vous conservez.
  -

### <a name="query"></a>Interroger
Vous pouvez utiliser l’API REST Azure Monitor, les commandes de l’Interface de ligne de commande interplateforme (CLI), les applets de commande PowerShell ou le Kit de développement logiciel .NET pour accéder aux données présentes dans le système ou dans le stockage Azure

Voici quelques exemples : 

* Obtention de données pour une application d’analyse personnalisée que vous avez écrite
* Création de requêtes personnalisées et envoi de ces données à une application tierce.

### <a name="route"></a>Routage
Vous pouvez diffuser des données d’analyse à d’autres emplacements en temps réel.

Voici quelques exemples :

* Envoi vers Application Insights afin d’en utiliser les outils de visualisation.
* Envoi vers Event Hubs afin de router les outils tiers pour l’exécution d’une analyse en temps réel.

### <a name="automate"></a>Automatisation
Vous pouvez utiliser les données d’analyse pour déclencher des alertes ou encore des processus complets. Voici quelques exemples :

* Utilisation des données pour la mise à l’échelle automatique des instances de calcul, vers le haut ou vers le bas selon le chargement de l’application.
* Envoi de messages électroniques lorsqu’une mesure dépasse un seuil prédéfini.
* Appel d’une URL Web (webhook) afin d’exécuter une action dans un système hors Azure
* Démarrage d’un runbook dans Azure Automation pour l’exécution de tout ensemble de tâches

## <a name="methods-of-use"></a>Méthodes d’utilisation
En général, vous pouvez manipuler le suivi, le routage et la récupération des données à l’aide de l’une des méthodes suivantes. Toutes les méthodes ne sont pas disponibles pour toutes les actions ou tous les types de données.

* [Portail Azure](https://portal.azure.com)
* [PowerShell](insights-powershell-samples.md)  
* [Interface de ligne de commande interplateforme (CLI)](insights-cli-samples.md)
* [API REST](https://msdn.microsoft.com/library/dn931943.aspx)
* [KIT DE DÉVELOPPEMENT LOGICIEL (SDK) .NET](https://msdn.microsoft.com/library/dn802153.aspx)

## <a name="azures-monitoring-offerings"></a>Offres d’analyse Azure
Azure propose des offres pour l’analyse de vos services, depuis les infrastructures de systèmes nus jusqu’à la télémétrie applicative. La meilleure stratégie d’analyse combine l’utilisation des trois pour obtenir une analyse complète et détaillée de l’intégrité de vos services.

* [Azure Monitor](http://aka.ms/azmondocs) – offre des services de visualisation, requête, routage, alertes, mise à l’échelle et automatisation pour les données de l’infrastructure Azure (journal d’activité) et pour chaque ressource Azure (journaux de diagnostic). Cet article fait partie de la documentation Azure Monitor. Le nom Azure Monitor a été publié le 25 septembre lors de l’Ignite 2016.  Le nom précédent était « Azure Insights ».  
* [Application Insights](https://azure.microsoft.com/documentation/services/application-insights/) – fournit une détection et des diagnostics approfondis et bien intégrés par-dessus les données d’Azure Monitoring pour les problèmes survenant au niveau de la couche d’application de votre service. Il s’agit de la plateforme de diagnostic par défaut pour App Service Web Apps.  Vous pouvez y acheminer les données provenant d’autres services.  
* [Log Analytics](https://azure.microsoft.com/documentation/services/log-analytics/), composant [d’Operations Management Suite](https://www.microsoft.com/oms/) – fournit une solution de gestion informatique globale aussi bien pour les infrastructures sur site que pour les solutions tierces basées sur le cloud (comme AWS) en plus des ressources Azure.  Les données d’Azure Monitor peuvent être acheminées directement dans Log Analytics afin de voir les mesures et journaux pour l’ensemble de votre environnement en un seul endroit.     

## <a name="next-steps"></a>Étapes suivantes
En savoir plus sur

* [Azure Monitor dans une vidéo de l’Ignite 2016](https://myignite.microsoft.com/videos/4977)
* [Prise en main d’Azure Monitor](monitoring-get-started.md)
* [Azure Diagnostic](../azure-diagnostics.md) si vous tentez de diagnostiquer des problèmes dans votre application Cloud Service, Virtual Machine ou Service Fabric.
* [Application Insights](https://azure.microsoft.com/documentation/services/application-insights/) si vous essayez de diagnostiquer des problèmes dans votre application web App Service.
* [Résolution des problèmes du stockage Azure](../storage/storage-e2e-troubleshooting.md) lorsque vous utilisez le stockage d’objets blob, de tables ou de files d’attente
* [Log Analytics](https://azure.microsoft.com/documentation/services/log-analytics/) et [Operations Management Suite](https://www.microsoft.com/oms/)

