---
title: "Gérer des machines virtuelles à l’aide du Gestionnaire de ressources et PowerShell | Microsoft Docs"
description: "Gestion des machines virtuelles à l’aide de modèles Azure Resource Manager et de PowerShell"
services: virtual-machines-windows
documentationcenter: 
author: davidmu1
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 48930854-7888-4e4c-9efb-7d1971d4cc14
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 09/27/2016
ms.author: davidmu
translationtype: Human Translation
ms.sourcegitcommit: 5919c477502767a32c535ace4ae4e9dffae4f44b
ms.openlocfilehash: 9671e22d6c027c589e229ae2ecbbda71f0bb7e1c


---
# <a name="manage-azure-virtual-machines-using-resource-manager-and-powershell"></a>Gestion des machines virtuelles Azure à l’aide de modèles Resource Manager et de PowerShell
## <a name="install-azure-powershell"></a>Installation d'Azure Powershell
Pour plus d’informations sur l’installation de la version la plus récente d’Azure PowerShell, la sélection de votre abonnement et la connexion à votre compte, consultez [Installation et configuration d’Azure PowerShell](../powershell-install-configure.md).

## <a name="set-variables"></a>Définition des variables
Toutes les commandes de l’article nécessitent le nom du groupe de ressources dans lequel se trouve la machine virtuelle et le nom de la machine virtuelle à gérer. Remplacez la valeur de **$rgName** avec le nom du groupe de ressources qui contient la machine virtuelle. Remplacez la valeur de **$vmName** avec le nom de la machine virtuelle. Créez les variables.

    $rgName = "resource-group-name"
    $vmName = "VM-name"

## <a name="display-information-about-a-virtual-machine"></a>Affichage des informations relatives à une machine virtuelle
Obtenez les informations relatives à la machine virtuelle.

    Get-AzureRmVM -ResourceGroupName $rgName -Name $vmName

Un résultat tel que cet exemple est renvoyé :

    ResourceGroupName        : rg1
    Id                       : /subscriptions/{subscription-id}/resourceGroups/
                               rg1/providers/Microsoft.Compute/virtualMachines/vm1
    Name                     : vm1
    Type                     : Microsoft.Compute/virtualMachines
    Location                 : centralus
    Tags                     : {}
    AvailabilitySetReference : {
                                  "id": "/subscriptions/{subscription-id}/resourceGroups/
                                  rg1/providers/Microsoft.Compute/availabilitySets/av1"
                               }
    Extensions               : []
    HardwareProfile          : {
                                  "vmSize": "Standard_A0"
                               }
    InstanceView             : null
    NetworkProfile           : {
                                  "networkInterfaces": [
                                    {
                                      "properties.primary": null,
                                      "id": "/subscriptions/{subscription-id}/resourceGroups/
                                      rg1/providers/Microsoft.Network/networkInterfaces/nc1"
                                    }
                                  ]
                               }
    OSProfile                : {
                                  "computerName": "vm1",
                                  "adminUsername": "myaccount1",
                                  "adminPassword": null,
                                  "customData": null,
                                  "windowsConfiguration": {
                                    "provisionVMAgent": true,
                                    "enableAutomaticUpdates": true,
                                    "timeZone": null,
                                    "additionalUnattendContents": [],
                                    "winRM": null
                                  },
                                  "linuxConfiguration": null,
                                  "secrets": []
                               }
    Plan                     : null
    ProvisioningState        : Succeeded
    StorageProfile           : {
                                  "imageReference": {
                                    "publisher": "MicrosoftWindowsServer",
                                    "offer": "WindowsServer",
                                    "sku": "2012-R2-Datacenter",
                                    "version": "latest"
                                  },
                                  "osDisk": {
                                    "osType": "Windows",
                                    "encryptionSettings": null,
                                    "name": "osdisk",
                                    "vhd": {
                                      "Uri": "http://sa1.blob.core.windows.net/vhds/osdisk1.vhd"
                                    }
                                    "image": null,
                                    "caching": "ReadWrite",
                                    "createOption": "FromImage"
                                  }
                                  "dataDisks": [],
                               }
    DataDiskNames            : {}
    NetworkInterfaceIDs      : {/subscriptions/{subscription-id}/resourceGroups/
                                rg1/providers/Microsoft.Network/networkInterfaces/nc1}

## <a name="stop-a-virtual-machine"></a>Arrêt d’une machine virtuelle
Arrêtez la machine virtuelle en cours d’exécution.

    Stop-AzureRmVM -ResourceGroupName $rgName -Name $vmName

Vous êtes invité à confirmer l’opération :

    Virtual machine stopping operation
    This cmdlet will stop the specified virtual machine. Do you want to continue?
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):

Saisissez **Y** pour arrêter la machine virtuelle.

Un résultat tel que cet exemple doit s’afficher après quelques minutes :

    StatusCode : Succeeded
    StartTime  : 9/13/2016 12:11:57 PM
    EndTime    : 9/13/2016 12:14:40 PM

## <a name="start-a-virtual-machine"></a>Démarrage d'une machine virtuelle
Démarrez la machine virtuelle si elle est arrêtée.

    Start-AzureRmVM -ResourceGroupName $rgName -Name $vmName

Un résultat tel que cet exemple doit s’afficher après quelques minutes :

    StatusCode : Succeeded
    StartTime  : 9/13/2016 12:32:55 PM
    EndTime    : 9/13/2016 12:35:09 PM

Si vous souhaitez redémarrer une machine virtuelle en cours d’exécution, utilisez la commande **Restart-AzureRmVM** décrite ci-après.

## <a name="restart-a-virtual-machine"></a>Redémarrage d’une machine virtuelle
Redémarrez la machine virtuelle en cours d’exécution.

    Restart-AzureRmVM -ResourceGroupName $rgName -Name $vmName

Un résultat tel que cet exemple est renvoyé :

    StatusCode : Succeeded
    StartTime  : 9/13/2016 12:54:40 PM
    EndTime    : 9/13/2016 12:55:54 PM

## <a name="delete-a-virtual-machine"></a>Suppression d'une machine virtuelle
Supprimez la machine virtuelle.  

    Remove-AzureRmVM -ResourceGroupName $rgName –Name $vmName

> [!NOTE]
> Utilisez le paramètre **-Force** pour ignorer l’invite de confirmation.
> 
> 

Si vous n’avez pas utilisé le paramètre -Force, vous êtes invité à confirmer :

    Virtual machine removal operation
    This cmdlet will remove the specified virtual machine. Do you want to continue?
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):

Un résultat tel que cet exemple est renvoyé :

    RequestId  IsSuccessStatusCode  StatusCode  ReasonPhrase
    ---------  -------------------  ----------  ------------
                              True          OK  OK

## <a name="update-a-virtual-machine"></a>Mise à jour d’une machine virtuelle
Cet exemple montre comment mettre à jour la taille de la machine virtuelle.

    $vmSize = "Standard_A1"
    $vm = Get-AzureRmVM -ResourceGroupName $rgName -Name $vmName
    $vm.HardwareProfile.vmSize = $vmSize
    Update-AzureRmVM -ResourceGroupName $rgName -VM $vm

Un résultat tel que cet exemple est renvoyé :

    RequestId  IsSuccessStatusCode  StatusCode  ReasonPhrase
    ---------  -------------------  ----------  ------------
                              True          OK  OK

Consultez la rubrique [Tailles des machines virtuelles dans Azure](virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) pour obtenir la liste des tailles disponibles pour une machine virtuelle.

## <a name="add-a-data-disk-to-a-virtual-machine"></a>Ajout d’un disque de données à une machine virtuelle
Cet exemple montre comment ajouter un disque de données à une machine virtuelle existante.

    $vm = Get-AzureRmVM -ResourceGroupName $rgName -Name $vmName
    Add-AzureRmVMDataDisk -VM $vm -Name "disk-name" -VhdUri "https://mystore1.blob.core.windows.net/vhds/datadisk1.vhd" -LUN 0 -Caching ReadWrite -DiskSizeinGB 1 -CreateOption Empty
    Update-AzureRmVM -ResourceGroupName $rgName -VM $vm

Le disque que vous ajoutez n’est pas initialisé. Pour initialiser le disque, vous pouvez vous connecter utiliser la gestion des disques. Si vous avez installé WinRM et un certificat sur ce dernier lors de sa création, vous pouvez utiliser PowerShell à distance pour initialiser le disque. Vous pouvez également utiliser une extension de script personnalisée : 

    $location = "location-name"
    $scriptName = "script-name"
    $fileName = "script-file-name"
    Set-AzureRmVMCustomScriptExtension -ResourceGroupName $rgName -Location $locName -VMName $vmName -Name $scriptName -TypeHandlerVersion "1.4" -StorageAccountName "mystore1" -StorageAccountKey "primary-key" -FileName $fileName -ContainerName "scripts"

Le fichier de script peut contenir les éléments de code suivants pour initialiser les disques :

    $disks = Get-Disk |   Where partitionstyle -eq 'raw' | sort number

    $letters = 70..89 | ForEach-Object { ([char]$_) }
    $count = 0
    $labels = @("data1","data2")

    foreach($d in $disks) {
        $driveLetter = $letters[$count].ToString()
        $d | 
        Initialize-Disk -PartitionStyle MBR -PassThru |
        New-Partition -UseMaximumSize -DriveLetter $driveLetter |
        Format-Volume -FileSystem NTFS -NewFileSystemLabel $labels[$count] `
            -Confirm:$false -Force 
        $count++
    }

## <a name="next-steps"></a>Étapes suivantes
Si vous rencontrez des problèmes avec un déploiement, vous pouvez consulter [Résolution des problèmes liés aux déploiements de groupes de ressources avec le portail Azure](../resource-manager-troubleshoot-deployments-portal.md).




<!--HONumber=Nov16_HO3-->


