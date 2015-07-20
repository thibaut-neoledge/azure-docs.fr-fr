<properties 
	pageTitle="Création d’une machine virtuelle Windows avec un modèle du Gestionnaire de ressources" 
	description="Utilisez un modèle du Gestionnaire de ressources pour créer facilement une machine virtuelle Windows avec PowerShell ou l’interface de ligne de commande Azure." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="JoeDavies-MSFT" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/29/2015" 
	ms.author="josephd"/>

# Création d’une machine virtuelle Windows avec un modèle du Gestionnaire de ressources

Vous pouvez facilement créer une machine virtuelle basée sur Windows à l'aide d'un modèle du Gestionnaire de ressources avec Azure PowerShell ou l’interface de ligne de commande Azure. Ce modèle crée une machine virtuelle unique exécutant Windows dans un nouveau réseau virtuel avec un seul sous-réseau dans un nouveau groupe de ressources.

![](./media/virtual-machines-create-windows-powershell-resource-manager-template/windowsvm.png)
 
Avant d’aller plus loin, veuillez vérifier qu’Azure, PowerShell et l’interface de ligne de commande Azure sont configurés et opérationnels.

[AZURE.INCLUDE [arm-getting-setup-powershell](../../includes/arm-getting-setup-powershell.md)]

[AZURE.INCLUDE [xplat-getting-set-up-arm](../../includes/xplat-getting-set-up-arm.md)]

## Création d’une machine virtuelle Windows avec un modèle du Gestionnaire de ressources en utilisant Azure PowerShell

Procédez comme suit pour créer une machine virtuelle Windows en utilisant un modèle du Gestionnaire des ressources dans le référentiel de modèles Github avec Azure PowerShell.

### Étape 1 : Examen du fichier JSON pour obtenir le modèle

Voici le contenu du fichier JSON relatif au modèle.

	{
    "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "newStorageAccountName": {
            "type": "string",
            "metadata": {
                "Description": "Unique DNS Name for the Storage Account where the Virtual Machine's disks will be placed."
            }
        },
        "adminUsername": {
            "type": "string",
            "metadata": {
               "Description": "Username for the Virtual Machine."
            }
        },
        "adminPassword": {
            "type": "securestring",
            "metadata": {
                "Description": "Password for the Virtual Machine."
            }
        },
        "dnsNameForPublicIP": {
            "type": "string",
            "metadata": {
                  "Description": "Unique DNS Name for the Public IP used to access the Virtual Machine."
            }
        },
        "windowsOSVersion": {
            "type": "string",
            "defaultValue": "2012-R2-Datacenter",
            "allowedValues": [
                "2008-R2-SP1", 
                "2012-Datacenter", 
                "2012-R2-Datacenter", 
                "Windows-Server-Technical-Preview"
            ],
            "metadata": {
                "Description": "The Windows version for the VM. This will pick a fully patched image of this given Windows version. Allowed values: 2008-R2-SP1, 2012-Datacenter, 2012-R2-Datacenter, Windows-Server-Technical-Preview."
            }
        }
    },
    "variables": {
        "location": "West US",
        "imagePublisher": "MicrosoftWindowsServer", 
        "imageOffer": "WindowsServer", 
        "OSDiskName": "osdiskforwindowssimple",
        "nicName": "myVMNic",
        "addressPrefix": "10.0.0.0/16", 
        "subnetName": "Subnet",
        "subnetPrefix": "10.0.0.0/24",
        "storageAccountType": "Standard_LRS",
        "publicIPAddressName": "myPublicIP",
        "publicIPAddressType": "Dynamic",
        "vmStorageAccountContainerName": "vhds",
        "vmName": "MyWindowsVM",
        "vmSize": "Standard_D1",
        "virtualNetworkName": "MyVNET",        
        "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',variables('virtualNetworkName'))]",
        "subnetRef": "[concat(variables('vnetID'),'/subnets/',variables('subnetName'))]"
    },    
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[parameters('newStorageAccountName')]",
            "apiVersion": "2015-05-01-preview",
            "location": "[variables('location')]",
            "properties": {
                "accountType": "[variables('storageAccountType')]"
            }
        },
        {
            "apiVersion": "2015-05-01-preview",
            "type": "Microsoft.Network/publicIPAddresses",
            "name": "[variables('publicIPAddressName')]",
            "location": "[variables('location')]",
            "properties": {
                "publicIPAllocationMethod": "[variables('publicIPAddressType')]",
                "dnsSettings": {
                    "domainNameLabel": "[parameters('dnsNameForPublicIP')]"
                }
            }
        },
        {
            "apiVersion": "2015-05-01-preview",
            "type": "Microsoft.Network/virtualNetworks",
            "name": "[variables('virtualNetworkName')]",
            "location": "[variables('location')]",
            "properties": {
                "addressSpace": {
                    "addressPrefixes": [
                        "[variables('addressPrefix')]"
                    ]
                },
                "subnets": [
                    {
                        "name": "[variables('subnetName')]",
                        "properties": {
                            "addressPrefix": "[variables('subnetPrefix')]"
                        }
                    }
                ]
            }
        },
        {
            "apiVersion": "2015-05-01-preview",
            "type": "Microsoft.Network/networkInterfaces",
            "name": "[variables('nicName')]",
            "location": "[variables('location')]",
            "dependsOn": [
                "[concat('Microsoft.Network/publicIPAddresses/', variables('publicIPAddressName'))]",
                "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]"
            ],
            "properties": {
                "ipConfigurations": [
                    {
                        "name": "ipconfig1",
                        "properties": {
                            "privateIPAllocationMethod": "Dynamic",
                            "publicIPAddress": {
                                "id": "[resourceId('Microsoft.Network/publicIPAddresses',variables('publicIPAddressName'))]"
                            },
                            "subnet": {
                                "id": "[variables('subnetRef')]"
                            }
                        }
                    }
                ]
            }
        },
        {
            "apiVersion": "2015-05-01-preview",
            "type": "Microsoft.Compute/virtualMachines",
            "name": "[variables('vmName')]",
            "location": "[variables('location')]",
            "dependsOn": [
                "[concat('Microsoft.Storage/storageAccounts/', parameters('newStorageAccountName'))]",
                "[concat('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
            ],
            "properties": {
                "hardwareProfile": {
                    "vmSize": "[variables('vmSize')]"
                },
                "osProfile": {
                    "computername": "[variables('vmName')]",
                    "adminUsername": "[parameters('adminUsername')]",
                    "adminPassword": "[parameters('adminPassword')]"
                },
                "storageProfile": {
                    "imageReference": {
                        "publisher": "[variables('imagePublisher')]",
                        "offer": "[variables('imageOffer')]",
                        "sku" : "[parameters('windowsOSVersion')]",
                        "version":"latest"
                    },
                   "osDisk" : {
                        "name": "osdisk",
                        "vhd": {
                            "uri": "[concat('http://',parameters('newStorageAccountName'),'.blob.core.windows.net/',variables('vmStorageAccountContainerName'),'/',variables('OSDiskName'),'.vhd')]"
                        },
                        "caching": "ReadWrite",
                        "createOption": "FromImage"
                    }
                },
                "networkProfile": {
                    "networkInterfaces": [
                        {
                            "id": "[resourceId('Microsoft.Network/networkInterfaces',variables('nicName'))]"
                        }
                    ]
                }
            }
        }
    ]
	} 


### Étape 2 : Création de la machine virtuelle avec le modèle

Entrez un nom de déploiement Azure, un nom de groupe de ressources et un emplacement de centre de données Azure, puis exécutez les commandes suivantes.

	$deployName="<deployment name>"
	$RGName="<resource group name>"
	$locName="<Azure location, such as West US>"
	$templateURI="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-simple-windows-vm/azuredeploy.json"
	New-AzureResourceGroup –Name $RGName –Location $locName
	New-AzureResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateUri $templateURI

Lorsque vous exécutez la commande **New-AzureResourceGroupDeployment**, vous êtes invité à entrer les valeurs des paramètres dans la section "parameters" du fichier JSON. Lorsque vous avez spécifié toutes les valeurs de paramètre, la commande crée le groupe de ressources et la machine virtuelle.

Voici un exemple.

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
	                    dnsNameForPublicIP  String                     contoso9875
	                    windowsOSVersion  String                     2012-R2-Datacenter
	
	Outputs           :

Vous disposez maintenant d'une nouvelle machine virtuelle Windows nommée MyWindowsVM dans votre nouveau groupe de ressources.

## Création d’une machine virtuelle Windows avec un modèle du Gestionnaire de ressources en utilisant l’interface de ligne de commande Azure

Procédez comme suit pour créer une machine virtuelle Windows en utilisant un modèle du Gestionnaire de ressources dans le référentiel de modèles Github avec l’interface de ligne de commande.

Entrez un nom de groupe de ressources et un emplacement Azure (par exemple, westus pour Ouest des États-Unis), supprimez les crochets, puis exécutez les commandes suivantes.

	azure group create [resource group] [location]
	azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-simple-windows-vm/azuredeploy.json [resource group] firstdeployment

Voici un exemple de la commande de l’interface de ligne de commande Azure définie pour le modèle.

	azure group create testrg westus
	azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-simple-windows-vm/azuredeploy.json testrg firstdeployment

Le résultat suivant doit s'afficher :

	azure group create testrg westus
	info:    Executing command group create
	+ Getting resource group testrg                                             
	+ Creating resource group testrg                                            
	info:    Created resource group testrg
	data:    Id:                  /subscriptions/2c73c582-4b11-4800-96f9-a9bd790a861c/resourceGroups/testrg
	data:    Name:                testrg
	data:    Location:            westus
	data:    Provisioning State:  Succeeded
	data:    Tags: 
	data:    
	info:    group create command OK

	azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-simple-windows-vm/azuredeploy.json testrg firstdeployment
	info:    Executing command group deployment create
	info:    Supply values for the following parameters
	newStorageAccountName: newstorage
	adminUsername: ops
	adminPassword: Pa$$W0rd1
	dnsNameForPublicIP: contoso
	windowsOSVersion: 2012-R2-Datacenter
	+ Initializing template configurations and parameters                          
	+ Creating a deployment                                                        
	info:    Created template deployment "firstdeployment"
	+ Registering providers                                                        


## Ressources supplémentaires

[Fournisseurs de calcul, de réseau et de stockage Azure dans Azure Resource Manager](virtual-machines-azurerm-versus-azuresm.md)

[Présentation du Gestionnaire de ressources Azure](../resource-group-overview.md)

[Création d’une machine virtuelle Windows avec le Gestionnaire de ressources Azure et PowerShell](virtual-machines-create-windows-powershell-resource-manager.md)

[Création d’une machine virtuelle Windows avec PowerShell et le Gestionnaire de ressources Azure](virtual-machines-create-windows-powershell-service-manager.md)

[Documentation sur les machines virtuelles](http://azure.microsoft.com/documentation/services/virtual-machines/)

[Installation et configuration d’Azure PowerShell](../install-configure-powershell.md)
 

<!---HONumber=July15_HO2-->