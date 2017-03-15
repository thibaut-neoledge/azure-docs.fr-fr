---
title: "Présentation de la tarification d’Azure IoT Hub | Microsoft Docs"
description: "Guide du développeur : informations concernant le fonctionnement des mesures et de la tarification avec IoT Hub, avec des exemples."
services: iot-hub
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 1ac90923-1edf-4134-bbd4-77fee9b68d24
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/12/2016
ms.author: elioda
translationtype: Human Translation
ms.sourcegitcommit: 3598f490752fa275d7c9c5a7d924c315e92f5b33
ms.openlocfilehash: e1f1dc43af2096fb9affc1d12433f091dd28c69c
ms.lasthandoff: 12/13/2016


---

# <a name="azure-iot-hub-pricing-information"></a>Informations sur la tarification Azure IoT Hub

La [tarification d’Azure IoT Hub][lnk-pricing] fournit les informations générales sur les différentes références et la tarification d’IoT Hub. Cet article contient des informations supplémentaires sur la manière dont les différentes fonctionnalités d’IoT Hub sont mesurées en tant que messages par IoT Hub.

## <a name="charges-per-operation"></a>Frais par opération

| Opération | Informations de facturation | 
| --------- | ------------------- |
| Opérations du registre d’identité <br/> (créer, récupérer, répertorier, mettre à jour, supprimer) | Non facturé. |
| Messages Appareil vers cloud | Les messages envoyés avec succès sont facturés en blocs de 4 Ko lors de l’entrée dans IoT Hub ; par exemple, un message de 6 Ko est facturé 2 messages. |
| Messages Cloud vers appareil | Les messages envoyés avec succès sont facturés en blocs de 4 Ko ; par exemple, un message de 6 Ko est facturé 2 messages. |
| Chargements de fichiers | Le transfert de fichiers vers le stockage Azure n’est pas mesuré par IoT Hub. Les messages de lancement et d’exécution du transfert de fichiers sont facturés en tant que messages, par incréments de 4 Ko. Par exemple, le transfert d’un fichier de 10 Mo est facturé 2 messages, en plus du coût du stockage Azure. |
| Méthodes directes | Les requêtes réussies de méthodes sont facturées par blocs de 4 Ko, tandis que les réponses comportant des corps non vides sont elles aussi facturées par blocs de 4 Ko, en tant que messages supplémentaires. Les requêtes dirigées vers des appareils déconnectés sont facturées en tant que messages, par blocs de 4 Ko. Par exemple, une méthode comportant un corps de 6 Ko entraînant une réponse sans corps de l’appareil est facturée en tant que deux messages ; un message présentant un corps de 6 Ko entraînant une réponse d’1 Ko de l’appareil est facturé en tant que deux messages, plus un autre message pour la réponse. |
| Lectures de représentations d’appareil | Les lectures de représentations d’appareil de l’appareil et du serveur principal de la solution sont facturées en tant que messages, par blocs de 512 octets. Par exemple, la lecture d’un appareil de 6 Ko est facturée en tant que 12 messages. |
| Mises à jour de représentations d’appareil (balises et propriétés) | Les mises à jour de représentations d’appareil de l’appareil et du serveur principal sont facturées en tant que messages, par blocs de 512 octets. Par exemple, la lecture d’un appareil de 6 Ko est facturée en tant que 12 messages. |
| Requêtes de représentations d’appareil | Les requêtes sont facturées en tant que messages, en fonction de la taille du résultat, par blocs de 512 octets. |
| Opérations de travaux <br/> (créer, mettre à jour, répertorier, supprimer) | Non facturé. |
| Opérations de travaux par appareil | Une tarification standard s’applique aux opérations de travaux (comme les mises à jour de représentations d’appareil, et les méthodes). Par exemple, un travail entraînant 1000 appels de méthode avec des requêtes d’1 Ko et des réponses à corps vide est facturée en tant que 1000 messages. |

> [!NOTE]
> L’ensemble des tailles sont calculées en fonction de la taille en octets de la charge utile (le tramage de protocole est ignoré). En présence de messages (présentant des propriétés et un corps), la taille est calculée sans protocole, tel que décrit dans le [guide du développeur de la messagerie IoT Hub][lnk-message-size].

## <a name="example-1"></a>Exemple 1

Un appareil transmet un message appareil-à-cloud d’1 Ko par minute vers IoT Hub ; ce message est ensuite lu par Azure Stream Analytics. Le serveur principal de la solution appelle une méthode (avec une charge utile de 512 octets) sur l’appareil toutes les dix minutes, afin de provoquer une action spécifique. L’appareil répond à la méthode, avec un résultat de 200 octets.

L’appareil consomme 1 message * 60 minutes * 24 heures = 1 440 messages appareil-à-cloud par jour, et 2 requêtes avec réponse * 6 fois par heure * 24 heures = 288 messages pour les méthodes, pour un total de 1 728 messages par jour.

## <a name="example-2"></a>Exemple n° 2

Chaque heure, un appareil envoie un message appareil-à-cloud de 100 Ko. Il met également à jour sa représentation d’appareil avec des charges utiles d’1 Ko toutes les 4 heures. Le serveur principal de la solution, une fois par jour, lit la représentation d’appareil de 14 Ko et la met à jour avec des charges utiles de 512 octets afin de modifier les configurations.

L’appareil consomme 25 messages appareil-à-cloud (100 Ko/4 Ko) * 24 heures, plus 1 message * 6 fois par jour pour les mises à jour de représentations d’appareil, pour un total de 156 messages par jour.
Le serveur principal de la solution consomme 28 messages (14 Ko/0,5 Ko) pour la lecture de la représentation d’appareil, plus 1 message pour la mise à jour, pour un total de 29 messages.

Au total, l’appareil et le serveur principal de la solution consomment 185 messages par jour.


[lnk-pricing]: https://azure.microsoft.com/pricing/details/iot-hub
[lnk-message-size]: iot-hub-devguide-messaging.md#message-size

