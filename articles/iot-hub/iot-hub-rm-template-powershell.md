---
title: "Créer un IoT Hub Azure à l’aide d’un modèle (PowerShell) | Microsoft Docs"
description: "Comment utiliser un modèle Azure Resource Manager pour créer un IoT Hub avec PowerShell."
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 7eade855-c289-4ffb-b5ef-02be8c5f670f
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/06/2016
ms.author: dobett
translationtype: Human Translation
ms.sourcegitcommit: 2abfeebeac222f4371b0945e1aeb6fcf8e51595d
ms.openlocfilehash: b884fe128b8414ae1692df92e89a41f7ba1c0447


---
# <a name="create-an-iot-hub-using-azure-resource-manager-template-powershell"></a>Créer un IoT Hub avec un modèle Azure Resource Manager (PowerShell)
[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

## <a name="introduction"></a>Introduction
Vous pouvez utiliser Azure Resource Manager pour créer et gérer des hubs Azure IoT de façon programmée. Ce didacticiel vous montre comment utiliser un modèle Azure Resource Manager pour créer un IoT Hub à l’aide de PowerShell.

> [!NOTE]
> Azure dispose de deux modèles de déploiement pour créer et utiliser des ressources : [Azure Resource Manager et classique](../azure-resource-manager/resource-manager-deployment-model.md).  Cet article traite de l’utilisation du modèle de déploiement Azure Resource Manager.
> 
> 

Pour réaliser ce didacticiel, vous avez besoin des éléments suivants :

* Un compte Azure actif. <br/>Si vous ne possédez pas de compte, vous pouvez créer un [compte gratuit][lnk-free-trial] en quelques minutes.
* [Azure PowerShell 1.0][lnk-powershell-install] ou version ultérieure.

> [!TIP]
> L’article [Utilisation d’Azure PowerShell avec Azure Resource Manager][lnk-powershell-arm] fournit des informations sur l’utilisation des scripts PowerShell et des modèles Resource Manager pour créer des ressources Azure. 
> 
> 

## <a name="connect-to-your-azure-subscription"></a>Connectez-vous à un abonnement Azure
Dans une invite de commandes PowerShell, entrez la commande suivante pour vous connecter à votre abonnement Azure :

```
Login-AzureRmAccount
```

Vous pouvez utiliser les commandes suivantes pour découvrir où vous pouvez déployer un IoT Hub et les versions de l'API actuellement prises en charge :

```
((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Devices).ResourceTypes | Where-Object ResourceTypeName -eq IoTHubs).Locations
((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Devices).ResourceTypes | Where-Object ResourceTypeName -eq IoTHubs).ApiVersions
```

Créez un groupe de ressources pour contenir votre IoT Hub avec la commande suivante dans l’un des emplacements pris en charge pour l’IoT Hub. Cet exemple crée un groupe de ressources appelé **MyIoTRG1**:

```
New-AzureRmResourceGroup -Name MyIoTRG1 -Location "East US"
```

## <a name="submit-an-azure-resource-manager-template-to-create-an-iot-hub"></a>Envoyer un modèle Azure Resource Manager pour créer un IoT Hub
Utilisez un modèle JSON pour créer un IoT Hub dans votre groupe de ressources. Vous pouvez également utiliser un modèle Azure Resource Manager pour apporter des modifications à un IoT Hub existant.

1. Utilisez un éditeur de texte pour créer un modèle Azure Resource Manager appelé **template.json** avec la définition de ressource suivante pour créer un IoT Hub standard. Cet exemple ajoute l’IoT Hub dans la zone **États-Unis de l’Est**, crée deux groupes de consommateurs (**cg1** et **cg2**) sur le point de terminaison compatible Event Hubs et utilise la version de l’API **2016-02-03**. Ce modèle vous demande également de transmettre le nom de l’IoT Hub en tant que paramètre appelé **hubName**. Pour obtenir la liste des emplacements qui prennent en charge IoT Hub, voir [Statut Azure][lnk-status].
   
    ```
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "hubName": {
          "type": "string"
        }
      },
      "resources": [
      {
        "apiVersion": "2016-02-03",
        "type": "Microsoft.Devices/IotHubs",
        "name": "[parameters('hubName')]",
        "location": "East US",
        "sku": {
          "name": "S1",
          "tier": "Standard",
          "capacity": 1
        },
        "properties": {
          "location": "East US"
        }
      },
      {
        "apiVersion": "2016-02-03",
        "type": "Microsoft.Devices/IotHubs/eventhubEndpoints/ConsumerGroups",
        "name": "[concat(parameters('hubName'), '/events/cg1')]",
        "dependsOn": [
          "[concat('Microsoft.Devices/Iothubs/', parameters('hubName'))]"
        ]
      },
      {
        "apiVersion": "2016-02-03",
        "type": "Microsoft.Devices/IotHubs/eventhubEndpoints/ConsumerGroups",
        "name": "[concat(parameters('hubName'), '/events/cg2')]",
        "dependsOn": [
          "[concat('Microsoft.Devices/Iothubs/', parameters('hubName'))]"
        ]
      }
      ],
      "outputs": {
        "hubKeys": {
          "value": "[listKeys(resourceId('Microsoft.Devices/IotHubs', parameters('hubName')), '2016-02-03')]",
          "type": "object"
        }
      }
    }
    ```
2. Enregistrez le fichier de modèle Azure Resource Manager sur votre ordinateur local. Cet exemple suppose que vous l’enregistrez dans un dossier appelé **c:\templates**.
3. Exécutez la commande suivante pour déployer votre nouvel IoT Hub, en utilisant le nom de votre IoT Hub en tant que paramètre. Dans cet exemple, le nom du service IoT Hub est **abcmyiothub** (ce nom doit être globalement unique et inclure votre nom ou vos initiales) :
   
    ```
    New-AzureRmResourceGroupDeployment -ResourceGroupName MyIoTRG1 -TemplateFile C:\templates\template.json -hubName abcmyiothub
    ```
4. La sortie affiche les clés de l’IoT Hub que vous avez créé.
5. Vous pouvez vérifier que votre application a ajouté le nouvel IoT Hub en accédant au [portail Azure][lnk-azure-portal] et en affichant votre liste de ressources, ou en utilisant l’applet de commande PowerShell **Get-AzureRmResource**.

> [!NOTE]
> Cet exemple d’application ajoute un IoT Hub S1 Standard qui vous est facturé. Lorsque vous avez terminé, vous pouvez supprimer le IoT Hub via le [portail Azure][lnk-azure-portal] ou à l’aide de l’applet de commande PowerShell **Remove-AzureRmResource**.
> 
> 

## <a name="next-steps"></a>Étapes suivantes
Maintenant que vous avez déployé un IoT Hub à l’aide d’un modèle Azure Resource Manager avec PowerShell, vous pouvez aller encore plus loin :

* Découvrez les capacités de l’[API REST du fournisseur de ressources IoT Hub][lnk-rest-api].
* Pour plus d’informations sur les capacités d’Azure Resource Manager, voir [Vue d’ensemble d’Azure Resource Manager][lnk-azure-rm-overview].

Pour en savoir plus sur le développement pour IoT Hub, consultez les pages suivantes :

* [Présentation du Kit de développement logiciel (SDK) C][lnk-c-sdk]
* [Kits de développement logiciel (SDK) Azure IoT][lnk-sdks]

Pour explorer davantage les capacités de IoT Hub, consultez :

* [Simulation d’un appareil avec le Kit de développement logiciel (SDK) de la passerelle IoT][lnk-gateway]

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-azure-portal]: https://portal.azure.com/
[lnk-status]: https://azure.microsoft.com/status/
[lnk-powershell-install]: /powershell/azureps-cmdlets-docs
[lnk-rest-api]: https://msdn.microsoft.com/library/mt589014.aspx
[lnk-azure-rm-overview]: ../azure-resource-manager/resource-group-overview.md
[lnk-powershell-arm]: ../azure-resource-manager/powershell-azure-resource-manager.md

[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md



<!--HONumber=Dec16_HO1-->


