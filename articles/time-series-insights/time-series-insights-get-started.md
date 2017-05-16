---
title: "Créer un environnement Azure Time Series Insights | Microsoft Docs"
description: "Dans ce didacticiel, vous allez découvrir comment créer un environnement Time Series, le connecter à une source d’événement et le préparer à analyser vos données d’événement en quelques minutes."
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
ms.openlocfilehash: eb710795916a2d7beea75a6408a0982fb4dc8750
ms.lasthandoff: 04/26/2017

---

# <a name="create-a-new-time-series-insights-environment-in-the-azure-portal"></a>Créer un nouvel environnement de Time Series Insights dans le portail Azure

Un environnement Time Series est une ressource Azure disposant d’une capacité d’entrée et de stockage. Les clients approvisionne des environnements via le portail Azure avec la capacité requise.

## <a name="steps-to-create-the-environment"></a>Étapes pour créer l’environnement

Procédez comme suit pour créer votre environnement :

1.    Connectez-vous au [portail Azure](https://portal.azure.com).
2.    Cliquez sur le signe plus (« + ») dans le coin supérieur gauche.
3.    Recherchez « Time Series Insights » dans la zone de recherche.

  ![Créer l’environnement Time Series Insights](media/get-started/getstarted-create-environment1.png)

4.    Sélectionnez « Time Series Insights », puis cliquez sur « Créer ».

  ![Créer le groupe de ressources Time Series Insights](media/get-started/getstarted-create-environment2.png)

5.    Spécifiez le nom de l’environnement. Ce nom représente l’environnement dans l’[explorateur time series](https://insights.timeseries.azure.com).
6.    Sélectionnez un abonnement. Choisissez celui qui contient votre source d’événement. Time Series Insights peut détecter automatiquement les ressources Azure IoT Hub et Concentrateur d’événements existant dans le même abonnement.
7.    Sélectionnez ou créez un groupe de ressources. Un groupe de ressources correspond à une collection de ressources Azure utilisées ensemble.
8.    Sélectionnez un emplacement d’hébergement. Pour éviter de déplacer des données entre des centres de données, choisissez l’emplacement qui contient votre source d’événement.
9.    Sélectionnez un niveau tarifaire.
10.    Sélectionnez la capacité. Vous pouvez modifier la capacité d’un environnement après sa création.
11.    Créez votre environnement. Vous pouvez également épingler votre environnement au tableau de bord pour y accéder facilement à chaque fois que vous vous connectez.

  ![Créer l’épingle Time Series Insights sur le tableau de bord](media/get-started/getstarted-create-environment3.png)

## <a name="next-steps"></a>Étapes suivantes

* [Définir des stratégies d’accès aux données](time-series-insights-data-access.md) pour accéder à votre environnement dans le [Portail Time Series Insights](https://insights.timeseries.azure.com)
* [Créer une source d’événement](time-series-insights-add-event-source.md)
* [Envoyer des événements](time-series-insights-send-events.md) à la source d’événement

