---
title: "Portail d’inscription en libre-service pour Azure Active Directory B2B Collaboration | Microsoft Docs"
description: "Azure Active Directory B2B Collaboration prend en charge les relations interentreprises en permettant aux partenaires commerciaux d’accéder de façon sélective à vos applications d’entreprise"
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
ms.date: 04/11/2017
ms.author: sasubram
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: f629eeb6f12c8785cab2585190f70e98b02fa5b4
ms.lasthandoff: 04/12/2017


---


# <a name="self-service-sign-up-portal-for-azure-ad-b2b-collaboration"></a>Portail d’inscription en libre-service pour Azure AD B2B Collaboration

Les clients peuvent faire beaucoup d’opérations avec les fonctionnalités prédéfinies du produit, qui sont exposées via nos expériences d’administration informatique dans le [portail Azure](https://portal.azure.com) et notre [volet d’accès aux applications](https://myapps.microsoft.com) pour les non-administrateurs. Mais nous sommes également conscients que les entreprises ont besoin de personnaliser le flux de travail d’intégration pour les utilisateurs B2B en fonction des besoins de leur organisation. Elles le peuvent avec [nos API](https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation).

En discutant avec la plupart de nos clients, nous avons constaté un besoin commun de sortir du lot. Ce fut le cas d’une organisation invitante qui ne pouvait (ou ne voulait) pas savoir à l’avance quels collaborateurs externes auraient besoin de pouvoir accéder à ses ressources. Elle souhaitait disposer d’un moyen de permettre à des utilisateurs d’entreprises partenaires de s’auto-inscrire avec un ensemble de stratégies que l’organisation invitante contrôlerait. Cela est possible grâce à nos API. Nous avons donc publié un projet sur Github qui faisait exactement cela : [exemple de projet Github](https://github.com/Azure/active-directory-dotnet-graphapi-b2bportal-web).

Notre projet Github montre comment les organisations peuvent utiliser nos API afin d’offrir à leurs partenaires approuvés des fonctionnalités d’inscription en libre-service basées sur une stratégie, avec des règles déterminant les applications auxquelles ils doivent pouvoir accéder. De cette façon, les utilisateurs partenaires peuvent accéder aux ressources appropriées quand ils en ont besoin, en toute sécurité, mais sans que personne au sein de l’organisation invitante doive les intégrer manuellement. Vous pouvez facilement déployer le projet en un clic sur un bouton dans un abonnement Azure de votre choix. Essayez !

## <a name="as-is-code"></a>Code en l’état

N’oubliez pas que ce code est mis à disposition en tant qu’exemple pour illustrer l’utilisation de l’API Invitation Azure Active Directory B2B. Il doit être personnalisé par votre équipe de développement ou un partenaire, et doit être révisé avant tout déploiement en environnement de production.

## <a name="next-steps"></a>Étapes suivantes

Consultez les autres articles sur la collaboration B2B d'Azure AD :
* [Qu’est-ce qu’Azure AD B2B Collaboration ?](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Comment les administrateurs Azure Active Directory ajoutent-ils des utilisateurs B2B Collaboration ?](active-directory-b2b-admin-add-users.md)
* [Comment les professionnels de l’information ajoutent-ils des utilisateurs B2B Collaboration ?](active-directory-b2b-iw-add-users.md)
* [Éléments de l’e-mail d’invitation de B2B Collaboration](active-directory-b2b-invitation-email.md)
* [Utilisation d’une invitation B2B Collaboration](active-directory-b2b-redemption-experience.md)
* [Attribution de licences Azure AD B2B Collaboration](active-directory-b2b-licensing.md)
* [Résolution des problèmes d’Azure Active Directory B2B Collaboration](active-directory-b2b-troubleshooting.md)
* [Questions fréquemment posées (FAQ) sur Azure Active Directory B2B Collaboration](active-directory-b2b-faq.md)
* [Authentification multifacteur pour les utilisateurs B2B Collaboration](active-directory-b2b-mfa-instructions.md)
* [Ajouter des utilisateurs B2B Collaboration sans invitation](active-directory-b2b-add-user-without-invite.md)
* [Index d’articles pour la gestion des applications dans Azure Active Directory](active-directory-apps-index.md)
