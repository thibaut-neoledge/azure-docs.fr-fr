---
title: "Exemple de script Azure PowerShell : Créer un disque géré à partir d’une capture instantanée | Microsoft Docs"
description: "Exemple de script Azure PowerShell : Créer un disque géré à partir d’une capture instantanée"
services: virtual-machines-windows
documentationcenter: storage
author: ramankumarlive
manager: kavithag
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 06/05/2017
ms.author: ramankum
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: b475516694d120b7ea05d0892b6789710eec171e
ms.contentlocale: fr-fr
ms.lasthandoff: 08/21/2017

---

# <a name="create-a-managed-disk-from-a-snapshot-with-powershell"></a>Créer un disque géré à partir d’une capture instantanée avec PowerShell

Ce script crée un disque géré à partir d’une capture instantanée. Utilisez-le pour restaurer une machine virtuelle à partir de captures instantanées de disques de système d’exploitation et de données. Créez des disques managés de système d’exploitation et de données à partir des captures instantanées respectives, puis créez une machine virtuelle en joignant les disques managés. Vous pouvez également restaurer les disques de données d’une machine virtuelle existante en joignant les disques de données créés à partir de captures instantanées.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exemple de script

[!code-powershell[main](../../../powershell_scripts/virtual-machine/create-managed-disk-from-snapshot/create-managed-disk-from-snapshot.ps1 "Créer un disque managé à partir d’une capture instantanée")]


## <a name="script-explanation"></a>Explication du script

Ce script a recours aux commandes suivantes pour créer un disque managé à partir d’une capture instantanée. Chaque commande du tableau renvoie à une documentation spécifique.

| Commande | Remarques |
|---|---|
| [Get-AzureRmSnapshot](/powershell/module/azurerm.compute/Get-AzureRmSnapshot) | Obtient les propriétés de capture instantanée.  |
| [New-AzureRmDiskConfig](/powershell/module/azurerm.compute/New-AzureRmDiskConfig) | Crée une configuration de disque qui est utilisée pour la création du disque. Il inclut l’ID de ressource de la capture instantanée parente, un emplacement identique à l’emplacement de la capture instantanée parente, et le type de stockage.  |
| [New-AzureRmDisk](/powershell/module/azurerm.compute/New-AzureRmDisk) | Crée un disque à partir de la configuration de disque, du nom du disque et du nom de groupe de ressources transmis en tant que paramètres. |


## <a name="next-steps"></a>Étapes suivantes

[Créer une machine virtuelle à partir d’un disque géré](./virtual-machines-windows-powershell-sample-create-vm-from-managed-os-disks.md?toc=%2fpowershell%2fmodule%2ftoc.json)

Pour plus d’informations sur le module Azure PowerShell, consultez [Documentation Azure PowerShell](/powershell/azure/overview).

Vous trouverez des exemples supplémentaires de scripts PowerShell de machine virtuelle dans la [documentation relative aux machines virtuelles Windows Azure](../../app-service-web/app-service-powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
