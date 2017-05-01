---
title: Tailles des machines virtuelles Linux dans Azure | Microsoft Docs
description: "Répertorie les différentes tailles disponibles pour les machines virtuelles Linux dans Azure."
services: virtual-machines-linux
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager,azure-service-management
ms.assetid: da681171-f045-4c80-a5a9-d8bd47964673
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/22/2017
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: d068cf9b6b2fd9084ffc60cac4837aa378a09c79
ms.lasthandoff: 04/12/2017

---

# <a name="sizes-for-linux-virtual-machines-in-azure"></a>Tailles des machines virtuelles Linux dans Azure
Cet article décrit les tailles et options disponibles pour les machines virtuelles Azure que vous pouvez utiliser pour exécuter vos applications et charges de travail Linux. Il expose également les points à prendre en considération pour le déploiement quand vous planifiez l’utilisation de ces ressources. Cet article est également disponible pour les [machines virtuelles Windows](../windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

> [!IMPORTANT]
> * Pour plus d'informations sur la tarification des différentes tailles, consultez [Tarification des machines virtuelles](https://azure.microsoft.com/pricing/details/virtual-machines/#Linux). 
> * Pour la disponibilité des tailles de machine virtuelle dans les régions Azure, consultez [Disponibilité des produits par région](https://azure.microsoft.com/regions/services/).
> * Pour connaître les limites générales des machines virtuelles Azure, consultez [Abonnement Azure et limites, quotas et contraintes du service](../../azure-subscription-service-limits.md).
> * Lisez-en davantage sur les [Unités de calcul Azure (ACU)](../windows/acu.md) pour découvrir comment comparer les performances de calcul entre les références Azure.
> 
> 

<br>   


| Type                     | Tailles           |    Description       |
|--------------------------|-------------------|------------------------------------------------------------------------------------------------------------------------------------|
| [Usage général](sizes-general.md)          | DSv2, Dv2, DS, D, Av2, A0-7,  | Ratio processeur/mémoire équilibré. Idéal pour le test et le développement, les bases de données petites à moyennes et les serveurs web au trafic faible à moyen. |
| [Optimisé pour le calcul](sizes-compute.md)        | Fs, F             | Ratio processeur/mémoire élevé. Convient pour les serveurs web au trafic moyen, les appareils réseau, les processus de traitement par lots et les serveurs d’application.        |
| [Mémoire optimisée](sizes-memory.md)         | GS, G, DSv2, DS, Dv2, D   | Ratio mémoire/coeur élevé. Idéal pour les serveurs de base de données relationnelle, les caches moyens à grands et l’analytique en mémoire.                 |
| [Optimisé pour le stockage](sizes-storage.md)        | Ls                | Débit de disque et E/S élevés. Idéale pour les bases de données NoSQL, SQL et Big Data.                                                         |
| [GPU](sizes-gpu.md)            | NV, NC            | Machines virtuelles spécialisées conçues pour les opérations graphiques lourdes et la retouche vidéo. Disponible avec un ou plusieurs GPU.       |
| [Calcul haute performance](sizes-hpc.md) | H, A8-11          | Nos machines virtuelles les plus rapides et dotées des processeurs les plus puissants avec interfaces réseau haut débit en option (RDMA). 

<br>

Lisez-en davantage sur les [Unités de calcul Azure (ACU)](../windows/acu.md) pour découvrir comment comparer les performances de calcul entre les références Azure.

En savoir plus sur les différentes tailles de machines virtuelles qui sont disponibles :
- [Usage général](sizes-general.md)
- [Optimisé pour le calcul](sizes-compute.md)
- [Mémoire optimisée](sizes-memory.md)
- [Optimisé pour le stockage](sizes-storage.md)
- [GPU](sizes-gpu.md)
- [Calcul haute performance](sizes-hpc.md)




