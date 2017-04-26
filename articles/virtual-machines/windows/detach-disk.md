---
title: "Détacher un disque de données d’une machine virtuelle Windows - Azure| Microsoft Docs"
description: "Apprenez à détacher un disque de données d’une machine virtuelle dans Azure à l’aide du modèle de déploiement Ressource Manager."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 13180343-ac49-4a3a-85d8-0ead95e2028c
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 03/21/2017
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: abdbb9a43f6f01303844677d900d11d984150df0
ms.openlocfilehash: cd60055833eec98c4bef4680fec9910c347d9d0c
ms.lasthandoff: 04/20/2017


---
# <a name="how-to-detach-a-data-disk-from-a-windows-virtual-machine"></a>Détachement d’un disque de données d’une machine virtuelle Windows
Lorsque vous n’avez plus besoin d’un disque de données qui est attaché à une machine virtuelle, vous pouvez le détacher facilement. Cela supprime le disque de la machine virtuelle, mais pas du stockage.

> [!WARNING]
> Si vous détachez un disque, il n’est pas supprimé automatiquement. Si vous êtes abonné au stockage Premium, vous continuerez à engager des frais de stockage pour le disque. Pour plus d’informations, consultez [Tarification et facturation de Premium Storage](../../storage/storage-premium-storage.md#pricing-and-billing).
>
>

Si vous souhaitez réutiliser les données du disque, vous pouvez l’attacher à la même machine virtuelle ou à une autre.

## <a name="detach-a-data-disk-using-the-portal"></a>Détacher un disque de données avec le portail
1. Dans le concentrateur du portail, sélectionnez **Machines virtuelles**.
2. Sélectionnez la machine virtuelle qui possède le disque de données que vous souhaitez détacher, puis cliquez sur **Arrêter** pour libérer la machine virtuelle.
3. Dans le panneau de la machine virtuelle, sélectionnez **Disques**.
4. En haut du panneau **Disques**, sélectionnez **Modifier**.
5. Dans le panneau **Disques**, à l’extrême droite du disque de données que vous souhaitez détacher, cliquez sur le bouton détacher ![image du bouton détacher](./media/detach-disk/detach.png).
5. Une fois que le disque a été supprimé, cliquez sur Enregistrer en haut du panneau.
6. Dans le panneau de la machine virtuelle, cliquez sur **Présentation**, puis cliquez sur le bouton **Démarrer** en haut du panneau pour redémarrer la machine virtuelle.



Le disque reste dans le stockage, mais il n’est plus attaché à une machine virtuelle.

## <a name="detach-a-data-disk-using-powershell"></a>Détacher un disque de données avec PowerShell
Dans cet exemple, la première commande récupère la machine virtuelle nommée **MyVM07** dans le groupe de ressources **RG11** à l’aide de l’applet de commande Get-AzureRmVM. La commande stocke la machine virtuelle dans la variable **$VirtualMachine** .

La deuxième commande supprime le disque de données nommé DataDisk3 de la machine virtuelle.

La dernière commande met à jour l’état de la machine virtuelle pour terminer le processus de suppression du disque de données.

```powershell
$VirtualMachine = Get-AzureRmVM -ResourceGroupName "RG11" -Name "MyVM07"
Remove-AzureRmVMDataDisk -VM $VirtualMachine -Name "DataDisk3"
Update-AzureRmVM -ResourceGroupName "RG11" -Name "MyVM07" -VM $VirtualMachine
```

Pour plus d’informations, consultez [Remove-AzureRmVMDataDisk](/powershell/module/azurerm.compute/remove-azurermvmdatadisk).

## <a name="next-steps"></a>Étapes suivantes
Si vous souhaitez réutiliser le disque de données, vous pouvez simplement [l’attacher à une autre machine virtuelle](attach-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)


