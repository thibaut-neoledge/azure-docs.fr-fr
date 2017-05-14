---
title: "Créer un IoT Hub Azure à l’aide d’une cmdlet PowerShell | Microsoft Docs"
description: "Comment utiliser une cmdlet PowerShell pour créer un IoT Hub."
services: iot-hub
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/04/2017
ms.author: dobett
ms.translationtype: Human Translation
ms.sourcegitcommit: 7c4d5e161c9f7af33609be53e7b82f156bb0e33f
ms.openlocfilehash: 7914b9f1f747d246f58c19b7faf56357bd39a201
ms.contentlocale: fr-fr
ms.lasthandoff: 05/04/2017


---
# <a name="create-an-iot-hub-using-the-new-azurermiothub-cmdlet"></a>Créez un IoT Hub à l’aide de la cmdlet New-AzureRmIotHub

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

## <a name="introduction"></a>Introduction

Vous pouvez utiliser les cmdlets Azure PowerShell pour créer et gérer des IoT Hubs Azure. Ce didacticiel vous montre comment créer un IoT Hub à l’aide de PowerShell.

> [!NOTE]
> Azure dispose de deux modèles de déploiement pour créer et utiliser des ressources : [Azure Resource Manager et classique](../azure-resource-manager/resource-manager-deployment-model.md).  Cet article traite de l’utilisation du modèle de déploiement Azure Resource Manager.

Pour réaliser ce didacticiel, vous avez besoin des éléments suivants :

* Un compte Azure actif. <br/>Si vous ne possédez pas de compte, vous pouvez créer un [compte gratuit][lnk-free-trial] en quelques minutes.
* [Applets de commande Azure PowerShell][lnk-powershell-install].

## <a name="connect-to-your-azure-subscription"></a>Connectez-vous à un abonnement Azure
Dans une invite de commandes PowerShell, entrez la commande suivante pour vous connecter à votre abonnement Azure :

```powershell
Login-AzureRmAccount
```

## <a name="create-resource-group"></a>Créer un groupe de ressources

Vous avez besoin d’un groupe de ressources pour déployer un IoT Hub. Vous pouvez utiliser un groupe de ressources existant ou en créer un.

Vous pouvez utiliser la commande suivante pour détecter les emplacements où vous pouvez déployer un IoT Hub :

```powershell
((Get-AzureRmResourceProvider `
  -ProviderNamespace Microsoft.Devices).ResourceTypes `
  | Where-Object ResourceTypeName -eq IoTHubs).Locations
```

Pour créer un groupe de ressources pour votre IoT Hub dans l’un des emplacements pris en charge pour l’IoT Hub, utilisez la commande suivante. Cet exemple crée un groupe de ressources appelé **MyIoTRG1** dans la région **États-Unis de l’Est** :

```powershell
New-AzureRmResourceGroup -Name MyIoTRG1 -Location "East US"
```

## <a name="create-an-iot-hub"></a>Créer un hub IoT

Pour créer un IoT Hub dans le groupe de ressources que vous avez créé à l’étape précédente, utilisez la commande suivante. Cet exemple crée un hub **S1** appelé **MyTestIoTHub** dans la région **États-Unis de l’Est** :

```powershell
New-AzureRmIotHub `
    -ResourceGroupName MyIoTRG1 `
    -Name MyTestIoTHub `
    -SkuName S1 -Units 1 `
    -Location "East US"
```

> [!NOTE]
> Le nom du IoT Hub doit être unique.

Vous pouvez afficher tous les IoT Hubs de votre abonnement à l’aide de la commande suivante :

```powershell
Get-AzureRmIotHub
```

L’exemple précédent ajoute un IoT Hub S1 Standard qui vous est facturé. Vous pouvez supprimer l’IoT Hub à l’aide de la commande suivante :

```powershell
Remove-AzureRmIotHub `
    -ResourceGroupName MyIoTRG1 `
    -Name MyTestIoTHub
```

Vous pouvez également supprimer un groupe de ressources et toutes les ressources qu’il contient à l’aide de la commande suivante :

```powershell
Remove-AzureRmResourceGroup -Name MyIoTRG1
```

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez déployé un IoT Hub à l’aide d’une cmdlet PowerShell, vous pouvez aller encore plus loin :

* Découvrez d’autres [cmdlets PowerShell pour travailler avec votre IoT Hub][lnk-iothub-cmdlets].
* Découvrez les capacités de [l’API REST du fournisseur de ressources IoT Hub][lnk-rest-api].

Pour en savoir plus sur le développement pour IoT Hub, consultez les articles suivants :

* [Présentation du Kit de développement logiciel (SDK) C][lnk-c-sdk]
* [Kits de développement logiciel (SDK) Azure IoT][lnk-sdks]

Pour explorer davantage les capacités de IoT Hub, consultez :

* [Simulation d’un appareil avec le Kit de développement logiciel (SDK) de la passerelle IoT][lnk-gateway]

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-powershell-install]: https://docs.microsoft.com/powershell/azure/install-azurerm-ps
[lnk-iothub-cmdlets]: https://docs.microsoft.com/powershell/module/azurerm.iothub/
[lnk-rest-api]: https://docs.microsoft.com/rest/api/iothub/iothubresource

[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md

