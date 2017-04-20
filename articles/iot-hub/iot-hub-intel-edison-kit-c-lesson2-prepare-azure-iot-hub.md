---
title: "Connecter Intel Edison (C) à Azure IoT - Leçon 2 : Enregistrer un appareil | Microsoft Docs"
description: "Créez un groupe de ressources, créez un Azure IoT Hub et inscrivez Edison dans Azure IoT Hub à l’aide de l’interface de ligne de commande Azure."
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: 
ROBOTS: NOINDEX
redirect_url: /azure/iot-hub/iot-hub-intel-edison-kit-c-get-started
ms.assetid: 80bfc3cd-a1fc-4775-8994-d8033381dd3d
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 475b25f02715a60493e79ecd2170854019dfc4ac
ms.openlocfilehash: 9eebae32bf469e0744855ed9f98306bd36eb9671
ms.lasthandoff: 01/25/2017


---
# <a name="create-your-iot-hub-and-register-intel-edison"></a>Créer votre IoT Hub et inscrire Intel Edison
## <a name="what-you-will-do"></a>Procédure à suivre
* Créez un groupe de ressources.
* Créez votre Azure IoT Hub dans le groupe de ressources.
* Ajoutez Intel Edison à Azure IoT Hub à l’aide de l’interface de ligne de commande Azure.

Lorsque vous utilisez l’interface de ligne de commande Azure pour ajouter Edison à votre IoT Hub, le service génère une clé permettant à Edison de s’authentifier auprès du service. Si vous rencontrez des problèmes, recherchez des solutions dans la [page de résolution des problèmes][troubleshooting].

## <a name="what-you-will-learn"></a>Contenu
Cet article portera sur les éléments suivants :
* Utilisation de l’interface de ligne de commande Azure pour créer un IoT Hub.
* Création d’une identité d’appareil pour Edison dans votre IoT Hub.

## <a name="what-you-need"></a>Ce dont vous avez besoin
* Un compte Azure. Si vous ne possédez pas de compte Azure, vous pouvez créer un [compte d’évaluation Azure gratuit](http://azure.microsoft.com/pricing/free-trial/) en quelques minutes.
* Vous devez avoir installé l’interface de ligne de commande Azure.

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


## <a name="register-edison-in-your-iot-hub"></a>Inscription d’Edison dans votre IoT Hub
Chaque appareil qui envoie des messages à votre IoT Hub et reçoit des messages de votre IoT Hub doit être inscrit sous un ID unique.

Inscrivez Edison dans votre IoT Hub en exécutant la commande suivante :

```bash
az iot device create --device-id myinteledison --hub-name {my hub name}
```

## <a name="summary"></a>Résumé
Vous avez créé un IoT Hub et enregistré Edison sous une identité d’appareil dans votre IoT Hub. Vous êtes prêt à découvrir comment envoyer des messages d’Edison à votre IoT Hub.

## <a name="next-steps"></a>Étapes suivantes
[Créer une application de fonction Azure et un compte de stockage Azure pour traiter et stocker les messages du IoT Hub][process-and-store-iot-hub-messages].


<!-- Images and links -->

[troubleshooting]: iot-hub-intel-edison-kit-c-troubleshooting.md
[process-and-store-iot-hub-messages]: iot-hub-intel-edison-kit-c-lesson3-deploy-resource-manager-template.md
