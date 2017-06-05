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
ms.sourcegitcommit: ef74361c7a15b0eb7dad1f6ee03f8df707a7c05e
ms.openlocfilehash: f35f693131bd1f4f47b161afb0a4f38d4f00bbd6
ms.contentlocale: fr-fr
ms.lasthandoff: 05/25/2017


---
# <a name="create-a-function-in-azure-that-is-triggered-by-a-timer"></a>Créez une fonction dans Azure, qui est déclenchée par un minuteur

Apprenez à créer une fonction dans Azure, qui s’exécute selon une planification que vous définissez.

![Créer une Function App dans le Portail Azure](./media/functions-create-scheduled-function/function-app-in-portal-editor.png)

## <a name="prerequisites"></a>Composants requis

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

[!INCLUDE [functions-portal-favorite-function-apps](../../includes/functions-portal-favorite-function-apps.md)]

## <a name="create-an-azure-function-app"></a>Création d’une application Azure Function

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

![Function App créée avec succès.](./media/functions-create-first-azure-function/function-app-create-success.png)

Créez ensuite une fonction dans la nouvelle Function App.

<a name="create-function"></a>

## <a name="create-a-timer-triggered-function"></a>Créer une fonction déclenchée par un minuteur

1. Développez votre Function App, cliquez sur le bouton **+** à côté de **Fonctions**, puis cliquez sur le modèle **TimerTrigger** pour la langue de votre choix. Utilisez ensuite les paramètres spécifiés dans le tableau :

    ![Créez une fonction déclenchée par un minuteur dans le portail Azure.](./media/functions-create-scheduled-function/functions-create-timer-trigger.png)

    | Paramètre | Valeur suggérée | Description |
    |---|---|---|
    | **Nommer votre fonction** | TimerTriggerCSharp1 | Définit le nom de votre fonction déclenchée par minuteur. |
    | **[Planification](http://en.wikipedia.org/wiki/Cron#CRON_expression)** | 0 \*/1 \* \* \* \* | Un champ de six [expressions CRON](http://en.wikipedia.org/wiki/Cron#CRON_expression) qui planifie l’exécution de votre fonction chaque minute. |

2. Cliquez sur **Create**. Une fonction est créée dans le langage que vous avez choisi et s’exécute chaque minute.

3. Vérifiez l’exécution en consultant les informations de traçage écrites dans les journaux.

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
