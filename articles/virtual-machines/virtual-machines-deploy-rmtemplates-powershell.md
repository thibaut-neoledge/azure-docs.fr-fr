<properties 
	pageTitle="Déploiement et gestion de machines virtuelles Azure à l’aide de modèles du Gestionnaire des ressources et de PowerShell" 
	description="Déployez facilement les ensembles de configurations les plus courants pour les machines virtuelles Azure et gérez-les en utilisant les modèles du Gestionnaire des ressources et de PowerShell." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="JoeDavies-MSFT" 
	manager="timlt" 
	editor=""
	tags="azure-resource-manager"/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/19/2015" 
	ms.author="josephd"/>

# Déploiement et gestion de machines virtuelles à l’aide de modèles Azure Resource Manager et de PowerShell

Cet article montre comment utiliser les modèles Azure Resource Manager et Powershell pour automatiser les tâches courantes de déploiement et de gestion de Microsoft Azure Virtual Machines. Pour connaître les modèles supplémentaires utilisables, consultez les pages [Modèles de démarrage rapide Microsoft Azure](http://azure.microsoft.com/documentation/templates/) et [Infrastructures d’application](virtual-machines-app-frameworks.md).

- [Déploiement d’une machine virtuelle Windows](#windowsvm)
- [Création d’une image de machine virtuelle personnalisée](#customvm)
- [Déploiement d’une application de plusieurs machines virtuelles utilisant un réseau virtuel et un équilibrage de charge externe](#multivm)
- [Mise à jour d’une machine virtuelle avec un modèle Resource Manager](#updatevm)
- [Suppression d’un groupe de ressources](#removerg)
- [Connexion à une machine virtuelle](#logon)
- [Affichage des informations relatives à une machine virtuelle](#displayvm)
- [Démarrage d’une machine virtuelle](#start)
- [Arrêt d’une machine virtuelle](#stop)
- [Redémarrage d’une machine virtuelle](#restart)
- [Suppression d’une machine virtuelle](#delete)

Avant de commencer, assurez-vous qu’Azure PowerShell est prêt à l’emploi.

[AZURE.INCLUDE [arm-getting-setup-powershell](../../includes/arm-getting-setup-powershell.md)]

## Présentation des groupes de ressources et des modèles Azure Resource Manager

La plupart des applications déployées et s’exécutant dans Microsoft Azure sont basées sur une combinaison de différents types de ressources cloud (au moins une machine virtuelle et un compte de stockage, une base de données SQL ou un réseau virtuel). Les modèles Microsoft Azure Resource Manager permettent de déployer et de gérer ces différentes ressources en même temps à l’aide d’une description JSON des ressources et des paramètres de configuration et de déploiement associés.

Une fois que vous avez défini un modèle de ressource basé sur JSON, vous pouvez l’exécuter de sorte que les ressources qui y sont définies soient déployées dans Azure à l’aide d’une commande PowerShell. Vous pouvez exécuter ces commandes soit de manière autonome dans l’interface de commande PowerShell soit dans un script qui contient la logique d’automatisation supplémentaire.

Les ressources que vous créez à l’aide de modèles Azure Resource Manager seront déployées sur un groupe de ressources Azure nouveau ou existant. Un *groupe de ressources Azure* vous permet de gérer plusieurs ressources déployées ensemble sous forme de groupe logique ; cela vous permet de gérer le cycle de vie global de l’application/du groupe et de fournir des API de gestion qui vous donnent la possibilité d’effectuer les actions suivantes :

- arrêter, démarrer ou supprimer d’un coup toutes les ressources au sein du groupe ; 
- appliquer des règles de contrôle d’accès en fonction du rôle (RBAC) pour le verrouillage des autorisations de sécurité ; 
- mener des opérations d’audit ; 
- et baliser des ressources avec des métadonnées supplémentaires pour améliorer leur suivi. 

Pour en savoir plus sur Azure Resource Manager, cliquez [ici](virtual-machines-azurerm-versus-azuresm.md). Si vous êtes intéressé par la création de modèles, consultez la page [Création de modèles du Gestionnaire de ressources Azure](../resource-group-authoring-templates.md).

## <a id="windowsvm"></a>TÂCHE : Déployer une machine virtuelle Windows

Utilisez les instructions de cette section pour déployer une nouvelle machine virtuelle Azure à l’aide d’un modèle du gestionnaire des ressources et d’Azure PowerShell. Ce modèle crée une machine virtuelle unique dans un nouveau réseau virtuel avec un seul sous-réseau.

![](./media/virtual-machines-deploy-rmtemplates-powershell/windowsvm.png)
 
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
	New-AzureResourceGroup -Name $RGName -Location $locName
	New-AzureResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateUri $templateURI

Lorsque vous exécutez la commande **New-AzureResourceGroupDeployment**, vous êtes invité à entrer les valeurs des paramètres dans la section "parameters" du fichier JSON. Lorsque vous avez spécifié toutes les valeurs de paramètre nécessaires, la commande crée le groupe de ressources et la machine virtuelle.

Voici un exemple.

	$deployName="TestDeployment"
	$RGName="TestRG"
	$locname="West US"
	$templateURI="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-simple-windows-vm/azuredeploy.json"
	New-AzureResourceGroup -Name $RGName -Location $locName
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

Vous disposez maintenant d’une nouvelle machine virtuelle Windows nommée MyWindowsVM dans votre nouveau groupe de ressources.

## <a id="customvm"></a>TÂCHE : Créer une image de machine virtuelle personnalisée

Utilisez les instructions de cette section pour créer une image de machine virtuelle personnalisée dans Azure avec un modèle du gestionnaire des ressources à l’aide d’Azure PowerShell. Ce modèle crée une seule machine virtuelle à partir d’un disque dur virtuel (VHD) spécifié.

### Étape 1 : Examen du fichier JSON pour obtenir le modèle

Voici le contenu du fichier JSON relatif au modèle.

	{
	    "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
	    "contentVersion": "1.0.0.0",
	    "parameters": {
	        "osDiskVhdUri": {
	            "type": "string",
	            "metadata": {
	                "Description": "Uri of the existing VHD"
	            }
	        },
	        "osType": {
	            "type": "string",
	            "allowedValues": [
	                "windows",
	                "linux"
	            ],
	            "metadata": {
	                "Description": "Type of OS on the existing vhd"
	            }
	        },
	        "location": {
	            "type": "String",
	            "defaultValue": "West US",
	            "metadata": {
	                "Description": "Location to create the VM in"
	            }
	        },
	        "vmSize": {
	            "type": "string",
	            "defaultValue": "Standard_A2",
	            "metadata": {
	                "Description": "Size of the VM"
	            }
	        },
	        "vmName": {
	            "type": "string",
	            "defaultValue": "myVM",
	            "metadata": {
	                "Description": "Name of the VM"
	            }
	        },
	        "nicName": {
	            "type": "string",
	            "defaultValue": "myNIC",
	            "metadata": {
	                "Description": "NIC to attach the new VM to"
	            }
	        }
	    },
	    "resources": [{
	        "apiVersion": "2014-12-01-preview",
	        "type": "Microsoft.Compute/virtualMachines",
	        "name": "[parameters('vmName')]",
	        "location": "[parameters('location')]",
	        "properties": {
	            "hardwareProfile": {
	                "vmSize": "[parameters('vmSize')]"
	            },
	            "storageProfile": {
	                "osDisk": {
	                    "name": "[concat(parameters('vmName'),'-osDisk')]",
	                    "osType": "[parameters('osType')]",
	                    "caching": "ReadWrite",
	                    "vhd": {
	                        "uri": "[parameters('osDiskVhdUri')]"
	                    }
	                }
	            },
	            "networkProfile": {
	                "networkInterfaces": [{
	                    "id": "[resourceId('Microsoft.Network/networkInterfaces',parameters('nicName'))]"
	                }]
	            }
	        }
	    }]
	}

### Étape 2 : Récupération du disque dur virtuel

Dans le cas d’une machine virtuelle Windows, consultez la page [Création et téléchargement d’un disque dur virtuel Windows Server dans Azure](virtual-machines-create-upload-vhd-windows-server.md).

Dans le cas d’une machine virtuelle Linux, consultez la page [Création et téléchargement d’un disque dur virtuel Linux dans Azure](virtual-machines-linux-create-upload-vhd.md).

### Étape 3 : Création de la machine virtuelle avec le modèle

Pour créer une nouvelle machine virtuelle basée sur le disque dur virtuel, remplacez les éléments entre « < > » par vos informations spécifiques et exécutez ces commandes :

	$deployName="<deployment name>"
	$RGName="<resource group name>"
	$locName="<Azure location, such as West US>"
	$templateURI="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-from-specialized-vhd/azuredeploy.json"
	New-AzureResourceGroup -Name $RGName -Location $locName
	New-AzureResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateUri $templateURI

Vous êtes invité à entrer les valeurs des paramètres dans la section "parameters" du fichier JSON. Lorsque vous avez spécifié toutes les valeurs de paramètre, Azure Resource Manager crée le groupe de ressources et la machine virtuelle.

Voici un exemple :

	$deployName="TestDeployment"
	$RGName="TestRG"
	$locname="West US"
	$templateURI="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-from-specialized-vhd/azuredeploy.json"
	New-AzureResourceGroup -Name $RGName -Location $locName
	New-AzureResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateUri $templateURI


Le type d’information suivant s’affiche :

	cmdlet New-AzureResourceGroup at command pipeline position 1
	Supply values for the following parameters:
	(Type !? for Help.)
	osDiskVhdUri: http://saacct.blob.core.windows.net/vhds/osdiskforwindows.vhd
	osType: windows
	location: West US
	vmSize: Standard_A3
	...

## <a id="multivm"></a>TÂCHE : Déployer une application de plusieurs machines virtuelles utilisant un réseau virtuel et un équilibrage de charge externe

Suivez les instructions fournies dans ces sections pour déployer une application de plusieurs machines virtuelles utilisant un réseau virtuel et un équilibrage de charge à l’aide d’un modèle du gestionnaire des ressources avec Azure PowerShell. Ce modèle crée deux machines virtuelles dans un réseau virtuel avec un seul sous-réseau dans un nouveau service cloud et les ajoute à un jeu d’équilibrage de charge externe pour le trafic entrant vers le port|TCP 80.

![](./media/virtual-machines-deploy-rmtemplates-powershell/multivmextlb.png)

Procédez comme suit pour déployer une application de plusieurs machines virtuelles utilisant un réseau virtuel et un équilibrage de charge à l’aide d’un modèle du Gestionnaire des ressources dans le référentiel de modèles Github avec des commandes Azure PowerShell.

### Étape 1 : Examen du fichier JSON pour obtenir le modèle

Voici le contenu du fichier JSON relatif au modèle.

	{
	"$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
	    "contentVersion": "1.0.0.0",
    "parameters": {
        "region": {
            "type": "string"
        },
        "storageAccountName": {
            "type": "string",
            "defaultValue": "uniqueStorageAccountName"
        },
        "adminUsername": {
            "type": "string"
        },
        "adminPassword": {
            "type": "securestring"
        },
        "dnsNameforLBIP": {
            "type": "string",
            "defaultValue": "uniqueDnsNameforLBIP"
        },
        "backendPort": {
            "type": "int",
            "defaultValue": 3389
        },
        "vmNamePrefix": {
            "type": "string",
            "defaultValue": "myVM"
        },
        "vmSourceImageName": {
            "type": "string",
            "defaultValue": "a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-201412.01-en.us-127GB.vhd"
        },
        "lbName": {
            "type": "string",
            "defaultValue": "myLB"
        },
        "nicNamePrefix": {
            "type": "string",
            "defaultValue": "nic"
        },
        "publicIPAddressName": {
            "type": "string",
            "defaultValue": "myPublicIP"
        },
        "vnetName": {
            "type": "string",
            "defaultValue": "myVNET"
        },
        "vmSize": {
            "type": "string",
            "defaultValue": "Standard_A1",
            "allowedValues": [
                "Standard_A0",
                "Standard_A1",
                "Standard_A2",
                "Standard_A3",
                "Standard_A4"
            ]
        }
    },
    "variables": {
        "storageAccountType": "Standard_LRS",
        "vmStorageAccountContainerName": "vhds",
        "availabilitySetName": "myAvSet",
        "addressPrefix": "10.0.0.0/16",
        "subnetName": "Subnet-1",
        "subnetPrefix": "10.0.0.0/24",
        "publicIPAddressType": "Dynamic",
        "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',parameters('vnetName'))]",
        "subnetRef": "[concat(variables('vnetID'),'/subnets/',variables ('subnetName'))]",
        "publicIPAddressID": "[resourceId('Microsoft.Network/publicIPAddresses',parameters('publicIPAddressName'))]",
        "lbID": "[resourceId('Microsoft.Network/loadBalancers',parameters('lbName'))]",
        "numberOfInstances": 2,
        "nicId1": "[resourceId('Microsoft.Network/networkInterfaces',concat(parameters('nicNamePrefix'), 0))]",
        "nicId2": "[resourceId('Microsoft.Network/networkInterfaces',concat(parameters('nicNamePrefix'), 1))]",
        "frontEndIPConfigID": "[concat(variables('lbID'),'/frontendIPConfigurations/LBFE')]",
        "backEndIPConfigID1": "[concat(variables('nicId1'),'/ipConfigurations/ipconfig1')]",
        "backEndIPConfigID2": "[concat(variables('nicId2'),'/ipConfigurations/ipconfig1')]",
        "sourceImageName": "[concat('/', subscription().subscriptionId,'/services/images/',parameters('vmSourceImageName'))]",
        "lbPoolID": "[concat(variables('lbID'),'/backendAddressPools/LBBE')]",
        "lbProbeID": "[concat(variables('lbID'),'/probes/tcpProbe')]"
    },
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[parameters('storageAccountName')]",
            "apiVersion": "2014-12-01-preview",
            "location": "[parameters('region')]",
            "properties": {
                "accountType": "[variables('storageAccountType')]"
            }
        },
        {
            "type": "Microsoft.Compute/availabilitySets",
            "name": "[variables('availabilitySetName')]",
            "apiVersion": "2014-12-01-preview",
            "location": "[parameters('region')]",
            "properties": {}
        },
        {
            "apiVersion": "2014-12-01-preview",
            "type": "Microsoft.Network/publicIPAddresses",
            "name": "[parameters('publicIPAddressName')]",
            "location": "[parameters('region')]",
            "properties": {
                "publicIPAllocationMethod": "[variables('publicIPAddressType')]",
                "dnsSettings": {
                    "domainNameLabel": "[parameters('dnsNameforLBIP')]"
                }
            }
        },
        {
            "apiVersion": "2014-12-01-preview",
            "type": "Microsoft.Network/virtualNetworks",
            "name": "[parameters('vnetName')]",
            "location": "[parameters('region')]",
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
            "apiVersion": "2014-12-01-preview",
            "type": "Microsoft.Network/networkInterfaces",
            "name": "[concat(parameters('nicNamePrefix'), copyindex())]",
            "location": "[parameters('region')]",
            "copy": {
                "name": "nicLoop",
                "count": "[variables('numberOfInstances')]"
            },
            "dependsOn": [
                "[concat('Microsoft.Network/virtualNetworks/', parameters('vnetName'))]"
            ],
            "properties": {
                "ipConfigurations": [
                    {
                        "name": "ipconfig1",
                        "properties": {
                            "privateIPAllocationMethod": "Dynamic",
                            "subnet": {
                                "id": "[variables('subnetRef')]"
                            }
                        }
                    }
                ]
            }
        },
        {
            "apiVersion": "2014-12-01-preview",
            "name": "[parameters('lbName')]",
            "type": "Microsoft.Network/loadBalancers",
            "location": "[parameters('region')]",
            "dependsOn": [
                "nicLoop",
                "[concat('Microsoft.Network/publicIPAddresses/', parameters('publicIPAddressName'))]"
            ],
            "properties": {
                "frontendIPConfigurations": [
                    {
                        "name": "LBFE",
                        "properties": {
                            "publicIPAddress": {
                                "id": "[variables('publicIPAddressID')]"
                            }
                        }
                    }
                ],
                "backendAddressPools": [
                    {
                        "name": "LBBE",
                        "properties": {
                            "backendIPConfigurations": [
                                {
                                    "id": "[variables('backEndIPConfigID1')]"
                                },
                                {
                                    "id": "[variables('backEndIPConfigID2')]"
                                }
                            ]
                        }
                    }
                ],
                "inboundNatRules": [
                    {
                        "name": "RDP-VM1",
                        "properties": {
                            "frontendIPConfigurations": [
                                {
                                    "id": "[variables('frontEndIPConfigID')]"
                                }
                            ],
                            "backendIPConfiguration": {
                                "id": "[variables('backEndIPConfigID1')]"
                            },
                            "protocol": "tcp",
                            "frontendPort": 50001,
                            "backendPort": 3389,
                            "enableFloatingIP": false
                        }
                    },
                    {
                        "name": "RDP-VM2",
                        "properties": {
                            "frontendIPConfigurations": [
                                {
                                    "id": "[variables('frontEndIPConfigID')]"
                                }
                            ],
                            "backendIPConfiguration": {
                                "id": "[variables('backEndIPConfigID2')]"
                            },
                            "protocol": "tcp",
                            "frontendPort": 50002,
                            "backendPort": 3389,
                            "enableFloatingIP": false
                        }
                    }
                ],
                "loadBalancingRules": [
                    {
                        "name": "LBRule",
                        "properties": {
                            "frontendIPConfigurations": [
                                {
                                    "id": "[variables('frontEndIPConfigID')]"
                                }
                            ],
                            "backendAddressPool": {
                                "id": "[variables('lbPoolID')]"
                            },
                            "protocol": "tcp",
                            "frontendPort": 80,
                            "backendPort": 80,
                            "enableFloatingIP": false,
                            "idleTimeoutInMinutes": 5,
                            "probe": {
                                "id": "[variables('lbProbeID')]"
                            }
                        }
                    }
                ],
                "probes": [
                    {
                        "name": "tcpProbe",
                        "properties": {
                            "protocol": "tcp",
                            "port": 80,
                            "intervalInSeconds": "5",
                            "numberOfProbes": "2"
                        }
                    }
                ]
            }
        },
        {
            "apiVersion": "2014-12-01-preview",
            "type": "Microsoft.Compute/virtualMachines",
            "name": "[concat(parameters('vmNamePrefix'), copyindex())]",
            "copy": {
                "name": "virtualMachineLoop",
                "count": "[variables('numberOfInstances')]"
            },
            "location": "[parameters('region')]",
            "dependsOn": [
                "[concat('Microsoft.Storage/storageAccounts/', parameters('storageAccountName'))]",
                "[concat('Microsoft.Network/networkInterfaces/', parameters('nicNamePrefix'), copyindex())]",
                "[concat('Microsoft.Compute/availabilitySets/', variables('availabilitySetName'))]"
            ],
            "properties": {
                "availabilitySet": {
                    "id": "[resourceId('Microsoft.Compute/availabilitySets',variables('availabilitySetName'))]"
                },
                "hardwareProfile": {
                    "vmSize": "[parameters('vmSize')]"
                },
                "osProfile": {
                    "computername": "[concat(parameters('vmNamePrefix'), copyIndex())]",
                    "adminUsername": "[parameters('adminUsername')]",
                    "adminPassword": "[parameters('adminPassword')]"
                },
                "storageProfile": {
                    "sourceImage": {
                        "id": "[variables('sourceImageName')]"
                    },
                    "destinationVhdsContainer": "[concat('http://',parameters('storageAccountName'),'.blob.core.windows.net/',variables('vmStorageAccountContainerName'),'/')]"
                },
                "networkProfile": {
                    "networkInterfaces": [
                        {
                            "id": "[resourceId('Microsoft.Network/networkInterfaces',concat(parameters('nicNamePrefix'),copyindex()))]"
                        }
                    ]
                }
            }
        }
    ]
	}


### Étape 2 : Création du déploiement avec le modèle

Entrez un nom de déploiement Azure, un nom de groupe de ressources et un emplacement Azure, puis exécutez les commandes suivantes.

	$deployName="<deployment name>"
	$RGName="<resource group name>"
	$locName="<Azure location, such as West US>"
	$templateURI="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-2-vms-loadbalancer-lbrules/azuredeploy.json"
	New-AzureResourceGroup -Name $RGName -Location $locName
	New-AzureResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateUri $templateURI

Lorsque vous exécutez la commande New-AzureResourceGroupDeployment, vous êtes invité à entrer les valeurs des paramètres du fichier JSON. Lorsque vous avez spécifié toutes les valeurs de paramètres, la commande crée le groupe de ressources et le déploiement.

	$deployName="TestDeployment"
	$RGName="TestRG"
	$locname="West US"
	$templateURI="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-2-vms-loadbalancer-lbrules/azuredeploy.json"
	New-AzureResourceGroup -Name $RGName -Location $locName
	New-AzureResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateUri $templateURI

Le résultat suivant devrait s’afficher :

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

## <a id="updatevm"></a>TÂCHE : Mettre à jour une machine virtuelle avec un modèle Resource Manager

Voici un exemple de modification d’un fichier de modèle JSON pour mettre à jour la configuration d’une machine virtuelle déployée avec un modèle Resource Manager. Dans cet exemple, vous créez une machine virtuelle Windows et la mettez à jour pour installer l’extension Symantec Endpoint Protection.

### Étape 1 : Création de la machine virtuelle avec un modèle

Si nécessaire, créez un dossier sur votre ordinateur pour stocker les fichiers de modèle. Indiquez le nom du dossier, puis exécutez ces commandes Azure PowerShell.

	$myFolder="<your folder path, such as C:\azure\templates\CreateVM>"
	$webClient=New-Object System.Net.WebClient
	$url="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-simple-windows-vm/azuredeploy.json"
	$filePath=$myFolder + "\azuredeploy.json"
	$webclient.DownloadFile($url,$filePath)
	$url = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-simple-windows-vm/azuredeploy.parameters.json"
	$filePath = $myFolder + "\azuredeploy.parameters.json"
	$webclient.DownloadFile($url,$filePath)

Dans votre dossier, ouvrez le fichier azuredeploy.parameters.json dans un éditeur de texte, spécifiez des valeurs pour les quatre paramètres, puis enregistrez le fichier.

Entrez un nouveau nom de déploiement, un nouveau nom de groupe de ressources et un emplacement Azure, puis exécutez les commandes suivantes.

	$deployName="<name for the new deployment>"
	$RGName="<name for the new Resource Group>"
	$locName="<an Azure location, such as West US>"
	cd $myFolder
	Switch-AzureMode AzureResourceManager
	New-AzureResourceGroup –Name $RGName –Location $locName
	New-AzureResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateFile azuredeploy.json -TemplateParameterFile azuredeploy.parameters.json

Le résultat suivant doit s’afficher.

	PS C:\azure\templates\windowsvm> $deployName="winvmexttest"
	PS C:\azure\templates\windowsvm> $RGName="winvmexttest"
	PS C:\azure\templates\windowsvm> $locname="West US"
	PS C:\azure\templates\windowsvm> New-AzureResourceGroup -Name $RGName -Location $locName
	VERBOSE: 11:22:02 AM - Created resource group 'winvmexttest' in location 'westus'
	
	
	ResourceGroupName : winvmexttest
	Location          : westus
	ProvisioningState : Succeeded
	Tags              :
	Permissions       :
	                    Actions  NotActions
	                    =======  ==========
	                    *
	
	ResourceId        : /subscriptions/a58ce54a-c262-460f-b8ef-fe36e6d5f5ec/resourceGroups/winvmexttest

	PS C:\azure\templates\windowsvm> New-AzureResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -Template
	File azuredeploy.json -TemplateParameterFile azuredeploy.parameters.json
	VERBOSE: 11:22:05 AM - Template is valid.
	VERBOSE: 11:22:05 AM - Create template deployment 'winvmexttest'.
	VERBOSE: 11:22:14 AM - Resource Microsoft.Storage/storageAccounts 'contososa' provisioning status is running
	VERBOSE: 11:22:21 AM - Resource Microsoft.Network/publicIPAddresses 'myPublicIP' provisioning status is running
	VERBOSE: 11:22:21 AM - Resource Microsoft.Network/virtualNetworks 'MyVNET' provisioning status is running
	VERBOSE: 11:22:37 AM - Resource Microsoft.Network/virtualNetworks 'MyVNET' provisioning status is succeeded
	VERBOSE: 11:22:39 AM - Resource Microsoft.Network/publicIPAddresses 'myPublicIP' provisioning status is succeeded
	VERBOSE: 11:22:41 AM - Resource Microsoft.Storage/storageAccounts 'contososa' provisioning status is succeeded
	VERBOSE: 11:22:43 AM - Resource Microsoft.Network/networkInterfaces 'myVMNic' provisioning status is succeeded
	VERBOSE: 11:22:52 AM - Resource Microsoft.Compute/virtualMachines 'MyWindowsVM' provisioning status is running
	VERBOSE: 11:26:36 AM - Resource Microsoft.Compute/virtualMachines 'MyWindowsVM' provisioning status is succeeded
	
	DeploymentName    : winvmexttest
	ResourceGroupName : winvmexttest
	ProvisioningState : Succeeded
	Timestamp         : 6/3/2015 6:26:38 PM
	Mode              : Incremental
	TemplateLink      :
	Parameters        :
	                    Name             Type                       Value
	                    ===============  =========================  ==========
	                    newStorageAccountName  String                     contososa
	                    adminUsername    String                     admin0987
	                    adminPassword    SecureString
	                    dnsNameForPublicIP  String                     contosovm
	                    windowsOSVersion  String                     2012-R2-Datacenter
	
	Outputs           :

Ensuite, connectez-vous à la machine virtuelle à partir du portail Azure en version préliminaire (**Parcourir > Machines virtuelles (v2) >** *Nom de la machine virtuelle* **> Connecter**).

Dans l’écran Démarrer, tapez **Symantec**. Notez que les composants Symantec Endpoint Protection ne sont pas installés (il n’existe aucun résultat de recherche avec « Symantec » dans le titre).

Fermez la connexion Bureau à distance.

### Étape 2 : Modification du fichier azuredeploy.json pour ajouter l’extension Symantec Endpoint Protection

Dans votre dossier, ouvrez le fichier azuredeploy.json avec un éditeur de texte de votre choix. Dans la section **variables**, ajoutez la ligne suivante juste après la ligne qui définit la variable publicIPAddressType :

	"vmExtensionName" : "SymantecExtension",

Dans la section **resources**, ajoutez la nouvelle section suivante juste avant la ligne avec le dernier crochet gauche « ] » :

	       {
	         "type": "Microsoft.Compute/virtualMachines/extensions",
	        "name": "[concat(variables('vmName'),'/', variables('vmExtensionName'))]",
	        "apiVersion": "2014-12-01-preview",
	        "location": "[variables('location')]",
	        "dependsOn": [
	            "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
	        ],
	        "properties": {
	            "publisher": "Symantec",
	            "type": "SymantecEndpointProtection",
	            "typeHandlerVersion": "12.1",
	            "settings": null,
	            "protectedSettings": null
	        }
	    }

Enregistrez le fichier azuredeploy.json avec ces changements. Vérifiez que les modifications ont été correctement effectuées, utilisez cette commande.

	Test-AzureResourceGroupTemplate -ResourceGroupName $RGName -TemplateFile azuredeploy.json

Si vous avez apporté les modifications correctement, vous devez voir ce qui suit.

	Template is valid.

Si vous ne voyez pas ce message, analysez le message d’erreur pour localiser la source de l’erreur.

### Étape 3 : Exécution du modèle modifié pour ajouter l’extension Symantec Endpoint Protection

Exécutez cette commande à l’invite de commandes Microsoft Azure PowerShell :

	New-AzureResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateFile azuredeploy.json -TemplateParameterFile azuredeploy.parameters.json

Le résultat suivant doit s’afficher.

	PS C:\azure\templates\winvmext> New-AzureResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateF	ile azuredeploy.json -TemplateParameterFile azuredeploy.parameters.json
	VERBOSE: 12:49:42 PM - Template is valid.
	VERBOSE: 12:49:42 PM - Create template deployment 'winvmexttest'.
	VERBOSE: 12:49:45 PM - Resource Microsoft.Network/publicIPAddresses 'myPublicIP' provisioning status is succeeded
	VERBOSE: 12:49:45 PM - Resource Microsoft.Network/virtualNetworks 'MyVNET' provisioning status is succeeded
	VERBOSE: 12:49:47 PM - Resource Microsoft.Storage/storageAccounts 'contososa' provisioning status is succeeded
	VERBOSE: 12:49:49 PM - Resource Microsoft.Network/networkInterfaces 'myVMNic' provisioning status is succeeded
	VERBOSE: 12:49:51 PM - Resource Microsoft.Compute/virtualMachines 'MyWindowsVM' provisioning status is running
	VERBOSE: 12:50:08 PM - Resource Microsoft.Compute/virtualMachines 'MyWindowsVM' provisioning status is succeeded
	VERBOSE: 12:50:15 PM - Resource Microsoft.Compute/virtualMachines/extensions 'MyWindowsVM/SymantecExtension'	provisioning status is running
	VERBOSE: 12:53:07 PM - Resource Microsoft.Compute/virtualMachines/extensions 'MyWindowsVM/SymantecExtension' provisioning status is succeeded
	
	
	DeploymentName    : winvmexttest
	ResourceGroupName : winvmexttest
	ProvisioningState : Succeeded
	Timestamp         : 6/3/2015 7:53:07 PM
	Mode              : Incremental
	TemplateLink      :
	Parameters        :
	                    Name             Type                       Value
	                    ===============  =========================  ==========
	                    newStorageAccountName  String                     contososa
	                    adminUsername    String                     admin0987
	                    adminPassword    SecureString
	                    dnsNameForPublicIP  String                     contosovm
	                    windowsOSVersion  String                     2012-R2-Datacenter
	
	Outputs           :

Connectez-vous à la machine virtuelle à partir du portail Azure en version préliminaire (**Parcourir > Machines virtuelles (v2) >** *Nom de la machine virtuelle* **> Connecter**).

Dans l’écran Démarrer, tapez **Symantec**. Le résultat affiché ressemble à ce qui suit, indiquant que l’extension Symantec Endpoint Protection est maintenant installée.

![](./media/virtual-machines-deploy-rmtemplates-powershell/SymantecExt.png)

## <a id="removerg"></a>TÂCHE : Supprimer un groupe de ressources

Vous pouvez supprimer n’importe quel groupe de ressources créé à l’aide de la commande **Remove-AzureResourceGroup**. Remplacez tous les éléments entre guillemets, y compris les caractères < and >, par les noms appropriés.

	Remove-AzureResourceGroup  -Name "<resource group name>"

Les informations suivantes s’affichent :

	Confirm
	Are you sure you want to remove resource group 'BuildRG'
	[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):

## <a id="logon"></a>TÂCHE : Se connecter à une machine virtuelle Windows

Dans le [portail Azure en version préliminaire](https://portal.azure.com/), cliquez sur **Tout parcourir > Machines virtuelles (v2) >** *Nom de la machine virtuelle* **> Connecter**.

Quand vous êtes invité à ouvrir ou enregistrer un fichier RDP, cliquez sur **Ouvrir**, puis sur **Connecter**. Tapez les informations d’identification d’un compte valide, puis cliquez sur **OK**.

Quand vous êtes invité à vous connecter malgré les erreurs de certificat, cliquez sur **Oui**.

## <a id="displayvm"></a>TÂCHE : Afficher les informations relatives à une machine virtuelle

La commande **Get-AzureVM** vous permet de voir les informations relatives à une machine virtuelle. Cette commande renvoie un objet de machine virtuelle qui peut être manipulé à l’aide de diverses applets de commande pour mettre à jour l’état de la machine virtuelle. Remplacez tous les éléments entre guillemets, y compris les caractères < and >, par les noms appropriés.

	Get-AzureVM -ResourceGroupName "<resource group name>" -Name "<VM name>"

Les informations suivantes relatives à votre machine virtuelle s’affichent.

	AvailabilitySetReference : null
	Extensions               : []
	HardwareProfile          : {
	                             "VirtualMachineSize": "Standard_D1"
	                           }
	Id                       : /subscriptions/fd92919d-eeca-4f5b-840a-e45c6770d92e/resourceGroups/BuildRG/providers/Microso
	                           ft.Compute/virtualMachines/MyWindowsVM
	InstanceView             : null
	Location                 : westus
	Name                     : MyWindowsVM
	NetworkProfile           : {
	                             "NetworkInterfaces": [
	                               {
	                                 "Primary": null,
	                                 "ReferenceUri": "/subscriptions/fd92919d-eeca-4f5b-840a-e45c6770d92e/resourceGroups/Bu
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
	                                 "Uri": "http://buildsaacct.blob.core.windows.net/vhds/osdiskforwindowssimple.vhd"
	                               }
	                             },
	                             "SourceImage": null
	                           }
	Tags                     : {}
	Type                     : Microsoft.Compute/virtualMachines


## <a id="start"></a>TÂCHE : Démarrer une machine virtuelle

Vous pouvez démarrer une machine virtuelle à l’aide de la commande **Start-AzureVM**. Remplacez tous les éléments entre guillemets, y compris les caractères < and >, par les noms appropriés.

	Start-AzureVM -ResourceGroupName "<resource group name>" -Name "<VM name>"

Les informations suivantes s’affichent :

	EndTime             : 4/28/2015 11:11:41 AM -07:00
	Error               :
	Output              :
	StartTime           : 4/28/2015 11:10:35 AM -07:00
	Status              : Succeeded
	TrackingOperationId : e1705973-d266-467e-8655-920016145347
	RequestId           : aac41de1-b85d-4429-9a3d-040b922d2e6d
	StatusCode          : OK

## <a id="stop"></a>TÂCHE : Arrêter une machine virtuelle

Vous pouvez arrêter une machine virtuelle à l’aide de la commande **Stop-AzureVM**. Remplacez tous les éléments entre guillemets, y compris les caractères < and >, par les noms appropriés.

	Stop-AzureVM -ResourceGroupName "<resource group name>" -Name "<VM name>"

Les informations suivantes s’affichent :

	Virtual machine stopping operation
	This cmdlet will stop the specified virtual machine. Do you want to continue?
	[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):
	
	
	EndTime             : 4/28/2015 11:09:08 AM -07:00
	Error               :
	Output              :
	StartTime           : 4/28/2015 11:06:55 AM -07:00
	Status              : Succeeded
	TrackingOperationId : 0c94dc74-c553-412c-a187-108bdb29657e
	RequestId           : 5cc9ddba-0643-4b5e-82b6-287b321394ee
	StatusCode          : OK

## <a id=restart"></a>TÂCHE : Redémarrer une machine virtuelle

Vous pouvez redémarrer une machine virtuelle à l’aide de la commande **Restart-AzureVM**. Remplacez tous les éléments entre guillemets, y compris les caractères < and >, par les noms appropriés.

	Restart-AzureVM -ResourceGroupName "<resource group name>" -Name "<VM name>"

Les informations suivantes s’affichent :

	EndTime             : 4/28/2015 11:16:26 AM -07:00
	Error               :
	Output              :
	StartTime           : 4/28/2015 11:16:25 AM -07:00
	Status              : Succeeded
	TrackingOperationId : 390571e0-c804-43ce-88c5-f98e0feb588e
	RequestId           : 7dac33e3-0164-4a08-be33-96205284cb0b
	StatusCode          : OK

## <a id=delete"></a>TÂCHE : Supprimer une machine virtuelle

Vous pouvez supprimer une machine virtuelle à l’aide de la commande **Remove-AzureVM**. Remplacez tous les éléments entre guillemets, y compris les caractères < and >, par les noms appropriés. Utilisez le paramètre **-Force** pour ignorer l’invite de confirmation.

	Remove-AzureVM -ResourceGroupName "<resource group name>" –Name "<VM name>"

Les informations suivantes s’affichent :

	Virtual machine removal operation
	This cmdlet will remove the specified virtual machine. Do you want to continue?
	[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):
	
	
	EndTime             : 4/28/2015 11:21:55 AM -07:00
	Error               :
	Output              :
	StartTime           : 4/28/2015 11:20:13 AM -07:00
	Status              : Succeeded
	TrackingOperationId : f74fad9e-f6bc-46ae-82b1-bfad3952aa44
	RequestId           : 6a30d2e0-63ca-43cf-975b-058631e048e7
	StatusCode          : OK

## Ressources supplémentaires

[Fournisseurs de calcul, de réseau et de stockage Azure dans Azure Resource Manager](virtual-machines-azurerm-versus-azuresm.md)

[Présentation d’Azure Resource Manager](../resource-group-overview.md)

[Déploiement et gestion de machines virtuelles à l’aide des modèles Azure Resource Manager et de l’interface de ligne de commande Azure](virtual-machines-deploy-rmtemplates-azure-cli.md)

[Documentation sur les machines virtuelles](http://azure.microsoft.com/documentation/services/virtual-machines/)

[Installation et configuration d’Azure PowerShell](../install-configure-powershell.md)

<!---HONumber=July15_HO2-->