<properties
	pageTitle="Gérer des machines virtuelles Azure à l’aide du Gestionnaire de ressources et PowerShell | Microsoft Azure"
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
	ms.date="09/10/2015"
	ms.author="davidmu"/>

# Gestion des machines virtuelles à l’aide de modèles Azure Resource Manager et de PowerShell

> [AZURE.SELECTOR]
- [Portal](virtual-machines-windows-tutorial.md)
- [PowerShell](virtual-machines-deploy-rmtemplates-powershell.md)

L’utilisation de modèles Azure PowerShell et Gestionnaire de ressources offre davantage de puissance et de flexibilité dans Microsoft Azure. Vous pouvez utiliser les tâches décrites dans cet article pour créer et gérer des ressources de machine virtuelle.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]Cet article traite de la création de ressources avec le modèle de déploiement Resource Manager. Vous pouvez également gérer des ressources avec le [modèle de déploiement classique](virtual-machines-windows-tutorial-classic-portal.md).

Ces tâches utilisent un modèle de gestionnaire de ressources et PowerShell :

- [Création d'une machine virtuelle](#windowsvm)
- [Créer une machine virtuelle avec un disque spécialisé](#customvm)
- [Créer plusieurs machines virtuelles dans un réseau virtuel avec un équilibreur de charge externe](#multivm)

Ces tâches utilisent uniquement PowerShell :

- [Suppression d’un groupe de ressources](#removerg)
- [Affichage des informations relatives à une machine virtuelle](#displayvm)
- [Démarrage d’une machine virtuelle](#start)
- [Arrêt d’une machine virtuelle](#stop)
- [Redémarrage d’une machine virtuelle](#restart)
- [Suppression d’une machine virtuelle](#delete)

Avant de commencer, assurez-vous qu’Azure PowerShell est prêt à l’emploi.

[AZURE.INCLUDE [arm-getting-setup-powershell](../../includes/arm-getting-setup-powershell.md)]



## Modèles Azure Resource Manager et groupes de ressources

Certaines des tâches décrites dans cet article montrent comment utiliser les modèles Azure Resource Manager et Powershell pour déployer automatiquement les machines virtuelles et les gérer.

La plupart des applications fonctionnant dans Microsoft Azure sont générées à partir d’une combinaison de différents types de ressources cloud (au moins une machine virtuelle et des comptes de stockage, une base de données SQL ou un réseau virtuel). Les modèles Azure Resource Manager permettent de gérer ces différentes ressources simultanément à l’aide d’une description JSON des ressources et des paramètres de configuration et de déploiement associés.

Une fois que vous avez défini un modèle de ressource basé sur JSON, vous pouvez l’utiliser une commande PowerShell pour déployer les ressources définies dans Azure. Vous pouvez exécuter ces commandes séparément dans l'interface de commande PowerShell ou les intégrer dans un script qui contient une logique d'automatisation supplémentaire.

Les ressources que vous créez à l’aide de modèles Azure Resource Manager sont déployées sur un *groupe de ressources Azure* nouveau ou existant. Un groupe de ressources vous permet de gérer plusieurs ressources déployées ensemble sous forme de groupe logique. Cela vous permet de gérer le cycle de vie global de l’application/du groupe.

Si vous êtes intéressé par la création de modèles, consultez [Création de modèles Azure Resource Manager](resource-group-authoring-templates.md).

### Créer un groupe de ressources

Pour les tâches de création d’une ressource, vous aurez besoin d’un groupe de ressources si vous n’en avez pas encore.

Dans la commande suivante, remplacez *nom de groupe de ressources* par le nom du nouveau groupe de ressources et *emplacement Azure* par l’emplacement de datacenter Azure dans lequel vous souhaitez enregistrer la ressource, puis exécutez la commande :

	New-AzureResourceGroup -Name "resource group name" -Location "Azure location"

## <a id="windowsvm"></a>TÂCHE : Création d’une machine virtuelle

Cette tâche utilise un modèle de la galerie de modèles. Pour en savoir plus sur le modèle, consultez [Déployer une machine virtuelle Windows simple dans l’ouest des États-Unis](https://azure.microsoft.com/documentation/templates/101-simple-windows-vm/).

![](./media/virtual-machines-deploy-rmtemplates-powershell/windowsvm.png)

Dans la commande suivante, remplacez *nom du déploiement* par le nom que vous souhaitez utiliser pour le déploiement et le *nom de groupe de ressources* par le nom de la ressource de groupe existant, puis exécutez la commande :

	New-AzureResourceGroupDeployment -Name "deployment name" -ResourceGroupName "resource group name" -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-simple-windows-vm/azuredeploy.json"

Voici un exemple :

	New-AzureResourceGroupDeployment -Name "TestDeployment" -ResourceGroupName "TestRG" -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-simple-windows-vm/azuredeploy.json"

Vous êtes invité à fournir les valeurs de paramètres dans la section **parameters** du fichier JSON :

	cmdlet New-AzureResourceGroupDeployment at command pipeline position 1
	Supply values for the following parameters:
	(Type !? for Help.)
	newStorageAccountName: saacct
	adminUsername: WinAdmin1
	adminPassword: *********
	dnsNameForPublicIP: contoso

Le résultat suivant doit s’afficher :

	VERBOSE: 10:56:59 AM - Template is valid.
	VERBOSE: 10:56:59 AM - Create template deployment 'TestDeployment'.
	VERBOSE: 10:57:08 AM - Resource Microsoft.Network/virtualNetworks 'MyVNET' provisioning status is succeeded
	VERBOSE: 10:57:11 AM - Resource Microsoft.Network/publicIPAddresses 'myPublicIP' provisioning status is running
	VERBOSE: 10:57:11 AM - Resource Microsoft.Storage/storageAccounts 'newsaacct' provisioning status is running
	VERBOSE: 10:57:38 AM - Resource Microsoft.Storage/storageAccounts 'newsaacct' provisioning status is succeeded
	VERBOSE: 10:57:40 AM - Resource Microsoft.Network/publicIPAddresses 'myPublicIP' provisioning status is succeeded
	VERBOSE: 10:57:45 AM - Resource Microsoft.Compute/virtualMachines 'MyWindowsVM' provisioning status is running
	VERBOSE: 10:57:45 AM - Resource Microsoft.Network/networkInterfaces 'myVMNic' provisioning status is succeeded
	VERBOSE: 11:01:59 AM - Resource Microsoft.Compute/virtualMachines 'MyWindowsVM' provisioning status is succeeded


	DeploymentName    : TestDeployment
	ResourceGroupName : TestRG
	ProvisioningState : Succeeded
	Timestamp         : 4/28/2015 6:02:13 PM
	Mode              : Incremental
	TemplateLink      :
	Parameters        :
                    	Name             Type                       Value
	                    ===============  =========================  ==========
	                    newStorageAccountName  String                     saacct
	                    adminUsername    String                     WinAdmin1
	                    adminPassword    SecureString
	                    dnsNameForPublicIP  String                     contoso9875
	                    windowsOSVersion  String                     2012-R2-Datacenter

	Outputs           :

Si vous souhaitez voir une vidéo de l’exécution de cette tâche, regardez ce qui suit :

[AZURE.VIDEO deploy-a-windows-virtual-machine-with-azure-resource-manager-templates-and-powershell]

## <a id="customvm"></a>TÂCHE : Créer une machine virtuelle avec un disque spécialisé

Cette tâche utilise un modèle de la galerie de modèles. Pour en savoir plus sur le modèle, consultez [Créer une machine virtuelle à partir d’un disque VHD spécialisé](https://azure.microsoft.com/documentation/templates/201-vm-from-specialized-vhd/).

Dans la commande suivante, remplacez *nom du déploiement* par le nom que vous souhaitez utiliser pour le déploiement et le *nom de groupe de ressources* par le nom de la ressource de groupe existant, puis exécutez la commande :

	New-AzureResourceGroupDeployment -Name "deployment name" -ResourceGroupName "resource group name" -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-from-specialized-vhd/azuredeploy.json"

Voici un exemple :

	New-AzureResourceGroupDeployment -Name "TestDeployment" -ResourceGroupName "TestRG" -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-from-specialized-vhd/azuredeploy.json"

Vous êtes invité à fournir les valeurs de paramètres dans la section **parameters** du fichier JSON :

	cmdlet New-AzureResourceGroup at command pipeline position 1
	Supply values for the following parameters:
	(Type !? for Help.)
	osDiskVhdUri: http://saacct.blob.core.windows.net/vhds/osdiskforwindows.vhd
	osType: windows
	location: West US
	vmSize: Standard_A3
	...

> [AZURE.NOTE]L’exemple ci-dessus utilise un fichier de disque dur virtuel qui existe dans le compte de stockage saacct. Le nom du disque a été fourni en tant que paramètre au modèle.

Si vous souhaitez voir une vidéo de l’exécution de cette tâche, regardez ce qui suit :

[AZURE.VIDEO create-a-custom-virtual-machine-image-in-azure-resource-manager-with-powershell]

## <a id="multivm"></a>TÂCHE : Créer plusieurs machines virtuelles dans un réseau virtuel avec un équilibreur de charge externe

Cette tâche utilise un modèle de la galerie de modèles. Pour en savoir plus sur le modèle, consultez [Créer une machine virtuelle à partir d’un disque VHD spécialisé](https://azure.microsoft.com/documentation/templates/201-2-vms-loadbalancer-lbrules/).

![](./media/virtual-machines-deploy-rmtemplates-powershell/multivmextlb.png)

Dans la commande suivante, remplacez *nom du déploiement* par le nom que vous souhaitez utiliser pour le déploiement et le *nom de groupe de ressources* par le nom de la ressource de groupe existant, puis exécutez la commande :

	New-AzureResourceGroupDeployment -Name "deployment name" -ResourceGroupName "resource group name" -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-2-vms-loadbalancer-lbrules/azuredeploy.json"

Vous êtes invité à fournir les valeurs de paramètres dans la section **parameters** du fichier JSON :

	cmdlet New-AzureResourceGroup at command pipeline position 1
	Supply values for the following parameters:
	(Type !? for Help.)
	newStorageAccountName: saTest
	adminUserName: WebAdmin1
	adminPassword: *******
	dnsNameforLBIP: web07
	backendPort: 80
	vmNamePrefix: WEBFARM
	...

Si vous souhaitez voir une vidéo de l’exécution de cette tâche, regardez ce qui suit :

[AZURE.VIDEO deploy-multi-vm-app-with-a-virtual-network-and-load-balancer-in-azure-resource-manager]

## <a id="removerg"></a>TÂCHE : Supprimer un groupe de ressources

Dans la commande suivante, remplacez *nom de groupe de ressources* par le nom du groupe de ressources que vous souhaitez supprimer, puis exécutez la commande :

	Remove-AzureResourceGroup  -Name "resource group name"

> [AZURE.NOTE]Utilisez le paramètre **-Force** pour ignorer l’invite de confirmation.

Vous êtes invité à confirmer si vous n’avez pas utilisé pas le paramètre -Force :

	Confirm
	Are you sure you want to remove resource group 'BuildRG'
	[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):

Si vous souhaitez voir une vidéo de l’exécution de cette tâche, regardez ce qui suit :

[AZURE.VIDEO removing-a-resource-group-in-azure]

## <a id="displayvm"></a>TÂCHE : Afficher les informations relatives à une machine virtuelle

Dans la commande suivante, remplacez *nom de groupe de ressources* par le nom du groupe de ressources contenant la machine virtuelle et *nom de machine virtuelle* par le nom de la machine, puis exécutez-la :

	Get-AzureVM -ResourceGroupName "resource group name" -Name "VM name"

Le résultat suivant doit s’afficher :

	AvailabilitySetReference : null
	Extensions               : []
	HardwareProfile          : {
	                             "VirtualMachineSize": "Standard_D1"
	                           }
	Id                       : /subscriptions/{subscription-id}/resourceGroups/BuildRG/providers/Microso
	                           ft.Compute/virtualMachines/MyWindowsVM
	InstanceView             : null
	Location                 : westus
	Name                     : MyWindowsVM
	NetworkProfile           : {
	                             "NetworkInterfaces": [
	                               {
	                                 "Primary": null,
	                                 "ReferenceUri": "/subscriptions/{subscription-id}/resourceGroups/Bu
	                           ildRG/providers/Microsoft.Network/networkInterfaces/myVMNic"
	                               }
	                             ]
	                           }
	OSProfile                : {
	                             "AdminPassword": null,
	                             "AdminUsername": "WinAdmin1",
	                             "ComputerName": "MyWindowsVM",
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
	StorageProfile           : {
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
	                                 "Uri": "http://saacct.blob.core.windows.net/vhds/osdiskforwindowssimple.vhd"
	                               }
	                             },
	                             "SourceImage": null
	                           }
	Tags                     : {}
	Type                     : Microsoft.Compute/virtualMachines

Si vous souhaitez voir une vidéo de l’exécution de cette tâche, regardez ce qui suit :

[AZURE.VIDEO displaying-information-about-a-virtual-machine-in-microsoft-azure-with-powershell]

## <a id="start"></a>TÂCHE : Démarrer une machine virtuelle

Dans la commande suivante, remplacez *nom de groupe de ressources* par le nom du groupe de ressources contenant la machine virtuelle et *nom de machine virtuelle* par le nom de la machine, puis exécutez-la :

	Start-AzureVM -ResourceGroupName "resource group name" -Name "VM name"

Le résultat suivant doit s’afficher :

	EndTime             : 4/28/2015 11:11:41 AM -07:00
	Error               :
	Output              :
	StartTime           : 4/28/2015 11:10:35 AM -07:00
	Status              : Succeeded
	TrackingOperationId : e1705973-d266-467e-8655-920016145347
	RequestId           : aac41de1-b85d-4429-9a3d-040b922d2e6d
	StatusCode          : OK

Si vous souhaitez voir une vidéo de l’exécution de cette tâche, regardez ce qui suit :

[AZURE.VIDEO start-stop-restart-and-delete-vms-in-microsoft-azure-with-powershell]

## <a id="stop"></a>TÂCHE : Arrêter une machine virtuelle

Dans la commande suivante, remplacez *nom de groupe de ressources* par le nom du groupe de ressources contenant la machine virtuelle et *nom de machine virtuelle* par le nom de la machine, puis exécutez-la :

	Stop-AzureVM -ResourceGroupName "resource group name" -Name "VM name"

Vous êtes invité à confirmer l’opération :

	Virtual machine stopping operation
	This cmdlet will stop the specified virtual machine. Do you want to continue?
	[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):

Le résultat suivant doit s’afficher :

	EndTime             : 4/28/2015 11:09:08 AM -07:00
	Error               :
	Output              :
	StartTime           : 4/28/2015 11:06:55 AM -07:00
	Status              : Succeeded
	TrackingOperationId : 0c94dc74-c553-412c-a187-108bdb29657e
	RequestId           : 5cc9ddba-0643-4b5e-82b6-287b321394ee
	StatusCode          : OK

Si vous souhaitez voir une vidéo de l’exécution de cette tâche, regardez ce qui suit :

[AZURE.VIDEO start-stop-restart-and-delete-vms-in-microsoft-azure-with-powershell]

## <a id="restart"></a>TÂCHE : Redémarrer une machine virtuelle

Dans la commande suivante, remplacez *nom de groupe de ressources* par le nom du groupe de ressources contenant la machine virtuelle et *nom de machine virtuelle* par le nom de la machine, puis exécutez-la :

	Restart-AzureVM -ResourceGroupName "resource group name" -Name "VM name"

Le résultat suivant doit s’afficher :

	EndTime             : 4/28/2015 11:16:26 AM -07:00
	Error               :
	Output              :
	StartTime           : 4/28/2015 11:16:25 AM -07:00
	Status              : Succeeded
	TrackingOperationId : 390571e0-c804-43ce-88c5-f98e0feb588e
	RequestId           : 7dac33e3-0164-4a08-be33-96205284cb0b
	StatusCode          : OK

Si vous souhaitez voir une vidéo de l’exécution de cette tâche, regardez ce qui suit :

[AZURE.VIDEO start-stop-restart-and-delete-vms-in-microsoft-azure-with-powershell]

## <a id="delete"></a>TÂCHE : Supprimer une machine virtuelle

Dans la commande suivante, remplacez *nom de groupe de ressources* par le nom du groupe de ressources contenant la machine virtuelle et *nom de machine virtuelle* par le nom de la machine, puis exécutez-la :

	Remove-AzureVM -ResourceGroupName "resource group name" –Name "VM name"

> [AZURE.NOTE]Utilisez le paramètre **-Force** pour ignorer l’invite de confirmation.

Vous êtes invité à confirmer si vous n’avez pas utilisé pas le paramètre -Force :

	Virtual machine removal operation
	This cmdlet will remove the specified virtual machine. Do you want to continue?
	[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):

Le résultat suivant doit s’afficher :

	EndTime             : 4/28/2015 11:21:55 AM -07:00
	Error               :
	Output              :
	StartTime           : 4/28/2015 11:20:13 AM -07:00
	Status              : Succeeded
	TrackingOperationId : f74fad9e-f6bc-46ae-82b1-bfad3952aa44
	RequestId           : 6a30d2e0-63ca-43cf-975b-058631e048e7
	StatusCode          : OK

Si vous souhaitez voir une vidéo de l’exécution de cette tâche, regardez ce qui suit :

[AZURE.VIDEO start-stop-restart-and-delete-vms-in-microsoft-azure-with-powershell]

## Ressources supplémentaires
[Modèles de démarrage rapide Microsoft Azure](http://azure.microsoft.com/documentation/templates/) et [Infrastructures d’applications](virtual-machines-app-frameworks.md)

[Fournisseurs de solutions de calcul, de réseau et de stockage Azure dans Azure Resource Manager](virtual-machines-azurerm-versus-azuresm.md)

[Présentation d’Azure Resource Manager](resource-group-overview.md)

[Documentation sur les machines virtuelles](http://azure.microsoft.com/documentation/services/virtual-machines/)

<!---HONumber=Sept15_HO3-->