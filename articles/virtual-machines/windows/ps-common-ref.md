---
title: Commandes PowerShell courantes pour les machines virtuelles Azure | Microsoft Docs
description: "Commandes PowerShell courantes pour vous aider à démarrer la création et la gestion de vos machines virtuelles Windows dans Azure."
services: virtual-machines-windows
documentationcenter: 
author: davidmu1
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: ba3839a2-f3d5-4e19-a5de-95bfb1c0e61e
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 07/17/2017
ms.author: davidmu
ms.translationtype: HT
ms.sourcegitcommit: cddb80997d29267db6873373e0a8609d54dd1576
ms.openlocfilehash: dd75685c2373f65965248656137f1551fa765b7b
ms.contentlocale: fr-fr
ms.lasthandoff: 07/18/2017

---
# <a name="common-powershell-commands-for-creating-and-managing-azure-virtual-machines"></a>Commandes PowerShell courantes permettant de créer et de gérer des machines virtuelles Azure

Cet article présente certaines des commandes Azure PowerShell que vous pouvez utiliser pour créer et gérer des machines virtuelles dans votre abonnement Azure.  Pour en savoir plus sur les commutateurs et options de ligne de commande spécifiques, vous pouvez utiliser la **commande** *Get-Help*.

Pour plus d’informations sur l’installation de la version la plus récente d’Azure PowerShell, la sélection de votre abonnement et la connexion à votre compte, consultez [Installation et configuration d’Azure PowerShell](/powershell/azure/overview).

Ces variables peuvent être utiles si vous utilisez plusieurs des commandes de cet article :

- $location : l’emplacement de la machine virtuelle. Vous pouvez utiliser [Get-AzureRmLocation](https://docs.microsoft.com/powershell/module/azurerm.resources/get-azurermlocation) pour rechercher une [région géographique](https://azure.microsoft.com/regions/) qui vous convient.
- $myResourceGroup - Le nom du groupe de ressources qui contient les machines virtuelles.
- $myVM - Le nom de la machine virtuelle.

## <a name="create-a-vm"></a>Créer une machine virtuelle

| Tâche | Commande |
| ---- | ------- |
| Créer une configuration de machine virtuelle |$vm = [New-AzureRmVMConfig](https://docs.microsoft.com/powershell/module/azurerm.compute/new-azurermvmconfig) -VMName $myVM -VMSize "Standard_D1_v1"<BR></BR><BR></BR>La configuration de machine virtuelle est utilisée pour définir ou mettre à jour les paramètres d’une machine virtuelle. La configuration est initialisée avec le nom de la machine virtuelle et sa [taille](sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). |
| Ajouter des paramètres de configuration |$vm = [Set-AzureRmVMOperatingSystem](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmoperatingsystem) -VM $vm -Windows -ComputerName $myVM -Credential $cred -ProvisionVMAgent -EnableAutoUpdate<BR></BR><BR></BR>Les paramètres de système d’exploitation, y compris les [informations d’identification](https://technet.microsoft.com/library/hh849815.aspx), sont ajoutés à l’objet de configuration que vous avez créé précédemment à l’aide de New-AzureRmVMConfig. |
| Ajouter une interface réseau |$vm = [Add-AzureRmVMNetworkInterface](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.5.0/Add-AzureRmVMNetworkInterface) -VM $vm -Id $nic.Id<BR></BR><BR></BR>Une machine virtuelle doit être associée à une [interface réseau](../virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) pour pouvoir communiquer au sein d’un réseau virtuel. Vous pouvez également utiliser la commande [Get-AzureRmNetworkInterface](https://docs.microsoft.com/powershell/module/azurerm.compute/add-azurermvmnetworkinterface) pour récupérer un objet d’interface réseau existant. |
| Spécifier une image de plateforme |$vm = [Set-AzureRmVMSourceImage](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmsourceimage) -VM $vm -PublisherName "publisher_name" -Offer "publisher_offer" -Skus "product_sku" -Version "latest"<BR></BR><BR></BR>[Des informations sur l’image](cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) sont ajoutées à l’objet de configuration que vous avez créé, via New-AzureRmVMConfig. L’objet renvoyé par cette commande est utilisé uniquement lorsque vous définissez le disque du système d’exploitation pour l’utilisation d’une image de plateforme. |
| Définir le disque du système d’exploitation pour l’utilisation d’une image de plateforme |$vm = [Set-AzureRmVMOSDisk](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmosdisk) -VM $vm -Name "myOSDisk" -VhdUri "http://mystore1.blob.core.windows.net/vhds/myOSDisk.vhd" -CreateOption FromImage<BR></BR><BR></BR>Le nom du disque du système d’exploitation et son emplacement au sein du [stockage](../../storage/storage-powershell-guide-full.md) sont ajoutés à l’objet de configuration que vous avez créé. |
| Définir le disque du système d’exploitation pour l’utilisation d’une image généralisée |$vm = Set-AzureRmVMOSDisk -VM $vm -Name "myOSDisk" -SourceImageUri "https://mystore1.blob.core.windows.net/system/Microsoft.Compute/Images/myimages/myprefix-osDisk.{guid}.vhd" -VhdUri "https://mystore1.blob.core.windows.net/vhds/disk_name.vhd" -CreateOption FromImage -Windows<BR></BR><BR></BR>Le nom du disque du système d’exploitation, l’emplacement de l’image source et celui du disque au sein du [stockage](../../storage/storage-powershell-guide-full.md) sont ajoutés à l’objet de configuration que vous avez créé. |
| Définir le disque du système d’exploitation pour l’utilisation d’une image spécialisée |$vm = Set-AzureRmVMOSDisk -VM $vm -Name "myOSDisk" -VhdUri "http://mystore1.blob.core.windows.net/vhds/" -CreateOption Attach -Windows |
| Créer une machine virtuelle |[New-AzureRmVM]() -ResourceGroupName $myResourceGroup -Location $location -VM $vm<BR></BR><BR></BR>Toutes les ressources sont créées dans un [groupe de ressources](../../azure-resource-manager/powershell-azure-resource-manager.md). Avant d’exécuter cette commande, exécutez les commandes New-AzureRmVMConfig, Set-AzureRmVMOperatingSystem, Set-AzureRmVMSourceImage, Add-AzureRmVMNetworkInterface et Set-AzureRmVMOSDisk. |

## <a name="get-information-about-vms"></a>Obtenir des informations sur les machines virtuelles

| Task | Commande |
| ---- | ------- |
| Répertorier les machines virtuelles au sein d’un abonnement |[Get-AzureRmVM](https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvm) |
| Répertorier les machines virtuelles au sein d’un groupe de ressources |Get-AzureRmVM -ResourceGroupName $myResourceGroup<BR></BR><BR></BR>Pour obtenir une liste de groupes de ressources dans votre abonnement, utilisez la commande [Get-AzureRmResourceGroup](https://docs.microsoft.com/powershell/module/azurerm.resources/get-azurermresourcegroup). |
| Obtenir des informations sur une machine virtuelle |Get-AzureRmVM -ResourceGroupName $myResourceGroup -Name $myVM |

## <a name="manage-vms"></a>Gérer les machines virtuelles
| Task | Commande |
| --- | --- |
| Démarrer une machine virtuelle |[Start-AzureRmVM](https://docs.microsoft.com/powershell/module/azurerm.compute/start-azurermvm) -ResourceGroupName $myResourceGroup -Name $myVM |
| Arrêter une machine virtuelle |[Stop-AzureRmVM](https://docs.microsoft.com/powershell/module/azurerm.compute/stop-azurermvm) -ResourceGroupName $myResourceGroup -Name $myVM |
| Redémarrer une machine virtuelle en cours d’exécution |[Restart-AzureRmVM](https://docs.microsoft.com/powershell/module/azurerm.compute/restart-azurermvm) -ResourceGroupName $myResourceGroup -Name $myVM |
| Supprimer une machine virtuelle |[Remove-AzureRmVM](https://docs.microsoft.com/powershell/module/azurerm.compute/remove-azurermvm) -ResourceGroupName $myResourceGroup -Name $myVM |
| Généraliser une machine virtuelle |[Set-AzureRmVm](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvm) -ResourceGroupName $myResourceGroup -Name $myVM -Generalized<BR></BR><BR></BR>Exécutez cette commande avant la commande Save-AzureRmVMImage. |
| Capturer une machine virtuelle |[Save-AzureRmVMImage](https://docs.microsoft.com/powershell/module/azurerm.compute/save-azurermvmimage) -ResourceGroupName $myResourceGroup -VMName $myVM -DestinationContainerName "myImageContainer" -VHDNamePrefix "myImagePrefix" -Path "C:\filepath\filename.json"<BR></BR><BR></BR>Une machine virtuelle doit être [préparée, arrêtée et généralisée](prepare-for-upload-vhd-image.md) pour être utilisée lors de la création d’une image. Avant d’exécuter cette commande, exécutez la commande Set-AzureRmVm. |
| Mettre à jour une machine virtuelle |[Update-AzureRmVM](https://docs.microsoft.com/powershell/module/azurerm.compute/update-azurermvm) -ResourceGroupName $myResourceGroup -VM $vm<BR></BR><BR></BR>Obtenez la configuration de machine virtuelle actuelle à l’aide de la commande Get-AzureRmVM, modifiez les paramètres de configuration sur l’objet de machine virtuelle, puis exécutez cette commande. |
| Ajouter un disque de données à une machine virtuelle |[Add-AzureRmVMDataDisk](https://docs.microsoft.com/powershell/module/azurerm.compute/add-azurermvmdatadisk) -VM $vm -Name "myDataDisk" -VhdUri "https://mystore1.blob.core.windows.net/vhds/myDataDisk.vhd" -LUN # -Caching ReadWrite -DiskSizeinGB # -CreateOption Empty<BR></BR><BR></BR>Utilisez la commande Get-AzureRmVM pour obtenir l’objet de machine virtuelle. Spécifiez le numéro de LUN et la taille du disque. Exécutez la commande Update-AzureRmVM pour appliquer les modifications apportées à la configuration à la machine virtuelle. Le disque que vous ajoutez n’est pas initialisé. |
| Supprimer un disque de données à partir d'une machine virtuelle |[Remove-AzureRmVMDataDisk](https://docs.microsoft.com/powershell/module/azurerm.compute/remove-azurermvmdatadisk) -VM $vm -Name "myDataDisk"<BR></BR><BR></BR>Utilisez la commande Get-AzureRmVM pour obtenir l’objet de machine virtuelle. Exécutez la commande Update-AzureRmVM pour appliquer les modifications apportées à la configuration à la machine virtuelle. |
| Ajouter une extension à une machine virtuelle |[Set-AzureRmVMExtension](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmextension) -ResourceGroupName $myResourceGroup -Location $location -VMName $myVM -Name "extensionName" -Publisher "publisherName" -Type "extensionType" -TypeHandlerVersion "#.#" -Settings $Settings -ProtectedSettings $ProtectedSettings<BR></BR><BR></BR>Exécutez cette commande avec les [informations de configuration](extensions-configuration-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) appropriées pour l’extension que vous souhaitez installer. |
| Supprimer une extension de machine virtuelle |[Remove-AzureRmVMExtension](https://docs.microsoft.com/powershell/module/azurerm.compute/remove-azurermvmextension) -ResourceGroupName $myResourceGroup -Name "extensionName" -VMName $myVM |

## <a name="next-steps"></a>Étapes suivantes
* Consultez les étapes de base pour la création d’une machine virtuelle, décrites dans la rubrique [Création d’une machine virtuelle Windows à l’aide de Resource Manager et de PowerShell](../virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).


