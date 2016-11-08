---
title: Activation de l’audit sur les bases de données SQL dans Azure Security Center | Microsoft Docs
description: Ce document vous montre comment implémenter la recommandation du Centre de sécurité Azure **Activer l’audit sur les bases de données SQL**.
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: ''

ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/29/2016
ms.author: terrylan

---
# Activation de l’audit sur les bases de données SQL dans le Azure Security Center
L’Azure Security Center vous recommande d’activer l’audit sur toutes les bases de données SQL, si ce n’est déjà fait. L’audit peut vous aider à respecter une conformité réglementaire, à comprendre l’activité de la base de données et à découvrir des discordances et anomalies susceptibles d’indiquer des problèmes pour l’entreprise ou des violations de la sécurité.

Une fois que vous avez activé l’audit, vous pouvez configurer les paramètres Threat Detection et les adresses électroniques pour recevoir des alertes de sécurité. Threat Detection permet de détecter les activités base de données anormales indiquant la présence potentielle de menaces de sécurité pour la base de données. Cela vous permet de détecter et de répondre aux menaces potentielles à mesure qu’elles surviennent.

Cette recommandation s’applique uniquement au service SQL Azure, elle ne concerne pas SQL en cours d’exécution sur vos machines virtuelles.

> [!NOTE]
> Ce document présente le service à l’aide d’un exemple de déploiement. Il ne s’agit pas d’un guide pas à pas.
> 
> 

## Implémenter la recommandation
1. Dans le panneau **Recommandations** , sélectionnez **Activer l’audit sur les bases de données SQL**. Cette opération ouvre le panneau **Activer l’audit sur les bases de données SQL**. ![Activer l’audit sur les bases de données SQL][1]
2. Sélectionnez une base de données SQL sur laquelle activer l’audit. Cette opération ouvre le panneau **Audit et détection des menaces**. ![Audit et détection des menaces][2]
3. Sur le panneau **Audit et détection des menaces**, sélectionnez **ON** sous **Audit**. ![Activer l’audit et la détection des menaces][3]
4. Suivez les étapes de la rubrique [Prise en main de Threat Detection pour la base de données SQL](../sql-database/sql-database-threat-detection-get-started.md) pour activer et configurer la détection des menaces (Threat Detection) et configurer la liste des adresses électroniques qui recevront les alertes de sécurité lors de la détection d’activités anormales.

## Voir aussi
Cet article vous a montré comment implémenter la recommandation de Security Center « Activer l’audit sur les bases de données SQL ». Pour en savoir plus sur la sécurisation de votre base de données SQL, consultez les rubriques suivantes :

* [Sécurisation de votre base de données SQL](../sql-database/sql-database-security.md)

Pour plus d’informations sur le Centre de sécurité, consultez les rubriques suivantes :

* [Définition des stratégies de sécurité dans Azure Security Center](security-center-policies.md) : découvrez comment configurer des stratégies de sécurité pour vos groupes de ressources et abonnements Azure.
* [Gestion des recommandations de sécurité dans Azure Security Center](security-center-recommendations.md) : découvrez la façon dont les recommandations peuvent vous aider à protéger vos ressources Azure.
* [Surveillance de l’intégrité de la sécurité dans Azure Security Center](security-center-monitoring.md) : découvrez comment surveiller l’intégrité de vos ressources Azure.
* [Gestion et résolution des alertes de sécurité dans Azure Security Center](security-center-managing-and-responding-alerts.md) : découvrez comment gérer et résoudre les alertes de sécurité.
* [Surveillance des solutions de partenaires avec Azure Security Center](security-center-partner-solutions.md) : découvrez comment surveiller l’état d’intégrité de vos solutions de partenaires.
* [FAQ Azure Security Center](security-center-faq.md) : forum aux questions concernant l’utilisation de ce service.
* [Blog sur la sécurité Azure](http://blogs.msdn.com/b/azuresecurity/) : découvrez les dernières nouvelles et informations sur la sécurité Azure.

<!--Image references-->
[1]: ./media/security-center-enable-auditing-on-sql-databases/enable-auditing-on-sql-databases.png
[2]: ./media/security-center-enable-auditing-on-sql-databases/auditing-threat-detection.png
[3]: ./media/security-center-enable-auditing-on-sql-databases/auditing-threat-detection-blade.png

<!---HONumber=AcomDC_0803_2016-->