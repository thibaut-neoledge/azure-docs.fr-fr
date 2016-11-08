---
title: Présentation d’Azure IoT Hub | Microsoft Docs
description: 'Présentation du service Azure IoT Hub : signification du hub IoT, connectivité des appareils, modèles de communication Internet des Objets et modèle de communication de service assistée.'
services: iot-hub
documentationcenter: ''
author: dominicbetts
manager: timlt
editor: ''

ms.service: iot-hub
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/25/2016
ms.author: dobett

---
# Qu’est-ce qu’Azure IoT Hub ?
Bienvenue dans Azure IoT Hub. Cet article fournit une vue d’ensemble d’Azure IoT Hub et décrit la raison pour laquelle il est recommandé d’utiliser ce service afin de mettre en œuvre une solution IoT. Azure IoT Hub est un service entièrement géré qui permet des communications bidirectionnelles fiables et sécurisées entre des millions d’appareils IoT et un serveur principal de solution. Azure IoT Hub :

* Fournit une messagerie appareil-à-cloud et cloud-à-appareil fiable à grande échelle.
* Assure la sécurité des communications grâce aux informations d’identification de sécurité par appareil et au contrôle d’accès.
* Fournit une surveillance complète de la connectivité des appareils et des événements de gestion de l’identité des appareils.
* Inclut des bibliothèques d’appareils pour les langages et les plateformes les plus courants.

L’article [Comparaison entre IoT Hub et Event Hubs][lnk-compare] décrit les différences principales entre ces deux services et présente les avantages liés à l’utilisation d’IoT Hub dans vos solutions IoT.

![Azure IoT Hub comme passerelle cloud dans une solution Internet des objets][img-architecture]

> [!NOTE]
> Pour une étude approfondie de l’architecture IoT, consultez le document [Microsoft Azure IoT services: Reference Architecture][lnk-refarch] \(Services Microsoft Azure IoT : Architecture de référence).
> 
> 

## Défis liés à la connectivité des appareils IoT
IoT Hub et les bibliothèques d'appareils vous aident à relever les défis en matière de fiabilité et de sécurisation de la connexion d'appareil au serveur principal de la solution. Les appareils IoT :

* sont souvent des systèmes intégrés, qui ne font appel à aucun opérateur humain ;
* peuvent être situés sur des sites distants avec un accès physique coûteux ;
* sont accessibles uniquement via le serveur principal de la solution ;
* peuvent avoir des performances et/ou des ressources de traitement limitées ;
* peuvent avoir une connectivité réseau intermittente, lente ou coûteuse ;
* peuvent nécessiter l’utilisation des protocoles d’application personnalisés, propriétaires ou spécifiques à un secteur.
* peuvent être créés à l’aide d’un large éventail de plateformes matérielles et logicielles populaires.

Outre les exigences ci-dessus, n’importe quelle solution IoT doit également offrir des possibilités d’évolution, la sécurité et la fiabilité. Il en résulte un ensemble d’exigences de connectivité complexe, long à mettre en place lorsque vous utilisez des technologies traditionnelles telles que des conteneurs web et des courtiers de messagerie.

## Pourquoi utiliser Azure IoT Hub ?
Azure IoT Hub répond aux défis liés à la connectivité de l’appareil comme suit :

* **Authentification par appareil et connectivité sécurisée**. Vous pouvez configurer chaque appareil avec sa propre [clé de sécurité][lnk-devguide-security] pour lui permettre de se connecter à IoT Hub. Le [Registre d’identité IoT Hub][lnk-devguide-identityregistry] stocke les clés et les identités des appareils dans une solution. Un serveur principal de la solution peut ajouter des appareils individuels à une liste d’autorisation ou de refus, permettant ainsi un contrôle complet de l’accès à l’appareil.
* **Surveillance des opérations de connectivité des appareils**. Vous pouvez recevoir des journaux d’opérations détaillés sur les opérations de gestion de l’identité des appareils et sur les événements de connectivité des appareils. Cette fonctionnalité de surveillance permet à votre solution IoT d’identifier les problèmes de connectivité, tels que les appareils qui tentent de se connecter avec des informations d’identification incorrectes, envoient des messages trop fréquemment ou rejettent tous les messages cloud-à-appareil.
* **Un ensemble complet de bibliothèques d’appareils**. Les [Kits de développement logiciel (SDK) d’appareil Azure IoT][lnk-device-sdks] sont disponibles et pris en charge pour différents langages et plateformes : C pour les distributions Linux, Windows et les systèmes d’exploitation en temps réel. Les Kits de développement logiciel (SDK) d’appareil Azure IoT prennent également en charge les langages gérés tels que C#, Java et JavaScript.
* **Protocoles et possibilités d’extension IoT**. Si votre solution ne peut pas utiliser les bibliothèques d’appareils, IoT Hub propose un protocole public qui permet aux appareils d’utiliser nativement les protocoles MQTT v3.1.1, HTTP 1.1 ou AMQP 1.0. Vous pouvez également étendre IoT Hub afin de permettre la prise en charge des protocoles personnalisés en :
  
  * Créant une passerelle de champ avec le [Kit de développement logiciel (SDK) Azure IoT Gateway][lnk-gateway-sdk] qui convertit votre protocole personnalisé en l’un des trois protocoles compris par IoT Hub.
  * Personnalisant la [passerelle de protocole Azure IoT][protocol-gateway], composant open source qui s’exécute dans le cloud.
* **Mise à l’échelle** Azure IoT Hub peut supporter des millions d’appareils connectés simultanément et des millions d’événements par seconde.

Ces avantages sont exploitables par de nombreux modèles de communication. IoT Hub vous permet actuellement de mettre en œuvre les modèles de communication spécifiques suivants :

* **Ingestion appareil vers cloud basée sur un événement.** IoT Hub permet de recevoir de manière fiable des millions d’événements par seconde à partir de vos appareils. Il peut ensuite les traiter sur votre chemin réactif à l’aide d’un moteur de processeur d’événements. Il peut également les stocker sur votre chemin à froid pour l’analyse. IoT Hub conserve les données d’événement jusqu’à 7 jours afin de garantir un traitement fiable et d’absorber les pics de charge.
* **Messagerie Cloud à appareil fiable (ou *commandes*).** Le serveur principal de la solution peut utiliser IoT Hub pour envoyer des messages avec la garantie d’au moins une livraison vers des appareils individuels. Chaque message bénéficie d’une durée de vie propre, et le serveur principal peut demander des accusés de réception et d’expiration. Ces accusés de réception permettent de garantir une visibilité totale du cycle de vie d’un message entre le cloud et un appareil. Vous pouvez ensuite mettre en œuvre une logique métier qui inclut des opérations s’exécutant sur des appareils.
* **Chargement des fichiers et des données de capteur mises en cache dans le cloud.** Vos appareils peuvent charger des fichiers dans Azure Storage à l’aide d’URI SAP gérés par IoT Hub. IoT Hub peut générer des notifications lorsque des fichiers arrivent dans le cloud pour activer le serveur principal chargé de les traiter.

## Passerelles
Une passerelle dans une solution IoT est généralement une [passerelle de protocole][lnk-gateway] déployée dans le cloud ou une [passerelle de champ][lnk-field-gateway] déployée localement avec vos appareils. Une passerelle de protocole effectue la traduction de protocole, par exemple MQTT à AMQP. Une passerelle de champ peut exécuter des analyses sur le périmètre, prendre des décisions urgentes pour réduire la latence, fournir des services de gestion des appareils, appliquer des contraintes de confidentialité et de sécurité, mais aussi effectuer la traduction de protocole. Les deux types de passerelle agissent comme intermédiaire entre vos appareils et votre IoT Hub.

Une passerelle de champ est différente d’un appareil de routage de trafic simple, par exemple un pare-feu ou un appareil de traduction d’adresses réseau, car elle a généralement un rôle actif dans la gestion de l’accès et du flux des informations dans votre solution.

Une solution peut inclure des passerelles de protocole et de champ.

## Comment IoT Hub fonctionne-t-il ?
Azure IoT Hub met en œuvre le modèle de [communications assistées par service][lnk-service-assisted-pattern] pour gérer les interactions entre vos appareils et votre serveur principal de service. L’objectif de la communication de service assistée consiste à établir des itinéraires de communication bidirectionnels fiables entre un système de contrôle (comme IoT Hub) et les appareils à usage spécifique déployés dans des espaces physiques non approuvés. Le modèle établit les principes suivants :

* La sécurité est prioritaire sur toutes les autres fonctionnalités.
* Les appareils n’acceptent pas les informations réseau non sollicitées. Un appareil établit tous les itinéraires et connexions pour le trafic sortant uniquement. Pour qu’un appareil reçoive une commande de la part d’un serveur principal, il doit régulièrement établir une connexion pour vérifier toutes les commandes en attente de traitement.
* Les appareils doivent uniquement connecter ou établir des itinéraires vers des services bien connus auxquels ils sont couplés, par exemple un IoT Hub.
* L’itinéraire de communication entre l’appareil et le service ou entre l’appareil et la passerelle est sécurisé au niveau du protocole d’application.
* L’authentification et l’autorisation au niveau du système sont basées sur les identités par appareil. Les autorisations et informations d’identification sont ainsi révocables presque instantanément.
* La communication bidirectionnelle des appareils connectés de façon sporadique en raison de problèmes d’alimentation ou de connectivité peut être facilitée par la mise en attente de commandes et de notifications aux appareils jusqu’à ce que qu’un appareil soit connecté pour les recevoir. IoT Hub gère des files d’attente spécifiques à un appareil pour les commandes qu’il envoie.
* Les données de charge utile d’application sont sécurisées séparément dans le cadre d’un transit protégé via des passerelles vers un service particulier.

L’industrie mobile utilise le schéma de communication assistée par service à grande échelle afin de mettre en œuvre des services de notification Push tels que le [Service de notification Push Windows][lnk-wns], [Google Cloud Messaging][lnk-google-messaging] et [Apple Push Notification Service][lnk-apple-push].

## Étapes suivantes
Pour découvrir comment Azure IoT Hub permet une gestion des appareils basée sur IoT pour gérer à distance, configurer et mettre à jour vos appareils, consultez l’article [Vue d’ensemble de la gestion des appareils Azure IoT Hub][lnk-device-management].

Pour implémenter des applications clientes sur un large éventail de plateformes matérielles et de systèmes d’exploitation d’appareils, vous pouvez utiliser les Kits de développement logiciel (SDK) d’appareils IoT. Ces kits incluent des bibliothèques qui facilitent l’envoi de données de télémétrie vers un IoT Hub et la réception de commandes cloud-à-appareils. Avec les Kits de développement logiciel (SDK), vous avez le choix parmi divers protocoles réseau pour communiquer avec IoT Hub. Pour plus d’informations, consultez la rubrique [plus d’informations sur les kits de développement logiciel (SDK) d’appareils][lnk-device-sdks].

Pour commencer à écrire du code et à exécuter certains exemples, consultez le didacticiel [Prise en main d’IoT Hub][lnk-get-started].

[img-architecture]: media/iot-hub-what-is-iot-hub/hubarchitecture.png


[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[protocol-gateway]: https://github.com/Azure/azure-iot-protocol-gateway/blob/master/README.md
[lnk-service-assisted-pattern]: http://blogs.msdn.com/b/clemensv/archive/2014/02/10/service-assisted-communication-for-connected-devices.aspx "Communication de service assistée, billet de blog de Clemens Vasters"
[lnk-compare]: iot-hub-compare-event-hubs.md
[lnk-gateway]: iot-hub-protocol-gateway.md
[lnk-field-gateway]: iot-hub-devguide-endpoints.md#field-gateways
[lnk-devguide-identityregistry]: iot-hub-devguide-identity-registry.md
[lnk-devguide-security]: iot-hub-devguide-security.md
[lnk-wns]: https://msdn.microsoft.com/library/windows/apps/mt187203.aspx
[lnk-google-messaging]: https://developers.google.com/cloud-messaging/
[lnk-apple-push]: https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW9
[lnk-device-sdks]: https://github.com/Azure/azure-iot-sdks
[lnk-refarch]: http://download.microsoft.com/download/A/4/D/A4DAD253-BC21-41D3-B9D9-87D2AE6F0719/Microsoft_Azure_IoT_Reference_Architecture.pdf
[lnk-gateway-sdk]: https://github.com/Azure/azure-iot-gateway-sdk
[lnk-device-management]: iot-hub-device-management-overview.md

<!---HONumber=AcomDC_1005_2016-->