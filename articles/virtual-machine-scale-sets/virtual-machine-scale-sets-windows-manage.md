---
title: Gérer des machines virtuelles dans un jeu de mise à l’échelle de machine virtuelle | Microsoft Docs
description: Gérez des machines virtuelles dans un jeu de mise à l’échelle de machine virtuelle à l'aide d'Azure PowerShell.
services: virtual-machine-scale-sets
documentationcenter: ''
author: davidmu1
manager: timlt
editor: ''
tags: azure-resource-manager

ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/14/2016
ms.author: davidmu

---
# Gérer des machines dans un jeu de mise à l’échelle de machines virtuelles
Utilisez les tâches décrites dans cet article pour gérer les ressources de machine virtuelle de votre jeu de mise à l’échelle de machine virtuelle.

Toutes les tâches qui impliquent la gestion d'une machine virtuelle dans un jeu de mise à l'échelle nécessitent de connaître l'ID d'instance de la machine que vous souhaitez gérer. Vous pouvez utiliser [Azure Resource Explorer](https://resources.azure.com) pour rechercher l'ID d'instance d'une machine virtuelle dans un jeu de mise à l'échelle. Vous utilisez également Resource Explorer pour vérifier l'état des tâches que vous avez terminées.

Consultez [Installation et configuration d’Azure PowerShell](../powershell-install-configure.md) pour savoir comment installer la dernière version d’Azure PowerShell, sélectionner l’abonnement à utiliser et vous connecter à votre compte Azure.

## Afficher les informations relatives à un jeu de mise à l’échelle de machine virtuelle
Vous pouvez obtenir des informations générales sur un jeu de mise à l'échelle, également appelé la vue d'instance. Ou vous pouvez obtenir des informations plus spécifiques, par exemple des informations sur les ressources du jeu.

Dans cette commande, remplacez le *nom de groupe de ressources* par le nom du groupe de ressources contenant le jeu de mise à l’échelle de machine virtuelle et le *nom du jeu de mise à l’échelle* par le nom du jeu de mise à l’échelle de machine virtuelle, puis exécutez la commande :

    Get-AzureRmVmss -ResourceGroupName "resource group name" -VMScaleSetName "scale set name"

Le résultat suivant doit s’afficher :

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

Dans cette commande, remplacez le *nom de groupe de ressources* par le nom du groupe de ressources contenant le jeu de mise à l’échelle de machine virtuelle, le *nom du jeu de mise à l’échelle* par le nom du jeu de mise à l’échelle de machine virtuelle, et *#* par l’identificateur d’instance de la machine virtuelle sur laquelle vous souhaitez obtenir des informations, puis exécutez la commande :

    Get-AzureRmVmssVM -ResourceGroupName "resource group name" -VMScaleSetName "scale set name" -InstanceId #

Le résultat suivant doit s’afficher :

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

## Démarrer une machine virtuelle dans un jeu de mise à l’échelle
Dans cette commande, remplacez le *nom de groupe de ressources* par le nom du groupe de ressources contenant le jeu de mise à l’échelle de machine virtuelle, le *nom du jeu de mise à l’échelle* par le nom du jeu de mise à l’échelle, *#* par l’identificateur de la machine virtuelle que vous voulez démarrer, puis exécutez la commande :

    Start-AzureRmVmss -ResourceGroupName "resource group name" -VMScaleSetName "scale set name" -InstanceId #

Dans Resource Explorer, nous pouvons voir que l'état de l'instance indique **running** (en cours d’exécution) :

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

Vous pouvez démarrer toutes les machines virtuelles dans le jeu en n’utilisant ne pas le paramètre - InstanceId.

## Arrêter une machine virtuelle dans un jeu de mise à l’échelle
Dans cette commande, remplacez le *nom de groupe de ressources* par le nom du groupe de ressources contenant le jeu de mise à l’échelle de machine virtuelle, le *nom du jeu de mise à l’échelle* par le nom du jeu de mise à l’échelle, *#* par l’identificateur de la machine virtuelle que vous voulez arrêter, puis exécutez la commande :

    Stop-AzureRmVmss -ResourceGroupName "resource group name" -VMScaleSetName "scale set name" -InstanceId #

Dans Resource Explorer, nous pouvons voir que l'état de l'instance indique **deallocated** (libéré) :

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

Pour arrêter une machine virtuelle sans la libérer, utilisez le paramètre - StayProvisioned. Vous pouvez arrêter toutes les machines virtuelles dans le jeu en n’utilisant ne pas le paramètre - InstanceId.

## Redémarrer une machine virtuelle dans un jeu de mise à l’échelle
Dans cette commande, remplacez le *nom de groupe de ressources* par le nom du groupe de ressources contenant le jeu de mise à l’échelle de machine virtuelle, le *nom du jeu de mise à l’échelle* par le nom du jeu de mise à l’échelle, *#* par l’identificateur de la machine virtuelle que vous voulez redémarrer, puis exécutez la commande :

    Restart-AzureRmVmss -ResourceGroupName "resource group name" -VMScaleSetName "scale set name" -InstanceId #

Vous pouvez redémarrer toutes les machines virtuelles dans le jeu en n’utilisant ne pas le paramètre - InstanceId.

## Supprimer une machine virtuelle d’un jeu de mise à l’échelle
Dans cette commande, remplacez le *nom de groupe de ressources* par le nom du groupe de ressources contenant le jeu de mise à l’échelle de machine virtuelle, le *nom du jeu de mise à l’échelle* par le nom du jeu de mise à l’échelle, *#* par l’identificateur de la machine virtuelle que vous voulez supprimer du jeu de mise à l’échelle, puis exécutez la commande :

    Remove-AzureRmVmss -ResourceGroupName "resource group name" –VMScaleSetName "scale set name" -InstanceId #

Vous pouvez supprimer simultanément tous les jeux de mise à l’échelle de machine virtuelle en n’utilisant ne pas le paramètre - InstanceId.

<!---HONumber=AcomDC_0720_2016-->