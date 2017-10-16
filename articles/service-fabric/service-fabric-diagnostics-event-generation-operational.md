---
title: "Canal opérationnel Azure Service Fabric | Microsoft Docs"
description: "Liste complète des journaux générés dans le canal opérationnel des clusters Azure Service Fabric."
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/19/2017
ms.author: dekapur
ms.openlocfilehash: 934719868ab9968db352db2b440014d35dbc0274
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="operational-channel"></a>Canal opérationnel 

Le canal opérationnel se compose des journaux provenant des principales actions effectuées par Service Fabric sur vos nœuds et votre cluster. Lorsque l’option « Diagnostics » est activée pour un cluster, l’agent Azure Diagnostics est déployé sur votre cluster et il est par défaut configuré pour lire les journaux à partir du canal opérationnel. Pour savoir comment modifier la configuration des diagnostics de votre cluster afin de sélectionner plus de journaux ou de compteurs de performances, consultez [Agent Azure Diagnostics](service-fabric-diagnostics-event-aggregation-wad.md). 

## <a name="operational-channel-logs"></a>Journaux du canal opérationnel 

Voici une liste complète des journaux fournis par Service Fabric dans le canal opérationnel. 

| EventId | Nom | Source (tâche) | Niveau |
| --- | --- | --- | --- |
| 25620 | NodeOpening | FabricNode | Informations |
| 25621 | NodeOpenedSuccess | FabricNode | Informations |
| 25622 | NodeOpenedFailed | FabricNode | Informations |
| 25623 | NodeClosing | FabricNode | Informations |
| 25624 | NodeClosed | FabricNode | Informations |
| 25625 | NodeAborting | FabricNode | Informations |
| 25626 | NodeAborted | FabricNode | Informations |
| 29627 | ClusterUpgradeStart | CM | Informations |
| 29628 | ClusterUpgradeComplete | CM | Informations |
| 29629 | ClusterUpgradeRollback | CM | Informations |
| 29630 | ClusterUpgradeRollbackComplete | CM | Informations |
| 29631 | ClusterUpgradeDomainComplete | CM | Informations |
| 23074 | ContainerActivated | Hébergement | Informations |
| 23075 | ContainerDeactivated | Hébergement | Informations |
| 29620 | ApplicationCreated | CM | Informations |
| 29621 | ApplicationUpgradeStart | CM | Informations |
| 29622 | ApplicationUpgradeComplete | CM | Informations |
| 29623 | ApplicationUpgradeRollback | CM | Informations |
| 29624 | ApplicationUpgradeRollbackComplete | CM | Informations |
| 29625 | ApplicationDeleted | CM | Informations |
| 29626 | ApplicationUpgradeDomainComplete | CM | Informations |
| 18566 | ServiceCreated | FM | Informations |
| 18567 | ServiceDeleted | FM | Informations |

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur la [génération d’événements au niveau de la plateforme](service-fabric-diagnostics-event-generation-infra.md) dans Service Fabric
* Modification de votre configuration [Azure Diagnostics](service-fabric-diagnostics-event-aggregation-wad.md) pour collecter davantage de journaux
* [Configuration d’Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md) pour voir les journaux de votre canal opérationnel
