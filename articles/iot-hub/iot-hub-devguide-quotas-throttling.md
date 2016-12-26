---
title: "Guide du développeur - Quotas et limitation | Microsoft Docs"
description: "Guide du développeur Azure IoT Hub - description des quotas qui s’appliquent à IoT Hub et comportement de limitation attendu"
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 425e1b08-8789-4377-85f7-c13131fae4ce
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/30/2016
ms.author: dobett
translationtype: Human Translation
ms.sourcegitcommit: c18a1b16cb561edabd69f17ecebedf686732ac34
ms.openlocfilehash: c0f8c779d7f9552dc05ac3791b74c3d57cb1fe64


---
# <a name="reference---quotas-and-throttling"></a>Référence - Quotas et limitation
## <a name="quotas-and-throttling"></a>Quotas et limitation
Chaque abonnement Azure peut avoir au maximum 10 IoT Hubs, et au maximum un hub gratuit.

Chaque IoT Hub est configuré avec un certain nombre d’unités dans une référence SKU spécifique (pour plus d’informations, consultez [Tarification Azure IoT Hub][lnk-pricing]). La référence et le nombre d’unités déterminent le quota quotidien maximal de messages que vous pouvez envoyer.

La référence détermine également le seuil de limitation qu’IoT Hub applique sur les opérations.

## <a name="operation-throttles"></a>Limitations d’opérations
Les limitations d’opération sont les limites de taux qui sont appliquées dans les plages de minutes et sont destinées à éviter les abus. IoT Hub essaie d’éviter le renvoi d’erreurs chaque fois que c’est possible, mais les exceptions commencent à être renvoyées si la limitation est dépassée pendant trop longtemps.

Vous trouverez ci-dessous la liste des limitations appliquées. Les valeurs font référence à un hub individuel.

| Limitation | Hubs gratuits et S1 | Hubs S2 | Hubs S3 | 
| -------- | ------- | ------- | ------- |
| Opérations de registre des identités (création, récupération, création de listes, mise à jour, suppression) | 100/min/unité | 100/min/unité | 5 000/min/unité |
| Connexions d’appareils | 100/s ou 12/s/unité maximum <br/> Par exemple, deux unités S1 équivalent à 2\*12 = 24/s, mais vous obtenez au moins 100/s sur vos unités. Avec neuf unités S1, vous obtenez 108/sec (9\*12) sur vos unités. | 120/s/unité | 6 000/s/unité |
| Envois appareil-à-cloud | 100/s ou 12/s/unité maximum <br/> Par exemple, deux unités S1 équivalent à 2\*12 = 24/s, mais vous obtenez au moins 100/s sur vos unités. Avec neuf unités S1, vous obtenez 108/sec (9\*12) sur vos unités. | 120/s/unité | 6 000/s/unité |
| Envois cloud-à-appareil | 100/min/unité | 100/min/unité | 5 000/min/unité |
| Réceptions cloud-à-appareil <br/> (uniquement lorsque les appareils utilisent HTTP)| 1 000/min/unité | 1 000/min/unité| 50 000/min/unité |
| Chargement de fichiers | 100 notifications de chargement de fichiers/min/unité | 100 notifications de chargement de fichiers/min/unité | 5 000 notifications de chargement de fichiers/min/unité |
| Méthodes directes | 10/s/unité | 30/s/unité | 1 500/s/unité | 
| Lectures de représentations | 10/s | 10/s ou 1/s/unité maximum | 50/s/unité |
| Mises à jour de représentations | 10/s | 10/s ou 1/s/unité maximum | 50/s/unité |
| Opérations de travaux <br/> (créer, mettre à jour, répertorier, supprimer) | 100/min/unité | 100/min/unité | 5 000/min/unité |
| Débit d’opérations de travaux par appareil | 10/s | 10/s ou 1/s/unité maximum | 50/s/unité |

Il est important de préciser que la limitation des *connexions d’appareil* régit la fréquence à laquelle les nouvelles connexions d’appareil peuvent être établies avec un IoT Hub et pas le nombre maximal d’appareils connectés simultanément. La limitation dépend du nombre d’unités configurées pour l’IoT Hub.

Par exemple, si vous achetez une seule unité S1, vous obtenez une limitation de 100 connexions par seconde. Cela signifie que pour connecter 100 000 appareils, au moins 1 000 secondes sont nécessaires (environ 16 minutes). Toutefois, vous pouvez avoir autant d’appareils connectés simultanément que d’appareils enregistrés dans le registre des identités.

Le billet de blog [IoT Hub throttling and you][lnk-throttle-blog] (Limitation d’IoT Hub et vous) fournit une présentation détaillée du comportement de limitation d’IoT Hub.

> [!NOTE]
> À tout moment, il est possible d’augmenter les quotas ou les limites en augmentant le nombre d’unités approvisionnées dans un hub IoT.
> 
> [!IMPORTANT]
> Les opérations de registre des identités sont prévues pour une utilisation au moment de l’exécution dans les scénarios de gestion et d’approvisionnement des appareils. La lecture ou la mise à jour d’un grand nombre d’identités d’appareils est prise en charge par le biais des [travaux d’importation et d’exportation][lnk-importexport].
> 
> 

## <a name="other-limits"></a>Autres limites

IoT Hub applique d’autres limites à ses différentes fonctionnalités.

| Opération | Limite |
| --------- | ----- |
| URI de chargement de fichiers | 10 000 URI de SAP peuvent être générés à la fois pour un compte de stockage. <br/>  10 URI de signature d’accès partagé/appareil peuvent être générés à la fois. |
| Travaux | L’historique des travaux est conservé pendant 30 jours maximum. <br/> Le nombre maximal de travaux simultanés est 1 (pour les niveaux gratuit et S1), 5 (pour S2) ou 10 (pour S3). |

## <a name="next-steps"></a>Étapes suivantes
Les autres rubriques de référence dans le Guide du développeur IoT Hub comprennent :

* [Points de terminaison IoT Hub][lnk-devguide-endpoints]
* [Langage de requête d’IoT Hub pour les représentations d’appareil et les travaux][lnk-devguide-query]
* [Prise en charge de MQTT au niveau d’IoT Hub][lnk-devguide-mqtt]

[lnk-pricing]: https://azure.microsoft.com/pricing/details/iot-hub
[lnk-throttle-blog]: https://azure.microsoft.com/blog/iot-hub-throttling-and-you/
[lnk-importexport]: iot-hub-devguide-identity-registry.md#import-and-export-device-identities

[lnk-devguide-endpoints]: iot-hub-devguide-endpoints.md
[lnk-devguide-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md



<!--HONumber=Nov16_HO5-->


