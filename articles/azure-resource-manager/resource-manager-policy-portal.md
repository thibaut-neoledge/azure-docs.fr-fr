---
title: "Stratégies de ressources sur le portail Azure | Microsoft Docs"
description: "Explique comment utiliser le portail Azure pour créer et gérer des stratégies Resource Manager. Les stratégies peuvent être appliquées au niveau de l’abonnement ou des groupes de ressources."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/30/2017
ms.author: tomfitz
ms.openlocfilehash: 868b2cc1559053057d17b34c03e2e31347f399bf
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2017
---
# <a name="use-azure-portal-to-assign-and-manage-resource-policies"></a>Utiliser le portail Azure pour attribuer et gérer les stratégies de ressources
Le portail vous permet d’affecter des stratégies de ressource pour les abonnements et les groupes de ressources. L’interface utilisateur facilite la sélection de la stratégie que vous souhaitez affecter, puis la spécification des valeurs de paramètre pour cette stratégie afin de personnaliser les paramètres de stratégie. 

Pour affecter une stratégie via le portail, la définition de stratégie doit déjà exister dans votre abonnement. Votre abonnement a plusieurs définitions de stratégie intégrées qui sont prêtes à être affectées à des groupes de ressources ou des abonnements. Vous voyez ces stratégies intégrées et les stratégies personnalisées que vous avez définies lors de l’utilisation du portail pour affecter des stratégies. Pour une introduction aux stratégies et à la définition de stratégies personnalisées, consultez la page [Vue d’ensemble des stratégies de ressources](resource-manager-policy.md).

Toutes les ressources enfants héritent des stratégies. Ainsi, si une stratégie est appliquée à un groupe de ressources, elle est applicable à toutes les ressources appartenant à ce groupe de ressources. Dans cet article, le terme **étendue** fait référence au groupe de ressources ou à l’abonnement affecté à la stratégie. 

Les stratégies sont évaluées lors de la création et de la mise à jour des ressources (opérations PUT et PATCH).

## <a name="assign-a-policy"></a>Attribution d’une stratégie

1. Pour affecter une stratégie à un groupe de ressources ou un abonnement, sélectionnez ce groupe de ressources ou abonnement. Dans les paramètres, sélectionnez **Stratégies**.

   ![sélectionner des stratégies](./media/resource-manager-policy-portal/select-policies.png)

2. Pour créer une affectation de stratégie pour cette étendue, sélectionnez **Ajouter une affectation**.

   ![ajouter une affectation](./media/resource-manager-policy-portal/add-assignment.png)

3. Sélectionnez la stratégie que vous souhaitez affecter. Même si vous n’avez pas ajouté de définitions de stratégie à votre abonnement, vous voyez les stratégies intégrées qui sont disponibles pour affectation. Ces stratégies intégrées couvrent de nombreux scénarios courants.

   ![sélectionner une définition](./media/resource-manager-policy-portal/select-definition.png)

4. Après avoir sélectionné une stratégie, vous voyez une description de la stratégie et ses paramètres. Par exemple, l’illustration suivante montre le paramètre **Emplacements autorisés**, qui est requis pour la stratégie qui restreint les emplacements disponibles.

   ![afficher des paramètres](./media/resource-manager-policy-portal/show-parameters.png)

5. Via l’interface utilisateur, sélectionnez les valeurs à spécifier pour les paramètres de stratégie (par exemple, les emplacements qui peuvent être utilisés pour le déploiement).

   ![sélectionner les valeurs des paramètres](./media/resource-manager-policy-portal/select-parameters.png)

6. Fournissez des valeurs pour les autres paramètres. L’étendue est automatiquement affectée selon le panneau sélectionné lors du démarrage de l’affectation de stratégie. Cliquez sur **OK** quand vous avez terminé.

   ![définir les paramètres](./media/resource-manager-policy-portal/define-parameters.png)

  Vous avez affecté la stratégie à l’étendue spécifiée.

## <a name="view-policy-assignments"></a>Afficher les affectations de stratégies

Après avoir attribué une stratégie, elle s’affiche dans la liste des stratégies pour cette étendue. L’onglet **Détails** affiche un résumé de l’affectation de stratégie.

![afficher les détails](./media/resource-manager-policy-portal/show-details.png)

L’onglet **Règle d’affectation** affiche le code JSON pour la définition de stratégie.

![afficher une règle d’affectation](./media/resource-manager-policy-portal/show-assignment-rule.png)

## <a name="change-an-existing-policy-assignment"></a>Modifier une affectation de stratégie existante

Pour modifier une stratégie, sélectionnez **Modifier l’affectation** ou **Supprimer**

![modifier ou supprimer une affectation](./media/resource-manager-policy-portal/edit-delete-policy.png)

## <a name="assign-custom-policies"></a>Affecter des stratégies personnalisées

Si vous avez défini des stratégies personnalisées dans votre abonnement, ces stratégies sont disponibles pour affectation via le portail. Ces stratégies ont le préfixe **[Custom]**

![stratégies personnalisées](./media/resource-manager-policy-portal/show-custom-policy.png)

## <a name="next-steps"></a>Étapes suivantes
* Pour en savoir plus sur la syntaxe JSON pour définir des stratégies, consultez la [Vue d’ensemble des stratégies de ressources](resource-manager-policy.md).
* Pour obtenir des conseils sur l’utilisation de Resource Manager par les entreprises pour gérer efficacement les abonnements, voir [Structure d’Azure Enterprise - Gouvernance normative de l’abonnement](resource-manager-subscription-governance.md).
* Le schéma de stratégie est publié à l’adresse [http://schema.management.azure.com/schemas/2015-10-01-preview/policyDefinition.json](http://schema.management.azure.com/schemas/2015-10-01-preview/policyDefinition.json). 

