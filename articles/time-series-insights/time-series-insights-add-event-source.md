---
title: "Ajouter une source d’événement à votre environnement Azure Time Series Insights | Microsoft Docs"
description: "Dans ce didacticiel, vous connectez une source d’événement à votre environnement Time Series Insights"
keywords: 
services: time-series-insights
documentationcenter: 
author: op-ravi
manager: santoshb
editor: cgronlun
ms.assetid: 
ms.service: time-series-insights
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/21/2017
ms.author: omravi
ms.translationtype: Human Translation
ms.sourcegitcommit: 6efa2cca46c2d8e4c00150ff964f8af02397ef99
ms.openlocfilehash: ffa2eaf3680e68ac14aabf49b6308caeb173fd43
ms.contentlocale: fr-fr
ms.lasthandoff: 07/01/2017

---

<a id="create-an-event-source-for-your-time-series-insights-environment-using-the-ibiza-portal" class="xliff"></a>

# Créer une source d’événement pour votre environnement Time Series Insights à l’aide du portail Ibiza

La source d’événement Time Series Insights est dérivée d’un service Broker pour les événements tel que les concentrateurs d’événements Azure. Time Series Insights se connecte directement aux sources d’événement, en recevant le flux de données sans demander aux utilisateurs d’écrire une seule ligne de code. Actuellement, Time Series Insights prend en charge les concentrateurs d’événements Azure et les IoT Hubs. À l’avenir, plusieurs sources d’événements seront ajoutées.

<a id="steps-to-add-an-event-source-to-your-environment" class="xliff"></a>

## Étapes pour ajouter une source d’événement à votre environnement

1.  Connectez-vous au [portail Ibiza](https://portal.azure.com).
2.  Cliquez sur « Toutes les ressources » dans le menu de gauche du portail Ibiza.
3.  Sélectionnez votre environnement Time Series Insights.

  ![Créer la source d’événement Time Series Insights](media/add-event-source/getstarted-create-event-source-1.png)

4.  Sélectionnez « Sources d’événement », puis cliquez sur « + Ajouter ».

  ![Créer la source d’événement Time Series Insights - Détails](media/add-event-source/getstarted-create-event-source-2.png)

5.  Spécifiez le nom de la source d’événement. Ce nom est associé à tous les événements provenant de cette source d’événement et est disponible au moment de la requête.
6.  Sélectionnez un concentrateur d’événements à partir de la liste des ressources du concentrateur d’événements de l’abonnement actuel. Sinon, choisissez l’option d’importation « Fournir des paramètres Event Hub manuellement » pour spécifier un concentrateur d’événements d’un autre abonnement. Les événements doivent être publiés au format JSON.
7.  Sélectionnez la stratégie disposant de l’autorisation de lecture sur le concentrateur d’événements.
8.  Spécifiez un groupe de consommateurs du concentrateur d’événements.

  > [!IMPORTANT]
  > Assurez-vous que ce groupe de consommateurs n’est pas utilisé par un autre service (par exemple, la tâche Stream Analytics ou un autre environnement Time Series Insights). Si le groupe de consommateurs est utilisé par d’autres services, l’opération de lecture est affectée pour cet environnement et les autres services. Si vous utilisez le groupe de consommateurs « $Default », ceci peut entraîner une réutilisation potentielle par d’autres lecteurs.

9.  Cliquez sur « Créer ».

Après la création de la source d’événement, Time Series Insights démarre automatiquement la diffusion de données dans votre environnement.

<a id="next-steps" class="xliff"></a>

## Étapes suivantes

* [Envoyer des événements](time-series-insights-send-events.md) à la source d’événement
* Afficher votre environnement dans le [Portail Time Series Insights](https://insights.timeseries.azure.com)

