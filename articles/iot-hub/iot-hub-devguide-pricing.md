---
title: "Présentation des tarifs Azure IoT Hub | Microsoft Docs"
description: "Guide du développeur : informations concernant le fonctionnement des mesures et des tarifs IoT Hub, avec des exemples."
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
ms.translationtype: Human Translation
ms.sourcegitcommit: 5edc47e03ca9319ba2e3285600703d759963e1f3
ms.openlocfilehash: 3470473e1b2aa107c32643a66092b68bfafd1a37
ms.contentlocale: fr-fr
ms.lasthandoff: 05/31/2017


---

# <a name="azure-iot-hub-pricing-information"></a>Informations sur les tarifs Azure IoT Hub

La [tarification d’Azure IoT Hub][lnk-pricing] fournit des informations générales sur les différentes références SKU et les tarifs d’IoT Hub. Cet article contient des informations supplémentaires sur la manière dont les différentes fonctionnalités d’IoT Hub sont mesurées en tant que messages par IoT Hub.

## <a name="charges-per-operation"></a>Frais par opération

| Opération | Informations de facturation | 
| --------- | ------------------- |
| Opérations du registre d’identité <br/> (créer, récupérer, répertorier, mettre à jour, supprimer) | Non facturé. |
| Messages appareil-à-cloud | Les messages envoyés avec succès sont facturés en blocs de 4 Ko lors de l’entrée dans IoT Hub ; par exemple, un message de 6 Ko est facturé 2 messages. |
| Messages cloud-à-appareil | Les messages envoyés avec succès sont facturés en blocs de 4 Ko ; par exemple, un message de 6 Ko est facturé 2 messages. |
| Chargements de fichiers | Le transfert de fichiers vers Stockage Azure n’est pas mesuré par IoT Hub. Les messages de lancement et de complétion du transfert de fichiers sont facturés en tant que messages, par incréments de 4 Ko. Par exemple, le transfert d’un fichier de 10 Mo est facturé deux messages, en plus du coût de Stockage Azure. |
| Méthodes directes | Les demandes réussies de méthodes sont facturées par blocs de 4 Ko, tandis que les réponses comportant des corps non vides sont elles aussi facturées par blocs de 4 Ko, en tant que messages supplémentaires. Les demandes dirigées vers des appareils déconnectés sont facturées en tant que messages, par blocs de 4 Ko. Par exemple, une méthode comportant un corps de 6 Ko entraînant une réponse sans corps de l’appareil est facturée en tant que deux messages ; un message présentant un corps de 6 Ko entraînant une réponse d’1 Ko de l’appareil est facturé en tant que deux messages, plus un autre message pour la réponse. |
| Lectures de jumeaux d’appareil | Les lectures de jumeaux d’appareil de l’appareil et du back-end de la solution sont facturées en tant que messages, par blocs de 512 octets. Par exemple, la lecture d’un jumeau d’appareil de 6 Ko est facturée en tant que 12 messages. |
| Mises à jour de jumeaux d’appareil (balises et propriétés) | Les mises à jour de jumeaux d’appareil de l’appareil sont facturées en tant que messages, par blocs de 512 octets. Par exemple, la lecture d’un jumeau d’appareil de 6 Ko est facturée en tant que 12 messages. |
| Requêtes de jumeaux d’appareil | Les requêtes sont facturées en tant que messages, en fonction de la taille du résultat, par blocs de 512 octets. |
| Opérations de travaux <br/> (créer, mettre à jour, répertorier, supprimer) | Non facturé. |
| Opérations de travaux par appareil | Les opérations de travaux (comme les mises à jour de jumeaux d’appareil et les méthodes) sont facturées normalement. Par exemple, un travail entraînant 1 000 appels de méthode avec des demandes de 1 Ko et des réponses à corps vide est facturé en tant que 1 000 messages. |

> [!NOTE]
> L’ensemble des tailles sont calculées en fonction de la taille en octets de la charge utile (le tramage de protocole est ignoré). En présence de messages (présentant des propriétés et un corps), la taille est calculée sans protocole, tel que décrit dans le [guide du développeur de la messagerie IoT Hub][lnk-message-size].

## <a name="example-1"></a>Exemple 1

Un appareil transmet un message appareil-à-cloud de 1 Ko par minute vers IoT Hub ; ce message est ensuite lu par Azure Stream Analytics. Le back-end de la solution appelle une méthode (avec une charge utile de 512 octets) sur l’appareil toutes les dix minutes, afin de déclencher une action spécifique. L’appareil répond à la méthode, avec un résultat de 200 octets.

L’appareil consomme 1 message * 60 minutes * 24 heures = 1440 messages appareil-à-cloud par jour, et 2 demandes avec réponse * 6 fois par heure * 24 heures = 288 messages pour les méthodes, pour un total de 1728 messages par jour.

## <a name="example-2"></a>Exemple n° 2

Chaque heure, un appareil envoie un message appareil-à-cloud de 100 Ko. Il met également à jour son jumeau d’appareil avec des charges utiles de 1 Ko toutes les 4 heures. Le back-end de la solution, une fois par jour, lit le jumeau d’appareil de 14 Ko et le met à jour avec des charges utiles de 512 octets afin de modifier les configurations.

L’appareil consomme 25 messages appareil-à-cloud (100 Ko/4 Ko) * 24 heures, plus 1 message * 6 fois par jour pour les mises à jour de jumeaux d’appareil, pour un total de 156 messages par jour.
Le back-end de la solution consomme 28 messages (14 Ko/0,5 Ko) pour la lecture du jumeau d’appareil, plus 1 message pour la mise à jour, pour un total de 29 messages.

Au total, l’appareil et le back-end de la solution consomment 185 messages par jour.


[lnk-pricing]: https://azure.microsoft.com/pricing/details/iot-hub
[lnk-message-size]: iot-hub-devguide-messages-construct.md

