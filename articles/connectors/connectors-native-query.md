---
title: "Ajout de l’action de requête dans des applications logiques | Microsoft Docs"
description: "Vue d’ensemble de l’action de requête pour les actions comme Filtrer le tableau."
services: 
documentationcenter: 
author: jeffhollan
manager: erikre
editor: 
tags: connectors
ms.assetid: 34e702c7-f9e5-4885-9266-fc7404adecfe
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/20/2016
ms.author: jehollan
ms.translationtype: Human Translation
ms.sourcegitcommit: b92f954680603891ced503a1134791312b5214f0
ms.openlocfilehash: a992fa17a07d6167297c4cf5fe9fb3b58181d7df
ms.contentlocale: fr-fr
ms.lasthandoff: 02/16/2017

---
# <a name="get-started-with-the-query-action"></a>Prise en main de l’action de requête
L’action de requête vous permet d’utiliser des lots et des tableaux pour obtenir des flux de travail afin d’effectuer les tâches suivantes :

* Créer une tâche pour tous les enregistrements à priorité élevée à partir d’une base de données.
* Enregistrer toutes les pièces jointes PDF pour les messages électroniques dans l’objet blob Azure.

Pour commencer à utiliser l’action de requête dans une application logique, consultez [Créer une application logique](../logic-apps/logic-apps-create-a-logic-app.md).

## <a name="use-the-query-action"></a>Utilisation de l’action de requête
Une action est une opération effectuée par le flux de travail défini dans une application logique. [En savoir plus sur les actions](connectors-overview.md).  

L’action de requête a actuellement une opération, appelée Filtrer le tableau, exposée dans le concepteur. Celle-ci vous permet d’interroger un tableau et de retourner un jeu de résultats filtrés.

Voici comment vous pouvez l’ajouter dans une application logique :

1. Sélectionnez le bouton **Nouvelle étape** .
2. Choisissez **Ajouter une action**.
3. Dans la zone de recherche d’action, tapez **Filtrer** pour afficher l’action **Filtrer le tableau**.
   
    ![Sélectionner l’action de requête](./media/connectors-native-query/using-action-1.png)
4. Sélectionnez un tableau à filtrer. (La capture d’écran suivante affiche le tableau des résultats de la recherche Twitter.)
5. Créez une condition à évaluer pour chaque élément. (La capture d’écran suivante filtre les tweets publiés par les utilisateurs ayant plus de 100 abonnés.)
   
    ![Terminer l’action de requête](./media/connectors-native-query/using-action-2.png)
   
    L’action génère un nouveau tableau qui contient uniquement les résultats respectant les exigences du filtre.
6. Cliquez dans le coin supérieur gauche de la barre d’outils pour enregistrer, et votre application logique est à la fois enregistrée et publiée (activation).

## <a name="query-action"></a>Action de requête
Voici les détails de l’action que ce connecteur prend en charge. Le connecteur n’a qu’une seule action possible.

| Action | Description |
| --- | --- |
| Filtrer le tableau |Évalue une condition pour chaque élément d’un tableau et renvoie les résultats |

## <a name="action-details"></a>Détails de l’action
L’action de requête est créée avec une action possible. Les tableaux suivants décrivent les champs de saisie obligatoires et facultatifs pour l’action, ainsi que les détails des résultats correspondants associés à son utilisation.

### <a name="filter-array"></a>Filtrer le tableau
Vous trouverez ci-dessous les champs de saisie de l’action permettant de générer une demande HTTP sortante.
Le symbole * désigne est un champ obligatoire.

| Nom complet | Nom de la propriété | Description |
| --- | --- | --- |
| De* |from |Le tableau à filtrer |
| Condition* |où |La condition à évaluer pour chaque élément |

<br>

### <a name="output-details"></a>Détails des résultats
Vous trouverez ci-dessous les détails de sortie correspondant à la requête HTTP.

| Nom de la propriété | Type de données | Description |
| --- | --- | --- |
| Tableau filtré |array |Tableau contenant un objet pour chaque résultat filtré |

## <a name="next-steps"></a>Étapes suivantes
Essayez maintenant la plateforme et [créez une application logique](../logic-apps/logic-apps-create-a-logic-app.md). Vous pouvez explorer les autres connecteurs disponibles dans les applications logiques en examinant notre [liste d’API](apis-list.md).


