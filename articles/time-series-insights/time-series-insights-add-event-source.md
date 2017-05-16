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
translationtype: Human Translation
ms.sourcegitcommit: 1cc1ee946d8eb2214fd05701b495bbce6d471a49
ms.openlocfilehash: 9dc394c0da34a8ee2796c6114e7f2f647c5345a1
ms.lasthandoff: 04/26/2017

---

# <a name="create-an-event-source-for-your-time-series-insights-environment-using-the-azure-portal"></a>Créer une source d’événement pour votre environnement Time Series Insights à l’aide du portail Azure

La source d’événement Time Series Insights est dérivée d’un service Broker pour les événements tel que les concentrateurs d’événements Azure. Time Series Insights se connecte directement aux sources d’événement, en recevant le flux de données sans demander aux utilisateurs d’écrire une seule ligne de code. Time Series Insights prend actuellement en charge les concentrateurs d’événements Azure et Azure IoT Hubs. D’autres sources d’événement seront ajoutées à l’avenir.

## <a name="steps-to-add-an-event-source-to-your-environment"></a>Étapes pour ajouter une source d’événement à votre environnement

1.    Connectez-vous au [portail Azure](https://portal.azure.com).
2.    Cliquez sur « Toutes les ressources » dans le menu de gauche du portail Azure.
3.    Sélectionnez votre environnement Time Series Insights.

  ![Créer la source d’événement Time Series Insights](media/add-event-source/getstarted-create-eventsource1.png)

4.    Sélectionnez « Sources d’événement », puis cliquez sur « + Ajouter ».

  ![Créer la source d’événement Time Series Insights - Détails](media/add-event-source/getstarted-create-eventsource2.png)

5.    Spécifiez le nom de la source d’événement. Ce nom est associé à tous les événements provenant de cette source d’événement et est disponible au moment de la requête.
6.    Sélectionnez un concentrateur d’événements dans la liste des ressources de concentrateur d’événements de l’abonnement actuel ou choisissez l’option d’importation « Fournir les paramètres de hub d'événements manuellement » pour spécifier un concentrateur d’événements situé dans un autre abonnement. Les événements doivent être publiés au format JSON.
7.    Sélectionnez la stratégie disposant de l’autorisation de lecture sur le concentrateur d’événements.
8.    Spécifiez un groupe de consommateurs du concentrateur d’événements.

  > [!IMPORTANT]
  > Assurez-vous que ce groupe de consommateurs n’est pas utilisé par un autre service (par exemple, la tâche Stream Analytics ou un autre environnement Time Series Insights). Si le groupe de consommateurs est utilisé par d’autres services, l’opération de lecture est affectée pour cet environnement et les autres services. Si vous utilisez le groupe de consommateurs « $Default », ceci peut entraîner une réutilisation potentielle par d’autres lecteurs.

9.    Cliquez sur « Créer ».

Après la création de la source d’événement, Time Series Insights démarre automatiquement la diffusion de données dans votre environnement.

## <a name="next-steps"></a>Étapes suivantes

* [Envoyer des événements](time-series-insights-send-events.md) à la source d’événement
* Afficher votre environnement dans le [Portail Time Series Insights](https://insights.timeseries.azure.com)

