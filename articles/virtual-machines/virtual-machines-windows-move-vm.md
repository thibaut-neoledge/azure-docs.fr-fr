---
title: Déplacement d’une machine virtuelle Windows | Microsoft Docs
description: Déplacement d’une machine virtuelle Windows vers un autre abonnement ou groupe de ressources Azure dans le modèle de déploiement Resource Manager.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: timlt
editor: ''
tags: azure-resource-manager

ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/08/2016
ms.author: cynthn

---
# Déplacement d’une machine virtuelle Windows vers un autre abonnement ou groupe de ressources Azure
Cet article vous guide tout au long du déplacement d’une machine virtuelle Windows entre des groupes de ressources ou des abonnements. Le déplacement entre abonnements peut être pratique si, à l’origine, vous avez créé une machine virtuelle dans un abonnement personnel, et que vous souhaitez à présent la déplacer vers l’abonnement de votre entreprise afin de poursuivre votre travail.

> [!NOTE]
> De nouveaux ID de ressource seront créés dans le cadre du déplacement. Une fois que la machine virtuelle a été déplacée, vous devez mettre à jour vos outils et vos scripts pour utiliser les nouveaux ID de ressource.
> 
> 

[!INCLUDE [virtual-machines-common-move-vm](../../includes/virtual-machines-common-move-vm.md)]

## Utilisation de PowerShell pour déplacer une machine virtuelle
Pour déplacer une machine virtuelle vers un autre groupe de ressources, vous devez vous assurer que vous déplacez également toutes les ressources dépendantes. Pour utiliser l’applet de commande Move-AzureRMResource, vous avez besoin du nom et du type des ressources. Pour cela, vous pouvez utiliser l’applet de commande Find-AzureRMResource.

    Find-AzureRMResource -ResourceGroupNameContains "<sourceResourceGroupName>"


Pour déplacer une machine virtuelle, nous devons déplacer plusieurs ressources. Nous pouvons simplement créer des variables distinctes pour chaque ressource, puis les répertorier. Cet exemple inclut la plupart des ressources de base pour une machine virtuelle, mais vous pouvez en ajouter d’autres en fonction des besoins.

    $sourceRG = "<sourceResourceGroupName>"
    $destinationRG = "<destinationResourceGroupName>"

    $vm = Get-AzureRmResource -ResourceGroupName $sourceRG -ResourceType "Microsoft.Compute/virtualMachines" -ResourceName "<vmName>"
    $storageAccount = Get-AzureRmResource -ResourceGroupName $sourceRG -ResourceType "Microsoft.Storage/storageAccounts" -ResourceName "<storageAccountName>"
    $diagStorageAccount = Get-AzureRmResource -ResourceGroupName $sourceRG -ResourceType "Microsoft.Storage/storageAccounts" -ResourceName "<diagnosticStorageAccountName>"
    $vNet = Get-AzureRmResource -ResourceGroupName $sourceRG -ResourceType "Microsoft.Network/virtualNetworks" -ResourceName "<vNetName>"
    $nic = Get-AzureRmResource -ResourceGroupName $sourceRG -ResourceType "Microsoft.Network/networkInterfaces" -ResourceName "<nicName>"
    $ip = Get-AzureRmResource -ResourceGroupName $sourceRG -ResourceType "Microsoft.Network/publicIPAddresses" -ResourceName "<ipName>"
    $nsg = Get-AzureRmResource -ResourceGroupName $sourceRG -ResourceType "Microsoft.Network/networkSecurityGroups" -ResourceName "<nsgName>"

    Move-AzureRmResource -DestinationResourceGroupName $destinationRG -ResourceId $vm.ResourceId, $storageAccount.ResourceId, $diagStorageAccount.ResourceId, $vNet.ResourceId, $nic.ResourceId, $ip.ResourceId, $nsg.ResourceId

Pour déplacer les ressources vers un autre abonnement, incluez le paramètre **DestinationSubscriptionId**.

    Move-AzureRmResource -DestinationSubscriptionId "<destinationSubscriptionID>" -DestinationResourceGroupName $destinationRG -ResourceId $vm.ResourceId, $storageAccount.ResourceId, $diagStorageAccount.ResourceId, $vNet.ResourceId, $nic.ResourceId, $ip.ResourceId, $nsg.ResourceId



Vous devrez confirmer que vous souhaitez déplacer les ressources spécifiées. Tapez **Y** pour confirmer que vous souhaitez déplacer les ressources.

## Étapes suivantes
Vous pouvez déplacer de nombreux types de ressources différents entre les groupes de ressources et les abonnements. Pour plus d’informations, consultez la page [Déplacement de ressources vers un nouveau groupe de ressources ou un abonnement](../resource-group-move-resources.md).

<!---HONumber=AcomDC_0810_2016-->