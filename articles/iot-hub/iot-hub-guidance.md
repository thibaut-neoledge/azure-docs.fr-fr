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
 ms.date="11/10/2015"
 ms.author="dobett"/>

# Conception de votre solution

Cet article vous explique comment concevoir les fonctionnalités suivantes dans votre solution Internet des Objets (IoT) :

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

Les données d'appareil qu'une solution IoT donnée stocke dépendent des exigences spécifiques de cette solution, mais une solution doit au minimum stocker les clés d'authentification et les identités des appareils. IoT Hub inclut un [registre d'identité][lnk-devguide-identityregistry] qui peut stocker des valeurs pour chaque appareil, comme les ID, les clés d'authentification et les codes d'état. Une solution peut utiliser d'autres services Azure, tels que des tables, des objets Blob ou DocumentDB pour stocker des données supplémentaires sur les appareils.

L'*approvisionnement des appareils* est le processus d'ajout des données d'appareil initial aux magasins dans votre solution. Pour autoriser un nouvel appareil à se connecter à votre concentrateur, vous devez ajouter un nouvel ID et de nouvelles clés d'appareil dans le [registre d'identité IoT Hub][lnk-devguide-identityregistry]. Dans le cadre du processus d'approvisionnement, vous devrez peut-être initialiser les données spécifiques à l'appareil dans d'autres magasins de la solution.

L'article [Guide de gestion d'un appareil IoT Hub][lnk-device-management] décrit quelques stratégies courantes d'approvisionnement d'appareil. Les [API du registre identité IoT Hub][lnk-devguide-identityregistry] vous permettent d'intégrer IoT Hub dans votre processus d'approvisionnement.

## Passerelles de champ

Dans une solution IoT, une *passerelle de champ* se situe entre vos appareils et votre concentrateur IoT, et se trouve généralement proche de vos appareils. Vos appareils communiquent directement avec la passerelle de champ à l'aide d'un protocole pris en charge par les appareils et la passerelle de champ communique avec IoT Hub à l'aide d'un protocole pris en charge par IoT Hub. Une passerelle de champ peut être un appareil ou logiciel autonome spécialisé qui s'exécute sur du matériel existant.

Une passerelle de champ est différente d'un appareil de routage de trafic simple (par exemple un pare-feu ou un appareil NAT) car elle a généralement un rôle actif dans la gestion de l'accès et du flux des informations dans votre solution. Par exemple, une passerelle de champ peut :

- Gérer les appareils locaux. Par exemple, une passerelle de champ peut effectuer le traitement de règle d'événement et envoyer des commandes aux appareils en réponse à des données de télémétrie spécifiques.
- Filtrer ou regrouper des données de télémétrie avant de les transmettre à IoT Hub. Ceci peut réduire la quantité de données qui sont envoyées à IoT Hub et réduire les coûts de votre solution.
- Aider à l'approvisionnement des appareils.
- Transformer les données de télémétrie pour faciliter le traitement de votre back-end de solution.
- Effectuer la traduction de protocole pour permettre aux appareils de communiquer avec IoT Hub, même lorsqu'ils n'utilisent pas les protocoles de transport pris en charge par IoT Hub.

> [AZURE.NOTE]En général, vous déployez une passerelle de champ de façon locale pour vos appareils, mais dans certains scénarios vous pouvez déployer une [passerelle de protocole][lnk-gateway] dans le cloud.

### Types de passerelles de champ

Une passerelle de champ peut être *transparente* ou *opaque*:

| &nbsp; | Transparente | Opaque |
|--------|-------------|--------|
| Identités stockées dans le registre d'identité IoT Hub | Tous les appareils connectés | Uniquement l'identité de la passerelle de champ |
| IoT Hub peut fournir un [dispositif anti-usurpation d'identité d'appareil][lnk-devguide-antispoofing] | Oui | Non |
| [Quotas et limitations][lnk-throttles-quotas] | Appliquer à chaque périphérique | Appliquer à la passerelle de champ |

### Autres considérations

Vous pouvez utiliser les [kits de développement logiciels d'appareil Azure IoT][lnk-device-sdks] pour implémenter une passerelle de champ. Certains kits de développement logiciels d'appareil offrent des fonctionnalités spécifiques qui vous aident à implémenter une passerelle de champ, comme la possibilité de multiplexer la communication à partir de plusieurs appareils sur la même connexion à IoT Hub. Comme l'explique le [Guide du développeur IoT Hub - Choisir votre protocole de communication][lnk-devguide-protocol], évitez d'utiliser HTTP/1 comme protocole de transport pour les passerelles de champ.

## Authentification d'appareil personnalisée

Vous pouvez utiliser le [registre d'identité d'appareil][lnk-devguide-identityregistry] pour configurer les informations d'identification de sécurité et le contrôle d'accès par appareil. Cependant, si une solution IoT représente déjà un investissement significatif dans un registre d'identité d'appareil personnalisé et/ou un schéma d'authentification, vous pouvez intégrer cette infrastructure existante à IoT Hub en créant un *service de jeton*. De cette façon, vous pouvez utiliser d'autres fonctionnalités IoT dans votre solution.

Un service de jeton est un service cloud personnalisé, qui utilise une *stratégie d'accès partagé* IoT Hub avec des autorisations **DeviceConnect** et qui permet de créer des jetons inclus dans l'*étendue de l'appareil* pour la connexion à votre concentrateur IoT.

  ![][img-tokenservice]

Voici les principales étapes du modèle de service de jeton :

1. Créez une [stratégie d'accès partagé IoT Hub][lnk-devguide-security] avec des autorisations **DeviceConnect** pour votre concentrateur IoT. Vous pouvez créer cette stratégie dans le [portail Azure][lnk-portal] ou par programme. Le service de jetons utilise cette stratégie pour signer les jetons qu'elle crée.
2. Lorsqu'un appareil doit accéder à votre concentrateur IoT, il demande à votre service de jetons un jeton signé. L'appareil peut s'authentifier avec votre registre d'identité d'appareil personnalisé/schéma d'authentification pour déterminer l'identité d'appareil que le service de jeton utilise pour créer le jeton.
3. Le service de jeton renvoie un jeton créé conformément à la section [Guide du développeur IoT Hub - Sécurité][lnk-devguide-security], en utilisant `/devices/{deviceId}` comme `resourceURI`, `deviceId` étant l'appareil en cours d'authentification. Le service de jeton utilise la stratégie d'accès partagé pour construire le jeton.
4. Le périphérique utilise le jeton directement avec le hub IoT.

> [AZURE.NOTE]Vous pouvez utiliser la classe .NET [SharedAccessSignatureBuilder][lnk-dotnet-sas] ou la classe Java [IotHubServiceSasToken][lnk-java-sas] pour créer un jeton dans votre service de jeton.

Le service de jetons peut définir l’expiration du jeton comme vous le souhaitez. Lorsque le jeton expire, le concentrateur IoT interrompt la connexion et l'appareil doit demander un nouveau jeton au service de jeton. Si vous utilisez un délai d'expiration court, cela accroît la charge de l'appareil et du service de jeton.

Pour qu'un appareil se connecte à votre concentrateur, vous devez l'ajouter au Registre identité d'appareil IoT Hub, même si l'appareil utilise un jeton et non une clé d'appareil pour se connecter. Par conséquent, vous pouvez continuer à utiliser le contrôle d'accès par périphérique en activant ou désactivant les identités des appareils dans le [registre d'identité IoT Hub][lnk-devguide-identityregistry] lorsque l'appareil s'authentifie avec un jeton. Cela réduit les risques liés à l'utilisation de jetons avec des délais d'expiration longs.

### Comparaison avec une passerelle personnalisée

Le modèle de service de jeton est la méthode recommandée pour implémenter un schéma de registre d'identité/d'authentification avec IoT Hub, car IoT Hub continue de gérer la plus grande partie du trafic de la solution. Cependant , il existe des cas où le schéma d'authentification personnalisé est si étroitement couplé au protocole (par exemple, [TLS-PSK][lnk-tls-psk]) qu'un service traitant l'ensemble du trafic (*passerelle personnalisée*) est requis. Pour plus d'informations, consultez la rubrique [Passerelle de protocole][lnk-gateway].

## Étapes suivantes

Suivez ces liens pour en savoir plus sur Azure IoT Hub :

- [Prise en main d'IoT Hub (didacticiel)][lnk-get-started]
- [Qu’est-ce qu’Azure IoT Hub ?][lnk-what-is-hub]

[img-tokenservice]: ./media/iot-hub-guidance/tokenservice.png

[lnk-devguide-identityregistry]: iot-hub-devguide.md#identityregistry
[lnk-device-management]: iot-hub-device-management.md

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

<!----HONumber=AcomDC_1203_2015-->