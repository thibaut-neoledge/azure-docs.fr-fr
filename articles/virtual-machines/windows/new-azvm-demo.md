---
title: "Créer une machine virtuelle Windows avec l’applet de commande New-AzVM dans Azure Cloud Shell | Microsoft Docs"
description: "Apprenez rapidement à créer des machines virtuelles Windows avec l’applet de commande New_AzVM dans Azure Cloud Shell."
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 09/21/2017
ms.author: cynthn
ROBOTS: NOINDEX
ms.translationtype: HT
ms.sourcegitcommit: 4f77c7a615aaf5f87c0b260321f45a4e7129f339
ms.openlocfilehash: c303235ead2af7cfaa368a5b5f00567ae44cfb86
ms.contentlocale: fr-fr
ms.lasthandoff: 09/22/2017

---

# <a name="create-a-windows-virtual-machine-with-the-new-azvm-preview-in-cloud-shell"></a>Créer une machine virtuelle Windows avec New-AzVM (préversion) dans Cloud Shell 

L’applet de commande New-AzVM (préversion) offre un moyen simplifié de créer une nouvelle machine virtuelle avec PowerShell. Ce guide décrit en détail comment utiliser PowerShell dans Azure Cloud Shell, avec l’applet de commande New-AzVM préinstallée, pour créer une nouvelle machine virtuelle Azure exécutant Windows Server 2016. Une fois le déploiement effectué, nous nous connectons au serveur avec RDP.  

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.


[!INCLUDE [cloud-shell-powershell](../../../includes/cloud-shell-powershell.md)]

## <a name="create-the-vm"></a>Créer la machine virtuelle

Vous pouvez utiliser l’applet de commande **New-AzVM** pour créer une machine virtuelle avec les valeurs par défaut, ce qui implique l’utilisation de l’image Windows Server 2016 Datacenter de la Place de marché Azure. Vous pouvez utiliser New-AzVM seul, auquel cas les valeurs par défaut des noms de ressources seront utilisées. Dans cet exemple, nous définissons le paramètre **-Name** en tant que *myVM*. L’applet de commande crée les ressources nécessaires en utilisant *myVM* comme préfixe pour le nom de ressource. 

Vérifiez que **PowerShell** est sélectionné dans Cloud Shell et tapez :

```azurepowershell-interactive
New-AzVm -Name myVM
```

Vous êtes invité à créer un nom d’utilisateur et un mot de passe pour la machine virtuelle, qui seront utilisés quand vous vous connecterez à la machine virtuelle plus loin dans cette rubrique. Le mot de passe doit compter 12 à 123 caractères et comprendre trois des quatre caractères suivants : une minuscule, une majuscule, un chiffre et un caractère spécial.

La création de la machine virtuelle et des ressources associées prend une minute. Lorsque vous avez terminé, vous pouvez voir toutes les ressources qui ont été créées avec l’applet de commande [Find-AzureRmResource](/powershell/module/azurerm.resources/find-azurermresource).

```azurepowershell-interactive
Find-AzureRmResource `
    -ResourceGroupNameEquals myVMResourceGroup | Format-Table Name
```

## <a name="connect-to-the-vm"></a>Connexion à la machine virtuelle

Une fois le déploiement terminé, créez une connexion Bureau à distance avec la machine virtuelle.

Utilisez la commande [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) pour retourner l’adresse IP publique de la machine virtuelle. Notez cette adresse IP.

```azurepowershell-interactive
Get-AzureRmPublicIpAddress `
    -ResourceGroupName myVMResourceGroup | Select IpAddress
```

Sur votre ordinateur local, ouvrez une invite de commandes et utilisez la commande **mstsc** pour démarrer une session Bureau à distance avec votre nouvelle machine virtuelle. Remplacez &lt;publicIPAddress&gt; par l’adresse IP de votre machine virtuelle. Lorsque vous y êtes invité, entrez le nom d’utilisateur et le mot de passe que vous aviez fournis pour votre machine virtuelle lors de sa création.

```
mstsc /v:<publicIpAddress>
```

## <a name="clean-up-resources"></a>Supprimer des ressources

Vous pouvez utiliser la commande [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) pour supprimer le groupe de ressources, la machine virtuelle et toutes les ressources associées, si vous n’en avez plus besoin.

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myVMResourceGroup
```

## <a name="next-steps"></a>Étapes suivantes

Dans cette rubrique, vous avez déployé une machine virtuelle simple avec New-AzVM et vous l’avez connectée via RDP. Pour en savoir plus sur les machines virtuelles Azure, suivez le didacticiel pour les machines virtuelles Windows.

> [!div class="nextstepaction"]
> [Didacticiels sur les machines virtuelles Azure Windows](./tutorial-manage-vm.md)

