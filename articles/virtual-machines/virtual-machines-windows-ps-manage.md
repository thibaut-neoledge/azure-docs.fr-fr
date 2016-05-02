<properties
	pageTitle="Gérer des machines virtuelles à l’aide du Gestionnaire de ressources et PowerShell | Microsoft Azure"
	description="Gestion des machines virtuelles à l’aide de modèles Azure Resource Manager et de PowerShell"
	services="virtual-machines-windows"
	documentationCenter=""
	authors="davidmu1"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="na"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/18/2016"
	ms.author="davidmu"/>

# Gestion des machines virtuelles Azure à l’aide de modèles Resource Manager et de PowerShell

## Installation d'Azure PowerShell
 
Consultez [Installation et configuration d’Azure PowerShell](../powershell-install-configure.md) pour plus d'informations sur l’installation de la dernière version d'Azure PowerShell, sélectionnez l’abonnement vous souhaitez utiliser et connectez-vous à votre compte Azure.

## Définition des variables

Toutes les commandes de l'article nécessitent le nom du groupe de ressources dans lequel se trouve la machine virtuelle et le nom de la machine virtuelle à gérer. Remplacez la valeur de **$rgName** avec le nom du groupe de ressources qui contient la machine virtuelle. Remplacez la valeur de **$vmName** avec le nom de la machine virtuelle. Créez les variables.

        $rgName = "resource group name"
        $vmName = "VM name"

## Affichage des informations relatives à une machine virtuelle

Obtenez les informations relatives à la machine virtuelle.
  
        Get-AzureRmVM -ResourceGroupName $rgName -Name $vmName

Le résultat suivant doit s’afficher :

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

## Démarrage d'une machine virtuelle

Démarrez la machine virtuelle.

        Start-AzureRmVM -ResourceGroupName $rgName -Name $vmName

Le résultat suivant doit s’afficher après quelques minutes :

        RequestId  IsSuccessStatusCode  StatusCode  ReasonPhrase
        ---------  -------------------  ----------  ------------
                                  True          OK  OK

## Arrêt d’une machine virtuelle

Arrêtez la machine virtuelle.

	    Stop-AzureRmVM -ResourceGroupName $rgName -Name $vmName

Vous êtes invité à confirmer l’opération :

        Virtual machine stopping operation
        This cmdlet will stop the specified virtual machine. Do you want to continue?
        [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):
        
Saisissez **Y** pour arrêter la machine virtuelle.

Le résultat suivant doit s’afficher après quelques minutes :

        RequestId  IsSuccessStatusCode  StatusCode  ReasonPhrase
        ---------  -------------------  ----------  ------------
                                  True          OK  OK

## Redémarrage d’une machine virtuelle

Redémarrez la machine virtuelle.

        $rgName = "resource group name"
        $vmName = "VM name"
        Restart-AzureRmVM -ResourceGroupName $rgName -Name $vmName

Le résultat suivant doit s’afficher :

        RequestId  IsSuccessStatusCode  StatusCode  ReasonPhrase
        ---------  -------------------  ----------  ------------
                                  True          OK  OK

## Suppression d'une machine virtuelle

Supprimez la machine virtuelle.

        $rgName = "resource group name"
        $vmName = "VM name"
	    Remove-AzureRmVM -ResourceGroupName $rgName –Name $vmName

> [AZURE.NOTE] Utilisez le paramètre **-Force** pour ignorer l’invite de confirmation.

Vous êtes invité à confirmer si vous n’avez pas utilisé pas le paramètre -Force :

	    Virtual machine removal operation
	    This cmdlet will remove the specified virtual machine. Do you want to continue?
	    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):

Le résultat suivant doit s’afficher :

        RequestId  IsSuccessStatusCode  StatusCode  ReasonPhrase
        ---------  -------------------  ----------  ------------
                                  True          OK  OK

## Mise à jour d’une machine virtuelle

Cet exemple montre comment mettre à jour la taille de la machine virtuelle.
        
        $vmSize = "Standard_A1"
        $vm = Get-AzureRmVM -ResourceGroupName $rgName -Name $vmName
        $vm.HardwareProfile.vmSize = $vmSize
        Update-AzureRmVM -ResourceGroupName $rgName -VM $vm
    
    See [Sizes for virtual machines in Azure](virtual-machines-windows-sizes.md) for a list of available sizes for a virtual machine.

Le résultat suivant doit s’afficher :

        RequestId  IsSuccessStatusCode  StatusCode  ReasonPhrase
        ---------  -------------------  ----------  ------------
                                  True          OK  OK

## Étapes suivantes

Si vous rencontrez des problèmes avec un déploiement, vous pouvez consulter [Résolution des problèmes liés aux déploiements de groupes de ressources avec le portail Azure](../resource-manager-troubleshoot-deployments-portal.md)

<!---HONumber=AcomDC_0420_2016-->