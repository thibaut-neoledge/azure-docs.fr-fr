<properties
 pageTitle="Rubriques de conseils pour l’utilisation d’Azure IoT Hub | Microsoft Azure"
 description="Un ensemble de rubriques d’aide pour le développement de solutions utilisant Azure IoT Hub."
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

# IoT Hub Azure - Guide

## Approvisionnement des appareils

Les solutions IoT assurent la mise à jour des informations d’appareil dans de nombreux systèmes et magasins de stockage. Le [registre d'identité IoT Hub][IoT Hub Developer Guide - identity registry] est un magasin parmi d’autres et gère les informations des appareils spécifiques à l’application. Nous appelons *approvisionnement* le processus de création des informations obligatoires relatives à l’appareil dans tous ces systèmes.

Il existe de nombreuses exigences et stratégies d’approvisionnement d’appareils (voir [Guide de gestion d’un appareil IoT Hub] pour plus d’informations). Le [registre d’identité IoT Hub][IoT Hub Developer Guide - identity registry] fournit les API dont vous avez besoin pour intégrer IoT Hub à votre processus d’approvisionnement.

## Passerelles de champ

Une passerelle de champ est un équipement ou matériel spécial ou un logiciel à usage général qui agit en tant que qu’activateur de communication, mais peut également servir de système de contrôle d’appareil local et de concentrateur de traitement de données de l’appareil. Une passerelle de champ peut d’une part exécuter les fonctions de traitement et de contrôle vers les appareils et, d’autre part, filtrer ou agréger les mesures de la télémétrie de l’appareil et réduire la quantité de données transférée vers le serveur principal.

La portée d’une passerelle de champ inclut la passerelle de champ elle-même et tous les appareils qui lui sont associés. Comme son nom l’indique, les passerelles de champ fonctionnent hors des installations de traitement des données et sont généralement associées à un site. Une passerelle de champ est différente d’un routeur de trafic simple, car elle joue un rôle actif dans la gestion des accès et des flux d’information. Cela signifie qu’il s’agit d’une entité adressée à une application et d’une connexion réseau ou d’un terminal de session (par exemple, les passerelles dans ce contexte peuvent aider à la configuration de l’appareil, la transformation des données, la conversion de protocole et le traitement des règles d’événement). Les périphériques NAT ou les pare-feu, au contraire, ne sont pas qualifiés de passerelles de champ, car il ne s’agit pas de terminaux de connexion ou de session. Ils acheminent (ou refusent) les connexions ou les sessions qui transitent par eux.

### Passerelle de champ transparente ou opaque

En ce qui concerne les identités d’appareils, les passerelles de champ sont *transparentes* si d’autres appareils à leur portée ont des entrées d’identité d’appareil dans le registre d’identités du hub IoT. Elles sont qualifiées d’*opaque* lorsque la seule identité présente dans le registre d’identités d’IoT Hub est celle de la passerelle de champ.

Il est important de noter que l’utilisation d’une passerelle *opaque* empêche IoT Hub d’offrir le [dispositif anti-usurpation d’identité d’appareil][IoT Hub Developer Guide - Anti-spoofing]. En outre, comme tous les appareils de la portée de la passerelle de champ sont représentés comme un appareil unique dans le hub IoT, ils sont soumis à des seuils et des quotas en tant qu’appareil unique.

### Autres considérations

Lors de l’implémentation d’une passerelle de champ, vous pouvez utiliser les [kits de développement logiciels d’appareil Azure IoT][]. Certains kits de développement logiciels fournissent des fonctionnalités de passerelle de champ spécifiques, notamment la possibilité de multiplexer plusieurs périphériques de communication sur la même connexion vers IoT Hub. Comme l’explique le [Guide du développeur IoT Hub - Choisir votre protocole de communication][], évitez d’utiliser HTTP/1 comme protocole de transport pour les passerelles de champ.

## Utilisation de schémas/services d’authentification d’appareil personnalisés

IoT Hub vous permet de configurer les informations d’identification de sécurité et le contrôle d’accès par appareil à l’aide du [Registre d’identité d’appareil][IoT Hub Developer Guide - identity registry].

Si une solution IoT représente déjà un investissement significatif dans un registre d’identité de périphérique personnalisé et/ou un schéma d’authentification, elle peut encore utiliser d’autres fonctionnalités IoT Hub en créant un *service de jeton* pour IoT Hub.

Le service de jeton est un service cloud à déploiement automatique, qui utilise une *stratégie d’accès partagé* IoT Hub avec des autorisations **DeviceConnect** et qui permet de créer des jetons inclus dans l’*étendue de l’appareil*.

  ![][img-tokenservice]

Voici les principales étapes du schéma de service de jeton.

1. Créez une [stratégie d’accès partagé IoT Hub][IoT Hub Developer Guide - Security] avec des autorisations **DeviceConnect** pour votre hub IoT. Cette stratégie sera utilisée par le service de jetons pour signer les jetons.
2. Lorsqu’un périphérique souhaite accéder au hub IoT, il demande à votre service de jetons un jeton signé. Le périphérique peut utiliser votre schéma de registre d’identité/d’authentification personnalisé.
3. Ce service de jetons renvoie un jeton créé conformément à la section [Guide du développeur IoT Hub - Sécurité][], en utilisant `/devices/{deviceId}` comme `resourceURI`, `deviceId` étant l’appareil en cours d’authentification.
4. Le périphérique utilise le jeton directement avec le hub IoT.

Le service de jetons peut définir l’expiration du jeton comme vous le souhaitez. À expiration, le hub IoT interrompt la connexion et l’appareil doit demander un nouveau jeton au service de jetons. Il est clair qu’un délai d’expiration court accroît la charge de l’appareil et du service de jetons.

Il faut préciser que l’identité de l’appareil doit être créée dans le hub IoT pour que l’appareil puisse se connecter. Cela signifie également que ce contrôle d’accès par appareil (en désactivant les identités des appareils conformément au [Guide du développeur IoT Hub - Registre identité][]), est toujours en service, même si l’appareil s’authentifie avec un jeton. Cela réduit l’existence de jetons de longue durée.

### Comparaison avec une passerelle personnalisée

Le modèle de service de jetons est la méthode recommandée pour implémenter un schéma de registre d’identité/d’authentification avec IoT Hub, car il laisse IoT Hub gérer la plus grande partie du trafic de la solution. Il existe des cas, cependant, où le schéma d’authentification personnalisé est si étroitement couplé au protocole (par exemple, [TLS-PSK][]) qu’un service traitant l’ensemble du trafic (*passerelle personnalisée*) est requis. Pour plus d’informations, consultez la rubrique [Passerelle de protocole][].

## Étapes suivantes

Suivez ces liens pour en savoir plus sur Azure IoT Hub :

- [Prise en main d'IoT Hub (didacticiel)][lnk-get-started]
- [Qu’est-ce qu’Azure IoT Hub ?][]

[img-tokenservice]: ./media/iot-hub-guidance/tokenservice.png

[IoT Hub Developer Guide - identity registry]: iot-hub-devguide.md#identityregistry
[Guide du développeur IoT Hub - Registre identité]: iot-hub-devguide.md#identityregistry
[Guide de gestion d’un appareil IoT Hub]: iot-hub-device-management.md
[IoT Hub Developer Guide - Anti-spoofing]: iot-hub-devguide.md#antispoofing
[kits de développement logiciels d’appareil Azure IoT]: iot-hub-sdks-summary.md
[Guide du développeur IoT Hub - Choisir votre protocole de communication]: iot-hub-devguide.md#amqpvshttp
[IoT Hub Developer Guide - Security]: iot-hub-devguide.md#security
[Guide du développeur IoT Hub - Sécurité]: iot-hub-devguide.md#security
[TLS-PSK]: https://tools.ietf.org/html/rfc4279
[Passerelle de protocole]: iot-hub-protocol-gateway.md

[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[Qu’est-ce qu’Azure IoT Hub ?]: iot-hub-what-is-iot-hub.md

<!---HONumber=Nov15_HO3-->