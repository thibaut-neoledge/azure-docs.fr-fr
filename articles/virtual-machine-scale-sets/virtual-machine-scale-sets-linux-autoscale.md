<properties
    pageTitle="Mettre à l’échelle automatiquement des jeux de mise à l’échelle de machines virtuelles Linux | Microsoft Azure"
    description="Mettre à l’échelle automatiquement un jeu de mise à l’échelle de machine virtuelle Linux avec l’interface de ligne de commande Azure"
    services="virtual-machine-scale-sets"
    documentationCenter=""
    authors="davidmu1"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machine-scale-sets"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="davidmu"/>


# <a name="automatically-scale-linux-machines-in-a-virtual-machine-scale-set"></a>Mise à l’échelle automatique de machines Linux dans un jeu de mise à l’échelle de machine virtuelle

Les jeux de mise à l’échelle de machines virtuelles facilitent le déploiement et la gestion de machines virtuelles identiques en tant qu’ensemble. Les groupes à échelle identique fournissent une couche de calcul hautement évolutive et personnalisable pour les applications « hyperscale », et prennent en charge les images de plateforme Windows, les images de plateforme Linux, des images personnalisées et les extensions. Pour en savoir plus, consultez la rubrique [Vue d’ensemble des jeux de mise à l’échelle de machines virtuelles](virtual-machine-scale-sets-overview.md).

Ce didacticiel vous montre comment créer un jeu de mise à l’échelle de machine virtuelle Linux à l’aide de la dernière version de Ubuntu Linux. Il vous montre également comment mettre automatiquement à l’échelle les machines dans le jeu. Pour créer le jeu de mise à l’échelle et configurer la mise à l’échelle, créez un modèle Azure Resource Manager et déployez-le à l’aide de l’interface de ligne de commande Azure. Pour en savoir plus sur les modèles, consultez [Création de modèles Azure Resource Manager](../resource-group-authoring-templates.md). Pour plus d'informations sur la mise à l'échelle automatique de jeux de mise à l'échelle, consultez la rubrique [Mise à l'échelle automatique et jeux de mise à l'échelle de machines virtuelles](virtual-machine-scale-sets-autoscale-overview.md).

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

Pour plus d’informations sur les ressources de Resource Manager, consultez [Calcul, réseau et fournisseurs de stockage Azure sous Azure Resource Manager](../virtual-machines/virtual-machines-linux-compare-deployment-models.md).

Avant de commencer les étapes de ce didacticiel, [installez l’interface de ligne de commande Azure](../xplat-cli-install.md).

## <a name="step-1:-create-a-resource-group-and-a-storage-account"></a>Étape 1 : créer un groupe de ressources et un compte de stockage

1. **Se connecter à Microsoft Azure** : dans votre interface de ligne de commande (Bash, Terminal, invite de commandes), passez en mode Resource Manager, puis [connectez-vous avec votre ID professionnel ou scolaire](../xplat-cli-connect.md#use-the-log-in-method). Suivez les invites pour une expérience de connexion interactive à votre compte Azure.

        azure config mode arm

        azure login

    > [AZURE.NOTE] Si vous disposez d’un ID professionnel ou scolaire et n’avez pas d’authentification à deux facteurs activée, vous pouvez utiliser `azure login -u` avec l’ID pour vous connecter sans session interactive. Si vous ne disposez pas d’un ID professionnel ou scolaire, vous pouvez [créer un ID professionnel ou scolaire à partir de votre compte Microsoft personnel](../virtual-machines/virtual-machines-linux-create-aad-work-id.md).

2. **Créer un groupe de ressources** : toutes les ressources doivent être déployées dans un groupe de ressources. Pour les besoins de ce didacticiel, nommez le groupe de ressources **vmsstest1**.

        azure group create vmsstestrg1 centralus

3. **Déployer un compte de stockage dans le nouveau groupe de ressources** : ce compte de stockage est l’emplacement dans lequel le modèle est stocké. Créez un compte de stockage nommé **vmsstestsa**.

        azure storage account create -g vmsstestrg1 -l centralus --kind Storage --sku-name LRS vmsstestsa

## <a name="step-2:-create-the-template"></a>Étape 2 : créer le modèle
Un modèle Azure Resource Manager permet de déployer et gérer des ressources Azure simultanément grâce à une description des ressources JSON et des paramètres de déploiement associés.

1. Dans votre éditeur favori, créez le fichier VMSSTemplate.json, puis ajoutez la structure JSON initiale pour prendre en charge le modèle.

        {
          "$schema":"http://schema.management.azure.com/schemas/2014-04-01-preview/VM.json",
          "contentVersion": "1.0.0.0",
          "parameters": {
          },
          "variables": {
          },
          "resources": [
          ]
        }

2. Les paramètres ne sont pas toujours nécessaires, mais ils offrent un moyen d’entrer des valeurs lors du déploiement du modèle. Ajoutez ces paramètres sous l’élément parent des paramètres que vous avez ajouté au modèle.

        "vmName": { "type": "string" },
        "vmSSName": { "type": "string" },
        "instanceCount": { "type": "string" },
        "adminUsername": { "type": "string" },
        "adminPassword": { "type": "securestring" },
        "resourcePrefix": { "type": "string" }
            
    - Nom de la machine virtuelle distincte utilisée pour accéder aux machines du groupe à échelle identique.
    - Nom du compte de stockage dans lequel le modèle est stocké.
    - Nombre d’instances de machines virtuelles à créer initialement dans le groupe à échelle identique.
    - Nom et mot de passe du compte d’administrateur sur les machines virtuelles.
    - Préfixe de nom pour les ressources qui sont créées pour prendre en charge le jeu de mise à l’échelle.

3. Des variables peuvent être utilisées dans un modèle pour spécifier des valeurs qui changent fréquemment ou qui doivent être créées à partir d’une combinaison de valeurs de paramètres. Ajoutez ces variables sous l’élément parent des variables que vous avez ajouté au modèle.

        "dnsName1": "[concat(parameters('resourcePrefix'),'dn1')]",
        "dnsName2": "[concat(parameters('resourcePrefix'),'dn2')]",
        "publicIP1": "[concat(parameters('resourcePrefix'),'ip1')]",
        "publicIP2": "[concat(parameters('resourcePrefix'),'ip2')]",
        "loadBalancerName": "[concat(parameters('resourcePrefix'),'lb1')]",
        "virtualNetworkName": "[concat(parameters('resourcePrefix'),'vn1')]",
        "nicName": "[concat(parameters('resourcePrefix'),'nc1')]",
        "lbID": "[resourceId('Microsoft.Network/loadBalancers',variables('loadBalancerName'))]",
        "frontEndIPConfigID": "[concat(variables('lbID'),'/frontendIPConfigurations/loadBalancerFrontEnd')]",
        "storageAccountSuffix": [ "a", "g", "m", "s", "y" ],
        "diagnosticsStorageAccountName": "[concat(parameters('resourcePrefix'), 'a')]",
        "accountid": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/', resourceGroup().name,'/providers/','Microsoft.Storage/storageAccounts/', variables('diagnosticsStorageAccountName'))]",
        "wadlogs": "<WadCfg><DiagnosticMonitorConfiguration>",
        "wadperfcounter": "<PerformanceCounters scheduledTransferPeriod=\"PT1M\"><PerformanceCounterConfiguration counterSpecifier=\"\\Processor\\PercentProcessorTime\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"CPU percentage guest OS\" locale=\"en-us\"/></PerformanceCounterConfiguration></PerformanceCounters>",
        "wadcfgxstart": "[concat(variables('wadlogs'),variables('wadperfcounter'),'<Metrics resourceId=\"')]",
        "wadmetricsresourceid": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',resourceGroup().name ,'/providers/','Microsoft.Compute/virtualMachineScaleSets/',parameters('vmssName'))]",
        "wadcfgxend": "[concat('\"><MetricAggregation scheduledTransferPeriod=\"PT1H\"/><MetricAggregation scheduledTransferPeriod=\"PT1M\"/></Metrics></DiagnosticMonitorConfiguration></WadCfg>')]"
        
    - Noms DNS utilisés par les interfaces réseau.
    - Les noms d’adresse IP et les préfixes destinés au réseau et aux sous-réseaux.
    - Les noms et les identificateurs du réseau virtuel, de l’équilibreur de charge et des interfaces réseau.
    - Les noms de compte de stockage pour les comptes associés aux machines du groupe à échelle identique.
    - Paramètres de l’extension de diagnostic qui est installé sur les machines virtuelles. Pour plus d’informations sur l’extension de diagnostic, consultez [Créer une machine virtuelle Windows avec des fonctionnalités de surveillance et de diagnostics à l’aide d’un modèle Azure Resource Manager](../virtual-machines/virtual-machines-windows-extensions-diagnostics-template.md).

4. Ajoutez la ressource de compte de stockage sous l’élément parent de ressources que vous avez ajouté au modèle. Ce modèle utilise une boucle pour créer les cinq comptes de stockage recommandés dans lesquels les disques de système d’exploitation et les données de diagnostic sont stockés. Cet ensemble de comptes peut prendre en charge jusqu’à 100 machines virtuelles dans un groupe à échelle identique, qui est la valeur maximale actuelle. Chaque compte de stockage nommé par un indicateur de lettre a été défini dans les variables combinées au suffixe que vous fournissez dans les paramètres du modèle.

        {
          "type": "Microsoft.Storage/storageAccounts",
          "name": "[concat(parameters('resourcePrefix'), variables('storageAccountSuffix')[copyIndex()])]",
          "apiVersion": "2015-06-15",
          "copy": {
            "name": "storageLoop",
            "count": 5
          },
          "location": "[resourceGroup().location]",
          "properties": { "accountType": "Standard_LRS" }
        },

5. Ajoutez la ressource de réseau virtuelle. Pour plus d’informations, consultez [Fournisseurs de ressources réseau](../virtual-network/resource-groups-networking.md).

        {
          "apiVersion": "2015-06-15",
          "type": "Microsoft.Network/virtualNetworks",
          "name": "[variables('virtualNetworkName')]",
          "location": "[resourceGroup().location]",
          "properties": {
            "addressSpace": { "addressPrefixes": [ "10.0.0.0/16" ] },
            "subnets": [
              {
                "name": "subnet1",
                "properties": { "addressPrefix": "10.0.0.0/24" }
              }
            ]
          }
        },

6. Ajoutez des ressources d’adresse IP publiques qui sont utilisées par l’équilibrage de charge et l’interface réseau.

        {
          "apiVersion": "2016-03-30",
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
          "apiVersion": "2016-03-30",
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
          "apiVersion": "2015-06-15",
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
                    "id": "[resourceId('Microsoft.Network/publicIPAddresses/', variables('publicIP1'))]"
                  }
                }
              }
            ],
            "backendAddressPools": [ { "name": "bepool1" } ],
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
                  "backendPort": 22
                }
              }
            ]
          }
        },

8. Ajoutez la ressource d’interface réseau qui est utilisée par la machine virtuelle distincte. Étant donné que les machines virtuelles d’un jeu de mise à l’échelle ne sont pas accessibles via une adresse IP publique, une machine virtuelle distincte est créée dans le même réseau virtuel pour accéder à distance aux machines.

        {
          "apiVersion": "2016-03-30",
          "type": "Microsoft.Network/networkInterfaces",
          "name": "[variables('nicName')]",
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
                    "id": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',resourceGroup().name,'/providers/Microsoft.Network/virtualNetworks/',variables('virtualNetworkName'),'/subnets/subnet1')]"
                  }
                }
              }
            ]
          }
        },

9. Ajoutez la machine virtuelle distincte dans le même réseau que le groupe à échelle identique.

        {
          "apiVersion": "2016-03-30",
          "type": "Microsoft.Compute/virtualMachines",
          "name": "[parameters('vmName')]",
          "location": "[resourceGroup().location]",
          "dependsOn": [
            "storageLoop",
            "[concat('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
          ],
          "properties": {
            "hardwareProfile": { "vmSize": "Standard_A1" },
            "osProfile": {
              "computername": "[parameters('vmName')]",
              "adminUsername": "[parameters('adminUsername')]",
              "adminPassword": "[parameters('adminPassword')]"
            },
            "storageProfile": {
              "imageReference": {
                "publisher": "Canonical",
                "offer": "UbuntuServer",
                "sku": "14.04.4-LTS",
                "version": "latest"
              },
              "osDisk": {
                "name": "[concat(parameters('resourcePrefix'), 'os1')]",
                "vhd": {
                  "uri":  "[concat('https://',parameters('resourcePrefix'),'a.blob.core.windows.net/vhds/',parameters('resourcePrefix'),'os1.vhd')]"
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
        },

10. Ajoutez la ressource de jeu de mise à l’échelle de machines virtuelles et spécifiez l’extension Diagnostics installée sur toutes les machines virtuelles du jeu de mise à l’échelle. La plupart des paramètres de cette ressource sont similaires à la ressource de machine virtuelle. Les principales différences sont l’élément capacity qui spécifie le nombre de machines virtuelles du jeu de mise à l’échelle et le paramètre upgradePolicy qui spécifie la manière dont les mises à jour sont appliquées aux machines virtuelles. Le jeu de mise à l’échelle n’est pas créé tant que tous les comptes de stockage ne sont pas créés, comme spécifié par l’élément dependsOn.

            {
              "type": "Microsoft.Compute/virtualMachineScaleSets",
              "apiVersion": "2016-03-30",
              "name": "[parameters('vmSSName')]",
              "location": "[resourceGroup().location]",
              "dependsOn": [
                "storageLoop",
                "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]",
                "[concat('Microsoft.Network/loadBalancers/', variables('loadBalancerName'))]"
              ],
              "sku": {
                "name": "Standard_A1",
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
                        "[concat('https://', parameters('resourcePrefix'), variables('storageAccountSuffix')[0],'.blob.core.windows.net/vmss')]",
                        "[concat('https://', parameters('resourcePrefix'), variables('storageAccountSuffix')[1],'.blob.core.windows.net/vmss')]",
                        "[concat('https://', parameters('resourcePrefix'), variables('storageAccountSuffix')[2],'.blob.core.windows.net/vmss')]",
                        "[concat('https://', parameters('resourcePrefix'), variables('storageAccountSuffix')[3],'.blob.core.windows.net/vmss')]",
                        "[concat('https://', parameters('resourcePrefix'), variables('storageAccountSuffix')[4],'.blob.core.windows.net/vmss')]"
                      ],
                      "name": "vmssosdisk",
                      "caching": "ReadOnly",
                      "createOption": "FromImage"
                    },
                    "imageReference": {
                      "publisher": "Canonical",
                      "offer": "UbuntuServer",
                      "sku": "14.04.4-LTS",
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
                        "name": "networkconfig1",
                        "properties": {
                          "primary": "true",
                          "ipConfigurations": [
                            {
                              "name": "ip1",
                              "properties": {
                                "subnet": {
                                  "id": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',resourceGroup().name,'/providers/Microsoft.Network/virtualNetworks/',variables('virtualNetworkName'),'/subnets/subnet1')]"
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
                        "name":"LinuxDiagnostic",
                        "properties": {
                          "publisher":"Microsoft.OSTCExtensions",
                          "type":"LinuxDiagnostic",
                          "typeHandlerVersion":"2.1",
                          "autoUpgradeMinorVersion":false,
                          "settings": {
                            "xmlCfg":"[base64(concat(variables('wadcfgxstart'),variables('wadmetricsresourceid'),variables('wadcfgxend')))]",
                            "storageAccount":"[variables('diagnosticsStorageAccountName')]"
                          },
                          "protectedSettings": {
                            "storageAccountName":"[variables('diagnosticsStorageAccountName')]",
                            "storageAccountKey":"[listkeys(variables('accountid'), '2015-06-15').key1]",
                            "storageAccountEndPoint":"https://core.windows.net"
                          }
                        }
                      }
                    ]
                  }
                }
              }
            },

11. Ajoutez la ressource autoscaleSettings qui définit le mode d’ajustement du groupe à échelle identique en fonction de l’utilisation du processeur sur les machines virtuelles du groupe. 
        
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
                          "metricName": "\\Processor\\PercentProcessorTime",
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
    
    Pour ce didacticiel, les valeurs suivantes sont importantes :
    
    - **metricName** : cette valeur est la même que celle du compteur de performances que nous avons défini dans la variable wadperfcounter. Grâce à cette variable, l’extension Diagnostics collecte le compteur **Processor\PercentProcessorTime**.
    - **metricResourceUri** : cette valeur est l’identificateur de ressource du jeu de mise à l’échelle de machines virtuelles.
    - **timeGrain** : cette valeur est la granularité des mesures collectées. Dans ce modèle, elle est définie sur une minute.
    - **statistic** : cette valeur détermine la façon dont les mesures sont combinées pour prendre en charge l’action de mise à l’échelle automatique. Les valeurs possibles sont : Moyenne, Min, Max. Dans ce modèle, l’utilisation moyenne totale du processeur des machines virtuelles est collectée.
    - **timeWindow** : cette valeur est la plage de temps pendant laquelle les données d’instance sont collectées. Elle doit être comprise entre 5 minutes et 12 heures.
    - **timeAggregation** : cette valeur détermine la façon dont les données collectées doivent être combinées au fil du temps. La valeur par défaut est Average. Les valeurs possibles sont : Moyenne, Minimum, Maximum, Dernier, Total, Nombre.
    - **operator** : cette valeur est l’opérateur utilisé pour comparer les données de mesure et le seuil. Les valeurs possibles sont : est égal à -Equals), différent de (NotEquals), supérieur à (GreaterThan), égal ou supérieur à (GreaterThanOrEqual), Inférieur à (LessThan), Inférieur ou égal à (LessThanOrEqual).
    - **threshold** : cette valeur déclenche l’action de mise à l’échelle. Dans ce modèle, les machines sont ajoutées au jeu de mise à l’échelle défini lorsque l’utilisation moyenne du processeur dans le jeu de machines des est supérieur à 50 %.
    - **direction** : cette valeur détermine l’opération qui est effectuée lorsque la valeur de seuil est atteinte. Les valeurs possibles sont Augmenter ou Diminuer. Dans ce modèle, le nombre de machines virtuelles dans le jeu de mise à l’échelle est augmenté si le seuil est supérieur à 50 % dans la fenêtre de temps définie.
    - **type** : cette valeur est le type d’action qui doit se produire. Elle doit être définie sur ChangeCount.
    - **value** : cette valeur est le nombre de machines virtuelles qui sont ajoutées ou supprimées dans le jeu de mise à l’échelle. Cette valeur doit être définie sur 1 ou supérieur. La valeur par défaut est 1. Dans ce modèle, le nombre d’ordinateurs présent dans le jeu de mise à l’échelle augmente de 1 lorsque le seuil est atteint.
    - **cooldown** : cette valeur est la durée d’attente depuis la dernière opération de mise à l’échelle avant que l’action suivante se produise. Elle doit être comprise entre une minute et une semaine.

12. Enregistrez le fichier de modèle.    

## <a name="step-3:-upload-the-template-to-storage"></a>Étape 3 : Téléchargement du modèle de stockage

Le modèle peut être chargé pour autant que vous connaissiez le nom et la clé primaire du compte de stockage que vous avez créé à l’étape 1.

1. Dans votre interface de ligne de commande (interpréteur de commandes, terminal, invite de commandes), exécutez les commandes suivantes pour définir les variables d’environnement nécessaires pour accéder au compte de stockage :

        export AZURE_STORAGE_ACCOUNT={account_name}
        export AZURE_STORAGE_ACCESS_KEY={key}

    Vous pouvez obtenir la clé en cliquant sur l’icône de clé lors de l’affichage de la ressource de compte de stockage dans le portail Azure. Quand vous utilisez une invite de commandes Windows, tapez **set** à la place d’export.

2. Créez le conteneur pour stocker le modèle.

        azure storage container create -p Blob templates

3. Chargez le fichier de modèle correspondant au nouveau conteneur.

        azure storage blob upload VMSSTemplate.json templates VMSSTemplate.json

## <a name="step-4:-deploy-the-template"></a>Étape 4 : déployer le modèle

Maintenant que vous avez créé le modèle, vous pouvez commencer à déployer les ressources. Utilisez cette commande pour démarrer le processus :

    azure group deployment create --template-uri https://vmsstestsa.blob.core.windows.net/templates/VMSSTemplate.json vmsstestrg1 vmsstestdp1

Lorsque vous appuyez sur Entrée, vous êtes invité à fournir des valeurs pour les variables que vous avez affectées. Remplacez les valeurs suivantes :

    vmName: vmsstestvm1
    vmSSName: vmsstest1
    instanceCount: 5
    adminUserName: vmadmin1
    adminPassword: VMpass1
    resourcePrefix: vmsstest

Environ 15 minutes sont nécessaires pour le déploiement correct de toutes les ressources.

>[AZURE.NOTE]Vous pouvez également utiliser la capacité du portail à déployer les ressources. Utilisez ce lien : https://portal.azure.com/#create/Microsoft.Template/uri/<link to VM Scale Set JSON template>

## <a name="step-5:-monitor-resources"></a>Étape 5 : analyser les ressources

Vous pouvez obtenir des informations sur les jeux de mise à l’échelle de machine virtuelle à l’aide des méthodes suivantes :

 - Le portail Azure : vous pouvez en obtenir une quantité limitée d’informations sur l’utilisation du portail.
 - [Explorateur de ressources Azure](https://resources.azure.com/) : cet outil est le meilleur qui soit pour déterminer l’état actuel de votre groupe identique. Suivez ce chemin d’accès. Vous devriez voir la vue de l’instance du groupe à échelle identique que vous avez créée :

        subscriptions > {your subscription} > resourceGroups > vmsstestrg1 > providers > Microsoft.Compute > virtualMachineScaleSets > vmsstest1 > virtualMachines

 - Interface de ligne de commande Azure - Utilisez cette commande pour obtenir des informations :

        azure resource show -n vmsstest1 -r Microsoft.Compute/virtualMachineScaleSets -o 2015-06-15 -g vmsstestrg1

 - Connectez-vous à la machine virtuelle jumpbox comme vous le feriez pour n’importe quel autre ordinateur et vous pouvez ensuite accéder à distance aux machines virtuelles du groupe à échelle identique pour surveiller les processus individuels.

>[AZURE.NOTE]Vous trouverez une API REST complète permettant d’obtenir des informations sur les groupes à échelle identique dans [Groupes de machines virtuelles à échelle identique](https://msdn.microsoft.com/library/mt589023.aspx)

## <a name="step-6:-remove-the-resources"></a>Étape 6 : supprimer les ressources

Étant donné que les ressources utilisées dans Microsoft Azure vous sont facturées, il est toujours conseillé de supprimer les ressources qui ne sont plus nécessaires. Vous n’avez pas besoin de supprimer séparément les ressources d’un groupe de ressources. Vous pouvez supprimer le groupe de ressources pour supprimer automatiquement toutes ses ressources.

        azure group delete vmsstestrg1

## <a name="next-steps"></a>Étapes suivantes

- Découvrez des exemples de fonctionnalités de surveillance Azure Insights dans les [exemple de démarrage rapide de l’interface de ligne de commande (CLI) multiplateforme Azure Insights](../azure-portal/insights-cli-samples.md)
- Pour en savoir plus sur les fonctionnalités de notification, consultez [Utilisation d’actions de mise à l’échelle automatique pour envoyer des notifications d’alerte webhook et par courrier électronique dans Azure Insights](../azure-portal/insights-autoscale-to-webhook-email.md) 
- Découvrez comment [utiliser les journaux d’audit pour envoyer des notifications d’alerte webhook et par courrier électronique dans Azure Insights](../azure-portal/insights-auditlog-to-webhook-email.md)
- Découvrez le modèle [Mettre à l’échelle automatiquement un groupe identique de machine virtuelle exécutant une application Ubuntu/Apache/PHP](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-lapstack-autoscale) qui configure une pile LAMP pour tester la fonctionnalité de mise à l’échelle automatique des groupes identiques de machines virtuelles.



<!--HONumber=Oct16_HO2-->


