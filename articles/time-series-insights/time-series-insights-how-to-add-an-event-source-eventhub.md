---
title: "Comment ajouter une source d’événement Event Hubs à votre environnement Azure Time Series Insights | Microsoft Docs"
description: "Ce tutoriel explique comment ajouter une source d’événement qui est connectée à un Event Hub à votre environnement Time Series Insights."
keywords: 
services: time-series-insights
documentationcenter: 
author: sandshadow
manager: almineev
editor: cgronlun
ms.assetid: 
ms.service: tsi
ms.devlang: na
ms.topic: how-to-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/19/2017
ms.author: edett
ms.translationtype: HT
ms.sourcegitcommit: 07e5e15f4f4c4281a93c8c3267c0225b1d79af45
ms.openlocfilehash: f6a993b3858cfb94dd9795f5e55f15fa6ec7dcb2
ms.contentlocale: fr-fr
ms.lasthandoff: 08/31/2017

---
# <a name="how-to-add-an-event-hub-event-source"></a>Comment ajouter une source d’événements Event Hub

Ce tutoriel explique comment utiliser le portail Azure pour ajouter une source d’événements qui lit à partir d’un Event Hub dans votre environnement Time Series Insights.

## <a name="prerequisites"></a>Composants requis

Vous avez créé un Event Hub et vous y avez écrit des événements. Pour plus d’informations sur les Event Hubs, consultez <https://azure.microsoft.com/services/event-hubs/>

> [Groupes de consommateurs] Chaque source d’événement Time Series Insights doit avoir son propre groupe de consommateurs dédié qui n’est pas partagé avec les autres consommateurs. Si plusieurs lecteurs consomment des événements du même groupe de consommateurs, tous les lecteurs sont susceptibles d’obtenir des erreurs. Notez qu’il existe également une limite de 20 groupes de consommateurs par hub d’événements. Pour plus d’informations, consultez [Guide de programmation Event Hubs](../event-hubs/event-hubs-programming-guide.md).

## <a name="choose-an-import-option"></a>Choisir une option d’importation

Les paramètres de la source d’événements peuvent être entrés manuellement ou un Event Hub peut être sélectionné à partir des Event Hubs qui sont à votre disposition.
Dans le sélecteur **Option d’importation**, choisissez l’une des options suivantes :

* Fournir des paramètres Event Hub manuellement
* Utiliser Event Hub événements à partir d’abonnements disponibles

### <a name="select-an-available-event-hub"></a>Sélectionner un Event Hub disponible

Le tableau suivant décrit chaque option sous l’onglet Nouvelle source d’événements avec sa description lors de la sélection d’un Event Hub disponible comme source d’événements :

| Nom de la propriété | Description |
| --- | --- |
| Nom de la source d’événement | Nom de votre source d’événements. Ce nom doit être unique au sein de votre environnement Time Series Insights.
| Source | Choisissez **Event Hub** pour créer une source d’événements Event Hub.
| ID d’abonnement | Sélectionnez l’abonnement dans lequel ce Event Hub a été créé.
| Espace de noms Service Bus | Sélectionnez l’espace de noms Service Bus qui contient le Event Hub.
| Nom du hub d’événements | Sélectionnez le nom du Event Hub.
| Nom de la stratégie du hub d’événements | Sélectionnez la stratégie d’accès partagé, qui peut être créée dans l’onglet Configuration du hub d’événements. Chaque stratégie d’accès partagé a un nom, les autorisations que vous définissez ainsi que des clés d’accès. La stratégie d’accès partagé pour votre source d’événements *doit* avoir des autorisations de **lecture**.
| Groupe de consommateurs du Event Hub | Groupe de consommateurs qui lit les événements du Event Hub. Il est vivement recommandé d’utiliser un groupe de consommateurs dédié pour votre source d’événements.

### <a name="provide-event-hub-settings-manually"></a>Fournir des paramètres Event Hub manuellement

Le tableau suivant décrit chaque propriété dans l’onglet Nouvelle source d’événements avec sa description lors de la saisie manuelle de paramètres :

| Nom de la propriété | Description |
| --- | --- |
| Nom de la source d’événement | Nom de votre source d’événements. Ce nom doit être unique au sein de votre environnement Time Series Insights.
| Source | Choisissez **Event Hub** pour créer une source d’événements Event Hub.
| ID d’abonnement | Abonnement dans lequel ce Event Hub a été créé.
| Groupe de ressources | Abonnement dans lequel ce Event Hub a été créé.
| Espace de noms Service Bus | Un espace de noms Service Bus est un conteneur pour un jeu d’entités de messagerie. En créant un hub d’événements, vous avez également créé un espace de noms Service Bus.
| Nom du hub d’événements | Nom de votre Event Hub Lorsque vous avez créé votre Event Hub, vous lui avez également donné un nom spécifique.
| Nom de la stratégie du hub d’événements | Stratégie d’accès partagé, qui peut être créée dans l’onglet Configuration du hub d’événements. Chaque stratégie d’accès partagé a un nom, les autorisations que vous définissez ainsi que des clés d’accès. La stratégie d’accès partagé pour votre source d’événements *doit* avoir des autorisations de **lecture**.
| Clé de la stratégie du Event Hub | Clé d’accès partagé utilisée pour authentifier l’accès à l’espace de noms Service Bus Tapez la clé primaire ou secondaire ici.
| Groupe de consommateurs du Event Hub | Groupe de consommateurs qui lit les événements du Event Hub. Il est vivement recommandé d’utiliser un groupe de consommateurs dédié pour votre source d’événements.

## <a name="next-steps"></a>Étapes suivantes

1. Ajouter une stratégie d’accès aux données dans votre environnement [Définir les stratégies d’accès aux données](time-series-insights-data-access.md)
1. Accéder à votre environnement sur le [Portail Time Series Insights](https://insights.timeseries.azure.com)
