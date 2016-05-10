<properties
	pageTitle="Gérer des machines virtuelles dans un jeu de mise à l’échelle de machine virtuelle | Microsoft Azure"
	description="Gérez des machines virtuelles dans un jeu de mise à l’échelle de machine virtuelle à l'aide d'Azure PowerShell."
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

# Gérer des machines dans un jeu de mise à l’échelle de machines virtuelles

Azure PowerShell vous offre beaucoup de puissance et de flexibilité pour gérer les ressources dans Microsoft Azure. Utilisez les tâches décrites dans cet article pour gérer les ressources de machine virtuelle de votre jeu de mise à l’échelle de machine virtuelle.

- [Afficher les informations relatives à un jeu de mise à l’échelle de machine virtuelle](#displayvm)
- [Démarrer une machine virtuelle dans un jeu de mise à l’échelle](#start)
- [Arrêter une machine virtuelle dans un jeu de mise à l’échelle](#stop)
- [Redémarrer une machine virtuelle dans un jeu de mise à l’échelle](#restart)
- [Supprimer une machine virtuelle d’un jeu de mise à l’échelle](#delete)

Toutes les tâches qui impliquent la gestion d'une machine virtuelle dans un jeu de mise à l'échelle nécessitent de connaître l'ID d'instance de la machine que vous souhaitez gérer. Vous pouvez utiliser [Azure Resource Explorer](https://resources.azure.com) pour rechercher l'ID d'instance d'une machine virtuelle dans un jeu de mise à l'échelle. Vous utilisez également Resource Explorer pour vérifier l'état des tâches que vous avez terminées.

[AZURE.INCLUDE [powershell-preview](../../includes/powershell-preview-inline-include.md)]

## <a id="displayvm"></a>Afficher les informations relatives à un jeu de mise à l’échelle de machine virtuelle

Vous pouvez obtenir des informations générales sur un jeu de mise à l'échelle, également appelé la vue d'instance. Ou vous pouvez obtenir des informations plus spécifiques, par exemple des informations sur les ressources du jeu.

Dans cette commande, remplacez *nom de groupe de ressources* par le nom du groupe de ressources contenant le jeu de mise à l’échelle de machine virtuelle et *nom du jeu de mise à l’échelle* par le nom du jeu de mise à l’échelle de machine virtuelle, puis exécutez la commande :

    Get-AzureRmVmss -ResourceGroupName "resource group name" -VMScaleSetName "scale set name"

Le résultat suivant doit s’afficher :

    Sku                      :  {
                                  "name": "Standard_A0",
                                  "tier": "Standard",
                                  "capacity": 4
                                }
    UpgradePolicy            :  {
                                  "mode": "Manual"
                                }
    VirtualMachineProfile    :  {
                                  "osProfile": {
                                    "computerNamePrefix": "myvmss1",
                                    "adminUserName": "user1",
                                    "adminPassword": null,
                                    "customData": null,
                                    "windowsConfiguration": {
                                      "provisionVMAgent": true,
                                      "enableAutomaticUpdates": true,
                                      "timeZone": null,
                                      "additionalUnattendContent": null,
                                      "winRM": null
                                    }
                                    "linuxConfiguration": null,
                                    "secrets": []
                                  },
                                  "storageProfile": {
                                    "imageReference": {
                                      "publisher": "MicrosoftWindowsServer",
                                      "offer": "WindowsServer",
                                      "sku": "2012-R2-Datacenter",
                                      "version": "latest"
                                    },
                                    "osDisk": {
                                      "name": "vmssosdisk",
                                      "caching": "ReadOnly",
                                      "createOption": "FromImage",
                                      "osType": null,
                                      "image": null,
                                      "vhdContainers": [
                                        "https://amyst1.blob.core.windows.net/vmss",
                                        "https://gmyst1.blob.core.windows.net/vmss",
                                        "https://mmyst1.blob.core.windows.net/vmss",
                                        "https://smyst1.blob.core.windows.net/vmss",
                                        "https://ymyst1.blob.core.windows.net/vmss"
                                      ]
                                    }
                                  },
                                  "networkProfile": {
                                    "networkInterfaceConfigurations": [
                                      {
                                        "name": "myresnc2",
                                        "properties.primary": true,
                                        "properties.ipConfigurations": [
                                          {
                                            "name": "ip1",
                                            "properties.subnet": {
                                              "id": "/subscriptions/{subscription-id}/resourceGroups/rg1/providers/Microsoft.Network/virtualNetworks/myresvn1/subnets/myressn1"
                                            },
                                            "properties.loadBalancerBackendAddressPools": [
                                              {
                                                "id": "/subscriptions/{subscription-id}/resourceGroups/rg1/providers/Microsoft.Network/loadBalancers/myreslb1/backendAddressPools/bepool1"
                                              }
                                            ],
                                            "properties.loadBalancerInboundNatPools": [
                                              {
                                                "id": "/subscriptions/{subscription-id}/resourceGroups/rg1/providers/Microsoft.Network/loadBalancers/myreslb1/inboundNatPools/natpool1"
                                              }
                                            ],
                                            "id": null
                                          }
                                        ],
                                        "id": null
                                      }
                                    ]
                                  },
                                  "extensionProfile": {
                                    "extensions": [
                                      {
                                        "name": "Microsoft.Insights.VMDiagnosticsSettings",
                                        "properties.publisher": "Microsoft.Azure.Diagnostics",
                                        "properties.type": "IaaSDiagnostics",
                                        "properties.typeHandlerVersion": "1.5",
                                        "properties.autoUpgradeMinorVersion": true,
                                        "properties.settings": {
                                          "xmlCfg": "{encoded configuration}",
                                          "storageAccount": "amyst1"
                                        },
                                        "properties.protectedSettings": null,
                                        "properties.provisioningState": null,
                                        "id": null
                                      }
                                    ]
                                  }
                                }
    ProvisioningState           : Succeeded
    Id                          : /subscriptions/{subscription-id}/resourceGroups/rg1/providers/Microsoft.Compute/virtualMachineScaleSets/myvmss1
    Name                        : myvmss1
	Type                        : Microsoft.Compute/virtualMachineScaleSets
	Location                    : westus
	Tags.Count                  : 0
	Tags                        :

Dans obtenir des informations générales, remplacez *nom de groupe de ressources* par le nom du groupe de ressources contenant le jeu de mise à l’échelle de machine virtuelle et *nom du jeu de mise à l’échelle* par le nom du jeu de mise à l’échelle de machine virtuelle, puis exécutez la commande :

	Get-AzureRmVmss -ResourceGroupName "resource group name" -VMScaleSetName "scale set name" -InstanceView

Le résultat suivant doit s’afficher :

    VirtualMachine   :  {
                          "statusesSummary": [
                            {
                              "code": "ProvisioningState/succeeded",
                              "count": 4
                            }
                          ]
                        }
    Extensions.Count :  1
    Extensions       :  {
                          "name": "Microsoft.Insights.VMDiagnosticsSettings",
                          "statusesSummary": [
                            {
                              "code": "ProvisioningState/succeeded",
                              "count": 4
                            }
                          ]
                        }
	Statuses.Count   :  1
	Statuses         :  {
                          "code": "ProvisioningState/succeeded",
                          "level": "Info",
                          "displayStatus": "Provisioning succeeded",
                          "message": null,
                          "time": "2016-03-14T20:29:37.170809Z"
                        }

## <a id="start"></a>Démarrer une machine virtuelle dans un jeu de mise à l’échelle

Dans cette commande, remplacez *nom de groupe de ressources* par le nom du groupe de ressources contenant le jeu de mise à l’échelle de machine virtuelle, *nom du jeu de mise à l’échelle de machine virtuelle* par le nom du jeu de mise à l’échelle, *ID d’instance* par l’identificateur de la machine virtuelle que vous voulez redémarrer, puis exécutez la commande :

    Start-AzureRmVmssVM -ResourceGroupName "resource group name" -VMScaleSetName "scale set name" -InstanceId "instance id"

Dans Resource Explorer, nous pouvons voir que l'état de l'instance indique **running** (en cours d’exécution) :

    "statuses": [
      {
        "code": "ProvisioningState/succeeded",
        "level": "Info",
        "displayStatus": "Provisioning succeeded",
        "time": "2016-03-15T02:10:08.0730839+00:00"
      },
      {
        "code": "PowerState/running",
        "level": "Info",
        "displayStatus": "VM running"
      }
    ]

## <a id="stop"></a>Arrêter une machine virtuelle dans un jeu de mise à l’échelle

Dans cette commande, remplacez *nom de groupe de ressources* par le nom du groupe de ressources contenant le jeu de mise à l’échelle de machine virtuelle, *nom du jeu de mise à l’échelle* par le nom du jeu de mise à l’échelle, *ID d’instance* par l’identificateur de la machine virtuelle que vous voulez arrêter, puis exécutez la commande :

	Stop-AzureRmVmssVM -ResourceGroupName "resource group name" -VMScaleSetName "scale set name" -InstanceId "instance id"

Dans Resource Explorer, nous pouvons voir que l'état de l'instance indique **deallocated** (libéré) :

	"statuses": [
      {
        "code": "ProvisioningState/succeeded",
        "level": "Info",
        "displayStatus": "Provisioning succeeded",
        "time": "2016-03-15T01:25:17.8792929+00:00"
      },
      {
        "code": "PowerState/deallocated",
        "level": "Info",
        "displayStatus": "VM deallocated"
      }
    ]

## <a id="restart"></a>Redémarrer une machine virtuelle dans un jeu de mise à l’échelle

Dans cette commande, remplacez *nom de groupe de ressources* par le nom du groupe de ressources contenant le jeu de mise à l’échelle de machine virtuelle, *nom du jeu de mise à l’échelle* par le nom du jeu de mise à l’échelle, *ID d’instance* par l’identificateur de la machine virtuelle que vous voulez redémarrer, puis exécutez la commande :

	Restart-AzureRmVmssVM -ResourceGroupName "resource group name" -VMScaleSetName "scale set name" -InstanceId "instance id"

## <a id="delete"></a>Supprimer une machine virtuelle d’un jeu de mise à l’échelle

Dans cette commande, remplacez *nom de groupe de ressources* par le nom du groupe de ressources contenant le jeu de mise à l’échelle de machine virtuelle, *nom du jeu de mise à l’échelle* par le nom du jeu de mise à l’échelle, *ID d’instance* par l’identificateur de la machine virtuelle que vous voulez supprimer du jeu de mise à l’échelle, puis exécutez la commande :

	Remove-AzureRmVmssVM -ResourceGroupName "resource group name" –VMScaleSetName "scale set name" -InstanceId "instance id"

<!---HONumber=AcomDC_0427_2016-->