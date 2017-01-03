---
title: "Changement d’attribut d’objet d’utilisateur externe pour la version préliminaire d’Azure Active Directory B2B Collaboration | Microsoft Docs"
description: "Azure Active Directory B2B prend en charge les relations interentreprises en permettant aux partenaires commerciaux d’accéder de façon sélective à vos applications d’entreprise"
services: active-directory
documentationcenter: 
author: viv-liu
manager: cliffdi
editor: 
tags: 
ms.assetid: 4ede9c7b-3830-42f2-b73d-cbecbcc7db64
ms.service: active-directory
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 05/09/2016
ms.author: viviali
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: d57fd7d6dcd186d1e79528c716d8bf81ad868ae5


---
# <a name="azure-ad-b2b-collaboration-preview-external-user-object-attribute-changes"></a>Version préliminaire d’Azure AD B2B Collaboration : changement d’attribut d’objet utilisateur externe
Chaque utilisateur d’un annuaire Azure AD est représenté par un objet utilisateur. L’objet utilisateur dans Azure AD subit des modifications d’attribut aux différentes étapes du flux d’échange d’invités de la collaboration B2B. L’objet utilisateur qui représente l’utilisateur partenaire dans l’annuaire possède des attributs qui changent au moment de l’échange lorsque le partenaire utilisateur clique sur le lien dans le message électronique d’invitation. Plus précisément :

* Les attributs **SignInName** et **AltSecId** sont remplis
* L’attribut **DisplayName** remplace le nom d’utilisateur principal (user_fabrikam.com#EXT#@contoso.com) par le nom de connexion (user@fabrikam.com)

Le suivi de ces attributs dans Azure AD peut vous aider à déterminer si un utilisateur partenaire a échangé son invitation de collaboration B2B.

## <a name="related-articles"></a>Articles connexes
Consultez les autres articles sur la collaboration B2B d'Azure AD :

* [Qu'est-ce que la collaboration B2B d'Azure AD ?](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Fonctionnement](active-directory-b2b-how-it-works.md)
* [Procédure pas à pas](active-directory-b2b-detailed-walkthrough.md)
* [Référence du format de fichier CSV](active-directory-b2b-references-csv-file-format.md)
* [Format du jeton utilisateur externe](active-directory-b2b-references-external-user-token-format.md)
* [Limites actuelles de la version préliminaire](active-directory-b2b-current-preview-limitations.md)
* [Index d’articles pour la gestion des applications dans Azure Active Directory](active-directory-apps-index.md)




<!--HONumber=Dec16_HO4-->


