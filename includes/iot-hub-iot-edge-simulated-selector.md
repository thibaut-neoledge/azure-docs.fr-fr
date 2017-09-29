> [!div class="op_single_selector"]
> * [Linux](../articles/iot-hub/iot-hub-linux-iot-edge-simulated-device.md)
> * [Windows](../articles/iot-hub/iot-hub-windows-iot-edge-simulated-device.md)

Cette procédure pas à pas de [l’exemple de chargement d’un appareil simulé sur le cloud] vous indique comment utiliser [Azure IoT Edge][lnk-sdk] pour envoyer les données de télémétrie appareil-vers-cloud à IoT Hub à partir d’appareils simulés.

Cette procédure pas à pas inclut les étapes suivantes :

* **Architecture** : informations architecturales concernant [l’exemple de chargement d’un appareil simulé sur le cloud].
* **Créer et exécuter**: les étapes requises pour créer et exécuter l’exemple.

## <a name="architecture"></a>Architecture

[l’exemple de chargement d’un appareil simulé sur le cloud] vous indique comment créer une passerelle qui envoie les données de télémétrie d’appareils simulés vers IoT Hub. Un appareil peut ne pas être en mesure de se connecter directement à IoT Hub dans les cas suivants :

* L’appareil n’utilise pas un protocole de communication compris par IoT Hub
* L’appareil n’est pas suffisamment intelligent pour mémoriser l’identité qui lui a été attribuée par IoT Hub

Une passerelle IoT Edge peut résoudre ces problèmes de plusieurs manières :

* La passerelle comprend le protocole utilisé par l’appareil, reçoit les données de télémétrie appareil-vers-cloud à partir de l’appareil, puis transfère ces messages vers IoT Hub à l’aide d’un protocole compris par IoT Hub.

* La passerelle mappe les identités IoT Hub sur les appareils et agit comme un proxy lorsqu’un appareil envoie des messages à IoT Hub.

Le diagramme suivant montre les principaux composants de l’exemple, notamment les modules IoT Edge :

![Diagramme : Le message de l’appareil simulé est acheminé à IoT Hub par la passerelle][1]

Cet exemple contient trois modules qui composent la passerelle :
1. Module d'ingestion de protocole
1. MAC &lt;-&gt; IoT Hub ID module
1. Module de communication IoT Hub

Les modules ne s’échangent pas directement les messages. Les modules publient des messages vers un répartiteur interne qui remet les messages aux autres modules à l’aide d’un mécanisme d’abonnement. Pour plus d’informations, consultez [Prise en main d’Azure IoT Edge][lnk-gw-getstarted].

![Diagramme : Les modules de la passerelle communiquent avec le répartiteur][2]

### <a name="protocol-ingestion-module"></a>Module d'ingestion de protocole

Le module d’ingestion de protocole constitue le point de départ du processus de transmission de données d’appareils vers le cloud par le biais de la passerelle. 

Dans l’exemple, ce module :

1. Crée les données de la température simulée. Si vous utilisez des appareils physiques, le module lit les données à partir de ces appareils.
1. Crée un message.
1. Place les données de la température simulée dans le contenu d’un message.
1. Ajoute une propriété avec une adresse MAC fictive au message.
1. Rend le message disponible pour le module suivant dans la chaîne.

Le module d’ingestion de protocole est **simulated_device.c** dans le code source.

### <a name="mac-lt-gt-iot-hub-id-module"></a>MAC &lt;-&gt; IoT Hub ID module

Le module MAC &lt;-&gt; ID IoT Hub fonctionne comme un traducteur. Cet exemple utilise une adresse MAC comme identificateur unique de l'appareil et la met en corrélation avec une identité d'appareil IoT Hub. Toutefois, vous pouvez écrire votre propre module qui utilise un autre identificateur unique. Par exemple, vos appareils peuvent comporter des numéros de série uniques, ou les données de télémétrie peuvent inclure un nom d’appareil incorporé unique.

Dans l’exemple, ce module :

1. Analyse les messages dotés d’une propriété d’adresse MAC.
1. Si une adresse MAC est détectée, il ajoute au message une autre propriété avec une clé d’appareil IoT Hub. 
1. Rend le message disponible pour le module suivant dans la chaîne.

Le développeur configure un mappage entre les adresses MAC et les identités IoT Hub pour associer les appareils simulés aux identités d’appareils IoT Hub. Le développeur ajoute le mappage manuellement dans le cadre de la configuration du module.

Le module MAC &lt;-&gt; ID IoT Hub est **identitymap.c** dans le code source. 

### <a name="iot-hub-communication-module"></a>Module de communication IoT Hub

Le module de communication IoT Hub ouvre une connexion HTTP entre la passerelle et IoT Hub. HTTP est un des trois protocoles compris par IoT Hub. Ce module vous évite d’ouvrir une connexion pour chaque appareil. Pour cela, il multiplexe les connexions de tous les appareils sur une connexion. Cette approche permet à une seule passerelle de connecter une multitude d’appareils. 

Dans l’exemple, ce module :

1. récupère les messages avec une propriété de clé d’appareil IoT Hub attribuée par le module précédent ; 
1. envoie le contenu des messages à IoT Hub à l’aide du protocole HTTP. 

Le module de communication IoT Hub est **iothub.c** dans le code source.

## <a name="before-you-get-started"></a>Avant de commencer

Avant de commencer, vous devez :

* [Créer un hub IoT][lnk-create-hub] dans votre abonnement Azure. Cet exemple de procédure pas à pas nécessite le nom de votre hub. Si vous ne possédez pas de compte, vous pouvez créer un [compte gratuit][lnk-free-trial] en quelques minutes.
* Ajoutez deux appareils à votre hub IoT et notez leur ID et leurs clés d’appareil. Vous pouvez utiliser l’outil [Explorateur d’appareils][lnk-device-explorer] ou [iothub-explorer][lnk-iothub-explorer] pour ajouter des appareils au hub IoT et récupérer leurs clés.


<!-- Images -->
[1]: media/iot-hub-iot-edge-simulated-selector/image1.png
[2]: media/iot-hub-iot-edge-simulated-selector/image2.png

<!-- Links -->
[l’exemple de chargement d’un appareil simulé sur le cloud]: https://github.com/Azure/iot-edge/blob/master/samples/simulated_device_cloud_upload/README.md
[lnk-sdk]: https://github.com/Azure/iot-edge
[lnk-gw-getstarted]: ../articles/iot-hub/iot-hub-linux-iot-edge-get-started.md
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-device-explorer]: https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer
[lnk-iothub-explorer]: https://github.com/Azure/iothub-explorer/blob/master/readme.md
[lnk-create-hub]: ../articles/iot-hub/iot-hub-create-through-portal.md