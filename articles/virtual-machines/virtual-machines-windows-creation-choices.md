---
title: "Différentes méthodes de création d’une machine virtuelle Windows | Microsoft Docs"
description: "Répertorie différentes méthodes de création d’une machine virtuelle Windows avec Resource Manager."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 809ba8f4-b54e-43c5-bbe3-8e710c49971f
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 09/27/2016
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 5919c477502767a32c535ace4ae4e9dffae4f44b
ms.openlocfilehash: a5e0ba0128b7f20d6307f5dea784de007e71321c


---
# <a name="different-ways-to-create-a-windows-virtual-machine-with-resource-manager"></a>Différentes méthodes de création de machine virtuelle Windows avec Resource Manager
Microsoft Azure propose différents moyens de créer des machines virtuelles adaptées à tous les utilisateurs et toutes les applications. Pour cette raison, vous devez effectuer certains choix relatifs à la machine virtuelle et à sa création. Cet article vous propose un résumé de ces options et des liens pour obtenir des instructions.

## <a name="azure-portal"></a>Portail Azure
Le portail Azure constitue un moyen simple de tester une machine virtuelle, en particulier si vous venez de démarrer avec Azure. 

[Créer une machine virtuelle exécutant Windows dans le portail](virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="template"></a>Modèle
Les machines virtuelles requièrent une combinaison de ressources (par exemple un groupe à haute disponibilité et des comptes de stockage). Au lieu de déployer et de gérer chaque ressource séparément, vous pouvez créer un modèle Azure Resource Manager qui déploie et approvisionne toutes les ressources en une seule opération coordonnée.

* [Création d’une machine virtuelle Windows avec un modèle du Gestionnaire de ressources](virtual-machines-windows-ps-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="azure-powershell"></a>Azure PowerShell
Si vous préférez travailler dans une invite de commandes, vous pouvez utiliser Azure PowerShell.

* [Créer une machine virtuelle Windows à l’aide de PowerShell](virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="visual-studio"></a>Visual Studio
Utilisez Visual Studio pour créer, gérer et déployer des machines virtuelles avec Azure Tools for Visual Studio et le Kit de développement logiciel (SDK) Azure.

[Azure Tools for Visual Studio](https://www.visualstudio.com/features/azure-tools-vs)




<!--HONumber=Nov16_HO3-->


