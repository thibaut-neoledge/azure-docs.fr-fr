---
title: "Présentation des stratégies de sécurité dans Azure Security Center | Microsoft Docs"
description: "Découvrez les stratégies de sécurité dans Azure Security Center et leurs principales fonctionnalités."
services: security-center
documentationcenter: na
author: YuriDio
manager: MBaldwin
editor: 
ms.assetid: f24b1e4a-cc36-4542-b21e-041453cdfcd8
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/13/2017
ms.author: yurid
ms.openlocfilehash: 95ef2099cb16bcfd550ce2799428f1a16031f535
ms.sourcegitcommit: 5d772f6c5fd066b38396a7eb179751132c22b681
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/13/2017
---
# <a name="security-policies-overview"></a>Vue d’ensemble des stratégies de sécurité
Ce document offre une vue d’ensemble des stratégies de sécurité dans Security Center.

## <a name="what-are-security-policies"></a>Que sont les stratégies de sécurité ?
Une stratégie de sécurité définit la configuration souhaitée de vos charges de travail, tout en garantissant leur conformité aux exigences de sécurité réglementaires. Dans Security Center, vous pouvez définir les stratégies de vos abonnements Azure, qui peuvent être adaptées au type de charge de travail ou à la sensibilité des données. Par exemple, les applications qui utilisent des données réglementées, telles que des informations d’identification personnelle, peuvent nécessiter un niveau de sécurité plus élevé que d’autres charges de travail. 

Les stratégies Security Center fournissent les composants suivants :

- Collecte de données : paramètres du provisionnement d’agent et de la [collecte de données](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection).
- Stratégie de sécurité : détermine quels contrôles sont surveillés et recommandés par Security Center (modifiez la [stratégie de sécurité](https://docs.microsoft.com/en-us/azure/security-center/security-center-policies) dans Security Center ou utilisez une [stratégie Azure](security-center-azure-policy.md), en préversion limitée, pour créer des définitions, définir des stratégies supplémentaires et assigner des stratégies à des groupes d’administration).
- Notifications par e-mail : paramètres des contacts de sécurité et des [notifications par e-mail](https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details).
- Niveau tarifaire : [sélection du niveau tarifaire](https://docs.microsoft.com/azure/security-center/security-center-pricing) Gratuit ou Standard, qui détermine les fonctionnalités Security Center disponibles pour les ressources visées (peut être spécifié pour les abonnements, les groupes de ressources et les espaces de travail). 


## <a name="who-can-edit-security-policies"></a>Qui peut modifier des stratégies de sécurité ?
Security Center utilise le contrôle d’accès en fonction du rôle (RBAC) qui fournit des rôles intégrés susceptibles d’être assignés à des utilisateurs, des groupes et des services dans Azure. Lorsqu’un utilisateur ouvre le Centre de sécurité, il voit uniquement les informations relatives aux ressources auxquelles il a accès. Autrement dit, l’utilisateur se voit attribuer le rôle de propriétaire, collaborateur ou lecteur, pour l’abonnement ou le groupe de ressources auquel appartiennent les ressources. Outre ces rôles, il existe deux rôles propres à Security Center :

- Lecteur Sécurité : un utilisateur qui appartient à ce rôle est en mesure d’afficher les droits relatifs à Security Center (ce qui inclut les recommandations, les alertes, la stratégie et l’intégrité), mais il ne peut pas effectuer de changements.
- Administrateur de la sécurité : il a les mêmes droits que le lecteur Sécurité, mais il peut également mettre à jour la stratégie de sécurité, et ignorer des alertes et des recommandations.


## <a name="next-steps"></a>Étapes suivantes
Dans ce document, vous avez découvert les stratégies de sécurité dans Azure Security Center. Pour plus d’informations sur le Centre de sécurité Azure, consultez les rubriques suivantes :

* [Définition des stratégies de sécurité dans Azure Security Center](security-center-policies.md) : découvrez comment configurer des stratégies de sécurité pour vos groupes de ressources et abonnements Azure.
* [Gestion des recommandations de sécurité dans Azure Security Center](security-center-recommendations.md) : découvrez la façon dont les recommandations peuvent vous aider à protéger vos ressources Azure.
* [Surveillance de l’intégrité de la sécurité dans Azure Security Center](security-center-monitoring.md) : découvrez comment surveiller l’intégrité de vos ressources Azure.
* [Gestion et résolution des alertes de sécurité dans Azure Security Center](security-center-managing-and-responding-alerts.md) : découvrez comment gérer et résoudre les alertes de sécurité.
* [Surveillance des solutions de partenaire avec Azure Security Center](security-center-partner-solutions.md) : découvrez comment surveiller l’état d’intégrité de vos solutions de partenaire.
- [Sécurité des données Azure Security Center](security-center-data-security.md) : découvrez comment les données sont gérées et protégées dans Security Center.
* [FAQ d’Azure Security Center](security-center-faq.md) : découvrez les réponses aux questions les plus souvent posées à propos de l’utilisation de ce service.
* [Blog sur la sécurité Azure](http://blogs.msdn.com/b/azuresecurity/) : découvrez les dernières nouvelles et informations sur la sécurité Azure.


