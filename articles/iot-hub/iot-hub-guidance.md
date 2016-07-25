<properties
 pageTitle="Guide de la solution IoT Hub | Microsoft Azure"
 description="Rubriques d'aide sur les passerelles, l'approvisionnement des appareils et l'authentification pour développer des solutions IoT avec Azure IoT Hub."
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
 ms.date="04/29/2016"
 ms.author="dobett"/>

# Conception de votre solution

Cet article vous explique comment concevoir les fonctionnalités suivantes dans votre solution IoT (Internet des objets) :

- Approvisionnement des appareils
- Passerelles de champ
- Authentification des appareils

## Approvisionnement des appareils

Les solutions IoT stockent des données sur les appareils individuels, telles que :

- Clés d'authentification et identité de l'appareil
- Version et type de matériel de l'appareil
- État de l'appareil
- Fonctionnalités et versions des logiciels
- Historique des commandes de l'appareil

Les données d’appareil qu’une solution IoT donnée stocke dépendent des exigences spécifiques de cette solution. Mais une solution doit au minimum stocker les clés d’authentification et les identités des appareils. Azure IoT Hub inclut un [registre d’identité][lnk-devguide-identityregistry] qui peut stocker des valeurs pour chaque appareil, comme les ID, les clés d’authentification et les codes d’état. Une solution peut utiliser d’autres services Azure, tels que des tables, des objets blob ou Azure DocumentDB pour stocker des données supplémentaires sur les appareils.

L'*approvisionnement des appareils* est le processus d'ajout des données d'appareil initial aux magasins dans votre solution. Pour autoriser un nouvel appareil à se connecter à votre hub, vous devez ajouter un nouvel ID et de nouvelles clés d’appareil dans le [registre d’identité IoT Hub][lnk-devguide-identityregistry]. Dans le cadre du processus d’approvisionnement, vous devrez peut-être initialiser les données spécifiques à l’appareil dans d’autres magasins de la solution.

Les [API du registre identité IoT Hub][lnk-devguide-identityregistry] vous permettent d'intégrer IoT Hub dans votre processus d'approvisionnement.

## Passerelles de champ

Dans une solution IoT, une *passerelle de champ* se situe entre vos appareils et votre IoT Hub. Elle est généralement située près de vos appareils. Vos appareils communiquent directement avec la passerelle de champ à l’aide d’un protocole pris en charge. La passerelle de champ communique avec IoT Hub à l’aide d’un protocole pris en charge par ce dernier. Une passerelle de champ peut être un matériel très spécialisé ou un ordinateur à faible consommation d'énergie exécutant un logiciel qui accomplit de bout en bout le scénario pour lequel la passerelle est prévue.

Une passerelle de champ est différente d’un appareil de routage de trafic simple, par exemple un pare-feu ou un appareil de traduction d’adresses réseau (NAT), car elle a généralement un rôle actif dans la gestion de l’accès et du flux des informations dans votre solution. Par exemple, une passerelle de champ peut :

- **Ajouter la prise en charge d’appareils nouveaux et hérités** : il existe des millions de capteurs et de mécanismes de positionnement nouveaux et hérités qui ne peuvent pas envoyer les données directement au cloud. Ces appareils utilisent un protocole qui ne convient pas pour Internet, n’implémentent aucun chiffrement, ou ne peuvent pas stocker les certificats d'identité. L’utilisation d'une passerelle facilite la connexion de ces appareils et en réduit le coût.
- **Exécuter une analyse de périmètre **: de nombreuses opérations peuvent être réalisées localement afin de réduire la quantité de données échangées avec le cloud. Les exemples incluent le filtrage des données, le traitement par lots et la compression. Il peut également être souhaitable d'effectuer certains calculs comme le nettoyage des données ou la notation d’un modèle d’apprentissage automatique avec les données réelles en local.
- **Minimiser la latence** : chaque milliseconde compte lorsque vous voulez empêcher les arrêts de la ligne de fabrication ou restaurer un service électrique. Analyser les données à proximité de l'appareil qui collecte des données peut faire la différence entre empêcher un sinistre qui entraînerait une défaillance en cascade du système.
- **Économiser la bande passante réseau** : une plateforme pétrolière offshore standard génère entre 1 et 2 To de données chaque jour. Un Boeing 787 génère la moitié d'un téraoctet de données par vol. Il n'est pas pratique de transférer vers le cloud de grandes quantités de données provenant de centaines voire de milliers d’appareils de périmètre. Et cela n'est pas obligatoire non plus car de nombreuses analyses critiques ne nécessitent pas de stockage ni de traitement à l'échelle du cloud.
- **Fonctionner de façon fiable** : les données IoT sont plus en plus utilisées pour les décisions concernant la sécurité des citoyens et les infrastructures critiques. L'intégrité et la disponibilité de l'infrastructure et des données ne peuvent pas être compromises par une mauvaise connexion au cloud. L'utilisation de fonctionnalités telles que le stockage et le transfert pour collecter et agir sur les données localement et les envoyer au cloud, le cas échéant, vous aide à créer des solutions fiables.
- **Résoudre les problèmes de sécurité et de confidentialité** : les appareils IoT et les données qu'ils produisent doivent être protégés. Les passerelles peuvent fournir des services comme isoler des appareils d'Internet, fournir des services de chiffrement et d'identité pour les appareils incapables de fournir eux-mêmes ces services, sécuriser les données mises en mémoire tampon ou stockées localement, et supprimer les informations personnelles identifiables avant leur publication sur Internet.

### Autres considérations

Vous pouvez utiliser les [Kits de développement logiciel (SDK) de passerelle Azure IoT][lnk-gateway-sdk] pour implémenter une passerelle de champ. Ces Kits de développement logiciel (SDK) offrent des fonctionnalités spécifiques comme la possibilité de multiplexer la communication à partir de plusieurs appareils sur la même connexion à IoT Hub.

## Authentification d'appareil personnalisée

Vous pouvez utiliser le [registre d'identité d'appareil][lnk-devguide-identityregistry] pour configurer les informations d'identification de sécurité et le contrôle d'accès par appareil. Cependant, si une solution IoT représente déjà un investissement significatif dans un registre d'identité d'appareil personnalisé et/ou un schéma d'authentification, vous pouvez intégrer cette infrastructure existante à IoT Hub en créant un *service de jeton*. De cette façon, vous pouvez utiliser d'autres fonctionnalités IoT dans votre solution.

Un service de jeton est un service cloud personnalisé. Il utilise une *stratégie d’accès partagé IoT Hub* avec des autorisations **DeviceConnect** pour créer des jetons *device-scoped*. Ces jetons permettent à un appareil de se connecter à votre IoT Hub.

  ![Étapes du modèle de service de jeton][img-tokenservice]

Voici les principales étapes du modèle de service de jeton :

1. Créez une [stratégie d'accès partagé IoT Hub][lnk-devguide-security] avec des autorisations **DeviceConnect** pour votre concentrateur IoT. Vous pouvez créer cette stratégie dans le [portail Azure][lnk-portal] ou par programme. Le service de jetons utilise cette stratégie pour signer les jetons qu'elle crée.
2. Lorsqu'un appareil doit accéder à votre hub IoT, il demande à votre service de jetons un jeton signé. L’appareil peut s’authentifier avec votre registre d’identité d’appareil personnalisé/schéma d’authentification pour déterminer l’identité d’appareil que le service de jeton utilise pour créer le jeton.
3. Le service de jeton renvoie un jeton. Le jeton est créé conformément à la section [Sécurité du Guide du développeur IoT Hub][lnk-devguide-security], en utilisant `/devices/{deviceId}` comme `resourceURI`, `deviceId` étant l’appareil authentifié. Le service de jeton utilise la stratégie d'accès partagé pour construire le jeton.
4. L’appareil utilise le jeton directement avec le hub IoT.

> [AZURE.NOTE] Vous pouvez utiliser la classe .NET [SharedAccessSignatureBuilder][lnk-dotnet-sas] ou la classe Java [IotHubServiceSasToken][lnk-java-sas] pour créer un jeton dans votre service de jeton.

Le service de jetons peut définir l’expiration du jeton comme vous le souhaitez. Lorsque le jeton expire, le hub IoT interrompt la connexion. L’appareil doit ensuite demander un nouveau jeton au service de jeton. Si vous utilisez un délai d'expiration court, cela accroît la charge de l'appareil et du service de jeton.

Pour qu’un appareil se connecte à votre hub, vous devez l’ajouter au Registre d’identité d’appareil IoT Hub, même si l’appareil utilise un jeton et non une clé d’appareil pour se connecter. Par conséquent, vous pouvez continuer à utiliser le contrôle d'accès par périphérique en activant ou désactivant les identités des appareils dans le [registre d'identité IoT Hub][lnk-devguide-identityregistry] lorsque l'appareil s'authentifie avec un jeton. Cela réduit les risques liés à l'utilisation de jetons avec des délais d'expiration longs.

### Comparaison avec une passerelle personnalisée

Le modèle de service de jeton est la méthode recommandée pour implémenter un schéma de registre d’identité/d’authentification avec IoT Hub. Il est recommandé, car il laisse IoT Hub gérer la plus grande partie du trafic de la solution. Cependant, il existe des cas où le schéma d’authentification personnalisé est si étroitement couplé au protocole qu’un service traitant l’ensemble du trafic (*passerelle personnalisée*) est requis. [Le protocole TLS (Transport Layer Security) et les clés prépartagées (PSK)][lnk-tls-psk] en sont un exemple. Pour plus d’informations, consultez la rubrique [Passerelle de protocole][lnk-gateway].

## Pulsation de l’appareil <a id="heartbeat"></a>

Le [registre des identités IoT Hub][lnk-devguide-identityregistry] contient un champ appelé **connectionState**. Vous ne devez utiliser le champ **connectionState** que pendant le développement et le débogage, et les solutions IoT ne doivent pas interroger le champ au moment de l’exécution (par exemple, pour vérifier si un appareil est connecté pour l’envoi éventuel d’un message cloud-à-appareil ou un SMS). Si votre solution IoT a besoin de savoir si un appareil est connecté (au moment de l’exécution ou sur la base d’informations plus précises que celles fournies par la propriété **connectionState**), votre solution doit implémenter le *modèle par pulsations*.

Dans le modèle par pulsations, l’appareil envoie des messages appareil-à-cloud au moins une fois par durée fixe (par exemple, au moins une fois par heure). Ainsi, même si un appareil n’a pas de données à envoyer, il envoie toujours un message appareil-à-cloud vide (généralement avec une propriété qui l’identifie comme pulsation). Côté service, la solution gère un mappage avec la dernière pulsation reçue pour chaque appareil et suppose qu’un problème affecte un appareil si elle ne reçoit pas un message de pulsation dans le temps imparti.

Une implémentation plus complexe pourrait inclure les informations de la [surveillance des opérations][lnk-devguide-opmon] pour identifier les appareils qui ne parviennent pas à se connecter ou à communiquer. Quand vous implémentez le modèle par pulsations, veillez à vérifier les [quotas et limitations IoT Hub][].

> [AZURE.NOTE] Si une solution IoT a uniquement besoin de l’état de la connexion de l’appareil pour déterminer si elle doit envoyer des messages cloud-à-appareil, et que ces messages ne sont pas diffusés à de larges groupes d’appareils, une solution plus simple peut être de définir un délai d’expiration court. Vous obtenez le même résultat qu’en maintenant l’état de la connexion de l’appareil avec sa pulsation, tout en étant beaucoup plus efficace. Il est également possible, en demandant des accusés de réception des messages, d’être informé par IoT Hub des appareils qui peuvent recevoir des messages et de ceux qui ne sont pas en ligne ou qui sont en état d’échec. Reportez-vous au [Guide du développeur IoT Hub][lnk-devguide-messaging] pour plus d’informations sur les messages C2D (cloud-à-appareil).

## Étapes suivantes

Pour en savoir plus sur la planification de votre déploiement IoT Hub, consultez :

- [Support MQTT][lnk-mqtt]
- [Appareils pris en charge][lnk-devices]
- [Prendre en charge des protocoles supplémentaires][lnk-protocols]
- [Comparer à Event Hubs][lnk-compare]
- [Mise à l’échelle, HA et DR][lnk-scaling]

Pour explorer davantage les capacités de IoT Hub, consultez :

- [Guide du développeur][lnk-devguide]
- [Exploration de la gestion des appareils à l’aide de l’exemple d’interface utilisateur][lnk-dmui]
- [Simulation d’un appareil avec le Kit de développement logiciel (SDK) Gateway][lnk-gateway]
- [Utilisation du portail Azure pour gérer IoT Hub][lnk-portal-manage]

[img-tokenservice]: ./media/iot-hub-guidance/tokenservice.png

[lnk-devguide-identityregistry]: iot-hub-devguide.md#identityregistry
[lnk-devguide-opmon]: iot-hub-operations-monitoring.md

[lnk-device-sdks]: iot-hub-sdks-summary.md
[lnk-devguide-security]: iot-hub-devguide.md#security
[lnk-tls-psk]: https://tools.ietf.org/html/rfc4279
[lnk-gateway]: iot-hub-protocol-gateway.md

[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[lnk-what-is-hub]: iot-hub-what-is-iot-hub.md
[lnk-portal]: https://portal.azure.com
[lnk-throttles-quotas]: ../azure-subscription-service-limits.md/#iot-hub-limits
[lnk-devguide-antispoofing]: iot-hub-devguide.md#antispoofing
[lnk-devguide-protocol]: iot-hub-devguide.md#amqpvshttp
[lnk-devguide-messaging]: iot-hub-devguide.md#messaging
[lnk-dotnet-sas]: https://msdn.microsoft.com/library/microsoft.azure.devices.common.security.sharedaccesssignaturebuilder.aspx
[lnk-java-sas]: http://azure.github.io/azure-iot-sdks/java/service/api_reference/com/microsoft/azure/iot/service/auth/IotHubServiceSasToken.html
[quotas et limitations IoT Hub]: iot-hub-devguide.md#throttling
[lnk-gateway-sdk]: https://github.com/Azure/azure-iot-gateway-sdk

[lnk-mqtt]: iot-hub-mqtt-support.md
[lnk-devices]: iot-hub-tested-configurations.md
[lnk-protocols]: iot-hub-protocol-gateway.md
[lnk-compare]: iot-hub-compare-event-hubs.md
[lnk-scaling]: iot-hub-scaling.md
[lnk-devguide]: iot-hub-devguide.md
[lnk-dmui]: iot-hub-device-management-ui-sample.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
[lnk-portal-manage]: iot-hub-manage-through-portal.md

<!---HONumber=AcomDC_0713_2016-->