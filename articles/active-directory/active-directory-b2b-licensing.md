---
title: "Guide d’attribution de licences pour Azure Active Directory B2B Collaboration | Microsoft Docs"
description: "Azure Active Directory B2B Collaboration nécessite des licences Azure AD payantes selon les fonctionnalités que vous souhaitez mettre à disposition de vos utilisateurs B2B Collaboration"
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
ms.date: 02/09/2017
ms.author: sasubram
translationtype: Human Translation
ms.sourcegitcommit: a4e59dfa8a098f63c3173176c4d2675d6a59af00
ms.openlocfilehash: 59a7fe2d1eea948f1452f21d2185e6bdf93dc41f


---

# <a name="azure-active-directory-b2b-collaboration-licensing-guidance"></a>Guide d’attribution de licences pour Azure Active Directory B2B Collaboration

Azure Active Directory (Azure AD) B2B Collaboration met à disposition des utilisateurs invités dans le client Azure AD un ensemble sélectionné de fonctionnalités Azure AD existantes. Par conséquent, les utilisateurs invités d’Azure AD B2B Collaboration disposeront d’une licence par le biais des licences Azure AD. Cette licence s’aligne sur les niveaux de licence Gratuit, De base et Premium P1/P2 existants comme indiqué ici : https://azure.microsoft.com/en-us/pricing/details/active-directory/.

L’invitation d’utilisateurs B2B et leur attribution à une application dans Azure AD n’entraînent aucun frais. En outre, les utilisateurs B2B bénéficient gratuitement de 10 applications maximales par utilisateur invité et de 3 rapports de base, conformément au niveau « Gratuit » d’Azure AD.
Les fonctionnalités Azure AD payantes, étendues aux utilisateurs B2B à l’aide de la fonctionnalité de collaboration B2B, devront disposer d’une licence par le biais de licences Azure AD payantes (De base, Premium P1 ou Premium P2, selon les fonctionnalités qui seront utilisées). Le client à l’origine de l’invitation obtiendra 5 droits d’utilisateur B2B avec chaque licence Azure AD payante. Autrement dit, chaque licence Azure AD payante qui fournit des droits à un utilisateur employé dans un client, inclura aussi désormais des droits pour 5 utilisateurs B2B invités dans le client.

## <a name="licensing-examples"></a>Exemples d’attribution de licences
- Un client souhaite inviter 100 utilisateurs B2B dans son client Azure AD. Il utilisera la configuration et la gestion de l’accès basées sur le groupe pour tous les utilisateurs, mais 50 de ces utilisateurs nécessiteront également l’authentification multifacteur et l’accès conditionnel. Dans ce cas, le client doit acheter 10 licences De base Azure AD et 10 licences Premium P1 Azure AD pour répondre correctement aux besoins de tous les utilisateurs B2B. De même, si le client à l’origine de l’invitation envisage d’utiliser les fonctionnalités de protection de l’identité pour les utilisateurs B2B, alors il doit avoir suffisamment de licences Premium P2 Azure AD pour couvrir tous ces utilisateurs B2B en respectant un ratio de 5 à 1.
- Un client a 10 employés qui disposent tous d’une licence Premium P1 Azure AD. Il veut désormais inviter 60 utilisateurs B2B, qui nécessiteront tous l’authentification multifacteur. Selon la règle d’attribution de licences de 5 à 1, le client doit disposer d’au moins 12 licences Premium P1 Azure AD pour couvrir les 60 utilisateurs B2B Collaboration. Dans la mesure où il dispose déjà de 10 licences Premium P1 pour ses 10 employés, il possède déjà les droits pour inviter 50 utilisateurs B2B avec des fonctionnalités de niveau Premium P1 telles que l’authentification multifacteur. Par conséquent, dans cet exemple, le client doit acheter 2 licences Premium P1 supplémentaires afin de couvrir les 10 autres utilisateurs B2B Collaboration.

> [!NOTE]
Il n’est pas utile d’attribuer des licences aux utilisateurs B2B pour activer ces droits d’utilisateur B2B Collaboration.

Le client qui possède le client à l’origine de l’invitation doit déterminer combien d’utilisateurs B2B Collaboration nécessitent des fonctionnalités Azure AD payantes et, selon qu’il s’agit de fonctionnalités de niveau De base, Premium P1 ou Premium P2, les clients doivent avoir le nombre suffisant de licences Azure AD appropriées payantes pour couvrir leurs utilisateurs B2B Collaboration en respectant un ratio de 5 à 1. Si une entreprise a besoin de droits d’utilisateur B2B Collaboration supplémentaires, elle doit acheter les licences Azure AD payantes nécessaires.

## <a name="additional-licensing-details"></a>Informations supplémentaires sur l’attribution de licences
- B2B Collaboration peut fournir des fonctionnalités différentes à différents utilisateurs B2B Collaboration selon le modèle existant des éditions Azure AD.
- Chaque licence Azure AD payante inclut des droits pour 5 utilisateurs B2B Collaboration (modèle 5 à 1).
- Il est inutile d’attribuer des licences aux comptes d’utilisateur B2B. Le calcul et la création de rapports se feront automatiquement.
- Chaque utilisateur invité obtient les droits de l’édition gratuite d’Azure AD si aucune licence Azure AD payante n’existe dans le client.
- Si un utilisateur B2B Collaboration dispose d’une licence Azure AD payante en tant qu’employé de son organisation, il n’utilisera pas l’une des licences B2B Collaboration du client à l’origine de l’invitation.

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



<!--HONumber=Feb17_HO2-->


