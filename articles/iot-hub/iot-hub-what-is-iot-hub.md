<properties
 pageTitle="Présentation d’IoT Hub Azure | Microsoft Azure"
 description="Une vue d’ensemble du service Azure IoT Hub, et notamment de la connectivité de l’appareil, des schémas de communication et du schéma de communication de service assistée."
 services="iot-hub"
 documentationCenter=""
 authors="fsautomata"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="09/29/2015"
 ms.author="elioda"/>

# Qu’est-ce qu’Azure IoT Hub ?

Azure IoT Hub est un service entièrement géré qui autorise des communications bidirectionnelles fiables et sécurisées entre des millions d’appareils IoT et un serveur d’applications principal. Azure IoT Hub propose une messagerie « appareil vers cloud » et « cloud vers appareil » à grande échelle extensible et fiable, garantit la sécurité des communications grâce au contrôle d’accès et aux informations d’identification de sécurité par appareil, et offre des bibliothèques de périphériques pour les plateformes et les langages les plus répandus.

![IoT Hub comme passerelle cloud ?][img-architecture]

## Connectivité des appareils IoT

L’une des plus grandes difficultés dans les projets IoT consiste à pouvoir connecter de manière fiable et sécurisée des appareils au serveur d’applications principal. Les appareils IoT ont souvent des caractéristiques différentes de celles des autres clients (navigateurs, autres serveurs et applications mobiles) :

-   Il s’agit souvent de systèmes intégrés, qui ne font appel à aucun opérateur humain.

-   Ils peuvent être situés sur des sites distants, où l’accès physique est très coûteux.

-   La connectivité à l’application principale peut être le seul moyen d’accéder à l’appareil.

-   Leurs performances et/ou leurs ressources de traitement peuvent être limitées.

-   La connectivité réseau de l’appareil peut être intermittente, coûteuse ou rare.

-   Il se peut que vous deviez utiliser des protocoles d’application personnalisés propriétaires ou spécifiques à un secteur.

-   Il existe un large éventail de plateformes matérielles et logicielles populaires permettant la création d’appareils.

Outre les exigences ci-dessus, n’importe quelle solution IoT doit également offrir des possibilités d’évolution, la sécurité et la fiabilité. Tous ces aspects se combinent dans un ensemble d’exigences de connectivité complexe, long à mettre en place avec des technologies traditionnelles telles que les conteneurs web et les courtiers de messagerie.

## Pourquoi utiliser Azure IoT Hub ?

Azure IoT Hub traite les exigences de connectivité du périphérique comme suit :

-   **Authentification par périphérique et connectivité sécurisée**. Chaque appareil peut utiliser sa propre clé de sécurité pour se connecter à IoT Hub. Le serveur principal d’application peut ensuite placer chaque appareil sur liste blanche ou sur liste noire, ce qui lui permet de maîtriser parfaitement l’accès à l’appareil.

-   **Un ensemble complet de bibliothèques de périphériques**. Les kits de développement logiciel (SDK) d’appareil Azure IoT sont disponibles et pris en charge pour différents langages et plateformes : C pour les distributions Linux, Windows et RTO. Les kits de développement logiciel (SDK) d’appareil Azure IoT prennent également en charge les langages gérés tels que C#, Java et JavaScript.

-   **Protocoles et possibilités d’extension IoT**. Si votre solution ne peut pas utiliser les bibliothèques du périphérique, IoT Hub propose un protocole public qui permet aux appareils d’utiliser nativement les protocoles HTTP 1.1 et AMQP 1.0. Vous pouvez également étendre IoT Hub afin de permettre la prise en charge de MQTT v3.1.1 avec le composant source de passerelle de protocole Azure IoT ouvert. Vous pouvez exécuter la passerelle de protocole Azure IoT dans le cloud ou sur site, et l’étendre pour prendre en charge des protocoles personnalisés.

-   **Mise à l’échelle** Azure IoT Hub peut supporter des millions d’appareils connectés simultanément et des millions d’événements par seconde.

Outre ces avantages, qui sont généralisés sur de nombreux schémas de communication, IoT Hub vous permet de mettre en œuvre les schémas de communication suivants :

-   **Ingestion appareil vers cloud basée sur un événement.** Les appareils fiables peuvent envoyer des millions d’événements par seconde afin qu’ils soient traités par le moteur de processeur d’événements de chemin réactif ou stockés en vue d’un parcours d’analyse à froid. IoT Hub conserve les données d’événement jusqu’à 7 jours afin de garantir un traitement fiable et absorber les pics de charge.

-   **Messagerie cloud à appareil fiable (ou *commandes*).** Le serveur principal de l’application peut envoyer des messages fiables (c’est-à-dire avec la garantie d’au moins une livraison) vers des appareils individuels. Chaque message bénéficie d’une durée de vie propre, et le serveur principal peut demander des accusés de réception et d’expiration afin de garantir une visibilité totale du cycle de vie d’un message cloud vers appareil.

Outre ces modèles de communication, vous pouvez implémenter d’autres modèles populaires tels que le chargement ou le téléchargement de fichiers en tirant parti de fonctionnalités spécifiques dans IoT Hub, par exemple, la gestion d’identité de périphérique cohérente, la surveillance et la mise à l’échelle de connectivité.

## Modèle de communication de service assistée

Azure IoT Hub gère les interactions entre vos périphériques et votre application principale dans une implémentation du modèle [Communication de service assistée][lnk-service-assisted-pattern]. L’objectif de la communication de service assistée consiste à établir des itinéraires de communication bidirectionnels fiables entre les systèmes de contrôle (comme IoT Hub) et les appareils à usage spécifique déployés dans des espaces physiques non approuvés. À cette fin, le modèle établit les principes suivants :

- La sécurité l’emporte sur toutes les fonctionnalités.
- Les appareils n’acceptent pas les informations réseau non sollicitées. Tous les itinéraires et connexions sont établis à partir d’un appareil pour le trafic sortant uniquement. Pour qu’un appareil reçoive une commande de la part d’un serveur principal, il doit régulièrement établir une connexion pour vérifier toutes les commandes en attente de traitement.
- En général, les appareils se connectent ou établissent des itinéraires uniquement vers des services bien connus auxquels ils sont couplés, par exemple une instance de service IoT Hub.
- L’itinéraire de communication entre l’appareil ou le service et la passerelle est sécurisé au niveau du protocole d’application.
- Les autorisations et l’authentification de niveau système doivent être basées sur les identités par appareil, et les informations d’identification et les autorisations doivent être révocables quasi instantanément.
- La communication bidirectionnelle des périphériques connectés de façon sporadique en raison de problèmes d’alimentation ou de connectivité peut être facilitée via la mise en attente de commandes et de notifications aux appareils jusqu’à ce que ces derniers se connectent pour les recevoir. IoT Hub gère les files d’attente spécifiques aux commandes qu’il envoie aux périphériques.
- Les données de charge utile d’application peuvent être sécurisées séparément dans le cadre d’un transit protégé via des passerelles vers un service particulier.

Le schéma de communication assistée de service est utilisé dans l’industrie mobile à grande échelle afin d’implémenter les services de notification Push, tels que [Service de notification](https://msdn.microsoft.com/library/windows/apps/mt187203.aspx), [Messagerie cloud Google](https://developers.google.com/cloud-messaging/) et [Service de notification Push Apple](http://go.microsoft.com/fwlink/p/?linkid=272584&clcid=0x409).

## Étapes suivantes

Pour en savoir plus sur Azure IoT Hub, consultez ces liens :

* [Guide du développeur IoT Hub]
* [Conseils pour IoT Hub]
* [Langages et plateformes d’appareils pris en charge]
* [Centre de développement Azure IoT]
* [Prise en main d’IoT Hub]

[IoT Hub Overview]: iot-hub-what-is-iot-hub.md
[Conseils pour IoT Hub]: iot-hub-guidance.md
[Guide du développeur IoT Hub]: iot-hub-devguide.md
[IoT Hub Supported Devices]: iot-hub-supported-devices.md
[Prise en main d’IoT Hub]: iot-hub-csharp-csharp-getstarted.md
[Langages et plateformes d’appareils pris en charge]: iot-hub-sdks-summary.md#os-platforms-and-hardware-compatibility
[Centre de développement Azure IoT]: https://azure.microsoft.com/develop/iot/

[img-why-use]: media/iot-hub-what-is-iot-hub/image1.png
[img-architecture]: media/iot-hub-what-is-iot-hub/hubarchitecture.png

[lnk-service-assisted-pattern]: http://blogs.msdn.com/b/clemensv/archive/2014/02/10/service-assisted-communication-for-connected-devices.aspx "Communication de service assistée, billet de blog de Clemens Vasters"

<!---HONumber=Oct15_HO3-->