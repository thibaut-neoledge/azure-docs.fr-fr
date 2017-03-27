---
title: "Options appareil-à-cloud d’Azure IoT Hub | Microsoft Docs"
description: "Guide du développeur - Recommandations sur les périodes d’utilisation des messages appareil-à-cloud, des propriétés signalées et du chargement des fichiers pour les communications cloud-à-appareil."
services: iot-hub
documentationcenter: .net
author: fsautomata
manager: timlt
editor: 
ms.assetid: 979136db-c92d-4288-870c-f305e8777bdd
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/09/2017
ms.author: elioda
translationtype: Human Translation
ms.sourcegitcommit: 8a531f70f0d9e173d6ea9fb72b9c997f73c23244
ms.openlocfilehash: aa3704fe844a41fef22b8cdd35838c68aebc7752
ms.lasthandoff: 03/10/2017


---
# <a name="device-to-cloud-communications-guidance"></a>Recommandations sur les communications appareil-à-cloud
Lors de l’envoi d’informations de l’application d’appareil au serveur principal de solution, IoT Hub expose trois options :

* Les [messages appareil-à-cloud][lnk-d2c], pour la télémétrie et les alertes de série chronologique.
* Les [propriétés signalées][lnk-twins], pour la consignation des informations sur l’état de l’appareil, telles que les fonctionnalités disponibles, les conditions ou l’état des flux de travail de longue durée. Par exemple, les mises à jour de configuration et de logiciels.
* Le [chargement des fichiers][lnk-fileupload], pour les contenus multimédias et les gros traitements télémétriques par lots chargés par des appareils connectés par intermittence ou compressés pour économiser de la bande passante.

Voici une comparaison détaillée des différentes options de communication appareil-à-cloud.

|  | Messages Appareil vers cloud | Propriétés signalées | Chargements de fichiers |
| ---- | ------- | ---------- | ---- |
| Scénario | Télémétrie et alertes de série chronologique. Par exemple, les lots de données de capteur de 256 Ko envoyés toutes les 5 minutes. | Capacités et conditions disponibles. Par exemple, le mode de connectivité d’appareil actuel, tel que Cellulaire ou Wi-Fi. Synchronisation des workflows de longue durée, comme les mises à jour logicielles et de la configuration. | Fichiers multimédias. Lots de télémétrie volumineux (généralement compressés). |
| Stockage et récupération | Stockage temporaire par IoT Hub, jusqu’à 7 jours. Lecture uniquement séquentielle. | Stockage par IoT Hub dans la représentation de l’appareil. Récupérables à l’aide du [langage de requête IoT Hub][lnk-query]. | Stockées dans le compte de stockage Azure fourni par l’utilisateur. |
| Taille | Jusqu’à 256 Ko de messages. | La taille maximale de propriétés signalées est de 8 Ko. | Taille maximale de fichier prise en charge par le stockage Blob Azure. |
| Fréquence | Élevée. Pour plus d’informations, consultez les [limites d’IoT Hub][lnk-quotas]. | Moyenne. Pour plus d’informations, consultez les [limites d’IoT Hub][lnk-quotas]. | Faible. Pour plus d’informations, consultez les [limites d’IoT Hub][lnk-quotas]. |
| Protocole | Disponible sur tous les protocoles. | Disponible actuellement uniquement lorsque vous utilisez MQTT. | Disponible sur tous les protocoles, mais nécessite HTTP sur l’appareil. |

Il est possible qu’une application nécessite l’envoi d’informations en tant que série chronologique de télémétrie ou alerte, et sa mise à disposition sur la représentation d’appareil. Dans ce scénario, vous pouvez choisir l’une des options suivantes :

* L’application pour périphérique envoie un message appareil-à-cloud et signale une modification de propriété. 
* Le serveur principal de la solution peut stocker des informations dans les balises du jumeau d’appareil lorsqu’il reçoit le message. 

Dans la mesure où les messages appareil-à-cloud prennent en charge un débit bien plus important que les mises à jour de représentations d’appareil, il est parfois souhaitable d’éviter de mettre à jour la représentation d’appareil lors de chaque message appareil-à-cloud.


[lnk-twins]: iot-hub-devguide-device-twins.md
[lnk-fileupload]: iot-hub-devguide-file-upload.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-d2c]: iot-hub-devguide-messaging.md#device-to-cloud-messages

