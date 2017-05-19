---
title: "Créez une fonction qui s’exécute selon une planification dans Azure | Microsoft Docs"
description: "Apprenez à créer une fonction dans Azure, qui s’exécute selon une planification que vous définissez."
services: functions
documentationcenter: na
author: ggailey777
manager: erikre
editor: 
tags: 
ms.assetid: ba50ee47-58e0-4972-b67b-828f2dc48701
ms.service: functions
ms.devlang: multiple
ms.topic: get-started-article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/02/2017
ms.author: glenga
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 10461bddeb4d5adf4a6e4f65159ba2653dbef8a4
ms.contentlocale: fr-fr
ms.lasthandoff: 05/10/2017


---
#  <a name="create-a-function-in-azure-that-is-triggered-by-a-timer"></a>Créez une fonction dans Azure, qui est déclenchée par un minuteur

Apprenez à créer une fonction dans Azure, qui s’exécute selon une planification que vous définissez. 

![Créer une Function App dans le Portail Azure](./media/functions-create-scheduled-function/function-app-in-portal-editor.png)

Moins de cinq minutes sont nécessaires pour effectuer toutes les étapes de cette rubrique.

## <a name="prerequisites"></a>Composants requis 

[!INCLUDE [Previous quickstart note](../../includes/functions-quickstart-previous-topics.md)]

Dans cette rubrique, vous créez une fonction déclenchée par un minuteur dans votre Function App existante. 

[!INCLUDE [functions-portal-favorite-function-apps](../../includes/functions-portal-favorite-function-apps.md)] 

## <a name="create-function"></a>Créer une fonction déclenchée par un minuteur

1. Développez votre Function App, cliquez sur le bouton **+** à côté de **Fonctions**, puis cliquez sur le modèle **TimerTrigger** pour la langue de votre choix. Ensuite, utilisez les paramètres comme indiqué dans la table, et cliquez sur **Créer** :

    | Paramètre      |  Valeur suggérée   | Description                              |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Nommer votre fonction** | TimerTriggerCSharp1 | Définit le nom de votre fonction déclenchée par minuteur.
    | **[Planification](http://en.wikipedia.org/wiki/Cron#CRON_expression)** | 0 */1 * * * * | Un champ de six [expressions CRON](http://en.wikipedia.org/wiki/Cron#CRON_expression) qui planifie l’exécution de votre fonction chaque minute. |

    Une fonction est créée dans le langage que vous avez choisi et s’exécute chaque minute. 

4. Vérifiez l’exécution en consultant les informations de traçage écrites dans les journaux. 

    ![Fonctions de visionneuse du journal dans le Portail Azure.](./media/functions-create-scheduled-function/functions-timer-trigger-view-logs2.png)

À présent, vous pouvez modifier la planification de la fonction afin qu’elle s’exécute moins souvent, par exemple une fois par heure. 

## <a name="update-the-timer-schedule"></a>Mise à jour de la planification du minuteur

1. Développez votre fonction et cliquez sur **Intégrer**. Il s’agit de l’endroit où vous définissez les liaisons d’entrée et de sortie pour votre fonction, et où vous configurez la planification. 

2. Entrez une nouvelle valeur de **Planification** de `0 0 */1 * * *`, puis cliquez sur **Enregistrer**.  

![Les fonctions mettent à jour la planification du minuteur dans le Portail Azure.](./media/functions-create-scheduled-function/functions-timer-trigger-change-schedule.png)

Vous disposez maintenant d’une fonction qui s’exécute toutes les heures. 

## <a name="clean-up-resources"></a>Supprimer des ressources

[!INCLUDE [Next steps note](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Étapes suivantes
Vous avez créé une fonction qui s’exécute selon une planification. 

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]

Pour en savoir plus sur les déclencheurs de minuteur, consultez la page [Planifier l’exécution de code avec Azure Functions](functions-bindings-timer.md). 




