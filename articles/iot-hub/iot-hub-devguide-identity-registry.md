---
title: "Comprendre le registre des identités d’Azure IoT Hub | Microsoft Docs"
description: "Guide du développeur - Description du registre des identités IoT Hub et de la manière de l’utiliser pour gérer vos appareils. Contient des informations sur l’importation et l’exportation d’identités d’appareils en bloc."
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 0706eccd-e84c-4ae7-bbd4-2b1a22241147
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/04/2017
ms.author: dobett
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 5e6ffbb8f1373f7170f87ad0e345a63cc20f08dd
ms.openlocfilehash: 75a2fa16a7e33cf85746538e120ca90a389b05c5
ms.lasthandoff: 03/24/2017


---
# <a name="understand-identity-registry-in-your-iot-hub"></a>Comprendre le registre des identités dans votre IoT Hub

## <a name="overview"></a>Vue d'ensemble

Chaque IoT Hub a un registre des identités contenant des informations sur les appareils autorisés à se connecter au IoT Hub. Pour qu’un appareil puisse se connecter à un Hub, une entrée correspondant à cet appareil doit figurer dans le registre des identités de l’IoT Hub. Un appareil doit également s’authentifier auprès de l’IoT Hub à l’aide des informations d’identification stockées dans le registre des identités.

À un niveau supérieur, le registre des identités est une collection compatible REST de ressources d’identité d’appareil. Lorsque vous ajoutez une entrée au registre des identités, IoT Hub crée un jeu de ressources par appareil dans le service, comme une file d’attente contenant des messages cloud vers appareil en transit.

### <a name="when-to-use"></a>Quand utiliser

Utilisez le registre des identités lorsque vous avez besoin de configurer des appareils qui se connectent à votre IoT Hub, et devez contrôler l’accès par appareil aux points de terminaison côté appareil de votre hub.

> [!NOTE]
> Le registre des identités ne contient pas de métadonnées spécifiques de l’application.

## <a name="identity-registry-operations"></a>Opérations du registre d’identité

Le registre des identités IoT Hub expose les opérations suivantes :

* Création d’une identité d’appareil
* Mise à jour d’une identité d’appareil
* Récupération d’une identité d’appareil par ID
* Suppression d’une identité d’appareil
* Création de listes contenant jusqu’à 1 000 identités
* Exportation de toutes les identités vers le stockage Blob Azure
* Importation de toutes les identités depuis le stockage Blob Azure

Toutes ces opérations peuvent utiliser un accès concurrentiel optimiste, comme spécifié dans [RFC7232][lnk-rfc7232].

> [!IMPORTANT]
> La seule façon de récupérer toutes les identités dans une registre des identités d’IoT Hub consiste à utiliser la fonctionnalité [Exporter][lnk-export].

Un registre des identités IoT Hub :

* ne contient pas de métadonnées de l’application ;
* est accessible en tant que dictionnaire à l’aide de la clé **deviceId** .
* ne prend pas en charge les requêtes expressives.

Une solution IoT possède généralement une zone de stockage distincte spécifique à la solution qui contient les métadonnées propres à l’application. Dans une solution de développement intelligente, par exemple, la zone de stockage spécifique à la solution doit enregistrer l’espace dans lequel un capteur de température sera déployé.

> [!IMPORTANT]
> Utilisez le registre des identités uniquement pour les opérations de gestion et d’approvisionnement. Les opérations à haut débit ne doivent pas dépendre de l’exécution d’opérations dans le registre des identités au moment de leur exécution. Par exemple, la vérification de l’état de la connexion d’un appareil avant l’envoi d’une commande n’est pas un modèle pris en charge. Veillez à vérifier les [taux de limitation][lnk-quotas] pour le registre des identités et le modèle de [pulsation de l’appareil][lnk-guidance-heartbeat].

## <a name="disable-devices"></a>Désactivation d’appareils

Vous pouvez désactiver les appareils en mettant à jour la propriété **status** d’une identité dans le registre des identités. Généralement, cette propriété est utilisée dans deux scénarios :

* Au cours d’un processus d’orchestration d’approvisionnement. Pour plus d’informations, voir [Approvisionnement des appareils][lnk-guidance-provisioning].
* Si, pour une raison quelconque, vous pensez qu’un appareil est compromis ou non autorisé.

## <a name="import-and-export-device-identities"></a>Importer et exporter les identités des appareils

Vous pouvez exporter des identités d’appareils en bloc à partir du registre des identités d’un IoT Hub, par le biais d’opérations asynchrones sur le [point de terminaison d’un fournisseur de ressources IoT Hub][lnk-endpoints]. Les exportations sont des tâches à long terme qui utilisent un conteneur d’objets blob fourni par le client pour enregistrer les données relatives à l’identité des appareils lues dans le registre des identités.

Vous pouvez importer des identités d’appareils en bloc dans le registre des identités d’un IoT Hub, par le biais d’opérations asynchrones sur le [point de terminaison d’un fournisseur de ressources IoT Hub][lnk-endpoints]. Les importations sont des tâches à long terme qui utilisent des données dans un conteneur d’objets blob, fourni par le client, pour écrire les données relatives à l’identité des appareils dans le registre des identités.

* Pour plus d’informations sur l’importation et l’exportation d’API, voir [IoT Hub - API REST de fournisseur de ressources][lnk-resource-provider-apis].
* Pour en savoir plus sur l’exécution de tâches d’importation et d’exportation, voir [Gestion en bloc des identités d’appareils IoT Hub][lnk-bulk-identity].

## <a name="device-provisioning"></a>Approvisionnement des appareils

Les données d’appareil qu’une solution IoT donnée stocke dépendent des exigences spécifiques de cette solution. Mais une solution doit au minimum stocker les clés d’authentification et les identités des appareils. Azure IoT Hub inclut un registre d’identité qui peut stocker des valeurs pour chaque appareil, comme les ID, les clés d’authentification et les codes d’état. Une solution peut utiliser d’autres services Azure, tels que le stockage sur table Azure, le stockage Blob Azure ou Azure DocumentDB pour stocker des données supplémentaires sur les appareils.

*Approvisionnement des appareils* est le processus d'ajout des données d'appareil initial aux magasins dans votre solution. Pour permettre à un nouvel appareil de se connecter à votre hub, vous devez ajouter un ID et des clés d’appareil au registre des identités d’IoT Hub. Dans le cadre du processus d’approvisionnement, vous devrez peut-être initialiser les données spécifiques à l’appareil dans d’autres magasins de la solution.

## <a name="device-heartbeat"></a>Pulsation des appareils

Le registre des identités IoT Hub contient un champ appelé **connectionState**. Vous ne devez utiliser le champ **connectionState** que pendant le développement et le débogage. Les solutions IoT ne doivent pas interroger le champ au moment de l’exécution (par exemple, pour vérifier si un appareil est connecté pour décider de l’envoi d’un message cloud-à-appareil ou d’un SMS).

Si votre solution IoT a besoin de savoir si un appareil est connecté (au moment de l’exécution ou sur la base d’informations plus précises que celles fournies par la propriété **connectionState**), vous devez implémenter le *modèle par pulsations*.

Dans le modèle par pulsations, l’appareil envoie des messages appareil-à-cloud au moins une fois par durée fixe (par exemple, au moins une fois par heure). Ainsi, même si un appareil n’a pas de données à envoyer, il envoie toujours un message appareil-à-cloud vide (généralement avec une propriété qui l’identifie comme pulsation). Côté service, la solution gère un mappage avec la dernière pulsation reçue pour chaque appareil. Elle suppose qu’un problème affecte un appareil si elle ne reçoit pas un message de pulsation dans le temps imparti.

Une implémentation plus complexe pourrait inclure les informations de la [surveillance des opérations][lnk-devguide-opmon] pour identifier les appareils qui ne parviennent pas à se connecter ou à communiquer. Quand vous implémentez le modèle par pulsations, veillez à vérifier les [quotas et limitations IoT Hub][lnk-quotas].

> [!NOTE]
> Si une solution IoT a uniquement besoin de l’état de la connexion de l’appareil pour déterminer si elle doit envoyer des messages cloud-à-appareil, et que ces messages ne sont pas diffusés à de larges groupes d’appareils, une solution plus simple peut être de définir un délai d’expiration court. Ce modèle permet d’obtenir le même résultat qu’en maintenant l’état de la connexion de l’appareil avec sa pulsation, tout en étant plus efficace. Il est également possible, en demandant des accusés de réception des messages, d’être informé par IoT Hub des appareils qui peuvent recevoir des messages et de ceux qui ne sont pas en ligne ou qui sont en état d’échec.

## <a name="reference-topics"></a>Rubriques de référence :

Les rubriques de référence suivantes fournissent des informations supplémentaires sur le registre des identités.

## <a name="device-identity-properties"></a>Propriétés d’identité des appareils

Les identités des appareils sont représentées sous forme de documents JSON avec les propriétés suivantes :

| Propriété | Options | Description |
| --- | --- | --- |
| deviceId |obligatoire, en lecture seule sur les mises à jour |Une chaîne qui respecte la casse (jusqu’à 128 caractères) de caractères alphanumériques 7 bits ASCII + `{'-', ':', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '$', '''}`. |
| generationId |obligatoire, en lecture seule |Une chaîne qui respecte la casse, générée par IoT Hub, d’une longueur maximale de 128 caractères. Cette valeur permet de distinguer les appareils dotés du même **deviceId**lorsqu’ils ont été supprimés et recréés. |
| etag |obligatoire, en lecture seule |Une chaîne représentant un ETag faible pour l’identité d’appareil, conformément à [RFC7232][lnk-rfc7232]. |
| auth |facultatif |Un objet composite contenant des informations d’authentification et des éléments de sécurité. |
| auth.symkey |facultatif |Un objet composite contenant une clé primaire et une clé secondaire, stockées au format base64. |
| status |required |Un indicateur d’accès. Peut être **Activé** ou **Désactivé**. Si la propriété est définie sur **Activé**, l’appareil est autorisé à se connecter. Si la propriété est définie sur **Désactivé**, cet appareil ne peut pas accéder à un point de terminaison de l’appareil. |
| statusReason |facultatif |Une chaîne de 128 caractères qui stocke le motif de l’état de l’identité de l’appareil. Tous les caractères UTF-8 sont autorisés. |
| statusUpdateTime |en lecture seule |Un indicateur temporel, indiquant la date et l’heure de la dernière mise à jour de l’état. |
| connectionState |en lecture seule |Un champ indiquant l’état de la connexion : **Connecté** ou **Déconnecté**. Ce champ représente la vue IoT Hub de l’état de connexion de l’appareil. **Important** : ce champ doit être utilisé uniquement à des fins de développement et de débogage. L’état de la connexion est mis à jour uniquement pour les appareils utilisant les protocoles AMQP ou MQTT. Cet état est basé sur les pings au niveau du protocole (tests ping MQTT ou AMQP) et peut avoir un délai maximum de 5 minutes seulement. Pour ces raisons, de faux positifs peuvent survenir. Par exemple : un appareil peut être signalé comme étant connecté, alors qu’il est déconnecté. |
| connectionStateUpdatedTime |en lecture seule |Un indicateur temporel, indiquant la date et la dernière heure de mise à jour de l’état de la connexion. |
| lastActivityTime |en lecture seule |Un indicateur temporel, indiquant la date et la dernière heure de connexion de l’appareil, de réception d’un message ou d’envoi d’un message. |

> [!NOTE]
> L’état de la connexion peut uniquement représenter la vue IoT Hub de l’état de la connexion. Les mises à jour à cet état peuvent être différées en fonction des conditions et des configurations du réseau.

## <a name="additional-reference-material"></a>Matériel de référence supplémentaire

Les autres rubriques de référence dans le Guide du développeur IoT Hub comprennent :

* La rubrique [Points de terminaison IoT Hub][lnk-endpoints] décrit les différents points de terminaison que chaque IoT Hub expose pour les opérations d’exécution et de gestion.
* La rubrique [Quotas et limitation][lnk-quotas] décrit les quotas appliqués au service IoT Hub, et le comportement de limitation auquel s’attendre en cas d’utilisation du service.
* La section [Azure IoT device et service SDK][lnk-sdks] répertorie les Kits de développement logiciel (SDK) en différents langages que vous pouvez utiliser pour le développement d’applications d’appareil et de service qui interagissent avec IoT Hub.
* La rubrique [Langage de requête d’IoT Hub pour les jumeaux d’appareil et les travaux][lnk-query] décrit le langage de requête d’IoT Hub permettant de récupérer à partir d’IoT Hub des informations sur des jumeaux d’appareil et des travaux.
* La rubrique [Prise en charge de MQTT au niveau d’IoT Hub][lnk-devguide-mqtt] fournit des informations supplémentaires sur la prise en charge du protocole MQTT par IoT Hub.

## <a name="next-steps"></a>Étapes suivantes

À présent que vous savez comment utiliser le registre des identités IoT Hub, vous serez peut-être intéressé par les rubriques suivantes du Guide du développeur Iot Hub :

* [Contrôler l’accès à IoT Hub][lnk-devguide-security]
* [Utiliser des représentations d’appareil pour synchroniser les données d’état et de configuration][lnk-devguide-device-twins]
* [Appeler une méthode directe sur un appareil][lnk-devguide-directmethods]
* [Planifier des travaux sur plusieurs appareils][lnk-devguide-jobs]

Si vous souhaitez tenter de mettre en pratique certains des concepts décrits dans cet article, vous serez peut-être intéressé par les didacticiels IoT Hub suivants :

* [Mise en route d’Azure IoT Hub][lnk-getstarted-tutorial]

<!-- Links and images -->

[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
[lnk-resource-provider-apis]: https://docs.microsoft.com/rest/api/iothub/iothubresource
[lnk-guidance-provisioning]: iot-hub-devguide-identity-registry.md#device-provisioning
[lnk-guidance-heartbeat]: iot-hub-devguide-identity-registry.md#device-heartbeat
[lnk-rfc7232]: https://tools.ietf.org/html/rfc7232
[lnk-bulk-identity]: iot-hub-bulk-identity-mgmt.md
[lnk-export]: iot-hub-devguide-identity-registry.md#import-and-export-device-identities
[lnk-devguide-opmon]: iot-hub-operations-monitoring.md

[lnk-devguide-security]: iot-hub-devguide-security.md
[lnk-devguide-device-twins]: iot-hub-devguide-device-twins.md
[lnk-devguide-directmethods]: iot-hub-devguide-direct-methods.md
[lnk-devguide-jobs]: iot-hub-devguide-jobs.md

[lnk-getstarted-tutorial]: iot-hub-csharp-csharp-getstarted.md

