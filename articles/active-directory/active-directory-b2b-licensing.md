---
title: "Guide d’attribution de licences pour Azure Active Directory B2B Collaboration | Microsoft Docs"
description: "Azure Active Directory B2B Collaboration ne nécessite pas de licences Azure AD payées, mais vous pouvez également obtenir des fonctionnalités payantes pour les utilisateurs invités B2B."
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
ms.date: 06/29/2017
ms.author: curtand
ms.reviewer: sasubram
ms.custom: it-pro
ms.translationtype: Human Translation
ms.sourcegitcommit: 1500c02fa1e6876b47e3896c40c7f3356f8f1eed
ms.openlocfilehash: 972b37e0009b3096691784d785901b0a5585eb4c
ms.contentlocale: fr-fr
ms.lasthandoff: 06/30/2017


---

# <a name="azure-active-directory-b2b-collaboration-licensing-guidance"></a>Guide d’attribution de licences pour Azure Active Directory B2B Collaboration

Utilisez B2B Collaboration pour inviter des utilisateurs invités dans votre locataire Azure AD afin de leur fournir l’accès aux services et aux ressources Azure AD que vous mettez à disposition.  

L’invitation d’utilisateurs B2B et leur attribution à une application dans Azure AD n’entraînent aucun frais. Jusqu’à 10 applications par utilisateur invité et trois rapports de base sont également gratuits pour les utilisateurs B2B Collaboration. Si des licences pertinentes sont affectées à votre utilisateur dans le locataire Azure AD du partenaire, il disposera également d’une licence dans votre locataire.

Si vous souhaitez fournir l’accès à des fonctionnalités Azure AD payantes, vous devez accorder à ces utilisateurs invités B2B des licences Azure AD appropriées. Un locataire invitant avec une licence Azure payée peut accorder des droits d’utilisateur B2B Collaboration à cinq utilisateurs invités supplémentaires. 

## <a name="licensing-examples"></a>Exemples d’attribution de licences
- Un client souhaite inviter 100 utilisateurs B2B Collaboration à son locataire Azure AD. Le client affecte la gestion de l’accès et l’approvisionnement pour tous les utilisateurs, mais 50 utilisateurs nécessitent également l’authentification multifacteur et l’accès conditionnel. Ce client doit acheter 10 licences De base Azure AD et 10 licences Premium P1 Azure AD pour répondre correctement aux besoins de ces utilisateurs B2B. Si le client envisage d’utiliser les fonctionnalités de protection de l’identité pour les utilisateurs B2B, il doit avoir des licences Premium P2 Azure AD pour couvrir les utilisateurs invités en respectant le même ratio de 5 à 1.
- Un client a 10 employés qui disposent tous d’une licence Premium P1 Azure AD. Il veut désormais inviter 60 utilisateurs B2B, qui nécessitent tous l’authentification multifacteur. Selon la règle d’attribution de licences de 5 à 1, le client doit disposer d’au moins 12 licences Premium P1 Azure AD pour couvrir les 60 utilisateurs B2B Collaboration. Puisqu’il dispose déjà de 10 licences Premium P1 pour ses 10 employés, il peut inviter 50 utilisateurs B2B avec des fonctionnalités de niveau Premium P1 telles que l’authentification multifacteur. Dans cet exemple, il doit acheter deux licences Premium P1 supplémentaires afin de couvrir les 10 autres utilisateurs B2B Collaboration.

> [!NOTE]
> Il n’existe encore aucun moyen d’attribuer directement des licences aux utilisateurs B2B pour activer ces droits d’utilisateur B2B Collaboration.

C’est le client propriétaire du locataire invitant qui doit déterminer combien d’utilisateurs B2B Collaboration ont besoin de fonctionnalités Azure AD payantes. En fonction des fonctionnalités Azure AD payantes que vous souhaitez mettre à disposition de vos utilisateurs invités, vous devez avoir suffisamment de licences Azure AD payées pour couvrir les utilisateurs B2B Collaboration avec le ratio de 5 à 1. 

## <a name="additional-licensing-details"></a>Informations supplémentaires sur l’attribution de licences
- Il est inutile d’attribuer des licences aux comptes d’utilisateur B2B. D’après le ratio de 5 à 1, l’accord des licences est calculé et signalé automatiquement.
- S’il n’existe aucune licence Azure AD payée dans le locataire, chaque utilisateur invité obtient les droits de l’édition Azure AD Free.
- Si un utilisateur B2B Collaboration dispose déjà d’une licence Azure AD payée délivrée par son organisation, il n’utilise pas l’une des licences B2B Collaboration du locataire à l’origine de l’invitation.

## <a name="advanced-discussion-what-are-the-licensing-considerations-when-we-add-users-from-a-conglomerate-organization-as-members-using-your-apis"></a>Discussion approfondie : quelles sont les considérations relatives aux licences lorsque nous ajoutons des utilisateurs à partir d’un conglomérat en tant que « membres » à l’aide de vos API ?
Un utilisateur invité B2B est invité à partir d’une organisation partenaire pour travailler avec l’organisation hôte. En règle générale, aucun autre cas n’est considéré comme B2B, même s’il utilise des fonctionnalités B2B. Nous allons examiner deux cas en particulier :

1. Si un hôte invite un employé à l’aide d’une adresse client
  1. Ce scénario n’est pas conforme à nos stratégies de gestion des licences et n’est actuellement pas recommandé.

2. Si une organisation hôte ajoute un utilisateur à partir d’un autre conglomérat
  1. Dans ce cas, l’utilisateur est invité à l’aide des API B2B, mais il ne s’agit généralement pas de B2B. Dans l’idéal, ces organisations doivent inviter les utilisateurs des autres organisations en tant que membres (notre API le permet). Dans ce cas, les licences doivent être assignées à ces membres pour leur permettre d’accéder aux ressources de l’organisation à l’origine de l’invitation.

  2. Certaines organisations utilisent une autre stratégie et ajoutent les utilisateurs de l’autre organisation comme « Invité ». Il existe deux cas :
      * Le conglomérat utilise déjà Azure AD et les utilisateurs invités sont concédés sous une licence de l’autre organisation : dans ce cas, les utilisateurs invités n’ont pas besoin de suivre la formule 1 à 5 présentée précédemment dans ce document. 

      * Le conglomérat n’utilise pas Azure AD ou ne dispose pas des licences adéquates : dans ce cas, suivez la formule 1 à 5 présentée précédemment dans ce document.

## <a name="next-steps"></a>Étapes suivantes

Consultez les autres articles sur la collaboration B2B d'Azure AD :

* [Qu’est-ce qu’Azure AD B2B Collaboration ?](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Comment les administrateurs Azure Active Directory ajoutent-ils des utilisateurs B2B Collaboration ?](active-directory-b2b-admin-add-users.md)
* [Comment les professionnels de l’information ajoutent-ils des utilisateurs B2B Collaboration ?](active-directory-b2b-iw-add-users.md)
* [Éléments de l’e-mail d’invitation de B2B Collaboration](active-directory-b2b-invitation-email.md)
* [Utilisation d’une invitation B2B Collaboration](active-directory-b2b-redemption-experience.md)
* [Résolution des problèmes d’Azure Active Directory B2B Collaboration](active-directory-b2b-troubleshooting.md)
* [Questions fréquemment posées (FAQ) sur Azure Active Directory B2B Collaboration](active-directory-b2b-faq.md)
* [API et personnalisation d’Azure Active Directory B2B Collaboration](active-directory-b2b-api.md)
* [Authentification multifacteur pour les utilisateurs B2B Collaboration](active-directory-b2b-mfa-instructions.md)
* [Ajouter des utilisateurs B2B Collaboration sans invitation](active-directory-b2b-add-user-without-invite.md)
* [Index d’articles pour la gestion des applications dans Azure Active Directory](active-directory-apps-index.md)

