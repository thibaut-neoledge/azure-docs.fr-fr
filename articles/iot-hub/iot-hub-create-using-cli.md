---
title: "Création d’un IoT Hub à l’aide de l’interface de ligne de commande Azure (az.py) | Microsoft Docs"
description: "Création d’un Azure IoT Hub à l’aide de l’interface de ligne de commande Azure multiplateforme 2.0 (version préliminaire) (az.py)."
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/02/2016
ms.author: dobett
translationtype: Human Translation
ms.sourcegitcommit: 39c8c4944ef19379dc04e04a717ab60d305593c4
ms.openlocfilehash: c52d9a5fadf494cc066bee773543c9d67bb8334b


---
# <a name="create-an-iot-hub-using-the-azure-cli-20-preview"></a>Créer un IoT Hub à l’aide de l’interface Azure CLI 2.0 (version préliminaire)

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

## <a name="introduction"></a>Introduction

Vous pouvez utiliser l’interface de ligne de commande Azure 2.0 (version préliminaire) pour créer et gérer des hubs Azure IoT de façon programmée. Cet article explique comment utiliser l’interface de ligne de commande Azure 2.0 (version préliminaire) (az.py) pour créer un IoT Hub.

Vous pouvez exécuter la tâche en utilisant l’une des versions suivantes de l’interface de ligne de commande (CLI) :

* [Azure CLI (azure.js)](iot-hub-create-using-cli-nodejs.md) : l'interface de ligne de commande pour les modèles de déploiement Classique et Resource Manager.
* Azure CLI 2.0 (version préliminaire) (az.py) : l’interface de ligne de commande nouvelle génération pour le modèle de déploiement Resource Manager, comme décrit dans cet article.

Pour réaliser ce didacticiel, vous avez besoin des éléments suivants :

* Un compte Azure actif. Si vous ne possédez pas de compte, vous pouvez créer un [compte gratuit][lnk-free-trial] en quelques minutes.
* [Version préliminaire d’Azure CLI 2.0][lnk-CLI-install].

## <a name="sign-in-and-set-your-azure-account"></a>Se connecter à votre compte Azure et le définir

Connectez-vous à votre compte Azure et configurez l'interface de ligne de commande Azure pour une utilisation avec les ressources IoT Hub.

1. Dans l’invite de commande, exécutez la [commande login][lnk-login-command] :
    
    ```azurecli
    az login
    ```

    Suivez les instructions pour vous authentifier à l’aide du code et vous connecter à votre compte Azure via un navigateur web.

2. Si vous possédez plusieurs abonnements Azure, la connexion à Azure vous donne accès à tous les abonnements Azure associés à vos informations d’identification. Utilisez la [commande suivante pour répertorier les comptes Azure][lnk-az-account-command] que vous pouvez utiliser :
    
    ```azurecli
    az account list 
    ```

    Utilisez la commande suivante pour sélectionner l’abonnement que vous souhaitez utiliser afin d'exécuter les commandes pour créer votre IoT Hub. Vous pouvez utiliser le nom de l’abonnement ou l’ID de la sortie de la commande précédente :

    ```azurecli
    az account set --subscription {your subscription name or id}
    ```

3. Vous devez inscrire le fournisseur IoT avant de déployer les ressources IoT. Exécutez la [commande suivante pour enregistrer le fournisseur IoT][lnk-az-register-command] :
    
    ```azurecli
    az provider register -namespace "Microsoft.Devices"
    ```

4. Vous devrez peut-être installer le _composant iot_ de l’interface de ligne de commande Azure. Exécutez la [commande suivante pour ajouter le composant iot][lnk-az-addcomponent-command] :
    
    ```azurecli
    az component update --add iot
    ```

## <a name="create-an-iot-hub"></a>Création d’un IoT Hub

Utilisez l’interface de ligne de commande Azure pour créer un groupe de ressources, puis ajoutez un IoT Hub.

1. Lorsque vous créez un IoT Hub, vous devez le créer dans un groupe de ressources. Utilisez un groupe de ressources existant, ou exécutez la [commande suivante pour en créer un][lnk-az-resource-command] :
    
    ```azurecli
     az resource group create --name {your resource group name} --location westus
    ```

    > [!TIP]
    > L’exemple précédent crée le groupe de ressources dans l’emplacement États-Unis de l'Ouest. Vous pouvez afficher une liste des emplacements disponibles en exécutant la commande `az account list-locations -o table`.
    >
    >

2. Utilisez la [commande suivante pour créer un IoT Hub][lnk-az-iot-command] dans votre groupe de ressources :
    
    ```azurecli
    az iot hub create --name {your iot hub name} --resource-group {your resource group name} --sku S1
    ```

> [!NOTE]
> Le nom de votre IoT Hub doit être globalement unique. La commande précédente crée un IoT Hub dans le niveau de tarification S1 pour lequel vous êtes facturé. Pour plus d’informations, consultez la [tarification Azure IoT Hub][lnk-iot-pricing].
>
>

## <a name="remove-an-iot-hub"></a>Supprimer un hub IoT

Vous pouvez utiliser l’interface de ligne de commande Azure pour [supprimer une ressource][lnk-az-resource-command], par exemple un IoT Hub, ou un groupe de ressources et toutes ses ressources, y compris les IoT Hubs.

Pour supprimer un IoT Hub, exécutez la commande suivante :

```azurecli
az resource delete --name {your iot hub name} --resource-group {your resource group name} --resource-type Microsoft.Devices/IotHubs
```

Pour supprimer un groupe de ressources et toutes ses ressources, exécutez la commande suivante :

```azurecli
az resource group delete --name {your resource group name}
```

## <a name="next-steps"></a>Étapes suivantes
Pour en savoir plus sur le développement pour IoT Hub, consultez les pages suivantes :

* [Guide du développeur d’IoT Hub][lnk-devguide]

Pour explorer davantage les capacités de IoT Hub, consultez :

* [Utilisation du portail Azure pour gérer IoT Hub][lnk-portal]

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-CLI-install]: https://docs.microsoft.com/cli/azure/install-az-cli2
[lnk-login-command]: https://docs.microsoft.com/cli/azure/get-started-with-az-cli2
[lnk-az-account-command]: https://docs.microsoft.com/cli/azure/account
[lnk-az-register-command]: https://docs.microsoft.com/cli/azure/provider
[lnk-az-addcomponent-command]: https://docs.microsoft.com/cli/azure/component
[lnk-az-resource-command]: https://docs.microsoft.com/cli/azure/resource
[lnk-az-iot-command]: https://docs.microsoft.com/cli/azure/iot
[lnk-iot-pricing]: https://azure.microsoft.com/pricing/details/iot-hub/
[lnk-devguide]: iot-hub-devguide.md
[lnk-portal]: iot-hub-create-through-portal.md 



<!--HONumber=Dec16_HO2-->


