---
title: "Paramètres de port du relais WCF Azure Service Bus | Microsoft Docs"
description: Informations sur les valeurs de port du relais WCF Azure Service Bus.
services: service-bus-relay
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/08/2017
ms.author: sethm
translationtype: Human Translation
ms.sourcegitcommit: f222caa272b86ff42873df09561ca4f6c6e6aa26
ms.openlocfilehash: ba499a98960b0cf2f865e2d349003cd8cb65b526
ms.lasthandoff: 11/28/2016


---

# <a name="azure-relay-port-settings"></a>Paramètres de port du relais Azure

Le tableau suivant décrit la configuration requise pour les valeurs de port d’une liaison de relais WCF Service Bus.

## <a name="ports"></a>Ports
  
|Liaison|Sécurité de transport|Port|  
|-------------|------------------------|----------|  
|[Classe BasicHttpRelayBinding Class](/dotnet/api/microsoft.servicebus.basichttprelaybinding) (client)|Oui|HTTPS| 
| |" |Non|HTTP|  
|[Classe BasicHttpRelayBinding](/dotnet/api/microsoft.servicebus.basichttprelaybinding) (service)|Vous pouvez soit utiliser|9351/HTTP|  
|[Classe NetEventRelayBinding](/dotnet/api/microsoft.servicebus.neteventrelaybinding) (client)|Oui|9351/HTTPS|  
||" |Non|9350/HTTP|  
|[Classe NetEventRelayBinding](/dotnet/api/microsoft.servicebus.neteventrelaybinding) (service)|Vous pouvez soit utiliser|9351/HTTP|  
|[Classe NetTcpRelayBinding](/dotnet/api/microsoft.servicebus.nettcprelaybinding) (client/service)|Vous pouvez soit utiliser|5671/9352/HTTP (9352/9353 en cas d’utilisation hybride)|  
|[Classe NetOnewayRelayBinding](/dotnet/api/microsoft.servicebus.netonewayrelaybinding) (client)|Oui|9351/HTTPS|  
||" |Non|9350/HTTP|  
|[Classe NetOnewayRelayBinding](/dotnet/api/microsoft.servicebus.netonewayrelaybinding) (service)|Vous pouvez soit utiliser|9351/HTTP|  
|[Classe WebHttpRelayBinding](/dotnet/api/microsoft.servicebus.webhttprelaybinding) (client)|Oui|HTTPS|  
||" |Non|HTTP|  
|[Classe WebHttpRelayBinding](/dotnet/api/microsoft.servicebus.webhttprelaybinding) (service)|Vous pouvez soit utiliser|9351/HTTP|  
|[Classe WS2007HttpRelayBinding](/dotnet/api/microsoft.servicebus.ws2007httprelaybinding) (client)|Oui|HTTPS|  
||" |Non|HTTP|  
|[Classe WS2007HttpRelayBinding](/dotnet/api/microsoft.servicebus.ws2007httprelaybinding) (service)|Vous pouvez soit utiliser|9351/HTTP|

## <a name="next-steps"></a>Étapes suivantes
Pour en savoir plus sur la messagerie Service Bus, voir les rubriques suivantes.

* [Concepts de base de Service Bus](../service-bus-messaging/service-bus-fundamentals-hybrid-solutions.md)
* [Files d’attente, rubriques et abonnements Service Bus](../service-bus-messaging/service-bus-queues-topics-subscriptions.md)
* [Utilisation des files d’attente Service Bus](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md)
* [Utilisation des rubriques et abonnements Service Bus](../service-bus-messaging/service-bus-dotnet-how-to-use-topics-subscriptions.md)

