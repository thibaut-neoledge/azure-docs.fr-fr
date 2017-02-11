---
title: "Créer votre Azure IoT Hub et inscrire Adafruit Feather M0 WiFi | Microsoft Docs"
description: "Créez un groupe de ressources, créez un Azure IoT Hub et inscrivez votre carte Adafruit Feather M0 WiFi dans Azure IoT Hub à l’aide de l’interface de ligne de commande Azure."
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "connexion arduino au cloud, azure iot hub, internet des objets cloud, azure iot hub créer un appareil, arduino cloud"
ms.assetid: 5edc690b-7a1d-4ebc-b011-ff27bfffe6e8
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/13/2016
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 9e8084fe26229ef9ed1676c0a3c34e0ee7be88b1
ms.openlocfilehash: 232c391e61e7e7ec053d2e7170f9abe4c79d9528


---
# <a name="create-your-iot-hub-and-register-your-adafruit-feather-m0-wifi-arduino-board"></a>Créer votre IoT Hub et inscrire votre carte Adafruit Feather M0 WiFi Arduino

## <a name="what-you-will-do"></a>Procédure à suivre
* Créez un groupe de ressources.
* Créez votre Azure IoT Hub dans le groupe de ressources.
* Ajoutez votre carte Arduino à Azure IoT Hub à l’aide de l’interface de ligne de commande Azure.

Lorsque vous utilisez l’interface de ligne de commande Azure pour ajouter votre carte Arduino à votre IoT Hub, le service génère une clé permettant à votre carte Arduino de s’authentifier auprès du service. Si vous rencontrez des problèmes, recherchez des solutions dans la [page de résolution des problèmes][troubleshoot].

## <a name="what-you-will-learn"></a>Contenu
Cet article portera sur les éléments suivants :
* Utilisation de l’interface de ligne de commande Azure pour créer un IoT Hub.
* Comment créer une identité d’appareil pour votre carte Arduino dans votre IoT Hub.

## <a name="what-you-need"></a>Ce dont vous avez besoin
* Un compte Azure
* Un ordinateur avec l’interface de ligne de commande Azure installée

## <a name="create-your-iot-hub"></a>Création de votre IoT Hub
Azure IoT Hub vous permet de connecter, surveiller et gérer des millions de ressources IoT. Pour créer votre IoT Hub, procédez comme suit :

1. Connectez-vous à votre compte Azure en exécutant la commande suivante :

   ```bash
   az login
   ```

   Après une connexion réussie, tous vos abonnements disponibles sont répertoriés.

2. Définissez l’abonnement par défaut que vous souhaitez utiliser en exécutant la commande suivante :

   ```bash
   az account set --subscription {subscription id or name}
   ```

   Vous trouverez `subscription ID or name` dans la sortie de la commande `az login` ou `az account list`.

3. Inscrivez le fournisseur en exécutant la commande suivante. Les fournisseurs de ressources sont des services qui fournissent des ressources pour votre application. Vous devez inscrire le fournisseur avant de déployer la ressource Azure proposée par le fournisseur.

   ```bash
   az provider register -n "Microsoft.Devices"
   ```
4. Créer un groupe de ressources nommé iot-sample dans la région ouest des États-Unis en exécutant la commande suivante :

   ```bash
   az group create --name iot-sample --location westus
   ```

   `westus` est l’emplacement où vous créez votre groupe de ressources. Si vous souhaitez utiliser un autre emplacement, vous pouvez exécuter `az account list-locations -o table` pour voir tous les emplacements pris en charge par Azure.

5. Créez un IoT Hub dans le groupe de ressources iot-sample en exécutant la commande suivante :

   ```bash
   az iot hub create --name {my hub name} --resource-group iot-sample
   ```

Par défaut, l’outil crée un IoT Hub dans le niveau de tarification gratuit. Pour plus d’informations, consultez la [tarification Azure IoT Hub](https://azure.microsoft.com/pricing/details/iot-hub/).

> [!NOTE]
> Le nom de votre IoT Hub doit être globalement unique.
> Vous ne pouvez créer qu’une seule édition F1 d’Azure IoT Hub sous votre abonnement Azure.

## <a name="register-your-arduino-board-in-your-iot-hub"></a>Inscrire votre carte Arduino dans votre IoT Hub
Chaque appareil qui envoie des messages à votre IoT Hub et reçoit des messages de votre IoT Hub doit être inscrit sous un ID unique.

Inscrivez votre carte Arduino dans votre IoT Hub en exécutant la commande suivante :

```bash
az iot device create --device-id mym0wifi --hub-name {my hub name}
```

## <a name="summary"></a>Résumé
Vous avez créé un IoT Hub et enregistré votre carte Arduino sous une identité d’appareil dans votre IoT Hub. Vous êtes prêt à découvrir comment envoyer des messages de votre carte Arduino à votre IoT Hub.

## <a name="next-steps"></a>Étapes suivantes
[Créer une application de fonction Azure et un compte de stockage Azure pour traiter et stocker les messages du IoT Hub][process-and-store-iot-hub-messages].


<!-- Images and links -->

[troubleshoot]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-troubleshooting.md
[process-and-store-iot-hub-messages]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson3-deploy-resource-manager-template.md


<!--HONumber=Dec16_HO2-->


