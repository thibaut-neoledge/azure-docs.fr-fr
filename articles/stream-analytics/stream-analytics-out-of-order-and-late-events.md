---
title: "Traitement de l’ordre et du retard des événements avec Azure Stream Analytics | Microsoft Docs"
description: "Découvrez le fonctionnement de Stream Analytics avec des événements en retard ou en désordre dans les flux de données."
keywords: "Événements en désordre, en retard"
documentationcenter: 
services: stream-analytics
author: jeffstokes72
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 04/20/2017
ms.author: jeffstok
ms.translationtype: Human Translation
ms.sourcegitcommit: 7f8b63c22a3f5a6916264acd22a80649ac7cd12f
ms.openlocfilehash: 02c854727e5185968dba49233df82ea6ff336ec8
ms.contentlocale: fr-fr
ms.lasthandoff: 05/01/2017


---
# <a name="azure-stream-analytics-event-order-handling"></a>Traitement de l’ordre des événements Azure Stream Analytics

Dans un flux de données temporelles d’événements, chaque événement est enregistré avec l’heure de réception associée. En raison de certaines conditions, il se peut parfois que des flux d’événements reçoivent certains événements dans un ordre différent que celui dans lequel ils ont été envoyés. Une simple retransmission TCP, ou même une différence d’heure entre l’appareil d’envoi et le Event Hub de réception, peut entraîner ce problème. Les événements de type « Ponctuation » sont également ajoutés dans des flux d’événement reçus, pour avancer l’heure en l’absence d’arrivée de l’événement. Ils sont nécessaires dans des scénarios tels que « M’informer lorsque aucune connexion ne se produit pendant 3 minutes ».

Les flux d’entrée qui ne sont pas dans l’ordre sont :
* Triés (et par conséquent **retardés**).
* Ajustés par le système, en fonction d’une stratégie spécifiée par l’utilisateur.


## <a name="lateness-tolerance"></a>Tolérance de retard
Stream Analytics tolère les types de scénarios suivants. Le service Stream Analytics dispose d’une gestion des événements « en désordre » et « en retard ». Il traite ces événements de la façon suivante :

* Les événements qui arrivent dans le désordre, mais dans la tolérance définie, sont **réorganisés par horodatage**.
* Les événements qui arrivent plus tard que la tolérance sont **supprimés ou ajustés**.
    * **Ajustés** : les événements sont ajustés de façon à ce qu’ils semblent arrivés à l’heure la plus récente acceptable.
    * **Supprimés** : ignorés.

![Traitement des événements Stream Analytics](media/stream-analytics-event-handling/stream-analytics-event-handling.png)

## <a name="reduce-the-number-of-out-of-order-events"></a>Réduire le nombre d’événements non ordonnés

Comme Stream Analytics applique une transformation temporelle lorsqu’il traite les événements entrants (par exemple, pour les agrégats fenêtrés ou les jointures temporelles), Stream Analytics trie les événements entrants par ordre d’horodatage.

Lorsque le mot clé « timestamp » n’est **pas** utilisé, l’heure de mise en file d’attente d’événement Azure Event Hubs est utilisée par défaut. Event Hubs garantit l’unitonicité de l’horodatage sur chaque partition du Event Hub. Il garantit également que les événements de toutes les partitions sont fusionnés dans l’ordre d’horodatage. Ces garanties Event Hubs permettent d’éviter les événement en désordre.

Parfois, il est important d’utiliser l’horodatage de l’expéditeur. Dans ce cas, l’horodatage de la charge utile de l’événement est choisi à l’aide de « timestamp by ». Dans ces scénarios, une ou plusieurs sources du désordre des événements peuvent être introduites :

* Les producteurs d’événements ont des décalages d’horloge. Cela est courant lorsque les producteurs proviennent de différents ordinateurs et qu’ils ont donc des horloges différentes.
* Il existe un délai réseau à partir de la source des événements pour le Event Hub de destination.
* Les décalages d’horloge existent entre les partitions du Event Hub. Stream Analytics trie tout d’abord les événements de toutes les partitions du Event Hub par heure de mise en file d’attente des événements. Ensuite, il examine dans le flux de données les événements qui sont dans un ordre incorrect.

Sous l’onglet Configuration, les valeurs par défaut suivantes s’affichent :

![Traitement des événements désordonnés dans Stream Analytics](media/stream-analytics-event-handling/stream-analytics-out-of-order-handling.png)

Si vous utilisez 0 seconde comme tolérance de désordre, cela veut dire que tous les événements sont dans l’ordre tout le temps. Il est peu probable que cela soit le cas si vous tenez compte des trois sources pouvant entraîner des événements dans le désordre. 

Pour autoriser Stream Analytics à corriger des événements en désordre, vous pouvez spécifier une fenêtre de tolérance non nulle. Stream Analytics met les événements en mémoire tampon dans cette fenêtre, puis les réorganise à l’aide de l’horodatage que vous avez choisi. Il applique ensuite la transformation temporelle. Vous pouvez démarrer avec une fenêtre de 3 secondes et régler la valeur pour réduire le nombre d’événements dont l’heure est ajustée. 

Un effet secondaire de la mise en mémoire tampon est que la sortie est **retardée du même intervalle de temps**. Vous pouvez régler la valeur afin de réduire le nombre d’événements non ordonnés et limiter la latence du travail.

## <a name="get-help"></a>Obtenir de l'aide
Pour une assistance supplémentaire, essayez notre [forum Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Étapes suivantes
* [Présentation de Stream Analytics](stream-analytics-introduction.md)
* [Prise en main de Stream Analytics](stream-analytics-get-started.md)
* [Mise à l’échelle des travaux Stream Analytics](stream-analytics-scale-jobs.md)
* [Informations de référence sur le langage de requête Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Références sur l’API REST de gestion de Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)
