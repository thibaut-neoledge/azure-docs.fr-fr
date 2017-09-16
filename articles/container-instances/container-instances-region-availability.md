---
title: "Disponibilité des ressources et des régions pour Azure Container Instances | Azure Docs"
description: "Découvrez les régions Azure qui prennent en charge le déploiement d’instances de conteneur et les limites de processeur et de mémoire pour ces instances."
services: container-instances
documentationcenter: 
author: mmacy
manager: timlt
editor: 
tags: 
keywords: 
ms.assetid: 
ms.service: container-instances
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/31/2017
ms.author: marsma
ms.custom: 
ms.translationtype: HT
ms.sourcegitcommit: 3eb68cba15e89c455d7d33be1ec0bf596df5f3b7
ms.openlocfilehash: 2b9b1b864bbfd73383759212dd7d91f8e4941544
ms.contentlocale: fr-fr
ms.lasthandoff: 09/01/2017

---

# <a name="region-availability-for-azure-container-instances"></a>Disponibilité des régions pour Azure Container Instances

En préversion, Azure Container Instances est disponible dans les régions suivantes avec les limites de processeur et de mémoire spécifiées.

| Lieu | SE | UC | Mémoire (Go) |
| -------- | -- | :---: | :-----------: |
| Europe de l’Ouest, Ouest des États-Unis, Est des États-Unis | Linux | 2 | 7 |
| Europe de l’Ouest, Ouest des États-Unis, Est des États-Unis | Windows | 2 | 3,5 |

## <a name="resource-availability"></a>Disponibilité des ressources

Les instances de conteneur créées dans les limites de ces ressources sont soumises à la disponibilité dans la région de déploiement. Quand une région a une charge importante, vous pouvez rencontrer un échec durant le déploiement des instances.

Pour atténuer ce type d’échec de déploiement, essayez de déployer des instances avec des paramètres de mémoire et de processeur inférieurs, ou essayez d’effectuer le déploiement plus tard.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur la résolution des problèmes de déploiement d’instances de conteneur, consultez [Résoudre les problèmes de déploiement avec Azure Container Instances](container-instances-troubleshooting.md).
