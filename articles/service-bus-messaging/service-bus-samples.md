---
title: "Vue d’ensemble d’exemples de messagerie Service Bus | Microsoft Docs"
description: "Classe et décrit les exemples de messagerie Service Bus avec des liens vers chacun d’eux."
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 0b420343-2d2a-4c65-98f1-ee0e39ef55c8
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/07/2016
ms.author: sethm
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 719403ca2ef90cdcf5f76fe15f7a33095e71a74c


---
# <a name="service-bus-messaging-samples"></a>Exemples de messagerie Service Bus
Les exemples de messagerie Service Bus illustrent des fonctionnalités clés de la [Messagerie Service Bus](https://azure.microsoft.com/services/service-bus/) (service cloud) et [Service Bus pour Windows Server](https://msdn.microsoft.com/library/dn282144.aspx). Cette article attribue une catégorie et décrit les exemples disponibles, avec des liens vers chacun d’eux.

> [!NOTE]
> Les exemples Service Bus ne sont pas installés avec le Kit de développement logiciel. Pour obtenir des exemples, visitez la [page d’exemples de kit de développement logiciels Microsoft Azure](https://code.msdn.microsoft.com/site/search?query=service%20bus&f%5B0%5D.Value=service%20bus&f%5B0%5D.Type=SearchText&ac=5).
> 
> Un ensemble actualisé d’exemples de messagerie Service Bus est également disponible [ici](https://github.com/Azure-Samples/azure-servicebus-messaging-samples) (ils ne sont pas décrits dans cet article pour le moment).  
> 
> 

Pour voir des exemples de relais, consultez [Exemples Service Bus Relay](../service-bus-relay/service-bus-relay-samples.md).

## <a name="service-bus-messaging"></a>Messagerie Service Bus
Les exemples suivants montrent comment écrire des applications qui utilisent la messagerie Service Bus.

Notez que les exemples de messagerie requièrent une chaîne de connexion pour accéder à votre espace de noms Service Bus.

### <a name="to-obtain-a-connection-string-for-azure-service-bus"></a>Pour obtenir une chaîne de connexion pour Azure Service Bus.
1. Connectez-vous au [Portail Azure](http://portal.azure.com).
2. Dans la colonne de gauche, cliquez sur **Service Bus**.
3. Cliquez sur le nom de votre espace de noms dans la liste.
4. Dans le panneau Espace de noms, cliquez sur **Stratégies d’accès partagé**.
5. Dans le panneau **Stratégies d’accès partagé**, cliquez sur **RootManageSharedAccessKey**.
6. Copiez la chaîne de connexion dans le Presse-papiers.

### <a name="to-obtain-a-connection-string-for-service-bus-for-windows-server"></a>Pour obtenir une chaîne de connexion de Service Bus pour Windows Server
1. Exécutez l’applet de commande PowerShell suivant :
   
    ```
    get-sbClientConfiguration
    ```
2. Collez la chaîne de connexion dans le fichier App.config de l’exemple.

### <a name="getting-started-samples"></a>Exemples de mise en route
Ces exemples décrivent les fonctionnalités de messagerie de base.

| Nom d’exemple | Description | Kit de développement logiciel minimum | Availability |
| --- | --- | --- | --- |
| [Mise en route : messagerie avec files d’attente](http://code.msdn.microsoft.com/Getting-Started-Brokered-aa7a0ac3) |Montre comment utiliser Microsoft Azure Service Bus pour envoyer et recevoir des messages à partir d’une file d’attente. |1.8 |Microsoft Azure Service Bus ; Service Bus pour Windows Server |
| [Mise en route : messagerie avec rubriques](http://code.msdn.microsoft.com/Getting-Started-Brokered-614d42e5) |Montre comment utiliser Microsoft Azure Service Bus pour envoyer et recevoir des messages à partir d’une rubrique avec plusieurs abonnements. |1.8 |Microsoft Azure Service Bus ; Service Bus pour Windows Server |

### <a name="exploring-features"></a>Exploration des fonctions
Les exemples suivants illustrent différentes fonctionnalités de Service Bus.

| Nom d’exemple | Description | Kit de développement logiciel minimum | Availability |
| --- | --- | --- | --- |
| [Fournisseurs de jeton HTTP](http://code.msdn.microsoft.com/Service-Bus-HTTP-Token-38f2cfc5) |Illustre les différents modes d’authentification d’un client HTTP/REST avec Service Bus. |2.1 |Microsoft Azure Service Bus ; Service Bus pour Windows Server |
| [Client HTTP Service Bus](http://code.msdn.microsoft.com/Service-Bus-HTTP-client-fe7da74a) |Montre comment envoyer et recevoir des messages de Service Bus via HTTP/REST. |2.3 |Microsoft Azure Service Bus ; Service Bus pour Windows Server |
| [Transfert automatique Service Bus](http://code.msdn.microsoft.com/Service-Bus-Autoforwarding-b9df470b) |Montre comment transférer automatiquement les messages depuis une file d’attente, un abonnement ou une file d’attente de rebut dans une autre file d’attente ou rubrique. Il montre également comment envoyer un message dans une file d’attente ou une rubrique via une file d’attente de transfert. |2.3 |Microsoft Azure Service Bus ; Service Bus pour Windows Server |
| [Messagerie répartie : exemple de session de canal WCF](http://code.msdn.microsoft.com/Brokered-Messaging-WCF-0a526451) |Montre comment utiliser Microsoft Azure Service Bus avec canaux Windows Communication Foundation (WCF). L’exemple montre l’utilisation de canaux WCF pour envoyer et recevoir des messages via une file d’attente Service Bus. L’exemple montre les communications de session et non-session via le Bus de Service. |1.8 |Microsoft Azure Service Bus ; Service Bus pour Windows Server |
| [Messagerie répartie : transactions](http://code.msdn.microsoft.com/Brokered-Messaging-8cd41d1e) |Montre comment utiliser les fonctions de messagerie Microsoft Azure Service Bus au sein d’une étendue de transaction, afin de garantir des lots d’opérations de messagerie sont validés automatiquement. |1.8 |Microsoft Azure Service Bus ; Service Bus pour Windows Server |
| [Messagerie répartie : opérations de gestion avec REST](http://code.msdn.microsoft.com/Brokered-Messaging-569cff88) |Montre comment effectuer des opérations de gestion sur le Bus de Service à l’aide de REST. |1.8 |Microsoft Azure Service Bus ; Service Bus pour Windows Server |
| [Fournisseur de ressources API REST](http://code.msdn.microsoft.com/Service-Bus-Resource-5d887203) |Montre comment utiliser les nouvelles API de REST RDFE Service Bus pour gérer les espaces de noms et les entités de messagerie. |1.8 |Microsoft Azure Service Bus ; Service Bus pour Windows Server |
| [Messagerie répartie : exemple de session de service WCF](http://code.msdn.microsoft.com/Brokered-Messaging-WCF-db4262c2) |Montre comment utiliser Microsoft Azure Service Bus en utilisant le modèle de service WCF. L’exemple montre l’utilisation du modèle de service WCF pour accomplir des sessions de communication via une file d’attente Service Bus. |1.8 |Microsoft Azure Service Bus ; Service Bus pour Windows Server |
| [Messagerie répartie : réponse à une demande](http://code.msdn.microsoft.com/Brokered-Messaging-Request-2b4ff5d8) |Montre comment utiliser Microsoft Azure Service Bus et la fonctionnalité de demande/réponse. L’exemple montre des clients simples et des serveurs communiquant via une file d’attente Service Bus. |1.8 |Microsoft Azure Service Bus ; Service Bus pour Windows Server |
| [Messagerie répartie : file d’attente de rebut](http://code.msdn.microsoft.com/Brokered-Messaging-Dead-22536dd8) |Montre comment utiliser Microsoft Azure Service Bus et la fonctionnalité de messagerie « file d’attente de rebut ». L’exemple montre un expéditeur et un destinataire simples communiquant via une file d’attente Service Bus. |1.8 |Microsoft Azure Service Bus ; Service Bus pour Windows Server |
| [Messagerie répartie : Messages différés](http://code.msdn.microsoft.com/Brokered-Messaging-ccc4f879) |Montre comment utiliser la fonction de différé de message de Microsoft Azure Service Bus. L’exemple montre un expéditeur et un destinataire simples communiquant via une file d’attente Service Bus. |1.8 |Microsoft Azure Service Bus ; Service Bus pour Windows Server |
| [Messagerie répartie : messages de session](http://code.msdn.microsoft.com/Brokered-Messaging-Session-41c43fb4) |Montre comment utiliser Microsoft Azure Service Bus et la fonctionnalité de messagerie de session. L’exemple montre des expéditeurs et des destinataires simples communiquant via une file d’attente Service Bus. |1.8 |Microsoft Azure Service Bus ; Service Bus pour Windows Server |
| [Messagerie répartie : rubrique de réponse à une demande](http://code.msdn.microsoft.com/Brokered-Messaging-Request-6759a36e) |Montre comment mettre en œuvre le modèle demande/réponse à l’aide des rubriques et des abonnements Microsoft Azure Service Bus. L’exemple montre des clients simples et des serveurs communiquant via la rubrique Service Bus. |1.8 |Microsoft Azure Service Bus ; Service Bus pour Windows Server |
| [Messagerie répartie : file d’attente de réponse à une demande](http://code.msdn.microsoft.com/Brokered-Messaging-Request-0ce8fcaf) |Montre comment utiliser Microsoft Azure Service Bus et la fonctionnalité demande/réponse. L’exemple montre des clients simples et des serveurs communiquant via deux files d’attente Service Bus. |1.8 |Microsoft Azure Service Bus ; Service Bus pour Windows Server |
| [Messagerie répartie : détection des doublons](http://code.msdn.microsoft.com/Brokered-Messaging-c0acea25) |Montre comment utiliser la détection des messages en double de Microsoft Azure Service Bus avec les files d’attente. Deux files d’attente sont créées, l’une avec la détection de doublons activée et l’autre sans. |1.8 |Microsoft Azure Service Bus ; Service Bus pour Windows Server |
| [Messagerie répartie : messagerie asynchrone](http://code.msdn.microsoft.com/Brokered-Messaging-Async-211c1e74) |Montre comment utiliser Microsoft Azure Service Bus pour envoyer et recevoir des messages de manière asynchrone à partir d’une file d’attente. La file d’attente fournit une communication asynchrone découplée entre un expéditeur et un ou plusieurs destinataires (ici, un seul destinataire). |1.8 |Microsoft Azure Service Bus ; Service Bus pour Windows Server |
| [Messagerie répartie : filtres avancés](http://code.msdn.microsoft.com/Brokered-Messaging-6b0d2749) |Montre comment utiliser les filtres de publication/abonnement Microsoft Azure Service. Il crée une rubrique et 3 abonnements avec des définitions de filtre différentes, envoie des messages à la rubrique et reçoit tous les messages à partir des abonnements. |1.8 |Microsoft Azure Service Bus ; Service Bus pour Windows Server |
| [Messagerie répartie : prérécupération des Messages](http://code.msdn.microsoft.com/Brokered-Messaging-be2dac1d) |Montre comment utiliser la fonctionnalité de prérécupération des messages Microsoft Azure Service Bus. Il montre comment utiliser la fonctionnalité de prérécupération des messages lors de la réception. |1.8 |Microsoft Azure Service Bus ; Service Bus pour Windows Server |

## <a name="service-bus-reference-tools"></a>Outils de référence Service Bus
Les exemples suivants illustrent différentes autres fonctionnalités du service.

| Nom d’exemple | Description | Kit de développement logiciel minimum | Availability |
| --- | --- | --- | --- |
| [Explorateur Service Bus](http://code.msdn.microsoft.com/Service-Bus-Explorer-f2abca5a) |L’explorateur Service Bus permet aux utilisateurs de se connecter à un espace de noms de Service Bus et de gérer les entités de messagerie de manière simple. L’outil fournit des fonctionnalités avancées telles que les fonctionnalités d’importation/exportation et la possibilité de tester des entités de messagerie et des services de relais. |1.8 |Microsoft Azure Service Bus ; Service Bus pour Windows Server |
| [Autorisation : SBAzTool](http://code.msdn.microsoft.com/Authorization-SBAzTool-6fd76d93) |Cet exemple montre comment créer et gérer les identités de service dans Microsoft Azure Active Directory Access Control (également appelé Service de contrôle d’accès ou ACS) pour une utilisation avec Service Bus. |N/A |Microsoft Azure Service Bus |

## <a name="next-steps"></a>Étapes suivantes
Consultez les rubriques suivantes pour obtenir des présentations conceptuelles de Service Bus.

* [Présentation de la messagerie Service Bus](service-bus-messaging-overview.md)
* [Architecture de Service Bus](service-bus-architecture.md)
* [Concepts de base de Service Bus](service-bus-fundamentals-hybrid-solutions.md)




<!--HONumber=Nov16_HO3-->


