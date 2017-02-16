> [!div class="op_single_selector"]
> * [Node.JS](../articles/iot-hub/iot-hub-node-node-direct-methods.md)
> * [C#](../articles/iot-hub/iot-hub-csharp-node-direct-methods.md)
> 
> 

## <a name="introduction"></a>Introduction
Azure IoT Hub est un service entièrement géré qui permet des communications bidirectionnelles fiables et sécurisées entre des millions d’appareils et un serveur principal de solution. Les didacticiels précédents ([Prise en main d’IoT Hub] et [Envoi de messages cloud-à-appareil avec IoT Hub]) illustre les fonctionnalités de base de messagerie appareil-à-cloud et cloud-à-appareil offertes par IoT Hub. IoT Hub permet également d’appeler des méthodes non durables sur des appareils à partir du cloud. Les méthodes représentent une interaction de demande-réponse avec un appareil, similaire à un appel HTTP, dans la mesure où elles réussissent ou échouent immédiatement (après un délai d’attente spécifié par l’utilisateur) pour permettre à l’utilisateur de connaître l’état de l’appel. [Appeler une méthode directe sur un appareil][lnk-devguide-methods] décrit plus en détail les méthodes, et indique quand utiliser des méthodes plutôt que des messages cloud-à-appareil.

Ce didacticiel vous explique les procédures suivantes :

* Utiliser le portail Azure pour créer un IoT Hub et une identité d’appareil dans celui-ci.
* Créer une application de périphérique simulé disposant d’une méthode directe qui peut être appelée par le cloud.
* Créer une application console qui appelle une méthode directe sur l’application de périphérique simulé via votre IoT Hub.

> [!NOTE]
> Actuellement, les méthodes directes sont accessibles uniquement à partir d’appareils qui se connectent à l’IoT Hub en utilisant le protocole MQTT. Pour obtenir des instructions sur la conversion d’une application de périphérique existante pour utiliser MQTT, voir [Support MQTT][lnk-devguide-mqtt].
> 
> 




[lnk-devguide-methods]: ../articles/iot-hub/iot-hub-devguide-direct-methods.md
[lnk-devguide-mqtt]: ../articles/iot-hub/iot-hub-mqtt-support.md

[Envoi de messages cloud-à-appareil avec IoT Hub]: ../articles/iot-hub/iot-hub-csharp-csharp-c2d.md
[Prise en main d’IoT Hub]: ../articles/iot-hub/iot-hub-node-node-getstarted.md

<!--HONumber=Feb17_HO3-->


