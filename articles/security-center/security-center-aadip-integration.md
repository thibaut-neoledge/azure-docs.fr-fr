---
title: "Connexion d’Azure Active Directory Identity Protection à Azure Security Center | Microsoft Docs"
description: "Découvrez l’intégration entre Azure Security Center et Azure Active Directory Identity Protection."
services: security-center
documentationcenter: na
author: YuriDio
manager: MBaldwin
editor: 
ms.assetid: 0d4b77c2-dba4-4e46-8f55-ab04ddd92496
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/13/2017
ms.author: yurid
ms.openlocfilehash: 7562dd5e1c303a6cb97d25bda5aa080bb5643583
ms.sourcegitcommit: ccb84f6b1d445d88b9870041c84cebd64fbdbc72
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/14/2017
---
# <a name="connecting-azure-active-directory-identity-protection-to-azure-security-center"></a>Connexion d’Azure Active Directory Identity Protection à Azure Security Center
Ce document vous aide à configurer l’intégration entre Azure Active Directory (AD) Identity Protection et Azure Security Center.

## <a name="why-connect-azure-ad-identity-protection"></a>Pourquoi connecter Azure AD Identity Protection ?
[Azure AD Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) permet de détecter les vulnérabilités potentielles qui affectent les identités de votre organisation. Une fois connecté, vous pouvez afficher les alertes d’Azure AD Identity Protection dans Security Center. Cette intégration vous permet d’afficher, de mettre en corrélation et d’examiner toutes les alertes de sécurité liées à vos charges de travail cloud hybrides dans Security Center. 

## <a name="how-do-i-configure-this-integration"></a>Comment faire pour configurer cette intégration
Si votre organisation utilise déjà Azure AD Identity Protection, effectuez les étapes ci-dessous pour configurer l’intégration :

1. Ouvrez le tableau de bord **Security Center**.
2. Dans le volet gauche, cliquez sur **Solutions de sécurité**. Security Center détecte automatiquement si la solution Azure AD Identity Protection est activée pour votre organisation.

    ![AADIP](./media/security-center-aadip-integration/security-center-aadip-integration-fig1.png)

3. Cliquez sur **CONNECTER**.
4. Faite défiler la page **Intégrer Azure AD Identity Protection** vers le bas, puis sélectionnez l’espace de travail approprié :

    ![espace de travail](./media/security-center-aadip-integration/security-center-aadip-integration-fig2.png)

5. Cliquez sur **Connecter**.

Une fois que vous avez terminé cette configuration, la solution Azure AD Identity Protection apparaît dans la page **Solutions de sécurité**, sous **Solutions connectées**. 

## <a name="next-steps"></a>Étapes suivantes
Dans ce document, vous avez appris à connecter Azure AD Identity Protection à Security Center. Pour plus d’informations sur Security Center, consultez les articles suivants :

* [Connexion de Microsoft Advanced Threat Analytics à Azure Security Center](security-center-ata-integration.md)
* [Définition des stratégies de sécurité dans Azure Security Center](security-center-policies.md) : découvrez comment configurer des stratégies de sécurité pour vos groupes de ressources et abonnements Azure.
* [Gestion des recommandations de sécurité dans Azure Security Center](security-center-recommendations.md) : découvrez la façon dont les recommandations peuvent vous aider à protéger vos ressources Azure.
* [Surveillance de l’intégrité de la sécurité dans Azure Security Center](security-center-monitoring.md) : découvrez comment surveiller l’intégrité de vos ressources Azure.
* [Gestion et résolution des alertes de sécurité dans Azure Security Center](security-center-managing-and-responding-alerts.md) : découvrez comment gérer et résoudre les alertes de sécurité.
* [Surveillance des solutions de partenaire avec Azure Security Center](security-center-partner-solutions.md) : découvrez comment surveiller l’état d’intégrité de vos solutions de partenaire.
- [Sécurité des données Azure Security Center](security-center-data-security.md) : découvrez comment les données sont gérées et protégées dans Security Center.
* [FAQ d’Azure Security Center](security-center-faq.md) : découvrez les réponses aux questions les plus souvent posées à propos de l’utilisation de ce service.
* [Blog sur la sécurité Azure](http://blogs.msdn.com/b/azuresecurity/) : découvrez les dernières nouvelles et informations sur la sécurité Azure.


