---
title: "Stratégies d’accès aux données dans Azure Time Series Insights | Microsoft Docs"
description: "Dans ce didacticiel, vous apprenez à gérer les stratégies d’accès aux données dans Time Series Insights"
keywords: 
services: time-series-insights
documentationcenter: 
author: op-ravi
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: tsi
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 05/01/2017
ms.author: omravi
ms.translationtype: Human Translation
ms.sourcegitcommit: 300958a69fc854cb8db02120a383a4cbbfcacd7b
ms.openlocfilehash: e975c6d8f217bc73948c0c046204b16b1a742bc7
ms.contentlocale: fr-fr
ms.lasthandoff: 05/20/2017

---

# <a name="grant-data-access-to-a-time-series-insights-environment-using-azure-portal"></a>Accorder l’accès aux données d’un environnement Time Series Insights à l’aide du portail Azure

Les environnements Time Series Insights comprennent deux types indépendants des stratégies d’accès :

* Stratégies d’accès de gestion
* Stratégies d’accès aux données

Ces deux stratégies accordent aux principaux Azure Active Directory (utilisateurs et applications) diverses autorisations sur un environnement spécifique. Les principaux (utilisateurs et applications) doivent appartenir à l’Active Directory (ou « locataire Azure ») associé à l’abonnement contenant l’environnement.

Les stratégies d’accès de gestion accordent des autorisations liées à la configuration de l’environnement, telles que la
*   création et la suppression de l’environnement, des sources d’événements, des ensembles de données de référence et la
*   gestion des stratégies d’accès aux données.

Les stratégies d’accès aux données accordent des autorisations pour générer des requêtes de données, manipuler des données de référence dans l’environnement, et des requêtes partagées enregistrées et des perspectives associées à l’environnement.

Les deux types de stratégies permettent de distinguer clairement l’accès à la gestion de l’environnement et l’accès aux données contenues dans l’environnement. Par exemple, il est possible de configurer un environnement de sorte que le propriétaire/créateur de l’environnement soit supprimé de l’accès aux données. Et les utilisateurs et services qui sont autorisés à lire des données de l’environnement peuvent ne pas avoir accès à la configuration de l’environnement.

## <a name="grant-data-access"></a>Accorder l’accès aux données
Les étapes suivantes montrent comment accorder l’accès aux données pour un utilisateur principal :

1.  Connectez-vous au [portail Azure](https://portal.azure.com).
2.  Cliquez sur « Toutes les ressources » dans le menu de gauche du portail Azure.
3.  Sélectionnez votre environnement Time Series Insights.

  ![Gérer la source Time Series Insights - Environnement](media/data-access/getstarted-grant-data-access1.png)

4.  Sélectionnez « Accès au plan de données », puis cliquez sur « Ajouter »

  ![Gérer la source Time Series Insights - Ajouter](media/data-access/getstarted-grant-data-access2.png)

5.  Cliquez sur « Sélectionner un utilisateur ».
6.  Recherchez et sélectionnez l’utilisateur par adresse électronique.
7.  Cliquez sur « Sélectionner » dans le panneau « Sélectionner un utilisateur ».

  ![Gérer la source Time Series Insights - Sélectionner un utilisateur](media/data-access/getstarted-grant-data-access3.png)

8.  Cliquez sur « Sélectionner un rôle ».
9.  Sélectionnez « Contributeur » si vous souhaitez autoriser l’utilisateur à modifier des données de référence et à partager des requêtes enregistrées et des perspectives avec d’autres utilisateurs de l’environnement. Sinon, sélectionnez « Lecteur » pour autoriser l’utilisateur à rechercher des données dans l’environnement et à enregistrer des requêtes personnelles (non partagés) dans l’environnement.
10. Cliquez sur « OK » dans le panneau « Sélectionner un rôle ».

  ![Gérer la source Time Series Insights - Sélectionner un rôle](media/data-access/getstarted-grant-data-access4.png)

11. Cliquez sur « OK » dans le panneau « Sélectionner un rôle utilisateur ».
12. Ce qui suit doit s’afficher :

  ![Gérer la source Time Series Insights - Résultats](media/data-access/getstarted-grant-data-access5.png)

## <a name="next-steps"></a>Étapes suivantes

* [Créer une source d’événement](time-series-insights-add-event-source.md)
* [Envoyer des événements](time-series-insights-send-events.md) à la source d’événement
* Afficher votre environnement dans le [Portail Time Series Insights](https://insights.timeseries.azure.com)

