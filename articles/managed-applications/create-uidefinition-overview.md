---
title: "Créer une définition d’interface utilisateur pour les applications managées Azure | Microsoft Docs"
description: "Décrit comment créer des définitions d’interface utilisateur pour des applications managées Azure"
services: managed-applications
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: managed-applications
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/26/2017
ms.author: tomfitz
ms.openlocfilehash: d8f04d8ed2e56cecb1b7a850bed55a02a9492bb5
ms.sourcegitcommit: 3ab5ea589751d068d3e52db828742ce8ebed4761
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/27/2017
---
# <a name="create-azure-portal-user-interface-for-your-managed-application"></a>Créer une interface utilisateur de portail Azure pour votre application managée
Ce document présente les concepts de base du fichier createUiDefinition.json. Le portail Azure utilise ce fichier pour générer l’interface utilisateur permettant de créer une application managée.

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

Pour les applications managées, le gestionnaire doit toujours être `Microsoft.Compute.MultiVm`, et la version la plus récente `0.1.2-preview`.

Le schéma de la propriété des paramètres dépend de la version et du gestionnaire spécifiés. Pour les applications managées, les propriétés prises en charge sont `basics`, `steps` et `outputs`. Les propriétés basics et steps contiennent des _éléments_, comme des zones de texte et des listes déroulantes, à afficher dans le portail Azure. La propriété outputs est utilisée pour mettre en correspondance les valeurs de sortie des éléments spécifiés avec les paramètres du modèle de déploiement Azure Resource Manager.

L’inclusion de `$schema` est recommandée, mais facultative. Si la valeur de `version` est spécifiée, celle-ci doit correspondre à la version figurant dans l’`$schema` URI.

## <a name="basics"></a>Concepts de base
L’étape relative aux concepts de base est toujours la première étape de l’Assistant généré quand le portail Azure analyse le fichier. Outre le fait d’afficher des éléments spécifiés dans `basics`, le portail injecte des éléments permettant aux utilisateurs de choisir l’abonnement, le groupe de ressources et l’emplacement du déploiement. En règle générale, les éléments demandant des paramètres concernant le déploiement, comme le nom d’un cluster ou des informations d’identification administrateur, doivent figurer dans cette étape.

Si le comportement d’un élément dépend de l’abonnement de l’utilisateur, du groupe de ressources ou de l’emplacement, cet élément ne peut pas être utilisé dans les principes de base. Par exemple, **Microsoft.Compute.SizeSelector** dépend de l’abonnement et de l’emplacement de l’utilisateur pour déterminer la liste des tailles disponibles. Par conséquent, **Microsoft.Compute.SizeSelector** ne peut être utilisé que dans steps. En règle générale, seuls les éléments de l’espace de noms **Microsoft.Common** peuvent être utilisés dans basics. Cependant, certains éléments dans d’autres espaces de noms (comme **Microsoft.Compute.Credentials**) qui ne dépendent pas du contexte de l’utilisateur, sont toujours autorisés.

## <a name="steps"></a>Étapes
La propriété steps peut contenir zéro ou plusieurs des étapes supplémentaires à afficher après les principes de base, chacun contenant un ou plusieurs éléments. Vous pouvez ajouter des étapes par rôle ou niveau de l’application déployée. Par exemple, ajoutez une étape pour les entrées destinées aux nœuds principaux et une étape pour les nœuds de travail à un cluster.

## <a name="outputs"></a>Sorties
Le portail Azure utilise la propriété `outputs` pour mettre en correspondance des éléments issus de `basics` et `steps` avec les paramètres du modèle de déploiement Azure Resource Manager. Les clés de ce dictionnaire sont les noms des paramètres du modèle et les valeurs sont les propriétés des objets de sortie issues des éléments référencés.

## <a name="functions"></a>Fonctions
Semblable aux fonctions de modèle dans Azure Resource Manager (à la fois en termes de syntaxe et de fonctionnalité), CreateUiDefinition propose des fonctions permettant de travailler avec les entrées et sorties des éléments, ainsi que des fonctionnalités telles que des logiques conditionnelles.

## <a name="next-steps"></a>Étapes suivantes
Le fichier createUiDefinition.json proprement dit a un schéma simple. Sa profondeur réelle provient de tous les éléments et fonctions pris en charge. Ces éléments sont décrits plus en détail dans :

- [Éléments](create-uidefinition-elements.md)
- [Fonctions](create-uidefinition-functions.md)

Un schéma JSON actuel pour createUiDefinition est disponible ici : https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json. 

Les versions ultérieures seront disponibles au même emplacement. Remplacez la partie `0.1.2-preview` de l’URL et la valeur `version` par l’identificateur de version que vous souhaitez utiliser. Les identificateurs de version actuellement prise en charge sont `0.0.1-preview`, `0.1.0-preview`, `0.1.1-preview`, et `0.1.2-preview`.