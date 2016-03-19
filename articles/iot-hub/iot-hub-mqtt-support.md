<properties
 pageTitle="Prise en charge de MQTT au niveau d’IoT Hub | Microsoft Azure"
 description="Description de la prise en charge de MQTT au niveau d’IoT Hub"
 services="iot-hub"
 documentationCenter=".net"
 authors="dominicbetts"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="multiple"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="02/03/2016"
 ms.author="dobett"/>

# Prise en charge de MQTT au niveau d’IoT Hub

Grâce à IoT Hub, les appareils peuvent communiquer avec les points de terminaison d’appareil IoT Hub à l’aide du protocole [MQTT v3.1.1][lnk-mqtt-org] sur le port 8883. IoT Hub nécessite que toutes les communications de périphérique soient sécurisées à l'aide de TLS/SSL.

## Connexion à IoT Hub

Un appareil peut se connecter à un hub IoT en utilisant le protocole MQTT, ainsi que les bibliothèques des [Kits de développement logiciel (SDK) Microsoft Azure IoT][lnk-device-sdks] ou directement le protocole MQTT.

## Utilisation des Kits de développement logiciel (SDK) clients pour les appareils

Les [Kits de développement logiciel (SDK) clients pour les appareils][lnk-device-sdks] qui prennent en charge le protocole MQTT sont disponibles pour Java, Node.js, C et C#. Les Kits de développement logiciel (SDK) clients pour les appareils utilisent la chaîne de connexion IoT Hub standard pour établir une connexion à un hub IoT. Pour utiliser le protocole MQTT, le paramètre de protocole du client doit être défini sur **MQTT**. Par défaut, les Kits de développement logiciel (SDK) clients pour les appareils se connectent à un hub IoT avec l’indicateur **CleanSession** défini sur **0** et utilisent **QoS 1** pour l’échange de messages avec le hub IoT.

Quand un appareil est connecté à un hub IoT, les Kits de développement logiciel (SDK) clients pour les appareils fournissent des méthodes qui permettent à l’appareil d’envoyer des messages et d’en recevoir à partir d’un hub IoT.

Le tableau suivant contient des liens vers des exemples de code pour chaque langage pris en charge et spécifie les paramètres à utiliser pour établir une connexion à IoT Hub à l’aide du protocole MQTT.

| Langage | Paramètre de protocole |
| -------------------------- | ------------------------- |
| [Node.JS][lnk-sample-node] | azure-iot-device-mqtt |
| [Java][lnk-sample-java] | IotHubClientProtocol.MQTT |
| [C][lnk-sample-c] | MQTT\_Protocol |
| [C#][lnk-sample-csharp] | TransportType.Mqtt |

## Utilisation directe du protocole MQTT

Si un appareil ne peut pas utiliser les Kits de développement logiciel (SDK) clients pour les appareils, il peut toujours se connecter aux points de terminaison d’appareil publics à l’aide du protocole MQTT. Dans le paquet **CONNECT**, l’appareil doit utiliser les valeurs suivantes :

- **deviceId** (ID de l’appareil) en guise de **ClientId** (ID du client)
- `{iothubhostname}/{device_id}` dans le champ **nom d’utilisateur**, où {iothubhostname} est l’enregistrement CName complet du hub IoT (par exemple, contoso.azure-devices.net).
- Un jeton SAP dans le champ **Password**. Le [format du jeton SAP][lnk-iothub-security] est identique à celui décrit pour les protocoles HTTP et AMQP :<br/>`SharedAccessSignature sig={signature-string}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}`.

Pour les paquets de connexion et de déconnexion MQTT, IoT Hub émet un événement sur le canal de la **surveillance des opérations**.

### Envoi de messages à IoT Hub

Après avoir correctement établi une connexion, un appareil peut envoyer des messages à IoT Hub à l’aide de `devices/{did}/messages/events/` ou `devices/{did}/messages/events/{property_bag}` comme **nom de canal (topic)**. L’élément `{property_bag}` permet à l’appareil d’envoyer des messages avec des propriétés supplémentaires dans un format codé URL. Par exemple :

```
RFC 2396-encoded(<PropertyName1>)=RFC 2396-encoded(<PropertyValue1>)&RFC 2396-encoded(<PropertyName2>)=RFC 2396-encoded(<PropertyValue2>)…
```
 
> [AZURE.NOTE] Ce codage est le même que celui utilisé pour les chaînes de requête dans le protocole HTTP.

L’application cliente de l’appareil peut également utiliser `devices/{did}/messages/events/{property_bag}` comme **nom de canal « Will »** pour définir des *messages « Will »* à transmettre en tant que message de télémétrie.

### Réception de messages

Pour recevoir des messages d’IoT Hub, un appareil doit s’abonner en utilisant `devices/{did}/messages/devicebound/#”` comme **filtre de canal**. IoT Hub remet les messages avec le **nom de canal** `devices/{did}/messages/devicebound/` ou `devices/{did}/messages/devicebound/{property_bag}` s’il existe des propriétés de message. `{property_bag}` contient des paires clé/valeur codées URL de propriétés de message. Seules les propriétés d’application et les propriétés système définissables par l’utilisateur (comme **messageId** ou **correlationId**) sont incluses dans le jeu de propriétés. Les noms de propriété système ont le préfixe **$**, tandis que les noms de propriété d’application ne sont précédés d’aucun préfixe.

## Étapes suivantes

Pour en savoir plus sur l’utilisation des Kits de développement logiciel (SDK) clients pour les appareils afin de communiquer avec IoT Hub, consultez [Prise en main d’Azure IoT Hub][lnk-iot-get-stated].

Pour en savoir plus sur le protocole MQTT, consultez la [documentation de MQTT][lnk-mqtt-docs].

[lnk-device-sdks]: https://github.com/Azure/azure-iot-sdks/blob/master/readme.md
[lnk-mqtt-org]: http://mqtt.org/
[lnk-iot-get-stated]: iot-hub-csharp-csharp-getstarted.md
[lnk-mqtt-docs]: http://mqtt.org/documentation
[lnk-iothub-security]: iot-hub-devguide.md#security
[lnk-sample-node]: https://github.com/Azure/azure-iot-sdks/blob/develop/node/device/samples/simple_sample_device.js
[lnk-sample-java]: https://github.com/Azure/azure-iot-sdks/blob/develop/java/device/samples/send-receive-sample/src/main/java/samples/com/microsoft/azure/iothub/SendReceive.java
[lnk-sample-c]: https://github.com/Azure/azure-iot-sdks/tree/master/c/iothub_client/samples/iothub_client_sample_mqtt
[lnk-sample-csharp]: https://github.com/Azure/azure-iot-sdks/tree/master/csharp/device/samples

<!---HONumber=AcomDC_0218_2016-->