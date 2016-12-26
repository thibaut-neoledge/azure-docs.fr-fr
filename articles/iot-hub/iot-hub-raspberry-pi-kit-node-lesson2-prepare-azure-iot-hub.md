---
title: "Création de votre IoT Hub et inscription de Raspberry Pi 3 | Microsoft Docs"
description: "Créez un groupe de ressources, créez un Azure IoT Hub et inscrivez Pi dans IoT Hub à l’aide de l’interface de ligne de commande Azure."
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: raspberry pi cloud, connexion pi cloud
ms.assetid: 736215b6-e7e4-46f9-af30-0ded9ffa5204
ms.service: iot-hub
ms.devlang: node
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/28/2016
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: d93b7b67ee0727f45c3f4b93459b013b16805bf6
ms.openlocfilehash: fcfb90a385a68e863b3c1d3d0fa3bb24fe778291


---
# <a name="create-your-iot-hub-and-register-raspberry-pi-3"></a>Création de votre IoT Hub et inscription de Raspberry Pi 3
## <a name="what-you-will-do"></a>Procédure à suivre
* Créez un groupe de ressources.
* Créez votre Azure IoT Hub dans le groupe de ressources.
* Ajoutez Raspberry Pi 3 à Azure IoT Hub à l’aide de l’interface de ligne de commande Azure.

Lorsque vous utilisez l’interface de ligne de commande Azure pour ajouter Pi à votre IoT Hub, le service génère une clé permettant à Pi de s’authentifier auprès du service. Si vous rencontrez des problèmes, recherchez des solutions dans la [page de résolution des problèmes](iot-hub-raspberry-pi-kit-node-troubleshooting.md).

## <a name="what-you-will-learn"></a>Contenu
Cet article portera sur les éléments suivants :
* Utilisation de l’interface de ligne de commande Azure pour créer un IoT Hub
* Création d’une identité d’appareil pour Pi dans votre IoT Hub

## <a name="what-you-need"></a>Ce dont vous avez besoin
* Un compte Azure
* Un Mac ou un ordinateur Windows avec l’interface de ligne de commande Azure installée

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
   az resource group create --name iot-sample --location westus
   ```

   `westus` est l’emplacement où vous créez votre groupe de ressources. Si vous souhaitez utiliser un autre emplacement, vous pouvez exécuter `az account list-locations -o table` pour voir tous les emplacements pris en charge par Azure.
 
5. Créez un IoT Hub dans le groupe de ressources iot-sample en exécutant la commande suivante :

   ```bash
   az iot hub create --name {my hub name} --resource-group iot-sample
   ```

   Par défaut, l’outil crée un IoT Hub dans le niveau de tarification gratuit. Pour plus d’informations, consultez la [tarification Azure IoT Hub](https://azure.microsoft.com/pricing/details/iot-hub/).

> [!NOTE] 
> Le nom de votre IoT Hub doit être globalement unique. Vous ne pouvez créer qu’une seule édition F1 d’Azure IoT Hub sous votre abonnement Azure.

## <a name="register-pi-in-your-iot-hub"></a>Inscription de Pi dans votre IoT Hub
Chaque appareil qui envoie des messages à votre IoT Hub et reçoit des messages de votre IoT Hub doit être inscrit sous un ID unique. Vous utilisez l’interface de ligne de commande Azure pour inscrire votre Pi et créer un certificat X.509 auto-signé pour l’authentification des appareils.

Exécutez la commande suivante :

```bash
# For Windows command prompt
az iot device create --device-id myraspberrypi --hub-name {my hub name} --x509 --output-dir %USERPROFILE%\.iot-hub-getting-started
 
# For macOS or Ubuntu
az iot device create --device-id myraspberrypi --hub-name {my hub name} --x509 --output-dir ~/.iot-hub-getting-started
```

## <a name="summary"></a>Résumé
Vous avez créé un IoT Hub et enregistré Pi sous une identité d’appareil dans votre IoT Hub. Vous êtes prêt à découvrir comment envoyer des messages de Pi à votre IoT Hub.

## <a name="next-steps"></a>Étapes suivantes
[Créer une application de fonction Azure et un compte de stockage Azure pour traiter et stocker les messages du IoT Hub](iot-hub-raspberry-pi-kit-node-lesson3-deploy-resource-manager-template.md)




<!--HONumber=Nov16_HO5-->


