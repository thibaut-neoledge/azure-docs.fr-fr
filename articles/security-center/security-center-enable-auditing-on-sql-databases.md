---
title: "Activation de l’audit et détection des menaces dans les bases de données SQL dans Azure Security Center | Microsoft Docs"
description: "Ce document vous explique comment implémenter la recommandation Azure Security Center intitulée **Activation de l’audit et détection des menaces dans les bases de données SQL dans Azure Security Center**."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 224b6755-2b36-4ecd-9af8-139a198e0df1
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/30/2017
ms.author: terrylan
translationtype: Human Translation
ms.sourcegitcommit: f41fbee742daf2107b57caa528e53537018c88c6
ms.openlocfilehash: 8f4febdaa4497fee0dc690b59cd6eaa415c5e5cf
ms.lasthandoff: 03/31/2017


---
# <a name="enable-auditing-and-threat-detection-on-sql-databases-in-azure-security-center"></a>Activation de l’audit et détection des menaces dans les bases de données SQL dans Azure Security Center
Azure Security Center vous recommande d’activer l’audit et la détection des menaces sur toutes les bases de données SQL, si ce n’est déjà fait. L’audit et la détection des menaces peuvent vous aider à respecter une conformité réglementaire, à comprendre l’activité de la base de données et à découvrir des discordances et des anomalies susceptibles d’indiquer des problèmes pour l’entreprise ou des violations de la sécurité.

Une fois que vous avez activé l’audit, vous pouvez configurer les paramètres Threat Detection et les adresses électroniques pour recevoir des alertes de sécurité. Threat Detection permet de détecter les activités base de données anormales indiquant la présence potentielle de menaces de sécurité pour la base de données. Cela vous permet de détecter et de répondre aux menaces potentielles à mesure qu’elles surviennent.

Cette recommandation s’applique uniquement au service SQL Azure, elle ne concerne pas SQL en cours d’exécution sur vos machines virtuelles.

> [!NOTE]
> Ce document présente le service à l’aide d’un exemple de déploiement.  Il ne s’agit pas d’un guide pas à pas.
>
>

## <a name="implement-the-recommendation"></a>Implémenter la recommandation
1. Dans le panneau **Recommandations**, sélectionnez **Activer l’audit et la détection des menaces sur les bases de données SQL**.  Cette opération ouvre le panneau **Activer l’audit et la détection des menaces sur les bases de données SQL**.

   ![Activer l’audit sur les bases de données SQL][1]
2. Sélectionnez une base de données SQL sur laquelle activer l’audit. Cette opération ouvre le panneau **Audit et détection des menaces**.

3. Dans le panneau **Audit et détection des menaces**, sélectionnez **ON** sous **Audit**.

   ![Activer l’audit et la détection des menaces][2]
4. Suivez les étapes de la rubrique [Détection de menaces pour les bases de données SQL dans le portail Azure](../sql-database/sql-database-threat-detection-portal.md) pour activer et configurer la détection des menaces (Threat Detection) et configurer la liste des adresses électroniques qui recevront les alertes de sécurité lors de la détection d’activités anormales.

## <a name="see-also"></a>Voir aussi
Cet article vous a montré comment implémenter la recommandation de Security Center « Activer l’audit et la détection des menaces sur les bases de données SQL ». Pour en savoir plus sur la sécurisation de votre base de données SQL, consultez les rubriques suivantes :

* [Sécurisation de votre base de données SQL](../sql-database/sql-database-security-overview.md)

Pour plus d’informations sur le Centre de sécurité, consultez les rubriques suivantes :

* [Définition des stratégies de sécurité dans Azure Security Center](security-center-policies.md) : découvrez comment configurer des stratégies de sécurité pour vos groupes de ressources et abonnements Azure.
* [Gestion des recommandations de sécurité dans Azure Security Center](security-center-recommendations.md) : découvrez comment les recommandations peuvent vous aider à protéger vos ressources Azure.
* [Surveillance de l’intégrité de la sécurité dans Azure Security Center](security-center-monitoring.md) : découvrez comment surveiller l’intégrité de vos ressources Azure.
* [Gestion et résolution des alertes de sécurité dans Azure Security Center](security-center-managing-and-responding-alerts.md) : découvrez comment gérer et résoudre les alertes de sécurité.
* [Surveillance des solutions partenaires avec Azure Security Center](security-center-partner-solutions.md) : découvrez comment surveiller l’état d’intégrité de vos solutions partenaires.
* [FAQ sur Azure Security Center](security-center-faq.md) : forum aux questions concernant l’utilisation de ce service.
* [Blog sur la sécurité Azure](http://blogs.msdn.com/b/azuresecurity/) : découvrez les dernières nouvelles et informations sur la sécurité Azure.

<!--Image references-->
[1]: ./media/security-center-enable-auditing-on-sql-databases/enable-auditing-on-sql-databases.png
[2]: ./media/security-center-enable-auditing-on-sql-databases/auditing-threat-detection-blade.png

