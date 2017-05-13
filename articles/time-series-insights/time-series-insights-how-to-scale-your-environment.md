---
title: "Mise à l’échelle de votre environnement Azure Time Series Insights | Microsoft Docs"
description: "Ce tutoriel explique comment mettre à l’échelle votre environnement Azure Time Series Insights."
keywords: 
services: time-series-insights
documentationcenter: 
author: sandshadow
manager: almineev
editor: cgronlun
ms.assetid: 
ms.service: time-series-insights
ms.devlang: na
ms.topic: how-to-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/19/2017
ms.author: edett
ms.translationtype: Human Translation
ms.sourcegitcommit: 7f8b63c22a3f5a6916264acd22a80649ac7cd12f
ms.openlocfilehash: 8f6c66ea2173c98179ec899d6626c2ab6f7ec4b6
ms.contentlocale: fr-fr
ms.lasthandoff: 05/01/2017

---
# <a name="how-to-scale-your-time-series-insights-environment"></a>Mise à l’échelle de votre environnement Time Series Insights

Ce tutoriel explique comment mettre à l’échelle votre environnement Time Series Insights.

> [!NOTE]
> La mise à l’échelle sur différents types de référence n’est pas autorisée. Un environnement avec une référence S1 ne peut pas être converti en environnement S2.

## <a name="s1-sku-ingress-rates-and-capacities"></a>Capacités et débits d’entrée de la référence S1

| Capacité de la référence S1 | Débit d’entrée | Capacité de stockage maximale
| --- | --- | --- |
| 1 | 1 Go (1 million d’événements) | 30 Go (30 millions d’événements) par mois |
| 10 | 10 Go (10 millions d’événements) | 300 Go (300 millions d’événements) par mois |

## <a name="s2-sku-ingress-rates-and-capacities"></a>Capacités et débits d’entrée de la référence S2

| Capacité de la référence S2 | Débit d’entrée | Capacité de stockage maximale
| --- | --- | --- |
| 1 | 10 Go (10 millions d’événements) | 300 Go (300 millions d’événements) par mois |
| 10 | 100 Go (100 millions d’événements) | 3 To (3 milliards d’événements) par mois |

Les capacités sont mises à l’échelle de façon linéaire. Par conséquent, une référence S1 avec la capacité 2 prend en charge 2 Go (2 millions) d’événements par débit d’entrée par jour et 60 Go (60 millions d’événements) par mois.

## <a name="changing-the-capacity-of-your-environment"></a>Modifier la capacité de votre environnement

1. Dans le portail Azure, sélectionnez l’environnement dont vous voulez changer la capacité.
1. Sous Paramètres, cliquez sur Configurer.
1. Utilisez le curseur de capacité pour sélectionner la capacité conforme à la configuration requise pour les débits d’entrée et la capacité de stockage.

## <a name="next-steps"></a>Étapes suivantes

* Vérifiez que la nouvelle capacité est suffisante pour éviter la limitation. Pour plus d’informations, consultez *Votre environnement pourrait être sujet à des limitations* [ici](time-series-insights-diagnose-and-solve-problems.md).
