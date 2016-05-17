<properties
	pageTitle="Gérer des machines virtuelles dans un jeu de mise à l’échelle de machine virtuelle | Microsoft Azure"
	description="Gérez des machines virtuelles dans un jeu de mise à l’échelle de machine virtuelle à l'aide d'Azure PowerShell."
	services="virtual-machine-scale-sets"
	documentationCenter=""
	authors="davidmu1"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machine-scale-sets"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/26/2016"
	ms.author="davidmu"/>

# Gérer des machines dans un jeu de mise à l’échelle de machines virtuelles

Utilisez les tâches décrites dans cet article pour gérer les ressources de machine virtuelle de votre jeu de mise à l’échelle de machine virtuelle.

Toutes les tâches qui impliquent la gestion d'une machine virtuelle dans un jeu de mise à l'échelle nécessitent de connaître l'ID d'instance de la machine que vous souhaitez gérer. Vous pouvez utiliser [Azure Resource Explorer](https://resources.azure.com) pour rechercher l'ID d'instance d'une machine virtuelle dans un jeu de mise à l'échelle. Vous utilisez également Resource Explorer pour vérifier l'état des tâches que vous avez terminées.

Consultez [Installation et configuration d’Azure PowerShell](../powershell-install-configure.md) pour savoir comment installer la dernière version d’Azure PowerShell, sélectionner l’abonnement à utiliser et vous connecter à votre compte Azure.

## Afficher les informations relatives à un jeu de mise à l’échelle de machine virtuelle

Vous pouvez obtenir des informations générales sur un jeu de mise à l'échelle, également appelé la vue d'instance. Ou vous pouvez obtenir des informations plus spécifiques, par exemple des informations sur les ressources du jeu.

Dans cette commande, remplacez le *nom de groupe de ressources* par le nom du groupe de ressources contenant le jeu de mise à l’échelle de machine virtuelle et le *nom du jeu de mise à l’échelle* par le nom du jeu de mise à l’échelle de machine virtuelle, puis exécutez la commande :

    Get-AzureRmVmss -ResourceGroupName "resource group name" -VMScaleSetName "scale set name"

Le résultat suivant doit s’afficher :

    Sku                   : Microsoft.Azure.Management.Compute.Models.Sku
    UpgradePolicy         : Microsoft.Azure.Management.Compute.Models.UpgradePolicy
    VirtualMachineProfile : Microsoft.Azure.Management.Compute.Models.VirtualMachineScaleSetVMProfile
    ProvisioningState     : Succeeded
    OverProvision         :
    Id                    : /subscriptions/{subscription-id}/resourceGroups/myrg1/providers/Microsoft.Compute/virtualMachineScaleSets/myvmss1
    Name                  : myvmss1
    Type                  : Microsoft.Compute/virtualMachineScaleSets
    Location              : centralus
    Tags                  :

Dans cette commande, remplacez le *nom de groupe de ressources* par le nom du groupe de ressources contenant le jeu de mise à l’échelle de machine virtuelle, le *nom du jeu de mise à l’échelle* par le nom du jeu de mise à l’échelle de machine virtuelle, et *#* par l’identificateur d’instance de la machine virtuelle sur laquelle vous souhaitez obtenir des informations, puis exécutez la commande :

    Get-AzureRmVmssVM -ResourceGroupName "resource group name" -VMScaleSetName "scale set name" -InstanceId #
        
Le résultat suivant doit s’afficher :

    InstanceId         : 1
    Sku                : Microsoft.Azure.Management.Compute.Models.Sku
    LatestModelApplied : True
    InstanceView       :
    HardwareProfile    :
    StorageProfile     : Microsoft.Azure.Management.Compute.Models.StorageProfile
    OsProfile          : Microsoft.Azure.Management.Compute.Models.OSProfile
    NetworkProfile     : Microsoft.Azure.Management.Compute.Models.NetworkProfile
    DiagnosticsProfile :
    AvailabilitySet    :
    ProvisioningState  : Succeeded
    LicenseType        :
    Plan               :
    Resources          :
    Id                 : /subscriptions/{subscription-id}/resourceGroups/myrg1/providers/Microsoft.
                         Compute/virtualMachineScaleSets/myvmss1/virtualMachines/1
    Name               : myvmss1_1
    Type               : Microsoft.Compute/virtualMachineScaleSets/virtualMachines
    Location           : centralus
    Tags               :
        
## Démarrer une machine virtuelle dans un jeu de mise à l’échelle

Dans cette commande, remplacez le *nom de groupe de ressources* par le nom du groupe de ressources contenant le jeu de mise à l’échelle de machine virtuelle, le *nom du jeu de mise à l’échelle* par le nom du jeu de mise à l’échelle, *#* par l’identificateur de la machine virtuelle que vous voulez démarrer, puis exécutez la commande :

    Start-AzureRmVmss -ResourceGroupName "resource group name" -VMScaleSetName "scale set name" -InstanceId #

Dans Resource Explorer, nous pouvons voir que l'état de l'instance indique **running** (en cours d’exécution) :

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

Dans cette commande, remplacez le *nom de groupe de ressources* par le nom du groupe de ressources contenant le jeu de mise à l’échelle de machine virtuelle, le *nom du jeu de mise à l’échelle* par le nom du jeu de mise à l’échelle, *#* par l’identificateur de la machine virtuelle que vous voulez arrêter, puis exécutez la commande :

	Stop-AzureRmVmss -ResourceGroupName "resource group name" -VMScaleSetName "scale set name" -InstanceId #

Dans Resource Explorer, nous pouvons voir que l'état de l'instance indique **deallocated** (libéré) :

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

Dans cette commande, remplacez le *nom de groupe de ressources* par le nom du groupe de ressources contenant le jeu de mise à l’échelle de machine virtuelle, le *nom du jeu de mise à l’échelle* par le nom du jeu de mise à l’échelle, *#* par l’identificateur de la machine virtuelle que vous voulez redémarrer, puis exécutez la commande :

	Restart-AzureRmVmss -ResourceGroupName "resource group name" -VMScaleSetName "scale set name" -InstanceId #
    
Vous pouvez redémarrer toutes les machines virtuelles dans le jeu en n’utilisant ne pas le paramètre - InstanceId.

## Supprimer une machine virtuelle d’un jeu de mise à l’échelle

Dans cette commande, remplacez le *nom de groupe de ressources* par le nom du groupe de ressources contenant le jeu de mise à l’échelle de machine virtuelle, le *nom du jeu de mise à l’échelle* par le nom du jeu de mise à l’échelle, *#* par l’identificateur de la machine virtuelle que vous voulez supprimer du jeu de mise à l’échelle, puis exécutez la commande :

	Remove-AzureRmVmss -ResourceGroupName "resource group name" –VMScaleSetName "scale set name" -InstanceId #

Vous pouvez supprimer simultanément tous les jeux de mise à l’échelle de machine virtuelle en n’utilisant ne pas le paramètre - InstanceId.

<!---HONumber=AcomDC_0504_2016-->