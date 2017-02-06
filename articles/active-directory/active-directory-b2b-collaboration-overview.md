---
title: Azure Active Directory B2B Collaboration | Microsoft Docs
description: "La collaboration B2B Azure Active Directory permet aux partenaires professionnels d’accéder à vos applications d’entreprise. Chaque utilisateur est représenté par un compte Azure AD unique."
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 9f6a044c-0b36-4a1d-8080-33e28696c42a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/09/2016
ms.author: curtand
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 7c56dee52201c95c2a0677355e2b4edd2f113259


---
# <a name="azure-active-directory-b2b-collaboration"></a>Collaboration B2B Azure Active Directory
La collaboration B2B Azure Active Directory (Azure AD) vous permet d’activer l’accès à vos applications d’entreprise à partir des identités gérées par les partenaires. Vous pouvez créer des relations intersociétés en invitant et en autorisant des utilisateurs de sociétés partenaires à accéder à vos ressources. La complexité est réduite, car chaque entreprise se fédère une seule fois avec Azure Active Directory et chaque utilisateur est représenté par un seul compte Azure AD. Si vos partenaires commerciaux gèrent leurs comptes dans Azure AD, la sécurité est renforcée  car l’accès est refusé lorsque des utilisateurs du partenaire sont résiliés de leurs organisations et que l’accès involontaire via l’appartenance dans les répertoires internes est empêché. Pour les partenaires professionnels qui ne disposent pas déjà d’Azure AD, la collaboration B2B offre une expérience d’inscription rationalisée afin de fournir des comptes Azure AD à vos partenaires professionnels.

* Vos partenaires professionnels utilisent leurs propres informations de connexion, ce qui vous libère de la gestion d’un répertoire de partenaires externes et de la nécessité de supprimer l’accès lorsque des utilisateurs quittent l’organisation partenaire.
* Vous gérez l’accès à vos applications indépendamment du cycle de vie des comptes de vos partenaires professionnels. Par exemple, cela signifie que vous pouvez révoquer l’accès sans que le service informatique de votre partenaire professionnel ne fasse quoi que ce soit.

## <a name="capabilities"></a>Fonctionnalités
La collaboration B2B simplifie la gestion et améliore la sécurité de l’accès des partenaires aux ressources d’entreprise, y compris aux applications SaaS, comme Office 365, Salesforce et les services Azure et toutes les applications mobiles, cloud et locales prenant en charge les revendications. La collaboration B2B permet aux partenaires de gérer leurs propres comptes, et les entreprises peuvent appliquer des stratégies de sécurité pour l’accès des partenaires.

La collaboration B2B Azure Active Directory est facile à configurer avec une inscription simplifiée pour les partenaires de toutes tailles, même s’ils ne disposent pas de leur propre Azure Active Directory via un processus de vérification de messagerie. La gestion est également facile, sans répertoires externes ni configurations de fédérations de partenaires.

## <a name="b2b-collaboration-process"></a>Processus de collaboration B2B
1. La collaboration B2B Azure AD permet à un administrateur d’entreprise d’inviter et d’autoriser un ensemble d’utilisateurs externes en téléchargeant un fichier de valeurs séparées par des virgules (CSV) ne dépassant pas 2 000 lignes sur le portail de collaboration B2B.

   ![Boîte de dialogue de téléchargement de fichier CSV](./media/active-directory-b2b-collaboration-overview/upload-csv.png)
2. Le portail enverra des invitations par e-mail à ces utilisateurs externes.
3. L’utilisateur invité se connectera à un compte professionnel existant avec Microsoft (géré dans Azure AD) ou obtiendra un nouveau compte professionnel dans Azure AD.
4. Une fois connecté, l’utilisateur sera redirigé vers l’application qui a été partagée avec lui.

Les invitations envoyées aux adresses électroniques grand public (par exemple, Gmail ou [*comcast.net*](http://comcast.net/)) ne sont pas prises en charge pour le moment.

Pour plus d’informations sur le fonctionnement de la collaboration B2B, regardez [cette vidéo](http://aka.ms/aadshowb2b).

## <a name="next-steps"></a>Étapes suivantes
Consultez les autres articles sur la collaboration B2B d’Azure AD.

* [Qu’est-ce qu’Azure AD B2B Collaboration ?](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Fonctionnement](active-directory-b2b-how-it-works.md)
* [Procédure pas à pas](active-directory-b2b-detailed-walkthrough.md)
* [Référence du format de fichier CSV](active-directory-b2b-references-csv-file-format.md)
* [Format du jeton utilisateur externe](active-directory-b2b-references-external-user-token-format.md)
* [Modifications de l’attribut d’objet utilisateur externe](active-directory-b2b-references-external-user-object-attribute-changes.md)
* [Limites actuelles de la version préliminaire](active-directory-b2b-current-preview-limitations.md)
* [Index d’articles pour la gestion des applications dans Azure Active Directory](active-directory-apps-index.md)



<!--HONumber=Dec16_HO4-->


