---

title: "Ajouter des utilisateurs B2B Collaboration à Azure Active Directory sans invitation | Microsoft Docs"
description: "Azure Active Directory B2B Collaboration permet aux professionnels de l’information d’ajouter des utilisateurs de leur organisation à Azure AD pour accéder à vos applications d’entreprise"
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
ms.date: 02/10/2017
ms.author: sasubram
translationtype: Human Translation
ms.sourcegitcommit: 0eef684115f9c21ea61502a10576f74cac0ace8e
ms.openlocfilehash: 8fa8ba169ca85c33e52eee4a7cd5b84aa4012673


---

# <a name="add-b2b-collaboration-users-without-an-invitation"></a>Ajouter des utilisateurs B2B Collaboration sans invitation

Si l’inviteur appartient à un rôle ayant des privilèges d’énumération dans le répertoire de l’organisation partenaire, à partir duquel il ajoute des utilisateurs, les utilisateurs invités sont ajoutés à organisation qui invite sans avoir besoin d’invitation.

Voici un exemple scénario montrant l’utilité de cette procédure :

1. Un utilisateur de l’organisation hôte (par exemple, WoodGrove) invite un utilisateur d’une organisation partenaire (par exemple, Sam@litware.com) en tant qu’invité).
2. L’administrateur de l’organisation hôte définit les stratégies qui permettent à Sam d’identifier et d’ajouter d’autres utilisateurs de l’organisation partenaire (Litware).
3. Maintenant, Sam peut ajouter d’autres utilisateurs Litware au répertoire, à des groupes et à des applications WoodGrove sans avoir besoin d’utiliser des invitations. Si Sam possède les privilèges d’énumération appropriés de Litware, cela se produit automatiquement.

### <a name="next-steps"></a>Étapes suivantes

Consultez les autres articles sur la collaboration B2B d'Azure AD :

* [Qu'est-ce que la collaboration B2B d'Azure AD ?](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Comment les administrateurs Azure Active Directory ajoutent-ils des utilisateurs B2B Collaboration ?](active-directory-b2b-admin-add-users.md)
* [Comment les professionnels de l’information ajoutent-ils des utilisateurs B2B Collaboration ?](active-directory-b2b-iw-add-users.md)
* [Éléments de l’e-mail d’invitation de B2B Collaboration](active-directory-b2b-invitation-email.md)
* [Utilisation d’une invitation B2B Collaboration](active-directory-b2b-redemption-experience.md)
* [Attribution de licences Azure AD B2B Collaboration](active-directory-b2b-licensing.md)
* [Résolution des problèmes d’Azure Active Directory B2B Collaboration](active-directory-b2b-troubleshooting.md)
* [Questions fréquemment posées (FAQ) sur Azure Active Directory B2B Collaboration](active-directory-b2b-faq.md)
* [API et personnalisation d’Azure Active Directory B2B Collaboration](active-directory-b2b-api.md)
* [Authentification multifacteur pour les utilisateurs B2B Collaboration](active-directory-b2b-mfa-instructions.md)
* [Index d’articles pour la gestion des applications dans Azure Active Directory](active-directory-apps-index.md)



<!--HONumber=Feb17_HO2-->


