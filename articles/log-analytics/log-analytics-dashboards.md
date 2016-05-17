<properties
	pageTitle="Créer un tableau de bord personnalisé dans Log Analytics | Microsoft Azure"
	description="Ce guide vous aide à comprendre comment les tableaux de bord Log Analytics permettent d'afficher l'ensemble de vos recherches de journal enregistrées en proposant une vue unique de votre environnement."
	services="log-analytics"
	documentationCenter=""
	authors="bandersmsft"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="log-analytics"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/28/2016"
	ms.author="banders"/>

# Créer un tableau de bord personnalisé dans Log Analytics

Ce guide vous aide à comprendre comment les tableaux de bord Log Analytics permettent d'afficher l'ensemble de vos recherches de journal enregistrées en proposant une vue unique de votre environnement.

![Exemple de tableau de bord](./media/log-analytics-dashboards/oms-dashboards-example-dash.png)

## Comment créer un tableau de bord ?

Pour commencer, accédez à la vue d'ensemble d'OMS en cliquant sur le bouton Aperçu situé dans la barre de navigation de gauche. La vignette « Mon tableau de bord » s'affiche sur la gauche. Cliquez dessus pour explorer les détails de votre tableau de bord.

![Vue d'ensemble](./media/log-analytics-dashboards/oms-dashboards-overview.png)



## Ajout d'une vignette

Dans les tableaux de bord, les vignettes sont alimentées par vos recherches de journal enregistrées. OMS propose un grand nombre de recherches enregistrées de journal prédéfinies qui vous permettent de commencer tout de suite. La représentation graphique suivante montre comment commencer.

![Représentation graphique](./media/log-analytics-dashboards/oms-dashboards-pictorial.png)

Dans la vue Mon tableau de bord, cliquez simplement sur l'icône d'engrenage « Personnaliser » située en bas de la page pour passer en mode de personnalisation. Le panneau de configuration qui s'ouvre sur le côté droit de la page affiche toutes les recherches de journal enregistrées de votre espace de travail.

![Ajout de vignettes 1](./media/log-analytics-dashboards/oms-dashboards-add-tile1.png)

Pour visualiser une recherche de journal enregistrée sous la forme d'une vignette, faites-la simplement glisser sur l'espace vide à gauche. À mesure que vous la faites glisser, elle se transforme en vignette.

![Ajout de vignettes 2](./media/log-analytics-dashboards/oms-dashboards-add-tile2.png)

![Ajout de vignettes 3](./media/log-analytics-dashboards/oms-dashboards-add-tile3.png)


## Modification d'une vignette

Dans la vue Mon tableau de bord, cliquez simplement sur l'icône d'engrenage « Personnaliser » située en bas de la page pour passer en mode de personnalisation. Cliquez sur la vignette que vous voulez modifier. Le volet droit devient le volet d'édition, et propose une sélection d'options :

![Modification d'une vignette](./media/log-analytics-dashboards/oms-dashboards-edit-tile.png)

### Visualisations de vignettes#
Vous avez le choix entre deux types de visualisations de vignettes :

|type de graphique|résultat|
|---|---|
|![Graphique à barres](./media/log-analytics-dashboards/oms-dashboards-bar-chart.png)|Affiche une chronologie des résultats de vos recherches de journal enregistrées ou une liste de résultats en fonction d'un champ, selon que votre recherche de journal regroupe les résultats en fonction d'un champ ou non.
|![métrique](./media/log-analytics-dashboards/oms-dashboards-metric.png)|Affiche le nombre total d'accès à vos résultats de recherche de journal sous la forme d'un nombre dans une vignette. Les vignettes de métrique vous permettent de définir un seuil qui met en surbrillance la vignette lorsque ce seuil est atteint.|

### Seuil
Vous pouvez créer un seuil sur une vignette à l'aide de la visualisation Métrique. Sélectionnez « Activé » pour créer une valeur de seuil sur la vignette. Choisissez si la vignette doit être mise en surbrillance lorsque la valeur est supérieure ou inférieure au seuil sélectionné, puis définissez la valeur de seuil au-dessous.

## Organisation du tableau de bord
Pour organiser votre tableau de bord, accédez à la vue Mon tableau de bord et cliquez sur l'icône d'engrenage « Personnaliser » située en bas de la page pour passer en mode de personnalisation. Cliquez sur la vignette que vous voulez déplacer et faites-la glisser vers l'emplacement souhaité.

![Organisation de votre tableau de bord](./media/log-analytics-dashboards/oms-dashboards-organize.png)

## Suppression d'une vignette
Pour supprimer une vignette, accédez à la vue Mon tableau de bord et cliquez sur l'icône d'engrenage **Personnaliser** située en bas de la page pour passer en mode de personnalisation. Sélectionnez la vignette à supprimer, puis dans le volet droit, sélectionnez **Supprimer une vignette**.

![Suppression d'une vignette](./media/log-analytics-dashboards/oms-dashboards-remove-tile.png)

## Étapes suivantes

- Créer des alertes dans Log Analytics pour générer des notifications et corriger les problèmes.

<!---HONumber=AcomDC_0504_2016-->