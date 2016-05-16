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
 ms.date="04/29/2016"
 ms.author="dobett"/>

# Prise en charge de MQTT au niveau d’IoT Hub

Grâce à IoT Hub, les appareils peuvent communiquer avec les points de terminaison d’appareil IoT Hub à l’aide du protocole [MQTT v3.1.1][lnk-mqtt-org] sur le port 8883. IoT Hub nécessite que toutes les communications de périphérique soient sécurisées à l'aide de TLS/SSL.

## Connexion à IoT Hub

Un appareil peut se connecter à un hub IoT en utilisant le protocole MQTT, ainsi que les bibliothèques des [Kits de développement logiciel (SDK) Microsoft Azure IoT][lnk-device-sdks] ou directement le protocole MQTT.

## Utilisation des Kits device client SDK

Les [Kits de développement logiciel (SDK) clients pour les appareils][lnk-device-sdks] qui prennent en charge le protocole MQTT sont disponibles pour Java, Node.js, C et C#. Les Kits device client SDK utilisent la chaîne de connexion IoT Hub standard pour établir une connexion à un hub IoT. Pour utiliser le protocole MQTT, le paramètre de protocole du client doit être défini sur **MQTT**. Par défaut, les Kits de développement logiciel (SDK) clients pour les appareils se connectent à un hub IoT avec l’indicateur **CleanSession** défini sur **0** et utilisent **QoS 1** pour l’échange de messages avec le hub IoT.

Quand un appareil est connecté à un hub IoT, les Kits device client SDK fournissent des méthodes qui permettent à l’appareil d’envoyer des messages et d’en recevoir à partir d’un hub IoT.

Le tableau suivant contient des liens vers des exemples de code pour chaque langage pris en charge et spécifie les paramètres à utiliser pour établir une connexion à IoT Hub à l’aide du protocole MQTT.

| Langage | Paramètre de protocole |
| -------------------------- | ------------------------- |
| [Node.JS][lnk-sample-node] | azure-iot-device-mqtt |
| [Java][lnk-sample-java] | IotHubClientProtocol.MQTT |
| [C][lnk-sample-c] | MQTT\_Protocol |
| [C#][lnk-sample-csharp] | TransportType.Mqtt |

## Utilisation directe du protocole MQTT

Si un appareil ne peut pas utiliser les Kits device client SDK, il peut toujours se connecter aux points de terminaison d’appareil publics à l’aide du protocole MQTT. Dans le paquet **CONNECT**, l’appareil doit utiliser les valeurs suivantes :

- Pour le champ **ClientId**, utilisez le **deviceId**. 
- Dans le champ **Nom d’utilisateur**, utilisez `{iothubhostname}/{device_id}`, où {iothubhostname} est l’enregistrement CName complet du IoT Hub.

    Par exemple, si le nom de votre IoT Hub est **contoso.azure-devices.net** et si le nom de votre appareil est **MyDevice01**, le champ complet **Nom d’utilisateur** doit contenir `contoso.azure-devices.net/MyDevice01`.

- Dans le champ **Mot de passe**, utilisez un jeton SAP. Le format du jeton SAP est identique pour les protocoles HTTP et AMQP : <br/>`SharedAccessSignature sig={signature-string}&se={expiry}&sr={URL-encoded-resourceURI}`.

    Pour plus d’informations sur la génération de jetons SAS, consultez la section consacrée aux appareils dans la rubrique [Utilisation de jetons de sécurité IoT Hub][lnk-sas-tokens].
    
    Lors du test, vous pouvez également utiliser l’outil [Explorateur d’appareils][lnk-device-explorer] pour générer rapidement un jeton SAP à copier et coller dans votre propre code :
    
    1. Accédez à l’onglet **Gestion** de l’Explorateur d’appareils.
    2. Cliquez sur **Jeton SAS** (en haut à droite).
    3. Sur **SASTokenForm**, sélectionnez votre appareil dans la liste déroulante **DeviceID**. Définissez votre **TTL**.
    4. Cliquez sur **Générer** pour créer votre jeton.
    
    Le jeton SAP généré ressemble à ceci : `HostName={your hub name}.azure-devices.net;DeviceId=javadevice;SharedAccessSignature=SharedAccessSignature sr={your hub name}.azure-devices.net%2fdevices%2fMyDevice01&sig=vSgHBMUG.....Ntg%3d&se=1456481802`.

    La partie à utiliser dans le champ **Mot de passe** pour la connexion avec MQTT est : `SharedAccessSignature sr={your hub name}.azure-devices.net%2fdevices%2fyDevice01&sig=vSgHBMUG.....Ntg%3d&se=1456481802g%3d&se=1456481802`.

Pour les paquets de connexion et de déconnexion MQTT, IoT Hub émet un événement sur le canal de la **surveillance des opérations**.

### Envoi de messages à IoT Hub

Après avoir correctement établi la connexion, un appareil peut envoyer des messages à IoT Hub à l’aide de `devices/{device_id}/messages/events/` ou `devices/{device_id}/messages/events/{property_bag}` comme **Nom de la rubrique**. L’élément `{property_bag}` permet à l’appareil d’envoyer des messages avec des propriétés supplémentaires dans un format codé URL. Par exemple :

```
RFC 2396-encoded(<PropertyName1>)=RFC 2396-encoded(<PropertyValue1>)&RFC 2396-encoded(<PropertyName2>)=RFC 2396-encoded(<PropertyValue2>)…
```

> [AZURE.NOTE] Ce codage est le même que celui utilisé pour les chaînes de requête dans le protocole HTTP.

L’application cliente de l’appareil peut également utiliser `devices/{device_id}/messages/events/{property_bag}` comme **nom de canal « Will »** pour définir des *messages « Will »* à transmettre en tant que message de télémétrie.

### Réception de messages

Pour recevoir des messages d’IoT Hub, l’appareil doit s’abonner en utilisant `devices/{device_id}/messages/devicebound/#”` comme **filtre de canal**. IoT Hub remet les messages avec le **nom de canal** `devices/{device_id}/messages/devicebound/` ou `devices/{device_id}/messages/devicebound/{property_bag}` s’il existe des propriétés de message. `{property_bag}` contient des paires clé/valeur codées URL de propriétés de message. Seules les propriétés d’application et les propriétés système définissables par l’utilisateur (comme **messageId** ou **correlationId**) sont incluses dans le jeu de propriétés. Les noms de propriété système ont le préfixe **$**, tandis que les noms de propriété d’application ne sont précédés d’aucun préfixe.

## Étapes suivantes

Pour plus d’informations sur la prise en charge MQTT avec les kits de développement logiciel (SDK) IoT Device, consultez la rubrique [Remarques sur la prise en charge MQTT][lnk-mqtt-devguide] dans le guide du développeur Azure IoT Hub.

Pour en savoir plus sur l’utilisation des Kits device client SDK en vue de communiquer avec IoT Hub, consultez [Prise en main d’Azure IoT Hub][lnk-iot-get-stated].

Pour en savoir plus sur le protocole MQTT, consultez la [documentation de MQTT][lnk-mqtt-docs].

[lnk-device-sdks]: https://github.com/Azure/azure-iot-sdks/blob/master/readme.md
[lnk-mqtt-org]: http://mqtt.org/
[lnk-iot-get-stated]: iot-hub-csharp-csharp-getstarted.md
[lnk-mqtt-docs]: http://mqtt.org/documentation
[lnk-sample-node]: https://github.com/Azure/azure-iot-sdks/blob/develop/node/device/samples/simple_sample_device.js
[lnk-sample-java]: https://github.com/Azure/azure-iot-sdks/blob/develop/java/device/samples/send-receive-sample/src/main/java/samples/com/microsoft/azure/iothub/SendReceive.java
[lnk-sample-c]: https://github.com/Azure/azure-iot-sdks/tree/master/c/iothub_client/samples/iothub_client_sample_mqtt
[lnk-sample-csharp]: https://github.com/Azure/azure-iot-sdks/tree/master/csharp/device/samples
[lnk-device-explorer]: https://github.com/Azure/azure-iot-sdks/blob/master/tools/DeviceExplorer/readme.md
[lnk-sas-tokens]: iot-hub-sas-tokens.md#using-sas-tokens-as-a-device
[lnk-mqtt-devguide]: iot-hub-devguide.md#mqtt-support

<!---HONumber=AcomDC_0504_2016-->