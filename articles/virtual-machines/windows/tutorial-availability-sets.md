---
title: "Tutoriel sur les groupes à haute disponibilité pour les machines virtuelles Windows dans Azure | Microsoft Docs"
description: "Découvrez les groupes à haute disponibilité pour les machines virtuelles Windows dans Azure."
documentationcenter: 
services: virtual-machines-windows
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 04/26/2017
ms.author: cynthn
ms.translationtype: Human Translation
ms.sourcegitcommit: be3ac7755934bca00190db6e21b6527c91a77ec2
ms.openlocfilehash: 831c55939ad3673aa8e44f165e18e826f64b54cc
ms.contentlocale: fr-fr
ms.lasthandoff: 05/03/2017


---

# <a name="how-to-use-availability-sets"></a>Utilisation des groupes à haute disponibilité

Dans ce tutoriel, vous allez découvrir comment augmenter la disponibilité de vos machines virtuelles en les plaçant dans un regroupement logique appelé groupe à haute disponibilité. Lorsque vous créez des machines virtuelles au sein d’un groupe à haute disponibilité, la plateforme Azure répartit les machines virtuelles dans l’infrastructure sous-jacente. En cas de panne matérielle ou de tâches de maintenance planifiées sur la plateforme, l’utilisation des groupes à haute disponibilité garantit qu’au moins une machine virtuelle continue à s’exécuter.

Les étapes de ce tutoriel peuvent être effectuées à l’aide de la dernière version du module [Azure PowerShell](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/).

## <a name="availability-set-overview"></a>Vue d’ensemble des groupes à haute disponibilité

Des machines virtuelles peuvent être créées dans des regroupements logiques de matériel dans le centre de données Azure sous-jacent. Si vous créez au moins deux machines virtuelles, vos ressources de calcul et de stockage sont réparties dans le matériel, par exemple sur les serveurs, les commutateurs réseau et l’espace de stockage. Cette distribution assure la disponibilité de votre application si un composant matériel devait subir une maintenance. Les groupes à haute disponibilité vous permettent de définir ce regroupement logique.

Ils garantissent une haute disponibilité pour les machines virtuelles. Vous devez également vous assurer que vos applications sont aussi conçues pour tolérer des événements de maintenance ou des interruptions.

## <a name="create-an-availability-set"></a>Créer un groupe à haute disponibilité

Vous pouvez créez un groupe à haute disponibilité avec la commande [New-AzureRmAvailabilitySet](/powershell/module/azurerm.compute/new-azurermavailabilityset). Dans cet exemple, nous définissons le nombre de domaines de mise à jour et d’erreur sur *2* pour le groupe à haute disponibilité nommé *myAvailabilitySet* dans le groupe de ressources *myResourceGroupAvailability*.


```powershell
New-AzureRmAvailabilitySet `
   -Location EastUS `
   -Name myAvailabilitySet `
   -ResourceGroupName myResourceGroupAvailability `
   -Managed `
   -PlatformFaultDomainCount 2 `
   -PlatformUpdateDomainCount 2
```

## <a name="create-vms-inside-an-availability-set"></a>Créer des machines virtuelles dans un groupe à haute disponibilité

Vous devez créer des machines virtuelles au sein du groupe à haute disponibilité pour vous assurer qu’elles sont correctement réparties dans le matériel. Vous ne pouvez pas ajouter une machine virtuelle existante à un groupe à haute disponibilité après sa création. 

Le matériel situé à un emplacement est divisé en plusieurs domaines de mise à jour et d’erreur. Un **domaine de mise à jour** est un groupe de machines virtuelles et d’équipements physiques sous-jacents pouvant être redémarrés en même temps. Les machines virtuelles d’un même **domaine d’erreur** partagent un espace de stockage commun ainsi qu’une source d’alimentation et un commutateur réseau. 

Lorsque vous créez une machine virtuelle à l’aide de la configuration utilisant [New-AzureRMVMConfig](/powershell/module/azurerm.compute/new-azurermvmconfig), vous spécifiez le groupe à haute disponibilité à l’aide du paramètre `-AvailabilitySetId` pour en spécifier l’ID.

Créez 2 machines virtuelles avec [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) dans le groupe à haute disponibilité.

```powershell
$availabilitySet = Get-AzureRmAvailabilitySet `
    -ResourceGroupName myResourceGroupAvailability `
    -Name myAvailabilitySet

$cred = Get-Credential -Message "Enter a username and password for the virtual machine."

$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig `
    -Name mySubnet `
    -AddressPrefix 192.168.1.0/24
$vnet = New-AzureRmVirtualNetwork `
    -ResourceGroupName myResourceGroupAvailability `
    -Location EastUS `
    -Name MYvNET `
    -AddressPrefix 192.168.0.0/16 `
    -Subnet $subnetConfig

for ($i=1; $i -le 2; $i++)
{
   $pip = New-AzureRmPublicIpAddress `
        -ResourceGroupName myResourceGroupAvailability `
        -Location EastUS `
        -Name "mypublicdns$(Get-Random)" `
        -AllocationMethod Static `
        -IdleTimeoutInMinutes 4

   $nsgRuleRDP = New-AzureRmNetworkSecurityRuleConfig `
        -Name myNetworkSecurityGroupRuleRDP$i `
        -Protocol Tcp `
        -Direction Inbound `
        -Priority 1000 `
        -SourceAddressPrefix * `
        -SourcePortRange * `
        -DestinationAddressPrefix * `
        -DestinationPortRange 3389 `
        -Access Allow

   $nsg = New-AzureRmNetworkSecurityGroup `
        -ResourceGroupName myResourceGroupAvailability `
        -Location EastUS `
        -Name myNetworkSecurityGroup$i `
        -SecurityRules $nsgRuleRDP

   $nic = New-AzureRmNetworkInterface `
        -Name myNic$i `
        -ResourceGroupName myResourceGroupAvailability `
        -Location EastUS `
        -SubnetId $vnet.Subnets[0].Id `
        -PublicIpAddressId $pip.Id `
        -NetworkSecurityGroupId $nsg.Id

   # Here is where we specify the availability set
   $vm = New-AzureRmVMConfig `
        -VMName myVM$i `
        -VMSize Standard_D1 `
        -AvailabilitySetId $availabilitySet.Id

   $vm = Set-AzureRmVMOperatingSystem `
        -VM $vm `
        -Windows -ComputerName myVM$i `   
        -Credential $cred `
        -ProvisionVMAgent `
        -EnableAutoUpdate
   $vm = Set-AzureRmVMSourceImage `
        -VM $vm `
        -PublisherName MicrosoftWindowsServer `
        -Offer WindowsServer `
        -Skus 2016-Datacenter `
        -Version latest
   $vm = Set-AzureRmVMOSDisk `
        -VM $vm `
        -Name myOsDisk$i `
        -DiskSizeInGB 128 `
        -CreateOption FromImage `
        -Caching ReadWrite
   $vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
   New-AzureRmVM `
        -ResourceGroupName myResourceGroupAvailability `
        -Location EastUS `
        -VM $vm
}

```

La création et la configuration des deux machines virtuelles prennent quelques minutes. Vous disposez ensuite de 2 machines virtuelles réparties sur le matériel sous-jacent. 

## <a name="check-for-available-vm-sizes"></a>Vérifier les tailles de machines virtuelles disponibles 

Vous pouvez ajouter ultérieurement d’autres machines virtuelles au groupe à haute disponibilité, mais vous devez connaître les tailles des machines virtuelles qui sont disponibles sur le matériel. Utilisez [Get-AzureRMVMSize](/powershell/module/azurerm.compute/get-azurermvmsize) pour répertorier toutes les tailles disponibles sur le cluster matériel pour le groupe à haute disponibilité.

```powershell
Get-AzureRmVMSize `
   -AvailabilitySetName myAvailabilitySet `
   -ResourceGroupName myResourceGroupAvailability  
```

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez appris à créer un groupe de machines virtuelles identiques. Passez au tutoriel suivant pour en savoir plus sur les groupes de machines virtuelles identiques.

[Créer un groupe de machines virtuelles identiques](tutorial-create-vmss.md)



