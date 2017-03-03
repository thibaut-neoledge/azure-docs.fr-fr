---
title: "Vue d’ensemble des exemples de relais Azure Service Bus | Microsoft Docs"
description: "Classe et décrit les exemples de relais Service Bus avec des liens vers chacun d’eux."
services: service-bus-relay
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 9237a9a2-f126-4d3f-9f9b-604ee6b32153
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/13/2017
ms.author: sethm
translationtype: Human Translation
ms.sourcegitcommit: 6027c973f0104fff9705a70f7812f62562165c1e
ms.openlocfilehash: d830770c49caeacf762382ce1c6113d9196a5994
ms.lasthandoff: 02/14/2017


---
# <a name="service-bus-relay-samples"></a>Exemples de relais Service Bus
Les exemples de relais Service Bus illustrent des fonctionnalités clés de [Service Bus Relay](https://azure.microsoft.com/services/service-bus/). Cette article attribue une catégorie et décrit les exemples disponibles, avec des liens vers chacun d’eux.

> [!NOTE]
> Les exemples Service Bus ne sont pas installés avec le Kit de développement logiciel (SDK) Azure. Pour obtenir des exemples, visitez la [page d’exemples de kit de développement logiciels Microsoft Azure](https://code.msdn.microsoft.com/site/search?query=service%20bus&f%5B0%5D.Value=service%20bus&f%5B0%5D.Type=SearchText&ac=5).
> 
> Par ailleurs, vous trouverez [ici](https://github.com/Azure-Samples/azure-servicebus-relay-samples) un ensemble mis à jour d’exemples de relais Service Bus.  
> 
> 

Pour voir des exemples de messagerie Service Bus, consultez la page [Exemples de messagerie Service Bus](../service-bus-messaging/service-bus-samples.md).

## <a name="azure-service-bus-relay"></a>Azure Service Bus Relay
Les exemples suivants montrent comment écrire des applications qui utilisent le service Azure Relay.

Notez que les exemples de relais requièrent une chaîne de connexion pour accéder à votre espace de noms Relay.

### <a name="to-obtain-a-connection-string-for-azure-relay"></a>Pour obtenir une chaîne de connexion pour Azure Relay :
1. Connectez-vous au [Portail Azure](http://portal.azure.com).
2. Cliquez pour développer la liste de vos espaces de noms dans le volet **Toutes les ressources**.
3. Cliquez sur le nom de votre espace de noms Relay dans la liste.
4. Dans le panneau Espace de noms, cliquez sur **Stratégies d’accès partagé**.
5. Dans le panneau **Stratégies d’accès partagé**, cliquez sur **RootManageSharedAccessKey**.
6. Copiez la chaîne de connexion dans le Presse-papiers.

### <a name="to-obtain-a-connection-string-for-service-bus-for-windows-server"></a>Pour obtenir une chaîne de connexion de Service Bus pour Windows Server
1. Exécutez l’applet de commande PowerShell suivant :
   
    ```powershell
    get-sbClientConfiguration
    ```
2. Collez la chaîne de connexion dans le fichier App.config de l’exemple.

## <a name="azure-relay"></a>Azure Relay
Exemples qui illustrent Azure Relay.

### <a name="getting-started"></a>Prise en main
| Nom d’exemple | Description | Kit de développement logiciel minimum | Availability |
| --- | --- | --- | --- |
| [Messagerie relayée WCF : Azure](http://code.msdn.microsoft.com/Relayed-Messaging-Windows-0d2cede3) |Montre comment exécuter un service et le client Service Bus sur Azure. Cet exemple configure le Bus des services par programmation. Seules les informations de sécurité et d’environnement sont stockées dans les fichiers de configuration. |1.8 |Microsoft Azure Service Bus |
| [Authentification de messagerie relayée WCF : secret partagé](http://code.msdn.microsoft.com/Relayed-Messaging-92b04c02) |Montre comment utiliser le nom de d’émetteur et le secret de l’émetteur pour procéder à l’authentification avec Service Bus. |1.8 |Microsoft Azure Service Bus |
| [Authentification de messagerie relayée WCF : WebNoAuth](http://code.msdn.microsoft.com/Relayed-Messaging-a4f0b831) |Montre comment exposer un service HTTP qui ne nécessite pas d’authentification de client utilisateur. |1.8 |Microsoft Azure Service Bus |
| [Liaisons de messagerie relayée WCF : WebHttp](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-a6477ba0) |Montre comment utiliser la liaison **WebHttpRelayBinding** liaison pour renvoyer des données binaires à l’aide du modèle de programmation web. |1.8 |Microsoft Azure Service Bus |
| [Liaisons de messagerie relayée WCF : NetTcp Relayé](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-2dec7692) |Montre comment utiliser la liaison **NetTcpRelayBinding** . |1.8 |Microsoft Azure Service Bus |

### <a name="exploring-features"></a>Exploration des fonctions
Exemples qui illustrent différentes fonctions de relais Service Bus.

| Nom d’exemple | Description | Kit de développement logiciel minimum | Availability |
| --- | --- | --- | --- |
| [Authentification de messagerie relayée WCF : Webtoken simple](http://code.msdn.microsoft.com/Relayed-Messaging-32c74392) |Montre comment utiliser les informations d’identification simples pour procéder à l’authentification avec Service Bus. Cet exemple est similaire à l’exemple Echo, avec quelques modifications. Plus précisément, cet exemple ajoute un comportement aux applications ServiceHost (service) et les applications ChannelFactory (client). |1.8 |Microsoft Azure Service Bus |
| [Messagerie relayée WCF : équilibrer la charge](http://code.msdn.microsoft.com/Relayed-Messaging-Load-bd76a9f8) |Montre comment utiliser Microsoft Azure Service Bus pour acheminer des messages vers plusieurs destinataires. Il montre plusieurs instances d’un simple service communiquant avec un client via la liaison **NetTcpRelayBinding** |1.8 |Microsoft Azure Service Bus |
| [Liaisons de messagerie relayée WCF : événement Net](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-c0176977) |Montre l’utilisation de la liaison **NetEventRelayBinding** vers Microsoft Azure Service Bus. |1.8 |Microsoft Azure Service Bus |
| [Liaisons de messagerie relayée WCF : session WS2007Http](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-ef1f1fcb) |Illustre l’utilisation de la liaison **WS2007HttpRelayBinding** avec des sessions fiables activées. Il montre également comment spécifier des informations d’identification Service Bus dans le fichier de configuration et non de la programmation. |1.8 |Microsoft Azure Service Bus |
| [Liaisons de messagerie relayée WCF : WS2007Http MsgSecCertificate](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-f29c9da5) |Montre comment utiliser la liaison **WS2007HttpRelayBinding** avec la sécurité des messages pour sécuriser les messages de bout en bout tout en exigeant les clients à authentifier avec Service Bus. |1.8 |Microsoft Azure Service Bus |
| [Messagerie relayée WCF : échange de métadonnées](http://code.msdn.microsoft.com/Relayed-Messaging-Metadata-f122312e) |Montre comment exposer un point de terminaison de métadonnées qui utilise la liaison de relais. **MetadataExchange** est pris en charge par les liaisons de relais suivantes : **NetTcpRelayBinding**, **NetOnewayRelayBinding**, **BasicHttpRelayBinding** et **WS2007HttpRelayBinding**. |1.8 |Microsoft Azure Service Bus |
| [Liaisons de messagerie relayée WCF : NetTcp Direct](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-ca039161) |Montre comment configurer la liaison **NetTcpRelayBinding** pour prendre en charge le mode de connexion **hybride** qui établit une connexion relayée et, si possible, bascule automatiquement sur une connexion directe entre un client et un service. |1.8 |Microsoft Azure Service Bus |
| [Liaisons de messagerie relayée WCF : NetTcp MsgSec UserName](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-30542392) |Montre comment utiliser la liaison **NetTcpRelayBinding** avec une sécurité de message. |1.8 |Microsoft Azure Service Bus |
| [Liaisons de messagerie relayée WCF : Net unidirectionnel](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-bb5b813a) |Montre comment exposer et consommer un point de terminaison de service à l’aide de la liaison **NetOnewayRelayBinding** . |1.8 |Microsoft Azure Service Bus |
| [Liaisons de messagerie relayée WCF : WS2007Http simple](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-aa4b793a) |Montre l’utilisation de la liaison **WS2007HttpRelayBinding** . Il montre un service simple qui n’utilise aucune option de sécurité et ne nécessite pas de clients à authentifier. |1.8 |Microsoft Azure Service Bus |

## <a name="next-steps"></a>Étapes suivantes
Consultez les rubriques suivantes pour obtenir des présentations conceptuelles de Service Bus.

* [Vue d’ensemble d’Azure Relay](relay-what-is-it.md)
* [Architecture de Service Bus](../service-bus-messaging/service-bus-architecture.md)
* [Concepts de base de Service Bus](../service-bus-messaging/service-bus-fundamentals-hybrid-solutions.md)


