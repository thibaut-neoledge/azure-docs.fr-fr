---
title: "Configurer l’Intel NUC comme passerelle Azure IoT | Microsoft Docs"
description: "Configurez Intel NUC comme passerelle IoT entre un capteur et Azure IoT Hub, afin de collecter des informations de capteurs et de les envoyer à IoT Hub."
services: iot-hub
documentationcenter: 
author: shizn
manager: yjianfeng
tags: 
keywords: passerelle iot, intel nuc, ordinateur nuc, DE3815TYKE
ms.assetid: 917090d6-35c2-495b-a620-ca6f9c02b317
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2016
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 1c4f44787a7200a1c3634b258df32d30152daa90
ms.openlocfilehash: ef479f503ac4e911f3a9d311d0f436d95396eedd


---
# <a name="set-up-intel-nuc-as-an-iot-gateway"></a>Configurer l’Intel NUC comme passerelle IoT

## <a name="what-you-will-do"></a>Procédure à suivre

- Configurez l’Intel NUC comme passerelle IoT.
- Installez le package du kit de développement logiciel (SDK) Gateway Azure IoT sur Intel NUC.
- Exécutez un exemple d’application « hello_world » sur Intel NUC pour vérifier le bon fonctionnement de la passerelle.
Si vous rencontrez des problèmes, recherchez des solutions dans la [page de résolution des problèmes](iot-hub-gateway-kit-c-troubleshooting.md).

## <a name="what-you-will-learn"></a>Contenu

Dans cette leçon, vous allez apprendre :

- la connexion d’Intel NUC à des périphériques ;
- l’installation et la mise à jour des packages nécessaires sur Intel NUC à l’aide du gestionnaire de package intelligent ;
- l’exécution de l’exemple d’application « hello_world » pour vérifier le bon fonctionnement de la passerelle.

## <a name="what-you-need"></a>Ce dont vous avez besoin

- Un Kit Intel NUC DE3815TYKE avec la suite logicielle de passerelle Intel IoT (Wind River Linux *7.0.0.13) préinstallée.
- Un câble Ethernet.
- Un clavier.
- Un câble VGA ou HDMI.
- Un écran équipé d’un port VGA ou HDMI.

![Un kit de passerelle](media/iot-hub-gateway-kit-lessons/lesson1/kit.png)

## <a name="connect-intel-nuc-with-the-peripherals"></a>Connexion d’Intel NUC aux périphériques

L’image ci-dessous est un exemple d’Intel NUC connecté à plusieurs périphériques :

1. Connecté à un clavier.
2. Connecté à l’écran par le biais d’un câble VGA ou d’un câble HDMI.
3. Connecté à un réseau filaire par le biais d’un câble Ethernet.
4. Connecté à l’alimentation par le biais d’un câble d’alimentation.

![Intel NUC connecté à des périphériques](media/iot-hub-gateway-kit-lessons/lesson1/nuc.png)

## <a name="connect-to-the-intel-nuc-system-from-host-computer-via-secure-shell-ssh"></a>Connexion au système Intel NUC à partir de l’ordinateur hôte par le biais de Secure Shell (SSH)

Vous devez disposer d’un clavier et d’un écran pour obtenir l’adresse IP de votre appareil NUC. Si vous connaissez déjà l’adresse IP, vous pouvez passer à l’étape 3 de cette section.

1. Activez Intel NUC en appuyant sur le bouton d’alimentation et connectez-vous au système.

   Le nom d’utilisateur et le mot de passe par défaut sont tous les deux `root`.

2. Obtenez l’adresse IP de NUC en exécutant la commande `ifconfig`. Cette étape s’effectue sur l’appareil NUC.

   Voici un exemple de la sortie de cette commande.

   ![sortie ifconfig indiquant l’IP NUC](media/iot-hub-gateway-kit-lessons/lesson1/ifconfig.png)

   Dans cet exemple, la valeur qui suit `inet addr:` correspond à l’adresse IP dont vous avez besoin lorsque vous prévoyez de vous connecter à distance à partir d’un ordinateur hôte à Intel NUC.

3. Utilisez l’un des clients SSH suivants à partir de votre ordinateur hôte pour vous connecter à Intel NUC.

   - [PuTTY](http://www.putty.org/) pour Windows.
   - Le client SSH intégré sur Ubuntu ou macOS.

   Il est plus efficace et plus productif d’utiliser Intel NUC à partir d’un ordinateur hôte. Vous avez besoin de l’adresse IP, du nom d’utilisateur et du mot de passe pour vous connecter à NUC par le biais d’un client SSH. Voici un exemple d’utilisation d’un client SSH sur macOS.
   ![Client SSH exécuté sur macOS](media/iot-hub-gateway-kit-lessons/lesson1/ssh.png)

## <a name="install-the-azure-iot-gateway-sdk-package"></a>Installation du package du Kit de développement logiciel (SDK) Gateway Azure IoT

Le package du kit de développement logiciel (SDK) Gateway Azure IoT contient les fichiers binaires précompilés du Kit de développement logiciel (SDK) et de ses dépendances. Ces fichiers binaires sont : le kit de développement logiciel (SDK) Gateway Azure IoT, le Kit de développement logiciel (SDK) Azure IoT et les outils correspondants. Le package contient également un exemple d’application « hello_world » qui est utilisé pour vérifier le bon fonctionnement de la passerelle. Le Kit de développement logiciel (SDK) constitue la partie principale de la passerelle. Pour installer le package, procédez comme suit :

1. Ajoutez le référentiel cloud IoT en exécutant les commandes suivantes dans une fenêtre de terminal :

   ```bash
   rpm --import http://iotdk.intel.com/misc/iot_pub.key
   smart channel --add IoT_Cloud type=rpm-md name="IoT_Cloud" baseurl=http://iotdk.intel.com/repos/iot-cloud/wrlinux7/rcpl13/ -y
   ```

   La commande `rpm` importe la clé rpm. La commande `smart channel` ajoute le canal rpm au gestionnaire de package intelligent (Smart Package Manager). Avant d’exécuter la commande `smart update`, vous voyez un résultat similaire à celui ci-dessous.

   ![Sortie des commandes rpm et canal intelligent](media/iot-hub-gateway-kit-lessons/lesson1/rpm_smart_channel.png)

   ```bash
   smart update
   ```

2. Exécutez la commande suivante pour installer le package :

   ```bash
   smart install packagegroup-cloud-azure -y
   ```

   `packagegroup-cloud-azure` est le nom du package. La commande `smart install` permet d’installer le package.

   Une fois le package installé, Intel NUC doit fonctionner en tant que passerelle.

## <a name="run-the-azure-iot-gateway-sdk-helloworld-sample-application"></a>Exécution de l’exemple d’application « hello_world » du Kit de développement logiciel (SDK) Gateway Azure IoT

Accédez à `azureiotgatewaysdk/samples` et exécutez l’exemple d’application « hello_world ». Cet exemple d’application crée une passerelle à partir du fichier `hello_world.json` et utilise les composants fondamentaux de l’architecture du Kit de développement logiciel (SDK) Gateway Azure IoT pour consigner le message « Hello World » dans un fichier toutes les 5 secondes.

Vous pouvez exécuter l’exemple d’application « hello_world » en exécutant la commande suivante :

```bash
cd /usr/share/azureiotgatewaysdk/samples/hello_world/
./hello_world hello_world.json
```

L’exemple d’application génère la sortie suivante si la passerelle fonctionne correctement :

![sortie d’application](media/iot-hub-gateway-kit-lessons/lesson1/hello_world.png)

Si vous rencontrez des problèmes, recherchez des solutions dans la [page de résolution des problèmes](iot-hub-gateway-kit-c-troubleshooting.md).

## <a name="summary"></a>Résumé

Félicitations ! Vous avez terminé la configuration d’Intel NUC comme passerelle. Vous pouvez maintenant passer à la leçon suivante afin de configurer un ordinateur hôte, de créer un Azure IoT Hub et d’inscrire votre unité logique Azure IoT Hub.

## <a name="next-steps"></a>Étapes suivantes
[Préparer votre ordinateur hôte et Azure IoT Hub](iot-hub-gateway-kit-c-lesson2-get-the-tools-win32.md)



<!--HONumber=Dec16_HO3-->


