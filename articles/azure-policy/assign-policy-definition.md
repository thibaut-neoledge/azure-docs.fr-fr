---
title: "Créer une affectation de stratégie pour identifier les ressources non conformes dans votre environnement Azure | Microsoft Docs"
description: "Cet article vous guide à travers les étapes de création d’une définition de stratégie pour identifier les ressources non conformes."
services: azure-policy
keywords: 
author: Jim-Parker
ms.author: jimpark
ms.date: 10/06/2017
ms.topic: quickstart
ms.service: azure-policy
ms.custom: mvc
ms.openlocfilehash: 2e0962ae02dd8132d878792634abc1f63b2c29a1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-policy-assignment-to-identify-non-compliant-resources-in-your-azure-environment"></a>Créer une affectation de stratégie pour identifier les ressources non conformes dans votre environnement Azure
La première étape pour comprendre la conformité dans Azure est de savoir où vous en êtes avec vos propres ressources actuelles. Ce démarrage rapide vous guide pas à pas dans le processus de création d’une stratégie d’affectation pour identifier les ressources qui n’utilisent pas SQL Server version 12.0. À la fin de ce processus, vous aurez identifié correctement les serveurs avec une version différente, qui sont par conséquent *non conformes*.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="opt-in-to-azure-policy"></a>Choisir Azure Policy

Azure Policy est désormais disponible en préversion limitée : vous devez donc vous inscrire pour en demander l’accès.

1. Accédez à Azure Policy sur https://aka.ms/getpolicy et sélectionnez **S’inscrire** dans le volet gauche.

   ![Rechercher une stratégie](media/assign-policy-definition/sign-up.png)

2. Choisissez Azure Policy en sélectionnant les abonnements que vous voulez utiliser dans la liste **Abonnement**. Sélectionnez ensuite **Inscription**.

   ![Accepter l’utilisation d’Azure Policy](media/assign-policy-definition/preview-opt-in.png)

   Quelques jours peuvent nous être nécessaires pour accepter votre demande d’inscription, en fonction de la demande. Une fois que votre demande est acceptée, vous êtes averti par e-mail que vous pouvez commencer à utiliser le service.

## <a name="create-a-policy-assignment"></a>Créer une affectation de stratégie

Dans ce démarrage rapide, nous créons une affectation de stratégie et affectons la définition *Require SQL Server version 12.0*. 

1. Sélectionnez **Affectations** dans le volet gauche de la page Azure Policy.
2. Sélectionnez **Assigner une stratégie** en haut du volet **Affectations**.

   ![Affecter une définition de stratégie](media/assign-policy-definition/select-assign-policy.png)

3. Dans la page **Assigner une stratégie**, cliquez sur le ![bouton de définition de stratégie](media/assign-policy-definition/definitions-button.png) situé en regard du champ **Stratégie** pour afficher la liste des définitions disponibles.

   ![Afficher les définitions de stratégie disponibles](media/assign-policy-definition/open-policy-definitions.png)

   Azure Policy est fourni avec des définitions de stratégie intégrées que vous pouvez utiliser. Vous y trouverez des définitions de stratégie intégrées comme :

   - Enforce tag and its value
   - Apply tag and its value
   - Require SQL Server version 12.0

4. Recherchez dans vos définitions de stratégie la définition de stratégie *Require SQL Server version 12.0*. Cliquez sur cette stratégie, puis sur **Sélectionner**.

   ![Rechercher la définition de stratégie appropriée](media/assign-policy-definition/select-available-definition.png)

5. Attribuez un **Nom** d’affichage à l’affectation de stratégie. Dans le cas présent, utilisons *Require SQL Server version 12.0*. Vous pouvez également ajouter une **Description** (facultatif). La description fournit des détails sur la façon dont cette affectation de stratégie s’assure que tous les serveurs SQL créés dans cet environnement utilisent la version 12.0.
6. Définissez le niveau tarifaire sur **Standard** pour vous assurer que la stratégie soit appliquée aux ressources existantes.

   Il existe deux niveaux tarifaires dans Azure Policy : *Gratuit* et *Standard*. Avec le niveau Gratuit, vous pouvez uniquement appliquer des stratégies à des ressources futures, alors qu’avec le niveau Standard, vous pouvez également les appliquer à des ressources existantes pour mieux comprendre votre état de conformité. Étant donné qu’il s’agit d’une préversion limitée, nous n'avons pas encore publié de modèle tarifaire. Vous ne recevrez donc pas de facture pour la sélection du niveau *Standard*. Pour en savoir plus sur les prix, consultez : [Prix Azure Policy](https://acom-milestone-ignite.azurewebsites.net/pricing/details/azure-policy/).

7. Sélectionnez l’**étendue** d’application souhaitée de la stratégie.  Une étendue détermine les ressources ou le regroupement de ressources sur lequel la stratégie est appliquée. Elle va d’un abonnement à des groupes de ressources.
8. Sélectionnez l’abonnement (ou le groupe de ressources) enregistré précédemment quand vous avez choisi Azure Policy. Dans cet exemple, nous utilisons l’abonnement suivant - **Azure Analytics Capacity Dev**, mais vos options peuvent différer.

   ![Rechercher la définition de stratégie appropriée](media/assign-policy-definition/assign-policy.png)

9. Sélectionnez **Attribuer**.

Vous êtes maintenant prêt à identifier les ressources non conformes pour comprendre l’état de conformité de votre environnement.

## <a name="identify-non-compliant-resources"></a>Identifier les ressources non conformes

Sélectionnez **Conformité** dans le volet gauche, puis recherchez l’affectation de stratégie que vous avez créée.

![Conformité à la stratégie](media/assign-policy-definition/policy-compliance.png)

Si des ressources existantes ne sont pas conformes à cette nouvelle affectation, elles apparaissent sous l’onglet **Ressources non conformes**.

Si une condition est évaluée sur vos ressources existantes et génère la valeur true pour certaines d'entre elles, ces ressources sont marquées comme non conformes à la stratégie. Le tableau suivant montre le fonctionnement des différentes actions actuellement disponibles avec le résultat de l’évaluation de conditions et l’état de conformité de vos ressources.

|Ressource  |Si la condition dans la stratégie a la valeur  |Action dans la stratégie   |État de conformité  |
|-----------|---------|---------|---------|
|Exists     |true     |REFUSER     |Non conforme |
|Exists     |False    |REFUSER     |Conforme     |
|Exists     |true     |Append   |Non conforme |
|Exists     |False    |Append   |Conforme     |
|Exists     |true     |Audit    |Non conforme |
|Exists     |False    |Audit    |Non conforme |

## <a name="clean-up-resources"></a>Supprimer des ressources

D’autres guides de cette collection sont basés sur ce démarrage rapide. Si vous prévoyez de continuer avec les didacticiels suivants, ne nettoyez pas les ressources créées dans ce démarrage rapide. Sinon, procédez comme suit pour supprimer toutes les ressources créées au cours de ce démarrage rapide dans le portail Azure.
1. Sélectionnez **Affectations** dans le volet gauche.
2. Recherchez l’affectation que vous venez de créer.

   ![Supprimer une affectation](media/assign-policy-definition/delete-assignment.png)

3.  Sélectionnez **Supprimer l’affectation**.

## <a name="next-steps"></a>Étapes suivantes

Dans ce démarrage rapide, vous avez affecté une définition de stratégie à une étendue pour vous assurer que toutes les ressources incluses dans cette étendue soient compatibles et pour identifier celles qui ne le sont pas.

Pour plus d’informations sur l’affectation de stratégies et garantir que les ressources **futures** qui sont créées sont conformes, continuez avec le didacticiel suivant :

> [!div class="nextstepaction"]
> [Création et gestion des stratégies](./create-manage-policy.md)

