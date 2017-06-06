---
title: "Créer un espace de noms Service Bus dans le Portail Azure | Microsoft Docs"
description: "Création d’un espace de noms Service Bus à l’aide du Portail Azure."
services: service-bus-messaging
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: 
ms.assetid: fbb10e62-b133-4851-9d27-40bd844db3ba
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: get-started-article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 03/23/2017
ms.author: sethm
ms.translationtype: Human Translation
ms.sourcegitcommit: 0bec803e4b49f3ae53f2cc3be6b9cb2d256fe5ea
ms.openlocfilehash: 5de92033eb7eb4fef8d27a215b3284ab80594065
ms.contentlocale: fr-fr
ms.lasthandoff: 03/24/2017


---
# <a name="create-a-service-bus-namespace-using-the-azure-portal"></a>Créer un espace de noms Service Bus à l’aide du Portail Azure
Un espace de noms est un conteneur commun pour tous les composants de messagerie. Plusieurs files d’attente et rubriques peuvent résider dans un seul espace de noms, et les espaces de noms servent souvent de conteneurs d’applications. Il existe deux façons différentes de créer un espace de noms Service Bus :

1. Portail Azure (cet article)
2. [Modèles Microsoft Azure Resource Manager][create-namespace-using-arm]

## <a name="create-a-namespace-in-the-azure-portal"></a>Créer un espace de noms dans le Portail Azure
[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

Félicitations ! Vous venez de créer un espace de noms de messagerie Service Bus.

## <a name="next-steps"></a>Étapes suivantes
Consultez les [exemples GitHub][github-samples], qui illustrent certaines des fonctionnalités les plus avancées de la messagerie Azure Service Bus.

[create-namespace-using-arm]: service-bus-resource-manager-overview.md
[github-samples]: https://github.com/Azure-Samples/azure-servicebus-messaging-samples

