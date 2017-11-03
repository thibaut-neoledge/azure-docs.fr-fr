---
title: "Gestion des solutions de partenaire connectées dans Azure Security Center | Microsoft Docs"
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
ms.date: 10/16/2017
ms.author: terrylan
ms.openlocfilehash: 181e1e00716987732ee809df6171c2f71087f3e1
ms.sourcegitcommit: bd0d3ae20773fc87b19dd7f9542f3960211495f9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/18/2017
---
# <a name="managing-connected-partner-solutions-with-azure-security-center"></a>Gestion des solutions de partenaire connectées avec Azure Security Center
Cet article explique comment gérer et surveiller des solutions de sécurité connectées dans Azure Security Center.

## <a name="monitoring-partner-solutions"></a>Surveillance des solutions de partenaire
Pour surveiller l’état d’intégrité des solutions de sécurité connectées et effectuer des opérations de gestion de base :

1. Sous **Security Center - Vue d’ensemble**, sélectionnez **Solutions de sécurité**.

  ![Sélectionner des solutions de sécurité][1]

  La section **Solutions connectées** comprend des solutions de sécurité qui sont connectées à Security Center et des informations sur l’état d’intégrité de chaque solution.

  ![Solutions de partenaires][2]

   L’état d’une solution de partenaire peut être :

   * Sain (vert) : aucun problème d’intégrité.
   * Défectueux (rouge) : problème d’intégrité nécessitant une action immédiate.
   * Problèmes d’intégrité (orange) : état d’intégrité non signalé par la solution.
   * Non signalé (gris) : état non encore signalé par la solution (ce qui peut être le cas si celle-ci a été récemment connectée et est en cours de déploiement), ou aucune donnée d’intégrité n’est disponible.

   > [!NOTE]
   > Si les données sur l’état d’intégrité ne sont pas disponibles, Security Center affiche la date et l’heure du dernier événement reçu pour indiquer si la solution signale ou non des données. Si aucune donnée d’intégrité n’est disponible et qu’aucune alerte n’a été reçue au cours des 14 derniers jours, Security Center indique que la solution est défectueuse ou qu’elle ne signale pas de données.
   >
   >

2. Sélectionnez **AFFICHAGE** pour obtenir des informations et des options supplémentaires, notamment :

  - **Console de solution**. Ouvre l’expérience de gestion pour cette solution.
  - **Associer la machine virtuelle**. Ouvre le panneau Associer les applications. Il vous permet de connecter des ressources à la solution de partenaire.
  - **Supprimer la solution**.
  - **Configurer**.

   ![Détail de la solution partenaire][3]

## <a name="next-steps"></a>Étapes suivantes
Dans cet article, vous avez appris à gérer et à surveiller des solutions de sécurité connectées dans Security Center. Pour plus d’informations sur le Centre de sécurité, consultez les rubriques suivantes :

* [Vue d’ensemble des solutions de sécurité](security-center-partner-integration.md) : découvrez comment connecter et gérer les solutions de sécurité.
* [Connexion de Microsoft Advanced Threat Analytics (ATA)](security-center-ata-integration.md) : découvrez comment connecter les alertes issues d’ATA.
* [Connexion d’Azure Active Directory (AD) Identity Protection ](security-center-aadip-integration.md) : découvrez comment connecter les alertes issues d’Azure AD Identity Protection.
* [Intégration de solutions de partenaires](security-center-partner-integration.md) : découvrez comment intégrer d’autres solutions de sécurité.
* [Gestion et résolution des alertes de sécurité](security-center-managing-and-responding-alerts.md) : découvrez comment gérer et résoudre les alertes de sécurité.
* [FAQ d’Azure Security Center](security-center-faq.md) : découvrez les réponses aux questions les plus souvent posées à propos de l’utilisation de ce service.
* [Blog sur la sécurité Azure](http://blogs.msdn.com/b/azuresecurity/) : accédez à des billets de blog sur la sécurité et la conformité Azure.

<!--Image references-->
[1]: ./media/security-center-partner-solutions/partner-solutions-tile.png
[2]: ./media/security-center-partner-solutions/partner-solutions.png
[3]: ./media/security-center-partner-solutions/partner-solutions-detail.png
