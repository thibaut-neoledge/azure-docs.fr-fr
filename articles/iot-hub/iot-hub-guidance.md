<properties
 pageTitle="Recommandations en matière IoT Hub | Microsoft Azure"
 description="Un ensemble de rubriques d’aide pour les solutions utilisant IoT Hub Azure."
 services="iot-hub"
 documentationCenter=".net"
 authors="fsautomata"
 manager="kevinmil"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="tbd"
 ms.date="09/29/2015"
 ms.author="elioda"/>

# IoT Hub Azure - Guide

## Comparaison entre IoT Hub et Event Hubs
L’une des principales utilisations d’Azure IoT Hub consiste à rassembler des mesures de télémétrie relevées par des appareils. C’est pour cette raison qu’IoT Hub est souvent comparé à [Event Hubs], qui est un service de traitement d’événement qui permet de créer des entrées d’événement et de télémétrie sur le cloud à grande échelle, avec une faible latence et une grande fiabilité.

Les services, cependant, présentent de nombreuses différences répertoriées dans les sections qui suivent.

| Domaine | IoT Hub | Event Hubs |
| ---- | ------- | ---------- |
| Modèles de communication | Entrée d’événement appareil vers cloud et messagerie cloud vers appareil. | Entrée d’événement seulement (en général utilisé pour les scénarios appareil vers cloud). |
| Sécurité | Identité par périphérique et contrôle d’accès révocable. Reportez-vous à la section [Guide du développeur IoT Hub - Sécurité]. | [Stratégies d’accès partagé][Event Hub - security] avec prise en charge de révocation limitée à l’aide des [stratégies de l’éditeur][Event Hub publisher policies]. Dans le cadre des solutions IoT, il est souvent nécessaire de mettre en œuvre une solution personnalisée pour prendre en charge les informations d’identification par périphérique et les mesures anti-usurpation d’identité. |
| Mise à l'échelle | Les concentrateurs IoT Hub sont optimisés pour prendre en charge des millions d’appareils connectés simultanément. | Les concentrateurs d’événements peuvent prendre en charge un nombre de connexions simultanées moindre : jusqu’à 5 000 connexions AMQP, conformément à ce qui est défini dans [Quotas Service Bus]. En revanche, Event Hubs permet aux utilisateurs de spécifier une partition pour chaque message envoyé. |
| Kits de développement logiciel (SDK) d’appareil | IoT Hub fournit [des kits de développement logiciel d’appareil][Azure IoT Hub SDKs] pour une grande variété de plateformes et de langages | L’option Event hubs est prise en charge sur .NET, C et offre des interfaces d’envoi HTTP et AMQP. |

En conclusion, même si le seul cas d’utilisation est l’entrée de mesures de télémétrie d’un appareil vers le cloud, IoT Hub offre un service spécialement conçu pour la connectivité d’appareil IoT et va continuer de développer les propositions de valeur de ces scénarios grâce à des fonctionnalités IoT spécifiques. Event Hubs est conçu pour saisir des événements à grande échelle, dans les contextes inter et intra centres de données. Il n’est pas rare d’utiliser à la fois IoT Hub et Event Hubs. Le premier gère les communications appareil vers cloud, et le second assure la saisie des événements dans des moteurs de traitement en temps réel.

## Approvisionnement des appareils <a id="provisioning"></a>
Les solutions IoT assurent la mise à jour des informations d’appareil dans de nombreux systèmes et magasins de stockage, et le [registre d’identité IoT Hub][IoT Hub Developer Guide - identity registry] est un magasin parmi d’autres et gère les informations de périphériques et gère les données d’appareils spécifiques à l’application. Nous appelons *approvisionnement* le processus de création des informations obligatoires relatives à l’appareil dans tous ces systèmes.

Il existe de nombreuses exigences et stratégies d’approvisionnement de périphériques (se référer à [Guide de gestion d’un appareil IoT Hub] pour plus d’informations). Le [registre d’identité IoT Hub][IoT Hub Developer Guide - identity registry] fournit les API dont vous avez besoin pour intégrer IoT Hub à votre processus d’approvisionnement.

## Passerelles de champ <a id="fieldgateways"></a>
Une passerelle de champ est un équipement ou matériel spécial ou un logiciel à usage général qui agit en tant que qu’activateur de communication, mais peut également servir de système de contrôle d’appareil local et de concentrateur de traitement de données de l’appareil. Une passerelle de champ peut d’une part exécuter les fonctions de traitement et de contrôle vers les appareils et, d’autre part, filtrer ou agréger les mesures de la télémétrie de l’appareil et réduire la quantité de données transférée vers le serveur principal.

La portée de la passerelle de champ inclut la passerelle de champ elle-même et tous les appareils qui lui sont associés. Comme son nom l’indique, les passerelles de champ fonctionnent hors des installations de traitement des données et sont généralement associées à un site. Une passerelle de champ est différente d’un routeur de trafic simple, car elle joue un rôle actif dans la gestion des accès et des flux d’information. Cela signifie qu’il s’agit d’une entité adressée à une application et d’une connexion réseau ou d’un terminal de session (par exemple, les passerelles dans ce contexte peuvent aider à la configuration de l’appareil, la transformation des données, la conversion de protocole et le traitement des règles d’événement). Les périphériques NAT ou les pare-feu, au contraire, ne sont pas qualifiés de passerelles de champ, car il ne s’agit pas de terminaux de connexion ou de session. Ils acheminent (ou refusent) les connexions ou les sessions qui transitent par eux.

### Passerelle de champ transparente ou opaque
En ce qui concerne les identités d’appareils, les passerelles de champ sont *transparentes* si d’autres périphériques à leur portée ont des entrées d’identité d’appareil dans le registre d’identités du concentrateur IoT. Elles sont qualifiées d’*opaque* lorsque la seule identité présente dans le registre d’identités d’IoT Hub est celle de la passerelle de champ.

Il est important de noter que l’utilisation d’une passerelle *opaque* empêche IoT Hub d’offrir le [dispositif anti-usurpation d’identité de périphérique][IoT Hub Developer Guide - Anti-spoofing]. En outre, comme tous les appareils de la portée de la passerelle de champ sont représentés comme un appareil unique dans le concentrateur IoT, ils sont soumis à des seuils et des quotas en tant que périphérique unique.

### Autres considérations

Lors de l’implémentation d’une passerelle de champ, vous pouvez utiliser les kits de développement logiciels d’appareil Azure IoT. Certains kits de développement logiciels fournissent des fonctionnalités de passerelle de champ spécifiques, notamment la possibilité de multiplexer plusieurs périphériques de communication sur la même connexion vers IoT Hub. Comme l’explique le [Guide du développeur IoT Hub - Choisir votre protocole de communication], évitez d’utiliser HTTP/1 comme protocole de transport pour les passerelles de champ.

## Utilisation de schémas/services d’authentification d’appareil personnalisés. <a id="customauth"></a>
IoT Hub Azure permet de configurer les informations d’identification de sécurité et le contrôle d’accès par périphérique à l’aide du [Registre d’identité d’appareil][IoT Hub Developer Guide - identity registry]. Si une solution IoT représente déjà un investissement significatif dans un registre d’identité de périphérique personnalisé et/ou un schéma d’authentification, elle peut encore tirer parti des autres fonctionnalités IoT Hub en créant un *service de jeton* pour IoT Hub.

Le service de jeton est un service cloud à déploiement automatique, qui utilise une stratégie d’accès partagé IoT Hub avec des autorisations **DeviceConnect** et qui permet de créer des jetons *inclus dans l’étendue de périphérique*.

  ![][img-tokenservice]

Voici les principales étapes du schéma de service de jeton.

1. Créez une [stratégie d’accès partagé IoT Hub][IoT Hub Developer Guide - Security] avec des autorisations **DeviceConnect** pour votre concentrateur IoT. Cette stratégie sera utilisée par le service de jetons pour signer les jetons.
2. Lorsqu’un périphérique souhaite accéder au concentrateur IoT, il demande à votre service de jetons un jeton signé. Le périphérique peut utiliser votre schéma de registre d’identité/d’authentification personnalisé.
3. Ce service de jetons renvoie un jeton créé conformément à la section [Guide du développeur IoT Hub - Sécurité], en utilisant `/devices/{deviceId}` comme `resourceURI`, `deviceId` étant le périphérique en cours d’authentification.
4. Le périphérique utilise le jeton directement avec le concentrateur IoT Hub.

Le service de jetons peut définir l’expiration du jeton comme vous le souhaitez. À expiration, le concentrateur IoT interrompra la connexion et l’appareil devra demander un nouveau jeton au service de jetons. Il est clair qu’un délai d’expiration court accroît la charge de l’appareil et du service de jetons.

Il faut préciser que l’identité du périphérique doit être créée dans le concentrateur IoT pour que le périphérique puisse se connecter. Cela signifie également que ce contrôle d’accès par appareil (en désactivant les identités des appareils conformément au [Guide du développeur IoT Hub - Registre identité]), est toujours en service, même si l’appareil s’authentifie avec un jeton. Cela réduit l’existence de jetons de longue durée.

### Comparaison avec une passerelle personnalisée
Le modèle de service de jetons est la méthode recommandée pour implémenter un schéma de registre d’identité/d’authentification avec IoT Hub, car il laisse IoT Hub gérer la plus grande partie du trafic de la solution. Il existe des cas, cependant, où le schéma d’authentification personnalisé est si étroitement couplé au protocole (par exemple, [TLS-PSK]) qu’un service traitant l’ensemble du trafic (*passerelle personnalisée*) est requis. Reportez-vous à l’article [Passerelle de protocole] pour plus d’informations.

## Mise à l’échelle IoT Hub
IoT Hub peut prendre en charge jusqu’à un million de périphériques connectés simultanément, en augmentant le nombre d’unités IoTHub S1 ou d’unités S2 à 2 000. Reportez-vous à [Tarification IoT Hub][lnk-pricing] pour plus d’informations.

Chaque unité IoT Hub autorise un certain nombre d’identités d’appareils pouvant être connectées simultanément dans le registre, et un nombre de messages quotidiens.

Pour mettre correctement à l’échelle votre solution, vous devez étudier votre utilisation particulière d’IoT Hub. Prenez plus particulièrement en compte le débit maximal requis pour les catégories d’opérations suivantes :

* Messages Appareil vers cloud
* Messages Cloud vers appareil
* Opérations du registre d’identité

Outre ces informations sur le débit, n’oubliez pas de faire référence aux [Quotas et limites IoT Hub] et de concevoir votre solution en conséquence.

### Débit de message Appareil vers cloud et Cloud vers appareil.
La meilleure façon de dimensionner une solution IoT Hub consiste à évaluer le trafic sur une base par périphérique.

Les messages Appareil vers cloud respectent les recommandations de débit soutenu.

| Niveau | Débit soutenu | Vitesse de transmission soutenue |
| ---- | -------------------- | ------------------- |
| S1 | jusqu’à 8 Ko/h par périphérique | moyenne de 4 messages/heure par périphérique |
| S2 | jusqu’à 4 Ko/min par périphérique | moyenne de 2 messages/min par périphérique |

Lors de la réception des messages appareil vers cloud, l’application principale peut attendre les débits maximum suivants (pour tous les lecteurs).

| Niveau | Débit soutenu |
| ---- | -------------------- |
| S1 | jusqu’à 120 Ko/min par unité, avec 2 Mo/s minimum. |
| S2 | jusqu’à 4 Mo/min par unité, avec 2 Mo/s minimum. |

Échelles de performance des messages cloud vers appareil, chaque appareil recevant jusqu’à 5 messages par minute.

### Débit des opérations de registre d’identité
Les opérations de registre d’identité IoT Hub ne sont pas censées être des opérations d’exécution, car elles sont principalement liées à la configuration de périphériques. Reportez-vous à la section [Quotas et limites IoT Hub] pour obtenir les pics de performances spécifiques.

### Partitionnement
Un concentrateur IoT unique peut recevoir des millions d’appareils, mais votre solution nécessite parfois des caractéristiques de performances spécifiques qu’un seul IoT Hub ne peut pas assurer. Dans ce cas, il est conseillé de partitionner vos périphériques sur plusieurs concentrateurs IoT, afin de lisser les pics de trafic et d’obtenir les débits et les vitesses de fonctionnement requis.

## Haute disponibilité et récupération d’urgence
En tant que service Azure, IoT Hub offre la haute disponibilité à l’aide de redondances au niveau de la région Azure, sans aucun travail supplémentaire requis par la solution. En outre, Azure offre certaines fonctions qui aident à construire des solutions avec des fonctionnalités de récupération d’urgence ou disponibilité inter-régions si nécessaire. Les solutions doivent être conçues et préparées pour tirer parti de ces fonctionnalités afin de fournir une haute disponibilité globale inter-régions aux appareils et à l’utilisateur. L’article [Guide technique pour la continuité des activités Azure] décrit les fonctionnalités intégrées Azure permettant la continuité d’activité et la récupération d’urgence. Le document [Récupération d’urgence et haute disponibilité des applications Azure] contient des recommandations en matière d’architecture sur les stratégies pour les applications Azure permettant d’atteindre HADR.

## Basculement régional avec IoT Hub
Un traitement complet des topologies de déploiement dans les solutions IoT est hors du propos de cette section,mais, pour les besoins de la haute disponibilité et de la récupération d’urgence, nous allons prendre en compte le modèle de déploiement *basculement régional*.

Dans un modèle de basculement régional, le serveur principal de solution s’exécute principalement sur un site de centre de données, mais un concentrateur IoT et le serveur principal supplémentaire seront déployés dans une autre région de centre de données à des fins de basculement, au cas où le concentrateur IoT du centre de données principal subirait une panne ou si la connectivité réseau du centre de données principal était coupée. Les périphériques utilisent un point de terminaison secondaire à chaque fois que la passerelle principale ne peut pas être atteinte. Grâce à la capacité de basculement entre régions, il est possible d’améliorer la disponibilité de la solution au-delà de la haute disponibilité au niveau d’une seule région.

À un niveau élevé, pour implémenter un modèle de basculement régional IoT Hub, vous aurez besoin des éléments suivants.

* **Un concentrateur IoT secondaire et un périphérique logique de routage secondaire** : en cas d’interruption de service dans votre région principale, les périphériques doivent commencer à se connecter à votre région secondaire. Étant donné l’état de la plupart des services impliqués, il est courant pour les administrateurs de solution de déclencher le processus de basculement inter-régions. La meilleure façon de communiquer le nouveau point de terminaison aux appareils tout en conservant le contrôle du processus consiste à consulter régulièrement un service de *conciergerie* pour connaître le point de terminaison actif en cours. Le service de concierge peut être une simple application web répliquée accessible à l’aide de techniques de redirection DNS (par exemple, l’utilisation d’[Azure Traffic Manager]).
* **Réplication du registre d’identité** : pour être utilisable, le concentrateur IoT secondaire doit contenir toutes les identités d’appareils devant être en mesure de se connecter à la solution. La solution doit conserver des sauvegardes géo-répliquées d’identités d’appareils et les télécharger dans le concentrateur IoT secondaire avant de basculer le point de terminaison actif des appareils. La fonctionnalité d’exportation d’identité de périphérique IoT Hub est très utile dans ce contexte, [Guide du développeur IoT Hub - Registre d’identité].
* **Fusion logique** : lorsque la région principale redevient disponible, l’ensemble des états et des données qui ont été créés dans le site secondaire doit revenir à la région primaire. Cette opération est fonction des identités d’appareil et des métadonnées d’application, qui devront être fusionnées avec le concentrateur IoT principal et vraisemblablement, d’autres applications, sont stockées dans la région primaire. Pour simplifier cette opération, il est généralement recommandé d’utiliser des opérations idempotentes. Cela réduit les effets secondaires d’une éventuelle distribution continue d’événements, mais également ceux des doublons ou de la livraison d’événements hors service. En outre, la logique d’application doit être conçue pour tolérer les éventuelles incohérences ou de légers retards dus au temps additionnel que le système prend pour se réparer ou en fonction des objectifs de points de récupération (RPO). L’article qui suit fournit d’autres recommandations sur ce sujet : [Prévention de défaillance : Guide des architectures cloud résilientes].




[img-tokenservice]: media/iot-hub-guidance/tokenservice.png

[IoT Hub Developer Guide - identity registry]: iot-hub-devguide.md#identityregistry
[Guide du développeur IoT Hub - Registre d’identité]: iot-hub-devguide.md#identityregistry
[Guide du développeur IoT Hub - Registre identité]: iot-hub-devguide.md#identityregistry
[Guide du développeur IoT Hub - Choisir votre protocole de communication]: iot-hub-devguide.md#amqpvshttp
[IoT Hub Developer Guide - Security]: iot-hub-devguide.md#security
[Guide du développeur IoT Hub - Sécurité]: iot-hub-devguide.md#security
[IoT Hub Developer Guide - Anti-spoofing]: iot-hub-devguide.md#antispoofing
[Passerelle de protocole]: iot-hub-protocol-gateway.md
[Quotas et limites IoT Hub]: iot-hub-devguide.md#throttling

[Guide de gestion d’un appareil IoT Hub]: iot-hub-device-management.md
[Event Hubs]: ../event-hubs/event-hubs-what-is-event-hubs/
[Azure Traffic Manager]: https://azure.microsoft.com/documentation/services/traffic-manager/
[Event Hub - security]: ../event-hubs/event-hubs-authentication-and-security-model-overview/
[Event Hub publisher policies]: ../event-hubs-overview/#common-publisher-tasks
[Quotas Service Bus]: ../service-bus/service-bus-quotas/
[Azure IoT Hub SDKs]: https://github.com/Azure/azure-iot-sdks/blob/master/readme.md

[TLS-PSK]: https://tools.ietf.org/html/rfc4279

[Guide technique pour la continuité des activités Azure]: https://msdn.microsoft.com/library/azure/hh873027.aspx
[Récupération d’urgence et haute disponibilité des applications Azure]: https://msdn.microsoft.com/library/azure/dn251004.aspx
[Prévention de défaillance : Guide des architectures cloud résilientes]: https://msdn.microsoft.com/library/azure/jj853352.aspx

[lnk-pricing]: https://azure.microsoft.com/pricing/details/iot-hub

<!---HONumber=Oct15_HO1-->