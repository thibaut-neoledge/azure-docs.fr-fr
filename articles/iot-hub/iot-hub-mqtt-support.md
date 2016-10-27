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
 ms.date="10/05/2016"
 ms.author="dobett"/>


# <a name="iot-hub-mqtt-support"></a>Prise en charge de MQTT au niveau d’IoT Hub

Grâce à IoT Hub, les appareils peuvent communiquer avec les points de terminaison d’appareil IoT Hub à l’aide du protocole [MQTT v3.1.1][lnk-mqtt-org] sur le port 8883. IoT Hub nécessite que toutes les communications de périphérique soient sécurisées à l'aide de TLS/SSL.

## <a name="connecting-to-iot-hub"></a>Connexion à IoT Hub

Un appareil peut utiliser le protocole MQTT pour se connecter à un IoT Hub, soit en utilisant les bibliothèques disponibles dans les [Kits de développement logiciel (SDK) Azure IoT][lnk-device-sdks]protocole MQTT.

## <a name="using-the-device-client-sdks"></a>Utilisation des Kits device client SDK

Des [Kits de développement logiciel (SDK) clients pour les appareils][lnk-device-sdks] prenant en charge le protocole MQTT sont disponibles pour Java, Node.js, C et C#. Les Kits device client SDK utilisent la chaîne de connexion IoT Hub standard pour établir une connexion à un hub IoT. Pour utiliser le protocole MQTT, le paramètre de protocole du client doit être défini sur **MQTT**. Par défaut, les Kits de développement logiciel (SDK) clients pour les appareils se connectent à un IoT Hub avec l’indicateur **CleanSession** défini sur **0**, et utilisent **QoS 1** pour l’échange de messages avec l’IoT Hub.

Quand un appareil est connecté à un hub IoT, les Kits device client SDK fournissent des méthodes qui permettent à l’appareil d’envoyer des messages et d’en recevoir à partir d’un hub IoT.

Le tableau suivant contient des liens vers des exemples de code pour chaque langage pris en charge et spécifie les paramètres à utiliser pour établir une connexion à IoT Hub à l’aide du protocole MQTT.

| Langage                   | Paramètre de protocole        |
| -------------------------- | ------------------------- |
| [Node.js][lnk-sample-node] | azure-iot-device-mqtt     |
| [Java][lnk-sample-java]    | IotHubClientProtocol.MQTT |
| [C][lnk-sample-c]          | MQTT_Protocol             |
| [C#][lnk-sample-csharp]    | TransportType.Mqtt        |
| [Python][lnk-sample-python] | IoTHubTransportProvider.MQTT |

### <a name="migrate-a-device-app-from-amqp-to-mqtt"></a>Migrer une application d’appareil de AMQP vers MQTT
Si vous utilisez les [Kits de développement logiciel (SDK) clients pour les appareils][lnk-appareil-SDK], la passage du protocole AMQP au protocole MQTT nécessite de modifier le paramètre de protocole dans l’initialisation du client comme indiqué ci-dessus.

Lors de cette opération, vérifiez les éléments suivants :

* AMQP retourne des erreurs pour nombreuses conditions, tandis que MQTT met fin à la connexion. Par conséquent, votre logique de gestion des exceptions peut nécessiter certaines modifications.
* MQTT ne prend pas en charge les opérations *reject* lors de la réception de [messages C2D][lnk-messaging]. Si votre serveur principal doit recevoir une réponse de l’application d’appareil, envisagez d’utiliser des [méthodes directes][lnk-methods].
* Actuellement, MQTT n’est pas disponible sur WebSockets.

## <a name="using-the-mqtt-protocol-directly"></a>Utilisation directe du protocole MQTT

Si un appareil ne peut pas utiliser les Kits device client SDK, il peut toujours se connecter aux points de terminaison d’appareil publics à l’aide du protocole MQTT. Dans le paquet **CONNECT** , l’appareil doit utiliser les valeurs suivantes :

- Pour le champ **ClientId**, utilisez le **deviceId**. 
- Dans le champ **Username**, utilisez `{iothubhostname}/{device_id}`, où {iothubhostname} est l’enregistrement CName complet du IoT Hub.

    Par exemple, si le nom de votre IoT Hub est **contoso.azure-devices.net** et si le nom de votre appareil est **MyDevice01**, le champ **Username** complet doit contenir `contoso.azure-devices.net/MyDevice01`.

- Dans le champ **Password**, utilisez un jeton SAP. Le format du jeton SAP est identique pour les protocoles HTTP et AMQP : <br/>`SharedAccessSignature sig={signature-string}&se={expiry}&sr={URL-encoded-resourceURI}`.

    Pour plus d’informations sur la génération de jetons SAP, voir la section consacrée aux appareils dans [Utilisation de jetons de sécurité IoT Hub][lnk-sas-tokens].
    
    Lors du test, vous pouvez également vous servir de l’outil [Explorateur d’appareils][lnk-device-explorer] pour générer rapidement un jeton SAP à copier et coller dans votre propre code :
    
    1. Accédez à l’onglet **Management** (Gestion) de l’Explorateur d’appareils.
    2. Cliquez sur **SAS Token** (Jeton SAP) en haut à droite.
    3. Dans **SASTokenForm**, sélectionnez votre appareil dans la liste déroulante **DeviceID**. Définissez votre **TTL**(Durée de vie).
    4. Cliquez sur **Generate** (Générer) pour créer votre jeton.
    
    Le jeton SAP généré présente la structure suivante :   `HostName={your hub name}.azure-devices.net;DeviceId=javadevice;SharedAccessSignature=SharedAccessSignature sr={your hub name}.azure-devices.net%2fdevices%2fMyDevice01&sig=vSgHBMUG.....Ntg%3d&se=1456481802`.

    La partie de ce jeton à utiliser dans le champ **Password** (Mot de passe) pour la connexion avec le protocole MQTT est :   `SharedAccessSignature sr={your hub name}.azure-devices.net%2fdevices%2fyDevice01&sig=vSgHBMUG.....Ntg%3d&se=1456481802g%3d&se=1456481802`.

Pour les paquets de connexion et de déconnexion MQTT, IoT Hub émet un événement sur le canal **Surveillance des opérations** .

### <a name="sending-messages-to-iot-hub"></a>Envoi de messages à IoT Hub

Après avoir correctement établi une connexion, un appareil peut envoyer des messages à IoT Hub en utilisant `devices/{device_id}/messages/events/` ou `devices/{device_id}/messages/events/{property_bag}` en tant que **Nom de la rubrique**. L’élément `{property_bag}` permet à l’appareil d’envoyer des messages avec des propriétés supplémentaires dans un format codé URL. Par exemple :

```
RFC 2396-encoded(<PropertyName1>)=RFC 2396-encoded(<PropertyValue1>)&RFC 2396-encoded(<PropertyName2>)=RFC 2396-encoded(<PropertyValue2>)…
```

> [AZURE.NOTE] Cet élément `{property_bag}` utilise le même encodage que celui utilisé pour les chaînes de requête dans le protocole HTTP.

L’application cliente de l’appareil peut également utiliser `devices/{device_id}/messages/events/{property_bag}` comme **nom de rubrique « Will »** pour définir des *messages « Will »* à transmettre en tant que message de télémétrie.

### <a name="receiving-messages"></a>Réception de messages

Pour recevoir des messages d’IoT Hub, l’appareil doit s’abonner en utilisant un `devices/{device_id}/messages/devicebound/#”` en tant que **Filtre de rubrique**. IoT Hub remet les messages avec le **Nom de la rubrique** `devices/{device_id}/messages/devicebound/`, ou `devices/{device_id}/messages/devicebound/{property_bag}` s’il y a des propriétés de message. `{property_bag}` contient des paires clé/valeur codées URL de propriétés de message. Seules les propriétés d’application et les propriétés système définissables par l’utilisateur (comme **messageId** ou **correlationId**) sont incluses dans le jeu de propriétés. Les noms de propriété système ont le préfixe **$**, tandis que les noms de propriété d’application ne sont précédés d’aucun préfixe.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, voir [Remarques sur la prise en charge MQTT][lnk-mqtt-devguide] dans le Guide du développeur Azure IoT Hub.

Pour en savoir plus sur le protocole MQTT, voir la [documentation de MQTT][lnk-mqtt-docs].

Pour en savoir plus sur la planification de votre déploiement IoT Hub, consultez :

- [Appareils pris en charge][lnk-devices]
- [Protocoles supplémentaires pris en charge][lnk-protocols]
- [Comparer à Event Hubs][lnk-compare]
- [Mise à l’échelle, haute disponibilité et récupération d’urgence][lnk-scaling]

Pour explorer davantage les capacités de IoT Hub, consultez :

- [Guide du développeur][lnk-devguide]
- [Simulation d’un appareil avec le Kit de développement logiciel (SDK) Gateway][lnk-gateway]

[lnk-device-sdks]: https://github.com/Azure/azure-iot-sdks/blob/master/readme.md
[lnk-mqtt-org]: http://mqtt.org/
[lnk-mqtt-docs]: http://mqtt.org/documentation
[lnk-sample-node]: https://github.com/Azure/azure-iot-sdks/blob/develop/node/device/samples/simple_sample_device.js
[lnk-sample-java]: https://github.com/Azure/azure-iot-sdks/blob/develop/java/device/samples/send-receive-sample/src/main/java/samples/com/microsoft/azure/iothub/SendReceive.java
[lnk-sample-c]: https://github.com/Azure/azure-iot-sdks/tree/master/c/iothub_client/samples/iothub_client_sample_mqtt
[lnk-sample-csharp]: https://github.com/Azure/azure-iot-sdks/tree/master/csharp/device/samples
[lnk-sample-python]: https://github.com/Azure/azure-iot-sdks/tree/master/python/device/samples
[lnk-device-explorer]: https://github.com/Azure/azure-iot-sdks/blob/master/tools/DeviceExplorer/readme.md
[lnk-sas-tokens]: iot-hub-devguide-security.md#using-sas-tokens-as-a-device
[lnk-mqtt-devguide]: iot-hub-devguide-messaging.md#notes-on-mqtt-support

[lnk-devices]: iot-hub-tested-configurations.md
[lnk-protocols]: iot-hub-protocol-gateway.md
[lnk-compare]: iot-hub-compare-event-hubs.md
[lnk-scaling]: iot-hub-scaling.md
[lnk-devguide]: iot-hub-devguide.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md

[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-messaging]: iot-hub-devguide-messaging.md



<!--HONumber=Oct16_HO2-->


