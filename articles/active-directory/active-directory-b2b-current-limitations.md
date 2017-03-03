---
title: "Limitations actuelles d’Azure Active Directory B2B Collaboration | Microsoft Docs"
description: "Limitations actuelles de la version préliminaire d’Azure Active Directory B2B Collaboration"
services: active-directory
documentationcenter: 
author: sasubram
manager: femila
editor: 
tags: 
ms.assetid: 
ms.service: active-directory
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: identity
ms.date: 02/16/2017
ms.author: sasubram
translationtype: Human Translation
ms.sourcegitcommit: 0c07c842ba8c6214d6746b0361af7b416069a6f5
ms.openlocfilehash: 4dae74740d8fd00d36492e5a9fad8848b5d03952
ms.lasthandoff: 02/17/2017


---

# <a name="azure-active-directory-b2b-collaboration-current-limitations"></a>Limitations actuelles d’Azure Active Directory B2B Collaboration

1. Les API d’invitation d’Azure Active Directory (Azure AD) B2B Collaboration sont en test de version bêta. Cette surface d’API est la direction à suivre, toutefois, comme toutes les versions bêta, elle est soumise au contrat de l’espace de noms de la version bêta. L’API sera intégrée à une version numérotée lors de la publication de la version grand public.
2. La possibilité d’une double authentification multifacteur (MFA) si votre partenaire dispose déjà d’une stratégie d’authentification multifacteur dans B2B Collaboration sera exécutée et gérée par l’organisation à l’origine de l’invitation. Ceci est souhaitable, car cela couvre toutes les identités et vous permet de contrôler la force d’authentification de vos invités dans B2B Collaboration. Toutefois, si vous avez un partenaire qui dispose déjà de l’authentification multifacteur configurée et appliquée, ses utilisateurs devront peut-être procéder à l’authentification multifacteur une fois dans leur organisation d’origine, puis à nouveau dans votre organisation. Dans les versions à venir, nous lancerons une stratégie qui vous permettra d’approuver l’authentification multifacteur de certains partenaires pour éviter le problème relatif à l’authentification multifacteur double.
3. Activation instantanée Dans les flux B2B Collaboration, nous ajoutons des utilisateurs au répertoire et les mettons à jour de manière dynamique pendant l’échange d’invitation, l’affectation d’application, etc. Les mises à jour et les écritures se produisent en général dans une instance de répertoire et doivent être répliquées entre toutes les instances. Nous avons observé que, dans certains cas, le laps de temps limité nécessaire à l’exécution de la réplication peut entraîner des problèmes qui se manifestent sous forme de problèmes d’autorisation. Nous nous efforçons de réduire/d’éliminer cet ensemble de problèmes avant la Mise à la disposition générale. En attendant, vous ne devriez pas rencontrer ce problème, mais si c’est le cas, l’actualisation ou une nouvelle tentative permet en général de résoudre le problème.

  ## <a name="next-steps"></a>Étapes suivantes

  Consultez les autres articles sur la collaboration B2B d'Azure AD :

  * [Qu’est-ce qu’Azure AD B2B Collaboration ?](active-directory-b2b-what-is-azure-ad-b2b.md)
  * [Propriétés de l’utilisateur B2B Collaboration](active-directory-b2b-user-properties.md)
  * [Ajout d’un utilisateur B2B Collaboration à un rôle](active-directory-b2b-add-guest-to-role.md)
  * [Déléguer des invitations B2B Collaboration](active-directory-b2b-delegate-invitations.md)
  * [Groupes dynamiques et B2B Collaboration](active-directory-b2b-dynamic-groups.md)
  * [Code B2B Collaboration et exemples PowerShell](active-directory-b2b-code-samples.md)
  * [Configurer des applications SaaS pour B2B Collaboration](active-directory-b2b-configure-saas-apps.md)
  * [Jetons utilisateur B2B Collaboration](active-directory-b2b-user-token.md)
  * [Mappage des revendications utilisateur B2B Collaboration](active-directory-b2b-claims-mapping.md)
  * [Partage externe d’Office 365](active-directory-b2b-o365-external-user.md)

