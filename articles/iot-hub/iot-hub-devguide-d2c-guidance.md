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
ms.date: 09/30/2016
ms.author: elioda
translationtype: Human Translation
ms.sourcegitcommit: e223d0613cd48994315451da87e6b7066585bdb6
ms.openlocfilehash: 8c3479e29b55eacc30842ffdfee23b4a00a13126


---
# <a name="device-to-cloud-communications-guidance"></a>Recommandations sur les communications appareil-à-cloud
Lors de l’envoi d’informations de l’application d’appareil au serveur principal de solution, IoT Hub expose trois options :

* les [messages appareil-à-cloud][lnk-d2c] pour la télémétrie et les alertes de série chronologique ;
* les [propriétés signalées][lnk-twins], pour la consignation des informations sur l’état de l’appareil, telles que les fonctionnalités disponibles, les conditions et l’état des flux de travail de longue durée (configuration et mises à jour logicielles) ;
* le [chargement des fichiers][lnk-fileupload], pour les contenus multimédias et les gros traitements télémétriques par lots chargés par des appareils connectés par intermittence ou compressés pour économiser de la bande passante.

Voici une comparaison détaillée des différentes options de communication appareil-à-cloud.

|  | Messages appareil-à-cloud | Propriétés signalées | Chargements de fichiers |
| ---- | ------- | ---------- | ---- |
| Scénario | Séries chronologiques de télémétrie et alertes, par exemple des lots de données de capteur de 256 Ko envoyés toutes les 5 minutes. | Fonctionnalités disponibles et conditions, par exemple le mode de connectivité actuel de l’appareil (réseau cellulaire ou Wi-Fi). Synchronisation des workflows de longue durée, comme les mises à jour logicielles et de la configuration. | Fichiers multimédias. Lots de télémétrie volumineux (généralement compressés). |
| Stockage et récupération | Stockage temporaire par IoT Hub, jusqu’à 7 jours. Lecture uniquement séquentielle. | Stockage par IoT Hub dans la représentation de l’appareil. Récupérables à l’aide du [langage de requête IoT Hub][lnk-query]. | Stockées dans le compte de stockage Azure fourni par l’utilisateur. |
| Taille | Jusqu’à 256 Ko de messages. | La taille maximale de propriétés signalées est de 8 Ko. | Taille maximale de fichier prise en charge par le stockage Blob Azure. |
| Fréquence | Élevée. Pour plus d’informations, référez-vous à [Limites de IoT Hub][lnk-quotas]. | Moyenne. Pour plus d’informations, référez-vous à [Limites de IoT Hub][lnk-quotas]. | Faible. Pour plus d’informations, référez-vous à [Limites de IoT Hub][lnk-quotas]. |
| Protocole | Disponible sur tous les protocoles. | Disponible actuellement uniquement lorsque vous utilisez MQTT. | Disponible sur tous les protocoles, mais nécessite HTTP sur l’appareil. |

> [!NOTE]
> Il est possible qu’une application nécessite l’envoi d’informations en tant que série chronologique de télémétrie ou alerte, et sa mise à disposition sur la représentation d’appareil. Le cas échéant, l’application d’appareil peut envoyer un message appareil-à-cloud et signaler une modification de propriété, ou le serveur principal de solution peut stocker les informations dans les balises de jumeau d’appareil au moment de la réception du message. Dans la mesure où les messages appareil-à-cloud prennent en charge un débit bien plus important que les mises à jour de représentations d’appareil, il est parfois souhaitable d’éviter de mettre à jour la représentation d’appareil lors de chaque message appareil-à-cloud.
> 
> 

[lnk-twins]: iot-hub-devguide-device-twins.md
[lnk-fileupload]: iot-hub-devguide-file-upload.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-d2c]: iot-hub-devguide-messaging.md#device-to-cloud-messages



<!--HONumber=Dec16_HO1-->


