<properties
	pageTitle="Mettre à l’échelle automatiquement des jeux de mise à l’échelle de machines virtuelles Windows | Microsoft Azure"
	description="Mettre à l’échelle automatiquement un jeu de mise à l’échelle de machine virtuelle Windows à l’aide d’Azure PowerShell"
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
	ms.date="03/22/2016"
	ms.author="davidmu"/>

# Mise à l’échelle automatique des machines dans un groupe de machines virtuelles à échelle identique

Les groupes de machines virtuelles à échelle identique facilitent le déploiement et la gestion de machines virtuelles identiques en tant que groupe. Les groupes à échelle identique fournissent une couche de calcul hautement évolutive et personnalisable pour les applications « hyperscale », et prennent en charge les images de plateforme Windows, les images de plateforme Linux, des images personnalisées et les extensions. Pour plus d’informations sur les jeux de mise à l’échelle, consultez [Jeux de mise à l’échelle de machine virtuelle](virtual-machine-scale-sets-overview.md).

Ce didacticiel vous montre comment créer un groupe de machines virtuelles identiques avec des machines virtuelles Windows et mettre automatiquement à l’échelle les machines du groupe. Vous pouvez le faire en créant un modèle Azure Resource Manager et en le déployant à l’aide d’Azure PowerShell. Pour en savoir plus sur les modèles, consultez [Création de modèles Azure Resource Manager](../resource-group-authoring-templates.md). Pour plus d'informations sur la mise à l'échelle automatique de jeux de mise à l'échelle, consultez la rubrique [Mise à l'échelle automatique et jeux de mise à l'échelle de machines virtuelles](virtual-machine-scale-sets-autoscale-overview.md).

Dans ce didacticiel, vous allez déployer les ressources et les extensions suivantes :

- Microsoft.Storage/storageAccounts
- Microsoft.Network/virtualNetworks
- Microsoft.Network/publicIPAddresses
- Microsoft.Network/loadBalancers
- Microsoft.Network/networkInterfaces
- Microsoft.Compute/virtualMachines
- Microsoft.Compute/virtualMachineScaleSets
- Microsoft.Insights.VMDiagnosticsSettings
- Microsoft.Insights/autoscaleSettings

Pour plus d’informations sur les ressources de Resource Manager, consultez [Calcul, réseau et fournisseurs de stockage Azure sous Azure Resource Manager](../virtual-machines/virtual-machines-windows-compare-deployment-models.md).

Le modèle que vous créez dans ce didacticiel est similaire à un modèle qui se trouve dans la galerie de modèles. Pour plus d’informations, consultez [Déployer un jeu de mise à l’échelle de machine virtuelle avec des machines virtuelles Windows et une Jumpbox](https://azure.microsoft.com/documentation/templates/201-vmss-windows-jumpbox/).

[AZURE.INCLUDE [powershell-preview-inline-include](../../includes/powershell-preview-inline-include.md)]

## Étape 1 : créer un groupe de ressources et un compte de stockage

1. **Se connecter à Microsoft Azure**. Ouvrez la fenêtre Microsoft Azure PowerShell et exécutez **Login-AzureRmAccount**.

2. **Créer un groupe de ressources** : toutes les ressources doivent être déployées dans un groupe de ressources. Pour les besoins de ce didacticiel, nommez le groupe de ressources **vmsstestrg1**. Consultez la page [New-AzureRmResourceGroup](https://msdn.microsoft.com/library/mt603739.aspx).

3. **Déploie un compte de stockage dans le nouveau groupe de ressources** : ce didacticiel utilise plusieurs comptes de stockage afin de faciliter le jeu de mise à l’échelle de machine virtuelle. Utilisez [New-AzureRmStorageAccount](https://msdn.microsoft.com/library/mt607148.aspx) pour créer un compte de stockage nommé **vmsstestsa**. Gardez la fenêtre Azure PowerShell ouverte en prévision pour d’autres opérations de ce didacticiel.

## Étape 2 : créer le modèle
Un modèle Azure Resource Manager permet de déployer et gérer des ressources Azure simultanément grâce à une description des ressources JSON et des paramètres de déploiement associés.

1. Dans votre éditeur favori, créez le fichier C:\\VMSSTemplate.json et ajoutez la structure JSON initiale pour prendre en charge le modèle.

        {
          "$schema":"http://schema.management.azure.com/schemas/2014-04-01-preview/VM.json",
          "contentVersion": "1.0.0.0",
          "parameters": {
          }
          "variables": {
          }
          "resources": [
          ]
        }

2. Les paramètres ne sont pas toujours obligatoires, mais ils facilitent la gestion de modèle. Ils fournissent un moyen de spécifier les valeurs du modèle, décrivent le type de valeur, la valeur par défaut le cas échéant, et éventuellement les valeurs de paramètre autorisées. Ajoutez ces paramètres sous l’élément parent des paramètres que vous avez ajouté au modèle.

        "vmName": {
          "type": "string"
        },
        "vmSSName": {
          "type": "string"
        },
        "instanceCount": {
          "type": "string"
        },
        "adminUsername": {
          "type": "string"
        },
        "adminPassword": {
          "type": "securestring"
        },
        "resourcePrefix": {
          "type": "string"
        }
        
    - Nom de la machine virtuelle distincte utilisée pour accéder aux machines du groupe à échelle identique.
    - Nom du compte de stockage dans lequel le modèle est stocké.
    - Nombre d’instances de machines virtuelles à créer initialement dans le groupe à échelle identique.
    - Nom et mot de passe du compte d’administrateur sur les machines virtuelles.
    - Préfixe pour les ressources créées dans le groupe de ressources.
    
3. Des variables peuvent être utilisées dans un modèle pour spécifier des valeurs qui changent fréquemment ou qui doivent être créées à partir d’une combinaison de valeurs de paramètres. Ajoutez ces variables sous l’élément parent des variables que vous avez ajouté au modèle.

        "apiVersion": "2016-03-30"
        "dnsName1": "[concat(parameters('resourcePrefix'),'dn1')] ",
        "dnsName2": "[concat(parameters('resourcePrefix'),'dn2')] ",
        "vmSize": "Standard_A0",
        "imagePublisher": "MicrosoftWindowsServer",
        "imageOffer": "WindowsServer",
        "imageVersion": "2012-R2-Datacenter",
        "addressPrefix": "10.0.0.0/16",
        "subnetName": "Subnet",
        "subnetPrefix": "10.0.0.0/24",
        "publicIP1": "[concat(parameters('resourcePrefix'),'ip1')]",
        "publicIP2": "[concat(parameters('resourcePrefix'),'ip2')]",
        "loadBalancerName": "[concat(parameters('resourcePrefix'),'lb1')]",
        "virtualNetworkName": "[concat(parameters('resourcePrefix'),'vn1')]",
        "nicName1": "[concat(parameters('resourcePrefix'),'nc1')]",
        "nicName2": "[concat(parameters('resourcePrefix'),'nc2')]",
        "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',variables('virtualNetworkName'))]",
        "publicIPAddressID1": "[resourceId('Microsoft.Network/publicIPAddresses',variables('publicIP1'))]",
        "publicIPAddressID2": "[resourceId('Microsoft.Network/publicIPAddresses',variables('publicIP2'))]",
        "lbID": "[resourceId('Microsoft.Network/loadBalancers',variables('loadBalancerName'))]",
        "nicId": "[resourceId('Microsoft.Network/networkInterfaces',variables('nicName2'))]",
        "frontEndIPConfigID": "[concat(variables('lbID'),'/frontendIPConfigurations/loadBalancerFrontEnd')]",
        "storageAccountType": "Standard_LRS",
        "storageAccountSuffix": [ "a", "g", "m", "s", "y" ],
        "diagnosticsStorageAccountName": "[concat(parameters('resourcePrefix'), 'saa')]",
        "accountid": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/', resourceGroup().name,'/providers/','Microsoft.Storage/storageAccounts/', variables('diagnosticsStorageAccountName'))]",
	    "wadlogs": "<WadCfg> <DiagnosticMonitorConfiguration overallQuotaInMB="4096" xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration"> <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter="Error"/> <WindowsEventLog scheduledTransferPeriod="PT1M" > <DataSource name="Application!*[System[(Level = 1 or Level = 2)]]" /> <DataSource name="Security!*[System[(Level = 1 or Level = 2)]]" /> <DataSource name="System!*[System[(Level = 1 or Level = 2)]]" /></WindowsEventLog>",
        "wadperfcounter": "<PerformanceCounters scheduledTransferPeriod="PT1M"><PerformanceCounterConfiguration counterSpecifier="\\Processor(_Total)\\% Processor Time" sampleRate="PT15S" unit="Percent"><annotation displayName="CPU utilization" locale="fr-FR"/></PerformanceCounterConfiguration>",
        "wadcfgxstart": "[concat(variables('wadlogs'),variables('wadperfcounter'),'<Metrics resourceId="')]",
        "wadmetricsresourceid": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',resourceGroup().name ,'/providers/','Microsoft.Compute/virtualMachineScaleSets/',parameters('vmssName'))]",
        "wadcfgxend": "[concat('"><MetricAggregation scheduledTransferPeriod="PT1H"/><MetricAggregation scheduledTransferPeriod="PT1M"/></Metrics></DiagnosticMonitorConfiguration></WadCfg>')]"

    - Noms DNS utilisés par les interfaces réseau.
	- La taille des machines virtuelles utilisées dans le groupe à échelle identique. Pour plus d’informations sur les tailles de machines virtuelles, voir [Tailles des machines virtuelles](../virtual-machines/virtual-machines-size-specs.md).
	- Les informations d’image de plateforme définissant le système d’exploitation qui va s’exécuter sur les machines virtuelles du groupe à échelle identique. Pour plus d’informations sur la sélection d’image, consultez [Rechercher et sélectionner des images de machines virtuelles Azure avec Windows PowerShell et l’interface de ligne de commande Azure](../virtual-machines/resource-groups-vm-searching.md).
	- Les noms d’adresse IP et les préfixes destinés au réseau et aux sous-réseaux.
	- Les noms et les identificateurs du réseau virtuel, de l’équilibreur de charge et des interfaces réseau.
	- Les noms de compte de stockage pour les comptes associés aux machines du groupe à échelle identique.
	- Paramètres de l’extension de diagnostic qui est installé sur les machines virtuelles. Pour plus d’informations sur l’extension de diagnostic, consultez [Créer une machine virtuelle Windows avec surveillance et diagnostics à l’aide d’un modèle Azure Resource Manager](../virtual-machines/virtual-machines-extensions-diagnostics-windows-template.md).
    
4. Ajoutez la ressource de compte de stockage sous l’élément parent de ressources que vous avez ajouté au modèle. Ce modèle utilise une boucle pour créer les 5 comptes de stockage recommandés dans lesquels les disques de système d’exploitation et les données de diagnostic sont stockés. Cet ensemble de comptes peut prendre en charge jusqu’à 100 machines virtuelles dans un groupe à échelle identique, qui est la valeur maximale actuelle. Chaque compte de stockage nommé par un indicateur de lettre a été défini dans les variables combinées au suffixe que vous fournissez dans les paramètres du modèle.

        {
          "type": "Microsoft.Storage/storageAccounts",
          "name": "[concat(variables('resourcePrefix'), parameters('storageAccountSuffix')[copyIndex()])]",
          "apiVersion": "2015-06-15",
          "copy": {
          "name": "storageLoop",
          "count": 5
        },
        "location": "[resourceGroup().location]",
        "properties": {
          "accountType": "[variables('storageAccountType')]"
        }
      }

5. Ajoutez la ressource de réseau virtuelle. Pour plus d’informations, consultez [Fournisseurs de ressources réseau](../virtual-network/resource-groups-networking.md).

        {
          "apiVersion": "[variables('apiVersion')]",
          "type": "Microsoft.Network/virtualNetworks",
          "name": "[variables('virtualNetworkName')]",
          "location": "[resourceGroup().location]",
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

6. Ajoutez des ressources d’adresse IP publiques qui sont utilisées par l’équilibrage de charge et l’interface réseau.

        {
          "apiVersion": "[variables('apiVersion')]",
          "type": "Microsoft.Network/publicIPAddresses",
          "name": "[variables('publicIP1')]",
          "location": "[resourceGroup().location]",
          "properties": {
            "publicIPAllocationMethod": "Dynamic",
            "dnsSettings": {
              "domainNameLabel": "[variables('dnsName1')]"
            }
          }
        },
        {
          "apiVersion": "[variables('apiVersion')]",
          "type": "Microsoft.Network/publicIPAddresses",
          "name": "[variables('publicIP2')]",
          "location": "[resourceGroup().location]",
          "properties": {
            "publicIPAllocationMethod": "Dynamic",
            "dnsSettings": {
              "domainNameLabel": "[variables('dnsName2')]"
            }
          }
        },

7. Ajoutez la ressource d’équilibrage de charge utilisée par le groupe à échelle identique. Pour plus d’informations, consultez [Prise en charge d’un équilibreur de charge par Azure Resource Manager](../load-balancer/load-balancer-arm.md)

        {
          "apiVersion": "[variables('apiVersion')]",
          "name": "[variables('loadBalancerName')]",
          "type": "Microsoft.Network/loadBalancers",
          "location": "[resourceGroup().location]",
          "dependsOn": [
            "[concat('Microsoft.Network/publicIPAddresses/', variables('publicIP1'))]"
          ],
          "properties": {
            "frontendIPConfigurations": [
              {
                "name": "loadBalancerFrontEnd",
                "properties": {
                  "publicIPAddress": {
                    "id": "[variables('publicIPAddressID1')]"
                  }
                }
              }
            ],
            "backendAddressPools": [
              {
                "name": "bepool1"
              }
            ],
            "inboundNatPools": [
              {
                "name": "natpool1",
                "properties": {
                  "frontendIPConfiguration": {
                    "id": "[variables('frontEndIPConfigID')]"
                  },
                  "protocol": "tcp",
                  "frontendPortRangeStart": 50000,
                  "frontendPortRangeEnd": 50500,
                  "backendPort": 3389
                }
              }
            ]
          }
        },

8. Ajoutez la ressource d’interface réseau qui est utilisée par la machine virtuelle distincte. Les machines présentes dans le groupe de machines virtuelles à échelle identique n’étant pas directement accessibles par le biais d’une adresse IP publique, une machine virtuelle distincte est créée dans le même réseau virtuel que le groupe à échelle identique et est utilisée pour accéder à distance aux machines du groupe.

        {
          "apiVersion": "[variables('apiVersion')]",
          "type": "Microsoft.Network/networkInterfaces",
          "name": "[variables('nicName1')]",
          "location": "[resourceGroup().location]",
          "dependsOn": [
            "[concat('Microsoft.Network/publicIPAddresses/', variables('publicIP2'))]",
            "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]"
          ],
          "properties": {
            "ipConfigurations": [
              {
                "name": "ipconfig1",
                "properties": {
                  "privateIPAllocationMethod": "Dynamic",
                  "publicIPAddress": {
                    "id": "[resourceId('Microsoft.Network/publicIPAddresses', variables('publicIP2'))]"
                  },
                  "subnet": {
                    "id": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',resourceGroup().name,'/providers/Microsoft.Network/virtualNetworks/',variables('virtualNetworkName'),'/subnets/',variables('subnetName'))]"
                  }
                }
              }
            ]
          }
        },

9. Ajoutez la machine virtuelle distincte dans le même réseau que le groupe à échelle identique.

        {
          "apiVersion": "[variables('apiVersion')]",
          "type": "Microsoft.Compute/virtualMachines",
          "name": "[parameters('vmName')]",
          "location": "[resourceGroup().location]",
          "dependsOn": [
            "[concat('Microsoft.Network/networkInterfaces/', variables('nicName1'))]"
          ],
          "properties": {
            "hardwareProfile": {
              "vmSize": "[variables('vmSize')]"
            },
            "osProfile": {
              "computername": "[parameters('vmName')]",
              "adminUsername": "[parameters('adminUsername')]",
              "adminPassword": "[parameters('adminPassword')]"
            },
            "storageProfile": {
              "imageReference": {
                "publisher": "[variables('imagePublisher')]",
                "offer": "[variables('imageOffer')]",
                "sku": "[variables('imageVersion')]",
                "version": "latest"
              },
              "osDisk": {
                "name": "osdisk1",
                "vhd": {
                  "uri":  "[concat('https://',parameters('resourcePrefix'),'saa.blob.core.windows.net/vhds/',parameters('resourcePrefix'),'osdisk1.vhd')]"
                },
                "caching": "ReadWrite",
                "createOption": "FromImage"        
              }
            },
            "networkProfile": {
              "networkInterfaces": [
                {
                  "id": "[resourceId('Microsoft.Network/networkInterfaces',variables('nicName1'))]"
                }
              ]
            }
          }
        },

10.	Ajoutez la ressource de groupe de machines virtuelles à échelle identique et spécifiez l’extension de Diagnostics installée sur toutes les machines virtuelles du groupe à échelle identique. La plupart des paramètres de cette ressource sont similaires à la ressource de machine virtuelle. Les principales différences sont l'ajout de l'élément de capacité qui spécifie le nombre de machines virtuelles qui devraient être initialisées dans le jeu de mise à l'échelle et le paramètre upgradePolicy qui spécifie comment les mises à jour sont appliquées aux machines virtuelles dans le jeu de mise à l'échelle. Le groupe à échelle identique n’est pas créé tant que les comptes de stockage ne sont pas tous créés, comme indiqué avec l’élément dependsOn.

            {
              "type": "Microsoft.Compute/virtualMachineScaleSets",
              "apiVersion": "[variables('apiVersion')]",
              "name": "[parameters('vmSSName')]",
              "location": "[resourceGroup().location]",
              "dependsOn": [
                "storageLoop",
                "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]",
                "[concat('Microsoft.Network/loadBalancers/', variables('loadBalancerName'))]"
              ],
              "sku": {
                "name": "[variables('vmSize')]",
                "tier": "Standard",
                "capacity": "[parameters('instanceCount')]"
              },
              "properties": {
                "upgradePolicy": {
                  "mode": "Manual"
                },
                "virtualMachineProfile": {
                  "storageProfile": {
                    "osDisk": {
                      "vhdContainers": [
                        "[concat('https://', parameters('resourcePrefix'), 'saa.blob.core.windows.net/vmss')]",
                        "[concat('https://', parameters('resourcePrefix'), 'sag.blob.core.windows.net/vmss')]",
                        "[concat('https://', parameters('resourcePrefix'), 'sam.blob.core.windows.net/vmss')]",
                        "[concat('https://', parameters('resourcePrefix'), 'sas.blob.core.windows.net/vmss')]",
                        "[concat('https://', parameters('resourcePrefix'), 'say.blob.core.windows.net/vmss')]"
                      ],
                      "name": "vmssosdisk",
                      "caching": "ReadOnly",
                      "createOption": "FromImage"
                    },
                    "imageReference": {
                      "publisher": "[variables('imagePublisher')]",
                      "offer": "[variables('imageOffer')]",
                      "sku": "[variables('imageVersion')]",
                      "version": "latest"
                    }
                  },
                  "osProfile": {
                    "computerNamePrefix": "[parameters('vmSSName')]",
                    "adminUsername": "[parameters('adminUsername')]",
                    "adminPassword": "[parameters('adminPassword')]"
                  },
                  "networkProfile": {
                    "networkInterfaceConfigurations": [
                      {
                        "name": "[variables('nicName2')]",
                        "properties": {
                          "primary": "true",
                          "ipConfigurations": [
                            {
                              "name": "ip1",
                              "properties": {
                                "subnet": {
                                  "id": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',resourceGroup().name,'/providers/Microsoft.Network/virtualNetworks/',variables('virtualNetworkName'),'/subnets/',variables('subnetName'))]"
                                },
                                "loadBalancerBackendAddressPools": [
                                  {
                                    "id": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',resourceGroup().name,'/providers/Microsoft.Network/loadBalancers/',variables('loadBalancerName'),'/backendAddressPools/bepool1')]"
                                  }
                                ],
                                "loadBalancerInboundNatPools": [
                                  {
                                    "id": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',resourceGroup().name,'/providers/Microsoft.Network/loadBalancers/',variables('loadBalancerName'),'/inboundNatPools/natpool1')]"
                                  }
                                ]
                              }
                            }
                          ]
                        }
                      }
                    ]
                  },
                  "extensionProfile": {
                    "extensions": [
                      {
                        "name": "Microsoft.Insights.VMDiagnosticsSettings",
                        "properties": {
                          "publisher": "Microsoft.Azure.Diagnostics",
                          "type": "IaaSDiagnostics",
                          "typeHandlerVersion": "1.5",
                          "autoUpgradeMinorVersion": true,
                          "settings": {
                            "xmlCfg": "[base64(concat(variables('wadcfgxstart'),variables('wadmetricsresourceid'),variables('wadcfgxend')))]",
                            "storageAccount": "[variables('diagnosticsStorageAccountName')]"
                          },
                          "protectedSettings": {
                            "storageAccountName": "[variables('diagnosticsStorageAccountName')]",
                            "storageAccountKey": "[listkeys(variables('accountid'), variables('apiVersion')).key1]",
                            "storageAccountEndPoint": "https://core.windows.net"
                          }
                        }
                      }
                    ]
                  }
                }
              }
            },

11.	Ajoutez la ressource autoscaleSettings qui définit comment l’ensemble de mise à l’échelle s’ajuste en fonction de l’utilisation du processeur sur les machines du jeu.

            {
              "type": "Microsoft.Insights/autoscaleSettings",
              "apiVersion": "2015-04-01",
              "name": "[concat(parameters('resourcePrefix'),'as1')]",
              "location": "[resourceGroup().location]",
              "dependsOn": [
                "[concat('Microsoft.Compute/virtualMachineScaleSets/',parameters('vmSSName'))]"
              ],
              "properties": {
                "enabled": true,
                "name": "[concat(parameters('resourcePrefix'),'as1')]",
                "profiles": [
                  {
                    "name": "Profile1",
                    "capacity": {
                      "minimum": "1",
                      "maximum": "10",
                      "default": "1"
                    },
                    "rules": [
                      {
                        "metricTrigger": {
                          "metricName": "\\Processor(_Total)\\% Processor Time",
                          "metricNamespace": "",
                          "metricResourceUri": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',resourceGroup().name,'/providers/Microsoft.Compute/virtualMachineScaleSets/',parameters('vmSSName'))]",
                          "timeGrain": "PT1M",
                          "statistic": "Average",
                          "timeWindow": "PT5M",
                          "timeAggregation": "Average",
                          "operator": "GreaterThan",
                          "threshold": 50.0
                        },
                        "scaleAction": {
                          "direction": "Increase",
                          "type": "ChangeCount",
                          "value": "1",
                          "cooldown": "PT5M"
                        }
                      }
                    ]
                  }
                ],
                "targetResourceUri": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/', resourceGroup().name,'/providers/Microsoft.Compute/virtualMachineScaleSets/',parameters('vmSSName'))]"
              }
            }

    Pour ce didacticiel, il s’agit de valeurs importantes :

    - **metricName** : il s’agit du même que le compteur de performance que nous avons défini dans la variable wadperfcounter. Grâce à cette variable, l’extension de diagnostic relève le compteur **Processeur(\_Total)\\% Temps processeur **.
- **metricResourceUri** : il s’agit de l’identificateur de ressource du jeu de mise à l’échelle de machine virtuelle.
    - **timeGrain** : il s’agit de la granularité des mesures collectées. Dans ce modèle, il est défini à 1 minute.
    - **statistiques** : ce paramètre détermine la façon dont les mesures sont combinées pour prendre en charge l’action de mise à l’échelle automatique. Les valeurs possibles sont : Moyenne, Min, Max. Dans ce modèle, nous recherchons le taux d’utilisation totale du processeur entre les machines virtuelles du groupe à échelle identique.
    - **timeWindow** : il s’agit de la plage de temps pendant laquelle les données d’instance sont collectées. Elle doit être comprise entre 5 minutes et 12 heures.
    - **timeAggregation** : ce paramètre détermine la façon dont les données recueillies doivent être combinées sur la durée. La valeur par défaut est Average. Les valeurs possibles sont : Moyenne, Minimum, Maximum, Dernier, Total, Nombre.
    - **opérateur** : opérateur utilisé pour comparer les données de mesure et le seuil. Les valeurs possibles sont : est égal à -Equals), différent de (NotEquals), supérieur à (GreaterThan), égal ou supérieur à (GreaterThanOrEqual), Inférieur à (LessThan), Inférieur ou égal à (LessThanOrEqual).
    - **seuil** : il s’agit de la valeur qui déclenche l’action de mise à l’échelle. Dans ce modèle, les machines sont ajoutées au jeu de mise à l’échelle défini lorsque l’utilisation moyenne du processeur dans le jeu de machines des est supérieur à 50 %.
    - **direction** : ce paramètre détermine l’opération qui est effectuée lorsque la valeur de seuil est atteinte. Les valeurs possibles sont Augmenter ou Diminuer. Dans ce modèle, le nombre de machines virtuelles dans le jeu de mise à l’échelle est augmenté si le seuil est supérieur à 50 % dans la fenêtre de temps définie.
    - **type** : il s’agit du type d’action qui doit se produire. Il doit être défini sur ChangeCount.
    - **valeur** : il s’agit du nombre de machines virtuelles qui sont ajoutées ou supprimées du jeu de mise à l’échelle. Cette valeur doit être définie sur 1 ou supérieur. La valeur par défaut est 1. Dans ce modèle, le nombre d’ordinateurs présent dans le jeu de mise à l’échelle augmente de 1 lorsque le seuil est atteint.
    - **ralentissement** : durée d’attente depuis la dernière opération de mise à l’échelle avant que l’action se produise. Elle peut être comprise entre 1 minute et 1 semaine.

12.	Enregistrez le fichier de modèle.

## Étape 3 : charger le modèle dans le stockage

Le modèle peut être téléchargé depuis la fenêtre Microsoft Azure PowerShell à condition que vous connaissiez le nom du compte et la clé primaire du compte de stockage que vous avez créé à l’étape 1.

1.	Dans la fenêtre Microsoft Azure PowerShell, définissez une variable qui spécifie le nom du compte de stockage que vous avez déployé à l’étape 1.

            $StorageAccountName = "vmstestsa"

2.	Définissez une variable qui spécifie la clé primaire du compte de stockage.

            $StorageAccountKey = "<primary-account-key>"

	Vous pouvez obtenir cette clé en cliquant sur l’icône de clé lors de l’affichage de la ressource de compte de stockage dans le portail Azure.

3.	Créez l’objet de contexte de compte de stockage utilisé pour valider les opérations avec le compte de stockage.

            $ctx = New-AzureStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

4.	Créez un nouveau conteneur de modèles dans lequel le modèle que vous avez créé peut être stocké.

            $ContainerName = "templates"
            New-AzureStorageContainer -Name $ContainerName -Context $ctx  -Permission Blob

5.	Téléchargez le fichier de modèle correspondant au nouveau conteneur.

            $BlobName = "VMSSTemplate.json"
            $fileName = "C:" + $BlobName
            Set-AzureStorageBlobContent -File $fileName -Container $ContainerName -Blob  $BlobName -Context $ctx

## Étape 4 : déployer le modèle

Maintenant que vous avez créé le modèle, vous pouvez commencer à déployer les ressources. Utilisez cette commande pour démarrer le processus :

        New-AzureRmResourceGroupDeployment -Name "vmsstestdp1" -ResourceGroupName "vmsstestrg1" -TemplateUri "https://vmsstestsa.blob.core.windows.net/templates/VMSSTemplate.json"

Lorsque vous appuyez sur Entrée, vous êtes invité à fournir des valeurs pour les variables que vous avez affectées. Remplacez les valeurs suivantes :

	vmName: vmsstestvm1
	vmSSName: vmsstest1
	instanceCount: 5
	adminUserName: vmadmin1
	adminPassword: VMpass1
	resourcePrefix: vmsstest

Il doit prendre environ 15 minutes pour que toutes les ressources soient déployées avec succès.

>[AZURE.NOTE]Vous pouvez également utiliser la capacité du portail à déployer les ressources. Pour ce faire, utilisez ce lien : https://portal.azure.com/#create/Microsoft.Template/uri/<link to VM Scale Set JSON template>

## Étape 5 : analyser les ressources

Vous pouvez obtenir des informations sur les jeux de mise à l’échelle de machine virtuelle à l’aide des méthodes suivantes :

 - Le portail Azure : vous pouvez en obtenir une quantité limitée d’informations sur l’utilisation du portail.
 - [Azure Resource Explorer](https://resources.azure.com/) : il s’agit du meilleur outil pour déterminer l’état actuel de votre jeu de mise à l’échelle. Suivez ce chemin d’accès. Vous devriez voir la vue de l’instance du jeu de mise à l’échelle que vous avez créée :

		subscriptions > {your subscription} > resourceGroups > vmsstestrg1 > providers > Microsoft.Compute > virtualMachineScaleSets > vmsstest1 > virtualMachines

 - Azure PowerShell : utilisez cette commande pour obtenir des informations :

		Get-AzureRmResource -name vmsstest1 -ResourceGroupName vmsstestrg1 -ResourceType Microsoft.Compute/virtualMachineScaleSets -ApiVersion 2015-06-15

 - Connectez-vous à la machine virtuelle jumpbox comme vous le feriez pour n’importe quel autre ordinateur et vous pouvez ensuite accéder à distance aux machines virtuelles de l’ensemble de mise à l’échelle pour surveiller les processus individuels.

>[AZURE.NOTE]Vous trouverez une API REST complète permettant d’obtenir des informations sur les jeux de mise à l’échelle dans [Ensembles de mise à l’échelle de machine virtuelle](https://msdn.microsoft.com/library/mt589023.aspx)

## Étape 6 : supprimer les ressources

Étant donné que les ressources utilisées dans Microsoft Azure vous sont facturées, il est toujours conseillé de supprimer les ressources qui ne sont plus nécessaires. Vous n’avez pas besoin de supprimer séparément les ressources d’un groupe de ressources. Vous pouvez supprimer le groupe de ressources et toutes ses ressources seront automatiquement supprimées.

	Remove-AzureRmResourceGroup -Name vmsstestrg1

Si vous souhaitez conserver votre groupe de ressources, vous pouvez supprimer uniquement le jeu de mise à l’échelle.

	Remove-AzureRmResource -Name vmsstest1 -ResourceGroupName vmsstestrg1 -ApiVersion 2015-06-15 -ResourceType Microsoft.Compute/virtualMachineScaleSets

<!---HONumber=AcomDC_0427_2016-->