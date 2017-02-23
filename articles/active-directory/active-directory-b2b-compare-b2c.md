---

title: Comparer B2B Collaboration et B2C dans Azure Active Directory | Microsoft Docs
description: "Quelle est la différence entre Azure Active Directory B2B Collaboration et Azure AD B2C ?"
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
ms.date: 02/08/2017
ms.author: sasubram
translationtype: Human Translation
ms.sourcegitcommit: 72e7e57185d882a5f3243e2705aed54ee8347798
ms.openlocfilehash: 5d157a936317bbf66cfc3634d4d08ea3483d137b


---

# <a name="compare-b2b-collaboration-and-b2c-in-azure-active-directory"></a>Comparer B2B Collaboration et B2C dans Azure Active Directory

Azure Active Directory (Azure AD) B2B Collection et Azure AD B2C vous permettent de travailler avec des utilisateurs externes dans Azure AD. En quoi se différencient-ils ?


Fonctionnalités de B2B Collaboration |     Offre autonome d’Azure AD B2C
-------- | --------
Public visé : les entreprises qui souhaitent fournir un accès aux données, ressources et applications d’entreprise à des utilisateurs de n’importe quelle autre organisation, à l’aide de l’identité de leur choix. | Public visé : applications web et mobiles destinées aux clients qui ciblent vos clients (particuliers, citoyens et clients institutionnels ou d’entreprise, et non vos employés ou collaborateurs externes), à l’aide de l’identité de leur choix.
Identités prises en charge : employés ou partenaires disposant d’un compte professionnel ou scolaire ou n’importe quelle adresse de messagerie. La fédération directe sera bientôt prise en charge.  | Identités prises en charge : utilisateurs consommateurs disposant de comptes d’application locaux (n’importe quel nom d’utilisateur ou adresse de messagerie) ou toute identité sociale prenant en charge la fédération directe.
Répertoire dans lequel les utilisateurs partenaire se trouvent : les utilisateurs partenaire de l’organisation externe sont gérés dans le même répertoire que les employés, mais font l’objet d’une annotation spéciale. Ces utilisateurs externes peuvent être gérés de la même façon que les employés, ils peuvent être ajoutés aux mêmes groupes, etc.  | Répertoire dans lequel les entités d’utilisateur client se trouvent : dans le répertoire de l’application. Elles sont gérées indépendamment du répertoire de partenaires et d’employés de l’entreprise (le cas échéant).
L’authentification unique (SSO) auprès de toutes les applications connectées à Azure AD (y compris les applications locales) est prise en charge (par exemple, Office 365), ainsi que d’autres applications Microsoft et SaaS non Microsoft (telles que Salesforce, Box, Workday, etc.).  |  L’authentification unique auprès d’applications détenues par le client dans les clients Azure AD B2C est prise en charge. L’authentification unique auprès d’Office 365 ou d’autres applications Microsoft ou SaaS non Microsoft n’est pas prise en charge.
Cycle de vie partenaire : géré par l’organisation hôte/qui invite.  | Cycle de vie client : libre-service ou géré par l’application.
Stratégie de sécurité et conformité : gérée par l’organisation hôte/qui invite.  | Stratégie de sécurité et conformité : gérée par l’application.
Marque : la marque de l’organisation hôte/qui invite est utilisée.  |    Marque : gérée par l’application. En général, la marque du produit apparaît, avec l’organisation en arrière-plan.
Plus d’informations : [Billet de blog](https://blogs.technet.microsoft.com/enterprisemobility/2017/02/01/azure-ad-b2b-new-updates-make-cross-business-collab-easy/), [Documentation](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)  | Plus d’informations : [Page produit](https://azure.microsoft.com/en-us/services/active-directory-b2c/), [Documentation](https://docs.microsoft.com/en-us/azure/active-directory-b2c/)


### <a name="next-steps"></a>Étapes suivantes

Consultez les autres articles sur la collaboration B2B d'Azure AD :

* [Qu'est-ce que la collaboration B2B d'Azure AD ?](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Propriétés de l’utilisateur B2B Collaboration](active-directory-b2b-user-properties.md)
* [Ajout d’un utilisateur B2B Collaboration à un rôle](active-directory-b2b-add-guest-to-role.md)
* [Déléguer des invitations B2B Collaboration](active-directory-b2b-delegate-invitations.md)
* [Groupes dynamiques et B2B Collaboration](active-directory-b2b-dynamic-groups.md)
* [Configurer des applications SaaS pour B2B Collaboration](active-directory-b2b-configure-saas-apps.md)
* [Jetons utilisateur B2B Collaboration](active-directory-b2b-user-token.md)
* [Mappage des revendications utilisateur B2B Collaboration](active-directory-b2b-claims-mapping.md)
* [Partage externe d’Office 365](active-directory-b2b-o365-external-user.md)
* [Limitations actuelles de B2B Collaboration](active-directory-b2b-current-limitations.md)
* [Obtention de la prise en charge pour B2B Collaboration](active-directory-b2b-support.md)



<!--HONumber=Feb17_HO2-->


