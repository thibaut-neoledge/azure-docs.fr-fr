---
title: "Diagnostiquer et résoudre les problèmes | Microsoft Docs"
description: "Ce didacticiel explique comment diagnostiquer et résoudre les problèmes dans votre environnement Time Series Insights."
keywords: 
services: time-series-insights
documentationcenter: 
author: venkatgct
manager: almineev
editor: cgronlun
ms.assetid: 
ms.service: time-series-insights
ms.devlang: na
ms.topic: how-to-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/24/2017
ms.author: venkatja
ms.translationtype: Human Translation
ms.sourcegitcommit: 7f8b63c22a3f5a6916264acd22a80649ac7cd12f
ms.openlocfilehash: 46d58e94f3fede3f7f19de8d97e23ac6910bc12a
ms.contentlocale: fr-fr
ms.lasthandoff: 05/01/2017

---
# <a name="diagnose-and-solve-problems"></a>Diagnostiquer et résoudre les problèmes

## <a name="i-do-not-see-my-data"></a>Mes données ne s’affichent pas
Voici quelques raisons qui pourraient expliquer pourquoi vous ne voyez pas les données de votre environnement dans le [portail Time Series Insights](https://insights.timeseries.azure.com).

### <a name="does-your-event-source-have-data-in-json-format"></a>Votre source d’événement contient-elle des données au format JSON ?
Pour le moment, Azure Time Series Insights ne prend en charge que les données JSON. Pour obtenir des exemples de données JSON, consultez la section *Structures JSON prises en charge* [ici](time-series-insights-send-events.md#supported-json-shapes).

### <a name="when-registering-your-event-source-did-you-provide-the-key-with-required-permissions"></a>Au moment d’enregistrer votre source d’événement, avez-vous fourni la clé avec les autorisations requises ?
1. Pour IoTHub, vous devez fournir la clé avec l’autorisation *Connexion de service*.

   ![Autorisation de connexion de service IotHub](media/diagnose-and-solve-problems/iothub-serviceconnect-permissions.png)

   Comme indiqué dans l’illustration précédente, les stratégies « iothubowner » et « service » sont acceptées, car elles disposent toutes deux de l’autorisation « Connexion de service ».
2. Pour EventHub, vous devez fournir la clé avec l’autorisation *Écouter*.

   ![Autorisation d’écoute EventHub](media/diagnose-and-solve-problems/eventhub-listen-permissions.png)

   Comme indiqué dans l’illustration précédente, les stratégies « read » et « manage » sont acceptées, car elles disposent toutes deux de l’autorisation de « lecture ».

### <a name="are-you-sure-that-the-consumer-group-provided-is-exclusive-to-time-series-insights"></a>Êtes-vous sûr que le groupe de consommateurs spécifié a été créé exclusivement pour Time Series Insights ?
Lors de l’enregistrement dans IoTHub ou EventHub, nous vous demandons de spécifier le groupe de consommateurs qui sera utilisé lors de la lecture de vos données. Ce groupe de consommateurs ne doit pas être partagé. S’il l’est, le concentrateur d’événements sous-jacent déconnecte automatiquement l’un des lecteurs au hasard.

## <a name="i-see-my-data-but-there-is-a-lag"></a>Mes données s’affichent, mais avec un décalage
Voici quelques raisons qui pourraient expliquer pourquoi vous ne voyez que certaines des données de votre environnement dans le [portail Time Series Insights](https://insights.timeseries.azure.com).

### <a name="your-environment-might-be-getting-throttled"></a>Votre environnement pourrait être sujet à des limitations.
Les limitations sont appliquées en fonction de la capacité et du type de référence de l’environnement. Cette capacité est répartie entre les différentes sources d’événements de l’environnement. Si votre source d’événement EventHub/IoTHub envoie des données au-delà des limites définies, cela génère des limitations et un décalage.

Le diagramme suivant illustre un environnement Time Series Insights avec une référence S1 et une capacité de 3 unités. Cet environnement peut recevoir 3 millions d’événements par jour.

![Capacité actuelle et référence de l’environnement](media/diagnose-and-solve-problems/environment-sku-current-capacity.png)

Supposons que cet environnement ait reçu des messages d’un concentrateur d’événements dont le taux d’entrée est indiqué dans le diagramme suivant :

![Capacité actuelle et référence de l’environnement](media/diagnose-and-solve-problems/eventhub-ingress-rate.png)

Comme indiqué dans le diagramme, le taux d’entrée quotidien est d’environ 67 000 messages. Cela représente environ 46 messages par minute. Si chaque message du concentrateur d’événements est aplati dans un seul événement Time Series Insights, aucune limitation ne sera appliquée à cet environnement. Si chaque message du concentrateur d’événements est aplati dans 100 événements Time Series Insights, 4 600 événements devraient être reçus toutes les minutes. Un environnement avec une référence S1 et une capacité de 3 unités ne peut pas recevoir plus de 2 100 événements par minute. (1 million d’événements par jour => 700 événements par minute, 3 unités => 2 100 événements par minute). Par conséquent, les limitations qui s’appliquent provoquent un décalage. Pour en savoir plus sur la logique de mise à plat, consultez la section *Structures JSON prises en charge* [ici](time-series-insights-send-events.md#supported-json-shapes).

#### <a name="recommended-steps"></a>Étapes recommandées
Pour éviter tout décalage, augmentez la capacité de votre environnement. [Mettre à l’échelle votre environnement Time Series Insights](time-series-insights-how-to-scale-your-environment.md)

### <a name="you-might-be-pushing-historical-data-and-hence-the-slow-ingress"></a>Vous pouvez envoyer des données historiques et donc ralentir le processus d’entrée.
Si vous vous connectez à une source d’événement existante, il est probable que votre concentrateur EventHub/IoTHub comporte déjà des données. Par conséquent, l’environnement démarre l’extraction des données depuis le début de la période de rétention des messages de la source d’événement. Ce comportement est le comportement par défaut et ne peut être modifié. Des limitations peuvent s’appliquer et l’ingestion des données historiques peut prendre un certain temps.

#### <a name="recommended-steps"></a>Étapes recommandées
Pour corriger le problème de décalage, procédez comme suit :
1. Définissez la capacité de référence sur la valeur maximale autorisée (10 unités dans ce cas). Une fois que la capacité a été augmentée, le processus d’entrée rattrape le retard beaucoup plus rapidement. Vous pouvez suivre sa progression rapide depuis le graphique de disponibilité dans le [portail Time Series Insights](https://insights.timeseries.azure.com). L’augmentation de capacité occasionne des frais supplémentaires.
2. Une fois le retard rattrapé, rétablissez la capacité de référence sur votre taux d’entrée normal.

## <a name="my-event-source-timestamp-property-name-setting-does-not-work"></a>Le paramètre *Nom de la propriété timestamp* de ma source d’événement ne fonctionne pas
Vérifiez que le nom et la valeur répondent aux critères suivants :
1. Le nom de la propriété timestamp est __sensible à la casse__.
2. La valeur de la propriété timestamp provenant de votre source d’événement, telle qu’une chaîne JSON, doit être au format __aaaa-MM-jjTHH:mm:ss.FFFFFFFK__. Exemple de chaîne : 2008-04-12T12:53Z.
