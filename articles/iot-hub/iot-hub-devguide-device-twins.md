---
title: "Présentation des représentations d’appareil Azure IoT Hub | Microsoft Docs"
description: "Guide du développeur - Utiliser des représentations d’appareil pour synchroniser les données d’état et de configuration entre IoT Hub et vos appareils"
services: iot-hub
documentationcenter: .net
author: fsautomata
manager: timlt
editor: 
ms.assetid: 8a3da072-a5bf-46e5-8de4-24cdbb2a03fa
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/30/2016
ms.author: elioda
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 37b2a82d7f6043224e68219fde753eef73078ffd
ms.openlocfilehash: e0f03adefe1c04b4d7b8cc2dea18f15eb3f05397
ms.lasthandoff: 03/02/2017


---
# <a name="understand-and-use-device-twins-in-iot-hub"></a>Comprendre et utiliser les représentations d’appareil IoT Hub
## <a name="overview"></a>Vue d'ensemble
Les *représentations d’appareil* sont des documents JSON qui stockent des informations sur l’état des appareils (métadonnées, configurations et conditions). IoT Hub conserve une représentation d’appareil pour chaque appareil que vous y connectez. Cet article décrit ce qui suit :

* la structure de la représentation d’appareil : *tags* (balises), propriétés *desired* (souhaitées) et *reported* (signalées)
* les opérations que les applications d’appareil et serveurs principaux peuvent effectuer sur des représentations d’appareil.

> [!NOTE]
> Actuellement, des représentations d’appareil sont accessibles uniquement à partir d’appareils qui se connectent à IoT Hub à l’aide du protocole MQTT. Pour obtenir des instructions sur la conversion d’une application de périphérique existante pour utiliser MQTT, voir [Support MQTT][lnk-devguide-mqtt].
> 
> 

### <a name="when-to-use"></a>Quand utiliser
Vous pouvez utiliser des représentations d’appareil pour répondre aux besoins suivants :

* Stocker dans le cloud des métadonnées spécifiques d’un appareil, telles que l’emplacement de déploiement d’un distributeur automatique.
* Rapporter des informations d’état actuel, telles que les capacités disponibles et les conditions de votre application d’appareil, par exemple, pour un appareil qui se connecte via un réseau cellulaire ou Wi-Fi.
* Synchroniser l’état des flux de travail de longue durée entre une application d’appareil et un serveur principal, par exemple, lorsque le serveur principal de la solution spécifie une nouvelle version de microprogramme à installer et que l’application d’appareil rapporte les différentes étapes du processus de mise à jour.
* Interroger les métadonnées, la configuration ou l’état de vos appareils

En cas de doute entre l’utilisation des propriétés signalées, des messages appareil-à-cloud ou du chargement de fichier, voir [Instructions pour la communication appareil-à-cloud][lnk-d2c-guidance].
En cas de doute entre l’utilisation des propriétés souhaitées des méthodes directes ou des messages cloud-à-appareil, voir [Instructions pour la communication appareil-à-cloud][lnk-c2d-guidance].

## <a name="device-twins"></a>Représentations d’appareil physique
Les représentations d’appareil stockent des informations relatives aux appareils, dont l’utilité est la suivante :

* Elles permettent à un appareil et à un serveur principal de synchroniser les conditions et la configuration de l’appareil.
* Elles permettent à un serveur principal de solution d’interroger et de cibler des opérations de longue durée.

Le cycle de vie d’une représentation d’appareil est lié à l’[identité d’appareil][lnk-identity] correspondante. Des représentations d’appareil sont implicitement créées et supprimées lors de la création ou de la suppression d’une identité d’appareil dans IoT Hub.

Une représentation d’appareil est un document JSON incluant les éléments suivants :

* **Tags** (balises). Document JSON lu et écrit par le serveur principal de solution. Les Tags ne sont pas visibles pour des applications d’appareil.
* **Propriétés souhaitées (Desired)**. Utilisées conjointement avec les propriétés signalées (Reported) pour synchroniser une configuration ou une condition d’appareil. Les propriétés souhaitées peuvent être définies uniquement par le serveur principal de solution, et peuvent être lues par l’application d’appareil. L’application d’appareil peut également être informée en temps réel de changements des propriétés souhaitées.
* **Propriétés signalées (Reported)**. Utilisées conjointement avec les propriétés souhaitées (Desired) pour synchroniser une configuration ou une condition d’appareil. Les propriétés signalées peuvent être définies uniquement par l’application d’appareil, et peuvent être lues et interrogées par le serveur principal de solution.

De plus, la racine de la représentation d’appareil contient les propriétés en lecture seule de l’identité correspondante, telles qu’elles figurent dans le [registre des identités][lnk-identity].

![][img-twin]

Voici un exemple de document JSON de représentation d’appareil :

        {
            "deviceId": "devA",
            "generationId": "123",
            "status": "enabled",
            "statusReason": "provisioned",
            "connectionState": "connected",
            "connectionStateUpdatedTime": "2015-02-28T16:24:48.789Z",
            "lastActivityTime": "2015-02-30T16:24:48.789Z",

            "tags": {
                "$etag": "123",
                "deploymentLocation": {
                    "building": "43",
                    "floor": "1"
                }
            },
            "properties": {
                "desired": {
                    "telemetryConfig": {
                        "sendFrequency": "5m"
                    },
                    "$metadata" : {...},
                    "$version": 1
                },
                "reported": {
                    "telemetryConfig": {
                        "sendFrequency": "5m",
                        "status": "success"
                    }
                    "batteryLevel": 55,
                    "$metadata" : {...},
                    "$version": 4
                }
            }
        }

L’objet racine contient les propriétés système et des objets conteneur pour `tags` et les propriétés `reported` et `desired`. Le conteneur `properties` contient des éléments en lecture seule (`$metadata`, `$etag` et `$version`) qui sont décrits respectivement dans les sections [Métadonnées de représentation d’appareil][lnk-twin-metadata] et [Accès concurrentiel optimiste][lnk-concurrency].

### <a name="reported-property-example"></a>Exemple de propriété signalée (Reported)
Dans l’exemple ci-dessus, la représentation d’appareil contient une propriété `batteryLevel` qui est signalée par l’application d’appareil. Cette propriété permet d’interroger des appareils et d’agir sur ceux-ci en fonction du dernier niveau signalé de charge de la batterie. Un autre exemple pourrait être celui d’une application d’appareil signalant des capacités d’appareil ou des options de connectivité.

Notez comment les propriétés signalées simplifient les scénarios où le serveur principal de solution s’intéresse à la dernière valeur connue d’une propriété. Si le serveur principal de la solution doit traiter une télémétrie d’appareil se présentant sous la forme de séquences d’événements horodatés, telles que des séries chronologiques, utilisez des [messages appareil-à-cloud][lnk-d2c].

### <a name="desired-property-example"></a>Exemple de propriété souhaitée
Dans l’exemple ci-dessus, les propriétés souhaitées et signalées de la représentation d’appareil `telemetryConfig` sont utilisées par le serveur principal d’application et l’application d’appareil pour synchroniser la configuration de la télémétrie pour cet appareil. Par exemple :

1. Le serveur principal de solution définit la propriété souhaitée avec la valeur de configuration souhaitée. Voici la partie du document contenant la propriété souhaitée (Desired) :
   
        ...
        "desired": {
            "telemetryConfig": {
                "sendFrequency": "5m"
            },
            ...
        },
        ...
2. L’application d’appareil est informée de la modification, immédiatement si elle est connectée ou à la première de la reconnexion. L’application d’appareil signale ensuite la configuration mise à jour (ou une condition d’erreur à l’aide de la propriété `status`). Voici la partie contenant les propriétés signalées :
   
        ...
        "reported": {
            "telemetryConfig": {
                "sendFrequency": "5m",
                "status": "success"
            }
            ...
        }
        ...
3. Le serveur principal de solution peut suivre les résultats de l’opération de configuration sur de nombreux appareils en [interrogeant][lnk-query] les représentations d’appareil.

> [!NOTE]
> Les extraits de code ci-dessus sont des exemples, optimisés pour la lisibilité, de manières possibles d’encoder la configuration d’un appareil et son état. L’IoT Hub n’impose pas de schéma spécifique pour les propriétés souhaitées et signalées de représentation d’appareil dans les représentations d’appareil.
> 
> 

Dans de nombreux cas, des représentations sont utilisées pour synchroniser des opérations de longue durée, telles que des mises à jour de microprogramme. Pour plus d’informations sur l’utilisation de propriétés pour synchroniser et suivre des opérations de longue durée sur des appareils, voir [Utiliser des propriétés souhaitées pour configurer des appareils][lnk-twin-properties].

## <a name="back-end-operations"></a>Opérations principales
Le serveur principal de solution opère sur la représentation d’appareil en utilisant les opérations atomiques suivantes, exposées via HTTP :

1. **Récupérer la représentation d’appareil par son id**. Cette opération retourne le contenu du document de la représentation d’appareil, à savoir les Tags (balises) et les propriétés souhaitées (Desired), signalées (Reported) et système.
2. **Mettre à jour Partiellement la représentation d’appareil**. Cette opération permet au serveur principal de solution de mettre à jour partiellement les Tags (balises) ou les propriétés souhaitées (Desired) de la représentation d’appareil. La mise à jour partielle est exprimée sous la forme d’un document JSON qui ajoute ou met à jour toute propriété mentionnée. Les propriétés définies sur `null` sont supprimées. Par exemple, le code suivant crée une propriété souhaitée avec la valeur `{"newProperty": "newValue"}`, remplace la valeur existante de `existingProperty` par `"otherNewValue"`, et supprime complètement `otherOldProperty`. Les autres propriétés souhaitées ou Tags existants ne sont pas modifiés :
   
        {
            "properties": {
                "desired": {
                    "newProperty": {
                        "nestedProperty": "newValue"
                    },
                    "existingProperty": "otherNewValue",
                    "otherOldProperty": null
                }
            }
        }
3. **Remplacer des propriétés souhaitées**. Cette opération permet au serveur principal de la solution de remplacer complètement toutes les propriétés souhaitées existantes, et de remplacer `properties/desired` par un nouveau document JSON.
4. **Remplacer des Tags**. De façon similaire au remplacement de propriétés souhaitées, cette opération permet au serveur principal de la solution de remplacer complètement tous les Tags existants, et de remplacer `tags` par un nouveau document JSON.

Toutes les opérations ci-dessus prennent en charge l’[accès concurrentiel optimiste][lnk-concurrency] et requièrent l’autorisation **ServiceConnect** définie dans l’article [Sécurité][lnk-security].

En plus de ces opérations, le serveur principal de solution peut interroger les représentations d’appareil à l’aide du [langage de requête d’IoT Hub][lnk-query] de type SQL, et effectuer des opérations sur de grands ensembles de représentations en utilisant des [travaux][lnk-jobs].

## <a name="device-operations"></a>Opérations d’appareil
L’application d’appareil opère sur la représentation d’appareil en utilisant les opérations atomiques suivantes :

1. **Récupérer la représentation d’appareil**. Cette opération retourne le contenu du document de la représentation d’appareil, à savoir les Tags (Balises) et les propriétés souhaitées (Desired), signalées (Reported) et système, pour l’appareil actuellement connecté.
2. **Mettre à jour partiellement les propriétés signalées (Reported)**. Cette opération permet la mise à jour partielle des propriétés signalées de l’appareil actuellement connecté. Elle utilise le même format de mise à jour JSON que le serveur principal de solution face à une mise à jour partielle des propriétés souhaitées.
3. **Observer les propriétés souhaitées (Desired)**. L’appareil actuellement connecté peut choisir d’être informé des mises à jour des propriétés souhaitées dès qu’elles se produisent. L’appareil reçoit la forme de mise à jour (remplacement partiel ou complet) exécutée par le serveur principal de la solution.

Toutes les opérations ci-dessus requièrent l’autorisation **DeviceConnect** définie dans l’article [Sécurité][lnk-security].

Les [Kits de développement logiciel (SDK) d’appareils Azure IoT][lnk-sdks] facilitent le recours aux opérations ci-dessus à partir d’un grand nombre de langages et plateformes. Pour plus d’informations sur les détails des primitives d’IoT Hub pour la synchronisation des propriétés souhaitées, voir [Flux de reconnexion d’appareil][lnk-reconnection].

> [!NOTE]
> Actuellement, des représentations d’appareil sont accessibles uniquement à partir d’appareils qui se connectent à IoT Hub à l’aide du protocole MQTT.
> 
> 

## <a name="reference-topics"></a>Rubriques de référence :
Les rubriques de référence suivantes vous fournissent des informations supplémentaires sur le contrôle de l’accès à votre hub IoT.

## <a name="tags-and-properties-format"></a>Format des Tags et propriétés
Les Tags (Balises) ainsi que les propriétés souhaitées (Desired) et signalées (Reported) sont des objets JSON soumis aux restrictions suivantes :

* Toutes les clés dans des objets JSON sont des chaînes UNICODE UTF-8 de 64 octets respectant la casse. Les caractères autorisés excluent les caractères de contrôle UNICODE (segments C0 et C1), ainsi que `'.'`, `' '` et `'$'`.
* Toutes les valeurs figurant dans les objets JSON peuvent être des types JSON suivants : booléen, nombre, chaîne, objet. Les tableaux ne sont pas autorisés.
* Tous les objets JSON dans les balises ainsi que dans les propriétés souhaitées et signalées, peuvent avoir une profondeur maximale de 5. Par exemple, l’objet suivant est valide :

        {
            ...
            "tags": {
                "one": {
                    "two": {
                        "three": {
                            "four": {
                                "five": {
                                    "property": "value"
                                }
                            }
                        }
                    }
                }
            },
            ...
        }

* Aucune valeur de chaîne ne peut avoir une longueur supérieure à 512 octets.

## <a name="device-twin-size"></a>Taille de représentation d’appareil
IoT Hub impose une limite de taille de 8 Ko aux valeurs de `tags`, de `properties/desired` et de `properties/reported`, à l’exception des éléments en lecture seule.
La taille est calculée en comptant tous les caractères à l’exception des caractères de contrôle UNICODE (segments C0 et C1) et de l’espace `' '` lorsqu’il apparaît en dehors d’une constante de chaîne.
IoT Hub rejette en générant une erreur toute opération susceptible d’augmenter la taille de ces documents au-delà de la limite.

## <a name="device-twin-metadata"></a>Métadonnées de représentation d’appareil
IoT Hub tient à jour l’horodateur de la dernière mise à jour de chaque objet JSON dans les propriétés souhaitées et signalées de la représentation d’appareil. Les horodateurs sont exprimés en UTC et codés au format [ISO8601] `YYYY-MM-DDTHH:MM:SS.mmmZ`.
Par exemple :

        {
            ...
            "properties": {
                "desired": {
                    "telemetryConfig": {
                        "sendFrequency": "5m"
                    },
                    "$metadata": {
                        "telemetryConfig": {
                            "sendFrequency": {
                                "$lastUpdated": "2016-03-30T16:24:48.789Z"
                            },
                            "$lastUpdated": "2016-03-30T16:24:48.789Z"
                        },
                        "$lastUpdated": "2016-03-30T16:24:48.789Z"
                    },
                    "$version": 23
                },
                "reported": {
                    "telemetryConfig": {
                        "sendFrequency": "5m",
                        "status": "success"
                    }
                    "batteryLevel": "55%",
                    "$metadata": {
                        "telemetryConfig": {
                            "sendFrequency": "5m",
                            "status": {
                                "$lastUpdated": "2016-03-31T16:35:48.789Z"
                            },
                            "$lastUpdated": "2016-03-31T16:35:48.789Z"
                        }
                        "batteryLevel": {
                            "$lastUpdated": "2016-04-01T16:35:48.789Z"
                        },
                        "$lastUpdated": "2016-04-01T16:24:48.789Z"
                    },
                    "$version": 123
                }
            }
            ...
        }

Ces informations sont conservées à chaque niveau (pas uniquement celui des feuilles de la structure JSON) afin de préserver les mises à jour qui suppriment des clés d’objet.

## <a name="optimistic-concurrency"></a>Accès concurrentiel optimiste
Les Tags ainsi que les propriétés souhaitées (Desired) et signalées (Reported) prennent en charge l’accès concurrentiel optimiste.
Les Tags ont un ETag, conforme à la norme [RFC7232], correspondant à leur représentation JSON. Vous pouvez utiliser celui-ci dans des opérations de mise à jour conditionnelle à partir du serveur principal de solution pour assurer la cohérence.

Les propriétés souhaitées et signalées de la représentation d’appareil n’ont pas d’ETag, mais une valeur `$version` dont la nature incrémentielle est garantie. De façon similaire à un ETag, la version peut être utilisée par la partie effectuant la mise à jour (par exemple, une application d’appareil pour une propriété signalée, ou le serveur principal d’application pour une propriété souhaitée) afin d’assurer la cohérence des mises à jour.

Les versions sont également utiles quand un agent observateur (par exemple, l’application d’appareil observant les propriétés souhaitées) doit concilier des concurrences entre les résultats d’une opération de récupération et d’une notification de mise à jour. Pour plus d’informations, voir [Flux de reconnexion d’appareil][lnk-reconnection].

## <a name="device-reconnection-flow"></a>Flux de reconnexion d’appareil
IoT Hub ne conserve pas les notifications de mise à jour de propriétés souhaitées pour les appareils déconnectés. Il en résulte qu’un appareil qui se connecte doit récupérer le document complet des propriétés souhaitées, en plus de s’abonner aux notifications de mise à jour. Étant donné la possibilité de concurrences entre les notifications de mise à jour et la récupération complète, le flux suivant doit être assuré :

1. L’application d’appareil se connecte à un IoT Hub.
2. L’application d’appareil s’abonne aux notifications de mise à jour des propriétés souhaitées.
3. L’application d’appareil récupère le document complet pour les propriétés souhaitées.

L’application d’appareil peut ignorer toutes les notifications dont la `$version` a une valeur inférieure ou égale à la version du document complet récupéré. Cela est possible, car IoT Hub garantit que les numéros de version sont toujours incrémentiels.

> [!NOTE]
> Cette logique est déjà implémentée dans les [Kits de développement logiciel (SDK) d’appareils Azure IoT][lnk-sdks]. Cette description est utile uniquement lorsque l’application d’appareil ne peut utiliser aucun des Kits de développement logiciel (SDK) d’appareils Azure IoT et que vous devez programmer l’interface MQTT directement.
> 
> 

## <a name="additional-reference-material"></a>Matériel de référence supplémentaire
Les autres rubriques de référence dans le Guide du développeur IoT Hub comprennent :

* La rubrique [Points de terminaison IoT Hub][lnk-endpoints] décrit les différents points de terminaison que chaque IoT Hub expose pour les opérations d’exécution et de gestion.
* La rubrique [Quotas et limitation][lnk-quotas] décrit les quotas appliqués au service IoT Hub, et le comportement de limitation auquel s’attendre en cas d’utilisation du service.
* La section [Azure IoT device et service SDK][lnk-sdks] répertorie les Kits de développement logiciel (SDK) en différents langages que vous pouvez utiliser lors du développement d’applications d’appareil et de service qui interagissent avec IoT Hub.
* La rubrique [Langage de requête d’IoT Hub pour les représentations d’appareil et les travaux][lnk-query] décrit le langage de requête d’IoT Hub permettant de récupérer à partir d’IoT Hub des informations sur des représentations d’appareil et des travaux.
* La rubrique [Prise en charge de MQTT au niveau d’IoT Hub][lnk-devguide-mqtt] fournit des informations supplémentaires sur la prise en charge du protocole MQTT par IoT Hub.

## <a name="next-steps"></a>Étapes suivantes
À présent que vous savez ce que sont des représentations d’appareil, vous serez peut-être intéressé par les rubriques suivantes du Guide du développeur IoT Hub :

* [Appeler une méthode directe sur un appareil][lnk-methods]
* [Planifier des travaux sur plusieurs appareils][lnk-jobs]

Si vous souhaitez tenter de mettre en pratique certains des concepts décrits dans cet article, vous serez peut-être intéressé par les didacticiels IoT Hub suivants :

* [Utilisation d’une représentation d’appareil][lnk-twin-tutorial]
* [Utilisation des propriétés de représentation d’appareil][lnk-twin-properties]

<!-- links and images -->

[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-jobs]: iot-hub-devguide-jobs.md
[lnk-identity]: iot-hub-devguide-identity-registry.md
[lnk-d2c]: iot-hub-devguide-messaging.md#device-to-cloud-messages
[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-security]: iot-hub-devguide-security.md
[lnk-c2d-guidance]: iot-hub-devguide-c2d-guidance.md
[lnk-d2c-guidance]: iot-hub-devguide-d2c-guidance.md

[ISO8601]: https://en.wikipedia.org/wiki/ISO_8601
[RFC7232]: https://tools.ietf.org/html/rfc7232
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md

[lnk-devguide-directmethods]: iot-hub-devguide-direct-methods.md
[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-twin-tutorial]: iot-hub-node-node-twin-getstarted.md
[lnk-twin-properties]: iot-hub-node-node-twin-how-to-configure.md
[lnk-twin-metadata]: iot-hub-devguide-device-twins.md#device-twin-metadata
[lnk-concurrency]: iot-hub-devguide-device-twins.md#optimistic-concurrency
[lnk-reconnection]: iot-hub-devguide-device-twins.md#device-reconnection-flow

[img-twin]: media/iot-hub-devguide-device-twins/twin.png

