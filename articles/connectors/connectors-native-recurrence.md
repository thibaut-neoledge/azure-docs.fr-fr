---
title: "Ajout du déclencheur de périodicité dans des applications logiques | Microsoft Docs"
description: "Vue d’ensemble du déclencheur de périodicité et de son utilisation avec une application logique Azure."
services: 
documentationcenter: 
author: jeffhollan
manager: erikre
editor: 
tags: connectors
ms.assetid: 51dd4f22-7dc5-41af-a0a9-e7148378cd50
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/18/2016
ms.author: jehollan
translationtype: Human Translation
ms.sourcegitcommit: b92f954680603891ced503a1134791312b5214f0
ms.openlocfilehash: fe558958c316c8dba42163e277ae01451f712e5a


---
# <a name="get-started-with-the-recurrence-trigger"></a>Prise en main du déclencheur de périodicité
En utilisant le déclencheur de périodicité, vous pouvez créer des workflows puissants dans le cloud.

Vous pouvez par exemple afficher :

* Planifier un workflow pour l’exécution quotidienne d’une procédure SQL stockée.
* Envoyer par e-mail un résumé de tous les tweets de la semaine écoulée sur la base d’un hashtag spécifique.

Pour commencer à utiliser le déclencheur de périodicité dans une application logique, consultez [Créer une application logique](../logic-apps/logic-apps-create-a-logic-app.md).

## <a name="use-a-recurrence-trigger"></a>Utilisation d’un déclencheur de périodicité
Un déclencheur est un événement qui peut être utilisé pour lancer le flux de travail défini dans une application logique. [En savoir plus sur les déclencheurs](connectors-overview.md).

Voici un exemple de séquence de configuration d’un déclencheur de périodicité dans une application logique :

1. Ajoutez le déclencheur **Périodicité** en tant que première étape dans une application logique.
2. Renseignez les paramètres pour l’intervalle de périodicité.

L’application logique est exécutée après l’écoulement de chaque intervalle de temps.

![Déclencheur HTTP](./media/connectors-native-recurrence/using-trigger.png)

## <a name="trigger-details"></a>Détails du déclencheur
Le déclencheur de périodicité a les propriétés suivantes qui peuvent être configurées.

Il déclenche une application logique après un intervalle de temps spécifié.
A * désigne est un champ obligatoire.

| Nom complet | Nom de la propriété | Description |
| --- | --- | --- |
| Frequency (Fréquence)* |frequency |L’unité de temps : `Second`, `Minute`, `Hour`, `Day` ou `Year`. |
| Interval (Intervalle)* |interval |L'intervalle de la fréquence donnée pour la récurrence. |
| Time Zone (Fuseau horaire) |timeZone |Si une heure de début (startTime) est spécifiée sans décalage UTC, ce fuseau horaire est utilisé. |
| Heure de début |startTime |L'heure de début au [format ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations). |

<br>

## <a name="next-steps"></a>Étapes suivantes
Essayez maintenant la plateforme et [créez une application logique](../logic-apps/logic-apps-create-a-logic-app.md). Vous pouvez explorer les autres connecteurs disponibles dans les applications logiques en examinant notre [liste d’API](apis-list.md).




<!--HONumber=Jan17_HO3-->


