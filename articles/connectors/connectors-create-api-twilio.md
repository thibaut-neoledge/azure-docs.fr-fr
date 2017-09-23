---
title: "Ajouter le connecteur Twilio à vos applications logiques Azure | Microsoft Docs"
description: "Vue d’ensemble du connecteur Twilio avec les paramètres d’API REST"
services: logic-apps
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: 43116187-4a2f-42e5-9852-a0d62f08c5fc
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 09/19/2016
ms.author: mandia; ladocs
ms.translationtype: Human Translation
ms.sourcegitcommit: c785ad8dbfa427d69501f5f142ef40a2d3530f9e
ms.openlocfilehash: a790ac51b0fea7e3fa379d20e0e094e7ce0d7696
ms.contentlocale: fr-fr
ms.lasthandoff: 05/26/2017

---
# <a name="get-started-with-the-twilio-connector"></a>Prise en main du connecteur Twilio
Connectez-vous à Twilio pour envoyer et recevoir des SMS, des MMS, et des adresses IP. Avec Twilio, vous pouvez :

* Créer votre flux d'activité en fonction des données que vous obtenez de Twilio. 
* Utiliser des actions pour obtenir un message, répertorier les messages, et plus encore. Ces actions obtiennent une réponse, puis mettent la sortie à la disposition d’autres actions. Par exemple, quand vous recevez un nouveau message Twilio, vous pouvez prendre ce message et l’utiliser comme flux de travail Service Bus. 

Commencez par créer une application logique. Pour cela, consultez [Créer une application logique](../logic-apps/logic-apps-create-a-logic-app.md).

## <a name="create-a-connection-to-twilio"></a>Créer une connexion à Twilio
Quand vous ajoutez ce connecteur à vos applications logiques, entrez les valeurs Twilio suivantes :

| Propriété | Requis | Description |
| --- | --- | --- |
| ID de compte |Oui |Entrez votre ID de compte Twilio |
| Jeton d'accès |Oui |Entrez votre jeton d’accès Twilio |

> [!INCLUDE [Steps to create a connection to Twilio](../../includes/connectors-create-api-twilio.md)]
> 
> 

Si vous n’avez pas un jeton d’accès Twilio, consultez [Identité de l’utilisateur et jetons d’accès](https://www.twilio.com/docs/api/chat/guides/identity).

## <a name="connector-specific-details"></a>Détails spécifiques aux connecteurs

Consultez tous les déclencheurs et les actions définies dans le swagger, ainsi que les éventuelles limites dans les [détails des connecteurs](/connectors/twilio/).

## <a name="more-connectors"></a>Autres connecteurs
Revenir à la [liste des API](apis-list.md).
