---
title: "Vue d’ensemble d’Azure Policy | Microsoft Docs"
description: "Azure Policy est un service dans Azure, que vous utilisez pour créer, affecter et gérer les définitions de stratégie dans votre environnement Azure."
services: azure-policy
keywords: 
author: Jim-Parker
ms.author: jimpark; nini
ms.date: 10/06/2017
ms.topic: overview
ms.service: azure-policy
manager: jochan
ms.custom: mvc
ms.openlocfilehash: 01b0915cdf37ddc00a4e6a38c99ce7f6f8c4f9c9
ms.sourcegitcommit: 51ea178c8205726e8772f8c6f53637b0d43259c6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="what-is-azure-policy"></a>Présentation d’Azure Policy

La gouvernance informatique fait clairement la distinction entre les objectifs métier et les projets informatiques. Une bonne gouvernance informatique implique la planification de vos initiatives et la définition de priorités à un niveau stratégique. Si votre entreprise connaît un nombre important de problèmes informatiques qui semblent ne jamais être résolus, l’implémentation de stratégies vous aide à mieux les gérer et les empêcher. C’est ici qu’Azure Policy entre en jeu.

Azure Policy est un service dans Azure, que vous utilisez pour créer, affecter et gérer des définitions de stratégie. Les définitions de stratégie appliquent différentes règles et actions sur vos ressources, qui restent donc conformes aux standards et aux contrats de niveau de service de l’entreprise. Pour cela, il effectue une évaluation de vos ressources et recherche celles qui ne sont pas conformes aux définitions de stratégie en place.

## <a name="policy-definition"></a>Définition de stratégie

Chaque définition de stratégie a des conditions d’application et une action associée, qui est effectuée si les conditions sont remplies.

Dans Azure Policy, nous offrons quelques stratégies intégrées qui sont disponibles par défaut. Par exemple :

- **Nécessitent SQL Server 12.0** : cette définition de stratégie a des conditions/règles garantissant que tous les serveurs SQL Server utilisent la version 12.0. Son action consiste à refuser tous les serveurs qui ne répondent pas à ces critères.
- **Références (SKU) de compte de stockage autorisées**: cette définition de stratégie a un ensemble de conditions/règles qui déterminent si un compte de stockage qui est déployé est dans un ensemble de tailles de référence (SKU). Son action consiste à refuser tous les serveurs dont la taille ne fait pas partie de l’ensemble de tailles de références (SKU) définies.
- **Type de ressource autorisé**: cette définition de stratégie a un ensemble de conditions/règles pour spécifier les types de ressources que votre organisation peut déployer. Son action consiste à refuser toutes les ressources qui ne font pas partie de cette liste définie.

Pour plus d’informations sur les structures des définitions de stratégie, consultez l’article [Structure des définitions de stratégie](../azure-resource-manager/resource-manager-policy.md#policy-definition-structure).

## <a name="policy-assignment"></a>Affectation de rôle
Une affectation de stratégie est une définition de stratégie qui a été affectée avec une étendue spécifique. Cette étendue peut aller d’un groupe d’administration à un groupe de ressources.

Pour plus d’informations sur les définitions et les affectations de stratégie, consultez [Vue d’ensemble des stratégies de ressources](../azure-resource-manager/resource-manager-policy.md).

## <a name="policy-parameters"></a>Paramètres de stratégie
Les paramètres de stratégie permettent de simplifier la gestion des stratégies en réduisant le nombre de définitions de stratégies que vous devez créer. Vous pouvez définir des paramètres lors de la création d’une définition de stratégie, pour la rendre plus générique. Vous pouvez ensuite réutiliser cette définition de stratégie pour différents scénarios en passant des valeurs différentes (vous spécifiez par exemple un ensemble d’emplacements pour un abonnement) au moment de l’affectation de la stratégie.

Les paramètres sont définis/créés lors de la création d’une définition de stratégie. Quand un paramètre est défini, un nom lui est affecté et éventuellement une valeur. Par exemple, vous pouvez définir un paramètre d’emplacement pour une stratégie, puis lui donner des valeurs différentes, comme *EastUS* ou *WestUS* lors de l’affectation d’une stratégie.

Pour plus d’informations sur les paramètres de stratégie, consultez [Vue d’ensemble des stratégies de ressources - Paramètres](../azure-resource-manager/resource-manager-policy.md#parameters).

## <a name="initiative-definition"></a>Définition d’initiative
Une définition d’initiative est une collection de définitions de stratégie qui sont spécialement conçues pour atteindre un objectif global particulier. Par exemple, vous pouvez créer une initiative intitulée **Activer la surveillance dans Azure Security Center**, avec comme objectif de surveiller toutes les recommandations de sécurité disponibles dans votre centre Azure Security Center.

Dans cette initiative, vous avez par exemple des définitions de stratégie comme celles-ci :

1. **Surveiller les bases de données non chiffrées dans Security Center** : pour surveiller les bases de données et les serveurs SQL Server non chiffrés.
2. **Surveiller les vulnérabilités du système d’exploitation dans Security Center** : pour surveiller les serveurs qui ne répondent pas à la base de référence configurée.
3. **Surveiller l’absence d’Endpoint Protection dans Security Center** : pour surveiller les serveurs où un agent Endpoint Protection n’est pas installé.

## <a name="initiative-assignment"></a>Affectation d’initiative
Comme une affectation de stratégie, une affectation d’initiative est une définition d’initiative affectée à une étendue spécifique. Les affectations d’initiative réduisent la nécessité de créer plusieurs définitions d’initiative pour chaque étendue. Cette étendue peut également aller d’un groupe d’administration à un groupe de ressources.

Dans l’exemple précédent, l’initiative **Activer la surveillance dans Azure Security Center** peut être affectée à différentes étendues. Par exemple, une affectation peut être faite à **AbonnementA** et une autre à **AbonnementB**.

## <a name="initiative-parameters"></a>Paramètres d’initiative
Comme les paramètres de stratégie, les paramètres d’initiative permettent de simplifier la gestion en réduisant la redondance. Les paramètres d’initiative sont essentiellement la liste des paramètres utilisés par les définitions de stratégie dans l’initiative.

Par exemple, prenons un scénario où vous avez une définition d’initiative, **initiativeC**, avec deux définitions de stratégie. Chaque définition de stratégie a un paramètre défini :

|Stratégie  |nom du paramètre     |Type de paramètre  |Remarque                                                                                                |
|--------|----------------------|-------|----------------------------------------------------------------------------------------------------------------|
|policyA |allowedLocations      |array  |Ce paramètre attend une liste de chaînes pour une valeur, le type de paramètre ayant été défini comme tableau |
|policyB |allowedSingleLocation |string |Ce paramètre attend un mot pour une valeur, le type de paramètre ayant été défini comme chaîne          |

Dans ce scénario, quand vous définissez les paramètres d’initiative pour **initiativeC**, vous avec trois options :

1. Tirer parti des paramètres des définitions de stratégie dans cette initiative : dans cet exemple, *allowedLocations* et *allowedSingleLocation* deviennent des paramètres d’initiative pour  **initiativeC**.
2. Fournir des valeurs pour les paramètres des définitions de stratégie dans la définition de cette initiative. Dans cet exemple, vous pouvez fournir une liste d’emplacements au **paramètre de policyA, allowedLocations** et au **paramètre de policyB, allowedSingleLocation**.
Vous pouvez également fournir des valeurs lors de l’affectation de cette initiative.
3. Fournir une liste d’options de *valeurs* qui peuvent être utilisées lors de l’affectation de cette initiative. Cela signifie que, quand vous affectez cette initiative, les paramètres hérités des définitions de stratégie dans l’initiative peuvent avoir seulement des valeurs de la liste fournie.

Par exemple, si la liste des options de valeurs fournie lors de la création de la définition d’initiative contient *EastUS*, *WestUS*, *CentralUS* et *WestEurope* , vous ne pouvez pas entrer une valeur différente, comme *Asie du Sud-est*, lors de l’affectation d’initiative, car elle ne fait pas partie de la liste.

## <a name="recommendations-for-managing-policies"></a>Recommandations pour la gestion des stratégies

Voici quelques conseils que nous vous recommandons de suivre lors de la création et de la gestion des définitions et des affectations de stratégie :

- Si vous créez des définitions de stratégie dans votre environnement, nous recommandons de commencer avec un effet d’audit, et non pas un effet de refus, de façon à conserver un suivi de l’impact de votre définition de stratégie dans votre environnement. Si vous avez déjà des scripts en place pour mettre à l’échelle automatiquement vos applications, la définition d’un effet de refus peut entraver ces tâches d’automatisation que vous avez déjà en place.
- Il est important de garder à l’esprit les hiérarchies de l’organisation lors de la création de définitions et d’affectations. Nous recommandons de créer les définitions à un niveau plus élevé, par exemple au niveau du groupe d’administration ou de l’abonnement, et d’affecter au niveau enfant suivant. Par exemple, si vous créez une définition de stratégie au niveau du groupe d’administration, une affectation de stratégie de cette définition peut être limitée au niveau d’un abonnement.
- Nous encourageons l’utilisation d’un niveau tarifaire standard, de façon à mieux comprendre l’état de conformité de votre environnement.
- Nous recommandons de toujours utiliser des définitions d’initiative au lieu de définitions de stratégie, même si vous n’avez qu’une seule stratégie à l’esprit. Par exemple, si vous avez une définition de stratégie *policyDefA* et que vous la créez sous la définition d’initiative *initiativeDefC*, si vous décidez de créer une autre définition de stratégie avec des objectifs similaires à ceux de *policyDefA*, vous pouvez simplement l’ajouter sous *initiativeDefC* et mieux les suivre de cette façon.

   N’oubliez pas qu’une fois que vous avez créé une affectation d’initiative à partir d’une définition d’initiative, toutes les nouvelles définitions de stratégie ajoutées à la définition d’initiative sont automatiquement ajoutées sous la ou les affectations d’initiative sous cette définition d’initiative. Cependant, si un nouveau paramètre est introduit dans la nouvelle définition de stratégie, vous devez mettre à jour la définition et les affectations de l’initiative afin de refléter ceci en modifiant la définition ou l’affectation.

## <a name="next-steps"></a>Étapes suivantes :
Maintenant que vous avez une vue d’ensemble d’Azure Policy et des autres concepts clés que nous avons introduits, voici les étapes suivantes que nous suggérons :

- [Affecter une définition de stratégie](./assign-policy-definition.md)
- [Affecter une définition de stratégie avec Azure CLI](./assign-policy-definition-cli.md)
- [Affecter une définition de stratégie avec PowerShell](./assign-policy-definition-ps.md)
