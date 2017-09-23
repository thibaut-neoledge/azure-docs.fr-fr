---
title: "Solutions d’Operations Management Suite (OMS) | Microsoft Docs"
description: "Les solutions étendent les fonctionnalités OMS (Operations Management Suite) en fournissant des scénarios de gestion empaquetés que les clients peuvent ajouter à leur espace de travail OMS.  Cet article explique comment les solutions personnalisées sont créées par les clients et partenaires."
services: operations-management-suite
documentationcenter: 
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 1f054a4e-6243-4a66-a62a-0031adb750d8
ms.service: operations-management-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/01/2017
ms.author: bwren
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 424d8654a047a28ef6e32b73952cf98d28547f4f
ms.openlocfilehash: 2443dd73fdf441721bd6f6f340da515d9f5a22a2
ms.contentlocale: fr-fr
ms.lasthandoff: 03/22/2017

---
# <a name="working-with-management-solutions-in-operations-management-suite-oms-preview"></a>Utilisation de solutions de gestion dans Operations Management Suite (OMS) (préversion)
> [!NOTE]
> Il s’agit d’une documentation préliminaire des solutions de gestion d’OMS qui sont actuellement en version préliminaire.    
> 
> 

Les solutions de gestion étendent les fonctionnalités d’Operations Management Suite (OMS) en offrant des scénarios de gestion empaquetés que les clients peuvent ajouter à leur environnement.  En plus des [solutions proposées par Microsoft](../log-analytics/log-analytics-add-solutions.md), les partenaires et les clients peuvent créer des solutions de gestion utilisables dans leur propre environnement ou mises à la disposition des clients par le biais de la communauté.

## <a name="finding-and-installing-management-solutions"></a>Recherche et installation de solutions de gestion
Il existe plusieurs méthodes de localisation et d’installation de solutions de gestion, qui sont décrites dans les sections suivantes.

### <a name="azure-marketplace"></a>Azure Marketplace
Les solutions de gestion fournies par Microsoft et les partenaires approuvés peuvent être installées à partir de la Place de marché Azure, dans le Portail Azure.

1. Connectez-vous au portail Azure.
2. Dans le volet gauche, sélectionnez **Plus de services**.
3. Faites défiler la liste jusqu’à **Solutions**, ou bien tapez *solutions* dans la boîte de dialogue **Filtre**.
4. Cliquez sur le bouton **+ Ajouter** .
5. Recherchez les solutions qui vous intéressent en parcourant la liste, en cliquant sur le bouton **Filtre**, ou encore en tapant dans la boîte **Rechercher partout**.
6. Cliquez sur un élément de la Place de marché pour afficher ses informations détaillées.
7. Cliquez sur **Créer** pour ouvrir le volet **Ajouter une solution**.
8. Vous êtes invité à entrer les informations requises, notamment [l’espace de travail OMS et le compte Automation](#oms-workspace-and-automation-account), en plus des valeurs des paramètres de la solution.
9. Cliquez sur **Créer** pour installer la solution.

### <a name="oms-portal"></a>Portail OMS
Les solutions de gestion fournies par Microsoft peuvent être installées à partir de la galerie de solutions dans le portail OMS.

1. Connectez-vous au portail OMS.
2. Cliquez sur la vignette **Galerie de solutions**.
3. Vous pouvez en savoir plus sur chaque solution disponible en consultant la page Galerie des solutions d'OMS. Cliquez sur le nom de la solution que vous souhaitez ajouter à OMS.
4. Des informations détaillées sur la solution que vous avez choisie s'affichent dans la page de la solution en question. Cliquez sur **Add**.
5. Une nouvelle vignette représentant la solution que vous avez ajoutée apparaît dans la page d'aperçu dans OMS. Vous pouvez commencer à l'utiliser une fois que le service OMS a traité vos données.

### <a name="azure-quickstart-templates"></a>Modèles de démarrage rapide Microsoft Azure
Les membres de la communauté peuvent soumettre des solutions de gestion aux modèles de démarrage rapide Azure.  Vous pouvez télécharger ces modèles pour une installation ultérieure ou les inspecter pour apprendre à [créer vos propres solutions](#creating-a-solution).

1. Suivez la procédure décrite dans [Espace de travail OMS et compte Automation](#oms-workspace-and-automation-account) pour lier un compte et un espace de travail.
2. Accédez à [Modèles de démarrage rapide Azure](https://azure.microsoft.com/documentation/templates/).  
3. Recherchez une solution qui vous intéresse.
4. Sélectionnez la solution dans les résultats pour en afficher les détails.
5. Cliquez sur le bouton **Déployer dans Azure**.
6. Vous êtes invité à fournir des informations comme le groupe de ressources et l’emplacement, en plus des valeurs des paramètres de la solution.
7. Cliquez sur **Achat** pour installer la solution.

### <a name="deploy-azure-resource-manager-template"></a>Déployer un modèle Azure Resource Manager
Les solutions que vous obtenez dans la communauté ou que vous [créez vous-même](#creating-a-solution) sont implémentées comme modèle Resource Manager ; vous pouvez utiliser toutes les méthodes standards de [déploiement d’un modèle](../azure-resource-manager/resource-group-template-deploy-portal.md).  Notez que vous devez créer et lier [l’espace de travail OMS et le compte Automation](#oms-workspace-and-automation-account) avant d’installer la solution.

## <a name="oms-workspace-and-automation-account"></a>Espace de travail OMS et compte Automation
La plupart des solutions de gestion nécessitent un [espace de travail OMS](../log-analytics/log-analytics-manage-access.md) qui contient les vues et un [compte Automation](../automation/automation-security-overview.md#automation-account-overview) qui contient les Runbooks et les ressources associées. L’espace de travail et le compte doivent répondre aux exigences suivantes.

* Une solution ne peut utiliser qu’un seul espace de travail OMS et un seul compte Automation.  
* L’espace de travail OMS et le compte Automation utilisés par une solution doivent être liés l’un à l’autre. Un espace de travail OMS ne peut être lié qu’à un seul compte Automation, et un compte Automation ne peut être lié qu’à un seul espace de travail OMS.
* Pour être liés, l’espace de travail OMS et le compte Automation doivent se trouver dans le même groupe de ressources et la même région.  La configuration suivante fait figure d’exception : un espace de travail OMS dans la région États-Unis de l’Est et un compte Automation dans États-Unis de l’Est 2 .

### <a name="creating-a-link-between-an-oms-workspace-and-automation-account"></a>Création d’un lien entre un espace de travail OMS et un compte Automation
Le mode de spécification de l’espace de travail OMS et du compte Automation dépend de la méthode d’installation de votre solution.

* Lorsque vous installez une solution Microsoft avec le portail OMS, elle est installée dans l’espace de travail OMS actif ; aucun compte Automation n’est requis.
* Lorsque vous installez une solution sur la Place de marché Azure, vous êtes invité à choisir un espace de travail OMS et un compte Automation ; un lien est créé pour vous entre les deux.  
* Pour les solutions extérieures à la Place de marché Azure, vous devez lier l’espace de travail OMS et le compte Automation avant d’installer la solution.  Pour ce faire, vous pouvez sélectionner n’importe quelle solution de la Place de marché Azure et choisir l’espace de travail OMS et le compte Automation.  Vous n’êtes pas obligé d’installer effectivement la solution, car le lien est créé dès que l’espace de travail OMS et le compte Automation sont sélectionnés.  Une fois le lien créé, vous pouvez utiliser cet espace de travail OMS et ce compte Automation pour toutes les solutions. 

### <a name="verifying-the-link-between-an-oms-workspace-and-automation-account"></a>Vérification du lien entre un espace de travail OMS et un compte Automation
Vous pouvez vérifier le lien entre un espace de travail OMS et un compte Automation à l’aide de la procédure suivante.

1. Sélectionnez le compte Automation dans le Portail Azure.
2. Faites défiler le volet **Paramètres** jusqu’en bas.
3. S’il existe une section appelée **Ressources OMS** dans le volet **Paramètres**, ce compte est associé à un espace de travail OMS.
4. Sélectionnez **Espace de travail** pour afficher les détails de l’espace de travail OMS lié à ce compte Automation.

## <a name="listing-management-solutions"></a>Liste des solutions de gestion
Utilisez la procédure suivante pour afficher les solutions de gestion des espaces de travail liés à votre abonnement Azure.

1. Connectez-vous au portail Azure.
2. Dans le volet gauche, sélectionnez **Plus de services**.
3. Faites défiler la liste jusqu’à **Solutions**, ou bien tapez *solutions* dans la boîte de dialogue **Filtre**.
4. Les solutions installées dans tous vos espaces de travail sont listées.

Notez que vous ne pouvez consulter que les solutions Microsoft installées dans l’espace de travail actif à l’aide du portail OMS.

## <a name="removing-a-management-solution"></a>Suppression d’une solution de gestion
Lorsqu’une solution de gestion est supprimée, toutes les ressources de la solution sont également supprimées.  

1. Recherchez la solution dans le Portail Azure à l’aide de la procédure décrite dans [Liste des solutions](#listing-solutions).
2. Sélectionnez la solution que vous souhaitez supprimer.
3. Cliquez sur le bouton **Supprimer** .

## <a name="creating-a-management-solution"></a>Création d’une solution de gestion
Une aide complète à la création de solutions de gestion est disponible dans [Création de solutions dans Operations Management Suite (OMS)](operations-management-suite-solutions-creating.md). 

## <a name="next-steps"></a>Étapes suivantes
* Dans [Modèles de démarrage rapide Azure](https://azure.microsoft.com/documentation/templates), recherchez des exemples de modèles Resource Manager.
* Créez vos propres [solutions de gestion](operations-management-suite-solutions-creating.md).


