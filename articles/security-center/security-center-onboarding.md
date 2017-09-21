---
title: "Intégration d’Azure Security Center Standard pour une sécurité renforcée | Microsoft Docs"
description: " Apprenez à intégrer Azure Security Center Standard pour une sécurité renforcée. "
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 411d7bae-c9d4-4e83-be63-9f2f2312b075
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/14/2017
ms.author: terrylan
ms.translationtype: HT
ms.sourcegitcommit: d24c6777cc6922d5d0d9519e720962e1026b1096
ms.openlocfilehash: a6394b1b02ebfe518dc2f2b7f65eb677bb0ba5f2
ms.contentlocale: fr-fr
ms.lasthandoff: 09/14/2017

---
# <a name="onboarding-to-azure-security-center-standard-for-enhanced-security"></a>Intégration d’Azure Security Center Standard pour une sécurité renforcée
Effectuez la mise à niveau vers Security Center Standard pour tirer profit d’une gestion de la sécurité et d’une protection contre les menaces renforcées pour vos charges de travail cloud hybrides.  Vous pouvez essayer gratuitement le niveau Standard pendant 60 jours. Pour plus d’informations, consultez la [page de tarification](https://azure.microsoft.com/pricing/details/security-center/) de Security Center.

Security Center Standard inclut :

- **Sécurité hybride** : obtenez une vue unifiée de la sécurité sur l’ensemble de vos charges de travail cloud et locales. Appliquez des stratégies de sécurité et évaluez en continu la sécurité de vos charges de travail cloud hybrides pour garantir la conformité aux normes de sécurité. Collectez, recherchez et analysez les données de sécurité à partir d’un large éventail de sources, dont les pare-feu et d’autres solutions partenaires.
- **Détection avancée des menaces** : utilisez l’analytique avancée et Microsoft Intelligent Security Graph pour avoir un avantage sur les cyberattaques en constante évolution.  Tirez parti des analytiques comportementales intégrées et de l’apprentissage machine pour identifier les attaques et les vulnérabilités zero-day. Surveillez les réseaux, machines et services cloud pour prévenir les attaques entrantes et les activités consécutives à une violation. Simplifiez l’investigation avec des outils interactifs et des informations sur les menaces contextuelles.
- **Contrôles d’accès et d’application** : bloquez les programmes malveillants et les autres applications indésirables en appliquant des recommandations de mise en liste verte adaptées à vos charges de travail spécifiques et alimentées par l’apprentissage machine. Réduisez la surface exposée aux attaques du réseau avec l’accès contrôlé juste à temps aux ports de gestion sur les machines virtuelles Azure, ce qui diminue de façon significative l’exposition aux attaques par force brute et autres attaques réseau.

## <a name="detecting-unprotected-resources"></a>Détection des ressources non protégées     
Security Center détecte automatiquement les abonnements Azure ou les espaces de travail dans lesquels Security Center Standard n’est pas activé. Cela inclut les abonnements Azure utilisant Security Center Gratuit et les espaces de travail dans lesquels la solution de sécurité n’est pas activée.

Vous pouvez mettre à niveau un abonnement Azure entier vers le niveau Standard, qui est hérité par toutes les ressources dans l’abonnement, ou vous pouvez définir une stratégie unique pour mettre à niveau un groupe de ressources spécifique uniquement. Si les paramètres de la stratégie du groupe de ressources sont uniques, Security Center ne remplacera pas les stratégies de tarification suite à la mise à niveau de l’abonnement vers le niveau Standard. L’application du niveau Standard à un abonnement concerne uniquement les machines virtuelles de l’abonnement associées aux espaces de travail créés par Security Center. L’application du niveau Standard à l’espace de travail concerne toutes les ressources associées à l’espace de travail.

> [!NOTE]
> Vous pouvez gérer vos coûts et limiter la quantité de données collectées pour une solution en limitant celle-ci à un ensemble spécifique d’agents. Le [ciblage de solution](../operations-management-suite/operations-management-suite-solution-targeting.md) vous permet d’appliquer une étendue à la solution et de cibler un sous-ensemble d’ordinateurs dans l’espace de travail.  Si vous utilisez le ciblage de solution, Security Center répertorie l’espace de travail comme n’ayant pas de solution.
>
>

## <a name="upgrade-an-azure-subscription"></a>Mettre à niveau un abonnement Azure
Pour mettre à niveau tous les abonnements vers le niveau Standard :
1. Dans le menu principal de Security Center, sélectionnez **Intégration**.
2. Dans **Intégration de la sécurité avancée**, Security Center répertorie les abonnements éligibles pour l’intégration. Vous pouvez mettre à niveau tous les abonnements répertoriés en sélectionnant **Appliquer le plan Standard**.

  ![Mettre à niveau tous les abonnements][1]

Pour mettre à niveau un abonnement individuel vers le niveau Standard : vous pouvez mettre à niveau un abonnement à partir de **Intégration** en sélectionnant **Appliquer le niveau Standard**. Pour mettre à niveau un groupe de ressources dans l’abonnement vers le niveau Standard, sélectionnez l’abonnement :
1. Sélectionnez un abonnement.  **Stratégie de sécurité** fournit des informations sur le groupe de ressources contenu dans l’abonnement.
2. Sélectionnez l’abonnement ou un groupe de ressources.

  ![Mettre à niveau tous les abonnements][2]

3. Sélectionnez **Standard** pour effectuer la mise à niveau du niveau Gratuit vers le niveau Standard.
4. Sélectionnez **Enregistrer**.

> [!NOTE]
> La mise à niveau d’un abonnement vers le niveau Standard activera [l’approvisionnement automatique](security-center-enable-data-collection.md) s’il a été précédemment désactivé. Nous vous recommandons d’activer l’approvisionnement automatique pour les agents d’analyse.
>
>

## <a name="upgrade-a-workspace"></a>Mettre à niveau un espace de travail
L’application du niveau Standard à l’espace de travail concerne toutes les ressources associées à l’espace de travail.

1. Revenez au panneau **Intégration**.
2. Sélectionnez un espace de travail.

  ![Mettre à niveau un espace de travail][8]

3. Sélectionnez **Standard** pour effectuer la mise à niveau.  
4. Sélectionnez **Enregistrer**.

   > [!NOTE]
   > Il existe un scénario dans lequel le niveau Gratuit ou Standard peut ne pas être appliqué à votre espace de travail. Si vous sélectionnez le niveau Gratuit, les fonctionnalités du niveau Gratuit de Security Center sont appliquées à vos machines virtuelles Azure uniquement. Les fonctionnalités du niveau Gratuit ne sont pas appliquées aux ordinateurs autres qu’Azure. Si vous sélectionnez le niveau Standard, les fonctionnalités du niveau Standard sont appliquées à toutes les machines virtuelles Azure et à tous les ordinateurs autres qu’Azure associés à l’espace de travail. Nous vous conseillons d’appliquer le niveau Standard pour offrir une sécurité avancée à vos ressources Azure et autres qu’Azure.
   >
   >

## <a name="onboard-non-azure-computers"></a>Intégrer des ordinateurs autres qu’Azure
Security Center peut surveiller l’état de sécurité de vos ordinateurs autres qu’Azure, mais vous devez d’abord intégrer ces ressources. Vous pouvez ajouter des ordinateurs autres qu’Azure à partir du panneau **Intégration** ou du panneau **Calcul**. Nous allons examiner les deux méthodes.

### <a name="add-new-non-azure-computers-from-onboarding"></a>Ajouter des ordinateurs autres qu’Azure à partir du panneau Intégration

1. Revenez au panneau **Intégration**.   
2. Sélectionnez **Do you want to add new non-Azure computers** (Souhaitez-vous ajouter de nouveaux ordinateurs autres qu’Azure).

  ![Ajouter un ordinateur autre qu’Azure][3]

Si vous avez des espaces de travail existants, ils sont répertoriés dans **Add new Non-Azure computers** (Ajouter de nouveaux ordinateurs autres qu’Azure). Vous pouvez ajouter des ordinateurs à un espace de travail existant ou créer un espace de travail. Pour créer un espace de travail, sélectionnez le lien **add a new workspace** (ajouter un nouvel espace de travail).

Nous allons examiner les deux méthodes :

- Créer un espace de travail et ajouter un ordinateur
- Sélectionner un espace de travail existant et ajouter un ordinateur

**Créer un espace de travail et ajouter un ordinateur**

1. Dans **Add new Non-Azure computers** (Ajouter de nouveaux ordinateurs autres qu’Azure), sélectionnez **add a new workspace** (ajouter un nouvel espace de travail).

   ![Ajouter un espace de travail][4]

2. Dans **Security and Audit**, sélectionnez **Espace de travail OMS** pour créer un espace de travail.
3. Dans **Espace de travail OMS**, entrez les informations de votre espace de travail.
4. Dans **Espace de travail OMS**, sélectionnez **OK**.  Une fois que vous sélectionnez OK, vous obtenez un lien pour télécharger un agent Windows ou Linux et des clés pour votre ID d’espace de travail, afin de les utiliser pour la configuration de l’agent.
5. Dans **Security and Audit**, sélectionnez **OK**.

**Sélectionner un espace de travail existant et ajouter un ordinateur**

Vous pouvez ajouter un ordinateur en suivant le flux de travail du panneau **Intégration**, comme indiqué ci-dessus. Vous pouvez également ajouter un ordinateur en suivant le flux de travail du panneau **Calcul**. Dans cet exemple, nous utilisons **Calcul**.

1. Revenez au menu principal de Security Center et au tableau de bord **Vue d’ensemble**.

   ![Vue d'ensemble][5]

2. Sélectionnez la mosaïque **Calcul**.
3. Dans **Calcul**, sélectionnez **Ajouter des ordinateurs**.

   ![Panneau Calcul][6]

4. Dans **Add new Non-Azure computers** (Ajouter de nouveaux ordinateurs autres qu’Azure), sélectionnez un espace de travail auquel connecter votre ordinateur, puis cliquez sur **Ajouter des ordinateurs**.

   ![Ajouter des ordinateurs][7]

 Le panneau **Agent direct** fournit un lien pour télécharger un agent Windows ou Linux et des clés pour votre ID d’espace de travail, afin de les utiliser pour la configuration de l’agent.   

## <a name="next-steps"></a>Étapes suivantes
Dans cet article, vous avez appris à intégrer des ressources Azure et autres qu’Azure pour tirer parti de la sécurité avancée de Security Center.  Pour utiliser davantage vos ressources intégrées, consultez les articles suivants :

- [Activer la collecte des données](security-center-enable-data-collection.md)
- [Rapport d’informations sur les menaces](security-center-threat-report.md)
- [Accès Juste à temps à la machine virtuelle](security-center-just-in-time.md)

<!--Image references-->
[1]: ./media/security-center-onboarding/onboard.png
[2]: ./media/security-center-onboarding/onboard-subscription.png
[3]: ./media/security-center-onboarding/add-non-azure-resource.png
[4]: ./media/security-center-onboarding/create-workspace.png
[5]: ./media/security-center-onboarding/overview.png
[6]: ./media/security-center-onboarding/compute-blade.png
[7]: ./media/security-center-onboarding/add-non-azure-computer.png
[8]: ./media/security-center-onboarding/onboard-workspace.png

