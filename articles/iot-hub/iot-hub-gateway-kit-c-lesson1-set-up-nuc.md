---
title: "Appareil SensorTag et passerelle Azure Io - Leçon 1 : Configurer l’Intel NUC | Microsoft Docs"
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
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: 4c6b70d793a7d734f5a29139e1f0b91f0d41e73a
ms.lasthandoff: 04/12/2017


---
# <a name="set-up-intel-nuc-as-an-iot-gateway"></a>Configurer l’Intel NUC comme passerelle IoT
[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

## <a name="what-you-will-do"></a>Procédure à suivre

- Configurez l’Intel NUC comme passerelle IoT.
- Installez le package du kit de développement logiciel (SDK) Gateway Azure IoT sur l’Intel NUC.
- Exécutez un exemple d’application « hello_world » sur l’Intel NUC pour vérifier le bon fonctionnement de la passerelle.

  > Si vous rencontrez des problèmes, recherchez des solutions dans la [page de résolution des problèmes](iot-hub-gateway-kit-c-troubleshooting.md).

## <a name="what-you-will-learn"></a>Contenu

Dans cette leçon, vous allez apprendre :

- la connexion d’Intel NUC à des périphériques ;
- l’installation et la mise à jour des packages nécessaires sur Intel NUC à l’aide du gestionnaire de package intelligent ;
- l’exécution de l’exemple d’application « hello_world » pour vérifier le bon fonctionnement de la passerelle.

## <a name="what-you-need"></a>Ce dont vous avez besoin

- Un Kit Intel NUC DE3815TYKE avec la suite logicielle de passerelle Intel IoT (Wind River Linux *7.0.0.13) préinstallée. [Cliquez ici pour acheter le kit de passerelle commerciale Grove IoT](https://www.seeedstudio.com/Grove-IoT-Commercial-Gateway-Kit-p-2724.html).
- Un câble Ethernet.
- Un clavier.
- Un câble VGA ou HDMI.
- Un écran équipé d’un port VGA ou HDMI.
- Facultatif : [SensorTag Texas Instruments (CC2650STK)](http://www.ti.com/tool/cc2650stk)

![Un kit de passerelle](media/iot-hub-gateway-kit-lessons/lesson1/kit.png)

## <a name="connect-intel-nuc-with-the-peripherals"></a>Connexion d’Intel NUC aux périphériques

L’image ci-dessous est un exemple d’Intel NUC connecté à plusieurs périphériques :

1. Connecté à un clavier.
2. Connecté à un écran à l’aide d’un câble VGA ou d’un câble HDMI.
3. Connecté à un réseau filaire à l’aide d’un câble Ethernet.
4. Connecté à l’alimentation à l’aide d’un câble d’alimentation.

![Intel NUC connecté à des périphériques](media/iot-hub-gateway-kit-lessons/lesson1/nuc.png)

## <a name="connect-to-the-intel-nuc-system-from-host-computer-via-secure-shell-ssh"></a>Connexion au système Intel NUC à partir de l’ordinateur hôte par le biais de Secure Shell (SSH)

Vous aurez besoin d’un clavier et d’un écran pour obtenir l’adresse IP de votre appareil Intel NUC. Si vous connaissez déjà l’adresse IP, vous pouvez passer directement à l’étape 3 de cette section.

1. Activez l’Intel NUC en appuyant sur le bouton d’alimentation, puis connectez-vous.

   Le nom d’utilisateur et le mot de passe par défaut sont tous les deux `root`.

       > Hit the enter key on your keyboard if you see either of the following errors when you boot: 'A TPM error (7) occurred attempting to read a pcr value.' or 'Timeout, No TPM chip found, activating TPM-bypass!'

2. Obtenez l’adresse IP de l’Intel NUC en exécutant la commande `ifconfig` sur l’appareil Intel NUC.

   Voici un exemple de la sortie de cette commande.

   ![Sortie de la commande ifconfig indiquant l’adresse IP de l’Intel NUC](media/iot-hub-gateway-kit-lessons/lesson1/ifconfig.png)

   Dans cet exemple, la valeur qui suit `inet addr:` correspond à l’adresse IP dont vous avez besoin lorsque vous vous connectez à l’Intel NUC à partir d’un ordinateur hôte.

3. Utilisez l’un des clients SSH suivants à partir de votre ordinateur hôte pour vous connecter à l’Intel NUC.

    - [PuTTY](http://www.putty.org/) pour Windows.
    - Le client SSH intégré sur Ubuntu ou macOS.

   Il est plus efficace et productif d’utiliser un Intel NUC à partir d’un ordinateur hôte. Vous aurez besoin de l’adresse IP, du nom d’utilisateur et du mot de passe de l’Intel NUC pour vous y connecter par le biais d’un client SSH. Voici un exemple qui fait appel à un client SSH sur Mac OS.
   ![Client SSH exécuté sur macOS](media/iot-hub-gateway-kit-lessons/lesson1/ssh.png)

## <a name="install-the-azure-iot-gateway-sdk-package"></a>Installation du package du Kit de développement logiciel (SDK) Gateway Azure IoT

Le package du kit de développement logiciel (SDK) Gateway Azure IoT contient les fichiers binaires précompilés du Kit de développement logiciel (SDK) et de ses dépendances. Ces fichiers binaires sont : le kit de développement logiciel (SDK) Gateway Azure IoT, le Kit de développement logiciel (SDK) Azure IoT et les outils correspondants. Le package contient également un exemple d’application « hello_world » qui est utilisé pour vérifier le bon fonctionnement de la passerelle. Le Kit de développement logiciel (SDK) constitue la partie principale de la passerelle. 

Pour installer le package, procédez comme suit.

1. Ajoutez le référentiel cloud IoT en exécutant les commandes suivantes dans une fenêtre de terminal :

   ```bash
   rpm --import https://iotdk.intel.com/misc/iot_pub2.key
   smart channel --add IoT_Cloud type=rpm-md name="IoT_Cloud" baseurl=http://iotdk.intel.com/repos/iot-cloud/wrlinux7/rcpl13/ -y
   smart channel --add WR_Repo type=rpm-md baseurl=https://distro.windriver.com/release/idp-3-xt/public_feeds/WR-IDP-3-XT-Intel-Baytrail-public-repo/RCPL13/corei7_64/
   ```

   > Entrez « o » quand il vous est demandé si vous voulez « inclure ce canal ? »

   La commande `rpm` importe la clé rpm. La commande `smart channel` ajoute le canal rpm au gestionnaire de package intelligent (Smart Package Manager). Avant d’exécuter la commande `smart update`, vous verrez une sortie similaire à celle ci-dessous.

   ![Sortie des commandes rpm et canal intelligent](media/iot-hub-gateway-kit-lessons/lesson1/rpm_smart_channel.png)

2. Exécutez la commande smart update :

   ```bash
   smart update
   ```

3. Installez le package Gateway Azure IoT en exécutant la commande suivante :

   ```bash
   smart install packagegroup-cloud-azure -y
   ```

   `packagegroup-cloud-azure` est le nom du package. La commande `smart install` permet d’installer le package.

    > Exécutez la commande suivante si vous recevez l’erreur « public key not available » (Clé publique non disponible).

    ```bash
    smart config --set rpm-check-signatures=false
    smart install packagegroup-cloud-azure -y
    ```
    > Redémarrez l’ordinateur Intel NUC si vous voyez cette erreur : « aucun package ne fournit util-linux-dev »

   Une fois le package installé, l’Intel NUC est prêt à fonctionner en tant que passerelle.

## <a name="run-the-azure-iot-gateway-sdk-helloworld-sample-application"></a>Exécution de l’exemple d’application « hello_world » du Kit de développement logiciel (SDK) Gateway Azure IoT

L’exemple d’application suivant crée une passerelle à partir d’un fichier `hello_world.json` et utilise les composants fondamentaux de l’architecture du SDK Gateway Azure IoT pour consigner le message « Hello World » dans un fichier (log.txt) toutes les 5 secondes.

Vous pouvez exécuter l’exemple Hello World en utilisant les commandes suivantes :

```bash
cd /usr/share/azureiotgatewaysdk/samples/hello_world/
./hello_world hello_world.json
```

Laissez l’application Hello World s’exécuter pendant quelques minutes, puis appuyez sur la touche Entrée pour l’arrêter.
![Sortie de l’application](media/iot-hub-gateway-kit-lessons/lesson1/hello_world.png)

> Vous pouvez ignorer les erreurs « invalid argument handle(NULL) » (Descripteur d’argument non valide (NULL)) qui s’affichent après que vous avez appuyé sur Entrée.

Vous pouvez vérifier que la passerelle a été correctement exécutée en ouvrant le fichier log.txt qui se trouve maintenant dans votre dossier hello_world : ![Vue du répertoire de log.txt](media/iot-hub-gateway-kit-lessons/lesson1/logtxtdir.png)

Ouvrez le fichier log.txt en utilisant la commande suivante :

```bash
vim log.txt
```

Vous verrez alors le contenu du fichier log.txt, qui est une sortie mise en forme des messages de journalisation qui ont été consignés toutes les 5 secondes par le module de passerelle Hello World.
![Vue du répertoire de log.txt](media/iot-hub-gateway-kit-lessons/lesson1/logtxtview.png)

Si vous rencontrez des problèmes, recherchez des solutions dans la [page de résolution des problèmes](iot-hub-gateway-kit-c-troubleshooting.md).

## <a name="summary"></a>Résumé

Félicitations ! Vous avez terminé la configuration d’Intel NUC comme passerelle. Vous pouvez maintenant passer à la leçon suivante afin de configurer un ordinateur hôte, de créer un Azure IoT Hub et d’inscrire votre unité logique Azure IoT Hub.

## <a name="next-steps"></a>Étapes suivantes
[Utiliser une passerelle IoT pour connecter un appareil à Azure IoT Hub](iot-hub-gateway-kit-c-iot-gateway-connect-device-to-cloud.md)


