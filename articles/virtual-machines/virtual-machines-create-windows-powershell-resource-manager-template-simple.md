<properties
	pageTitle="Création d’une machine virtuelle avec un modèle ARM | Microsoft Azure"
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
	ms.date="07/28/2015"
	ms.author="davidmu"/>

# Création d’une machine virtuelle Windows avec un modèle du Gestionnaire de ressources et PowerShell

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]Cet article traite de la création de ressources avec le modèle de déploiement de Resource Manager. Vous ne pouvez pas créer cette ressource avec le modèle de déploiement classique.

Vous pouvez facilement créer une machine virtuelle basée sur Windows à l'aide d'un modèle du Gestionnaire de ressources avec Azure PowerShell. Ce modèle crée une machine virtuelle unique exécutant Windows dans un nouveau réseau virtuel avec un seul sous-réseau dans un nouveau groupe de ressources.

![](./media/virtual-machines-create-windows-powershell-resource-manager-template-simple/windowsvm.png)

Avant d’aller plus loin, veuillez vérifier qu’Azure et PowerShell sont configurés et opérationnels.

[AZURE.INCLUDE [arm-getting-setup-powershell](../../includes/arm-getting-setup-powershell.md)]

## Création d’une machine virtuelle Windows

Procédez comme suit pour créer une machine virtuelle Windows en utilisant un modèle du Gestionnaire des ressources dans le référentiel de modèles Github avec Azure PowerShell.

Entrez un nom de déploiement Azure, un nom de groupe de ressources et un emplacement de centre de données Azure, puis exécutez les commandes suivantes.

	$deployName="<deployment name>"
	$RGName="<resource group name>"
	$locName="<Azure location, such as West US>"
	$templateURI="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-simple-windows-vm/azuredeploy.json"
	New-AzureResourceGroup –Name $RGName –Location $locName
	New-AzureResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateUri $templateURI

Lorsque vous exécutez la commande **New-AzureResourceGroupDeployment**, vous êtes invité à entrer les valeurs des paramètres dans la section "parameters" du fichier JSON. Lorsque vous avez spécifié toutes les valeurs de paramètre, la commande crée le groupe de ressources et la machine virtuelle.

	$deployName="TestDeployment"
	$RGName="TestRG"
	$locname="West US"
	$templateURI="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-simple-windows-vm/azuredeploy.json"
	New-AzureResourceGroup –Name $RGName –Location $locName
	New-AzureResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateUri $templateURI

Le résultat suivant doit s’afficher :

	cmdlet New-AzureResourceGroupDeployment at command pipeline position 1
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
                    	Name             Type                       Value
	                    ===============  =========================  ==========
	                    newStorageAccountName  String                     newsaacct
	                    adminUsername    String                     WinAdmin1
	                    adminPassword    SecureString
	                    dnsNameForPublicIP  String                     contoso
	                    windowsOSVersion  String                     2012-R2-Datacenter

	Outputs           :

Vous disposez maintenant d'une nouvelle machine virtuelle Windows nommée MyWindowsVM dans votre nouveau groupe de ressources.

## Ressources supplémentaires

[Fournisseurs de calcul, de réseau et de stockage Azure dans Azure Resource Manager](virtual-machines-azurerm-versus-azuresm.md)

[Présentation du Gestionnaire de ressources Azure](resource-group-overview.md)

[Création d’une machine virtuelle Windows avec le Gestionnaire de ressources Azure et PowerShell](virtual-machines-create-windows-powershell-resource-manager.md)

[Création d’une machine virtuelle Windows avec PowerShell et le Gestionnaire de ressources Azure](virtual-machines-create-windows-powershell-service-manager.md)

[Documentation sur les machines virtuelles](http://azure.microsoft.com/documentation/services/virtual-machines/)

[Installation et configuration d’Azure PowerShell](install-configure-powershell.md)

<!---HONumber=Sept15_HO4-->