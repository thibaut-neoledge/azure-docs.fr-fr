---
title: "Activer l’audit et la détection des menaces sur les serveurs SQL dans Azure Security Center | Microsoft Docs"
description: "Ce document vous explique comment implémenter la recommandation Azure Security Center **Activer l’audit et la détection des menaces sur les serveurs SQL**."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 042fca4d-7dab-4172-8614-e8c21ccb4960
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/30/2017
ms.author: terrylan
ms.openlocfilehash: 660b537aef8d175a478ff93d60b8391d55fc92ad
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="enable-auditing-and-threat-detection-on-sql-servers-in-azure-security-center"></a>Activer l’audit et la détection des menaces sur les serveurs SQL dans Azure Security Center
Azure Security Center vous recommande d’activer l’audit et la détection des menaces pour toutes les bases de données sur vos serveurs SQL Azure si l’audit n’est pas déjà activé. L’audit et la détection des menaces peuvent vous aider à respecter une conformité réglementaire, à comprendre l’activité de la base de données, et à découvrir des discordances et des anomalies susceptibles d’indiquer des problèmes pour l’entreprise ou des violations de la sécurité.

Une fois que vous avez activé l’audit, vous pouvez configurer les paramètres Threat Detection et les adresses électroniques pour recevoir des alertes de sécurité. Threat Detection permet de détecter les activités base de données anormales indiquant la présence potentielle de menaces de sécurité pour la base de données. Cela vous permet de détecter et de répondre aux menaces potentielles à mesure qu’elles surviennent.

Cette recommandation s’applique uniquement au service SQL Azure, elle ne concerne pas SQL Server en cours d’exécution sur vos machines virtuelles dans Azure Infrastructure Services (Azure IaaS).

> [!NOTE]
> Ce document présente le service à l’aide d’un exemple de déploiement.  Il ne s’agit pas d’un guide pas à pas.
>
>

## <a name="implement-the-recommendation"></a>Implémenter la recommandation
1. Dans le panneau **Recommandations**, sélectionnez **Activer l’audit et la détection des menaces sur les serveurs SQL**.  Ceci ouvre le panneau **Activer l’audit et la détection des menaces sur les serveurs SQL**.

   ![Activer l’audit sur les serveurs SQL][1]
2. Sélectionnez un serveur SQL sur lequel activer l’audit et la détection des menaces. Ceci opération ouvre le panneau **Audit et détection des menaces**.

3. Dans le panneau **Audit et détection des menaces**, sélectionnez **ACTIVÉ** sous **Audit**.

   ![Activer l’audit des paramètres][2]
4. Suivez les étapes de la rubrique [Audit de base de données SQL dans le portail Azure](../sql-database/sql-database-auditing-portal.md) pour configurer l’emplacement de stockage de vos journaux d’audit. Le compte de stockage de l’abonnement pour la collecte de données est le compte de stockage par défaut.
5. Suivez les étapes de la rubrique [Prise en main de Threat Detection pour la base de données SQL](../sql-database/sql-database-threat-detection.md) pour activer et configurer la détection des menaces (Threat Detection) et configurer la liste des adresses électroniques qui recevront les alertes de sécurité lors de la détection d’activités anormales.

## <a name="see-also"></a>Voir aussi
Cet article vous a montré comment implémenter la recommandation de Security Center « Activer l’audit et la détection des menaces sur les serveurs SQL ». Pour en savoir plus sur la sécurisation de votre base de données SQL, consultez les rubriques suivantes :

* [Sécurisation de votre base de données SQL](../sql-database/sql-database-security-overview.md)

Pour plus d’informations sur Security Center, consultez les rubriques suivantes :

* [Définition des stratégies de sécurité dans Azure Security Center](security-center-policies.md) : découvrez comment configurer des stratégies de sécurité pour vos groupes de ressources et abonnements Azure.
* [Gestion des recommandations de sécurité dans Azure Security Center](security-center-recommendations.md) : découvrez comment les recommandations peuvent vous aider à protéger vos ressources Azure.
* [Surveillance de l’intégrité de la sécurité dans Azure Security Center](security-center-monitoring.md) : découvrez comment surveiller l’intégrité de vos ressources Azure.
* [Gestion et résolution des alertes de sécurité dans Azure Security Center](security-center-managing-and-responding-alerts.md) : découvrez comment gérer et résoudre les alertes de sécurité.
* [Surveillance des solutions partenaires avec Azure Security Center](security-center-partner-solutions.md) : découvrez comment surveiller l’état d’intégrité de vos solutions partenaires.
* [Questions fréquentes (FAQ) sur Azure Security Center](security-center-faq.md) : questions fréquemment posées sur l’utilisation de ce service.
* [Blog sur la sécurité Azure](http://blogs.msdn.com/b/azuresecurity/) : découvrez les dernières nouvelles et informations sur la sécurité Azure.

<!--Image references-->
[1]: ./media/security-center-enable-auditing-on-sql-server/enable-auditing-on-sql-servers.png
[2]: ./media/security-center-enable-auditing-on-sql-server/auditing-settings-blade.png
