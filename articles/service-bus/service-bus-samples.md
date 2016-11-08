---
title: Vue d’ensemble des exemples Service Bus | Microsoft Docs
description: Classe et décrit les exemples Service Bus avec des liens vers chacun d’eux.
services: service-bus
documentationcenter: na
author: sethmanheim
manager: timlt
editor: ''

ms.service: service-bus
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/06/2016
ms.author: sethm

---
# Exemples Service Bus
Les exemples de Service Bus illustrent des fonctionnalités clés de [Service Bus](https://azure.microsoft.com/services/service-bus/) (service cloud) et [Service Bus pour Windows Server](https://msdn.microsoft.com/library/dn282144.aspx). Cette article attribue une catégorie et décrit les exemples disponibles, avec des liens vers chacun d’eux.

> [!NOTE]
> Les exemples Service Bus ne sont pas installés avec le Kit de développement logiciel. Pour obtenir des exemples, visitez la [page d’exemples de kit de développement logiciels Microsoft Azure](https://code.msdn.microsoft.com/site/search?query=service%20bus&f%5B0%5D.Value=service%20bus&f%5B0%5D.Type=SearchText&ac=5).
> 
> Un ensemble actualisé d’exemples de messagerie Service Bus est également disponible [ici](https://github.com/Azure-Samples/azure-servicebus-messaging-samples) (ils ne sont pas décrits dans cet article pour le moment). Des exemples de relais sont disponibles [ici](https://github.com/Azure-Samples/azure-servicebus-relay-samples).
> 
> 

## Messagerie répartie Service Bus
Les exemples suivants montrent comment écrire des applications qui utilisent Service Bus.

Notez que les exemples de messagerie répartie requièrent une chaîne de connexion pour accéder à votre espace de noms Service Bus.

### Pour obtenir une chaîne de connexion pour Azure Service Bus.
1. Connectez-vous au [portail Azure Classic](http://manage.windowsazure.com).
2. Dans la colonne de gauche, cliquez sur **Service Bus**.
3. Cliquez sur le nom de votre espace de noms de service dans la liste.
4. Cliquez sur **Connection Information**. Dans la boîte de dialogue **Accéder aux informations de connexion**, copiez la chaîne de connexion dans le presse-papiers.
5. Collez la chaîne de connexion dans le fichier App.config de l’exemple.

### Pour obtenir une chaîne de connexion de Service Bus pour Windows Server
1. Exécutez l’applet de commande PowerShell suivant :
   
    ```
    get-sbClientConfiguration
    ```
2. Collez la chaîne de connexion dans le fichier App.config de l’exemple.

### Exemples de mise en route
Ces exemples décrivent les fonctionnalités de messagerie et de relais de base.

| Nom d’exemple | Description | Kit de développement logiciel minimum | Availability |
| --- | --- | --- | --- |
| [Mise en route : messagerie avec files d’attente](http://code.msdn.microsoft.com/Getting-Started-Brokered-aa7a0ac3) |Montre comment utiliser Microsoft Azure Service Bus pour envoyer et recevoir des messages à partir d’une file d’attente. |1\.8 |Microsoft Azure Service Bus ; Service Bus pour Windows Server |
| [Mise en route : messagerie avec rubriques](http://code.msdn.microsoft.com/Getting-Started-Brokered-614d42e5) |Montre comment utiliser Microsoft Azure Service Bus pour envoyer et recevoir des messages à partir d’une rubrique avec plusieurs abonnements. |1\.8 |Microsoft Azure Service Bus ; Service Bus pour Windows Server |
| [Prise en main des concentrateurs d’événements](http://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097) |Illustre les fonctionnalités de base des concentrateurs d’événements telles que la création d’un concentrateur d’événements, l’envoi d’événements à un concentrateur d’événements, consommant des événements à l’aide du processeur d’événements. |2\.4 |Microsoft Azure Service Bus |

### Exploration des fonctions
Les exemples suivants illustrent différentes fonctionnalités de Service Bus.

| Nom d’exemple | Description | Kit de développement logiciel minimum | Availability |
| --- | --- | --- | --- |
| [Fournisseurs de jeton HTTP](http://code.msdn.microsoft.com/Service-Bus-HTTP-Token-38f2cfc5) |Illustre les différents modes d’authentification d’un client HTTP/REST avec Service Bus. |2\.1 |Microsoft Azure Service Bus ; Service Bus pour Windows Server |
| [Client HTTP Service Bus](http://code.msdn.microsoft.com/Service-Bus-HTTP-client-fe7da74a) |Montre comment envoyer et recevoir des messages de Service Bus via HTTP/REST. |2\.3 |Microsoft Azure Service Bus ; Service Bus pour Windows Server |
| [Transfert automatique Service Bus](http://code.msdn.microsoft.com/Service-Bus-Autoforwarding-b9df470b) |Montre comment transférer automatiquement les messages depuis une file d’attente, un abonnement ou une file d’attente de rebut dans une autre file d’attente ou rubrique. Il montre également comment envoyer un message dans une file d’attente ou une rubrique via une file d’attente de transfert. |2\.3 |Microsoft Azure Service Bus ; Service Bus pour Windows Server |
| [Messagerie répartie : exemple de session de canal WCF](http://code.msdn.microsoft.com/Brokered-Messaging-WCF-0a526451) |Montre comment utiliser Microsoft Azure Service Bus avec canaux Windows Communication Foundation (WCF). L’exemple montre l’utilisation de canaux WCF pour envoyer et recevoir des messages via une file d’attente Service Bus. L’exemple montre les communications de session et non-session via le Bus de Service. |1\.8 |Microsoft Azure Service Bus ; Service Bus pour Windows Server |
| [Messagerie répartie : transactions](http://code.msdn.microsoft.com/Brokered-Messaging-8cd41d1e) |Montre comment utiliser les fonctions de messagerie Microsoft Azure Service Bus au sein d’une étendue de transaction, afin de garantir des lots d’opérations de messagerie sont validés automatiquement. |1\.8 |Microsoft Azure Service Bus ; Service Bus pour Windows Server |
| [Messagerie répartie : opérations de gestion avec REST](http://code.msdn.microsoft.com/Brokered-Messaging-569cff88) |Montre comment effectuer des opérations de gestion sur le Bus de Service à l’aide de REST. |1\.8 |Microsoft Azure Service Bus ; Service Bus pour Windows Server |
| [Fournisseur de ressources API REST](http://code.msdn.microsoft.com/Service-Bus-Resource-5d887203) |Montre comment utiliser les nouvelles API de REST RDFE Service Bus pour gérer les espaces de noms et les entités de messagerie. |1\.8 |Microsoft Azure Service Bus ; Service Bus pour Windows Server |
| [Messagerie répartie : exemple de session de service WCF](http://code.msdn.microsoft.com/Brokered-Messaging-WCF-db4262c2) |Montre comment utiliser Microsoft Azure Service Bus en utilisant le modèle de service WCF. L’exemple montre l’utilisation du modèle de service WCF pour accomplir des sessions de communication via une file d’attente Service Bus. |1\.8 |Microsoft Azure Service Bus ; Service Bus pour Windows Server |
| [Messagerie répartie : réponse à une demande](http://code.msdn.microsoft.com/Brokered-Messaging-Request-2b4ff5d8) |Montre comment utiliser Microsoft Azure Service Bus et la fonctionnalité de demande/réponse. L’exemple montre des clients simples et des serveurs communiquant via une file d’attente Service Bus. |1\.8 |Microsoft Azure Service Bus ; Service Bus pour Windows Server |
| [Messagerie répartie : file d’attente de rebut](http://code.msdn.microsoft.com/Brokered-Messaging-Dead-22536dd8) |Montre comment utiliser Microsoft Azure Service Bus et la fonctionnalité de messagerie « file d’attente de rebut ». L’exemple montre un expéditeur et un destinataire simples communiquant via une file d’attente Service Bus. |1\.8 |Microsoft Azure Service Bus ; Service Bus pour Windows Server |
| [Messagerie répartie : Messages différés](http://code.msdn.microsoft.com/Brokered-Messaging-ccc4f879) |Montre comment utiliser la fonction de différé de message de Microsoft Azure Service Bus. L’exemple montre un expéditeur et un destinataire simples communiquant via une file d’attente Service Bus. |1\.8 |Microsoft Azure Service Bus ; Service Bus pour Windows Server |
| [Messagerie répartie : messages de session](http://code.msdn.microsoft.com/Brokered-Messaging-Session-41c43fb4) |Montre comment utiliser Microsoft Azure Service Bus et la fonctionnalité de messagerie de session. L’exemple montre des expéditeurs et des destinataires simples communiquant via une file d’attente Service Bus. |1\.8 |Microsoft Azure Service Bus ; Service Bus pour Windows Server |
| [Messagerie répartie : rubrique de réponse à une demande](http://code.msdn.microsoft.com/Brokered-Messaging-Request-6759a36e) |Montre comment mettre en œuvre le modèle demande/réponse à l’aide des rubriques et des abonnements Microsoft Azure Service Bus. L’exemple montre des clients simples et des serveurs communiquant via la rubrique Service Bus. |1\.8 |Microsoft Azure Service Bus ; Service Bus pour Windows Server |
| [Messagerie répartie : file d’attente de réponse à une demande](http://code.msdn.microsoft.com/Brokered-Messaging-Request-0ce8fcaf) |Montre comment utiliser Microsoft Azure Service Bus et la fonctionnalité demande/réponse. L’exemple montre des clients simples et des serveurs communiquant via deux files d’attente Service Bus. |1\.8 |Microsoft Azure Service Bus ; Service Bus pour Windows Server |
| [Messagerie répartie : détection des doublons](http://code.msdn.microsoft.com/Brokered-Messaging-c0acea25) |Montre comment utiliser la détection des messages en double de Microsoft Azure Service Bus avec les files d’attente. Deux files d’attente sont créées, l’une avec la détection de doublons activée et l’autre sans. |1\.8 |Microsoft Azure Service Bus ; Service Bus pour Windows Server |
| [Messagerie répartie : messagerie asynchrone](http://code.msdn.microsoft.com/Brokered-Messaging-Async-211c1e74) |Montre comment utiliser Microsoft Azure Service Bus pour envoyer et recevoir des messages de manière asynchrone à partir d’une file d’attente. La file d’attente fournit une communication asynchrone découplée entre un expéditeur et un ou plusieurs destinataires (ici, un seul destinataire). |1\.8 |Microsoft Azure Service Bus ; Service Bus pour Windows Server |
| [Messagerie répartie : filtres avancés](http://code.msdn.microsoft.com/Brokered-Messaging-6b0d2749) |Montre comment utiliser les filtres de publication/abonnement Microsoft Azure Service. Il crée une rubrique et 3 abonnements avec des définitions de filtre différentes, envoie des messages à la rubrique et reçoit tous les messages à partir des abonnements. |1\.8 |Microsoft Azure Service Bus ; Service Bus pour Windows Server |
| [Messagerie répartie : prérécupération des Messages](http://code.msdn.microsoft.com/Brokered-Messaging-be2dac1d) |Montre comment utiliser la fonctionnalité de prérécupération des messages Microsoft Azure Service Bus. Il montre comment utiliser la fonctionnalité de prérécupération des messages lors de la réception. |1\.8 |Microsoft Azure Service Bus ; Service Bus pour Windows Server |

## Service Bus Relay
Exemples qui illustrent le relais du Bus des services.

### Prise en main
| Nom d’exemple | Description | Kit de développement logiciel minimum | Availability |
| --- | --- | --- | --- |
| [Messagerie relayée : Azure](http://code.msdn.microsoft.com/Relayed-Messaging-Windows-0d2cede3) |Montre comment exécuter un service et le client Service Bus sur Azure. Cet exemple configure le Bus des services par programmation. Seules les informations de sécurité et d’environnement sont stockées dans les fichiers de configuration. |1\.8 |Microsoft Azure Service Bus |
| [Authentification de messagerie relayée : secret partagé](http://code.msdn.microsoft.com/Relayed-Messaging-92b04c02) |Montre comment utiliser le nom de d’émetteur et le secret de l’émetteur pour procéder à l’authentification avec Service Bus. |1\.8 |Microsoft Azure Service Bus |
| [Authentification de messagerie relayée : WebNoAuth](http://code.msdn.microsoft.com/Relayed-Messaging-a4f0b831) |Montre comment exposer un service HTTP qui ne nécessite pas d’authentification de client utilisateur. |1\.8 |Microsoft Azure Service Bus |
| [Liaisons de message relayées : WebHttp](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-a6477ba0) |Montre comment utiliser la liaison **WebHttpRelayBinding** liaison pour renvoyer des données binaires à l’aide du modèle de programmation web. |1\.8 |Microsoft Azure Service Bus |
| [Liaisons de message relayées : NetTcp Relayé](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-2dec7692) |Montre comment utiliser la liaison **NetTcpRelayBinding**. |1\.8 |Microsoft Azure Service Bus |

### Exploration des fonctions
Exemples qui illustrent différentes fonctions de relais Service Bus.

| Nom d’exemple | Description | Kit de développement logiciel minimum | Availability |
| --- | --- | --- | --- |
| [Authentification de messagerie relayée : Webtoken simple](http://code.msdn.microsoft.com/Relayed-Messaging-32c74392) |Montre comment utiliser les informations d’identification simples pour procéder à l’authentification avec Service Bus. Cet exemple est similaire à l’exemple Echo, avec quelques modifications. Plus précisément, cet exemple ajoute un comportement aux applications ServiceHost (service) et les applications ChannelFactory (client). |1\.8 |Microsoft Azure Service Bus |
| [Messagerie relayée : équilibrer la charge](http://code.msdn.microsoft.com/Relayed-Messaging-Load-bd76a9f8) |Montre comment utiliser Microsoft Azure Service Bus pour acheminer des messages vers plusieurs destinataires. Il montre plusieurs instances d’un simple service communiquant avec un client via la liaison **NetTcpRelayBinding** |1\.8 |Microsoft Azure Service Bus |
| [Liaisons de message relayées : événement Net](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-c0176977) |Montre l’utilisation de la liaison **NetEventRelayBinding** vers Microsoft Azure Service Bus. |1\.8 |Microsoft Azure Service Bus |
| [Liaisons de messagerie relayées : session WS2007Http](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-ef1f1fcb) |Illustre l’utilisation de la liaison **WS2007HttpRelayBinding** avec des sessions fiables activées. Il montre également comment spécifier des informations d’identification Service Bus dans le fichier de configuration et non de la programmation. |1\.8 |Microsoft Azure Service Bus |
| [Liaisons de messagerie relayées : WS2007Http MsgSecCertificate](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-f29c9da5) |Montre comment utiliser la liaison **WS2007HttpRelayBinding** avec la sécurité des messages pour sécuriser les messages de bout en bout tout en exigeant les clients à authentifier avec Service Bus. |1\.8 |Microsoft Azure Service Bus |
| [Messagerie relayée : échange de métadonnées](http://code.msdn.microsoft.com/Relayed-Messaging-Metadata-f122312e) |Montre comment exposer un point de terminaison de métadonnées qui utilise la liaison de relais. **MetadataExchange** est pris en charge par les liaisons de relais suivantes : **NetTcpRelayBinding**, **NetOnewayRelayBinding**, **BasicHttpRelayBinding** et **WS2007HttpRelayBinding**. |1\.8 |Microsoft Azure Service Bus |
| [Liaisons de messagerie relayées : NetTcp Direct](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-ca039161) |Montre comment configurer la liaison **NetTcpRelayBinding** pour prendre en charge le mode de connexion **hybride** qui établit une connexion relayée et, si possible, bascule automatiquement sur une connexion directe entre un client et un service. |1\.8 |Microsoft Azure Service Bus |
| [Liaisons de messagerie relayées : NetTcp MsgSec UserName](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-30542392) |Montre comment utiliser la liaison **NetTcpRelayBinding** avec une sécurité de message. |1\.8 |Microsoft Azure Service Bus |
| [Liaisons de message relayées : Net unidirectionnel](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-bb5b813a) |Montre comment exposer et consommer un point de terminaison de service à l’aide de la liaison **NetOnewayRelayBinding**. |1\.8 |Microsoft Azure Service Bus |
| [Liaisons de messagerie relayées : WS2007Http simple](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-aa4b793a) |Montre l’utilisation de la liaison **WS2007HttpRelayBinding**. Il montre un service simple qui n’utilise aucune option de sécurité et ne nécessite pas de clients à authentifier. |1\.8 |Microsoft Azure Service Bus |

## Outils de référence Service Bus
Les exemples suivants illustrent différentes autres fonctionnalités du service.

| Nom d’exemple | Description | Kit de développement logiciel minimum | Availability |
| --- | --- | --- | --- |
| [Explorateur Service Bus](http://code.msdn.microsoft.com/Service-Bus-Explorer-f2abca5a) |L’explorateur Service Bus permet aux utilisateurs de se connecter à un espace de noms de Service Bus et de gérer les entités de messagerie de manière simple. L’outil fournit des fonctionnalités avancées telles que les fonctionnalités d’importation/exportation et la possibilité de tester des entités de messagerie et des services de relais. |1\.8 |Microsoft Azure Service Bus ; Service Bus pour Windows Server |
| [Autorisation : SBAzTool](http://code.msdn.microsoft.com/Authorization-SBAzTool-6fd76d93) |Cet exemple montre comment créer et gérer les identités de service dans Microsoft Azure Active Directory Access Control (également appelé Service de contrôle d’accès ou ACS) pour une utilisation avec Service Bus. |N/A |Microsoft Azure Service Bus |

## Étapes suivantes
Consultez les rubriques suivantes pour obtenir des présentations conceptuelles de Service Bus.

* [Présentation de la messagerie Service Bus](../service-bus-messaging/service-bus-messaging-overview.md)
* [Architecture de Service Bus](../service-bus-messaging/service-bus-architecture.md)
* [Concepts de base de Service Bus](../service-bus-messaging/service-bus-fundamentals-hybrid-solutions.md)

<!---HONumber=AcomDC_0928_2016-->