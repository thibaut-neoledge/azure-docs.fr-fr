---
title: Gérer les groupes dont votre groupe est membre dans la version préliminaire d’Azure Active Directory | Microsoft Docs
description: Les groupes peuvent contenir d’autres groupes dans Azure Active Directory. Voici comment gérer ces adhésions.
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
# Gérer les groupes dont votre groupe est membre dans la version préliminaire d’Azure Active Directory
Les groupes peuvent contenir d’autres groupes dans la version préliminaire d’Azure Active Directory. [Nouveautés de la version préliminaire](active-directory-preview-explainer.md) Voici comment gérer ces adhésions.

## Comment trouver les groupes dont mon groupe est membre ?
1. Connectez-vous au [portail Azure](https://portal.azure.com) en utilisant un compte d’administrateur général pour le répertoire.
2. Sélectionnez **Plus de services**, saisissez **Utilisateurs et groupes** dans la zone de texte, puis sélectionnez **Entrée**.
   
   ![Ouvrir la gestion des utilisateurs](./media/active-directory-groups-membership-azure-portal/search-user-management.png)
3. Dans le panneau **Utilisateurs et groupes**, sélectionnez **Tous les groupes**.
   
   ![Ouvrir le panneau de groupes](./media/active-directory-groups-membership-azure-portal/view-groups-blade.png)
4. Dans le panneau **Utilisateurs et groupes - Tous les groupes**, sélectionnez un groupe.
5. Dans le panneau **Groupe - *NomGroupe***, sélectionnez **Appartenances aux groupes**.
   
   ![Ouverture du panneau Appartenances aux groupes](./media/active-directory-groups-membership-azure-portal/group-membership-blade.png)
6. Pour ajouter votre groupe en tant que membre d’un autre groupe, dans le panneau **Groupe - Appartenances aux groupes**, sélectionnez la commande **Ajouter**.
7. Sélectionnez un groupe à partir du panneau **Sélectionner un groupe**, puis cliquez sur le bouton **Sélectionner** en bas du panneau. Vous pouvez ajouter votre groupe à un seul groupe à la fois. La zone **Utilisateur** filtre l’affichage en fonction de la correspondance de votre entrée avec une partie ou l’intégralité d’un nom d’utilisateur ou d’appareil. Dans cette zone aucun caractère générique n’est accepté.
   
   ![Ajouter une appartenance à un groupe](./media/active-directory-groups-membership-azure-portal/add-group-membership.png)
8. Pour supprimer votre groupe en tant que membre d’un autre groupe, dans le panneau **Groupe - Appartenances aux groupes**, sélectionnez un groupe.
9. Dans le panneau ***NomGroupe***, sélectionnez la commande **Supprimer**, puis confirmez votre choix dans l’invite de commandes.
   
   ![Commande Supprimer des appartenances](./media/active-directory-groups-membership-azure-portal/remove-group-membership.png)
10. Lorsque vous avez terminé de modifier les appartenances aux groupes de votre groupe, sélectionnez **Enregistrer**.

## Informations supplémentaires
Ces articles fournissent des informations supplémentaires sur Azure Active Directory.

* [Consulter les groupes existants](active-directory-groups-view-azure-portal.md)
* [Création d’un nouveau groupe et ajout de membres](active-directory-groups-create-azure-portal.md)
* [Gérer les paramètres d’un groupe](active-directory-groups-settings-azure-portal.md)
* [Gérer les membres d’un groupe](active-directory-groups-members-azure-portal.md)
* [Gérer les règles dynamiques pour les utilisateurs dans un groupe](active-directory-groups-dynamic-membership-azure-portal.md)

<!---HONumber=AcomDC_0914_2016-->