---
title: "Limitations d’Azure Active Directory B2B Collaboration | Microsoft Docs"
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
ms.sourcegitcommit: 0e71a840d4f503779131ee4a21fe6063d33185f1
ms.openlocfilehash: cbefca2d45a332cd57cfea49dfeaa300426d5502
ms.lasthandoff: 02/24/2017


---

# <a name="limitations-of-azure-ad-b2b-collaboration"></a>Limitations d’Azure AD B2B Collaboration
Azure Active Directory (Azure AD) B2B Collaboration subit actuellement les limitations décrites dans le présent article.

## <a name="invitation-apis-are-in-preview"></a>Les API d’invitation sont en version préliminaire
Nous visons pour demain la surface d’API. Toutefois, comme toutes les versions préliminaires, l’API est soumise au contrat d’espace de noms des versions préliminaires. L’API sera intégrée à une version numérotée lors de la publication de la version grand public (GA).

## <a name="possible-double-multi-factor-authentication"></a>Risque de redondance de l’authentification multifacteur
Cette redondance peut se produire si votre partenaire a déjà mis en place une stratégie Azure Multi-Factor Authentication. L’authentification multifacteur pour les utilisateurs B2B Collaboration est exécutée et gérée dans l’organisation à l’origine de l’invitation. Cette authentification est souhaitable car elle couvre toutes les identités et vous permet de contrôler la force d’authentification de vos invités dans B2B Collaboration.

Toutefois, si un partenaire a déjà configuré et appliqué Multi-Factor Authentication, ses utilisateurs devront peut-être effectuer l’authentification une fois dans leur organisation d’origine, puis de nouveau dans les vôtres.

Dans une prochaine version, nous prévoyons de présenter une stratégie qui vous évitera le problème de double authentification en choisissant de faire confiance à l’authentification multi-facteur du partenaire.

## <a name="instant-on"></a>Activation instantanée
Dans les flux B2B Collaboration, nous ajoutons des utilisateurs au répertoire et les mettons à jour de manière dynamique pendant l’échange d’invitation, l’affectation d’application, etc. Les mises à jour et les écritures se produisent d’ordinaire dans une instance de répertoire et doivent être répliquées entre toutes les instances. Nous avons observé que le laps de temps limité nécessaire à l’exécution de la réplication peut entraîner des problèmes d’autorisation. Nous déployons tous les efforts nécessaires pour réduire ou éliminer ces problèmes avant la publication de notre version grand public. Vous risquez de rencontrer ces problèmes entre-temps, mais vous pouvez actualiser ou effectuer de nouvelles tentatives pour tenter de les résoudre.

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

