---
title: "Ajout d’un retard dans des applications logiques | Microsoft Docs"
description: "Vue d’ensemble des actions Retarder et Retarder jusqu’à et de leur utilisation avec une application Azure logique."
services: 
documentationcenter: 
author: jeffhollan
manager: erikre
editor: 
tags: connectors
ms.assetid: 915f48bf-3bd8-4656-be73-91a941d0afcd
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/18/2016
ms.author: jehollan
ms.translationtype: Human Translation
ms.sourcegitcommit: b92f954680603891ced503a1134791312b5214f0
ms.openlocfilehash: 5f4f7052d48b4ca4ed91212d970551141e78e852
ms.contentlocale: fr-fr
ms.lasthandoff: 02/16/2017

---
# <a name="get-started-with-the-delay-and-delay-until-actions"></a>Prise en main des actions Retarder et Retarder jusqu’à
Les actions Retarder et Retarder jusqu’à vous permettent d’exécuter des scénarios de workflow.

Vous pouvez par exemple afficher :

* Attendre un jour de semaine pour envoyer une mise à jour d’état par e-mail.
* Retarder le workflow jusqu’à ce qu’un appel HTTP dispose d’assez de temps avant la reprise et la récupération du résultat.

Pour commencer à utiliser l’action Retarder dans une application logique, consultez [Créer une application logique](../logic-apps/logic-apps-create-a-logic-app.md).

## <a name="use-the-delay-actions"></a>Utilisation d’actions Retarder
Une action est une opération effectuée par le flux de travail défini dans une application logique. [En savoir plus sur les actions](connectors-overview.md).

Voici un exemple de séquence d’utilisation d’une étape de retard dans une application logique :

1. Après avoir ajouté un déclencheur, cliquez sur **Nouvelle étape** pour ajouter une action.
2. Recherchez **Retarder** pour afficher les actions Retarder. Dans cet exemple, nous sélectionnons **Retarder**.
   
    ![Action Retarder](./media/connectors-native-delay/using-action-1.png)
3. Exécutez toutes les propriétés de l’action pour configurer le retard.
   
    ![Configuration du retard](./media/connectors-native-delay/using-action-2.png)
4. Cliquez sur **Enregistrer** pour publier et activer l’application logique.

## <a name="action-details"></a>Détails de l’action
Le déclencheur de périodicité a les propriétés suivantes qui peuvent être configurées.

### <a name="delay-action"></a>Action Retarder
Cette action retarde l’exécution pour un intervalle de temps donné.
A * désigne est un champ obligatoire.

| Nom complet | Nom de la propriété | Description |
| --- | --- | --- |
| Count* |count |Le nombre d’unités à retarder |
| Unit* |unité |L’unité de temps : `Second`, `Minute`, `Hour` ou `Day` |

<br>

### <a name="delay-until-action"></a>Action Retarder jusqu'à
Cette action retarde l’exécution jusqu’à une date/heure spécifique.
A * désigne est un champ obligatoire.

| Nom complet | Nom de la propriété | Description |
| --- | --- | --- |
| Year* |timestamp |L’année jusqu’à laquelle le retard doit être défini (GMT) |
| Month* |timestamp |Le mois jusqu’auquel le retard doit être défini (GMT) |
| Day* |timestamp |Le jour jusqu’auquel le retard doit être défini (GMT) |

<br>

## <a name="next-steps"></a>Étapes suivantes
Essayez maintenant la plateforme et [créez une application logique](../logic-apps/logic-apps-create-a-logic-app.md). Vous pouvez explorer les autres connecteurs disponibles dans les applications logiques en examinant notre [liste d’API](apis-list.md).


