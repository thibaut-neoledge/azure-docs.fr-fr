---
title: "Examiner et de partager des données d’utilisation avec des classeurs interactifs dans Azure Application Insights | Microsoft docs"
description: "Analyse démographique des utilisateurs de votre application web."
services: application-insights
documentationcenter: 
author: numberbycolors
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 06/12/2017
ms.author: cfreeman
ms.translationtype: Human Translation
ms.sourcegitcommit: 7948c99b7b60d77a927743c7869d74147634ddbf
ms.openlocfilehash: 44ea03d30c1f2eabc92af63b549823c03a6901e2
ms.contentlocale: fr-fr
ms.lasthandoff: 06/20/2017

---

# <a name="investigate-and-share-usage-data-with-interactive-workbooks-in-application-insights"></a>Examiner et de partager des données d’utilisation avec des classeurs interactifs dans Azure Application Insights

Les classeurs associent des visualisations de données [Azure Application Insights](app-insights-overview.md), [des requêtes Analytics](app-insights-analytics.md)et du texte dans des documents interactifs. Les classeurs sont modifiables par les autres membres de l’équipe ayant accès à la même ressource Azure. Cela signifie que les requêtes et les contrôles utilisés pour créer un classeur sont à la disposition d’autres personnes consultant ce dernier, ce qui les rend faciles à explorer, à étendre et simplifie les recherches dans ceux-ci.

Les classeurs sont utiles pour :

* Explorer l’utilisation de votre application lorsque vous ne connaissez pas les mesures d’intérêt à l’avance : nombre d’utilisateurs, taux de rétention, taux de conversion, etc.. Contrairement à d’autres outils d’analyse d’utilisation d’Application Insights, les classeurs vous permettent de combiner plusieurs types de visualisations et d’analyses, ce qui les rend très utile pour ce type d’exploration sous forme libre.
* Expliquer à votre équipe le fonctionnement d’une toute nouvelle fonctionnalité, en montrant le nombre d’interactions clés et d’autres mesures de l’utilisateur.
* Partager les résultats d’une expérimentation A/B dans votre application avec d’autres membres de votre équipe. Vous pouvez expliquer les objectifs de l’expérimentation avec le texte, puis montrer chaque mesure d’utilisation et requête Analytics utilisée pour évaluer l’expérimentation, en vous aidant de légendes claires indiquant si chaque mesure se situe au-dessus ou en dessous de la cible.
* Créer des rapports relatifs à l’impact d’une panne sur l’utilisation de votre application, en combinant des données, une explication du texte et une présentation des étapes suivantes pour éviter à l’avenir d’éventuelles interruptions.

> [!NOTE]
> Votre ressource Application Insights doit contenir des vues de page ou des événements personnalisés pour utiliser les classeurs. [Découvrez comment configurer votre application pour collecter des vues de page automatiquement à l’aide du Kit de développement logiciel (SDK) JavaScript Application Insights](app-insights-javascript.md).
> 
> 

## <a name="editing-rearranging-cloning-and-deleting-workbook-sections"></a>Modifier, réorganiser, cloner et supprimer des sections de classeur

Un classeur est composé de sections : visualisations d’utilisation, graphiques, tables, texte ou résultats de requête Analytics modifiables de manière indépendante.

Pour modifier le contenu d’une section de classeur, cliquez sur le bouton **Modifier** ci-dessous et à droite de la section du classeur.

![Commandes d’édition de la section Workbooks d’Application Insights](./media/app-insights-usage-workbooks/editing-controls.png)

1. Lorsque vous avez fini de modifier une section, cliquez sur **Modifications terminées** dans l’angle inférieur gauche de la section.

2. Pour créer un doublon de section, cliquez sur l’icône **Cloner cette section**. La création de sections en double est une bonne façon d’itérer sur une requête sans perdre les itérations précédentes.

3. Pour déplacer une section vers un classeur, cliquez sur l’icône **Déplacer vers le haut** ou **Déplacer vers le bas**.

4. Pour supprimer une section de façon permanente, cliquez sur l’icône **Supprimer**.

## <a name="adding-usage-data-visualization-sections"></a>Ajout de sections de visualisation de données d’utilisation

Les classeurs proposent quatre types de visualisations d’analyse d’utilisation intégrées. Chacune répond à une question courante concernant l’utilisation de votre application. Pour ajouter des tables et des graphiques autres que ces quatre sections, ajoutez des sections de requête Analytics (voir ci-dessous).

Pour ajouter une section Utilisateurs, Sessions, Événements ou Rétention à votre classeur, utilisez **Ajouter des utilisateurs** ou tout autre bouton correspondant au bas du classeur ou d’une section.

![Section Utilisateurs dans Workbooks](./media/app-insights-usage-workbooks/users-section.png)

Les sections **Utilisateurs** répondent à la question « Combien d’utilisateurs ont visualisé une page ou utilisé une certaine fonctionnalité de mon site ? »

Les sections **Sessions** répondent à la question « Combien de sessions les utilisateurs ont-ils passé à afficher une page ou à utiliser certaines fonctionnalités de mon site ? »

Les sections **Événements** répondent à la question « Combien de fois les utilisateurs ont-ils affiché une page ou utilisé certaines fonctionnalités de mon site ? »

Chacun de ces types de trois sections propose les mêmes ensembles de commandes et de visualisations :

* [En savoir plus sur la modification des sections Utilisateurs, Sessions et Événements](app-insights-usage-segmentation.md)
* Activez/désactivez les visualisations relatives au graphique principal, aux grilles d’histogramme, aux insights automatiques et aux échantillons d’utilisateurs à l’aide des cases à cocher **Afficher le graphique**, **Afficher la grille**, **Afficher les insights**, et **Échantillon de ces utilisateurs** situées en haut de chaque section.

![Section Rétention dans Workbooks](./media/app-insights-usage-workbooks/retention-section.png)

Les sections **Rétention** répondent à la question « Sur les personnes ayant affiché une page ou utilisé des fonctionnalités un jour ou une semaine donnée, combien sont revenues le jour ou la semaine suivante ? »

* [En savoir plus sur la modification des sections Rétention](app-insights-usage-retention.md)
* Activez/désactivez le graphique facultatif de rétention global à l’aide de la case à cocher **Afficher le graphique de rétention global**située en haut de la section.

## <a name="adding-application-insights-analytics-sections"></a>Ajout de sections Application Insights Analytics

![Section Analytics dans Workbooks](./media/app-insights-usage-workbooks/analytics-section.png)

Pour ajouter une section Application Insights Analytics à votre classeur, utilisez le bouton **Ajouter une requête Analytics** situé en bas du classeur, ou de chaque section.

Les sections de requête Analytics vous permettent d’ajouter des requêtes arbitraires sur vos données Application Insights dans les classeurs. Cette flexibilité signifie que les sections de requête Analytics doivent être votre réponse privilégiée à toute question relative à votre site autre que les quatre répertoriées ci-dessus pour les Utilisateurs, les Sessions, les Événements et la Rétention :

* Combien d’exceptions votre site a levé au cours de la même période en tant que refus d’utilisation ?
* Quelle était la répartition des temps de chargement de page pour les utilisateurs affichant une page ?
* Combien d’utilisateurs ont affiché certaines pages de votre site en particulier ? Il peut être utile de comprendre si des groupes d’utilisateurs utilisent différents sous-ensembles de fonctionnalités de votre site (utilisez l’opérateur `join` avec le modificateur `kind=leftanti` dans le langage de requête Analytics).

Utilisez le [document de référence du langage de requête Analytics](app-insights-analytics-reference.md) pour en savoir plus sur l’écriture des requêtes.

## <a name="adding-text-and-markdown-sections"></a>Ajout de texte et de sections Markdown

Le fait d’ajouter des en-têtes, des explications et des commentaires à vos classeurs permet de donner un aspect narratif à un ensemble de tables et de graphiques. Les sections de texte des classeurs prennent en charge la [syntaxe Markdown](https://daringfireball.net/projects/markdown/) pour la mise en forme du texte (en-têtes, gras, italique et listes à puces, par exemple).

Pour ajouter une section de textes à votre classeur, utilisez le bouton **Ajouter du texte** situé en bas du classeur, ou de chaque section.

## <a name="saving-and-sharing-workbooks-with-your-team"></a>Enregistrer et partager des classeurs avec votre équipe

Les classeurs sont enregistrés au sein d’une ressource Application Insights, soit dans la section **Mes rapports** qui est privée ou dans la section **Rapports partagés** accessible à tous les utilisateurs ayant accès à la ressource Application Insights. Pour afficher tous les classeurs de la ressource, cliquez sur le bouton **Ouvrir** dans la barre d’action.

Pour partager un classeur qui se trouve actuellement dans **Mes rapports** :

1. Dans la barre d’action, cliquez sur **Ouvrir**.
2. Cliquez sur le bouton «... » en regard du classeur que vous souhaitez partager.
3. Cliquez sur **Déplacer vers les rapports partagés**.

Pour partager un classeur avec un lien ou par e-mail, cliquez sur **Partager** dans la barre d’action. Gardez à l’esprit que les destinataires du lien ont besoin d’accéder à cette ressource dans le portail Azure pour afficher le classeur. Pour apporter des modifications, les destinataires doivent au moins disposer des autorisations de collaborateur pour la ressource.

Pour épingler un lien à un classeur dans un tableau de bord Azure :

1. Dans la barre d’action, cliquez sur **Ouvrir**.
2. Cliquez sur le bouton «... » en regard du classeur que vous souhaitez épingler.
3. Cliquez sur **Épingler au tableau de bord**.

## <a name="next-steps"></a>Étapes suivantes

* [Vue d’ensemble de l’utilisation](app-insights-usage-overview.md)
* [Utilisateurs, Sessions et Événements](app-insights-usage-segmentation.md)
* [Rétention](app-insights-usage-retention.md)
* [Ajout d’événements personnalisés à votre application](app-insights-api-custom-events-metrics.md)


