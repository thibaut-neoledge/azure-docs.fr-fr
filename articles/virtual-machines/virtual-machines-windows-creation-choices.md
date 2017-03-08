---
title: "Différentes méthodes de création d’une machine virtuelle Windows dans Azure | Microsoft Docs"
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
ms.date: 03/02/2017
ms.author: cynthn
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 32e30b44c2f7cfa9c1069190fdc53dbe6e9f4cd5
ms.openlocfilehash: d01b71257a4a95294aebcde8c1d056e3fc3ed1be
ms.lasthandoff: 03/01/2017


---
# <a name="different-ways-to-create-a-windows-virtual-machine"></a>Les différentes façons de créer une machine virtuelle Windows

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


