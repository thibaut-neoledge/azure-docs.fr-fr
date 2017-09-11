---
title: "Exemple de script Azure PowerShell : Copier (déplacer) la capture instantanée d’un disque géré vers un abonnement identique ou différent | Microsoft Docs"
description: "Exemple de script Azure PowerShell : Copier (déplacer) la capture instantanée d’un disque géré vers un abonnement identique ou différent"
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
ms.date: 06/06/2017
ms.author: ramankum
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: f7b4869669a2c5e840f9bd384dcd6d6096ba58e2
ms.contentlocale: fr-fr
ms.lasthandoff: 08/21/2017

---

# <a name="copy-snapshot-of-a-managed-disk-in-same-subscription-or-different-subscription-with-powershell"></a>Copier une capture instantanée de disque géré dans un abonnement identique ou différent avec PowerShell

Ce script crée la copie d’une capture instantanée dans le même abonnement ou un autre abonnement. Utilisez ce script pour déplacer une capture instantanée vers un autre abonnement dans une optique de rétention des données. Le stockage de captures instantanées dans un autre abonnement vous protège contre la suppression accidentelle de captures instantanées dans votre abonnement principal. 

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exemple de script

[!code-powershell[main](../../../powershell_scripts/virtual-machine/copy-snapshot-to-same-or-different-subscription/copy-snapshot-to-same-or-different-subscription.ps1 "Copier une capture instantanée")]


## <a name="script-explanation"></a>Explication du script

Ce script utilise les commandes suivantes pour créer une capture instantanée dans l’abonnement cible à l’aide de l’ID de la capture instantanée source. Chaque commande du tableau renvoie à une documentation spécifique.

| Commande | Remarques |
|---|---|
| [New-AzureRmSnapshotConfig](/powershell/module/azurerm.compute/New-AzureRmSnapshotConfig) | Crée une configuration de capture instantanée, utilisée pour la création de captures instantanées. Elle inclut l’ID de ressource de la capture instantanée parente et un emplacement identique à l’emplacement de la capture instantanée parente.  |
| [New-AzureRmSnapshot](/powershell/module/azurerm.compute/New-AzureRmDisk) | Crée une capture instantanée à partir de la configuration de capture instantanée, du nom de capture instantanée et du nom de groupe de ressources transmis en tant que paramètres. |


## <a name="next-steps"></a>Étapes suivantes

[Créer une machine virtuelle à partir d’une capture instantanée](./virtual-machines-windows-powershell-sample-create-vm-from-snapshot.md?toc=%2fpowershell%2fmodule%2ftoc.json)

Pour plus d’informations sur le module Azure PowerShell, consultez [Documentation Azure PowerShell](/powershell/azure/overview).

Vous trouverez des exemples supplémentaires de scripts PowerShell de machine virtuelle dans la [documentation relative aux machines virtuelles Windows Azure](../../app-service-web/app-service-powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
