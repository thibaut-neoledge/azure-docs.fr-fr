<properties 
	pageTitle="Modèle Resource Manager pour la batterie de serveurs SharePoint à trois serveurs" 
	description="Découvrez la structure du modèle Azure Resource Manager pour la batterie de serveurs SharePoint à trois serveurs." 
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

# Modèle Resource Manager pour la batterie de serveurs SharePoint à trois serveurs

Cette rubrique vous explique la structure du fichier de modèle azuredeploy.json pour la batterie de serveurs SharePoint à trois serveurs. Vous pouvez également voir le contenu de ce modèle dans votre navigateur à partir d’[ici](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/sharepoint-three-vm/azuredeploy.json).

Si vous souhaitez examiner une copie locale du fichier azuredeploy.json, spécifiez un dossier local comme emplacement du fichier et créez ce dossier (par exemple, C:\\Azure\\Templates\\SharePointFarm). Indiquez le nom du dossier, puis exécutez ces commandes à l’invite de commandes Azure PowerShell.

	$folderName="<folder name, such as C:\Azure\Templates\SharePointFarm>"
	$webclient = New-Object System.Net.WebClient
	$url = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/sharepoint-three-vm/azuredeploy.json"	
	$filePath = $folderName + "\azuredeploy.json"
	$webclient.DownloadFile($url,$filePath)

Ouvrez le modèle azuredeploy.json dans un éditeur de texte ou dans un outil de votre choix. La section suivante décrit la structure du fichier de modèle et l'objectif de chaque section.

## Section "parameters"

La section « parameters » spécifie les paramètres qui sont utilisés pour l’entrée de données dans ce modèle. Il est possible de définir 50 paramètres maximum. Voici un exemple de paramètre relatif à l’emplacement d’Azure :

	"deploymentLocation": {
		"type": "string",
		"allowedValues": [
			"West US",
			"East US",
			"West Europe",
			"East Asia",
			"Southeast Asia"
		],
		"metadata": {
			"Description": "The region to deploy the resources into"
		},
		"defaultValue":"West Europe"
	},

## section "variables"

La section "variables" spécifie les variables qui peuvent être utilisées dans ce modèle. Il est possible de définir 100 variables maximum. Voici quelques exemples :

	"LBFE": "LBFE",
	"LBBE": "LBBE",
	"RDPNAT": "RDP",
	"spWebNAT": "spWeb",
	"adSubnetName": "adSubnet",
	"sqlSubnetName": "sqlSubnet",
	"spSubnetName": "spSubnet",
	"adNicName": "adNic",
	"sqlNicName": "sqlNic",
	"spNicName": "spNic",

## section "resources"

La section **"resources"** spécifie les informations nécessaires au déploiement des ressources de la batterie de serveurs SharePoint dans un groupe de ressources. Il est possible de définir 250 ressources maximum. Les niveaux de profondeur de celles-ci ne peuvent pas être supérieurs à 5.

Cette section comporte les sous-sections suivantes :

### Microsoft.Storage/storageAccounts  

Cette section crée un nouveau compte de stockage pour toutes les ressources de disques durs virtuels et de disques de la batterie de serveurs. Voici le code JSON pour le compte de stockage :

	{
	  "type": "Microsoft.Storage/storageAccounts",
	  "name": "[parameters('newStorageAccountName')]",
	  "apiVersion": "2015-05-01-preview",
	  "location": "[parameters('deploymentLocation')]",
	  "properties": {
		"accountType": "[parameters('storageAccountType')]"
	  }
	},

### Microsoft.Network/publicIPAddresses

Ces sections créent un ensemble d’adresses IP publiques via lequel chaque machine virtuelle peut être atteinte via Internet. Voici un exemple :

	{
		"apiVersion": "2015-05-01-preview",
		"type": "Microsoft.Network/publicIPAddresses",
		"name": "[variables('adpublicIPAddressName')]",
		"location": "[parameters('deploymentLocation')]",
		"properties": {
			"publicIPAllocationMethod": "[parameters('publicIPAddressType')]",
			"dnsSettings": {
				"domainNameLabel": "[parameters('adDNSPrefix')]"
			}
		}
	},

### Microsoft.Compute/availabilitySets

Ces sections créent trois groupes à haute disponibilité, un pour chaque niveau de déploiement :

- contrôleurs de domaine Active Directory ;
- cluster SQL Server ;
- Serveurs SharePoint

Voici un exemple :

	{
		"type": "Microsoft.Compute/availabilitySets",
		"name": "[variables('spAvailabilitySetName')]",
		"apiVersion": "2015-05-01-preview",
		"location": "[parameters('deploymentLocation')]"
	},

### Microsoft.Network/virtualNetworks

Cette section crée un réseau virtuel cloud uniquement avec trois sous-réseaux (un pour chaque niveau de déploiement), dans lequel les machines virtuelles sont placées. Voici le code JSON :

	{
		"name": "[parameters('virtualNetworkName')]",
		"type": "Microsoft.Network/virtualNetworks",
		"location": "[parameters('deploymentLocation')]",
		"apiVersion": "2015-05-01-preview",
		"properties": {
			"addressSpace": {
			"addressPrefixes": [
				"[parameters('virtualNetworkAddressRange')]"
			]
			},
			"subnets": "[variables('subnets')]"
		}
	},


### Microsoft.Network/loadBalancers

Ces sections créent des instances d’équilibrage de charge pour chaque machine virtuelle pour fournir des NAT et le filtrage pour le trafic entrant à partir d’Internet. Pour chaque équilibrage de charge, les paramètres configurent les règles NAT frontales, principales et entrantes. Par exemple, il existe des règles de trafic Bureau à distance pour chaque machine virtuelle et, pour le serveur SharePoint, une règle pour autoriser le trafic web entrant (port TCP 80) à partir d’Internet. Voici l’exemple relatif au serveur SharePoint :


	{
		"apiVersion": "2015-05-01-preview",
		"name": "[variables('spLBName')]",
		"type": "Microsoft.Network/loadBalancers",
		"location": "[parameters('deploymentLocation')]",
		"dependsOn": [
			"[resourceId('Microsoft.Network/publicIPAddresses',variables('sppublicIPAddressName'))]"
		],
		"properties": {
			"frontendIPConfigurations": [
				{
					"name": "[variables('LBFE')]",
					"properties": {
						"publicIPAddress": {
							"id": "[resourceId('Microsoft.Network/publicIPAddresses',variables('sppublicIPAddressName'))]"
						}
					}
				}
			],
			"backendAddressPools": [
				{
					"name": "[variables('LBBE')]"
				}
			],
			"inboundNatRules": [
				{
					"name": "[variables('RDPNAT')]",
					"properties": {
						"frontendIPConfiguration": {
							"id": "[variables('splbFEConfigID')]"
						},
						"protocol": "tcp",
						"frontendPort": "[parameters('RDPPort')]",
						"backendPort": 3389,
						"enableFloatingIP": false
					}
				},
				{
					"name": "[variables('spWebNAT')]",
					"properties": {
						"frontendIPConfiguration": {
							"id": "[variables('splbFEConfigID')]"
						},
						"protocol": "tcp",
						"frontendPort": 80,
						"backendPort": 80,
						"enableFloatingIP": false
					}
				}
			]
		}
	},

### Microsoft.Network/networkInterfaces

Ces sections créent une interface réseau pour chaque machine virtuelle et configurent une adresse IP statique pour le contrôleur de domaine. Voici l’exemple relatif à l’interface réseau du contrôleur de domaine :

	{
		"name": "[variables('adNicName')]",
		"type": "Microsoft.Network/networkInterfaces",
		"location": "[parameters('deploymentLocation')]",
		"dependsOn": [
			"[parameters('virtualNetworkName')]",
			"[concat('Microsoft.Network/loadBalancers/',variables('adlbName'))]"
		],
		"apiVersion": "2015-05-01-preview",
		"properties": {
			"ipConfigurations": [
				{
					"name": "ipconfig1",
					"properties": {
						"privateIPAllocationMethod": "Static",
						"privateIPAddress": "[parameters('adNicIPAddress')]",
						"subnet": {
							"id": "[variables('adSubnetRef')]"
						},
						"loadBalancerBackendAddressPools": [
							{
								"id": "[variables('adBEAddressPoolID')]"
							}
						],
						"loadBalancerInboundNatRules": [
							{
								"id": "[variables('adRDPNATRuleID')]"
							}
						]
					}
				}
			]
		}
	},


### Microsoft.Compute/virtualMachines

Ces sections créent et configurent les trois machines virtuelles du déploiement.

La première section crée et configure le contrôleur de domaine qui :

- spécifie le compte de stockage, le groupe à haute disponibilité, l’interface réseau et l’instance d’équilibrage de charge ;
- ajoute un disque supplémentaire ;
- exécute un script PowerShell pour configurer le contrôleur de domaine.

Voici le code JSON :

		{
			"apiVersion": "2015-05-01-preview",
			"type": "Microsoft.Compute/virtualMachines",
			"name": "[parameters('adVMName')]",
			"location": "[parameters('deploymentLocation')]",
			"dependsOn": [
				"[resourceId('Microsoft.Storage/storageAccounts',parameters('newStorageAccountName'))]",
				"[resourceId('Microsoft.Network/networkInterfaces',variables('adNicName'))]",
				"[resourceId('Microsoft.Compute/availabilitySets', variables('adAvailabilitySetName'))]",
				"[resourceId('Microsoft.Network/loadBalancers',variables('adlbName'))]"
			],
			"properties": {
				"hardwareProfile": {
					"vmSize": "[parameters('adVMSize')]"
				},
				"availabilitySet": {
					"id": "[resourceId('Microsoft.Compute/availabilitySets', variables('adAvailabilitySetName'))]"
				},
				"osProfile": {
					"computername": "[parameters('adVMName')]",
					"adminUsername": "[parameters('adminUsername')]",
					"adminPassword": "[parameters('adminPassword')]"
				},
				"storageProfile": {
					"imageReference": {
						"publisher": "[parameters('adImagePublisher')]",
						"offer": "[parameters('adImageOffer')]",
						"sku": "[parameters('adImageSKU')]",
						"version": "latest"
					},
					"osDisk": {
						"name": "osdisk",
						"vhd": {
							"uri": "[concat('http://',parameters('newStorageAccountName'),'.blob.core.windows.net/',parameters('vmContainerName'),'/',parameters('adVMName'),'-osdisk.vhd')]"
						},
						"caching": "ReadWrite",
						"createOption": "FromImage"
					},
					"dataDisks": [
						{
							"vhd": {
								"uri": "[concat('http://',parameters('newStorageAccountName'),'.blob.core.windows.net/',parameters('vmContainerName'),'/', variables('adDataDisk'),'-1.vhd')]"
							},
							"name": "[concat(parameters('adVMName'),'-data-disk1')]",
							"caching": "None",
							"createOption": "empty",
							"diskSizeGB": "[variables('adDataDiskSize')]",
							"lun": 0
						}
					]
				},
				"networkProfile": {
					"networkInterfaces": [
						{
							"id": "[resourceId('Microsoft.Network/networkInterfaces',variables('adNicName'))]"
						}
					]
				}
			},
			"resources": [
				{
					"type": "Microsoft.Compute/virtualMachines/extensions",
					"name": "[concat(parameters('adVMName'),'/InstallDomainController')]",
					"apiVersion": "2015-05-01-preview",
					"location": "[parameters('deploymentLocation')]",
					"dependsOn": [
						"[resourceId('Microsoft.Compute/virtualMachines', parameters('adVMName'))]"
					],
					"properties": {
						"publisher": "Microsoft.Powershell",
						"type": "DSC",
						"typeHandlerVersion": "1.7",
						"settings": {
							"ModulesUrl": "[variables('adModulesURL')]",
							"ConfigurationFunction": "[variables('adConfigurationFunction')]",
							"Properties": {
								"DomainName": "[parameters('domainName')]",
								"AdminCreds": {
									"UserName": "[parameters('adminUserName')]",
									"Password": "PrivateSettingsRef:AdminPassword"
								}
							}
						},
						"protectedSettings": {
							"Items": {
								"AdminPassword": "[parameters('adminPassword')]"
							}
						}
					}
				}
			]
		},

Une section supplémentaire pour le contrôleur de domaine commençant par **"name": "UpdateVNetDNS"** configure le serveur DNS du réseau virtuel pour qu’il utilise l’adresse IP statique du contrôleur de domaine.

La section **"type": "Microsoft.Compute/virtualMachines"** suivante crée les machines virtuelles SQL Server du déploiement et :

- spécifie le compte de stockage, le groupe à haute disponibilité, l’équilibrage de charge, le réseau virtuel et l’interface réseau ;
- ajoute un disque supplémentaire.

Les autres sections **"Microsoft.Compute/virtualMachines/extensions"** font appel au script PowerShell pour configurer le serveur SQL Server.

La section **"type": "Microsoft.Compute/virtualMachines"** suivante crée la machine virtuelle SharePoint du déploiement en spécifiant le compte de stockage, le groupe à haute disponibilité, l’équilibrage de charge, le réseau virtuel et l’interface réseau. Une autre section **"Microsoft.Compute/virtualMachines/extensions"** appelle un script PowerShell pour configurer la batterie de serveurs SharePoint.

Notez l’organisation générale des sous-sections de la **"resources"** section du fichier JSON :

1.	Créez les éléments de l’infrastructure Azure requis pour prendre en charge plusieurs machines virtuelles (un compte de stockage, des adresses IP publiques, des groupes à haute disponibilité, un réseau virtuel, des interfaces réseau, des instances de programme d’équilibrage de charge).
2.	Créez la machine virtuelle du contrôleur de domaine qui utilise les éléments communs et spécifiques de l’infrastructure Azure précédemment créés, ajoute un disque de données et exécute un script PowerShell. En outre, mettez à jour le réseau virtuel pour qu’il utilise l’adresse IP statique du contrôleur de domaine.
3.	Créez la machine virtuelle SQL Server qui utilise les éléments communs et spécifiques de l’infrastructure Azure précédemment créés pour le contrôleur de domaine, ajoute des disques de données et exécute un script PowerShell pour configurer le serveur SQL Server.
4.	Créez la machine virtuelle du serveur SharePoint qui utilise les éléments communs et spécifiques de l’infrastructure Azure précédemment créés et exécute un script PowerShell pour configurer la batterie de serveurs SharePoint.

Vous devez suivre les mêmes étapes pour votre propre modèle JSON visant à créer une infrastructure à plusieurs niveaux :

1.	Créez les éléments communs (compte de stockage, réseau virtuel), propres au niveau (groupes à haute disponibilité) et propres à la machine virtuelle (adresses IP publiques, groupes à haute disponibilité, interfaces réseau et instances d’équilibrage de charge) de l’infrastructure Azure requis pour votre déploiement.
2.	Pour chaque niveau de votre application (par exemple l’authentification, la base de données, le web), créez et configurez les serveurs de ce niveau à l’aide des éléments communs (compte de stockage, réseau virtuel), propres au niveau (groupes à haute disponibilité) et propres à la machine virtuelle (adresses IP publiques, interfaces réseau et instances d’équilibrage de charge).

Pour plus d’informations, consultez [Langage du modèle Azure Resource Manager](https://msdn.microsoft.com/library/azure/dn835138.aspx).

## Ressources supplémentaires

[Fournisseurs de calcul, de réseau et de stockage Azure dans Azure Resource Manager](virtual-machines-azurerm-versus-azuresm.md)

[Présentation d’Azure Resource Manager](../resource-group-overview.md)

[Création de modèles Azure Resource Manager](../resource-group-authoring-templates.md)

[Documentation sur les machines virtuelles](http://azure.microsoft.com/documentation/services/virtual-machines/)

 

<!---HONumber=July15_HO2-->