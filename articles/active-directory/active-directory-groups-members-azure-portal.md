<properties
	pageTitle="Gérer les membres des groupes dans la version préliminaire d’Azure Active Directory | Microsoft Azure"
	description="Comment connaître les utilisateurs et les appareils qui sont membres d’un groupe dans Azure Active Directory"
	services="active-directory"
	documentationCenter=""
	authors="curtand"
	manager="femila"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/12/2016"
	ms.author="curtand"/>


# Gérer les membres des groupes dans la version préliminaire d’Azure Active Directory

Cet article explique comment gérer les membres d’un groupe dans la version préliminaire d’Azure Active Directory (Azure AD). [Nouveautés de la version préliminaire](active-directory-preview-explainer.md)

## Comment trouver les membres et les gérer ?

1.  Connectez-vous au [portail Azure](https://portal.azure.com) en utilisant un compte d’administrateur général pour le répertoire.

2.  Sélectionnez **Plus de services**, saisissez **Utilisateurs et groupes** dans la zone de texte, puis sélectionnez **Entrée**.

  ![Ouvrir la gestion des utilisateurs](./media/active-directory-groups-members-azure-portal/search-user-management.png)

3.  Dans le panneau **Utilisateurs et groupes**, sélectionnez **Tous les groupes**.

  ![Ouvrir le panneau de groupes](./media/active-directory-groups-members-azure-portal/view-groups-blade.png)

4. Dans le panneau **Utilisateurs et groupes - Tous les groupes**, sélectionnez un groupe.

5. Dans le panneau **Groupe - *NomGroupe***, sélectionnez **Membres**.

  ![Ouverture du panneau Membres](./media/active-directory-groups-members-azure-portal/view-group-members.png)

6. Pour ajouter des membres au groupe, dans le panneau **Groupe - Membres**, sélectionnez **Ajouter des membres**.

  ![Commande Ajouter des membres](./media/active-directory-groups-members-azure-portal/add-group-members-command.png)

7. Dans le panneau **Membres**, sélectionnez un ou plusieurs utilisateurs ou appareils à ajouter au groupe, puis cliquez sur le **Sélectionner** en bas du panneau pour les ajouter au groupe. La zone **Utilisateur** filtre l’affichage en fonction de la correspondance de votre entrée avec une partie ou l’intégralité d’un nom d’utilisateur ou d’appareil. Dans cette zone aucun caractère générique n’est accepté.

8. Pour supprimer des membres du groupe, dans le panneau **Groupe - Membres**, sélectionnez un membre.

9. Dans le panneau ***NomMembre***, sélectionnez la commande **Supprimer**, puis confirmez votre choix dans l’invite de commandes.

  ![Commande Supprimer des membres](./media/active-directory-groups-members-azure-portal/remove-group-members-command.png)

9. Lorsque vous avez terminé la modification des membres du groupe, sélectionnez **Enregistrer**.


## Informations supplémentaires

Ces articles fournissent des informations supplémentaires sur Azure Active Directory.

* [Consulter les groupes existants](active-directory-groups-view-azure-portal.md)
* [Création d’un nouveau groupe et ajout de membres](active-directory-groups-create-azure-portal.md)
* [Gérer les paramètres d’un groupe](active-directory-groups-settings-azure-portal.md)
* [Gérer l’appartenance à un groupe](active-directory-groups-membership-azure-portal.md)
* [Gérer les règles dynamiques pour les utilisateurs dans un groupe](active-directory-groups-dynamic-membership-azure-portal.md)

<!---HONumber=AcomDC_0914_2016-->