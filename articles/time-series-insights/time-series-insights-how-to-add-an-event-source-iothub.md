---
title: "Comment ajouter une source d’événement IoT Hub à votre environnement Azure Time Series Insights | Microsoft Docs"
description: "Ce tutoriel explique comment ajouter une source d’événement qui est connectée à un IoT Hub à votre environnement Time Series Insights."
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
ms.openlocfilehash: 72037677fac528ff8174d25b474ca7e70826a7b0
ms.contentlocale: fr-fr
ms.lasthandoff: 05/01/2017

---
# <a name="how-to-add-an-iot-hub-event-source"></a>Comment ajouter une source d’événements IoT Hub

Ce tutoriel explique comment utiliser le portail Azure pour ajouter une source d’événements qui lit à partir d’un IoT Hub dans votre environnement Time Series Insights.

## <a name="prerequisites"></a>Composants requis

Vous avez créé un IoT Hub et vous y avez écrit des événements. Pour plus d’informations sur les IoT Hubs, consultez <https://azure.microsoft.com/services/iot-hub/>

> [Groupes de consommateurs] Chaque source d’événement Time Series Insights doit avoir son propre groupe de consommateurs dédié qui n’est pas partagé avec les autres consommateurs. Si plusieurs lecteurs consomment des événements du même groupe de consommateurs, tous les lecteurs sont susceptibles d’obtenir des erreurs. Pour plus d’informations, reportez-vous au [Guide du développeur IoT Hub](../iot-hub/iot-hub-devguide.md).

## <a name="choose-an-import-option"></a>Choisir une option d’importation

Les paramètres de la source d’événement peuvent être entrés manuellement ou un IoT Hub peut être sélectionné à partir des IoT Hubs qui sont à votre disposition.
Dans le sélecteur **Option d’importation**, choisissez l’une des options suivantes :

* Fournir des paramètres IoT Hub manuellement
* Utiliser l’IoT Hub à partir des abonnements disponibles

### <a name="select-an-available-iot-hub"></a>Sélectionner un IoT Hub disponible

Le tableau suivant décrit chaque option sous l’onglet Nouvelle source d’événements avec sa description lors de la sélection d’un IoT Hub disponible comme source d’événements :

| Nom de la propriété | Description |
| --- | --- |
| Nom de la source d’événement | Nom de votre source d’événements. Ce nom doit être unique au sein de votre environnement Time Series Insights.
| Source | Choisissez **IoT Hub** pour créer une source d’événements IoT Hub.
| ID d’abonnement | Sélectionnez l’abonnement dans lequel cet IoT Hub a été créé.
| Nom de l’IoT Hub | Sélectionnez le nom de l’IoT Hub.
| Nom de la stratégie IoT Hub | Sélectionnez la stratégie d’accès partagé, qui se trouve sous l’onglet Paramètres IoT Hub. Chaque stratégie d’accès partagé a un nom, les autorisations que vous définissez ainsi que des clés d’accès. La stratégie d’accès partagé pour votre source d’événements *doit* avoir des autorisations de **connexion au service**.
| Groupe de consommateurs IoT Hub | Groupe de consommateurs qui lit les événements d’IoT Hub. Il est vivement recommandé d’utiliser un groupe de consommateurs dédié pour votre source d’événements.

### <a name="provide-iot-hub-settings-manually"></a>Fournir des paramètres IoT Hub manuellement

Le tableau suivant décrit chaque propriété dans l’onglet Nouvelle source d’événements avec sa description lors de la saisie manuelle de paramètres :

| Nom de la propriété | Description |
| --- | --- |
| Nom de la source d’événement | Nom de votre source d’événements. Ce nom doit être unique au sein de votre environnement Time Series Insights.
| Source | Choisissez **IoT Hub** pour créer une source d’événements IoT Hub.
| ID d’abonnement | Abonnement dans lequel cet IoT hub a été créé.
| Groupe de ressources | Abonnement dans lequel cet IoT hub a été créé.
| Nom de l’IoT Hub | Nom de votre IoT Hub Lorsque vous avez créé votre IoT Hub, vous lui avez également donné un nom spécifique.
| Nom de la stratégie IoT Hub | Stratégie d’accès partagé, qui peut être créée dans l’onglet Paramètres IoT Hub. Chaque stratégie d’accès partagé a un nom, les autorisations que vous définissez ainsi que des clés d’accès. La stratégie d’accès partagé pour votre source d’événements *doit* avoir des autorisations de **connexion au service**.
| Clé de stratégie IoT Hub | Clé d’accès partagé utilisée pour authentifier l’accès à l’espace de noms Service Bus Tapez la clé primaire ou secondaire ici.
| Groupe de consommateurs IoT Hub | Groupe de consommateurs qui lit les événements d’IoT Hub. Il est vivement recommandé d’utiliser un groupe de consommateurs dédié pour votre source d’événements.

## <a name="next-steps"></a>Étapes suivantes

1. Ajouter une stratégie d’accès aux données dans votre environnement [Définir les stratégies d’accès aux données](time-series-insights-data-access.md)
1. Accéder à votre environnement sur le [Portail Time Series Insights](https://insights.timeseries.azure.com)
