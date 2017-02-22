---
title: "Création d’un IoT Hub à l’aide de l’interface de ligne de commande Azure (azure.js) | Microsoft Docs"
description: "Création d’un Azure IoT Hub à l’aide de l’interface de ligne de commande Azure multiplateforme (azure.js)."
services: iot-hub
documentationcenter: .net
author: BeatriceOltean
manager: timlt
editor: 
ms.assetid: 46a17831-650c-41d9-b228-445c5bb423d3
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/04/2017
ms.author: boltean
translationtype: Human Translation
ms.sourcegitcommit: 9ded95283b52f0fc21ca5b99df8e72e1e152fe1c
ms.openlocfilehash: 44291ed86775312a894f6acf92260cde76982f27


---
# <a name="create-an-iot-hub-using-the-azure-cli"></a>Création d’un IoT Hub à l’aide de l’interface de ligne de commande Azure
[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

## <a name="introduction"></a>Introduction
Vous pouvez utiliser l’interface de ligne de commande Azure (azure.js) pour créer et gérer des Azure IoT Hubs de façon programmée. Cet article explique comment utiliser l’interface de ligne de commande Azure (azure.js) pour créer un IoT Hub.

Vous pouvez exécuter la tâche en utilisant l’une des versions suivantes de l’interface de ligne de commande (CLI) :

* Azure CLI (azure.js) : l’interface de ligne de commande pour les modèles de déploiement Classique et Resource Manager décris dans cet article.
* [Azure CLI 2.0 (version préliminaire) (az.py)](iot-hub-create-using-cli.md) : l’interface de ligne de commande nouvelle génération pour le modèle de déploiement Resource Manager.

Pour réaliser ce didacticiel, vous avez besoin des éléments suivants :

* Un compte Azure actif. Si vous ne possédez pas de compte, vous pouvez en créer un [gratuitement][lnk-free-trial] en quelques minutes.
* [Azure CLI 0.10.4][lnk-CLI-install] ou version ultérieure. Si vous avez déjà installé l’interface de ligne de commande (CLI) Azure, vous pouvez valider la version actuelle à l’invite de commandes avec la commande suivante :
  ```
    azure --version
  ```

> [!NOTE]
> Azure dispose de deux modèles de déploiement pour créer et utiliser des ressources : [Azure Resource Manager et classique](../azure-resource-manager/resource-manager-deployment-model.md). L’interface de ligne de commande (CLI) Azure doit être en mode Azure Resource Manager :
> 
> ```
> azure config mode arm
> ```
> 
> 

## <a name="set-your-azure-account-and-subscription"></a>Configurer votre compte et votre abonnement Microsoft Azure
1. À l’invite de commandes, connectez-vous en tapant la commande suivante :
   
   ```
    azure login
   ```
   Utilisez le navigateur web suggéré et le code d’authentification.
2. Si vous possédez plusieurs abonnements Azure, la connexion à Azure donne accès à tous les abonnements Azure associés à vos informations d’identification. Vous pouvez afficher les abonnements et identifier l’abonnement Azure par défaut à l’aide de la commande suivante :
   
   ```
    azure account list 
   ```

   Pour définir le contexte de l’abonnement sous lequel vous souhaitez exécuter le reste des commandes, utilisez la commande suivante :

   ```
    azure account set <subscription name>
   ```

3. Si vous n’avez pas de groupe de ressources, vous pouvez en créer un nommé **exampleResourceGroup** :
   ```
    azure group create -n exampleResourceGroup -l westus
   ```

> [!TIP]
> Pour plus d’informations sur l’utilisation de l’interface de ligne de commande Azure pour gérer des ressources Azure, voir l’article [Utiliser l’interface de ligne de commande Azure pour gérer les ressources et les groupes de ressources Azure][lnk-CLI-arm]. 
> 
> 

## <a name="create-an-iot-hub"></a>Création d’un IoT Hub
Paramètres obligatoires :

```
 azure iothub create -g <resource-group> -n <name> -l <location> -s <sku-name> -u <units>  
    - <resourceGroup> The resource group name (case insensitive alphanumeric, underscore and hyphen, 1-64 length)
    - <name> (The name of the IoT hub to be created. The format is case insensitive alphanumeric, underscore and hyphen, 3-50 length )
    - <location> (The location (azure region/datacenter) where the IoT hub will be provisioned.
    - <sku-name> (The name of the sku, one of: [F1, S1, S2, S3] etc. For the latest full list refer to the pricing page for IoT Hub.
    - <units> (The number of provisioned units. Range : F1 [1-1] : S1, S2 [1-200] : S3 [1-10]. IoT Hub units are based on your total message count and the number of devices you want to connect.)
```
Pour afficher tous les paramètres disponibles pour la création, vous pouvez utiliser la commande d’aide dans une invite de commandes :

```
    azure iothub create -h 
```
Exemple rapide :

 Pour créer un IoT Hub appelé **exampleIoTHubName** dans le groupe de ressources **exampleResourceGroup** exécutez la commande suivante :

```
    azure iothub create -g exampleResourceGroup -n exampleIoTHubName -l westus -k s1 -u 1
```

> [!NOTE]
> Cette commande CLI Azure crée un IoT Hub Standard S1 pour lequel vous êtes facturé. Vous pouvez supprimer l’IoT Hub **exampleIoTHubName** à l’aide de la commande suivante :
> 
> ```
> azure iothub delete -g exampleResourceGroup -n exampleIoTHubName
> ```
> 
> 
> 

## <a name="next-steps"></a>Étapes suivantes
Pour en savoir plus sur le développement pour IoT Hub, consultez les pages suivantes :

* [Kits de développement logiciel (SDK) IoT][lnk-sdks]

Pour explorer davantage les capacités de IoT Hub, consultez :

* [Utilisation du portail Azure pour gérer IoT Hub][lnk-portal]

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-azure-portal]: https://portal.azure.com/
[lnk-status]: https://azure.microsoft.com/status/
[lnk-CLI-install]: ../xplat-cli-install.md
[lnk-rest-api]: https://msdn.microsoft.com/library/mt589014.aspx
[lnk-CLI-arm]: ../azure-resource-manager/xplat-cli-azure-resource-manager.md

[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-portal]: iot-hub-create-through-portal.md 



<!--HONumber=Jan17_HO1-->


