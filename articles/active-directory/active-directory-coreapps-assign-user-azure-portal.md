<properties
	pageTitle="Affecter un utilisateur ou un groupe à une application d’entreprise dans la version préliminaire d’Azure Active Directory | Microsoft Azure"
	description="Comment sélectionner une application d’entreprise pour affecter un utilisateur ou un groupe dans Azure Active Directory"
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

# Affecter un utilisateur ou un groupe à une application d’entreprise dans la version préliminaire d’Azure Active Directory

Il est facile d’affecter un utilisateur ou un groupe à vos applications d’entreprise dans la version préliminaire d’Azure Active Directory (Azure AD). [Nouveautés de la version préliminaire](active-directory-preview-explainer.md) Vous devez disposer des autorisations appropriées pour gérer l’application d’entreprise. Dans la version préliminaire actuelle, vous devez être administrateur global pour le répertoire.

## Comment attribuer l’accès à une application d’entreprise à un utilisateur ?

1. Connectez-vous au [portail Azure](https://portal.azure.com) en utilisant un compte d’administrateur général pour le répertoire.

2. Sélectionnez **Plus de services**, saisissez Azure Active Directory dans la zone de texte, puis sélectionnez **Entrée**.

3. Sur le panneau **Azure Active Directory - *NomRépertoire*** panneau (autrement dit, le panneau Azure Active Directory pour le répertoire que vous gérez), sélectionnez **Applications d’entreprise**.

  ![Ouverture des applications d’entreprise](./media/active-directory-coreapps-assign-user-azure-portal/open-enterprise-apps.png)

4. Sur le panneau**Applications d’entreprise**, sélectionnez **Toutes les applications**. Vous verrez une liste des applications que vous pouvez gérer.

5. Sur le panneau**Applications d’entreprise - Toutes les applications**, sélectionnez une application.

6. Sur le panneau ***NomApplication*** (autrement dit, le panneau avec le nom de l’application sélectionnée dans le titre), sélectionnez **Utilisateurs et groupes**.

  ![Sélection de la commande Toutes les applications](./media/active-directory-coreapps-assign-user-azure-portal/select-app-users.png)

7. Sur le panneau ***NomApplication*** **- Affectation d’utilisateurs et de groupes**, sélectionnez la commande **Ajouter**.

8. Sur le panneau **Ajouter une affectation** panneau, sélectionnez **Utilisateurs et groupes**.

  ![Affecter un utilisateur ou un groupe à l’application](./media/active-directory-coreapps-assign-user-azure-portal/assign-users.png)

9. Sur le panneau **Utilisateurs et groupes** panneau, sélectionnez un ou plusieurs utilisateurs ou groupes dans la liste, puis sélectionnez le bouton **Sélectionner** en bas du panneau.

10. Sur le panneau **Ajouter une affectation** panneau, sélectionnez **Rôle**. Puis, dans le panneau **Sélectionner un rôle**, choisissez un rôle à appliquer aux utilisateurs ou groupes sélectionnez, puis cliquez sur le bouton **OK** en bas du panneau.

11. Sur le panneau **Ajouter une affectation**, sélectionnez le bouton **Affecter** en bas du panneau. Les utilisateurs ou les groupes auront les autorisations définies par le rôle sélectionné pour cette application d’entreprise.

## Étapes suivantes

- [Voir tous mes groupes](active-directory-groups-view-azure-portal.md)
- [Suppression d’une affectation d’utilisateur ou de groupe à partir d’une application d’entreprise](active-directory-coreapps-remove-assignment-user-azure-portal.md)
- [Désactiver les connexions utilisateur pour une application d’entreprise](active-directory-coreapps-disable-app-azure-portal.md)
- [Modifier le nom ou le logo d’une application d’entreprise](active-directory-coreapps-change-app-logo-azure-portal.md)

<!---HONumber=AcomDC_0914_2016-->