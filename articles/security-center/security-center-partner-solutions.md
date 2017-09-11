---
title: Gestion des solutions de partenaire dans Azure Security Center | Microsoft Docs
description: "Ce document vous explique comment le Centre de sécurité Azure vous permet de surveiller en un clin d’œil l’intégrité de vos solutions de partenaires intégrées à votre abonnement Azure."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 70c076ef-3ad4-4000-a0c1-0ac0c9796ff1
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/17/2017
ms.author: terrylan
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: 2ebb930e877c5027f4d7b0a316a7f5ebe84471b1
ms.contentlocale: fr-fr
ms.lasthandoff: 08/21/2017

---
# <a name="monitoring-partner-solutions-with-azure-security-center"></a>Surveillance des solutions de partenaire avec le Centre de sécurité Azure
Ce document explique comment surveiller l’état d’intégrité de vos solutions de partenaire dans le Centre de sécurité Azure.

> [!NOTE]
> Ce document présente le service à l’aide d’un exemple de déploiement. Ce document n'est pas un guide pas à pas.
>
>

## <a name="monitoring-partner-solutions"></a>Surveillance des solutions de partenaire
La mosaïque **Solutions de partenaire** du panneau **Security Center** vous permet de surveiller en un clin d’œil l’intégrité de vos solutions de partenaire intégrées à votre abonnement Azure.

![Mosaïque Solutions de partenaire][1]

La mosaïque **Solutions de partenaires** affiche le nombre de solutions de partenaires intégrées à votre abonnement. Si votre abonnement ne comporte aucune solution intégrée, la mosaïque indique zéro.

Pour afficher l’intégrité de vos solutions de partenaire :

1. Sélectionnez la vignette **Solutions de partenaires** . Le panneau **Solutions de partenaires** vous présente la liste de vos solutions de partenaires connectées au Centre de sécurité.

   ![Solutions de partenaires][3]

   L’état d’une solution de partenaire peut être :

   * Protégé (vert) : aucun problème d’intégrité.
   * Défectueux (rouge) : problème d’intégrité nécessitant une action immédiate.
   * Arrêté (orange) : état d’intégrité non signalé par la solution.
   * Inconnu (orange) : intégrité de la solution inconnue pour l’instant en raison d’un échec de l’ajout d’une nouvelle ressource à la solution existante.
   * Non signalé (gris) : état non encore signalé par la solution (ce qui peut être le cas si celle-ci a été récemment connectée et est en cours de déploiement).

2. Sélectionnez une solution de partenaire. Dans cet exemple, sélectionnons la solution **Qualys**.  Un panneau s'ouvre et affiche l'état de la solution partenaire et des ressources associées à cette solution. Sélectionnez **Console de solution** afin d’ouvrir l’expérience de gestion du partenaire pour cette solution.

   ![Détail de la solution partenaire][4]
3. Revenez au panneau **Qualys** et sélectionnez **Associer la machine virtuelle**. Le panneau **Associer les applications** s’ouvre. Il vous permet de connecter des ressources à la solution de partenaire.

   ![Associer des ressources à la solution de partenaire][5]

## <a name="next-steps"></a>Étapes suivantes
Ce document vous a présenté la mosaïque **Solutions de partenaire** du Centre de sécurité. Pour plus d’informations sur Security Center, consultez les articles suivants :

* [Définition des stratégies de sécurité dans Azure Security Center](security-center-policies.md) : découvrez comment configurer des stratégies de sécurité pour vos groupes de ressources et abonnements Azure.
* [Gestion des recommandations de sécurité dans Azure Security Center](security-center-recommendations.md) : découvrez la façon dont les recommandations peuvent vous aider à protéger vos ressources Azure.
* [Surveillance de l’intégrité de la sécurité dans Azure Security Center](security-center-monitoring.md) : découvrez comment surveiller l’intégrité de vos ressources Azure.
* [Gestion et résolution des alertes de sécurité dans Azure Security Center](security-center-managing-and-responding-alerts.md) : découvrez comment gérer et résoudre les alertes de sécurité.
* [FAQ Azure Security Center](security-center-faq.md) : forum aux questions concernant l’utilisation de ce service.
* [Blog sur la sécurité Azure](http://blogs.msdn.com/b/azuresecurity/) : découvrez les dernières nouvelles et informations sur la sécurité Azure.

<!--Image references-->
[1]: ./media/security-center-partner-solutions/partner-solutions-tile.png
[3]: ./media/security-center-partner-solutions/partner-solutions.png
[4]: ./media/security-center-partner-solutions/partner-solutions-detail.png
[5]: ./media/security-center-partner-solutions/link-applications.png

