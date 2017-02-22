---
title: "Appareil simulé et passerelle Azure IoT - Leçon 2 : Enregistrer un appareil | Microsoft Docs"
description: 
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "azure iot hub, cloud de l’internet des objets, créer un appareil azure iot hub, ti sensortag, ti ble"
ms.assetid: 23cfbe21-22c6-4fe1-ae41-63714a897f12
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/07/2016
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 61e9a9fc7876094c04238c61cfc38efdd97b05f7
ms.openlocfilehash: 759085dcb4993d38f79cb19efe58e1a968ddb682


---

# <a name="create-your-azure-iot-hub-and-register-your-device"></a>Créer un Azure IoT Hub et inscrire votre appareil

## <a name="what-you-will-do"></a>Procédure à suivre

- Créer un groupe de ressources
- Créer votre premier IoT Hub
- Inscrire votre appareil dans votre IoT Hub à l’aide de l’interface de ligne de commande Azure. 

Lorsque vous inscrivez votre appareil dans votre IoT Hub, le service Azure IoT Hub génère une clé permettant à votre appareil de s’authentifier auprès du service. 

Si vous rencontrez des problèmes, recherchez des solutions dans la [page de résolution des problèmes](iot-hub-gateway-kit-c-sim-troubleshooting.md).

## <a name="what-you-will-learn"></a>Contenu

Dans cette leçon, vous allez apprendre ce qui suit :

- Utilisation de l’interface de ligne de commande Azure pour créer un IoT Hub.
- l’inscription d’un appareil dans un IoT Hub.

## <a name="what-you-need"></a>Ce dont vous avez besoin

- Un abonnement Azure actif. Si vous ne possédez pas de compte Azure, vous pouvez créer un [compte d’évaluation Azure gratuit](http://azure.microsoft.com/pricing/free-trial/) en quelques minutes.
- Vous devez avoir installé l’interface de ligne de commande Azure.

## <a name="create-an-iot-hub"></a>Créer un hub IoT

Pour créer un IoT Hub, procédez comme suit :

1. Connectez-vous à votre compte Azure en exécutant la commande suivante :

   ```bash
   az login
   ```

   Une fois la connexion établie, tous vos abonnements disponibles sont répertoriés.

2. Définissez l’abonnement Azure par défaut que vous souhaitez utiliser en exécutant la commande suivante :

   ```bash
   az account set --subscription {subscription id or name}
   ```

   Vous trouverez `subscription ID or name` dans la sortie de la commande `az login` ou `az account list`.

3. Inscrivez le fournisseur en exécutant la commande suivante. Les fournisseurs de ressources sont des services qui fournissent des ressources pour votre application. Vous devez inscrire le fournisseur avant de déployer la ressource Azure proposée par le fournisseur.

   ```bash
   az provider register -n "Microsoft.Devices"
   ```

4. Créez un groupe de ressources nommé `iot-gateway` dans la région États-Unis de l’Ouest en exécutant la commande suivante :

   ```bash
   az group create --name iot-gateway --location westus
   ```
   
   `westus` est l’emplacement où vous créez votre groupe de ressources. Si vous souhaitez utiliser un autre emplacement, vous pouvez exécuter `az account list-locations -o table` pour voir tous les emplacements pris en charge par Azure.

5. Créez un IoT Hub dans le groupe de ressources `iot-gateway` en exécutant la commande suivante :

   ```bash
   az iot hub create --name {my hub name} --resource-group iot-gateway
   ```

Par défaut, l’outil crée un IoT Hub dans le niveau de tarification gratuit. Pour plus d’informations, consultez la [tarification Azure IoT Hub](https://azure.microsoft.com/pricing/details/iot-hub/).

> [!NOTE]
> Le nom de votre IoT Hub doit être globalement unique. Vous ne pouvez créer qu’une seule édition F1 d’Azure IoT Hub sous votre abonnement Azure.

## <a name="register-your-device-in-your-iot-hub"></a>Inscrire votre appareil dans votre IoT Hub

Chaque appareil qui envoie des messages à votre IoT Hub et reçoit des messages de votre IoT Hub doit être inscrit sous un ID unique.
Inscrivez votre appareil dans votre Azure IoT Hub en exécutant la commande suivante :

```bash
az iot device create --device-id mydevice --hub-name {my hub name} --resource-group iot-gateway
```

## <a name="summary"></a>Résumé

Vous avez créé un IoT Hub et inscrit votre appareil logique sous une identité d’appareil dans votre IoT Hub. Vous êtes prêt à découvrir comment configurer et exécuter un exemple d’application de la passerelle pour envoyer des données de votre appareil physique à votre IoT Hub dans le cloud.

## <a name="next-steps"></a>Étapes suivantes
[Configurer et exécuter un exemple d’application de téléchargement cloud d’appareil simulé](iot-hub-gateway-kit-c-sim-lesson3-configure-simulated-device-app.md)


<!--HONumber=Jan17_HO4-->


