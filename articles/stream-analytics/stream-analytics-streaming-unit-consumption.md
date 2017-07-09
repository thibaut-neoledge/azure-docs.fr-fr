---
title: "Azure Stream Analytics : Optimiser votre travail pour utiliser efficacement les unités de streaming | Microsoft Docs"
description: "Meilleures pratiques en matière de requêtes pour la mise à l’échelle et les performances dans Azure Stream Analytics."
keywords: "unité de streaming, performance des requêtes"
services: stream-analytics
documentationcenter: 
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
ms.sourcegitcommit: 6dbb88577733d5ec0dc17acf7243b2ba7b829b38
ms.openlocfilehash: 341d4f057e0a4611bbff498d494ca54d0b1fd9e7
ms.contentlocale: fr-fr
ms.lasthandoff: 07/04/2017


---

# <a name="optimize-your-job-to-use-streaming-units-efficiently"></a>Optimiser votre travail pour utiliser efficacement les unités de streaming

Azure Stream Analytics agrège la pondération des performances d’exécution d’un travail dans des unités de streaming. Les unités de streaming représentent les ressources informatiques consommées pour exécuter un travail. Ces unités permettent de décrire la capacité relative de traitement des événements basée sur une mesure mixte du processeur, de la mémoire et des taux de lecture et d’écriture. Cette capacité vous permet de vous concentrer sur la logique de requête. Vous ne devez plus tenir compte des performances des niveaux de stockage, ni allouer de mémoire pour votre travail manuellement et calculer approximativement le nombre de processeurs nécessaires pour exécuter votre travail en temps voulu.

## <a name="how-many-sus-are-required-for-a-job"></a>Combien d’unités de streaming sont requises pour un travail ?

Le choix du nombre d’unités de streaming requises pour un travail particulier dépend de la configuration de la partition pour les entrées et de la requête définie pour le travail. Le panneau **Mise à l’échelle** vous permet de définir le nombre adapté d’unités de streaming. Il est recommandé d’allouer plus d’unités de streaming que nécessaire. Le moteur de traitement Stream Analytics est configuré pour une latence et un débit supérieurs, en allouant une capacité de mémoire supplémentaire.

En règle générale, la meilleure pratique consiste à démarrer avec 6 unités de streaming pour les requêtes qui n’utilisent pas *PARTITION BY*. Déterminez ensuite la configuration idéale en utilisant une méthode d’essai et d’erreur où vous modifiez le nombre d’unités de streaming, une fois que vous avez transmis le volume représentatif de données et examiné les métriques relatives à l’utilisation de l’unité de streaming.

Azure Stream Analytics conserve les événements dans une fenêtre dédiée à la mémoire tampon de réorganisation avant d’amorcer tout traitement. Les événements sont triés de manière chronologique dans la fenêtre de réorganisation et les opérations suivantes sont exécutées selon cette organisation. La réorganisation des événements par heure octroie à l’opérateur une visibilité sur l’ensemble des événements de la chronologie stipulée. Cela lui permet de fait d’exécuter le traitement requis et de produire une sortie. Avec ce mécanisme, le traitement est reporté de la durée stipulée dans la fenêtre de renouvellement de commande. L’encombrement de mémoire du travail (qui affecte la consommation des unités de streaming) est fonction de la taille de cette fenêtre de réorganisation et du nombre d’événements qu’elle contient.

> [!NOTE]
> Lorsque le nombre de lecteurs change lors des mises à niveau du travail, des avertissements temporaires sont journalisés dans des journaux d’audit. Les travaux Stream Analytics récupèrent automatiquement de ces problèmes temporaires.

## <a name="common-high-memory-causes-for-high-su-usage-for-running-jobs"></a>Causes courantes d’une mémoire élevée pour une utilisation élevée des unités de streaming lors de l’exécution de travaux

### <a name="high-cardinality-for-group-by"></a>Cardinalité élevée pour GROUP BY

La cardinalité des événements à venir détermine la consommation de mémoire pour la tâche.

Par exemple, dans `SELECT count(*) from input group by clustered, tumblingwindow (minutes, 5)`, le nombre associé aux **clusters** est la cardinalité de la requête.

Pour atténuer les problèmes provoqués par une cardinalité élevée, augmentez la taille de la requête en augmentant les partitions à l’aide de **PARTITION BY**.

```
Select count(*) from input
Partition By clusterid
GROUP BY clustered tumblingwindow (minutes, 5)
```

Le nombre de *clusters* est ici la cardinalité de GROUP BY.

Une fois que la requête est partitionnée, elle est répartie sur plusieurs nœuds. Par conséquent, le nombre d’événements arrivant dans chaque nœud est réduit, ce qui réduit d’autant la taille de la mémoire tampon de réorganisation. Vous devez également partitionner les Event Hubs par partitionid.

### <a name="high-unmatched-event-count-for-join"></a>Nombre élevée d’événements sans correspondance pour JOIN

Le nombre d’événements sans correspondance de l’objet JOIN affecte la consommation de mémoire pour la requête. Prenons l’exemple d’une requête qui vise à identifier l’exposition publicitaire qui génère des clics :

```
SELECT id from clicks INNER JOIN,
impressions on impressions.id = clicks.id AND DATEDIFF(hour, impressions, clicks) between 0 AND 10
```

Dans ce scénario, il se peut qu’il y ait de nombreuses annonces affichées et que peu de clics soient générés. Avec un tel résultat, le travail doit conserver tous les événements dans la fenêtre de temps. La consommation de mémoire est proportionnelle à la taille de la fenêtre et au taux d’événements. 

Pour atténuer ce problème, augmentez la taille de la requête en augmentant les partitions à l’aide de l’objet PARTITION BY. 

Une fois que la requête est partitionnée, elle est répartie sur plusieurs nœuds de traitement. Par conséquent, le nombre d’événements arrivant dans chaque nœud est réduit, ce qui réduit d’autant la taille de la mémoire tampon de réorganisation.

### <a name="large-number-of-out-of-order-events"></a>Nombre élevé d’événements en désordre 

Une grande fenêtre temporelle présentant un nombre élevé d’événements en désordre provoque l’augmentation de la taille de la mémoire tampon de réorganisation. Pour atténuer ce problème, augmentez la taille de la requête en augmentant les partitions à l’aide de l’objet PARTITION BY. Une fois que la requête est partitionnée, elle est répartie sur plusieurs nœuds. Par conséquent, le nombre d’événements arrivant dans chaque nœud est réduit, ce qui réduit d’autant la taille de la mémoire tampon de réorganisation. 


## <a name="get-help"></a>Obtenir de l'aide
Pour obtenir une assistance, consultez le [forum Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Étapes suivantes
* [Présentation d’Azure Stream Analytics](stream-analytics-introduction.md)
* [Prise en main d’Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Mise à l'échelle des travaux Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Références sur le langage des requêtes Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Références sur l’API REST de gestion d’Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

