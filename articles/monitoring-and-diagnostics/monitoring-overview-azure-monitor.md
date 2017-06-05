---
title: "Vue d’ensemble d’Azure Monitor | Microsoft Docs"
description: "Azure Monitor collecte des statistiques qui peuvent être utilisées dans les alertes, les webhooks, la mise à l’échelle automatique et l’automatisation. L’article liste également les autres options de surveillance de Microsoft."
author: rboucher
manager: carmonm
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/12/2017
ms.author: robb
ms.translationtype: Human Translation
ms.sourcegitcommit: 5e92b1b234e4ceea5e0dd5d09ab3203c4a86f633
ms.openlocfilehash: 726a33b5cad8b44c520d6c59d418fec1bbfc6fa5
ms.contentlocale: fr-fr
ms.lasthandoff: 05/10/2017


---

# <a name="overview-of-azure-monitor"></a>Vue d’ensemble d’Azure Monitor
Cet article fournit une vue d’ensemble du service Azure Monitor dans Microsoft Azure. Vous y trouverez ce qu’Azure Monitor fait et des pointeurs vers des informations supplémentaires sur l’utilisation d’Azure Monitor.  Si vous préférez une présentation vidéo, consultez les liens Étapes suivants en bas de cet article. 

## <a name="why-monitor-your-application-or-system"></a>Pourquoi surveiller votre application ou système
Les applications cloud sont complexes, et se composent de nombreux éléments mobiles. L’analyse fournit des données visant à garantir que votre application reste opérationnelle et soit exécutée en toute intégrité. Elle vous permet également de parer à des problèmes potentiels ou de résoudre des problèmes déjà survenus. En outre, vous pouvez utiliser les données d’analyse pour obtenir des informations détaillées sur votre application. Ces connaissances peuvent vous aider à améliorer les performances de l’application ou sa facilité de gestion, ou à automatiser des actions qui exigeraient normalement une intervention manuelle.


## <a name="azure-monitor-and-microsofts-other-monitoring-products"></a>Azure Monitor et autres produits de surveillance de Microsoft
Azure Monitor fournit des métriques de niveau de base d’infrastructure et des journaux pour la plupart des services Microsoft Azure. Les services Azure qui ne placent pas encore leurs données dans Azure Monitor le feront prochainement. 

Microsoft libre des produits et services supplémentaires qui fournissent de nouvelles fonctionnalités de surveillance pour les développeurs, DevOps ou opérateurs informatiques qui ont également des installations locales. Pour une vue d’ensemble et mieux comprendre comment ces différents produits et services fonctionnent ensemble, consultez [Surveillance dans Microsoft Azure](monitoring-overview.md).

## <a name="monitoring-sources---compute"></a>Sources d’analyse - Calcul

![Modèle pour l’analyse et le diagnostic pour les ressources non liées au calcul](./media/monitoring-overview-azure-monitor/Monitoring_Azure_Resources-compute_v6.png)

Les services de calcul comprennent 
- Services cloud 
- Machines virtuelles 
- Jeux de mise à l’échelle de machine virtuelle 
- Service Fabric

### <a name="application---diagnostics-logs-application-logs-and-metrics"></a>Application - Journaux de diagnostic, journaux d’Application et métriques
Les applications peuvent fonctionner parallèlement au SE invité dans le modèle de calcul. Elles émettent leur propre ensemble de journaux et de métriques. Azure Monitor s’appuie sur l’extension de diagnostics Azure pour collecter la plupart des métriques et journaux de niveau application (Windows ou Linux). Les types comprennent

* Compteurs de performances
* Journaux d’application
* Journaux des événements Windows
* .NET EventSource
* Journaux IIS
* ETW basé sur les manifestes
* Vidages sur incident
* Journaux d'erreurs client

Sans l’extension de diagnostics, seules quelques métriques telles que l’utilisation du processeur sont disponibles. 

### <a name="host-and-guest-vm-metrics"></a>Métriques d’hôte et machine virtuelle invitée
Les ressources de calcul précédemment répertoriées ont une machine virtuelle hôte dédiée et un système d’exploitation invité avec lesquels elles interagissent. La machine virtuelle hôte et le SE invité sont l’équivalent de la racine virtuelle et de la machine virtuelle invitée dans le modèle d’hyperviseur Hyper-V. Vous pouvez collecter des métriques sur les deux. Vous pouvez également collecter des journaux de diagnostic sur le système d’exploitation invité.   

### <a name="activity-log"></a>Journal d’activité
Vous pouvez rechercher le journal d’activité (précédemment appelé journal opérationnel ou d’audit) pour obtenir des informations sur vos ressources telles qu’elles sont analysées par l’infrastructure Azure. Ce journal contient des informations telles que les dates et heures de création ou de destruction de ressources.  Pour plus d’informations, voir [Présentation du journal d’activité](monitoring-overview-activity-logs.md). 

## <a name="monitoring-sources---everything-else"></a>Surveillance des sources - Tout le reste

![Modèle pour l’analyse et le diagnostic pour les ressources liées au calcul](./media/monitoring-overview-azure-monitor/Monitoring_Azure_Resources-non-compute_v6.png)


### <a name="resource---metrics-and-diagnostics-logs"></a>Ressources - Métriques et journaux de diagnostics
Les métriques et journaux de diagnostics collectables varient selon le type de ressource. Par exemple, les applications web fournissent des statistiques sur l’E/S disque et le pourcentage UC. Ces métriques n’existent pas pour une file d’attente Service Bus, qui fournit à la place des métriques telles que la taille de la file d’attente et le débit des messages. Une liste des métriques actuellement collectables pour chaque ressource est disponible sur [Mesures prises en charge](monitoring-supported-metrics.md). 

### <a name="host-and-guest-vm-metrics"></a>Métriques d’hôte et machine virtuelle invitée
Le routage 1:1 entre votre ressource et un hôte ou une machine virtuelle invitée spécifique n’est pas systématiquement appliqué, les métriques ne sont donc pas disponibles.

### <a name="activity-log"></a>Journal d’activité
Le journal d’activité est identique à celui des ressources de calcul.  

## <a name="uses-for-monitoring-data"></a>Utilisations de l’analyse de données
Une fois que vous collectez vos données, vous pouvez effectuer les opérations suivantes dessus avec Azure Monitor

### <a name="route"></a>Routage
Vous pouvez diffuser des données d’analyse à d’autres emplacements en temps réel.

Voici quelques exemples :

- Envoi vers Application Insights afin de pouvoir utiliser ses outils de visualisation et d’analyse plus riches.
- Envoi vers Event Hubs afin de router les outils tiers. 

### <a name="store-and-archive"></a>Stockage et archivage
Certaines données de surveillance sont déjà disponibles dans Azure Monitor et enregistrées pendant un laps de temps défini. 
- Les métriques sont stockées pendant 30 jours. 
- Les entrées de journal d’activité sont conservées pendant 90 jours. 
- Les journaux de diagnostic ne sont pas stockés du tout. 

Si vous souhaitez stocker les données plus longtemps que les périodes de temps répertoriées ci-dessus, vous pouvez utiliser un stockage Azure. Les données de surveillance sont conservées dans votre compte de stockage sur la base d’une stratégie de rétention que vous définissez. Vous n’avez pas à payer pour l’espace utilisé par les données dans le stockage Azure. 

Quelques façons d’utiliser ces données :

- Une fois les données écrites, vous pouvez les lire et les traiter à l’aide d’outils internes ou externes à Azure.
- Vous devez télécharger les données localement pour les archiver localement ou modifier votre stratégie de rétention dans le cloud pour les conserver sur une période prolongée.  
- Vous laissez indéfiniment les données dans le stockage Azure à des fins d’archivage. 

### <a name="query"></a>Requête
Vous pouvez utiliser l’API REST Azure Monitor, les commandes de l’Interface de ligne de commande interplateforme (CLI), les applets de commande PowerShell ou le Kit de développement logiciel .NET pour accéder aux données présentes dans le système ou dans le stockage Azure

Voici quelques exemples : 

* Obtention de données pour une application d’analyse personnalisée que vous avez écrite
* Création de requêtes personnalisées et envoi de ces données à une application tierce.

### <a name="visualize"></a>Visualisation
En affichant les données analysées sous forme de graphiques et de diagrammes, vous êtes en mesure d’identifier des tendances plus rapidement qu’en lisant simplement les données brutes vous-même.  

Il existe quelques méthodes de visualisation, qui sont les suivantes :

* Utilisation du portail Azure
* Routage de données vers Azure Application Insights
* Routage de données vers Microsoft PowerBI
* Routage de données vers un outil de visualisation tiers par streaming en direct ou par lecture de l’outil à partir d’une archive située dans le stockage Azure


### <a name="automate"></a>Automatisation
Vous pouvez utiliser les données d’analyse pour déclencher des alertes ou encore des processus complets. Voici quelques exemples :

* Utilisation des données pour la mise à l’échelle automatique des instances de calcul, vers le haut ou vers le bas selon le chargement de l’application.
* Envoi de messages électroniques lorsqu’une mesure dépasse un seuil prédéfini.
* Appel d’une URL Web (webhook) afin d’exécuter une action dans un système hors Azure
* Démarrage d’un runbook dans Azure Automation pour l’exécution de tout ensemble de tâches

## <a name="methods-of-accessing-azure-monitor"></a>Méthodes d’accès à Azure Monitor
En général, vous pouvez manipuler le suivi, le routage et la récupération des données à l’aide de l’une des méthodes suivantes. Toutes les méthodes ne sont pas disponibles pour toutes les actions ou tous les types de données.

* [Portail Azure](https://portal.azure.com)
* [PowerShell](insights-powershell-samples.md)  
* [Interface de ligne de commande interplateforme (CLI)](insights-cli-samples.md)
* [API REST](https://docs.microsoft.com/rest/api/monitor/)
* [Kit SDK .NET](https://msdn.microsoft.com/library/dn802153.aspx)

## <a name="next-steps"></a>Étapes suivantes
En savoir plus sur
- Une vidéo de procédure pas à pas uniquement pour Azure Monitor est disponible à l’adresse  
[Prise en main d’Azure Monitor](https://channel9.msdn.com/Blogs/Azure-Monitoring/Get-Started-with-Azure-Monitor). Une vidéo supplémentaire décrivant un scénario dans lequel vous pouvez utiliser Azure Monitor est disponible sur [Découvrir Microsoft Azure Monitoring et Diagnostic](https://channel9.msdn.com/events/Ignite/2016/BRK2234) et [Azure Monitor dans une vidéo de l’Ignite 2016](https://myignite.microsoft.com/videos/4977)
- Parcourir l’interface Azure Monitor dans [Prise en main d’Azure Monitor](monitoring-get-started.md)
- Configurez les [Extensions Azure Diagnostics](../azure-diagnostics.md) si vous tentez de diagnostiquer des problèmes dans votre service cloud, machine virtuelle, jeux de mise à l’échelle de machine virtuelle ou application Service Fabric.
- [Application Insights](https://azure.microsoft.com/documentation/services/application-insights/) si vous essayez de diagnostiquer des problèmes dans votre application web App Service.
- [Résolution des problèmes du stockage Azure](../storage/storage-e2e-troubleshooting.md) lorsque vous utilisez le stockage d’objets blob, de tables ou de files d’attente
- [Log Analytics](https://azure.microsoft.com/documentation/services/log-analytics/) et [Operations Management Suite](https://www.microsoft.com/oms/)

