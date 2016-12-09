---
title: "Extensions et fonctionnalités des machines virtuelles | Microsoft Docs"
description: "Découvrez les extensions disponibles pour les machines virtuelles, regroupées par ce qu’ils fournissent ou améliorent."
services: virtual-machines-windows
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager
ms.assetid: 999d63ee-890e-432e-9391-25b3fc6cde28
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 09/30/2016
ms.author: nepeters
translationtype: Human Translation
ms.sourcegitcommit: ee34a7ebd48879448e126c1c9c46c751e477c406
ms.openlocfilehash: 7bd48e2ef9177369190494c38bfdcf1bb99e61ea


---
# <a name="about-virtual-machine-extensions-and-features"></a>À propos des extensions et des fonctionnalités des machines virtuelles
## <a name="azure-vm-extensions"></a>Extensions de machine virtuelle Azure
Les extensions de machine virtuelle Azure sont de petites applications qui exécutent des tâches de configuration et d’automatisation post-déploiement sur des machines virtuelles Azure. Par exemple, si une machine virtuelle requiert l’installation d’un logiciel, une protection antivirus ou une configuration de Docker, il est possible d’effectuer ces tâches à l’aide d’une extension de machine virtuelle. Les extensions de machine virtuelle Azure peuvent être exécutées à l’aide de l’interface de ligne de commande Azure, de PowerShell, de modèles Resource Manager et du portail Azure. Les extensions peuvent être associées à un nouveau déploiement de machine virtuelle ou s’exécuter sur tout système existant.

Ce document précise les conditions préalables pour une extension de machine virtuelle Azure. Il fournit également des instructions sur la manière de détecter des extensions de machine virtuelle disponibles. 

## <a name="azure-vm-agent"></a>Agent Azure VM
L’agent Azure VM gère l’interaction entre une machine virtuelle et le contrôleur de structure Azure. L’agent VM est responsable de nombreux aspects fonctionnels liés au déploiement et à la gestion des machines virtuelles Azure, dont les extensions de machine virtuelle en cours d’exécution. L’agent Azure VM est préinstallé sur les images de la galerie Azure et peut être installé sur les systèmes d’exploitation pris en charge. 

Pour plus d’informations sur les systèmes d’exploitation pris en charge et des instructions d’installation, voir [Agent de machine virtuelle Azure](virtual-machines-windows-classic-agents-and-extensions.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

## <a name="discover-vm-extensions"></a>Détecter des extensions de machine virtuelle
De nombreuses extensions de machine virtuelle différentes sont disponibles pour une utilisation avec les machines virtuelles Azure. Pour en obtenir la liste complète, exécutez la commande suivante avec l’interface de ligne de commande Azure, en remplaçant location par l’emplacement de votre choix.

```none
Get-AzureVMAvailableExtension | Select ExtensionName, Version
```

<br />

## <a name="common-vm-extensions"></a>Extensions de machine virtuelle courantes
| Nom de l’extension | Description | Informations complémentaires |
| --- | --- | --- |
| Extension de script personnalisé pour Windows |Exécuter des scripts sur une machine virtuelle Azure |[Extension de script personnalisé pour Windows](virtual-machines-windows-extensions-customscript.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) |
| Extension DSC pour Windows |Extension PowerShell DSC (Desired State Configuration). |[Extension de machine virtuelle Docker](virtual-machines-windows-extensions-dsc-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) |
| Extension Diagnostics Azure |Gérer les diagnostics Azure |[Extension Diagnostics Azure](https://azure.microsoft.com/blog/windows-azure-virtual-machine-monitoring-with-wad-extension/) |




<!--HONumber=Nov16_HO3-->


