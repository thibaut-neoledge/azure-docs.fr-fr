---
title: "Contrôles dans l’accès conditionnel Azure Active Directory | Microsoft Docs"
description: "Découvrez comment fonctionnent les contrôles dans l’accès conditionnel Azure Active Directory."
services: active-directory
keywords: "accès conditionnel aux applications, accès conditionnel à Azure AD, accès sécurisé aux ressources d’entreprise, stratégies d’accès conditionnel"
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/07/2017
ms.author: markvi
ms.reviewer: calebb
ms.translationtype: HT
ms.sourcegitcommit: 2c6cf0eff812b12ad852e1434e7adf42c5eb7422
ms.openlocfilehash: 0f7d847c98e790c542f3a3e666b9a887099a6cbc
ms.contentlocale: fr-fr
ms.lasthandoff: 09/13/2017

---

# <a name="controls-in-azure-active-directory-conditional-access"></a>Contrôles dans l’accès conditionnel Azure Active Directory 

Avec l’[accès conditionnel Azure Active Directory (Azure AD)](active-directory-conditional-access-azure-portal.md), vous pouvez contrôler la façon dont les utilisateurs autorisés accèdent à vos applications cloud. Dans une stratégie d’accès conditionnel, vous définissez la réponse (« faire ») à une condition spécifique (« quand cela se produit »). Dans le contexte de l’accès conditionnel, 

- « **When this happens** » est une **instruction de condition**
- « **Then do this** » est un **contrôle**

![Contrôle](./media/active-directory-conditional-access-controls/11.png)

Une stratégie d’accès conditionnel combine une instruction de condition à des contrôles.

![Contrôle](./media/active-directory-conditional-access-controls/12.png)

Chaque contrôle est soit une exigence qui doit être remplie par la personne ou le système qui se connecte, soit une restriction sur ce que l’utilisateur peut faire après la connexion. 

Il existe deux types de contrôles : 

- **Contrôle d’octroi** - pour réguler l’accès

- **Contrôles de session** - pour restreindre l’accès au sein d’une session

Cette rubrique explique les divers contrôles qui sont disponibles dans l’accès conditionnel Azure AD. 

## <a name="grant-controls"></a>Contrôles d’octroi

Avec les contrôles d’octroi, vous pouvez bloquer l’accès complètement ou autoriser l’accès avec des exigences supplémentaires en sélectionnant les contrôles de votre choix. Pour de nombreux contrôles, vous pouvez exiger ce qui suit :

- Tous les contrôles sélectionnés doivent être satisfaits (*AND*). 
- Un seul contrôle sélectionné doit être satisfait (*OR*).

![Contrôle](./media/active-directory-conditional-access-controls/51.png)



### <a name="multi-factor-authentication"></a>Authentification multifacteur

Vous pouvez utiliser ce contrôle pour exiger une authentification multifacteur pour accéder à l’application cloud spécifiée. Ce contrôle prend en charge les fournisseurs multifacteur suivants : 

- Azure Multi-Factor Authentication 

- Un fournisseur d’authentification multifacteur local combiné avec les services de fédération Active Directory (AD FS).
 
L’authentification multifacteur contribue à empêcher tout accès à vos ressources par un utilisateur non autorisé qui peut avoir obtenu l’accès aux informations d’identification principales d’un utilisateur valide.



### <a name="compliant-device"></a>Conformité de l’appareil

Vous pouvez configurer des stratégies d’accès conditionnel au niveau de l’appareil. L’objectif d’une stratégie d’accès conditionnel appareil par appareil est de n’accorder l’accès aux ressources configurées qu’aux appareils de confiance. Vous pouvez par exemple exiger un appareil conforme, ce qui implique de définir ce qu’est un appareil de confiance. Pour plus d’informations, consultez [Configurer des stratégies d’accès conditionnel au niveau de l’appareil Azure Active Directory](active-directory-conditional-access-policy-connected-applications.md).

### <a name="domain-joined-device"></a>Appareil joint à un domaine

Pour configurer des stratégies d’accès conditionnel au niveau de l’appareil, vous pouvez également exiger un appareil joint à un domaine. Cette exigence fait référence aux tablettes professionnelles, ordinateurs portables et postes de travail Windows joints à une instance sur site d’Active Directory. Pour plus d’informations, consultez [Configurer des stratégies d’accès conditionnel au niveau de l’appareil Azure Active Directory](active-directory-conditional-access-policy-connected-applications.md).





### <a name="approved-client-app"></a>Application cliente approuvée

Étant donné que vos employés utilisent des appareils mobiles pour les tâches personnelles et professionnelles, vous pouvez souhaiter avoir la possibilité de protéger les données d’entreprise accessibles à l’aide d’appareils même si ce n’est pas vous qui les gérez.
Vous pouvez utiliser des [stratégies de protection des applications Intune](https://docs.microsoft.com/intune/app-protection-policy) pour mieux protéger les données de votre entreprise, quelle que soit votre solution de gestion des appareils mobiles (MDM).


Avec les applications clientes approuvées, vous pouvez exiger qu’une application cliente qui tente d’accéder à vos applications cloud prenne en charge des [stratégies de protection des applications Intune](https://docs.microsoft.com/intune/app-protection-policy). Par exemple, vous pouvez restreindre l’accès à Exchange Online à l’application Outlook. Une stratégie d’accès conditionnel qui requiert des applications clientes approuvées est aussi appelée [stratégie d’accès conditionnel basé sur les applications](active-directory-conditional-access-mam.md). Pour obtenir la liste des applications clientes approuvées prises en charge, consultez [Spécification d’application cliente approuvée](active-directory-conditional-access-technical-reference.md#approved-client-app-requirement).



## <a name="session-controls"></a>Contrôles de session

Les contrôles de session permettent de limiter l’expérience dans une application cloud. Les contrôles de session sont appliqués par les applications cloud et s’appuient sur des informations supplémentaires fournies par Azure AD à l’application concernant la session.

![Contrôle](./media/active-directory-conditional-access-controls/31.png)

### <a name="use-app-enforced-restrictions"></a>Utiliser les restrictions appliquées par l’application

Vous pouvez utiliser ce contrôle pour exiger qu’Azure AD transmette les informations d’appareil à l’application cloud. Cela permet à l’application cloud de savoir si l’utilisateur utilise un appareil conforme ou un appareil joint à un domaine. Ce contrôle est actuellement pris en charge uniquement avec SharePoint en comme application cloud. SharePoint utilise les informations d’appareil pour fournir aux utilisateurs une expérience limitée ou complète en fonction de l’état de l’appareil.
Pour savoir plus en détails comment exiger un accès limité avec SharePoint, consultez la page [Contrôler l’accès à partir d’appareils non gérés](https://aka.ms/spolimitedaccessdocs).



## <a name="next-steps"></a>Étapes suivantes

- Pour savoir comment configurer une stratégie d’accès conditionnel, consultez [Prise en main de l’accès conditionnel dans Azure Active Directory](active-directory-conditional-access-azure-portal-get-started.md).

- Si vous êtes prêt à configurer des stratégies d’accès conditionnel pour votre environnement, consultez les [Meilleures pratiques pour l’accès conditionnel dans Azure Active Directory](active-directory-conditional-access-best-practices.md). 

