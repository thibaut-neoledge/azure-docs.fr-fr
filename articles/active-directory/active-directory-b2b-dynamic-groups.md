---
title: Groupes dynamiques et Azure Active Directory B2B Collaboration | Microsoft Docs
description: "Azure Active Directory B2B Collaboration peut être utilisé avec des groupes dynamiques Azure AD"
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
ms.date: 05/04/2017
ms.author: sasubram
ms.translationtype: Human Translation
ms.sourcegitcommit: e72275ffc91559a30720a2b125fbd3d7703484f0
ms.openlocfilehash: a694d01281cfdc4559f779f18b92d0412d59cf45
ms.contentlocale: fr-fr
ms.lasthandoff: 05/05/2017


---

# <a name="dynamic-groups-and-azure-active-directory-b2b-collaboration"></a>Groupes dynamiques et Azure Active Directory B2B Collaboration

## <a name="what-are-dynamic-groups"></a>Qu'est-ce-que les groupes dynamiques ?
La configuration dynamique de l’appartenance au groupe de sécurité pour Azure Active Directory (Azure AD) est disponible dans le [portail Azure](https://portal.azure.com). Les administrateurs peuvent définir des règles pour remplir les groupes créés dans Azure Active Directory en fonction d'attributs utilisateur (par exemple, userType, département ou pays). Cela permet d'ajouter automatiquement des membres à un groupe de sécurité ou d'en supprimer en fonction des modifications apportées à leurs attributs. Ces groupes permettent d’accorder l’accès à des applications ou à des ressources cloud (telles que des sites et documents SharePoint), et d’attribuer des licences à des utilisateurs. En savoir plus sur les groupes dynamiques dans [Groupes dédiés dans Azure Active Directory](active-directory-accessmanagement-dedicated-groups.md).

Avec un abonnement AAD Premium P1 ou P2, le portail Azure vous permet désormais de créer des règles avancées pour activer des appartenances dynamiques plus complexes basées sur les attributs pour des groupes Azure Active Directory. Pour en savoir plus sur la création de règles avancées, consultez la rubrique [Utilisation d’attributs pour créer des règles avancées pour l’appartenance au groupe dans Azure Active Directory](active-directory-groups-dynamic-membership-azure-portal.md).

## <a name="what-are-the-built-in-dynamic-groups"></a>Que sont les groupes dynamiques intégrés ?
Le groupe dynamique **Tous les utilisateurs** permet à des administrateurs de locataires de créer un groupe contenant tous les utilisateurs dans le locataire en un seul clic. Par défaut, le groupe **Tous les utilisateurs** inclut tous les utilisateurs du répertoire, dont les invités et les utilisateurs externes.
Dans le nouveau portail d’administration Azure Active Directory, vous pouvez choisir d’activer le groupe **Tous les utilisateurs** dans l’affichage des paramètres de groupe.

![groupes intégrés](media/active-directory-b2b-dynamic-groups/built-in-groups.png)

En renforçant le paramètre par défaut Renforcement de groupe dynamique tous les utilisateurs par défaut, le groupe **Tous les utilisateurs** contient également les utilisateurs de collaboration B2B (invité). Si vous souhaitez sécuriser davantage votre groupe **Tous les utilisateurs** en supprimant les utilisateurs invités, vous pouvez le faire facilement en modifiant la règle basée sur les attributs du groupe **Tous les utilisateurs**. L’illustration suivante montre le groupe **Tous les utilisateurs** modifié de manière à exclure les invités.

![activer le groupe Tous les utilisateurs](media/active-directory-b2b-dynamic-groups/enable-all-users-group.png)

Il peut également s’avérer utile de créer un nouveau groupe dynamique qui contient uniquement des utilisateurs invités. Cela peut être très pratique pour le ciblage de stratégies (notamment les stratégies d’accès conditionnel) sur les utilisateurs invités.
Voici une illustration de ce à quoi peut ressembler un tel groupe :

![exclure les utilisateurs invités](media/active-directory-b2b-dynamic-groups/exclude-guest-users.png)

## <a name="next-steps"></a>Étapes suivantes

Consultez les autres articles sur la collaboration B2B d'Azure AD :

* [Qu’est-ce qu’Azure AD B2B Collaboration ?](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Propriétés de l’utilisateur B2B Collaboration](active-directory-b2b-user-properties.md)
* [Ajout d’un utilisateur B2B Collaboration à un rôle](active-directory-b2b-add-guest-to-role.md)
* [Déléguer des invitations B2B Collaboration](active-directory-b2b-delegate-invitations.md)
* [Code B2B Collaboration et exemples PowerShell](active-directory-b2b-code-samples.md)
* [Configurer des applications SaaS pour B2B Collaboration](active-directory-b2b-configure-saas-apps.md)
* [Jetons utilisateur B2B Collaboration](active-directory-b2b-user-token.md)
* [Mappage des revendications utilisateur B2B Collaboration](active-directory-b2b-claims-mapping.md)
* [Partage externe d’Office 365](active-directory-b2b-o365-external-user.md)
* [Limitations actuelles de B2B Collaboration](active-directory-b2b-current-limitations.md)

