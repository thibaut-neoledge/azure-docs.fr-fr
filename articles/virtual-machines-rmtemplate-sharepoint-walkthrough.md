<properties 
	pageTitle="Le modèle du Gestionnaire des ressources de batterie de serveurs SharePoint haute disponibilité" 
	description="Découvrez la structure du modèle Azure Resource Manager pour la batterie de serveurs SharePoint haute disponibilité." 
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

# Le modèle du Gestionnaire des ressources de batterie de serveurs SharePoint haute disponibilité

Cette rubrique vous explique la structure du fichier de modèle azuredeploy.json pour la batterie de serveurs SharePoint haute disponibilité.

## section "parameters"

Les "parameters" spécifient les paramètres qui sont utilisés pour saisir des données dans ce modèle. Il est possible de définir 50 paramètres maximum. Voici un exemple de paramètre relatif à l’emplacement d’Azure :

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

	"RDPNAT":"RDP", 
	"spWebLB":"spWeb", 
	"SQLAOListener":"SQLAlwaysOnEndPointListener", 
	"SQLAOProbe":"SQLAlwaysOnEndPointProbe", 
	"staticSubnetName": "staticSubnet", 
	"sqlSubnetName": "sqlSubnet", 
	"spwebSubnetName": "spwebSubnet", 
	"spappSubnetName": "spappSubnet", 

## section "resources"

La section **"resources"** spécifie les informations nécessaires au déploiement des ressources de la batterie de serveurs SharePoint dans un groupe de ressources. Il est possible de définir 250 ressources maximum. Les niveaux de profondeur de celles-ci ne peuvent pas être supérieurs à 5.

Cette section comporte les sous-sections suivantes :

### Microsoft.Storage/storageAccounts  

Cette section crée un nouveau compte de stockage pour toutes les ressources de disques durs virtuels et de disques de la batterie de serveurs. Voici le code JSON pour le compte de stockage :

	{
	  "type": "Microsoft.Storage/storageAccounts",
	  "name": "[parameters('newStorageAccountName')]",
	  "apiVersion": "2014-12-01-preview",
	  "location": "[parameters('deploymentLocation')]",
	  "properties": {
		"accountType": "[parameters('storageAccountType')]"
	  }
	},

### Microsoft.Network/publicIPAddresses

Ces sections créent un ensemble d’adresses IP publiques via lequel chaque machine virtuelle peut être atteinte via Internet. Voici un exemple :

	{
		"apiVersion": "2014-12-01-preview",
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

Ces sections créent quatre groupes à haute disponibilité, un pour chaque niveau de déploiement :

- contrôleurs de domaine Active Directory ;
- cluster SQL Server ;
- serveurs de couche application ;
- serveurs de couche web

Voici un exemple :

	{
		"type": "Microsoft.Compute/availabilitySets",
		"name": "[variables('spAvailabilitySetName')]",
		"apiVersion": "2014-12-01-preview",
		"location": "[parameters('deploymentLocation')]"
	},

### Microsoft.Network/virtualNetworks

Cette section crée un réseau virtuel cloud uniquement avec quatre sous-réseaux \(un pour chaque niveau de déploiement\), dans lequel les machines virtuelles sont placées. Voici le code JSON :

	{
		"name": "[parameters('virtualNetworkName')]",
		"type": "Microsoft.Network/virtualNetworks",
		"location": "[parameters('deploymentLocation')]",
		"apiVersion": "2014-12-01-preview",
		"properties": {
			"addressSpace": {
			"addressPrefixes": [
				"[parameters('virtualNetworkPrefix')]"
			]
			},
			"subnets": "[variables('subnets')]"
		}
	},



### Microsoft.Network/loadBalancers

Ces sections créent des instances d’équilibrage de charge pour chaque machine virtuelle pour fournir des NAT et le filtrage pour le trafic entrant à partir d’Internet. Pour chaque équilibrage de charge, les paramètres configurent les règles NAT frontales, principales et entrantes. Par exemple, il existe des règles de trafic Bureau à distance pour chaque machine virtuelle et une règle pour autoriser le trafic web entrant \(port TCP 80\) à partir d’Internet pour les serveurs de la couche web. Voici l’exemple relatif au serveur de la couche web :

        {
            "apiVersion": "2014-12-01-preview",
            "name": "[variables('splbName')]",
            "type": "Microsoft.Network/loadBalancers",
            "location": "[parameters('deploymentLocation')]",
            "dependsOn": [
                "[resourceId('Microsoft.Network/publicIPAddresses',variables('spIPAddressName'))]"
            ],
            "properties": {
                "frontendIPConfigurations": [
                    {
                        "name": "[variables('spLBFE')]",
                        "properties": {
                            "publicIPAddress": {
                                "id": "[resourceId('Microsoft.Network/publicIPAddresses',variables('spIPAddressName'))]"
                            },
                        }
                    }
                ],
                "backendAddressPools": [
                    {
                        "name": "[variables('spWebLBBE')]"
                    }
                ],
                "loadBalancingRules": [
                    {
                        "name": "[variables('spWebLB')]",
                        "properties": {
                        "frontendIPConfiguration": {
                            "id": "[variables('splbFEConfigID')]"
                        },
                        "probe": {
                            "id": "[variables('spwebProbeID')]"
                        },
                        "protocol": "tcp",
                        "frontendPort": 80,
                        "backendPort": 80,
                        "enableFloatingIP": false
                        }
                    }
                ],
                "probes": [
                    {
                        "name": "[variables('spWebProbe')]",
                        "properties": {
                            "protocol": "http",
                            "port": "[variables('spWebProbePort')]",
                            "intervalInSeconds": "15",
                            "numberOfProbes": "5",
                            "requestPath":"/"
                        }
                    }
                ]
            }
        },

### Microsoft.Network/networkInterfaces

Ces sections créent une interface réseau pour chaque machine virtuelle et configurent des adresses IP statiques pour les contrôleurs de domaine. Voici l’exemple relatif à l’interface réseau pour le contrôleur de domaine principal :

	{
		"name": "[variables('adPDCNicName')]",
		"type": "Microsoft.Network/networkInterfaces",
		"location": "[parameters('deploymentLocation')]",
		"dependsOn": [
			"[parameters('virtualNetworkName')]",
			"[concat('Microsoft.Network/loadBalancers/',variables('rdpLBName'))]"
		],
		"apiVersion": "2014-12-01-preview",
		"properties": {
			"ipConfigurations": [
				{
					"name": "ipconfig1",
					"properties": {
						"privateIPAllocationMethod": "Static",
						"privateIPAddress" :"[parameters('adPDCNicIPAddress')]",
						"subnet": {
							"id": "[variables('staticSubnetRef')]"
						},
						"loadBalancerBackendAddressPools": [
							{
								"id":"[variables('adBEAddressPoolID')]"
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

Ces sections créent et configurent les neuf machines virtuelles du déploiement.

Les deux premières sections principales créent et configurent les contrôleurs du déploiement. Chaque section :

- spécifie le compte de stockage, le groupe à haute disponibilité, l’interface réseau et l’instance d’équilibrage de charge pour chaque machine virtuelle de contrôleur de domaine ;
- ajoute un disque supplémentaire à chaque machine virtuelle de contrôleur de domaine ;
- exécute le script PowerShell pour configurer les machines virtuelles comme contrôleurs de domaine.

Voici l’exemple relatif au contrôleur de domaine principal :

        {
            "apiVersion": "2014-12-01-preview",
            "type": "Microsoft.Compute/virtualMachines",
            "name": "[variables('adPDCVMName')]",
            "location": "[parameters('deploymentLocation')]",
            "dependsOn": [
                "[resourceId('Microsoft.Storage/storageAccounts',parameters('newStorageAccountName'))]",
                "[resourceId('Microsoft.Network/networkInterfaces',variables('adPDCNicName'))]",
                "[resourceId('Microsoft.Compute/availabilitySets', variables('adAvailabilitySetName'))]",
                "[resourceId('Microsoft.Network/loadBalancers',variables('rdpLBName'))]"
            ],
            "properties": {
                "hardwareProfile": {
                    "vmSize": "[parameters('adVMSize')]"
                },
                "availabilitySet": {
                    "id": "[resourceId('Microsoft.Compute/availabilitySets', variables('adAvailabilitySetName'))]"
                },
                "osProfile": {
                    "computername": "[variables('adPDCVMName')]",
                    "adminUsername": "[parameters('adminUsername')]",
                    "adminPassword": "[parameters('adminPassword')]",
                    "windowsProfile": {
                        "provisionVMAgent": "true"
                    }
                },
                "storageProfile": {
                    "sourceImage": {
                        "id": "[variables('adSourceImageName')]"
                    },
                    "destinationVhdsContainer": "[concat('http://',parameters('newStorageAccountName'),'.blob.core.windows.net/',parameters('vmContainerName'),'/')]",
                    "dataDisks": [
                        {
                            "vhd": {
                                "uri":"[concat('http://',parameters('newStorageAccountName'),'.blob.core.windows.net/',parameters('vmContainerName'),'/', variables('adPDCVMName'),'data-1.vhd')]"
                                },
                            "name":"[concat(variables('adPDCVMName'),'-data-disk1')]",
                            "caching" : "None",
                            "diskSizeGB": "[variables('adDataDiskSize')]",
                            "lun": 0
                        }
                    ]
                },
                "networkProfile": {
                    "networkInterfaces": [
                        {
                            "id": "[resourceId('Microsoft.Network/networkInterfaces',variables('adPDCNicName'))]"
                        }
                    ]
                }
            },
            "resources" :[
                {
                    "type": "Microsoft.Compute/virtualMachines/extensions",
                    "name": "[concat(variables('adPDCVMName'),'/InstallDomainController')]",
                    "apiVersion": "2014-12-01-preview",
                    "location": "[parameters('deploymentLocation')]",
                    "dependsOn":[
                        "[resourceId('Microsoft.Compute/virtualMachines', variables('adPDCVMName'))]"
                    ],
                    "properties": {
                        "publisher": "Microsoft.Powershell",
                        "type": "DSC",
                        "typeHandlerVersion": "1.7",
                        "settings": {
                            "ModulesUrl": "[variables('adPDCModulesURL')]",
                            "ConfigurationFunction": "[variables('adPDCConfigurationFunction')]",
                            "Properties": {
                                "DomainName": "[parameters('domainName')]",
                                "AdminCreds":{
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


Une section supplémentaire après chaque contrôleur de domaine commençant par **"name": "UpdateVNetDNS"** configure les serveurs DNS du réseau virtuel de sorte qu’ils utilisent les adresses IP statiques des deux contrôleurs de domaine.

Les trois sections **"type": "Microsoft.Compute/virtualMachines"** suivantes créent les machines virtuelles du cluster SQL Server au sein du déploiement. Chaque section :

- spécifie le compte de stockage, le groupe à haute disponibilité, l’équilibrage de charge, le réseau virtuel et l’interface réseau pour chaque machine virtuelle ;
- ajoute deux disques supplémentaires à chaque serveur SQL.

Les autres sections **"Microsoft.Compute/virtualMachines/extensions"** font appel au script PowerShell pour configurer les machines virtuelles du cluster SQL Server, le cluster SQL server et activent les groupes de disponibilité AlwaysOn.

Les quatre sections **"type": "Microsoft.Compute/virtualMachines"** suivantes créent les machines virtuelles de la batterie de serveurs SharePoint au sein du déploiement. Chaque section spécifie le compte de stockage, le groupe à haute disponibilité, l’équilibrage de charge, le réseau virtuel et l’interface réseau pour chaque machine virtuelle.

Les autres sections **"Microsoft.Compute/virtualMachines/extensions"** font appel à des scripts PowerShell pour configurer les serveurs SharePoint sous forme de batterie.

Notez l’organisation générale des sous-sections de la **"resources"** section du fichier JSON :

1.	Créez les éléments de l’infrastructure Azure requis pour prendre en charge plusieurs machines virtuelles \(un compte de stockage, des adresses IP publiques, des groupes à haute disponibilité, un réseau virtuel, des interfaces réseau, des instances de programme d’équilibrage de charge\).
2.	Créez les machines virtuelles du contrôleur de domaine qui utilisent les éléments communs et spécifiques de l’infrastructure Azure créés préalablement, ajoutez des disques de données et exécutez les scripts PowerShell. De plus, mettez à jour le réseau virtuel de sorte qu’il utilise les adresses IP statiques des contrôleurs de domaine.
3.	Créez les machines virtuelles du cluster SQL Server qui utilisent les éléments communs et spécifiques de l’infrastructure Azure créés préalablement pour les contrôleurs de domaine, ajoutez des disques de données et exécutez les scripts PowerShell pour configurer le cluster et les groupes de disponibilité AlwaysOn SQL Server.
4.	Créez les machines virtuelles du serveur SharePoint qui utilisent les éléments communs et spécifiques de l’infrastructure Azure créés préalablement, ajoutez des disques de données et exécutez les scripts PowerShell pour configurer la batterie de serveurs SharePoint.

Vous devez suivre les mêmes étapes pour votre propre modèle JSON visant à créer une infrastructure à plusieurs niveaux :

1.	Créez les éléments communs \(compte de stockage, réseau virtuel\), propres au niveau \(groupes à haute disponibilité\) et propres à la machine virtuelle \(adresses IP publiques, groupes à haute disponibilité, interfaces réseau et instances d’équilibrage de charge\) de l’infrastructure Azure requis pour votre déploiement.
2.	Pour chaque niveau de votre application \(par exemple l’authentification, la base de données, le web\), créez et configurez les serveurs de ce niveau à l’aide des éléments communs \(compte de stockage, réseau virtuel\), propres au niveau \(groupes à haute disponibilité\) et propres à la machine virtuelle \(adresses IP publiques, interfaces réseau et instances d’équilibrage de charge\).

Pour plus d’informations, consultez [Langage du modèle Azure Resource Manager](https://msdn.microsoft.com/library/azure/dn835138.aspx).


## Ressources supplémentaires

[Fournisseurs de calcul, de réseau et de stockage Azure dans Azure Resource Manager](virtual-machines-azurerm-versus-azuresm.md)

[Présentation d’Azure Resource Manager](resource-group-overview.md)

[Création de modèles Azure Resource Manager](resource-group-authoring-templates.md)

[Documentation sur les machines virtuelles](http://azure.microsoft.com/documentation/services/virtual-machines/)


<!--HONumber=52-->
