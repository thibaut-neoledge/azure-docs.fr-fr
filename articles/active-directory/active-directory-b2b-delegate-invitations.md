---
title: "Déléguer des invitations pour Azure Active Directory B2B Collaboration | Microsoft Docs"
description: "Les propriétés de l’utilisateur Azure Active Directory B2B Collaboration sont configurables"
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
ms.sourcegitcommit: 0c05cd490ee9125f7e5182cb502db6f4e9390094
ms.openlocfilehash: 6c6d757a770613498bedca0f8f3a965241d692eb


---

# <a name="delegate-invitations-for-azure-active-directory-b2b-collaboration"></a>Déléguer des invitations pour Azure Active Directory B2B Collaboration

Avec l’actualisation de la version préliminaire publique d’Azure Active Directory (Azure AD) B2B Collaboration, il n’est pas nécessaire d’être un administrateur général pour inviter des utilisateurs. Vous pouvez contrôler qui peut envoyer des invitations à l’aide de stratégies et déléguer les invitations aux utilisateurs qui disposent des rôles autorisés à inviter. Le rôle Inviteur d’invités est une nouvelle façon importante de déléguer les invitations d’utilisateurs invités.

## <a name="guest-inviter-role"></a>Rôle Inviteur d’invités
Il est possible d’affecter l’utilisateur au rôle Inviteur d’invités pour envoyer des invitations. Vous n’êtes pas obligé d’être membre du rôle Administrateur général pour envoyer des invitations. Par défaut, les utilisateurs normaux peuvent également appeler l’API d’invitation, sauf si l’administrateur général a désactivé les invitations pour les utilisateurs normaux. Pour ce faire, vous pouvez utiliser le portail Azure et également PowerShell.

Voici un exemple illustrant l’ajout d’un utilisateur au rôle Inviteur d’invités via PowerShell :

```
Add-MsolRoleMember -RoleObjectId 95e79109-95c0-4d8e-aee3-d01accf2d47b -RoleMemberEmailAddress <RoleMemberEmailAddress >
```

## <a name="controlling-who-can-invite"></a>Contrôler qui peut inviter

![contrôler comment inviter](media/active-directory-b2b-delegate-invitations/control-who-to-invite.png)

Avec Azure AD B2B Collaboration, nous autorisons l’administrateur du client à définir les stratégies d’invitation suivantes :

1. Désactiver les invitations
2. Seuls les administrateurs et les utilisateurs membres du rôle Inviteur d’invités peuvent inviter
3. Les administrateurs, le rôle Inviteur d’invités et les membres peuvent inviter
4. Tous les utilisateurs, notamment les invités, peuvent inviter

Par défaut, les clients sont définis sur l’option 4 (tous les utilisateurs, notamment les invités, peuvent inviter des utilisateurs B2B).

## <a name="next-steps"></a>Étapes suivantes

Consultez les autres articles sur la collaboration B2B d'Azure AD :

* [Qu’est-ce qu’Azure AD B2B Collaboration ?](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Propriétés de l’utilisateur B2B Collaboration](active-directory-b2b-user-properties.md)
* [Ajout d’un utilisateur B2B Collaboration à un rôle](active-directory-b2b-add-guest-to-role.md)
* [Groupes dynamiques et B2B Collaboration](active-directory-b2b-dynamic-groups.md)
* [Code B2B Collaboration et exemples PowerShell](active-directory-b2b-code-samples.md)
* [Configurer des applications SaaS pour B2B Collaboration](active-directory-b2b-configure-saas-apps.md)
* [Jetons utilisateur B2B Collaboration](active-directory-b2b-user-token.md)
* [Mappage des revendications utilisateur B2B Collaboration](active-directory-b2b-claims-mapping.md)
* [Partage externe d’Office 365](active-directory-b2b-o365-external-user.md)
* [Limitations actuelles de B2B Collaboration](active-directory-b2b-current-limitations.md)



<!--HONumber=Feb17_HO1-->


