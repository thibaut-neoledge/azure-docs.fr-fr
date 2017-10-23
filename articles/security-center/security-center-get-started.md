---
title: "Guide de démarrage rapide d’Azure Security Center | Microsoft Docs"
description: "Cet article vous aide à démarrer rapidement sur Azure Security Center en vous guidant à travers les composants de surveillance de sécurité et de gestion des stratégies pour vous amener vers les prochaines étapes."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/14/2017
ms.author: terrylan
ms.openlocfilehash: c28f92af96f31d1c386cf072f83fc142b9a7f588
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-security-center-quick-start-guide"></a>Guide de démarrage rapide d’Azure Security Center
Cet article vous aide à démarrer rapidement sur Azure Security Center en vous guidant à travers les composants de surveillance de sécurité et de gestion des stratégies d’Azure Security Center.

## <a name="prerequisites"></a>Prérequis
Pour utiliser Security Center, vous devez avoir un abonnement à Microsoft Azure. Si vous n’avez pas d’abonnement, vous pouvez vous inscrire pour avoir un [compte gratuit](https://azure.microsoft.com/pricing/free-trial/).

Le niveau Gratuit de Security Center est automatiquement activé dans tous les abonnements Azure. Il vous donne accès à des stratégies de sécurité, à la fonctionnalité d’évaluation de la sécurité en continu et à des recommandations de sécurité actionnables pour vous aider à protéger vos ressources Azure.

Security Center est accessible à partir du [portail Azure](https://azure.microsoft.com/features/azure-portal/). Pour en savoir plus sur le portail Azure, consultez la [documentation relative au portail](https://azure.microsoft.com/documentation/services/azure-portal/).

## <a name="permissions"></a>Autorisations
Dans Security Center, vous ne voyez les informations relatives à une ressource que lorsque vous avez reçu le rôle de propriétaire, de collaborateur ou de lecteur pour l’abonnement ou le groupe de ressources auquel appartient la ressource. Pour plus d’informations sur les rôles et les actions autorisées dans Security Center, consultez l’article [Autorisations dans Azure Security Center](security-center-permissions.md).

## <a name="data-collection"></a>Collecte des données
Azure Security Center collecte des données à partir de vos machines virtuelles Azure et ordinateurs autres qu’Azure pour surveiller les menaces et vulnérabilités de sécurité. Les données sont collectées à l’aide de Microsoft Monitoring Agent, qui lit divers journaux d’événements et configurations liées à la sécurité de la machine et copie les données dans votre espace de travail à des fins d’analyse. Security Center approvisionne Microsoft Monitoring Agent sur toutes les machines virtuelles Azure prises en charge existantes et sur toutes celles nouvellement créées. Consultez [Activer la collecte de données](security-center-enable-data-collection.md) pour en savoir plus sur le fonctionnement de la collecte de données.

L’approvisionnement automatique est fortement recommandé et est requis pour les abonnements appartenant au niveau Standard de Security Center. La désactivation de l’approvisionnement automatique limite la surveillance de la sécurité pour vos ressources.

Consultez [Tarification de Security Center](security-center-pricing.md) pour en savoir plus sur les niveaux tarifaires Gratuit et Standard.

Les étapes suivantes décrivent comment accéder aux composants d’Azure Security Center et les utiliser.

> [!NOTE]
> Cet article présente le service à l’aide d’un exemple de déploiement. Il ne s’agit pas d’un guide pas à pas.
>
>

## <a name="access-security-center"></a>Accéder à Security Center
Dans le portail, procédez comme suit pour accéder à Security Center :

1. Dans le menu **Microsoft Azure**, sélectionnez **Security Center**.

   ![Menu Azure][1]
2. Si vous accédez à Azure Security Center pour la première fois, le panneau **Bienvenue** s’ouvre. Sélectionnez **Lancer le Security Center** pour ouvrir **Security Center**.
   ![Écran d’accueil][10]
3. Après avoir lancé Security Center à partir du panneau Bienvenue ou sélectionné Security Center dans le menu Microsoft Azure, **Security Center** s’ouvre. Pour accéder facilement au panneau **Security Center** à l’avenir, sélectionnez l’option **Épingler le panneau au tableau de bord** (en haut à droite).
   ![Option Épingler le panneau au tableau de bord][2]

## <a name="use-security-center"></a>Utiliser Security Center
Vous pouvez configurer des stratégies de sécurité pour vos groupes de ressources et abonnements Azure. Configurons une stratégie de sécurité pour votre abonnement :

1. Dans le menu principal de Security Center, sélectionnez **Stratégie de sécurité**.
2. Sous **Security Center - Stratégie de sécurité**, sélectionnez un abonnement.
3. Sous **Stratégie de sécurité - Collecte de données**, l’option **Approvisionnement automatiquement** est activée. Security Center approvisionne Microsoft Monitoring Agent sur toutes les machines virtuelles Azure prises en charge existantes et sur toutes celles nouvellement créées.

    ![Stratégie de sécurité][12]

4. Dans le panneau **Stratégie de sécurité**, sélectionnez la **stratégie de sécurité** du composant de la stratégie.

     ![Stratégie de sécurité][11]

5. Sous **Afficher les recommandations pour**, activez les recommandations que vous souhaitez inclure dans votre stratégie de sécurité. Exemples :

   * **L’activation** de l’option **Mises à jour du système** permet d’analyser toutes les machines virtuelles prises en charge pour y détecter des mises à jour de système d’exploitation manquantes.
   * **L’activation** de l’option **Vulnérabilités du système d’exploitation** permet d’analyser toutes les machines virtuelles prises en charge afin d’identifier toute configuration du système d’exploitation risquant de rendre la machine virtuelle plus vulnérable aux attaques.

6. Sélectionnez **Enregistrer**.

### <a name="view-recommendations"></a>Afficher les recommandations
1. Retournez dans le panneau **Security Center** et sélectionnez la vignette **Recommandations**. Security Center analyse l’état de sécurité de vos ressources Azure à intervalles réguliers. Lorsque Security Center identifie des failles de sécurité potentielles, il affiche des recommandations sur le panneau **Recommandations**.
   ![Recommandations dans Azure Security Center][5]
2. Sélectionnez une recommandation sur le panneau **Recommandations** pour visualiser plus d’informations et/ou prendre des mesures afin de résoudre le problème.

### <a name="view-the-security-state-of-your-resources"></a>Afficher l’état de sécurité de vos ressources
1. Retournez au panneau **Centre de sécurité** . La section **Prévention** du tableau de bord contient des indicateurs de l’état de sécurité pour les machines virtuelles, la mise en réseau, les données et les applications.
2. Sélectionnez **Compute** pour afficher plus d’informations. Le panneau **Compute** s’ouvre affichant trois onglets :

  - **Vue d’ensemble** : contient des recommandations relatives aux machines virtuelles et au monitoring.
  - **Machines virtuelles et ordinateurs** : indique l’état actuel de la sécurité des machines virtuelles et des ordinateurs.
  - **Services cloud** : liste les rôles web et de travail contrôlés par Security Center.

    ![Intégrité de la sécurité des calculs][6]

3. Sous l’onglet **Vue d’ensemble**, sélectionnez une recommandation afin d’afficher des informations supplémentaires et/ou de configurer les contrôles nécessaires.
4. Sous l’onglet **Machines virtuelles et ordinateurs**, sélectionnez une ressource pour afficher des détails supplémentaires.

### <a name="view-security-alerts"></a>Afficher les alertes de sécurité
1. Retournez dans le panneau **Security Center** et sélectionnez la mosaïque **Alertes de sécurité**. Le panneau **Alertes de sécurité** s’ouvre et affiche la liste des alertes. Les alertes sont générées par l’analyse de vos journaux de sécurité et de l’activité réseau par Azure Security Center. Des alertes de solutions de partenaires intégrées sont incluses.
   ![Alertes de sécurité dans Azure Security Center][7]

2. Sélectionnez une alerte pour afficher des informations supplémentaires. Dans cet exemple, nous sélectionnons **Fichier binaire système modifié détecté dans le filtre de vidage**. Les panneaux contenant des informations supplémentaires sur l’alerte s’ouvrent.
   ![Détails des alertes de sécurité dans Azure Security Center][8]

### <a name="view-the-health-of-your-partner-solutions"></a>Afficher l’intégrité de vos solutions de partenaires
1. Retournez au panneau **Centre de sécurité** . La vignette **Solutions de sécurité** permet de surveiller en un coup d’œil l’état d’intégrité de vos solutions de partenaires intégrées à votre abonnement Azure.
2. Sélectionnez la vignette **Solutions de sécurité**. Un panneau s’ouvre et affiche la liste de vos solutions de partenaires connectées à Azure Security Center.
   ![solutions de partenaires][9]
3. Sélectionnez une solution de partenaire. Un panneau s'ouvre et affiche l’état de la solution partenaire et des ressources associées à cette solution. Sélectionnez **Console de solution** afin d’ouvrir l’expérience de gestion du partenaire pour cette solution.

   ![Solutions de partenaires][13]

## <a name="next-steps"></a>Étapes suivantes
Cet article vous a présenté les composants de surveillance de sécurité et de gestion des stratégies de sécurité dans Azure Security Center. Maintenant que vous êtes familiarisé avec Azure Security Center, procédez comme suit :

* Configurez une stratégie de sécurité pour votre abonnement Azure. Consultez [Définition de stratégies de sécurité dans Azure Security Center](security-center-policies.md).
* Utilisez les recommandations de Security Center pour vous aider à protéger vos ressources Azure. Consultez [Gestion des recommandations de sécurité dans Azure Security Center](security-center-recommendations.md).
* Vérifiez et gérez vos alertes de sécurité actuelles. Consultez [Gestion et résolution des alertes de sécurité dans Azure Security Center](security-center-managing-and-responding-alerts.md).
- Découvrez comment intégrer des partenaires pour améliorer la sécurité globale. Consultez [Intégration des partenaires et des solutions dans Azure Security Center](security-center-partner-integration.md).
- Découvrez de quelle façon les données sont gérées et protégées dans Security Center. Consultez [Sécurité des données Azure Security Center](security-center-data-security.md).
* Apprenez-en plus sur les [fonctionnalités avancées de détection des menaces](security-center-detection-capabilities.md) fournies avec le [niveau Standard](security-center-pricing.md) d’Azure Security Center. Le niveau Standard est gratuit les 60 premiers jours.
* Si vous avez des questions sur l’utilisation d’Azure Security Center, consultez [Questions fréquentes (FAQ) sur Azure Security Center](security-center-faq.md).

<!--Image references-->
[1]: ./media/security-center-get-started/azure-menu.png
[2]: ./media/security-center-get-started/security-center-pin.png
[5]: ./media/security-center-get-started/recommendations.png
[6]: ./media/security-center-get-started/resources-health.png
[7]: ./media/security-center-get-started/security-alert.png
[8]: ./media/security-center-get-started/security-alert-detail.png
[9]: ./media/security-center-get-started/partner-solutions.png
[10]: ./media/security-center-get-started/welcome.png
[11]: ./media/security-center-get-started/show-recommendations-for.png
[12]: ./media/security-center-get-started/automatic-provisioning.png
[13]: ./media/security-center-get-started/partner-solutions-detail.png
