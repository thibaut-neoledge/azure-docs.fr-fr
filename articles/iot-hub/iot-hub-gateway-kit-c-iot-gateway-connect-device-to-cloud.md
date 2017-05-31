---
title: "Utiliser une passerelle IoT pour connecter un appareil à Azure IoT Hub | Microsoft Docs"
description: "Découvrez comment utiliser Intel NUC comme passerelle IoT pour connecter un TI SensorTag et envoyer des données de capteur à Azure IoT Hub dans le cloud."
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: iot passerelle connecter appareil dans le cloud
ms.assetid: cb851648-018c-4a7e-860f-b62ed3b493a5
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/06/2017
ms.author: xshi
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 2b772afc946f1637cb8d61c198bc9cf18f42e1ba
ms.contentlocale: fr-fr
ms.lasthandoff: 05/10/2017


---
# <a name="use-iot-gateway-to-connect-things-to-the-cloud---sensortag-to-azure-iot-hub"></a>Utilisation de la passerelle IoT pour connecter des objets au cloud - SensorTag pour Azure IoT Hub

> [!NOTE]
> Avant de commencer ce didacticiel, vérifiez que vous avez suivi le didacticiel [Configurer l’Intel NUC comme passerelle IoT](iot-hub-gateway-kit-c-lesson1-set-up-nuc.md). Dans [Configurer l’Intel NUC comme passerelle IoT](iot-hub-gateway-kit-c-lesson1-set-up-nuc.md), vous configurez l’appareil Intel NUC comme passerelle IoT.

## <a name="what-you-will-learn"></a>Contenu

Vous apprenez à utiliser une passerelle IoT pour connecter un SensorTag de Texas Instruments (CC2650STK) pour Azure IoT Hub. La passerelle IoT envoie les données de température et d’humidité collectées auprès du SensorTag à Azure IoT Hub.

## <a name="what-you-will-do"></a>Procédure à suivre

- Créez un hub IoT.
- Inscrivez un appareil dans l’IoT Hub pour le SensorTag.
- Activez la connexion entre la passerelle IoT et le SensorTag.
- Exécutez un exemple d’application BLE pour envoyer des données du SensorTag à votre IoT Hub.

## <a name="what-you-need"></a>Ce dont vous avez besoin

- Didacticiel [Configurer l’Intel NUC comme passerelle IoT](iot-hub-gateway-kit-c-lesson1-set-up-nuc.md) suivi dans lequel vous configurez l’Intel NUC comme passerelle IoT.
- Un client SSH qui s’exécute sur votre ordinateur hôte. PuTTY est recommandé sur Windows. Linux et macOS sont déjà accompagnés d’un client SSH.
- L’adresse IP et le nom d’utilisateur et le mot de passe pour accéder à la passerelle du client SSH.
- Une connexion Internet.

[!INCLUDE [iot-hub-get-started-create-hub-and-device](../../includes/iot-hub-get-started-create-hub-and-device.md)]

> [!NOTE]
> Ici vous inscrivez ce nouvel appareil pour votre SensorTag

## <a name="enable-the-connection-between-the-iot-gateway-and-the-sensortag"></a>Activer la connexion entre la passerelle IoT et le SensorTag

Dans cette section, vous allez effectuer les tâches suivantes :

- Obtenez l’adresse MAC du SensorTag pour la connexion Bluetooth.
- Établissez une connexion Bluetooth entre la passerelle IoT et le SensorTag.

### <a name="get-the-mac-address-of-the-sensortag-for-bluetooth-connection"></a>Obtenir l’adresse MAC du SensorTag pour la connexion Bluetooth

1. Sur l’ordinateur hôte, exécutez le client SSH et connectez-vous à la passerelle IoT.
1. Débloquez le Bluetooth en exécutant la commande suivante :

   ```bash
   sudo rfkill unblock bluetooth
   ```

1. Démarrez le service Bluetooth sur la passerelle IoT et accédez à un interpréteur de commandes Bluetooth pour configurer le Bluetooth en exécutant les commandes suivantes :

   ```bash
   sudo systemctl start bluetooth
   bluetoothctl
   ```

1. Allumez le contrôleur Bluetooth en exécutant la commande suivante sur l’interpréteur de commandes Bluetooth :

   ```bash
   power on
   ```

   ![allumer le contrôleur Bluetooth sur la passerelle IoT avec bluetoothctl](./media/iot-hub-iot-gateway-connect-device-to-cloud/8_power-on-bluetooth-controller-at-bluetooth-shell-bluetoothctl.png)

1. Démarrez l’analyse des périphériques Bluetooth proches en exécutant la commande suivante :

   ```bash
   scan on
   ```

   ![Analyser les périphériques Bluetooth proches avec bluetoothctl](./media/iot-hub-iot-gateway-connect-device-to-cloud/9_start-scan-nearby-bluetooth-devices-at-bluetooth-shell-bluetoothctl.png)

1. Appuyez sur le bouton de jumelage du SensorTag. Le voyant vert du SensorTag clignote.
1. Sur l’interpréteur de commandes Bluetooth, vous devriez voir que le SensorTag a été détecté. Prenez note de l’adresse MAC du SensorTag. Dans cet exemple, l’adresse MAC du SensorTag est `24:71:89:C0:7F:82`.
1. Désactivez l’analyse en exécutant la commande suivante :

   ```bash
   scan off
   ```

   ![Arrêter l’analyse des périphériques Bluetooth proches avec bluetoothctl](./media/iot-hub-iot-gateway-connect-device-to-cloud/10_stop-scanning-nearby-bluetooth-devices-at-bluetooth-shell-bluetoothctl.png)

### <a name="initiate-a-bluetooth-connection-from-the-iot-gateway-to-the-sensortag"></a>Établir une connexion Bluetooth entre la passerelle IoT et le SensorTag

1. Connectez-vous au SensorTag en exécutant la commande suivante :

   ```bash
   connect <MAC address>
   ```

   ![Se connecter au SensorTag avec bluetoothctl](./media/iot-hub-iot-gateway-connect-device-to-cloud/11_connect-to-sensortag-at-bluetooth-shell-bluetoothctl.png)

1. Déconnectez-vous du SensorTag et quittez l’interpréteur de commandes Bluetooth en exécutant les commandes suivantes :

   ```bash
   disconnect
   exit
   ```

   ![Se déconnecter du SensorTag avec bluetoothctl](./media/iot-hub-iot-gateway-connect-device-to-cloud/12_disconnect-from-sensortag-at-bluetooth-shell-bluetoothctl.png)

Vous avez activé avec succès la connexion entre le SensorTag et la passerelle IoT.

## <a name="run-a-ble-sample-application-to-send-sensortag-data-to-your-iot-hub"></a>Exécutez un exemple d’application BLE pour envoyer des données du SensorTag à votre IoT Hub

L’exemple d’application BLE (Bluetooth Low Energy) est fourni par Azure IoT Edge. L’exemple d’application collecte les données de la connexion BLE et les envoie à votre IoT Hub. Pour exécuter l’exemple d’application, vous devez suivre la procédure suivante :

1. Configurez l’exemple d’application.
1. Exécutez l’exemple d’application sur la passerelle IoT.

### <a name="configure-the-sample-application"></a>Configurer l’exemple d’application

1. Accédez au dossier de l’exemple d’application en exécutant la commande suivante :

   ```bash
   cd /user/share/azureiotgatewaysdk/samples
   ```

1. Ouvrez le fichier de configuration en exécutant la commande suivante :

   ```bash
   vi ble_gateway.json
   ```

1. Dans le fichier de configuration, renseignez les valeurs suivantes :

   **IoTHubName** : le nom de votre IoT Hub.

   **IoTHubSuffix** : obtenez IoTHubSuffix à partir de la clé primaire de la chaîne de connexion d’appareil que vous avez relevée. Assurez-vous d’obtenir la clé primaire de la chaîne de connexion d’appareil et non la clé primaire de votre chaîne de connexion d’IoT Hub. La clé primaire de la chaîne de connexion d’appareil est au format `HostName=IOTHUBNAME.IOTHUBSUFFIX;DeviceId=DEVICEID;SharedAccessKey=SHAREDACCESSKEY`.

   **Transport** : la valeur par défaut est `amqp`. Cette valeur indique le protocole lors du transport. Elle peut être `http`, `amqp` ou `mqtt`.

   **macAddress** : l’adresse MAC du SensorTag que vous avez relevée.

   **deviceID**: l’ID de l’appareil que vous avez créé dans votre IoT Hub.

   **deviceKey** : la clé primaire de la chaîne de connexion d’appareil.

   ![Exécuter le fichier de configuration de l’exemple d’application BLE](./media/iot-hub-iot-gateway-connect-device-to-cloud/13_edit-config-file-of-ble-sample.png)

1. Appuyez sur `ESC` et entrez `:wq` pour enregistrer le fichier.

### <a name="run-the-sample-application"></a>Exécuter l’exemple d’application

1. Assurez-vous que le SensorTag est allumé.
1. Exécutez la commande suivante :

   ```bash
   ./ble_gateway ble_gateway.json
   ```

## <a name="next-steps"></a>Étapes suivantes

[Utiliser une passerelle IoT pour la transformation des données de capteur avec Azure IoT Edge](iot-hub-gateway-kit-c-use-iot-gateway-for-data-conversion.md)

