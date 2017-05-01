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
ms.date: 03/30/2017
ms.author: kasing
translationtype: Human Translation
ms.sourcegitcommit: 6ea03adaabc1cd9e62aa91d4237481d8330704a1
ms.openlocfilehash: 22bf0402c16745dac1c8287b0ceb3598ea41305b
ms.lasthandoff: 04/06/2017


---
# <a name="community-tools-to-migrate-iaas-resources-from-classic-to-azure-resource-manager"></a>Outils de la communauté permettant de faire migrer des ressources IaaS à partir de déploiements classiques vers Azure Resource Manager
Cet article répertorie les outils qui ont été fournis par la communauté afin de faciliter la migration de ressources IaaS à partir de déploiements classiques vers le modèle de déploiement Azure Resource Manager.

> [!NOTE]
> Ces outils ne sont pas pris en charge officiellement par le Support Microsoft. Ils sont donc disponibles en open source sur GitHub, et nous acceptons volontiers les demandes de publication de correctifs ou de scénarios supplémentaires. Pour signaler un problème, utilisez la fonctionnalité de problèmes GitHub.
> 
> Une migration avec ces outils entraînera un temps d’arrêt pour votre machine virtuelle Classic. Si vous souhaitez effectuer une migration prise en charge par la plateforme, consultez les articles suivants : 
> 
>   * [Migration prise en charge par la plateforme de ressources IaaS Classic vers Azure Resource Manager](migration-classic-resource-manager-overview.md)
>   * [Étude technique approfondie de la migration prise en charge par la plateforme de ressources Classic vers Azure Resource Manager](migration-classic-resource-manager-deep-dive.md)
>   * [Faire migrer des ressources IaaS Classic vers Azure Resource Manager à l’aide d’Azure PowerShell](migration-classic-resource-manager-ps.md)
> 
> 

## <a name="asmmetadataparser"></a>AsmMetadataParser
Il s’agit d’un ensemble d’outils d’assistance créé dans le cadre de migrations d’entreprise depuis la gestion des services Azure vers Azure Resource Manager. Cet outil vous permet de répliquer votre infrastructure dans un autre abonnement, qui peut être utilisé pour tester la migration et mettre à plat tous les problèmes avant d’exécuter la migration sur votre abonnement de production.

[Lien vers la documentation des outils](https://github.com/Azure/classic-iaas-resourcemanager-migration/tree/master/AsmToArmMigrationApiToolset)

## <a name="migaz"></a>migAz
migAz est une option supplémentaire pour migrer un ensemble complet de ressources IaaS classiques vers des ressources Azure Resource Manager IaaS. La migration peut être exécutée dans le même abonnement ou entre différents abonnements et types d’abonnements (par ex. : les abonnements CSP).

[Lien vers la documentation des outils](https://github.com/Azure/classic-iaas-resourcemanager-migration/tree/master/migaz)

## <a name="next-steps"></a>Étapes suivantes

* [Vue d’ensemble de la migration prise en charge par la plateforme de ressources IaaS Classic vers Azure Resource Manager](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Étude technique approfondie de la migration prise en charge par la plateforme de ressources Classic vers Azure Resource Manager](migration-classic-resource-manager-deep-dive.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Planification de la migration des ressources IaaS d’Azure Classic vers Azure Resource Manager](migration-classic-resource-manager-plan.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Faire migrer des ressources IaaS Classic vers Azure Resource Manager à l’aide d’Azure PowerShell](migration-classic-resource-manager-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Faire migrer des ressources IaaS Classic vers Azure Resource Manager à l’aide de l’interface de ligne de commande Azure](../linux/migration-classic-resource-manager-cli.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Passer en revue les erreurs courantes de migration](migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Passez en revue les questions fréquemment posées sur la migration des ressources IaaS de Classic vers Azure Resource Manager](migration-classic-resource-manager-faq.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)


