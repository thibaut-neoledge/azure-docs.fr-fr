---
title: Ajout de nouveaux utilisateurs à la version préliminaire d’Azure Active Directory | Microsoft Docs
description: Expliquez comment ajouter de nouveaux utilisateurs, ou comment modifier les informations d’un utilisateur dans Azure Active Directory.
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
# Ajout de nouveaux utilisateurs à la version préliminaire d’Azure Active Directory
> [!div class="op_single_selector"]
> * [Portail Azure](active-directory-users-create-azure-portal.md)
> * [Portail Azure Classic](active-directory-create-users.md)
> 
> 

Cet article explique comment ajouter de nouveaux utilisateurs de votre organisation dans à la version préliminaire d’Azure Active Directory (Azure AD). [Nouveautés de la version préliminaire](active-directory-preview-explainer.md)

1. Connectez-vous au [portail Azure](https://portal.azure.com) en utilisant un compte d’administrateur général pour le répertoire.
2. Sélectionnez **Plus de services**, saisissez **Utilisateurs et groupes** dans la zone de texte, puis sélectionnez **Entrée**.
   
   ![Ouvrir la gestion des utilisateurs](./media/active-directory-users-create-azure-portal/create-users-user-management.png)
3. Dans le panneau **Utilisateurs et groupes**, sélectionnez **Tous les utilisateurs**, puis sélectionnez **Ajouter**.
   
   ![Sélection de la commande Ajouter](./media/active-directory-users-create-azure-portal/create-users-add-command.png)
4. Saisissez les détails de l’utilisateur, dont son **nom** et son **nom d’utilisateur**. La partie du nom de domaine du nom d’utilisateur doit être le nom de domaine initial par défaut, « foo.onmicrosoft.com », ou un nom de domaine vérifié, non fédéré, comme « contoso.com ».
5. Copiez ou notez d’une autre façon le mot de passe généré de sorte à pouvoir le fournir à l’utilisateur une fois ce processus terminé.
6. Si vous le souhaitez, vous pouvez ouvrir et remplir les informations dans le panneau **Profil**, le panneau **Groupes**, ou le panneau **Rôle de répertoire** pour l’utilisateur. Pour plus d’informations sur les utilisateurs et les rôles d’administrateur, consultez la page [Attribution de rôles d’administrateur dans Azure AD](active-directory-assign-admin-roles.md).
7. Dans le panneau **Utilisateur**, sélectionnez **Créer**.
8. Distribuez de manière sécurisée le mot de passe généré au nouvel utilisateur afin qu’il puisse se connecter.

## Étapes suivantes
* [Ajouter un utilisateur externe](active-directory-users-create-external-azure-portal.md)
* [Réinitialiser le mot de passe d’un utilisateur dans le nouveau portail Azure](active-directory-users-reset-password-azure-portal.md)
* [Modifier les informations de travail d’un utilisateur](active-directory-users-work-info-azure-portal.md)
* [Gérer les profils utilisateur](active-directory-users-profile-azure-portal.md)
* [Suppression d’un utilisateur dans Azure AD](active-directory-users-delete-user-azure-portal.md)
* [Affecter un utilisateur à un rôle dans Azure AD](active-directory-users-assign-role-azure-portal.md)

<!---HONumber=AcomDC_0914_2016-->