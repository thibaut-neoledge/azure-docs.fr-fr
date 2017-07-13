---
title: "Gérer la messagerie cloud à appareil d’Azure IoT Hub avec iothub-explorer | Microsoft Docs"
description: "Découvrez comment utiliser l’outil d’interface de ligne de commande iothub-explorer pour surveiller les messages appareil vers Cloud (D2C) et l’envoi de messages Cloud vers appareil C2D dans Azure IoT Hub."
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: "iothub explorer, messagerie cloud à appareil, iot hub cloud vers appareil, messagerie cloud vers appareil"
ms.assetid: 04521658-35d3-4503-ae48-51d6ad3c62cc
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/27/2017
ms.author: xshi
ms.translationtype: Human Translation
ms.sourcegitcommit: 9ae7e129b381d3034433e29ac1f74cb843cb5aa6
ms.openlocfilehash: a7abb8fb279e134b7f23df779f1c4548a9feb82d
ms.contentlocale: fr-fr
ms.lasthandoff: 05/08/2017

---
<a id="use-iothub-explorer-to-send-and-receive-messages-between-your-device-and-iot-hub" class="xliff"></a>

# Utiliser Iothub-explorer pour envoyer et recevoir des messages entre votre appareil et IoT Hub

![Diagramme de bout en bout](media/iot-hub-get-started-e2e-diagram/2.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

[iothub-explorer](https://github.com/azure/iothub-explorer) dispose d’un certain nombre de commandes qui facilitent la gestion d’IoT Hub. Ce didacticiel se concentre sur l’utilisation d’iothub-explorer pour l’envoi et la réception de messages entre votre appareil et l’instance IoT Hub.

<a id="what-you-will-learn" class="xliff"></a>

## Contenu

Vous apprenez à utiliser iothub-explorer pour surveiller les messages appareil vers cloud et envoyer des messages cloud vers appareil. Il peut s’agir de données de capteur collectées par votre appareil, puis envoyées vers votre instance IoT Hub. Il peut s’agir de commandes envoyées par votre IoT Hub à votre appareil pour faire clignoter un voyant connecté à votre appareil.

<a id="what-you-will-do" class="xliff"></a>

## Procédure à suivre

- Utilisez iothub-explorer pour surveiller les messages appareil vers cloud.
- Utilisez iothub-explorer pour envoyer des messages cloud vers appareil.

<a id="what-you-need" class="xliff"></a>

## Ce dont vous avez besoin

- Le didacticiel [Configurer votre appareil](iot-hub-raspberry-pi-kit-node-get-started.md) terminé, qui traite des exigences suivantes :
  - Un abonnement Azure actif.
  - Une instance Azure IoT Hub associée à votre abonnement.
  - Une application cliente qui envoie des messages à votre instance Azure IoT Hub.
- iothub-explorer. ([Installer iothub-explorer](https://github.com/azure/iothub-explorer))

<a id="monitor-device-to-cloud-messages" class="xliff"></a>

## Analyse de messages appareil vers cloud

Pour analyser les messages envoyés à partir de votre appareil à votre instance IoT H+ub, procédez comme suit :

1. Ouvrez une fenêtre de console.
1. Exécutez la commande suivante :

   ```bash
   iothub-explorer monitor-events <device-id> --login "<IoTHubConnectionString>"
   ```

   > [!Note]
   > Obtenez `<device-id>` et `<IoTHubConnectionString>` à partir de votre instance IoT Hub. Assurez-vous d’avoir terminé le didacticiel précédent. Vous pouvez aussi essayer d’utiliser `iothub-explorer monitor-events <device-id> --login "HostName=<my-hub>.azure-devices.net;SharedAccessKeyName=<my-policy>;SharedAccessKey=<my-policy-key>"` si vous disposez de `HostName`, `SharedAccessKeyName` et `SharedAccessKey`.

<a id="send-cloud-to-device-messages" class="xliff"></a>

## Envoi de messages cloud vers appareil

Pour envoyer un message à partir de votre instance IoT Hub sur votre appareil, procédez comme suit :

1. Ouvrez une fenêtre de console.
1. Démarrez une session sur votre instance IoT Hub en exécutant la commande suivante :

   ```bash
   iothub-explorer login `<IoTHubConnectionString>`
   ```

1. Envoyez un message à votre appareil en exécutant la commande suivante :

   ```bash
   iothub-explorer send <device-id> <message>
   ```

La commande fait clignoter le voyant connecté à votre appareil et envoie le message à ce dernier.

> [!Note]
> L’appareil n’a pas besoin de renvoyer de commande acquitter (ack) à votre instance IoT Hub à la réception du message.

<a id="next-steps" class="xliff"></a>

## Étapes suivantes

Vous avez appris à analyser des messages appareil vers cloud et à envoyer des messages cloud vers appareil entre votre appareil IoT et l’instance IoT Hub.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]

