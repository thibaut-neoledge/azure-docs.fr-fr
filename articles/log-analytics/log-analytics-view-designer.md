---
title: Concepteur de vues de Log Analytics | Microsoft Docs
description: "Le Concepteur de vues de Log Analytics permet de créer dans la console OMS des vues personnalisées contenant différentes visualisations des données du référentiel OMS. Cet article contient une présentation du Concepteur de vues et des procédures de création et modification des vues personnalisées."
services: log-analytics
documentationcenter: 
author: bwren
manager: jwhit
editor: 
ms.assetid: ce41dc30-e568-43c1-97fa-81e5997c946a
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/09/2016
ms.author: bwren
translationtype: Human Translation
ms.sourcegitcommit: c6190a5a5aba325b15aef97610c804f5441ef7ad
ms.openlocfilehash: efe0eeb6c9389545f51f22bbf0a2d6389939c375


---
# <a name="log-analytics-view-designer"></a>Concepteur de vues de Log Analytics
Le Concepteur de vues de Log Analytics permet de créer des vues personnalisées dans la console OMS, qui contiennent différentes visualisations de données du référentiel OMS. Cet article contient une présentation du Concepteur de vues et des procédures de création et modification des vues personnalisées.

Autres articles disponibles concernant le Concepteur de vues :

* [Référence de vignette](log-analytics-view-designer-tiles.md) - référence des paramètres pour chacune des vignettes utilisables dans vos vues personnalisées. 
* [Référence des composants de visualisation](log-analytics-view-designer-parts.md) - référence des paramètres pour chacune des vignettes utilisables dans vos vues personnalisées. 

## <a name="concepts"></a>Concepts
Les vues créées avec le Concepteur de vues contiennent les éléments répertoriés dans le tableau suivant.

| Partie | Description |
|:--- |:--- |
| Vignette |Affichée sur le tableau de bord principal de vue d’ensemble de Log Analytics.  Inclut un résumé visuel des informations contenues dans la vue personnalisée.  Les différents types de vignettes fournissent des visualisations différentes d’enregistrements dans le référentiel OMS.  Cliquez sur la vignette pour ouvrir la vue personnalisée. |
| Vue personnalisée |Affichée lorsque l’utilisateur clique sur la vignette.  Contient un ou plusieurs composants de visualisation. |
| Composants de visualisation |Visualisation de données dans le référentiel OMS en fonction d’une ou plusieurs [recherches de journal](log-analytics-log-searches.md).  La plupart des composants incluent un en-tête qui fournit une visualisation d’ensemble et une liste des premiers résultats.  Les différents types de composants produisent différents visualisations d’enregistrements dans le référentiel OMS.  Cliquez sur des éléments dans le composant pour effectuer une recherche de journal fournissant des enregistrements détaillés. |

![Vue d’ensemble du concepteur](media/log-analytics-view-designer/overview.png)

## <a name="add-view-designer-to-your-workspace"></a>Ajouter un concepteur de vues à votre espace de travail
Bien que le Concepteur de vues soit en version préliminaire, vous devez l’ajouter à votre espace de travail en sélectionnant **Fonctionnalités préliminaires** dans la section **Paramètres** du portail OMS.

![Activer la version préliminaire](media/log-analytics-view-designer/preview.png)

## <a name="creating-and-editing-views"></a>Création et modification de vues
### <a name="create-a-new-view"></a>Créer une vue
Ouvrez une nouvelle vue dans le **Concepteur de vues** en cliquant sur la vignette de celui-ci dans le tableau de bord OMS principal.

![Vignette Concepteur de vues](media/log-analytics-view-designer/view-designer-tile.png)

### <a name="edit-an-existing-view"></a>Modifier une vue
Pour modifier une vue dans le Concepteur de vues, ouvrez la vue en cliquant sur sa vignette dans le tableau de bord OMS principal.  Cliquez ensuite sur le bouton **Modifier** pour ouvrir la vue dans le Concepteur de vues.

![Modifier une vue](media/log-analytics-view-designer/menu-edit.png)

### <a name="clone-an-existing-view"></a>Cloner une vue
Lorsque vous clonez une vue, cela a pour effet de créer une vue et de l’ouvre dans le Concepteur de vues.  La nouvelle vue porte le même nom que la vue originale, avec le mot « Copy » ajouté à la fin.  Pour cloner une vue, ouvrez la vue existante en cliquant sur sa vignette dans le tableau de bord OMS principal.  Cliquez ensuite sur le bouton **Cloner** pour ouvrir la vue dans le Concepteur de vues.

![Cloner une vue](media/log-analytics-view-designer/edit-menu-clone.png)

### <a name="delete-an-existing-view"></a>Supprimer une vue
Pour supprimer une vue, ouvrez-la en cliquant sur sa vignette dans le tableau de bord OMS principal.  Cliquez ensuite sur le bouton **Modifier** pour ouvrir la vue dans le Concepteur de vues, puis cliquez sur **Supprimer la vue**.

![Supprimer une vue](media/log-analytics-view-designer/edit-menu-delete.png)

### <a name="export-an-existing-view"></a>Exporter une vue
Vous pouvez exporter une vue dans un fichier JSON que vous pouvez importer dans un autre espace de travail ou utiliser dans un [modèle Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md).  Pour exporter une vue, ouvrez-la en cliquant sur sa vignette dans le tableau de bord OMS principal.  Cliquez ensuite sur le bouton **Exporter** pour créer un fichier dans le dossier de téléchargement du navigateur.  Le nom du fichier celui de la vue, avec l’extension *omsview*.

![Exporter une vue](media/log-analytics-view-designer/edit-menu-export.png)

### <a name="import-an-existing-view"></a>Importer une vue
Vous pouvez importer un fichier *omsview* que vous avez exporté à partir d’un autre groupe d’administration.  Pour importer une vue, commencez par créer une vue.  Cliquez ensuite sur le bouton **Importer**, puis sélectionnez le fichier *omsview*.  La configuration du fichier est copiée dans la vue existante.

![Exporter une vue](media/log-analytics-view-designer/edit-menu-import.png)

## <a name="working-with-view-designer"></a>Utilisation du Concepteur de vues
Le Concepteur de vues comporte trois volets.  Le volet **Conception** représente la vue personnalisée.  Lorsque vous ajoutez des vignettes et composants du volet **Contrôle** au volet **Conception**, ils sont ajoutés à la vue.  Le volet **Propriétés** affiche les propriétés de la vignette ou du composant sélectionnés.

![Concepteur de vues](media/log-analytics-view-designer/view-designer-screenshot.png)

### <a name="configure-view-tile"></a>Vignette Configurer la vue
Un vue personnalisée ne peut avoir qu’une seule vignette.  Pour afficher la vignette active ou en sélectionner une autre, dans le volet **Contrôle** sélectionnez l’onglet **Vignette**.  Le volet **Propriétés** affiche les propriétés de la vignette active.  Configurez les propriétés de la vignette conformément aux informations détaillées de la [Référence de la vignette](log-analytics-view-designer-tiles.md), puis cliquez sur **Appliquer** pour enregistrer les modifications.

### <a name="configure-visualization-parts"></a>Configurer les composants de visualisation
Une vue peut inclure une nombre quelconque de composants de visualisation.  Sélectionnez l’onglet **Vue**, puis un composant de visualisation à ajouter à la vue.  Le volet **Propriétés** affiche les propriétés du composant sélectionné.  Configurez les propriétés de la vue conformément aux informations détaillées de la [Référence du composant de visualisation](log-analytics-view-designer-parts.md), puis cliquez sur **Appliquer** pour enregistrer les modifications.

### <a name="delete-a-visualization-part"></a>Supprimer un composant de visualisation
Vous pouvez supprimer un composant de visualisation de la vue en cliquant sur le bouton **X** dans l’angle supérieur droit du composant.

### <a name="rearrange-visualization-parts"></a>Réorganiser les composants de visualisation
Les vues ne comprennent qu’une seule ligne de composants de visualisation.  Réorganisez les composants existants dans une vue en cliquant dessus, puis en les faisant glisser vers un nouvel emplacement.

## <a name="next-steps"></a>Étapes suivantes
* Ajouter des [vignettes](log-analytics-view-designer-tiles.md) à votre vue personnalisée.
* Ajouter des [composants de visualisation](log-analytics-view-designer-parts.md) à votre vue personnalisée.




<!--HONumber=Dec16_HO4-->


