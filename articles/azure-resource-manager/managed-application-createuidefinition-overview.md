---
title: "Comprendre la création de la définition de l’interface utilisateur pour des applications gérées Azure | Microsoft Docs"
description: "Décrit comment créer des définitions d’interface utilisateur pour des applications gérées Azure"
services: azure-resource-manager
documentationcenter: na
author: tabrezm
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/11/2017
ms.author: tabrezm;tomfitz
ms.translationtype: Human Translation
ms.sourcegitcommit: 97fa1d1d4dd81b055d5d3a10b6d812eaa9b86214
ms.openlocfilehash: 176b891538f85c5638a2321561c3d8bd377d245b
ms.contentlocale: fr-fr
ms.lasthandoff: 05/11/2017


---
# <a name="getting-started-with-createuidefinition"></a>Prise en main de CreateUiDefinition
Ce document présente les principaux concepts d’une fonction CreateUiDefinition, utilisée par le portail Azure afin de générer l’interface utilisateur pour la création d’une application gérée.

```json
{
   "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json",
   "handler": "Microsoft.Compute.MultiVm",
   "version": "0.1.2-preview",
   "parameters": {
      "basics": [ ],
      "steps": [ ],
      "outputs": { }
   }
}
```

Une fonction CreateUiDefinition contient toujours trois propriétés : 

* handler
* version
* parameters

Pour les applications gérées, le gestionnaire doit toujours être `Microsoft.Compute.MultiVm`, et la version la plus récente `0.1.2-preview`.

Le schéma de la propriété des paramètres dépend de la version et du gestionnaire spécifiés. Pour les applications gérées, les propriétés prises en charge sont `basics`, `steps`, et `outputs`. Les propriétés basics et steps contiennent des _éléments_, comme des zones de texte et des listes déroulantes, à afficher dans le portail Azure. La propriété outputs est utilisée pour mettre en correspondance les valeurs de sortie des éléments spécifiés avec les paramètres du modèle de déploiement Azure Resource Manager.

L’inclusion de `$schema` est recommandée, mais facultative. Si la valeur de `version` est spécifiée, celle-ci doit correspondre à la version figurant dans l’`$schema` URI.

## <a name="basics"></a>Concepts de base
L’étape relative aux principes de base est toujours la première étape de l’Assistant générée lors de l’analyse d’une fonction CreateUiDefinition par le portail Azure. Outre le fait d’afficher des éléments spécifiés dans `basics`, le portail injecte des éléments permettant aux utilisateurs de choisir l’abonnement, le groupe de ressources et l’emplacement du déploiement. En règle générale, les éléments demandant des paramètres concernant le déploiement, comme le nom d’un cluster ou des informations d’identification administrateur, doivent figurer dans cette étape.

Si le comportement d’un élément dépend de l’abonnement de l’utilisateur, du groupe de ressources ou de l’emplacement, cet élément ne peut pas être utilisé dans les principes de base. Par exemple, **Microsoft.Compute.SizeSelector** dépend de l’abonnement et de l’emplacement de l’utilisateur pour déterminer la liste des tailles disponibles. Par conséquent, **Microsoft.Compute.SizeSelector** ne peut être utilisé que dans steps. En règle générale, seuls les éléments de l’espace de noms **Microsoft.Common** peuvent être utilisés dans basics. Cependant, certains éléments dans d’autres espaces de noms (comme **Microsoft.Compute.Credentials**) qui ne dépendent pas du contexte de l’utilisateur, sont toujours autorisés.

## <a name="steps"></a>Étapes
La propriété steps peut contenir zéro ou plusieurs des étapes supplémentaires à afficher après les principes de base, chacun contenant un ou plusieurs éléments. Vous pouvez ajouter des étapes par rôle ou niveau de l’application déployée. Par exemple, ajoutez une étape pour les entrées destinées aux nœuds principaux et une étape pour les nœuds de travail à un cluster.

## <a name="outputs"></a>Outputs
Le portail Azure utilise la propriété `outputs` pour mettre en correspondance des éléments issus de `basics` et `steps` avec les paramètres du modèle de déploiement Azure Resource Manager. Les clés de ce dictionnaire sont les noms des paramètres du modèle et les valeurs sont les propriétés des objets de sortie issues des éléments référencés.

## <a name="functions"></a>Fonctions
Semblable aux [fonctions de modèle](resource-group-template-functions.md) dans Azure Resource Manager (à la fois en termes de syntaxe et de fonctionnalité), CreateUiDefinition propose des fonctions permettant de travailler avec les entrées et sorties des éléments, ainsi que des fonctions, telles que des logiques conditionnelles.

## <a name="next-steps"></a>Étapes suivantes
CreateUiDefinition elle-même dispose d’un schéma simple. Sa profondeur réelle provient de l’ensemble des éléments et fonctions pris en charge décrits en détail dans les documents suivants :

- [Éléments](managed-application-createuidefinition-elements.md)
- [Fonctions](managed-application-createuidefinition-functions.md)

Un schéma JSON actuel pour CreateUiDefinition est disponible ici : https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json. 

Les versions ultérieures seront disponibles au même emplacement. Remplacez la partie `0.1.2-preview` de l’URL et la valeur `version` par l’identificateur de version que vous souhaitez utiliser. Les identificateurs de version actuellement prise en charge sont `0.0.1-preview`, `0.1.0-preview`, `0.1.1-preview`, et `0.1.2-preview`.
