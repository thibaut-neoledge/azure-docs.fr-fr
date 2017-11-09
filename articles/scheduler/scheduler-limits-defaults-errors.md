---
title: "Limites et valeurs par défaut de Scheduler"
description: "Limites et valeurs par défaut de Scheduler"
services: scheduler
documentationcenter: .NET
author: derek1ee
manager: kevinlam1
editor: 
ms.assetid: 88f4a3e9-6dbd-4943-8543-f0649d423061
ms.service: scheduler
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 08/18/2016
ms.author: deli
ms.openlocfilehash: db6b1c196cb468f41c7a7ce34758de346b522abb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="scheduler-limits-and-defaults"></a>Limites et valeurs par défaut de Scheduler
## <a name="scheduler-quotas-limits-defaults-and-throttles"></a>Limitations, valeurs par défaut, limites et quotas de Scheduler
[!INCLUDE [scheduler-limits-table](../../includes/scheduler-limits-table.md)]

## <a name="the-x-ms-request-id-header"></a>L'en-tête x-ms-request-id
Chaque requête adressée au service de Scheduler retourne un en-tête de réponse nommé**x-ms-request-id**. Cet en-tête contient une valeur opaque qui identifie de façon unique la requête.

Si une requête échoue constamment et que vous avez vérifié qu'elle est formulée correctement, vous pouvez utiliser cette valeur pour signaler l'erreur à Microsoft. Dans votre rapport, incluez la valeur de x-ms-request-id, l’heure approximative de la requête, l’identificateur de l’abonnement, la collection de travaux et/ou la tâche et le type d’opération tenté par la requête.

## <a name="see-also"></a>Voir aussi
 [Présentation d'Azure Scheduler](scheduler-intro.md)

 [Concepts, terminologie et hiérarchie d’entités d’Azure Scheduler](scheduler-concepts-terms.md)

 [Prise en main de Scheduler dans le portail Azure](scheduler-get-started-portal.md)

 [Plans et facturation dans Azure Scheduler](scheduler-plans-billing.md)

 [Informations de référence sur l’API REST d’Azure Scheluler](https://msdn.microsoft.com/library/mt629143)

 [Informations de référence sur les applets de commande PowerShell d’Azure Scheluler](scheduler-powershell-reference.md)

 [Haute disponibilité et fiabilité d’Azure Scheluler](scheduler-high-availability-reliability.md)

 [Authentification sortante d’Azure Scheluler](scheduler-outbound-authentication.md)

