---
title: "Inspecteurs pris en charge disponibles avec la préparation des données Azure Machine Learning | Microsoft Docs"
description: "Ce document fournit la liste complète des inspecteurs disponibles pour la préparation de données Azure ML."
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: 
ms.service: machine-learning
ms.workload: data-services
ms.custom: 
ms.devlang: 
ms.topic: article
ms.date: 09/11/2017
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: bc14c051fb0f518b1cff2236a61d24cb052700f0
ms.contentlocale: fr-fr
ms.lasthandoff: 09/25/2017

---

# <a name="supported-inspectors-for-this-preview"></a>Inspecteurs pris en charge pour cette version d’évaluation
Ce document décrit le jeu d’inspecteurs disponibles dans cette version d’évaluation.

## <a name="the-halo-effect"></a>Effet de Halo 
Certains inspecteurs prennent en charge l’effet de Halo. Cet effet utilise deux couleurs différentes pour indiquer immédiatement et visuellement la modification d’une transformation. La couleur grise représente la valeur précédente de la transformation la plus récente, tandis que la couleur bleue indique la valeur actuelle. Cet effet peut être activé ou désactivé dans les options.

## <a name="graphical-filtering"></a>Filtrage graphique 
Certains inspecteurs prennent en charge le filtrage des données en utilisant l’inspecteur en tant qu’éditeur. Cela implique la sélection d’éléments de graphiques, puis l’utilisation de la barre d’outils en haut à droite de la fenêtre Inspecteur pour filtrer les valeurs sélectionnées. 

## <a name="column-statistics"></a>Statistiques de colonne
Pour les colonnes numériques, cet inspecteur fournit un éventail de statistiques différentes relatives à la colonne. Les statistiques incluent les éléments suivants :
- Minimale
- Quartile inférieur
- Quartile médian
- Quartile supérieur
- Maximale
- Moyenne
- Écart type


### <a name="options"></a>Options 
- Aucun

## <a name="histogram"></a>Histogramme 
Calcule et affiche un histogramme d’une colonne numérique unique. Le nombre de compartiments par défaut est calculé à l’aide de la règle de Scott, qui peut être remplacée dans les options.
Cet inspecteur prend en charge l’effet de Halo.


### <a name="options"></a>Options
- Nombre minimal de compartiments (s’applique même lorsque la création de compartiments par défaut est activée)
- Nombre de compartiments par défaut (règle de Scott) 
- Afficher le halo
- Superposition du tracé de la densité du noyau (noyau gaussien) 


### <a name="actions"></a>Actions
Cet inspecteur prend en charge le filtrage via les compartiments, ainsi que la sélection unique ou multiple de compartiments, et il applique les filtres comme indiqué précédemment.

## <a name="value-counts"></a>Nombres de valeurs
Cet inspecteur présente un tableau de la fréquence des valeurs pour la colonne actuellement sélectionnée. L’affichage par défaut correspond aux 6 premières valeurs. La limite peut être remplacée par n’importe quel nombre en partant du bas. Cet inspecteur prend en charge l’effet de Halo.

### <a name="options"></a>Options 
- Nombre de premières valeurs affichées
- Tri décroissant
- Inclure les erreurs/valeurs NULL
- Afficher le halo


### <a name="actions"></a>Actions 
Cet inspecteur prend en charge le filtrage via les barres, ainsi que la sélection unique ou multiple des barres, et il applique les filtres comme indiqué précédemment.

## <a name="box-plot"></a>Diagramme à surfaces 
Diagramme à surfaces en boîtes à moustaches d’une colonne numérique.

### <a name="options"></a>Options 
- Regrouper par colonne

## <a name="scatter-plot"></a>Nuage de points
Nuage de points pour deux colonnes numériques. Les données sont échantillonnées vers le bas pour des raisons de performances et la taille d’échantillon peut être modifiée dans les options.

### <a name="options"></a>Options  
- Colonne de l’axe X
- Colonne de l’axe Y
- Taille d’échantillon
- Regrouper par colonne


## <a name="time-series"></a>Série chronologique
Graphique linéaire avec reconnaissance du temps sur l’axe X.

### <a name="options"></a>Options
- Colonne de date
- Colonne numérique
- Taille d’échantillon


### <a name="actions"></a>Actions
Cet inspecteur prend en charge le filtrage via une méthode de sélection de type « cliquez et faites glisser » afin de sélectionner une plage sur le graphique. Une fois la sélection terminée, appliquez les filtres comme indiqué précédemment.


## <a name="map"></a>Mappage 
Mappage avec des points tracés en supposant que la latitude et la longitude ont été spécifiées. La latitude doit être sélectionnée en premier.

### <a name="options"></a>Options
- Colonne de latitude
- Colonne de longitude
- Clustering
- Regrouper par colonne


### <a name="actions"></a>Actions
Cet inspecteur prend en charge le filtrage par sélection de point sur le mappage. Appuyez sur la touche Contrôle, puis cliquez et faites glisser la souris pour former un carré autour des points. Ensuite, appliquez les filtres comme indiqué précédemment.
Vous pouvez redimensionner rapidement le mappage afin d’afficher uniquement tous les points possibles en appuyant sur **E** sur le côté gauche du mappage.

