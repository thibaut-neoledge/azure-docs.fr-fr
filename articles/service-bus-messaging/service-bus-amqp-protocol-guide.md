---
title: Guide du protocole AMQP 1.0 dans Azure Service Bus et Event Hubs | Microsoft Docs
description: "Guide du protocole pour les expressions et description d’AMQP 1.0 dans Azure Service Bus et Event Hubs"
services: service-bus-messaging,event-hubs
documentationcenter: .net
author: clemensv
manager: timlt
editor: 
ms.assetid: d2d3d540-8760-426a-ad10-d5128ce0ae24
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/12/2017
ms.author: clemensv;jotaub;hillaryc;sethm
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: 9197e429f25f5431d7ea36170c3339cd4d4bc32d
ms.lasthandoff: 04/12/2017


---
# <a name="amqp-10-in-azure-service-bus-and-event-hubs-protocol-guide"></a>Guide du protocole AMQP 1.0 dans Azure Service Bus et Event Hubs

Advanced Message Queueing Protocol 1.0 est un protocole de transfert et de tramage normalisé permettant de transférer de manière asynchrone, sécurisée et fiable des messages entre deux parties. Il est le principal protocole de messagerie Azure Service Bus et Azure Event Hubs. Les deux services prennent également en charge le protocole HTTPS. Le protocole SBMP propriétaire qui est également pris en charge est progressivement supprimé en faveur du protocole AMQP.

AMQP 1.0 est le résultat d’une vaste collaboration au sein du secteur entre des fournisseurs de middleware, tels que Microsoft et Red Hat et de nombreux utilisateurs de middleware de messagerie, tels que JP Morgan Chase représentant le secteur des services financiers. OASIS est le forum de normalisation technique destiné au protocole AMQP et aux spécifications d’extension ayant été approuvé officiellement comme norme internationale nommée ISO/IEC 19494.

## <a name="goals"></a>Objectifs

Cet article résume brièvement les concepts fondamentaux de la spécification relative à la messagerie AMQP 1.0 et un petit ensemble de spécifications d’extension à l’état de projet, en cours de finalisation par le comité technique AMQP d’OASIS, et explique comment Azure Service Bus implémente et s’appuie sur ces spécifications.

L’objectif est de permettre à tout développeur utilisant une pile de client AMQP 1.0 existante sur n’importe quelle plateforme d’interagir avec Azure Service Bus via AMQP 1.0.

Les piles AMQP 1.0 courantes à usage général, telles que Apache Proton ou AMQP.NET Lite, implémentent déjà tous les mouvements AMQP 1.0 de base. Ces mouvements fondamentaux sont parfois encapsulés dans une API de niveau plus élevé ; Apache Proton offre même les deux, l’API Messenger impérative et l’API Reactor réactive.

Dans la discussion suivante, nous supposerons que la gestion des connexions, des sessions et des liens AMQP, ainsi que le traitement des transferts de trame et de contrôle de flux est effectué par la pile respective (telle que Apache Proton-C) et qu’ils nécessitent peu d’attention, si ce n’est celle des développeurs d’applications. Nous allons supposer l’existence de quelques API primitives, telles que la possibilité de se connecter et de créer une certaine forme d’objets d’abstraction *expéditeur* et *destinataire*, qui ont respectivement une certaine forme d’opérations `send()` et `receive()`.

Lorsque nous aborderons les fonctionnalités avancées d’Azure Service Bus, telles que la recherche de messages ou la gestion des sessions, celles-ci seront expliquées en termes AMQP, mais également sous la forme d’une pseudo-implémentation en couche au-dessus de cette abstraction d’API supposée.

## <a name="what-is-amqp"></a>Qu’est-ce que le protocole AMQP ?

AMQP est un protocole de tramage et de transfert. Le tramage signifie qu’il fournit la structure des flux de données binaires qui circulent dans les deux directions d’une connexion réseau. La structure délimite les blocs de données distincts (*trames*) à échanger entre les parties connectées. Les fonctionnalités de transfert s’assurent que les deux parties qui communiquent peuvent établir une compréhension partagée pour savoir quand les trames doivent être transférées et à quel moment les transferts doivent être considérés comme terminés.

Contrairement aux précédents projets de version expirés élaborés par le groupe de travail AMQP, qui sont encore en cours d’utilisation par quelques courtiers de messages, le protocole AMQP 1.0 normalisé et finalisé du groupe de travail ne recommande pas la présence d’un courtier de messages ou d’une topologie spécifique pour les entités situées dans un courtier de messages.

Le protocole peut être utilisé pour la communication pair à pair symétrique, pour l’interaction avec les courtiers de messages qui prennent en charge les files d’attente et les entités de publication/d’abonnement, comme le fait Azure Service Bus. Il peut également être utilisé pour l’interaction avec l’infrastructure de messagerie où les modèles d’interaction diffèrent des files d’attente standards, comme c’est le cas avec Azure Event Hubs. Un Event Hub agit comme une file d’attente lorsque des événements lui sont envoyés, mais se comporte comme un service de stockage en série lorsque les événements sont lus à partir de celui-ci ; cela ressemble un peu à un lecteur de bande. Le client choisit un décalage dans le flux de données disponibles, et tous les événements sont ensuite traités à partir de ce décalage vers le dernier disponible.

Le protocole AMQP 1.0 est conçu pour être extensible et autoriser d’autres spécifications pour améliorer ses fonctionnalités. Les trois spécifications d’extension que nous abordons dans ce document illustrent ce principe. Pour les communications via l’infrastructure HTTPS/WebSockets où la configuration des ports AMQP TCP natifs peut être difficile, une spécification de liaison définit comment disposer AMQP sur WebSocket. Pour l’interaction avec l’infrastructure de messagerie sous forme de demande/réponse à des fins de gestion ou pour fournir des fonctionnalités avancées, la spécification de gestion AMQP définit les primitives d’interaction de base requises. Pour l’intégration du modèle d’autorisation fédérée, la spécification de la sécurité basée sur des revendications AMQP définit comment associer et renouveler des jetons d’autorisation associés aux liens.

## <a name="basic-amqp-scenarios"></a>Scénarios AMQP de base

Cette section explique l’utilisation de base d’AMQP 1.0 avec Azure Service Bus, qui inclut la création de connexions, de sessions et de liens et le transfert de messages vers et depuis des entités Service Bus, telles que des files d’attente, des rubriques et des abonnements.

La source faisant le plus autorité pour découvrir comment fonctionne AMQP est la spécification AMQP 1.0. La spécification a été élaborée pour guider de manière précise l’implémentation et non pour être formé au protocole. Cette section se concentre sur la présentation de toute la terminologie nécessaire à la description de l’utilisation d’AMQP 1.0 par Service Bus. Pour une présentation plus complète d’AMQP, et pour aborder plus largement AMQP 1.0, vous pouvez consulter [ce cours vidéo][this video course].

### <a name="connections-and-sessions"></a>Connexions et sessions

AMQP appelle les *conteneurs* de programmes de communication ; ceux-ci contiennent les *nœuds*, qui sont les entités communiquant à l’intérieur de ces conteneurs. Une file d’attente peut être un nœud. AMQP permet le multiplexage, de sorte qu’une seule connexion peut être utilisée pour nombreux chemins de communication entre les nœuds ; par exemple, un client d’application peut recevoir simultanément à partir d’une file d’attente et envoyer vers une autre file d’attente via la même connexion réseau.

![][1]

La connexion réseau est par conséquent ancrée sur le conteneur. Elle est lancée par le conteneur dans le rôle client établissant une connexion de socket TCP sortante vers un conteneur dans le rôle de récepteur qui écoute et accepte les connexions TCP entrantes. L’établissement d’une liaison de connexion inclut la négociation de la version du protocole, la déclaration ou la négociation de l’utilisation du protocole Transport Level Security (TLS/SSL) et l’établissement d’une liaison d’authentification/autorisation au niveau de la portée de la connexion basée sur SASL.

Azure Service Bus requiert l’utilisation de TLS à tout moment. Il prend en charge les connexions sur le port TCP 5671, dans le cadre duquel, la connexion TCP est tout d’abord superposée à TLS avant la saisie de la négociation du protocole AMQP et prend également en charge les connexions sur le port TCP 5672, dans le cadre duquel le serveur propose immédiatement une mise à niveau de connexion obligatoire vers TLS à l’aide du modèle prescrit par AMQP. La liaison AMQP WebSockets crée un tunnel via le port TCP 443 qui équivaut alors aux connexions AMQP 5671.

Après avoir configuré la connexion et le protocole TLS, Service Bus propose deux options de mécanisme SASL :

* SASL PLAIN est couramment utilisé pour transmettre des informations d’identification de nom d’utilisateur et de mot de passe à un serveur. Service Bus n’a pas de compte, mais des [règles de sécurité d’accès partagé](service-bus-sas.md) nommées, qui confèrent des droits et sont associées à une clé. Le nom d’une règle est utilisé comme nom d’utilisateur et la clé (comme texte codé en base64) est utilisé comme mot de passe. Les droits associés à la règle choisie régissent les opérations autorisées sur la connexion.
* SASL ANONYMOUS est utilisé pour ignorer l’autorisation SASL lorsque le client souhaite utiliser le modèle (CBS) de sécurité basé sur des revendications, qui fera l’objet d’une description ultérieurement. Avec cette option, une connexion cliente peut être établie de manière anonyme pour une courte période pendant laquelle le client peut uniquement interagir avec le point de terminaison CBS, et la négociation CBS doit s’effectuer.

Une fois la connexion de transport établie, chaque conteneur déclare la taille de trame maximale qu’il souhaite traiter, et le délai d’inactivité au terme duquel ils se déconnectent de manière unilatérale en l’absence d’activité sur la connexion.

Ils déclarent également le nombre de canaux simultanés pris en charge. Un canal est un chemin d’accès de transfert virtuel, unidirectionnel et sortant sur la connexion. Une session emprunte un canal à partir de chacun des conteneurs interconnectés pour former un chemin de communication bidirectionnelle.

Les sessions disposent d’un modèle de contrôle de flux utilisant une fenêtre ; lorsqu’une session est créée, chaque partie déclare le nombre de trames qu’elle est prête à accepter dans sa fenêtre de réception. Alors que les parties échangent des trames, les trames transférées remplissent cette fenêtre et les transferts s’arrêtent une fois la fenêtre pleine et jusqu’à sa réinitialisation ou expansion à l’aide du *performatif de flux* (*performatif* est le terme AMQP pour les mouvements au niveau du protocole échangés entre les deux parties).

Ce modèle utilisant une fenêtre est à peu près semblable au concept de contrôle de flux utilisant une fenêtre, mais au niveau de la session à l’intérieur du socket. Le concept du protocole visant à autoriser plusieurs sessions simultanées consiste à ce que le trafic à priorité élevée soit prioritaire sur un trafic normal ralenti, comme sur une voie expresse.

Azure Service Bus utilise actuellement une session pour chaque connexion. La taille maximale de trame de Service Bus est de 262 144 octets (256 Ko) pour La version Standard de Service Bus et Event Hubs. Elle est de 1 048 576 (1 Mo) pour la version Premium de Service Bus. Service Bus n’impose pas de fenêtre de limitation spécifique au niveau de la session, mais réinitialise la fenêtre régulièrement dans le cadre du contrôle de flux au niveau du lien (voir [la section suivante](#links)).

Les connexions, les canaux et les sessions sont éphémères. En cas de coupure de la connexion sous-jacente, les connexions, le tunnel TLS, le contexte d’autorisation SASL et les sessions doivent être rétablis.

### <a name="links"></a>Liens

AMQP transfère les messages via des liens. Un lien est un chemin de communication créé sur une session qui permet le transfert de messages dans une seule direction ; la négociation du statut de transfert s’effectue sur le lien et est bidirectionnelle entre les parties connectées.

![][2]

Des liens peuvent être créés par un conteneur à tout moment et sur une session existante, ce qui rend AMQP différent de nombreux protocoles, y compris HTTP et MQTT, où le lancement de transferts et le chemin d’accès de transfert sont un privilège exclusif de la partie créant la connexion de socket.

Le conteneur à l’origine du lien demande au conteneur opposé d’accepter un lien et il choisit un rôle d’expéditeur ou de destinataire. Par conséquent, chaque conteneur peut être à l’origine de la création de chemins d’accès unidirectionnels ou bidirectionnels, le dernier étant modélisé sous forme de paires de liens.

Des liens sont nommés et associés à des nœuds. Comme indiqué au début, les nœuds sont les entités qui communiquent à l’intérieur d’un conteneur.

Dans Service Bus, un nœud est l’équivalent direct d’une file d’attente, d’une rubrique, d’un abonnement ou d’une sous-file d’attente de rebut d’une file d’attente ou d’un abonnement. Le nom de nœud utilisé dans AMQP est donc le nom relatif de l’entité au sein de l’espace de noms Service Bus. Si une file d’attente est nommée **myqueue**, ce dernier est également son nom de nœud AMQP. Un abonnement de rubrique suit la convention de l’API HTTP dans la mesure où il est trié dans la collection de ressources « subscriptions ». Par conséquent, un abonnement **sub** ou une rubrique **mytopic** porte le nom de nœud AMQP **mytopic/subscriptions/sub**.

Le client qui se connecte doit également utiliser un nom de nœud local pour la création de liens ; Service Bus n’est pas normatif concernant ces noms de nœud et ne les interprète pas. Les piles de client AMQP 1.0 utilisent en général un schéma pour s’assurer que ces noms de nœud éphémères sont uniques dans le cadre du client.

### <a name="transfers"></a>Transferts

Une fois qu’un lien a été établi, les messages peuvent être transférés via celui-ci. Dans AMQP, un transfert est exécuté avec un mouvement de protocole explicite (le performatif de *transfert*) qui déplace un message de l’expéditeur vers le destinataire via un lien. Un transfert est terminé lorsqu’il « réglé », ce qui signifie que les deux parties ont établi une compréhension commune du résultat de ce transfert.

![][3]

Dans le cas le plus simple, l’expéditeur peut choisir d’envoyer des messages « préalablement réglés », ce qui signifie que le client n’est pas intéressé par le résultat et que le destinataire ne fournira pas de commentaires sur le résultat de l’opération. Ce mode est pris en charge par Service Bus au niveau protocole AMQP, mais n’est pas exposé dans les API du client.

En temps normal les messages sont envoyés non réglés et le destinataire indique l’acceptation ou le rejet à l’aide du performatif de *traitement*. Le rejet se produit lorsque le destinataire ne peut pas accepter le message pour une raison quelconque. Le message de refus contient des informations sur la raison, qui est une structure d’erreur définie par AMQP. Si les messages sont rejetés en raison d’erreurs internes dans Service Bus, le service renvoie des informations supplémentaires à l’intérieur de cette structure utilisables pour fournir des indications de diagnostic au personnel chargé du support technique si vous déposez des demandes de support. Nous aborderons les erreurs plus en détail ultérieurement.

Une forme spéciale de rejet est l’état *released*, qui indique que le destinataire n’oppose pas d’objection technique au transfert, et qu’il n’est pas intéressé par le fait de régler ce dernier. Ce cas survient, par exemple, lorsqu’un message est envoyé à un client de Service Bus et que le client choisit d’« abandonner » le message, car il ne peut pas effectuer le travail issu du traitement du message, alors que la remise du message proprement dite n’est pas en cause. L’état *modified* est une variante de cet état. Il permet de modifier le message au moment de sa publication. Cet état n’est pas utilisé par Service Bus à l’heure actuelle.

La spécification AMQP 1.0 définit un autre état de traitement, *received*, qui permet de traiter spécifiquement la récupération de liens. La récupération de liens permet de reconstituer l’état d’un lien et toutes les remises en attente sur une nouvelle connexion et session, lorsque la session et la connexion précédentes ont été perdues.

Service Bus ne prend pas en charge la récupération de liens ; si le client perd la connexion à Service Bus avec un transfert de message non réglé en attente, ce transfert de message est perdu et le client doit se reconnecter, rétablir le lien et relancer le transfert.

Ainsi, Service Bus et Event Hubs prennent en charge les transferts « au moins une fois » où l’expéditeur peut être assuré du stockage et de l’acceptation de son message. Toutefois, les transferts « une fois au maximum » au niveau AMQP, au cours desquels le système tenterait de récupérer le lien et continuerait de négocier l’état de remise pour éviter la duplication du transfert du message, ne sont pas pris en charge.

Pour compenser les potentiels envois en double, Service Bus prend en charge la détection des doublons comme fonctionnalité facultative sur les files d’attente et les rubriques. La détection des doublons enregistre les ID de message de tous les messages entrants pendant un laps de temps défini par l’utilisateur et annule de manière silencieuse tous les messages envoyés avec le même ID de message au cours de cette même période.

### <a name="flow-control"></a>Contrôle de flux

Outre le modèle de contrôle de flux au niveau de la session indiqué précédemment, chaque lien a son propre modèle de contrôle de flux. Le contrôle de flux au niveau de la session évite au conteneur de devoir gérer un trop grand nombre de trames en une seule fois. Le contrôle de flux au niveau du lien charge l’application de décider du nombre de messages qu’elle souhaite gérer à partir d’un lien et à quel moment.

![][4]

Les transferts ne peuvent se produire sur un lien que lorsque l’expéditeur dispose d’un « *crédit de lien* » suffisant. Le crédit de lien est un compteur défini par le récepteur à l’aide du performatif de *flux*, qui est limité à un lien. Dès que l’expéditeur se voit attribuer un crédit de lien, il tente de l’utiliser en envoyant des messages. Chaque remise de message décrémente d’autant (1) le crédit de lien restant. Lorsque le crédit de lien est épuisé, les remises s’interrompent.

Lorsque Service Bus se trouve dans le rôle du destinataire, il offre instantanément à l’expéditeur un crédit de lien important permettant d’envoyer immédiatement des messages. À mesure que le crédit de lien est utilisé, Service Bus envoie parfois un performatif de *flux* à l’expéditeur pour mettre à jour le solde du crédit de lien.

Dans le rôle de l’expéditeur, Service Bus st prompt à envoyer des messages afin d’épuiser tout crédit de lien restant.

Un appel de « réception » au niveau de l’API se traduit par un performatif de *flux* envoyé à Service Bus par le client. Service Bus utilise ce crédit en prenant le premier message non verrouillé disponible de la file d’attente, en le verrouillant et en le transférant. Si aucun message n’est immédiatement disponible pour la remise, tout crédit restant par tout lien établi avec cette entité spécifique reste enregistré dans l’ordre d’arrivée et les messages sont verrouillés et transférés dès qu’ils sont disponibles pour utiliser tout crédit en attente.

Le verrouillage d’un message est levé dès que l’état terminal d’un transfert est *accepted*, *rejected*, ou *released*. Le message est supprimé de Service Bus lorsque l’état terminal est *accepted*. Il reste dans Service Bus et est remis au destinataire suivant dès que le transfert se voit attribuer un des autres états. Service Bus déplace automatiquement le message vers la file d’attente de rebut de l’entité lorsque le nombre maximal de remises autorisé pour l’entité est atteint en raison des rejets répétés ou des versions.

Bien qu’aujourd’hui les API Service Bus n’exposent pas directement une telle option, un client de protocole AMQP de niveau inférieur peut utiliser le modèle de crédit de lien pour transformer l’interaction de style « pull», qui consiste à émettre une unité de crédit pour chaque demande de réception en un modèle de style « push » qui consiste à émettre un très grand nombre de crédits de lien et à recevoir les messages dès qu’ils sont disponibles sans intervention supplémentaire. Le modèle Push est pris en charge par le biais des paramètres de propriétés [MessagingFactory.PrefetchCount](/dotnet/api/microsoft.servicebus.messaging.messagingfactory#Microsoft_ServiceBus_Messaging_MessagingFactory_PrefetchCount) ou [MessageReceiver.PrefetchCount](/dotnet/api/microsoft.servicebus.messaging.messagereceiver#Microsoft_ServiceBus_Messaging_MessageReceiver_PrefetchCount). Lorsqu’ils sont différents de zéro, le client AMQP l’utilise comme crédit de lien.

Dans ce contexte, il est important de comprendre que l’horloge d’expiration du verrou sur le message situé à l’intérieur de l’entité démarre lorsque le message est extrait de l’entité, et non lorsqu’il est envoyé. Chaque fois que le client indique qu’il est prêt à recevoir des messages en émettant un crédit de lien, on s’attend à ce qu’il extrait de manière active des messages sur le réseau et qu’il soit prêt à les gérer. Sinon, le verrouillage du message peut expirer avant même la remise du message. L’utilisation du contrôle de flux de crédit de lien doit refléter directement la disponibilité de traitement immédiate des messages disponibles distribués auprès du destinataire.

En résumé, les sections suivantes fournissent une vue d’ensemble schématique du flux performatif lors des différentes interactions de l’API. Chaque section décrit une opération logique différente. Certaines de ces interactions peuvent être « différées », ce qui signifie qu’elles peuvent être effectuées uniquement en cas de besoin. La création d’un expéditeur de message peut ne pas provoquer d’interaction réseau tant que le premier message n’est pas envoyé ou demandé.

Les flèches dans le tableau suivant indiquent le sens du flux performatif.

#### <a name="create-message-receiver"></a>Création du destinataire du message

| Client | SERVICE BUS |
| --- | --- |
| --> attach(<br/>name={nom du lien},<br/>handle={gestion numérique},<br/>role=**receiver**,<br/>source={nom de l’entité},<br/>target={id du lien client}<br/>) |Le client se joint à l’entité en tant que destinataire |
| Service Bus répond en attachant son extrémité du lien |<-- attach(<br/>name={nom du lien},<br/>handle={gestion numérique},<br/>role=**sender**,<br/>source={nom de l’entité},<br/>target={id du lien client}<br/>) |

#### <a name="create-message-sender"></a>Création de l’expéditeur du message

| Client | SERVICE BUS |
| --- | --- |
| --> attach(<br/>name={nom du lien},<br/>handle={gestion numérique},<br/>role=**sender**,<br/>source={id du lien client},<br/>target={nom de l’entité}<br/>) |Aucune action |
| Aucune action |<-- attach(<br/>name={nom du lien},<br/>handle={gestion numérique},<br/>role=**receiver**,<br/>source={id du lien client},<br/>target={nom de l’entité}<br/>) |

#### <a name="create-message-sender-error"></a>Création de l’expéditeur du message (erreur)

| Client | SERVICE BUS |
| --- | --- |
| --> attach(<br/>name={nom du lien},<br/>handle={gestion numérique},<br/>role=**sender**,<br/>source={id du lien client},<br/>target={nom de l’entité}<br/>) |Aucune action |
| Aucune action |<-- attach(<br/>name={nom du lien},<br/>handle={gestion numérique},<br/>role=**receiver**,<br/>source=null,<br/>target=null<br/>)<br/><br/><-- detach(<br/>handle={gestion numérique},<br/>closed=**true**,<br/>error={infos sur l’erreur}<br/>) |

#### <a name="close-message-receiversender"></a>Fermeture de l’expéditeur/du destinataire du message

| Client | SERVICE BUS |
| --- | --- |
| --> detach(<br/>handle={gestion numérique},<br/>closed=**true**<br/>) |Aucune action |
| Aucune action |<-- detach(<br/>handle={gestion numérique},<br/>closed=**true**<br/>) |

#### <a name="send-success"></a>Envoi (réussite)

| Client | SERVICE BUS |
| --- | --- |
| --> transfer(<br/>delivery-id={gestion numérique},<br/>delivery-tag={gestion binaire},<br/>settled=**false**,,more=**false**,<br/>state=**null**,<br/>resume=**false**<br/>) |Aucune action |
| Aucune action |<-- disposition(<br/>role=receiver,<br/>first={id d’envoi},<br/>last={id d’envoi},<br/>settled=**true**,<br/>state=**accepted**<br/>) |

#### <a name="send-error"></a>Envoi (erreur)

| Client | SERVICE BUS |
| --- | --- |
| --> transfer(<br/>delivery-id={gestion numérique},<br/>delivery-tag={gestion binaire},<br/>settled=**false**,,more=**false**,<br/>state=**null**,<br/>resume=**false**<br/>) |Aucune action |
| Aucune action |<-- disposition(<br/>role=receiver,<br/>first={id d’envoi},<br/>last={id d’envoi},<br/>settled=**true**,<br/>state=**rejected**(<br/>error={infos sur l’erreur}<br/>)<br/>) |

#### <a name="receive"></a>Réception

| Client | SERVICE BUS |
| --- | --- |
| --> flow(<br/>link-credit=1<br/>) |Aucune action |
| Aucune action |< transfer(<br/>delivery-id={gestion numérique},<br/>delivery-tag={gestion binaire},<br/>settled=**false**,<br/>more=**false**,<br/>state=**null**,<br/>resume=**false**<br/>) |
| --> disposition(<br/>role=**receiver**,<br/>first={id d’envoi},<br/>last={id d’envoi},<br/>settled=**true**,<br/>state=**accepted**<br/>) |Aucune action |

#### <a name="multi-message-receive"></a>Réception de plusieurs messages

| Client | SERVICE BUS |
| --- | --- |
| --> flow(<br/>link-credit=3<br/>) |Aucune action |
| Aucune action |< transfer(<br/>delivery-id={gestion numérique},<br/>delivery-tag={gestion binaire},<br/>settled=**false**,<br/>more=**false**,<br/>state=**null**,<br/>resume=**false**<br/>) |
| Aucune action |< transfer(<br/>delivery-id={gestion numérique+1},<br/>delivery-tag={gestion binaire},<br/>settled=**false**,<br/>more=**false**,<br/>state=**null**,<br/>resume=**false**<br/>) |
| Aucune action |< transfer(<br/>delivery-id={gestion numérique+2},<br/>delivery-tag={gestion binaire},<br/>settled=**false**,<br/>more=**false**,<br/>state=**null**,<br/>resume=**false**<br/>) |
| --> disposition(<br/>role=receiver,<br/>first={id d’envoi},<br/>last={id d’envoi+2},<br/>settled=**true**,<br/>state=**accepted**<br/>) |Aucune action |

### <a name="messages"></a>Messages

Les sections suivantes expliquent quelles propriétés des sections de message AMQP standard sont utilisées par Service Bus et comment elles correspondent aux API Service Bus.

#### <a name="header"></a>en-tête

| Nom du champ | Usage | Nom de l’API |
| --- | --- | --- |
| durable |- |- |
| priority |- |- |
| ttl |Durée de vie de ce message |[TimeToLive](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_TimeToLive) |
| first-acquirer |- |- |
| delivery-count |- |[DeliveryCount](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_DeliveryCount) |

#### <a name="properties"></a>properties

| Nom du champ | Usage | Nom de l’API |
| --- | --- | --- |
| message-id |Identifiant défini par l’application, au format libre pour ce message. Utilisation pour la détection des doublons. |[MessageId](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_MessageId) |
| user-id |Identifiant d’utilisateur défini par l’application, non interprété par Service Bus. |Pas accessible via l’API Service Bus. |
| to |Identifiant de destination défini par l’application, non interprété par Service Bus. |[To](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_To) |
| subject |Identifiant d’objet de message défini par l’application, non interprété par Service Bus. |[Label](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_Label) |
| reply-to |Indicateur reply-path défini par l’application, non interprété par Service Bus. |[ReplyTo](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_ReplyTo) |
| correlation-id |Identifiant de corrélation défini par l’application, non interprété par Service Bus. |[CorrelationId](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_CorrelationId) |
| content-type |Indicateur de type de contenu défini par l’application pour le corps, non interprété par Service Bus. |[ContentType](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_ContentType) |
| content-encoding |Indicateur d’encodage de contenu défini par l’application pour le corps, non interprété par Service Bus. |Pas accessible via l’API Service Bus. |
| absolute-expiry-time |Déclare à quel instant absolu le message expire. Ignoré en entrée (la durée de vie de l’en-tête est observée), fait autorité en sortie. |[ExpiresAtUtc](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_ExpiresAtUtc) |
| creation-time |Déclare à quelle heure le message a été créé. Pas utilisé par Service Bus |Pas accessible via l’API Service Bus. |
| group-id |Identifiant défini par l’application pour un ensemble de messages connexes. Utilisé pour les sessions Service Bus. |[SessionId](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_SessionId) |
| group-sequence |Compteur identifiant le nombre de séquences relatives du message se trouvant dans la session. Ignoré par Service Bus. |Pas accessible via l’API Service Bus. |
| reply-to-group-id |- |[ReplyToSessionId](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_ReplyToSessionId) |

## <a name="advanced-service-bus-capabilities"></a>Fonctionnalités avancées de Service Bus

Cette section traite des fonctionnalités avancées d’Azure Service Bus qui reposent sur des projets d’extension d’AMQP en cours de développement au sein du comité technique OASIS pour AMQP. Service Bus implémente la dernière version de ces projets et adoptera les modifications introduites dès que ces projets auront été normalisés.

> [!NOTE]
> Les opérations avancées de messagerie Service Bus sont prises en charge via un modèle de demande/réponse. Les détails de ces opérations sont décrits dans le document [AMQP 1.0 in Service Bus: request/response-based operations](service-bus-amqp-request-response.md) (AMQP 1.0 dans Service Bus : opérations basées sur le modèle de demande-réponse).
> 
> 

### <a name="amqp-management"></a>Gestion du protocole AMQP

La spécification de gestion du protocole AMQP est le premier projet d’extension dont nous parlerons ici. Cette spécification définit un ensemble de mouvements de protocole disposés en couche au-dessus du protocole AMQP, qui permettent des interactions de gestion avec l’infrastructure de messagerie via AMQP. La spécification définit des opérations génériques, telles que la *création*, la *lecture*, la *mise à jour* et la *suppression*, pour la gestion des entités au sein d’une infrastructure de messagerie et un ensemble d’opérations de requête.

Tous ces mouvements nécessitent une interaction demande/réponse entre le client et l’infrastructure de messagerie. Par conséquent, la spécification définit comment modéliser ce modèle d’interaction au-dessus d’AMQP : le client se connecte à l’infrastructure de messagerie, lance une session, puis crée une paire de liens. Sur un lien, le client joue le rôle d’expéditeur et sur l’autre, il agit en tant que destinataire, créant ainsi une paire de liens pouvant agir sous forme de canal bidirectionnel.

| Opération logique | Client | Service Bus |
| --- | --- | --- |
| Création d’un chemin d’accès de réponse à une demande |--> attach(<br/>name={*nom du lien*},<br/>handle={*gestion numérique*},<br/>role=**sender**,<br/>source=**null**,<br/>target=”myentity/$management”<br/>) |Aucune action |
| Création d’un chemin d’accès de réponse à une demande |Aucune action |\<-- attach(<br/>name={*nom du lien*},<br/>handle={*gestion numérique*},<br/>role=**receiver**,<br/>source=null,<br/>target=”myentity”<br/>) |
| Création d’un chemin d’accès de réponse à une demande |--> attach(<br/>name={*nom du lien*},<br/>handle={*gestion numérique*},<br/>role=**receiver**,<br/>source=”myentity/$management”,<br/>target=”myclient$id”<br/>) | |
| Création d’un chemin d’accès de réponse à une demande |Aucune action |\<-- attach(<br/>name={*nom du lien*},<br/>handle={*gestion numérique*},<br/>role=**sender**,<br/>source=”myentity”,<br/>target=”myclient$id”<br/>) |

Une fois cette paire de liens en place, l’implémentation de la demande/réponse est simple : une demande est un message envoyé à une entité se trouvant au sein de l’infrastructure de messagerie qui comprend ce modèle. Dans ce message de demande, le champ *reply-to* de la section des *propriétés* est défini sur l’identifiant *cible* du lien sur lequel remettre la réponse. L’entité de gestion traite la demande, puis envoie la réponse sur le lien dont l’identifiant *cible* correspond à l’identifiant *reply-to* indiqué.

Ce modèle nécessite évidemment que le conteneur du client et l’identifiant généré par le client pour la destination de la réponse soient uniques sur tous les clients et, pour des raisons de sécurité, qu’ils soient également difficiles à deviner.

Les échanges de messages utilisés pour le protocole de gestion et pour tous les autres protocoles utilisant le même modèle se produisent au niveau de l’application ; ils ne définissent pas de nouveaux mouvements au niveau du protocole AMQP. Cela est intentionnel afin que les applications puissent tirer immédiatement parti de ces extensions avec les piles compatibles AMQP 1.0.

Service Bus n’implémente actuellement aucune des principales fonctionnalités de la spécification de gestion. Toutefois, le modèle demande/réponse défini par cette dernière est indispensable pour la fonctionnalité de sécurité basée sur des revendications et pour presque toutes les fonctionnalités avancées que nous aborderons dans les sections suivantes.

### <a name="claims-based-authorization"></a>Autorisation basée sur des revendications

Le projet de spécification d’autorisation basée sur des revendications AMQP (CBS) s’appuie sur le modèle demande/réponse de la spécification de gestion et décrit un modèle généralisé pour l’utilisation des jetons de sécurité fédérés avec AMQP.

Le modèle de sécurité par défaut du protocole AMQP abordé dans l’introduction repose sur SASL et s’intègre à la négociation de connexion AMQP. L’utilisation de SASL a l’avantage de fournir un modèle extensible pour lequel un ensemble de mécanismes a été défini desquels tout protocole reposant officiellement sur SASL peut bénéficier. Parmi ces mécanismes figurent « PLAIN » pour le transfert des noms d’utilisateurs et des mots de passe, « EXTERNAL » pour la liaison avec la sécurité au niveau TLS, « ANONYMOUS » pour exprimer l’absence d’authentification/autorisation explicite et une grande variété de mécanismes supplémentaires qui permettent de transmettre des jetons ou des informations d’identification d’authentification et/ou d’autorisation.

L’intégration de SASL au protocole AMQP présente deux inconvénients :

* L’ensemble des informations d’identification et des jetons se limite à la connexion. Une infrastructure de messagerie peut vouloir fournir un contrôle d’accès différencié par entité. Par exemple, en autorisant le porteur d’un jeton à effectuer un envoi vers la file d’attente A, mais pas la file d’attente B. Dans la mesure où le contexte d’autorisation est ancré à la connexion, il est impossible d’utiliser une connexion unique et des jetons d’accès différents pour les files d’attente A et B.
* Les jetons d’accès sont généralement valides uniquement pour une durée limitée. Cela oblige l’utilisateur à réacquérir régulièrement des jetons et offre la possibilité à l’émetteur du jeton de refuser d’en émettre un nouveau si les autorisations d’accès de l’utilisateur ont été modifiées. Les connexions AMQP peuvent durer très longtemps. Le modèle SASL permet uniquement de définir un jeton au moment de la connexion, ce qui signifie que l’infrastructure de messagerie doit déconnecter le client lorsque le jeton a expiré ou accepter le risque lié au fait d’autoriser des communications continues avec un client dont les droits d’accès peuvent avoir été révoqués entre-temps.

La spécification AMQP CBS, implémentée par Service Bus, offre une solution de contournement élégante pour ces deux problèmes : elle permet à un client d’associer des jetons d’accès à chaque nœud et de mettre à jour ces jetons avant leur expiration, sans interrompre le flux de messages.

CBS définit un nœud de gestion virtuel nommé *$cbs*, qui doit être fourni par l’infrastructure de messagerie. Le nœud de gestion accepte les jetons pour le compte de tous les autres nœuds dans l’infrastructure de messagerie.

Le mouvement de protocole est un échange de demande/réponse, tel que défini par la spécification de gestion. Cela signifie que le client établit une paire de liens avec le nœud *$cbs*, transmet une demande sur le lien sortant, puis attend la réponse sur le lien entrant.

Le message de demande possède les propriétés d’application suivantes :

| Clé | Facultatif | Type de valeur | Contenu de la valeur |
| --- | --- | --- | --- |
| operation |Non |string |**put-token** |
| type |Non |string |Type du jeton placé. |
| name |Non |string |« Audience » à laquelle le jeton s’applique. |
| expiration |Oui |timestamp |Délai d’expiration du jeton. |

La propriété *name* identifie l’entité avec laquelle le jeton doit être associé. Dans Service Bus, il s’agit du chemin d’accès à la file d’attente ou à la rubrique/l’abonnement. La propriété *type* identifie le type de jeton :

| Type de jeton | Description du jeton | Type de corps | Remarques |
| --- | --- | --- | --- |
| amqp:jwt |JSON Web Token (JWT) |Valeur AMQP (chaîne) |Pas encore disponible. |
| amqp:swt |Clé d’authentification Web simple (SWT) |Valeur AMQP (chaîne) |Pris en charge uniquement pour les clés d’authentification web simples SWT émises par AAD/ACS |
| servicebus.windows.net:sastoken |Jeton SAS Service Bus |Valeur AMQP (chaîne) |- |

Les jetons confèrent des droits. Service Bus connaît trois droits fondamentaux : « Envoyer » autorise l’envoi, « Écouter » autorise la réception, et « Gérer » autorise la manipulation d’entités. Les jetons SWT émis par AAD/ACS incluent explicitement ces droits en tant que revendications. Les jetons SAP Service Bus font référence aux règles configurées sur l’espace de noms ou l’entité. Ces dernières sont configurées avec des droits. Le fait de signer le jeton avec la clé associée à cette règle permet au jeton d’exprimer les droits respectifs. Le jeton associé à une entité à l’aide de *put-token* permet au client connecté d’interagir avec l’entité selon les droits du jeton. Un lien sur lequel le client joue le rôle *d’expéditeur* exige le droit « Envoyer ». Le rôle de *destinataire* exige le droit « Écouter ».

Le message de réponse a les valeurs *application-properties* suivantes :

| Clé | Facultatif | Type de valeur | Contenu de la valeur |
| --- | --- | --- | --- |
| status-code |Non |int |Code de réponse HTTP **[RFC2616]**. |
| status-description |Oui |string |Description de l’état. |

Le client peut appeler *put-token* à plusieurs reprises pour toutes les entités de l’infrastructure de messagerie. Les jetons portent sur le client actuel et sont ancrés sur la connexion actuelle, ce qui signifie que le serveur annule tous les jetons conservés en cas d’abandon de la connexion.

L’implémentation de Service Bus actuelle autorise uniquement CBS conjointement avec la méthode SASL « ANONYMOUS ». Une connexion SSL/TLS doit toujours exister avant la négociation SASL.

Le mécanisme ANONYMOUS doit donc être pris en charge par le client AMQP 1.0 choisi. L’accès anonyme signifie que la négociation de connexion initiale, y compris la création de la session initiale se produit sans que Service Bus sache qui crée la connexion.

Une fois la session et la connexion établies, l’association des liens avec le nœud *$cbs* et l’envoi de la demande *put-token* sont les seules opérations autorisées. Un jeton valide doit être défini avec succès à l’aide d’une demande *put-token* pour un nœud d’entité dans les 20 secondes suivant l’établissement de la connexion, sans quoi la connexion sera abandonnée unilatéralement par Service Bus.

Le client est ensuite chargé de vérifier l’expiration du jeton. Lorsqu’un jeton arrive à expiration, Service Bus annule rapidement tous les liens sur la connexion à l’entité respective. Pour éviter cela, le client peut remplacer le jeton du nœud par un nouveau jeton à tout moment via le nœud de gestion *$cbs* virtuel avec le même mouvement *put-token* et sans gêner le trafic de la charge utile qui transite sur différents liens.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur AMQP, consultez les liens suivants :

* [Vue d’ensemble d’AMQP de Service Bus]
* [Prise en charge d’AMQP 1.0 dans les rubriques et files d’attente partitionnées Service Bus]
* [AMQP dans Service Bus pour Windows Server]

[this video course]: https://www.youtube.com/playlist?list=PLmE4bZU0qx-wAP02i0I7PJWvDWoCytEjD
[1]: ./media/service-bus-amqp-protocol-guide/amqp1.png
[2]: ./media/service-bus-amqp-protocol-guide/amqp2.png
[3]: ./media/service-bus-amqp-protocol-guide/amqp3.png
[4]: ./media/service-bus-amqp-protocol-guide/amqp4.png

[Vue d’ensemble d’AMQP de Service Bus]: service-bus-amqp-overview.md
[Prise en charge d’AMQP 1.0 dans les rubriques et files d’attente partitionnées Service Bus]: service-bus-partitioned-queues-and-topics-amqp-overview.md
[AMQP dans Service Bus pour Windows Server]: https://msdn.microsoft.com/library/dn574799.aspx

