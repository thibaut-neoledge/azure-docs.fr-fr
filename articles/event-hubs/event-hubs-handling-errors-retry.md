---
title: Bonnes pratiques pour la gestion des erreurs dans Azure Event Hubs | Microsoft Docs
description: Gestion des erreurs et des nouvelles tentatives dans Azure Event Hubs
services: event-hubs
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 04/09/2017
ms.author: sethm
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: 200bc0828574bca72739dea5badb22da129cd896
ms.lasthandoff: 04/12/2017

---

# <a name="-event-hubs-best-practices-for-handling-errors-and-retry"></a>🔧 Bonnes pratiques pour la gestion des erreurs et des nouvelles tentatives dans Event Hubs

> [!NOTE]
> 
> Cette rubrique n’a pas encore été écrite ! 
>
> Nous apprécions votre contribution pour déterminer l’étendue et l’approche à mettre en œuvre pour ce contenu. Vous pouvez suivre l’état et fournir une contribution sur ce [problème dans GitHub](https://github.com/Azure/azure-event-hubs/issues/305).
> 
> Découvrez comment vous pouvez apporter votre contribution sur [GitHub](https://github.com/Microsoft/azure-docs/blob/master/contributor-guide/contributor-guide-index.md).

Cet article couvre ce qui suit :

- Quels sont les différents types d’erreurs ?
- Que se passe-t-il quand je reçois une erreur ?
- Quels types d’erreurs doivent faire l’objet d’une nouvelle tentative ? Lesquels ne doivent pas faire l’objet d’une nouvelle tentative ?
- Stratégie de nouvelle tentative personnalisée

