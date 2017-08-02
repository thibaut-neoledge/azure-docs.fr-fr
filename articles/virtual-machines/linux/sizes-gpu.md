---
title: Tailles des machines virtuelles Linux Azure - GPU | Microsoft Docs
description: "Répertorie les différentes tailles de GPU optimisées disponibles pour les machines virtuelles Linux dans Azure."
services: virtual-machines-linux
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager,azure-service-management
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/07/2017
ms.author: cynthn
ms.translationtype: HT
ms.sourcegitcommit: 2ad539c85e01bc132a8171490a27fd807c8823a4
ms.openlocfilehash: ec289cf53f2cfecd2744b739667ef831dafd59a4
ms.contentlocale: fr-fr
ms.lasthandoff: 07/12/2017

---

# <a name="gpu-linux-vm-sizes"></a>Tailles de machine virtuelle Linux GPU

[!INCLUDE [virtual-machines-common-sizes-gpu](../../../includes/virtual-machines-common-sizes-gpu.md)]


[!INCLUDE [virtual-machines-common-sizes-table-defs](../../../includes/virtual-machines-common-sizes-table-defs.md)]

[!INCLUDE [virtual-machines-n-series-linux-support](../../../includes/virtual-machines-n-series-linux-support.md)]

Pour les étapes d’installation et de vérification des pilotes, consultez l’article sur l’[installation de pilotes de la série N pour Linux](n-series-driver-setup.md).

[!INCLUDE [virtual-machines-n-series-considerations](../../../includes/virtual-machines-n-series-considerations.md)]

* Nous ne recommandons pas l’installation d’un serveur spécifique ou d’autres systèmes qui utilisent le pilote nouveau sur les machines virtuelles Ubuntu NC. Avant d’installer les pilotes GPU NVIDIA, vous devez désactiver le pilote nouveau.  

## <a name="other-sizes"></a>Autres tailles
- [Usage général](sizes-general.md)
- [Optimisé pour le calcul](sizes-compute.md)
- [Mémoire optimisée](sizes-memory.md)
- [Optimisé pour le stockage](sizes-storage.md)
- [Calcul haute performance](sizes-hpc.md)

## <a name="next-steps"></a>Étapes suivantes
Lisez-en davantage sur les [Unités de calcul Azure (ACU)](acu.md) pour découvrir comment comparer les performances de calcul entre les références Azure.
