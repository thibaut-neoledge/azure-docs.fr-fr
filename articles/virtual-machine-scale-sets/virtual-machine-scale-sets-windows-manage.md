---
title: "Gérer des machines virtuelles dans un groupe de machines virtuelles identiques | Microsoft Docs"
description: "Gérez des machines virtuelles dans un jeu de mise à l’échelle de machine virtuelle à l&quot;aide d&quot;Azure PowerShell."
services: virtual-machine-scale-sets
documentationcenter: 
author: Thraka
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: d35fa77a-de96-4ccd-a332-eb181d1f4273
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/27/2016
ms.author: adegeo
ms.translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: 88541f6054df1476866d68fdc1c9690a73ada83c
ms.contentlocale: fr-fr
ms.lasthandoff: 04/27/2017


---
# <a name="manage-virtual-machines-in-a-virtual-machine-scale-set"></a>Gérer des machines virtuelles dans un groupe de machines virtuelles identiques
Utilisez les tâches décrites dans cet article pour gérer les machines virtuelles de votre groupe de machines virtuelles identiques.

La plupart des tâches qui impliquent la gestion d’une machine virtuelle dans un groupe identique nécessitent de connaître l’ID d’instance de la machine que vous souhaitez gérer. Vous pouvez utiliser [Azure Resource Explorer](https://resources.azure.com) pour rechercher l'ID d'instance d'une machine virtuelle dans un jeu de mise à l'échelle. Vous utilisez également Resource Explorer pour vérifier l'état des tâches que vous avez terminées.

Pour plus d’informations sur l’installation de la version la plus récente d’Azure PowerShell, la sélection de votre abonnement et la connexion à votre compte, consultez [Installation et configuration d’Azure PowerShell](/powershell/azure/overview).

## <a name="display-information-about-a-scale-set"></a>Affichage des informations relatives à un groupe identique
Vous pouvez obtenir des informations générales sur un jeu de mise à l'échelle, également appelé la vue d'instance. Ou vous pouvez obtenir des informations plus spécifiques, par exemple des informations sur les ressources du groupe identique.

Remplacez les valeurs entre guillemets par le nom de votre groupe de ressources et de votre groupe identique, puis exécutez la commande :

    Get-AzureRmVmss -ResourceGroupName "resource group name" -VMScaleSetName "scale set name"

Le résultat suivant doit s’afficher :

    Id                                          : /subscriptions/{sub-id}/resourceGroups/myrg1/providers/Microsoft.Compute/virtualMachineScaleSets/myvmss1
    Name                                        : myvmss1
    Type                                        : Microsoft.Compute/virtualMachineScaleSets
    Location                                    : centralus
    Sku                                         :
      Name                                      : Standard_A0
      Tier                                      : Standard
      Capacity                                  : 3
    UpgradePolicy                               :
      Mode                                      : Manual
    VirtualMachineProfile                       :
      OsProfile                                 :
        ComputerNamePrefix                      : vmss1
        AdminUsername                           : admin1
        WindowsConfiguration                    :
          ProvisionVMAgent                      : True
          EnableAutomaticUpdates                : True
    StorageProfile                              :
      ImageReference                            :
        Publisher                               : MicrosoftWindowsServer
        Offer                                   : WindowsServer
        Sku                                     : 2012-R2-Datacenter
        Version                                 : latest
      OsDisk                                    :
        Name                                    : vmssosdisk
        Caching                                 : ReadOnly
        CreateOption                            : FromImage
        VhdContainers[0]                        : https://astore.blob.core.windows.net/vmss
        VhdContainers[1]                        : https://gstore.blob.core.windows.net/vmss
        VhdContainers[2]                        : https://mstore.blob.core.windows.net/vmss
        VhdContainers[3]                        : https://sstore.blob.core.windows.net/vmss
        VhdContainers[4]                        : https://ystore.blob.core.windows.net/vmss
    NetworkProfile                              :
      NetworkInterfaceConfigurations[0]         :
        Name                                    : mync1
        Primary                                 : True
        IpConfigurations[0]                     :
          Name                                  : ip1
          Subnet                                :
            Id                                  : /subscriptions/{sub-id}/resourceGroups/myrg1/providers/Microsoft.Network/virtualNetworks/myvn1/subnets/mysn1
          LoadBalancerBackendAddressPools[0]    :
            Id                                  : /subscriptions/{sub-id}/resourceGroups/myrg1/providers/Microsoft.Network/loadBalancers/mylb1/backendAddressPools/bepool1
        LoadBalancerInboundNatPools[0]          :
            Id                                  : /subscriptions/{sub-id}/resourceGroups/myrg1/providers/Microsoft.Network/loadBalancers/mylb1/inboundNatPools/natpool1
    ExtensionProfile                            :
      Extensions[0]                             :
        Name                                    : Microsoft.Insights.VMDiagnosticsSettings
        Publisher                               : Microsoft.Azure.Diagnostics
        Type                                    : IaaSDiagnostics
        TypeHandlerVersion                      : 1.5
        AutoUpgradeMinorVersion                 : True
        Settings                                : {"xmlCfg":"...","storageAccount":"astore"}
    ProvisioningState                           : Succeeded

Remplacez les valeurs entre guillemets par le nom de votre groupe de ressources et de votre groupe identique. Remplacez *#* par l’identificateur d’instance de la machine virtuelle pour laquelle vous souhaitez obtenir des informations, puis exécutez la commande :

    Get-AzureRmVmssVM -ResourceGroupName "resource group name" -VMScaleSetName "scale set name" -InstanceId #

Un résultat de ce type est renvoyé :

    Id                            : /subscriptions/{sub-id}/resourceGroups/myrg1/providers/Microsoft.Compute/
                                    virtualMachineScaleSets/myvmss1/virtualMachines/0
    Name                          : myvmss1_0
    Type                          : Microsoft.Compute/virtualMachineScaleSets/virtualMachines
    Location                      : centralus
    InstanceId                    : 0
    Sku                           :
      Name                        : Standard_A0
      Tier                        : Standard
    LatestModelApplied            : True
    StorageProfile                :
      ImageReference              :
        Publisher                 : MicrosoftWindowsServer
        Offer                     : WindowsServer
        Sku                       : 2012-R2-Datacenter
        Version                   : 4.0.20160617
      OsDisk                      :
        OsType                    : Windows
        Name                      : vmssosdisk-os-0-e11cad52959b4b76a8d9f26c5190c4f8
        Vhd                       :
          Uri                     : https://astore.blob.core.windows.net/vmss/vmssosdisk-os-0-e11cad52959b4b76a8d9f26c5190c4f8.vhd
        Caching                   : ReadOnly
        CreateOption              : FromImage
    OsProfile                     :
      ComputerName                : myvmss1-0
      AdminUsername               : admin1
      WindowsConfiguration        :
        ProvisionVMAgent          : True
        EnableAutomaticUpdates    : True
    NetworkProfile                :
      NetworkInterfaces[0]        :
        Id                        : /subscriptions/{sub-id}/resourceGroups/myrg1/providers/Microsoft.Compute/virtualMachineScaleSets/
                                    myvmss1/virtualMachines/0/networkInterfaces/mync1
    ProvisioningState             : Succeeded
    Resources[0]                  :
      Id                          : /subscriptions/{sub-id}/resourceGroups/myrg1/providers/Microsoft.Compute/virtualMachines/
                                    myvmss1_0/extensions/Microsoft.Insights.VMDiagnosticsSettings
      Name                        : Microsoft.Insights.VMDiagnosticsSettings
      Type                        : Microsoft.Compute/virtualMachines/extensions
      Location                    : centralus
      Publisher                   : Microsoft.Azure.Diagnostics
      VirtualMachineExtensionType : IaaSDiagnostics
      TypeHandlerVersion          : 1.5
      AutoUpgradeMinorVersion     : True
      Settings                    : {"xmlCfg":"...","storageAccount":"astore"}
      ProvisioningState           : Succeeded

## <a name="start-a-virtual-machine-in-a-scale-set"></a>Démarrer une machine virtuelle dans un jeu de mise à l’échelle
Remplacez les valeurs entre guillemets par le nom de votre groupe de ressources et de votre groupe identique. Remplacez *#* par l’identificateur de la machine virtuelle que vous souhaitez lancer, puis exécutez la commande :

    Start-AzureRmVmss -ResourceGroupName "resource group name" -VMScaleSetName "scale set name" -InstanceId #

Dans Resource Explorer, nous pouvons voir que l'état de l'instance indique **running**(en cours d’exécution) :

    "statuses": [
      {
        "code": "ProvisioningState/succeeded",
        "level": "Info",
        "displayStatus": "Provisioning succeeded",
        "time": "2016-03-15T02:10:08.0730839+00:00"
      },
      {
        "code": "PowerState/running",
        "level": "Info",
        "displayStatus": "VM running"
      }
    ]

Vous pouvez démarrer toutes les machines virtuelles du groupe identique en enlevant le paramètre - InstanceId.

## <a name="stop-a-virtual-machine-in-a-scale-set"></a>Arrêter une machine virtuelle dans un jeu de mise à l’échelle
Remplacez les valeurs entre guillemets par le nom de votre groupe de ressources et de votre groupe identique. Remplacez *#* par l’identificateur de la machine virtuelle que vous souhaitez arrêter, puis exécutez la commande :

    Stop-AzureRmVmss -ResourceGroupName "resource group name" -VMScaleSetName "scale set name" -InstanceId #

Dans Resource Explorer, nous pouvons voir que l'état de l'instance indique **deallocated**(libéré) :

    "statuses": [
      {
        "code": "ProvisioningState/succeeded",
        "level": "Info",
        "displayStatus": "Provisioning succeeded",
        "time": "2016-03-15T01:25:17.8792929+00:00"
      },
      {
        "code": "PowerState/deallocated",
        "level": "Info",
        "displayStatus": "VM deallocated"
      }
    ]

Pour arrêter une machine virtuelle sans la libérer, utilisez le paramètre - StayProvisioned. Vous pouvez arrêter toutes les machines virtuelles du groupe en enlevant le paramètre - InstanceId.

## <a name="restart-a-virtual-machine-in-a-scale-set"></a>Redémarrer une machine virtuelle dans un jeu de mise à l’échelle
Remplacez les valeurs entre guillemets par le nom de votre groupe de ressources et du groupe identique. Remplacez *#* par l’identificateur de la machine virtuelle que vous souhaitez relancer, puis exécutez la commande :

    Restart-AzureRmVmss -ResourceGroupName "resource group name" -VMScaleSetName "scale set name" -InstanceId #

Vous pouvez redémarrer toutes les machines virtuelles du groupe en enlevant le paramètre - InstanceId.

## <a name="remove-a-virtual-machine-from-a-scale-set"></a>Supprimer une machine virtuelle d’un jeu de mise à l’échelle
Remplacez les valeurs entre guillemets par le nom de votre groupe de ressources et du groupe identique. Remplacez *#* par l’identificateur de la machine virtuelle que vous souhaitez supprimer, puis exécutez la commande :  

    Remove-AzureRmVmss -ResourceGroupName "resource group name" –VMScaleSetName "scale set name" -InstanceId #

Vous pouvez supprimer simultanément tous les jeux de mise à l’échelle de machine virtuelle en n’utilisant ne pas le paramètre - InstanceId.

## <a name="change-the-capacity-of-a-scale-set"></a>Modifier la capacité d’un groupe identique
Vous pouvez ajouter ou supprimer des machines virtuelles en modifiant la capacité du groupe. Récupérez le groupe identique que vous souhaitez modifier, définissez la capacité voulue, puis mettez-le à jour avec la nouvelle capacité. Dans ces commandes, remplacez les valeurs entre guillemets par le nom de votre groupe de ressources et du groupe identique.

    $vmss = Get-AzureRmVmss -ResourceGroupName "resource group name" -VMScaleSetName "scale set name"
    $vmss.sku.capacity = 5
    Update-AzureRmVmss -ResourceGroupName "resource group name" -Name "scale set name" -VirtualMachineScaleSet $vmss 

Si vous supprimez des machines virtuelles du groupe identique, les machines virtuelles possédant les ID le plus élevés sont supprimées en premier.


