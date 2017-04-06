---
title: "Outils de la communauté - Déplacement de ressources classiques vers Azure Resource Manager | Microsoft Docs"
description: "Cet article répertorie les outils qui ont été fournis par la communauté afin de faciliter la migration de ressources IaaS à partir de déploiements classiques vers le modèle de déploiement Azure Resource Manager."
services: virtual-machines-windows
documentationcenter: 
author: singhkays
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 228b697b-3950-49f5-84bb-283bb56621b1
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 1/23/2017
ms.author: kasing
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: 5e2ad5e5eae97645368797c8cdf848d88719bb64
ms.lasthandoff: 03/29/2017


---
# <a name="community-tools-to-migrate-iaas-resources-from-classic-to-azure-resource-manager"></a>Outils de la communauté permettant de faire migrer des ressources IaaS à partir de déploiements classiques vers Azure Resource Manager
Cet article répertorie les outils qui ont été fournis par la communauté afin de faciliter la migration de ressources IaaS à partir de déploiements classiques vers le modèle de déploiement Azure Resource Manager.

> [!NOTE]
> Ces outils ne sont pas pris en charge officiellement par le Support Microsoft. Ils sont donc disponibles en open source sur GitHub, et nous acceptons volontiers les demandes de publication de correctifs ou de scénarios supplémentaires. Pour signaler un problème, utilisez la fonctionnalité de problèmes GitHub.
> 
> Une migration avec ces outils entraînera un temps d’arrêt pour votre machine virtuelle Classic. Si vous souhaitez effectuer une migration prise en charge par la plateforme, consultez les articles suivants : 
> 
> * [Migration prise en charge par la plateforme de ressources IaaS Classic vers Azure Resource Manager](virtual-machines-windows-migration-classic-resource-manager.md)
> * [Étude technique approfondie de la migration prise en charge par la plateforme de ressources Classic vers Azure Resource Manager](virtual-machines-windows-migration-classic-resource-manager-deep-dive.md)
> * [Faire migrer des ressources IaaS Classic vers Azure Resource Manager à l’aide d’Azure PowerShell](virtual-machines-windows-ps-migration-classic-resource-manager.md)
> 
> 

## <a name="asm2arm"></a>ASM2ARM
Il s’agit d’un module de script PowerShell pour migrer votre machine virtuelle **unique** à partir d’un déploiement classique vers le modèle de déploiement Azure Resource Manager. 

[Lien vers la documentation des outils](https://github.com/Azure/classic-iaas-resourcemanager-migration/tree/master/asm2arm)

## <a name="migaz"></a>migAz
migAz est une option supplémentaire pour migrer un ensemble complet de ressources IaaS classiques vers des ressources Azure Resource Manager IaaS. La migration peut être exécutée dans le même abonnement ou entre différents abonnements et types d’abonnements (par ex. : les abonnements CSP).

[Lien vers la documentation des outils](https://github.com/Azure/classic-iaas-resourcemanager-migration/tree/master/migaz)


