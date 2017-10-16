---
title: "Solutions Azure pour l’Internet des objets (IoT) | Microsoft Docs"
description: "Une vue d’ensemble d’IoT sur Azure, avec un exemple d’architecture de solution et sa relation avec Azure IoT Hub et les solutions préconfigurées."
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 437d2655-896f-4a9e-a4a8-b864790d3ef8
ms.service: iot-suite
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/24/2017
ms.author: dobett
ms.openlocfilehash: 320190488bb4c7b8192421f9dd50a5264f558584
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
[!INCLUDE [iot-azure-and-iot](../../includes/iot-azure-and-iot.md)]

## <a name="azure-iot-suite"></a>Azure IoT Suite
Microsoft Azure IoT Suite est une solution d’entreprise qui permet une prise en main rapide grâce à un ensemble de solutions préconfigurées extensibles. Ces solutions sont conçues pour couvrir la plupart des scénarios IoT comme la [surveillance à distance][lnk-preconfigured-solutions], la [maintenance prédictive][lnk-predictive-maintenance] et la [fabrique connectée][lnk-connected-factory]. Ces solutions sont des implémentations de l’architecture de solution IoT décrite dans cet article.

Les solutions préconfigurées sont des solutions complètes fonctionnant de bout en bout qui incluent :

- des simulations d’appareils pour faciliter la prise en main ;
- Les services Azure préconfigurés tels que [Azure IoT Hub][Azure IoT Hub], [Azure Event Hubs][Azure Event Hubs], [Azure Stream Analytics][Azure Stream Analytics], [Azure Machine Learning][Azure Machine Learning] et [Stockage Azure][Azure storage].
- des consoles de gestion de solutions spécifiques.

Les solutions préconfigurées contiennent du code éprouvé, prêt pour la production, que vous pouvez personnaliser et étendre pour implémenter vos propres scénarios IoT spécifiques.

Vous pouvez également être intéressé par le service [Azure IoT Hub][Azure IoT Hub], utilisé par un grand nombre de solutions préconfigurées. [Azure IoT Hub][Azure IoT Hub] établit une communication bidirectionnelle sécurisée et fiable entre les appareils et le cloud utilisé dans l’architecture de solution préconfigurée.

## <a name="next-steps"></a>Étapes suivantes
Explorez les ressources ci-après pour en savoir plus sur IoT Suite et sur les solutions préconfigurées :

* [Qu’est-ce qu’Azure IoT Suite ?][lnk-whatissuite]
* [Que sont les solutions préconfigurées Azure IoT Suite ?][lnk-whatarepreconfigured]

[lnk-whatissuite]: iot-suite-overview.md
[lnk-whatarepreconfigured]: iot-suite-what-are-preconfigured-solutions.md

[lnk-preconfigured-solutions]: iot-suite-getstarted-preconfigured-solutions.md
[Azure IoT Hub]: https://azure.microsoft.com/documentation/services/iot-hub/
[Azure Event Hubs]: https://azure.microsoft.com/documentation/services/event-hubs/
[Azure Stream Analytics]: https://azure.microsoft.com/documentation/services/stream-analytics/
[Azure Machine Learning]: https://azure.microsoft.com/documentation/services/machine-learning/
[Azure storage]: https://azure.microsoft.com/documentation/services/storage/
[lnk-predictive-maintenance]: iot-suite-predictive-overview.md
[lnk-connected-factory]: iot-suite-connected-factory-overview.md