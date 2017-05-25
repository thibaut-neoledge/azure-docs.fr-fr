---
title: "Créer un tableau de bord personnalisé dans Azure Log Analytics | Microsoft Docs"
description: "Ce guide vous aide à comprendre comment les tableaux de bord Log Analytics permettent d’afficher l’ensemble de vos recherches de journal enregistrées en proposant une vue unique de votre environnement."
services: log-analytics
documentationcenter: 
author: MGoedtel
manager: carmonm
editor: 
ms.assetid: abb07f6c-b356-4f15-85f5-60e4415d0ba2
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/12/2017
ms.author: magoedte
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: a0c8af30fbed064001c3fd393bf0440aa1cb2835
ms.openlocfilehash: a8c9766bf066a7f0dfd28ebb4e41bf0eaf3f05bd
ms.contentlocale: fr-fr
ms.lasthandoff: 02/28/2017


---
# <a name="create-a-custom-dashboard-for-use-in-log-analytics"></a>Créer un tableau de bord personnalisé à utiliser dans Log Analytics
Ce guide vous aide à comprendre comment les tableaux de bord Log Analytics permettent d’afficher l’ensemble de vos recherches de journal enregistrées en proposant une vue unique de votre environnement.

![Exemple de tableau de bord](./media/log-analytics-dashboards/oms-dashboards-example-dash.png)

Tous les tableaux de bord personnalisés que vous créez dans le portail OMS sont également disponibles dans l’application Mobile OMS. Pour plus d’informations sur les applications, voir les pages suivantes.

* [Application mobile OMS de la Boutique Microsoft ](http://www.windowsphone.com/store/app/operational-insights/4823b935-83ce-466c-82bb-bd0a3f58d865)
* [Application mobile OMS d’Apple iTunes](https://itunes.apple.com/app/microsoft-operations-management/id1042424859?mt=8)

![Tableau de bord mobile](./media/log-analytics-dashboards/oms-search-mobile.png)

## <a name="how-do-i-create-my-dashboard"></a>Comment créer un tableau de bord ?
Pour commencer, accédez à la page de présentation d’OMS. La vignette **Mon tableau de bord** s’affiche sur la gauche. Cliquez dessus pour explorer les détails de votre tableau de bord.

![Vue d'ensemble](./media/log-analytics-dashboards/oms-dashboards-overview.png)

## <a name="adding-a-tile"></a>Ajout d'une vignette
Dans les tableaux de bord, les vignettes sont alimentées par vos recherches de journal enregistrées. OMS propose un grand nombre de recherches enregistrées de journal prédéfinies qui vous permettent de commencer tout de suite. Suivez les étapes suivantes pour commencer.

Dans la vue Mon tableau de bord, cliquez simplement sur **Personnaliser** pour passer en mode de personnalisation.

![Représentation graphique](./media/log-analytics-dashboards/oms-dashboards-pictorial01.png)

 Le panneau de configuration qui s'ouvre sur le côté droit de la page affiche toutes les recherches de journal enregistrées de votre espace de travail. Pour visualiser une recherche de journal enregistrée sous forme de vignette, pointez sur une recherche enregistrée, puis cliquez sur le signe **plus**.

![Ajout de vignettes 1](./media/log-analytics-dashboards/oms-dashboards-pictorial02.png)

Lorsque vous cliquez sur le signe **plus**, une nouvelle vignette s’affiche dans la vue Mon tableau de bord.

![Ajout de vignettes 2](./media/log-analytics-dashboards/oms-dashboards-pictorial03.png)

## <a name="edit-a-tile"></a>Modification d'une vignette
Dans la vue Mon tableau de bord, cliquez simplement sur  **Personnaliser** pour passer en mode de personnalisation. Cliquez sur la vignette que vous voulez modifier. Le volet droit devient le volet d'édition, et propose une sélection d'options :

![Modification d'une vignette](./media/log-analytics-dashboards/oms-dashboards-pictorial04.png)

![Modification d'une vignette](./media/log-analytics-dashboards/oms-dashboards-pictorial05.png)

### <a name="tile-visualizations"></a>Visualisations de vignettes
Vous avez le choix entre deux sortes de visualisations de vignettes :

| type de graphique | résultat |
| --- | --- |
| ![Graphique à barres](./media/log-analytics-dashboards/oms-dashboards-bar-chart.png) |Affiche une chronologie des résultats de vos recherches de journal enregistrées sous forme de graphique à barres, ou une liste de résultats en fonction d’un champ, selon que votre recherche de journal regroupe les résultats par champ ou non. |
| ![métrique](./media/log-analytics-dashboards/oms-dashboards-metric.png) |Affiche le nombre total d'accès à vos résultats de recherche de journal sous la forme d'un nombre dans une vignette. Les vignettes de métrique vous permettent de définir un seuil qui met en surbrillance la vignette lorsque ce seuil est atteint. |
| ![line](./media/log-analytics-dashboards/oms-dashboards-line.png) |Affiche une chronologie de vos résultats de recherche journal enregistrés, avec des valeurs représentées sous la forme d’un graphique en courbes. |

### <a name="threshold"></a>Seuil
Vous pouvez créer un seuil sur une vignette à l'aide de la visualisation Métrique. Sélectionnez « Activé » pour créer une valeur de seuil sur la vignette. Choisissez si la vignette doit être mise en surbrillance lorsque la valeur est supérieure ou inférieure au seuil sélectionné, puis définissez la valeur de seuil au-dessous.

## <a name="organizing-the-dashboard"></a>Organisation du tableau de bord
Pour organiser votre tableau de bord, accédez à la vue Mon tableau de bord, puis cliquez sur **Personnaliser** pour passer en mode de personnalisation. Cliquez sur la vignette que vous voulez déplacer et faites-la glisser vers l'emplacement souhaité.

![Organisation de votre tableau de bord](./media/log-analytics-dashboards/oms-dashboards-organize.png)

## <a name="remove-a-tile"></a>Suppression d'une vignette
Pour supprimer une vignette, accédez à la vue Mon tableau de bord, puis cliquez sur **Personnaliser** pour passer en mode de personnalisation. Sélectionnez la vignette à supprimer, puis dans le volet droit, sélectionnez **Supprimer une vignette**.

![Suppression d'une vignette](./media/log-analytics-dashboards/oms-dashboards-remove-tile.png)

## <a name="next-steps"></a>Étapes suivantes
* Créez des [alertes](log-analytics-alerts.md) dans Log Analytics pour générer des notifications et corriger les problèmes.

