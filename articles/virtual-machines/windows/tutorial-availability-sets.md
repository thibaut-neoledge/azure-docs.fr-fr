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
ms.date: 05/08/2017
ms.author: cynthn
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: 18d4994f303a11e9ce2d07bc1124aaedf570fc82
ms.openlocfilehash: 022396a8bf0478414be179b9f7341a459ed2bc60
ms.contentlocale: fr-fr
ms.lasthandoff: 05/09/2017

---

# <a name="how-to-use-availability-sets"></a>Utilisation des groupes à haute disponibilité

Ce didacticiel explique comment améliorer la disponibilité et la fiabilité de vos solutions de machine virtuelle sur Azure en utilisant une fonctionnalité appelée Groupes à haute disponibilité. Les groupes à haute disponibilité veillent à ce que les machines virtuelles que vous déployez sur Azure soient distribuées sur plusieurs clusters matériels isolés. Cela garantit que, si une défaillance matérielle ou logicielle se produit dans Azure, seul un sous-ensemble de vos machines virtuelles soit concerné et que votre solution globale reste disponible et opérationnelle dans la perspective des clients qui l’utilisent. 

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Créer un groupe à haute disponibilité
> * Créer une machine virtuelle dans un groupe à haute disponibilité
> * Vérifier les tailles de machines virtuelles disponibles

Ce didacticiel requiert le module Azure PowerShell version 3.6 ou ultérieure. Exécutez ` Get-Module -ListAvailable AzureRM` pour trouver la version. Si vous devez effectuer une mise à niveau, consultez [Installer le module Azure PowerShell](/powershell/azure/install-azurerm-ps).

## <a name="availability-set-overview"></a>Vue d’ensemble des groupes à haute disponibilité

Un groupe à haute disponibilité est une fonctionnalité de regroupement logique que vous pouvez utiliser dans Azure pour vous assurer que les ressources de machine virtuelle que vous y incluez sont isolées les unes des autres lors de leur déploiement dans un centre de données Azure. Azure veille à ce que les machines virtuelles que vous placez dans un groupe à haute disponibilité s’exécutent sur plusieurs serveurs physiques, racks de calcul, unités de stockage et commutateurs réseau. Cela garantit qu’en cas de panne matérielle ou logicielle d’Azure, seul un sous-ensemble de vos machines virtuelles est affecté, et que votre application globale reste opérationnelle et disponible pour vos clients. L’utilisation de groupes à haute disponibilité est essentielle pour créer des solutions cloud fiables.

Prenons l’exemple d’une solution basée sur une machine virtuelle classique dans laquelle vous disposez de 4 serveurs web frontaux et utilisez 2 machines virtuelles principales hébergeant une base de données. Avec Azure, vous pouvez définir deux groupes à haute disponibilité avant de déployer vos machines virtuelles : l’un pour le niveau « web » et l’autre pour le niveau « base de données ». Lorsque vous créez une machine virtuelle, vous pouvez spécifier le groupe à haute disponibilité en tant que paramètre pour la commande az vm create, de sorte qu’Azure veille automatiquement à ce que les machines virtuelles que vous créez dans le groupe disponible soient isolées sur plusieurs ressources matérielles. Cela signifie que, si le matériel sur lequel s’exécute l’une de vos machines virtuelles serveur web ou serveur de base de données rencontre un problème, vous savez que les autres instances de votre serveur web et de vos machines virtuelles de base de données continueront à s’exécuter correctement parce qu’elles se trouvent sur d’autres éléments matériels.

Vous devriez toujours utiliser des groupes à haute disponibilité lorsque vous souhaitez déployer des solutions fiables basées sur des machines virtuelles dans Azure.

## <a name="create-an-availability-set"></a>Créer un groupe à haute disponibilité

Vous pouvez créez un groupe à haute disponibilité avec la commande [New-AzureRmAvailabilitySet](/powershell/module/azurerm.compute/new-azurermavailabilityset). Dans cet exemple, nous définissons le nombre de domaines de mise à jour et d’erreur sur *2* pour le groupe à haute disponibilité nommé *myAvailabilitySet* dans le groupe de ressources *myResourceGroupAvailability*.

Créez un groupe de ressources.

```powershell
New-AzureRmResourceGroup -Name myResourceGroupAvailability -Location EastUS
```


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

Dans ce didacticiel, vous avez appris à :

> [!div class="checklist"]
> * Créer un groupe à haute disponibilité
> * Créer une machine virtuelle dans un groupe à haute disponibilité
> * Vérifier les tailles de machines virtuelles disponibles

Passez au didacticiel suivant pour en savoir plus sur les groupes de machines virtuelles identiques.

> [!div class="nextstepaction"]
> [Créer un groupe de machines virtuelles identiques](tutorial-create-vmss.md)



