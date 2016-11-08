---
title: Attribution de rôles d’administrateur à un utilisateur dans la version préliminaire d’Azure Active Directory | Microsoft Docs
description: Expliquez comment modifier les informations administratives d’un utilisateur dans Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: femila
editor: ''

ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/12/2016
ms.author: curtand

---
# Attribution de rôles d’administrateur à un utilisateur dans la version préliminaire d’Azure Active Directory
Cet article explique comment affecter un rôle administratif à un utilisateur dans la version préliminaire d’Azure Active Directory (Azure AD). [Nouveautés de la version préliminaire](active-directory-preview-explainer.md) Pour en savoir plus sur l’ajout de nouveaux utilisateurs dans votre organisation, voir [Ajout ou modification d’utilisateurs dans Azure Active Directory](active-directory-users-create-azure-portal.md). Par défaut, les utilisateurs ajoutés ne reçoivent pas d’autorisations d’administrateur, mais vous pouvez leur attribuer des rôles à tout moment.

## Affecter un rôle à un utilisateur
1. Connectez-vous au [portail Azure](https://portal.azure.com) en utilisant un compte d’administrateur général pour le répertoire.
2. Sélectionnez **Plus de services**, saisissez **Utilisateurs et groupes** dans la zone de texte, puis sélectionnez **Entrée**.
   
   ![Ouvrir la gestion des utilisateurs](./media/active-directory-users-assign-role-azure-portal/create-users-user-management.png)
3. Dans le panneau **Utilisateurs et groupes**, sélectionnez **Tous les utilisateurs**.
   
   ![Ouverture du panneau Tous les utilisateurs](./media/active-directory-users-assign-role-azure-portal/create-users-open-users-blade.png)
4. Sur le panneau **Utilisateurs et groupes - Tous les utilisateurs**, sélectionnez un utilisateur dans la liste.
5. Dans le panneau de l’utilisateur sélectionné, sélectionnez **Rôle de répertoire**, puis affectez l’utilisateur à un rôle dans la liste **Rôle de répertoire**. Pour plus d’informations sur les utilisateurs et les rôles d’administrateur, consultez la page [Attribution de rôles d’administrateur dans Azure AD](active-directory-assign-admin-roles.md).
   
      ![Affectation d’un utilisateur à un rôle](./media/active-directory-users-assign-role-azure-portal/create-users-assign-role.png)
6. Sélectionnez **Enregistrer**.

## Étapes suivantes
* [Ajouter un utilisateur](active-directory-users-create-azure-portal.md)
* [Réinitialiser le mot de passe d’un utilisateur dans le nouveau portail Azure](active-directory-users-reset-password-azure-portal.md)
* [Modifier les informations de travail d’un utilisateur](active-directory-users-work-info-azure-portal.md)
* [Gérer les profils utilisateur](active-directory-users-profile-azure-portal.md)
* [Suppression d’un utilisateur dans Azure AD](active-directory-users-delete-user-azure-portal.md)

<!---HONumber=AcomDC_0914_2016--->