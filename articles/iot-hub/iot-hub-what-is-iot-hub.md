<properties
 pageTitle="Présentation d'IoT Hub Azure | Microsoft Azure"
 description="Une vue d’ensemble du service Azure IoT Hub, et notamment de la connectivité de l’appareil, des schémas de communication et du schéma de communication de service assistée."
 services="iot-hub"
 documentationCenter=""
 authors="dominicbetts"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="11/09/2015"
 ms.author="dobett"/>

# Qu’est-ce qu’Azure IoT Hub ?

Bienvenue dans Azure IoT Hub. Cet article fournit une vue d'ensemble d'Azure IoT Hub et décrit la raison pour laquelle il est recommandé d'utiliser ce service lorsque vous mettez en œuvre une solution IoT.

Azure IoT Hub est un service entièrement géré qui permet des communications bidirectionnelles fiables et sécurisées entre des millions d'appareils IoT et un serveur principal de solution. Azure IoT Hub propose une messagerie « appareil vers cloud » et « cloud vers appareil » à l'échelle extensible et fiable, garantit la sécurité des communications grâce au contrôle d'accès et aux informations d'identification de sécurité par appareil, et offre des bibliothèques d'appareils pour les plates-formes et les langages les plus répandus.

![IoT Hub comme passerelle cloud ?][img-architecture]

## Défis liés à la connectivité des appareils IoT

IoT Hub et les bibliothèques d'appareils vous aident à relever les défis en matière de fiabilité et de sécurisation de la connexion d'appareil au serveur principal de la solution. Les appareils IoT :

- sont souvent des systèmes intégrés, qui ne font appel à aucun opérateur humain ;
- peuvent être situés sur des sites distants avec un accès physique très coûteux ;
- sont accessibles uniquement via le serveur principal de la solution ;
- peuvent avoir des performances et/ou des ressources de traitement limitées ;
- peuvent avoir une connectivité réseau intermittente, lente ou coûteuse ;
- peuvent nécessiter l'utilisation des protocoles d'application personnalisés propriétaires ou spécifiques à un secteur.
- peuvent être crées à l'aide d'un large éventail de plates-formes matérielles et logicielles populaires.

Outre les exigences ci-dessus, n’importe quelle solution IoT doit également offrir des possibilités d’évolution, la sécurité et la fiabilité. Il en résulte un ensemble d'exigences de connectivité complexe, long à mettre en place avec des technologies traditionnelles telles que des conteneurs web et des courtiers de messagerie.

## Pourquoi utiliser Azure IoT Hub ?

Azure IoT Hub répond aux défis liés à la connectivité du périphérique comme suit :

-   **Authentification par périphérique et connectivité sécurisée**. Vous pouvez configurer chaque périphérique avec sa propre clé de sécurité pour lui permettre de se connecter à l'IoT Hub. Un serveur principal de la solution peut intégrer des appareils individuels à une liste blanche ou noire, permettant ainsi un contrôle de l'accès à l'appareil.

-   **Un ensemble complet de bibliothèques de périphériques**. Les kits de développement logiciel (SDK) d’appareil Azure IoT sont disponibles et pris en charge pour différents langages et plateformes : C pour les distributions Linux, Windows et RTO. Les kits de développement logiciel (SDK) d’appareil Azure IoT prennent également en charge les langages gérés tels que C#, Java et JavaScript.

-   **Protocoles et possibilités d’extension IoT**. Si votre solution ne peut pas utiliser les bibliothèques du périphérique, IoT Hub propose un protocole public qui permet aux appareils d’utiliser nativement les protocoles HTTP 1.1 et AMQP 1.0. Vous pouvez également étendre l'IoT Hub afin de permettre la prise en charge de MQTT v3.1.1 avec le composant open source [Azure IoT Protocol Gateway][protocol-gateway]. Vous pouvez exécuter la passerelle de protocole Azure IoT (Azure IoT Protocol Gateway) dans le cloud ou sur site et l'étendre pour prendre en charge des protocoles personnalisés.

-   **Mise à l’échelle** Azure IoT Hub peut supporter des millions d’appareils connectés simultanément et des millions d’événements par seconde.

Ces avantages sont exploitables par de nombreux modèles de communication. IoT Hub vous permet actuellement de mettre en œuvre les modèles de communication spécifiques suivants :

-   **Ingestion appareil vers cloud basée sur un événement.** IoT Hub peut recevoir en toute fiabilité des millions d'événements par seconde de vos appareils et les traiter sur votre chemin réactif à l'aide d'un moteur de traitement d'événements ou les stocker sur votre chemin à froid pour analyse. IoT Hub conserve les données d'événement jusqu'à 7 jours afin de garantir un traitement fiable et absorber les pics de charge.

-   **Messagerie cloud à appareil fiable (ou *commandes*).** Le serveur principal de la solution peut utiliser IoT Hub pour envoyer des messages avec la garantie d'au moins une livraison vers des appareils individuels. Chaque message bénéficie d'une durée de vie propre, et le serveur principal peut demander des accusés de réception et d'expiration afin de garantir une visibilité totale du cycle de vie d'un message de type « cloud vers appareil ». Cela vous permet de mettre en œuvre une logique métier qui inclut des opérations s'exécutant sur des appareils.

Vous pouvez également mettre en œuvre d'autres modèles populaires tels que le chargement ou le téléchargement de fichiers en tirant parti de fonctionnalités spécifiques dans IoT Hub, par exemple la gestion d'identité de périphérique cohérente, la surveillance de connectivité et la mise à l'échelle.

## Comment IoT Hub fonctionne-t-il ?

Azure IoT Hub met en œuvre le modèle de [communications assistées par service][lnk-service-assisted-pattern] pour gérer les interactions entre vos appareils et votre serveur principal de service. L'objectif de la communication assistée par service consiste à établir des itinéraires de communication bidirectionnels fiables entre un système de contrôle tel qu'IoT Hub et les appareils à usage spécifique déployés dans des espaces physiques non approuvés. Le modèle établit les principes suivants :

- La sécurité est prioritaire sur toutes les autres fonctionnalités.
- Les appareils n’acceptent pas les informations réseau non sollicitées. Un appareil établit tous les itinéraires et connexions pour le trafic sortant uniquement. Pour qu’un appareil reçoive une commande de la part d’un serveur principal, il doit régulièrement établir une connexion pour vérifier toutes les commandes en attente de traitement.
- Les appareils doivent uniquement connecter ou établir des itinéraires vers des services bien connus auxquels ils sont couplés, par exemple un IoT Hub.
- L’itinéraire de communication entre l’appareil ou le service et la passerelle est sécurisé au niveau du protocole d’application.
- L'autorisation et l'authentification au niveau du système doivent être basées sur des identités par appareil, et les informations d'identification et les autorisations doivent être révocables quasi instantanément.
- La communication bidirectionnelle des périphériques connectés de façon sporadique en raison de problèmes d'alimentation ou de connectivité peut être facilitée via la mise en attente de commandes et de notifications aux appareils jusqu'à ce que qu'un appareil soit connecté pour les recevoir. IoT Hub gère des files d'attente spécifiques aux commandes qu'il envoie.
- Les données de charge utile d'application sont sécurisées séparément dans le cadre d'un transit protégé via des passerelles vers un service particulier.

Le schéma de communication assistée par service est utilisé dans l'industrie mobile à grande échelle afin de mettre en œuvre des services de notification Push tels que [Service de notification](https://msdn.microsoft.com/library/windows/apps/mt187203.aspx), la [Messagerie cloud Google](https://developers.google.com/cloud-messaging/) et l'[Apple Push Notification Service](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW9).

## Étapes suivantes

Pour en savoir plus sur Azure IoT Hub, consultez ces liens :

* [Prise en main d’IoT Hub]
* [Connexion de votre périphérique]
* [Traitement de messages de type « Appareil vers cloud »]

[Prise en main d’IoT Hub]: iot-hub-csharp-csharp-getstarted.md
[Connexion de votre périphérique]: https://azure.microsoft.com/develop/iot/
[Traitement de messages de type « Appareil vers cloud »]: iot-hub-csharp-csharp-process-d2c.md
[protocol-gateway]: https://github.com/Azure/azure-iot-protocol-gateway/blob/master/README.md
[img-architecture]: media/iot-hub-what-is-iot-hub/hubarchitecture.png

[lnk-service-assisted-pattern]: http://blogs.msdn.com/b/clemensv/archive/2014/02/10/service-assisted-communication-for-connected-devices.aspx "Communication de service assistée, billet de blog de Clemens Vasters"

<!---HONumber=Nov15_HO3-->