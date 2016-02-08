<properties
	pageTitle="Gérer les machines virtuelles Azure Resource Manager | Microsoft Azure"
	description="Gestion des machines virtuelles à l’aide d’Azure Resource Manager, des modèles et PowerShell"
	services="virtual-machines"
	documentationCenter=""
	authors="davidmu1"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/07/2016"
	ms.author="davidmu"/>

# Gestion des machines virtuelles à l’aide de modèles Azure Resource Manager et de PowerShell

> [AZURE.SELECTOR]
- [PowerShell](virtual-machines-deploy-rmtemplates-powershell.md)
- [CLI](virtual-machines-deploy-rmtemplates-azure-cli.md)

<br>


L’utilisation de modèles Azure PowerShell et Gestionnaire de ressources offre davantage de puissance et de flexibilité dans Microsoft Azure. Vous pouvez utiliser les tâches décrites dans cet article pour gérer des ressources de machine virtuelle.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]Modèle de déploiement classique

Ces tâches utilisent uniquement PowerShell :

- [Afficher les informations relatives à une machine virtuelle](#displayvm)
- [Démarrer une machine virtuelle](#start)
- [Arrêter une machine virtuelle](#stop)
- [Redémarrer une machine virtuelle](#restart)
- [Supprimer une machine virtuelle](#delete)

[AZURE.INCLUDE [powershell-preview](../../includes/powershell-preview-inline-include.md)]

## <a id="displayvm"></a>Afficher les informations relatives à une machine virtuelle

Dans la commande suivante, remplacez *nom de groupe de ressources* par le nom du groupe de ressources contenant la machine virtuelle et *nom de machine virtuelle* par le nom de la machine, puis exécutez la commande :

	Get-AzureRmVM -ResourceGroupName "resource group name" -Name "VM name"

Le résultat suivant doit s’afficher :


	ResourceGroupName        : rg1
	Id                       : /subscriptions/{subscription-id}/resourceGroups/
															rg1/providers/Microsoft.Compute/virtualMachines/vm1
	Name                     : vm1
	Type                     : Microsoft.Azure.Management.Compute.Models.VirtualMachineGetResponse
	Location                 : westus
	Tags                     : {}
	AvailabilitySetReference : null
	Extensions               : []
	HardwareProfile          :  {
																"VirtualMachineSize": "Standard_D1"
															}
	InstanceView             : null
	Location                 : westus
	Name                     : vm1
	NetworkProfile           :  {
																"NetworkInterfaces": [
																	{
																		"Primary": null,
																		"ReferenceUri": "/subscriptions/{subscription-id}/resourceGroups/
																		rg1/providers/Microsoft.Network/networkInterfaces/nc1"
																	}
																]
															}
	OSProfile                :  {
																"AdminPassword": null,
																"AdminUsername": "WinAdmin1",
																"ComputerName": "vm1",
																"CustomData": null,
																"LinuxConfiguration": null,
																"Secrets": [],
																"WindowsConfiguration": {
																	"AdditionalUnattendContents": [],
																	"EnableAutomaticUpdates": true,
																	"ProvisionVMAgent": true,
																	"TimeZone": null,
																	"WinRMConfiguration": null
																}
															}
	Plan                     : null
	ProvisioningState        : Succeeded
	StorageProfile           : 	{
																"DataDisks": [],
																"ImageReference": {
																	"Offer": "WindowsServer",
																	"Publisher": "MicrosoftWindowsServer",
																	"Sku": "2012-R2-Datacenter",
																	"Version": "latest"
																},
																"OSDisk": {
																	"OperatingSystemType": "Windows",
																	"Caching": "ReadWrite",
																	"CreateOption": "FromImage",
																	"Name": "osdisk",
																	"SourceImage": null,
																	"VirtualHardDisk": {
																		"Uri": "http://sa1.blob.core.windows.net/vhds/osdisk1.vhd"
																	}
																}
															}
	DataDiskNames            :  {}
	NetworkInterfaceIDs      : { /subscriptions/{subscription-id}/resourceGroups/
																rg1/providers/Microsoft.Network/networkInterfaces/nc1}

Si vous souhaitez voir une vidéo de l’exécution de cette tâche, regardez ce qui suit :

[AZURE.VIDEO displaying-information-about-a-virtual-machine-in-microsoft-azure-with-powershell]

## <a id="start"></a>Démarrer une machine virtuelle

Dans la commande suivante, remplacez *nom de groupe de ressources* par le nom du groupe de ressources contenant la machine virtuelle et *nom de machine virtuelle* par le nom de la machine, puis exécutez la commande :

	Start-AzureRmVM -ResourceGroupName "resource group name" -Name "VM name"

Le résultat suivant doit s’afficher :

	Status              : Succeeded
	StatusCode          : OK
	RequestId           : 06935ddf-6e89-48d2-b46a-229493e3e9d1
	Output              :
	Error               :
	StartTime           : 4/28/2015 11:10:35 AM -07:00
	EndTime             : 4/28/2015 11:11:41 AM -07:00
	TrackingOperationId : c1aa0a70-4f4f-4d6c-a8ac-7ea35c004ce0

Si vous souhaitez voir une vidéo de l’exécution de cette tâche, regardez ce qui suit :

[AZURE.VIDEO start-stop-restart-and-delete-vms-in-microsoft-azure-with-powershell]

## <a id="stop"></a>Arrêter une machine virtuelle

Dans la commande suivante, remplacez *nom de groupe de ressources* par le nom du groupe de ressources contenant la machine virtuelle et *nom de machine virtuelle* par le nom de la machine, puis exécutez la commande :

	Stop-AzureRmVM -ResourceGroupName "resource group name" -Name "VM name"

Vous êtes invité à confirmer l’opération :

	Virtual machine stopping operation
	This cmdlet will stop the specified virtual machine. Do you want to continue?
	[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):

Le résultat suivant doit s’afficher :

	Status              : Succeeded
	StatusCode          : OK
	RequestId           : aac41de1-b85d-4429-9a3d-040b922d2e6d
	Output              :
	Error               :
	StartTime           : 4/28/2015 11:10:35 AM -07:00
	EndTime             : 4/28/2015 11:11:41 AM -07:00
	TrackingOperationId : e1705973-d266-467e-8655-920016145347

Si vous souhaitez voir une vidéo de l’exécution de cette tâche, regardez ce qui suit :

[AZURE.VIDEO start-stop-restart-and-delete-vms-in-microsoft-azure-with-powershell]

## <a id="restart"></a>Redémarrer une machine virtuelle

Dans la commande suivante, remplacez *nom de groupe de ressources* par le nom du groupe de ressources contenant la machine virtuelle et *nom de machine virtuelle* par le nom de la machine, puis exécutez la commande :

	Restart-AzureRmVM -ResourceGroupName "resource group name" -Name "VM name"

Le résultat suivant doit s’afficher :

	Status              : Succeeded
	StatusCode          : OK
	RequestId           : 4b05891c-fdff-4c9a-89ca-e4f1d7691aed
	Output              :
	Error               :
	StartTime           : 1/5/2016 12:06:53 PM -08:00
	EndTime             : 1/5/2016 12:06:54 PM -08:00
	TrackingOperationId : 5aeeab89-45ab-41b9-84ef-9e9a7e732207


Si vous souhaitez voir une vidéo de l’exécution de cette tâche, regardez ce qui suit :

[AZURE.VIDEO start-stop-restart-and-delete-vms-in-microsoft-azure-with-powershell]

## <a id="delete"></a>Supprimer une machine virtuelle

Dans la commande suivante, remplacez *nom de groupe de ressources* par le nom du groupe de ressources contenant la machine virtuelle et *nom de machine virtuelle* par le nom de la machine, puis exécutez la commande :

	Remove-AzureRmVM -ResourceGroupName "resource group name" –Name "VM name"

> [AZURE.NOTE] Utilisez le paramètre **-Force** pour ignorer l’invite de confirmation.

Vous êtes invité à confirmer si vous n’avez pas utilisé pas le paramètre -Force :

	Virtual machine removal operation
	This cmdlet will remove the specified virtual machine. Do you want to continue?
	[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):

Le résultat suivant doit s’afficher :

	Status              : Succeeded
	StatusCode          : OK
	RequestId           : 2d723b40-ce1f-4b11-a603-dc659a13b6f0
	Output              :
	Error               :
	StartTime           : 1/5/2016 12:10:28 PM -08:00
	EndTime             : 1/5/2016 12:12:12 PM -08:00
	TrackingOperationId : d138ab29-83bf-4948-9d13-dab87db1a639

Si vous souhaitez voir une vidéo de l’exécution de cette tâche, regardez ce qui suit :

[AZURE.VIDEO start-stop-restart-and-delete-vms-in-microsoft-azure-with-powershell]

<!---HONumber=AcomDC_0128_2016-->