---
title: Partage externe dans Office 365 et Azure Active Directory B2B Collaboration | Microsoft Docs
description: "référence de mappage des revendications pour Azure Active Directory B2B Collaboration"
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
ms.date: 02/06/2017
ms.author: sasubram
translationtype: Human Translation
ms.sourcegitcommit: d3f68340592d9032999ecb5cc16ac1fedcce4c33
ms.openlocfilehash: 716d19be09085db91c17d7ed76422e05d164d137


---

# <a name="office-365-external-sharing-and-azure-active-directory-b2b-collaboration"></a>Partage externe dans Office 365 et Azure Active Directory B2B Collaboration

Le partage externe dans Office&365; (OneDrive, SharePoint Online, groupes unifiés, etc.) et à l’aide d’Azure Active Directory (Azure AD) B2B Collaboration revient techniquement au même. Tous les partages externes, (à l’exception de OneDrive / SharePoint Online), notamment les **invités dans des groupes unifiés** utilisent déjà l’API d'invitation Azure AD B2B Collaboration pour le partage.

OneDrive/SharePoint Online (ODSP) possède un gestionnaire d’invitation distinct, car la prise en charge par ODSP du partage externe a démarré avant qu’il ne soit automatiquement pris en charge dans le cadre de l’infrastructure Azure AD. Au fil du temps, plusieurs fonctionnalités ont été ajoutées au partage externe ODSP, ce qui a attiré de des millions d'utilisateurs exploitant le modèle de partage intégré. Nous travaillons avec ODSP à intégrer les API d’invitation Azure AD B2B (abordées dans cette documentation), pour exploiter tous les avantages de bout en bout toutes les innovations dans les expériences proposées par Azure AD. En attendant, il existe quelques différences subtiles entre le fonctionnement du partage externe ODSP et le fonctionnement d’Azure AD B2B :

1. ODSP ajoute l’utilisateur au répertoire une fois qu'il a utilisé son invitation. Par conséquent, vous ne verrez pas l'utilisateur dans le portail Azure jusqu'à ce qu'il utilise son invitation. Si un utilisateur est invité par un autre site dans l’intervalle, une nouvelle invitation est générée. Toutefois, lorsque vous utilisez B2B Collaboration, nous ajoutons l’utilisateur immédiatement à l'invitation afin qu’il soit affiché partout.

2. L’expérience d'utilisation de l'invitation dans ODSP diffère de celle de B2B Collaboration.

3. Toutefois, lorsque l’utilisateur a utilisé l’invitation, les deux expériences sont similaires.

4. Les utilisateurs invités par B2B Collaboration peuvent être sélectionnés dans les boîtes de dialogue de partage ODSP. Les utilisateurs invités par ODSP apparaissent également dans Azure AD une fois qu’ils ont utilisé leur invitation.

5. Pour utiliser le partage externe dans ODSP avec B2B Collaboration d'une manière gérée, administrée et contrôlée, définissez le paramètre de partage ODSP externe sur **Autoriser le partage uniquement avec des utilisateurs externes figurant déjà dans le répertoire**. Les utilisateurs peuvent accéder à des sites partagés de manière externe et choisir parmi les collaborateurs externes ajoutés par l'administrateur. L’administrateur peut ajouter les collaborateurs externes par le biais des API d’invitation B2B Collaboration.

![le paramètre de partage ODSP externe](media/active-directory-b2b-o365-external-user/odsp-sharing-setting.png)

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
* [Limitations actuelles de B2B Collaboration](active-directory-b2b-current-limitations.md)



<!--HONumber=Feb17_HO1-->


