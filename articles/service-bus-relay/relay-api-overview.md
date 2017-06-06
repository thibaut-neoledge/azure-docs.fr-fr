---
title: "Vue d’ensemble de l’API Azure Relay | Microsoft Docs"
description: "Vue d’ensemble des API Azure Relay disponibles"
services: event-hubs
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: fdaa1d2b-bd80-4e75-abb9-0c3d0773af2d
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/24/2017
ms.author: sethm
ms.translationtype: Human Translation
ms.sourcegitcommit: 356de369ec5409e8e6e51a286a20af70a9420193
ms.openlocfilehash: edc730aa383b07dc308da8a160203faf3636208b
ms.contentlocale: fr-fr
ms.lasthandoff: 03/27/2017

---

# <a name="available-relay-apis"></a>API Relay disponibles

## <a name="runtime-apis"></a>API de runtime

Vous trouverez ci-dessous une liste de tous les clients de runtime Relay actuellement disponibles.

Consultez la section [Informations supplémentaires](#additional-information) pour plus de détails sur l’état de chaque bibliothèque de runtime.

| Langue/plateforme | Fonctionnalité disponible | Package client | Référentiel |
| --- | --- | --- | --- |
| .NET Standard | les connexions hybrides | [Microsoft.Azure.Relay](https://www.nuget.org/packages/Microsoft.Azure.Relay/) | [GitHub](https://github.com/azure/azure-relay-dotnet) |
| .NET Framework | Relais WCF | [WindowsAzure.ServiceBus](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) | N/A |
| Nœud | les connexions hybrides | [hyco-ws](https://www.npmjs.com/package/hyco-ws)<br/>[hyco-websocket](https://www.npmjs.com/package/hyco-websocket) | [GitHub](https://github.com/Azure/azure-relay-node) |

### <a name="additional-information"></a>Informations supplémentaires

#### <a name="net"></a>.NET
L’écosystème .NET a plusieurs runtimes, par conséquent, il existe plusieurs bibliothèques .NET pour Event Hubs. La bibliothèque .NET Standard peut être exécutée à l’aide de .NET Core ou de .NET Framework, tandis que la bibliothèque .NET Framework peut uniquement être exécutée dans un environnement .NET Framework. Pour plus d’informations sur .NET Framework, consultez les [versions d’infrastructure](https://docs.microsoft.com/dotnet/articles/standard/frameworks#framework-versions).

## <a name="next-steps"></a>Étapes suivantes
Pour en savoir plus sur Azure Relay, consultez les liens suivants :
* [Qu’est-ce qu’Azure Relay ?](relay-what-is-it.md)
* [FAQ Relay](relay-faq.md)
