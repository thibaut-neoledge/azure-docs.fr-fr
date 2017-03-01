---
title: "Azure App Service : Mise à l’échelle des applications d’App Service"
description: "Découvrez les tenants et les aboutissants de la mise à l’échelle d’application dans App Service."
keywords: "app service, azure app service, mise à l&quot;échelle, évolutif, plan app service, coût d&quot;app service"
services: app-service
documentationcenter: 
author: btardif
manager: erikre
editor: 
ms.assetid: f403c971-4450-432b-8cea-3eeb426c0147
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/07/2016
ms.author: byvinyal
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: ec043a9e01aca2e843e50cef8b90328ba1939ea5
ms.lasthandoff: 11/17/2016


---
# <a name="azure-app-service-scaling-app-service-applications"></a>Azure App Service : Mise à l’échelle des applications d’App Service
Les applications hébergées dans Azure App Service peuvent [être mises à l’échelle massivement](https://azure.microsoft.com/blog/canadian-broadcasting-corporation-radio-canada-leverage-azure-for-smooth-election-coverage/).
Toutefois, la mise à l’échelle d’une application est un problème complexe pour lequel il n’existe pas de solution universelle. Pour mettre à l’échelle correctement votre application, il convient de prendre en compte 3 domaines clés :

1. Comprendre l’architecture de votre application et ses faiblesses.
   * Votre application est-elle avec état ? Sans état ?
   * Quels sont les composants de votre application ?
     * Où se trouvent les goulots d’étranglement dans l’application ?
   * Lors de l’application de la charge à votre application, qu’est-ce qui s’arrête en premier ?
2. Comprendre les exigences de performances et de charge attendues.
   * L’application doit-elle répondre à 1 000 ou à un million d’utilisateurs ?
   * Le trafic provient-il d’un seul emplacement géographique ou du monde entier ?
   * Existe-t-il des variations saisonnières ? Des pics de trafic ?
   * À quelle vitesse, l’application doit-elle répondre ? 1 seconde ? 1 milliseconde ?
3. Comprendre et exploiter correctement la plateforme d’hébergement de votre application.
   * Quelles fonctionnalités dois-je utiliser pour atteindre mes objectifs de mise à l’échelle ?

Cette section vous aidera à comprendre tous les facteurs et à concevoir une stratégie tirant parti des fonctionnalités d’App Service nécessaires pour atteindre vos objectifs d’évolutivité.

[!INCLUDE [app-service-blueprint-scaling-app-service-applications](../../includes/app-service-blueprint-scaling-app-service-applications.md)]


