---
title: "Utilisation du portail Azure pour configurer le téléchargement du fichier | Microsoft Docs"
description: "Utilisation du portail Azure pour configurer votre IoT Hub en vue d’activer les transferts de fichiers à partir d’appareils connectés. Comprend des informations sur la configuration du compte de stockage Azure de destination."
services: iot-hub
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 915f1597-272d-4fd4-8c5b-a0ccb1df0d91
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/03/2017
ms.author: dobett
ms.translationtype: Human Translation
ms.sourcegitcommit: 6dbb88577733d5ec0dc17acf7243b2ba7b829b38
ms.openlocfilehash: 149dd84d7d8f4ff9cd30f9fc649ced3cb364cfb7
ms.contentlocale: fr-fr
ms.lasthandoff: 07/04/2017

---
# <a name="configure-iot-hub-file-uploads-using-the-azure-portal"></a>Configurer les chargements de fichiers IoT Hub à l’aide du portail Azure

[!INCLUDE [iot-hub-file-upload-selector](../../includes/iot-hub-file-upload-selector.md)]

## <a name="file-upload"></a>Chargement de fichiers

Pour utiliser la [fonctionnalité de chargement de fichiers dans IoT Hub][lnk-upload], vous devez d’abord associer un compte de stockage Azure à votre concentrateur. Sélectionnez **Chargement de fichier** pour afficher une liste de propriétés de chargement de fichiers pour l’IoT Hub en cours de modification.

![Afficher les paramètres de chargement de fichier IoT Hub dans le portail][13]

**Conteneur de stockage**: utilisez le portail Azure pour sélectionner un conteneur d’objets blob dans un compte Azure Storage de votre abonnement Azure actuel à associer à votre IoT Hub. Si nécessaire, vous pouvez créer un compte Azure Storage dans le panneau **Comptes de stockage** et un conteneur d’objets blob dans le panneau **Conteneurs**. IoT Hub génère automatiquement des URI SAS avec des autorisations d’écriture pour ce conteneur d’objets blob pour les appareils à utiliser lorsqu’ils chargent des fichiers.

![Afficher les conteneurs de stockage pour le chargement de fichiers dans le portail][14]

**Recevoir des notifications pour les fichiers chargés**: activez ou désactivez les notifications de chargement de fichiers.

**SAS TTL**: ce paramètre est la durée de vie des URI de signature d’accès partagé renvoyés à l’appareil par IoT Hub. Il est défini par défaut sur une heure, mais il peut être personnalisé avec d’autres valeurs à l’aide du curseur.

**Durée de vie par défaut des paramètres de notification de fichiers**: la durée de vie d’une notification de chargement avant son expiration. Il est défini par défaut sur un jour, mais il peut être personnalisé avec d’autres valeurs à l’aide du curseur.

**Nombre maximal de remises de notifications de fichier**: le nombre de tentatives de remise d’une notification de chargement de fichier par l’IoT Hub. Il est défini par défaut sur 10, mais il peut être personnalisé avec d’autres valeurs à l’aide du curseur.

![Configurer le chargement de fichier IoT Hub dans le portail][15]

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur les fonctionnalités de téléchargement de fichiers d’IoT Hub, consultez [Télécharger des fichiers à partir d’un appareil][lnk-upload] dans le guide du développeur IoT Hub.

Suivez ces liens pour en savoir plus sur la gestion de Azure IoT Hub :

* [Gérer en bloc des appareils IoT][lnk-bulk]
* [Métriques d’IoT Hub][lnk-metrics]
* [Surveillance des opérations][lnk-monitor]

Pour explorer davantage les capacités de IoT Hub, consultez :

* [Guide du développeur IoT Hub][lnk-devguide]
* [Simulation d’un appareil avec IoT Edge][lnk-iotedge]
* [Sécuriser votre solution IoT de bout en bout][lnk-securing]

[13]: ./media/iot-hub-configure-file-upload/file-upload-settings.png
[14]: ./media/iot-hub-configure-file-upload/file-upload-container-selection.png
[15]: ./media/iot-hub-configure-file-upload/file-upload-selected-container.png

[lnk-upload]: iot-hub-devguide-file-upload.md

[lnk-bulk]: iot-hub-bulk-identity-mgmt.md
[lnk-metrics]: iot-hub-metrics.md
[lnk-monitor]: iot-hub-operations-monitoring.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-iotedge]: iot-hub-linux-iot-edge-simulated-device.md
[lnk-securing]: iot-hub-security-ground-up.md

