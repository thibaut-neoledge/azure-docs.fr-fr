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
 ms.date="02/03/2016"
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

L'article [Guide de gestion d'un appareil IoT Hub][lnk-device-management] décrit quelques stratégies courantes d'approvisionnement d'appareil. Les [API du registre identité IoT Hub][lnk-devguide-identityregistry] vous permettent d'intégrer IoT Hub dans votre processus d'approvisionnement.

## Passerelles de champ

Dans une solution IoT, une *passerelle de champ* se situe entre vos appareils et votre IoT Hub. Elle est généralement située près de vos appareils. Vos appareils communiquent directement avec la passerelle de champ à l’aide d’un protocole pris en charge. La passerelle de champ communique avec IoT Hub à l’aide d’un protocole pris en charge par ce dernier. Une passerelle de champ peut être un appareil ou logiciel autonome spécialisé qui s'exécute sur du matériel existant.

Une passerelle de champ est différente d’un appareil de routage de trafic simple, par exemple un pare-feu ou un appareil de traduction d’adresses réseau (NAT), car elle a généralement un rôle actif dans la gestion de l’accès et du flux des informations dans votre solution. Par exemple, une passerelle de champ peut :

- Gérer les appareils locaux. Par exemple, une passerelle de champ peut effectuer le traitement de règle d'événement et envoyer des commandes aux appareils en réponse à des données de télémétrie spécifiques.
- Filtrer ou regrouper des données de télémétrie avant de les transmettre à IoT Hub. Ceci peut réduire la quantité de données qui sont envoyées à IoT Hub et réduire les coûts de votre solution.
- Aider à l'approvisionnement des appareils.
- Transformer les données de télémétrie pour faciliter le traitement de votre back-end de solution.
- Effectuer la traduction de protocole pour permettre aux appareils de communiquer avec IoT Hub, même lorsqu’ils n’utilisent pas les protocoles de transport pris en charge par IoT Hub.

> [AZURE.NOTE] En général, vous déployez une passerelle de champ de façon locale pour vos appareils, mais dans certains scénarios vous pouvez déployer une [passerelle de protocole][lnk-gateway] dans le cloud.

### Types de passerelles de champ

Une passerelle de champ peut être *transparente* ou *opaque* :

| &nbsp; | Passerelle transparente | Passerelle opaque|
|--------|-------------|--------|
| Identités stockées dans le registre d’identité IoT Hub | Identités de tous les appareils connectés | Uniquement l'identité de la passerelle de champ |
| IoT Hub peut fournir un [dispositif anti-usurpation d'identité d'appareil][lnk-devguide-antispoofing] | Oui | Non |
| [Quotas et limitations][lnk-throttles-quotas] | Appliquer à chaque appareil | Appliquer à la passerelle de champ |

> [AZURE.IMPORTANT]  Quand vous utilisez un modèle de passerelle opaque, tous les appareils qui se connectent par le biais de la passerelle partagent la même file d’attente cloud-à-appareil, qui peut contenir au maximum 50 messages. Ainsi, le modèle de passerelle opaque ne doit être utilisé que quand très peu d’appareils se connectent par le biais de chaque passerelle de champ et que leur trafic cloud-à-appareil est faible.

### Autres considérations

Vous pouvez utiliser les [kits de développement logiciels d'appareil Azure IoT][lnk-device-sdks] pour implémenter une passerelle de champ. Certains Kits de développement logiciel (SDK) d’appareil offrent des fonctionnalités spécifiques qui vous aident à implémenter une passerelle de champ, comme la possibilité de multiplexer la communication à partir de plusieurs appareils sur la même connexion à IoT Hub. Comme l’explique le [Guide du développeur IoT Hub - Choisir votre protocole de communication][lnk-devguide-protocol], évitez d’utiliser HTTP/1 comme protocole de transport pour les passerelles de champ.

## Authentification d'appareil personnalisée

Vous pouvez utiliser le [registre d'identité d'appareil][lnk-devguide-identityregistry] pour configurer les informations d'identification de sécurité et le contrôle d'accès par appareil. Cependant, si une solution IoT représente déjà un investissement significatif dans un registre d'identité d'appareil personnalisé et/ou un schéma d'authentification, vous pouvez intégrer cette infrastructure existante à IoT Hub en créant un *service de jeton*. De cette façon, vous pouvez utiliser d'autres fonctionnalités IoT dans votre solution.

Un service de jeton est un service cloud personnalisé. Il utilise une *stratégie d’accès partagé IoT Hub* avec des autorisations **DeviceConnect** pour créer des jetons *device-scoped*. Ces jetons permettent à un appareil de se connecter à votre IoT Hub.

  ![Étapes du modèle de service de jeton][img-tokenservice]

Voici les principales étapes du modèle de service de jeton :

1. Créez une [stratégie d'accès partagé IoT Hub][lnk-devguide-security] avec des autorisations **DeviceConnect** pour votre hub IoT. Vous pouvez créer cette stratégie dans le [portail Azure][lnk-portal] ou par programme. Le service de jetons utilise cette stratégie pour signer les jetons qu'elle crée.
2. Lorsqu'un appareil doit accéder à votre hub IoT, il demande à votre service de jetons un jeton signé. L’appareil peut s’authentifier avec votre registre d’identité d’appareil personnalisé/schéma d’authentification pour déterminer l’identité d’appareil que le service de jeton utilise pour créer le jeton.
3. Le service de jeton renvoie un jeton. Le jeton est créé conformément à la section [Sécurité du Guide du développeur IoT Hub][lnk-devguide-security], en utilisant `/devices/{deviceId}` comme `resourceURI`, `deviceId` étant l’appareil authentifié. Le service de jeton utilise la stratégie d'accès partagé pour construire le jeton.
4. L’appareil utilise le jeton directement avec le hub IoT.

> [AZURE.NOTE] Vous pouvez utiliser la classe .NET [SharedAccessSignatureBuilder][lnk-dotnet-sas] ou la classe Java [IotHubServiceSasToken][lnk-java-sas] pour créer un jeton dans votre service de jeton.

Le service de jetons peut définir l’expiration du jeton comme vous le souhaitez. Lorsque le jeton expire, le hub IoT interrompt la connexion. L’appareil doit ensuite demander un nouveau jeton au service de jeton. Si vous utilisez un délai d'expiration court, cela accroît la charge de l'appareil et du service de jeton.

Pour qu’un appareil se connecte à votre hub, vous devez l’ajouter au Registre d’identité d’appareil IoT Hub, même si l’appareil utilise un jeton et non une clé d’appareil pour se connecter. Par conséquent, vous pouvez continuer à utiliser le contrôle d'accès par appareil en activant ou désactivant les identités des appareils dans le [registre d'identité IoT Hub][lnk-devguide-identityregistry] lorsque l'appareil s'authentifie avec un jeton. Cela réduit les risques liés à l'utilisation de jetons avec des délais d'expiration longs.

### Comparaison avec une passerelle personnalisée

Le modèle de service de jeton est la méthode recommandée pour implémenter un schéma de registre d’identité/d’authentification avec IoT Hub. Il est recommandé, car il laisse IoT Hub gérer la plus grande partie du trafic de la solution. Cependant, il existe des cas où le schéma d’authentification personnalisé est si étroitement couplé au protocole qu’un service traitant l’ensemble du trafic (*passerelle personnalisée*) est requis. [Le protocole TLS (Transport Layer Security) et les clés prépartagées (PSK)][lnk-tls-psk] en sont un exemple. Pour plus d’informations, consultez la rubrique [Passerelle de protocole][lnk-gateway].

## Pulsation de l’appareil <a id="heartbeat"></a>

Le [Registre d’identité IoT Hub][lnk-devguide-identityregistry] contient un champ appelé **connectionState**. Vous ne devez utiliser le champ **connectionState** que pendant le développement et le débogage, et les solutions IoT ne doivent pas interroger le champ au moment de l’exécution (par exemple, pour vérifier si un appareil est connecté afin d’envoyer éventuellement un message cloud-à-appareil ou un SMS). Si votre solution IoT a besoin de savoir si un appareil est connecté (au moment de l’exécution ou sur la base d’informations plus précises que celles fournies par la propriété **connectionState**), votre solution doit implémenter le *modèle par pulsations*.

Dans le modèle par pulsations, l’appareil envoie des messages appareil-à-cloud au moins une fois par durée fixe (par exemple, au moins une fois par heure). Ainsi, même si un appareil n’a pas de données à envoyer, il envoie toujours un message appareil-à-cloud vide (généralement avec une propriété qui l’identifie comme pulsation). Côté service, la solution gère un mappage avec la dernière pulsation reçue pour chaque appareil et suppose qu’un problème affecte un appareil si elle ne reçoit pas un message de pulsation dans le temps imparti.

Une implémentation plus complexe pourrait inclure les informations de la [surveillance des opérations][lnk-devguide-opmon] pour identifier les appareils qui ne parviennent pas à se connecter ou à communiquer. Quand vous implémentez le modèle par pulsations, veillez à vérifier les [quotas et limitations IoT Hub][].

## Étapes suivantes

Suivez ces liens pour en savoir plus sur Azure IoT Hub :

- [Prise en main d'IoT Hub (didacticiel)][lnk-get-started]
- [Qu’est-ce qu’Azure IoT Hub ?][lnk-what-is-hub]

[img-tokenservice]: ./media/iot-hub-guidance/tokenservice.png

[lnk-devguide-identityregistry]: iot-hub-devguide.md#identityregistry
[lnk-device-management]: iot-hub-device-management.md
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
[lnk-dotnet-sas]: https://msdn.microsoft.com/library/microsoft.azure.devices.common.security.sharedaccesssignaturebuilder.aspx
[lnk-java-sas]: http://azure.github.io/azure-iot-sdks/java/service/api_reference/com/microsoft/azure/iot/service/auth/IotHubServiceSasToken.html
[quotas et limitations IoT Hub]: iot-hub-devguide.md#throttling

<!---HONumber=AcomDC_0204_2016-->