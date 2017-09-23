---
title: "Ajouter le connecteur Yammer à vos applications logiques Azure | Microsoft Docs"
description: "Vue d’ensemble du connecteur Yammer avec les paramètres d’API REST"
services: logic-apps
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: b5ae0827-fbb3-45ec-8f45-ad1cc2e7eccc
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/18/2016
ms.author: mandia; ladocs
ms.translationtype: Human Translation
ms.sourcegitcommit: c785ad8dbfa427d69501f5f142ef40a2d3530f9e
ms.openlocfilehash: c7a213343b4fb2b5a89a5052a459061b404a431c
ms.contentlocale: fr-fr
ms.lasthandoff: 05/26/2017

---
# <a name="get-started-with-the-yammer-connector"></a>Prise en main du connecteur Yammer
Connectez-vous à Yammer pour accéder aux conversations dans votre réseau d’entreprise. Avec Yammer, vous pouvez effectuer les opérations suivantes :

* Créer votre flux d’activité en fonction des données que vous obtenez de Yammer. 
* Utiliser des déclencheurs quand un nouveau message arrive dans un groupe ou un flux que vous suivez.
* Utiliser des actions pour publier un message, obtenir tous les messages et bien plus encore. Ces actions obtiennent une réponse, puis mettent la sortie à la disposition d’autres actions. Par exemple, quand un nouveau message apparaît, vous pouvez envoyer un message électronique à l’aide d’Office 365.

Commencez par créer une application logique. Pour cela, consultez [Créer une application logique](../logic-apps/logic-apps-create-a-logic-app.md).

## <a name="create-a-connection-to-yammer"></a>Créer une connexion à Yammer
Pour utiliser le connecteur Yammer, vous devez créer une **connexion**, puis fournir les détails de ces propriétés : 

| Propriété | Requis | Description |
| --- | --- | --- |
| Jeton |Oui |Indiquez les informations d’identification Yammer. |

> [!INCLUDE [Steps to create a connection to Yammer](../../includes/connectors-create-api-yammer.md)]
> 

## <a name="connector-specific-details"></a>Détails spécifiques aux connecteurs

Consultez tous les déclencheurs et les actions définies dans le swagger, ainsi que les éventuelles limites dans les [détails des connecteurs](/connectors/yammer/).

## <a name="more-connectors"></a>Autres connecteurs
Revenir à la [liste des API](apis-list.md).
