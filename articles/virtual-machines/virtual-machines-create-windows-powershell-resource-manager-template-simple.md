<properties
	pageTitle="Créer une machine virtuelle avec un modèle de machine virtuelle | Microsoft Azure"
	description="Utilisez un modèle du Gestionnaire de ressources et Azure PowerShell pour créer une machine virtuelle Windows."
	services="virtual-machines"
	documentationCenter=""
	authors="davidmu1"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/07/2016"
	ms.author="davidmu"/>

# Création d’une machine virtuelle Windows avec un modèle du Gestionnaire de ressources et PowerShell

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]Modèle de déploiement classique


Vous pouvez facilement créer une machine virtuelle basée sur Windows à l'aide d'un modèle du Gestionnaire de ressources avec Azure PowerShell. Ce modèle crée une machine virtuelle unique exécutant Windows dans un nouveau réseau virtuel avec un seul sous-réseau dans un nouveau groupe de ressources.

![](./media/virtual-machines-create-windows-powershell-resource-manager-template-simple/windowsvm.png)

[AZURE.INCLUDE [powershell-preview](../../includes/powershell-preview-inline-include.md)]

## Création d’une machine virtuelle Windows

Procédez comme suit pour créer une machine virtuelle Windows en utilisant un modèle du Gestionnaire des ressources dans le référentiel de modèles Github avec Azure PowerShell.

Dans les commandes suivantes, entrez le nom que vous souhaitez utiliser pour le déploiement, le nom du nouveau groupe de ressources, ainsi que l’emplacement où la ressource doit être créée.


		$deployName="<deployment name>"
		$RGName="<resource group name>"
		$locName="<Azure location, such as West US>"
		$templateURI="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-simple-windows-vm/azuredeploy.json"
		New-AzureRmResourceGroup –Name $RGName –Location $locName
		New-AzureRmResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateUri $templateURI


Quand vous exécutez la commande **New-AzureRmResourceGroupDeployment**, vous êtes invité à entrer les valeurs des paramètres dans la section « parameters » du fichier JSON. Une fois que vous avez spécifié toutes les valeurs des paramètres, la commande crée le groupe de ressources et la machine virtuelle.

Le résultat suivant devrait s'afficher :


		cmdlet New-AzureRmResourceGroupDeployment at command pipeline position 1
		Supply values for the following parameters:
		(Type !? for Help.)
		newStorageAccountName: newsaacct
		adminUsername: WinAdmin1
		adminPassword: *********
		dnsNameForPublicIP: contoso
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
											Name                   Type                       Value
											===============        =========================  ==========
											newStorageAccountName  String                     newsaacct
											adminUsername          String                     WinAdmin1
											adminPassword          SecureString
											dnsNameForPublicIP     String                     contoso
											windowsOSVersion       String                     2012-R2-Datacenter
		Outputs           :


## Étapes suivantes

Pour savoir comment gérer la machine virtuelle que vous venez de créer, passez en revue [Gérer des machines virtuelles à l’aide d’Azure Resource Manager et de PowerShell](virtual-machines-deploy-rmtemplates-powershell.md).

<!---HONumber=AcomDC_0114_2016-->