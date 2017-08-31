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
ms.service: tsi
ms.devlang: na
ms.topic: how-to-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/24/2017
ms.author: venkatja
ms.translationtype: Human Translation
ms.sourcegitcommit: fc4172b27b93a49c613eb915252895e845b96892
ms.openlocfilehash: 4b8d5fdab1744b2db658917f91d6dac05db30d2f
ms.contentlocale: fr-fr
ms.lasthandoff: 05/12/2017

---
# <a name="diagnose-and-solve-problems-in-your-time-series-insights-environment"></a>Diagnostiquer et résoudre les problèmes dans votre environnement Time Series Insights

## <a name="i-dont-see-my-data"></a>Mes données ne s’affichent pas
Voici quelques raisons qui pourraient expliquer pourquoi vous ne voyez pas les données de votre environnement dans le [portail Azure Time Series Insights](https://insights.timeseries.azure.com).

### <a name="your-event-source-doesnt-have-data-in-json-format"></a>Votre source d’événement ne contient pas de données au format JSON
Pour le moment, Azure Time Series Insights ne prend en charge que les données JSON. Pour obtenir des exemples de données JSON, consultez [Structures JSON prises en charge](time-series-insights-send-events.md#supported-json-shapes).

### <a name="when-you-registered-your-event-source-you-didnt-provide-the-key-that-has-the-required-permission"></a>Lorsque vous avez inscrit votre source d’événements, vous n’avez pas fourni la clé qui a l’autorisation requise
* Pour IoTHub, vous devez fournir la clé avec l’autorisation **Connexion de service**.

   ![Autorisation de connexion de service IoT Hub](media/diagnose-and-solve-problems/iothub-serviceconnect-permissions.png)

   Comme indiqué dans l’illustration précédente, les stratégies **iothubowner** et **service** sont acceptées, car elles disposent toutes deux de l’autorisation **Connexion de service**.
* Pour Event Hub, vous devez fournir la clé avec l’autorisation **Écouter**.

   ![Autorisation d’écoute EventHub](media/diagnose-and-solve-problems/eventhub-listen-permissions.png)

   Comme indiqué dans l’illustration précédente, les stratégies **read** et **manage** sont acceptées, car elles disposent toutes deux de l’autorisation **Listen**.

### <a name="the-provided-consumer-group-is-not-exclusive-to-time-series-insights"></a>Le groupe de consommateurs fourni n’est pas exclusif à Time Series Insights
Lors de l’enregistrement dans IoT Hub ou Event Hub, nous vous demandons de spécifier le groupe de consommateurs qui sera utilisé pour la lecture de vos données. Ce groupe de consommateurs ne doit pas être partagé. S’il l’est, le concentrateur d’événements sous-jacent déconnecte automatiquement l’un des lecteurs au hasard.

## <a name="i-see-my-data-but-theres-a-lag"></a>Mes données s’affichent, mais avec un décalage
Voici quelques raisons qui pourraient expliquer pourquoi vous ne voyez que certaines des données de votre environnement dans le [portail Time Series Insights](https://insights.timeseries.azure.com).

### <a name="your-environment-is-getting-throttled"></a>Votre environnement est sujet à des limitations
Les limitations sont appliquées en fonction de la capacité et du type de référence de l’environnement. Cette capacité est répartie entre les différentes sources d’événements de l’environnement. Si la source d’événement pour votre Event Hub/IoT Hub envoie des données au-delà des limites définies, cela génère des limitations et un décalage.

Le diagramme suivant illustre un environnement Time Series Insights ayant une référence S1 et une capacité de 3 unités. Cet environnement peut recevoir 3 millions d’événements par jour.

![Capacité actuelle et référence de l’environnement](media/diagnose-and-solve-problems/environment-sku-current-capacity.png)

Supposons que cet environnement ait reçu des messages d’un concentrateur d’événements dont le taux d’entrée est indiqué dans le diagramme suivant :

![Exemple de taux d’entrée pour un concentrateur d’événements](media/diagnose-and-solve-problems/eventhub-ingress-rate.png)

Comme indiqué dans le diagramme, le taux d’entrée quotidien est d’environ 67 000 messages. Cela représente environ 46 messages par minute. Si chaque message du concentrateur d’événements est aplati dans un seul événement Time Series Insights, aucune limitation ne sera appliquée à cet environnement. Si chaque message du concentrateur d’événements est aplati dans 100 événements Time Series Insights, 4 600 événements devraient être reçus toutes les minutes. Un environnement de référence (SKU) S1 qui a une capacité de 3 peut uniquement prendre 2 100 événements d’entrée toutes les minutes (1 million d’événements par jour = 700 événements par minute à 3 unités = 2 100 événements par minute). Par conséquent, les limitations qui s’appliquent provoquent un décalage. 

Pour en savoir plus sur la logique de mise à plat, consultez [Structures JSON prises en charge](time-series-insights-send-events.md#supported-json-shapes).

#### <a name="recommended-steps"></a>Étapes recommandées
Pour éviter tout décalage, augmentez la capacité de votre environnement. Pour plus d’informations, consultez [Comment mettre à l’échelle votre environnement Time Series Insights](time-series-insights-how-to-scale-your-environment.md).

### <a name="youre-pushing-historical-data-and-causing-slow-ingress"></a>Vous envoyez des données d’historique et causez un ralentissement en entrée
Si vous vous connectez à une source d’événement existante, il est probable que votre concentrateur Event Hub ou IoT Hub comporte déjà des données. Par conséquent, l’environnement démarre l’extraction des données depuis le début de la période de rétention des messages de la source d’événement. 

Ce comportement est le comportement par défaut et ne peut être modifié. Vous pouvez appliquer des limitations, et l’ingestion des données historiques peut prendre un certain temps.

#### <a name="recommended-steps"></a>Étapes recommandées
Pour corriger le décalage, suivez les étapes ci-dessous :
1. Définissez la capacité de référence sur la valeur maximale autorisée (10 unités dans ce cas). Une fois que la capacité a été augmentée, le processus d’entrée rattrape le retard beaucoup plus rapidement. Vous pouvez suivre sa progression rapide depuis le graphique de disponibilité dans le [portail Time Series Insights](https://insights.timeseries.azure.com). L’augmentation de capacité occasionne des frais supplémentaires.
2. Une fois le retard rattrapé, rétablissez la capacité de référence sur votre taux d’entrée normal.

## <a name="my-event-sources-timestamp-property-name-setting-doesnt-work"></a>Le paramètre *Nom de la propriété timestamp* de ma source d’événement ne fonctionne pas
Vérifiez que le nom et la valeur répondent aux critères suivants :
* Le nom de la propriété timestamp est _sensible à la casse_.
* La valeur de la propriété timestamp provenant de votre source d’événement, telle qu’une chaîne JSON, doit être au format _aaaa-MM-jjTHH:mm:ss.FFFFFFFK_. Exemple de chaîne : 2008-04-12T12:53Z.

