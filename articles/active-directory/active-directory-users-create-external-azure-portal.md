<properties
	pageTitle="Ajouter des utilisateurs à partir d’autres répertoires ou entreprises partenaires dans la version préliminaire d’Azure Active Directory | Microsoft Azure"
	description="Explique comment ajouter des utilisateurs ou modifier les informations utilisateur dans Azure Active Directory, y compris celles des utilisateurs externes et invités."
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

# Ajouter des utilisateurs à partir d’autres répertoires ou entreprises partenaires dans la version préliminaire Azure Active Directory

> [AZURE.SELECTOR]
- [Portail Azure](active-directory-users-create-external-azure-portal.md)
- [Portail Azure Classic](active-directory-create-users-external.md)

Cet article explique comment ajouter des utilisateurs à partir d’autres répertoires dans la version préliminaire d’Azure Active Directory (Azure AD), ou à partir d’entreprises partenaires. [Nouveautés de la version préliminaire](active-directory-preview-explainer.md) Pour en savoir plus sur l’ajout de nouveaux utilisateurs dans votre organisation, et en particulier l’ajout d’utilisateurs disposant de comptes Microsoft, voir [Ajout ou modification d’utilisateurs dans Azure Active Directory](active-directory-users-create-azure-portal.md). Par défaut, les utilisateurs ajoutés ne reçoivent pas d’autorisations d’administrateur, mais vous pouvez leur attribuer des rôles à tout moment.

## Ajouter un utilisateur

1.  Connectez-vous au [portail Azure](https://portal.azure.com) en utilisant un compte d’administrateur général pour le répertoire.

2.  Sélectionnez **Plus de services**, saisissez **Utilisateurs et groupes** dans la zone de texte, puis sélectionnez **Entrée**.

    ![Ouvrir la gestion des utilisateurs](./media/active-directory-users-create-external-azure-portal/create-users-user-management.png)

3.  Dans le panneau **Utilisateurs et groupes**, sélectionnez **Utilisateurs**, puis sélectionnez **Ajouter**.

    ![Sélection de la commande Ajouter](./media/active-directory-users-create-external-azure-portal/create-users-add-command.png)

4. Dans le panneau **Utilisateur**, fournissez un nom complet dans **Nom** et le nom de connexion dans **Nom d’utilisateur**.

5. Copiez ou notez d’une autre façon le mot de passe généré de sorte à pouvoir le fournir à l’utilisateur une fois ce processus terminé.

6. Si vous le souhaitez, sélectionnez **Profil** pour ajouter le nom et le prénom, la fonction et le nom de service de l’utilisateur.

	![Opening the user profile](./media/active-directory-users-create-external-azure-portal/create-users-user-profile.png)

	- Sélectionnez **Groupes** pour ajouter l’utilisateur à un ou plusieurs groupes.

		![Ajout d’un utilisateur aux groupes](./media/active-directory-users-create-external-azure-portal/create-users-user-groups.png)

	- Sélectionnez **Rôle organisationnel** pour affecter l’utilisateur à un rôle à partir de la liste **Rôles**. Pour plus d’informations sur les utilisateurs et les rôles d’administrateur, consultez la page [Attribution de rôles d’administrateur dans Azure AD](active-directory-assign-admin-roles.md).

		![Affectation d’un utilisateur à un rôle](./media/active-directory-users-create-external-azure-portal/create-users-assign-role.png)

7. Sélectionnez **Créer**.

8. Distribuez de manière sécurisée le mot de passe généré au nouvel utilisateur afin qu’il puisse se connecter.

> [AZURE.IMPORTANT] Si votre organisation utilise plusieurs domaines, vous devez avoir connaissance des problèmes suivants lorsque vous ajoutez un compte d’utilisateur :
>
> - Pour ajouter des comptes d’utilisateurs avec le même nom d’utilisateur principal (UPN) sur plusieurs domaines, ajoutez **d’abord** geoffgrisso@contoso.onmicrosoft.com, par exemple, **suivi de** geoffgrisso@contoso.com.
> - **N’ajoutez pas** geoffgrisso@contoso.com avant d’avoir indiqué geoffgrisso@contoso.onmicrosoft.com. Il est important de respecter cet ordre et il peut être difficile de revenir en arrière.

Si vous modifiez les informations d’un utilisateur dont l’identité est synchronisée avec votre service Active Directory local, vous ne pouvez pas modifier les informations utilisateur dans le portail Azure Classic. Pour modifier les informations d’un utilisateur, servez-vous de vos outils de gestion Active Directory locaux.


## Étapes suivantes

- [Ajouter un utilisateur](active-directory-users-create-azure-portal.md)
- [Réinitialiser le mot de passe d’un utilisateur dans le nouveau portail Azure](active-directory-users-reset-password-azure-portal.md)
- [Affecter un utilisateur à un rôle dans Azure AD](active-directory-users-assign-role-azure-portal.md)
- [Modifier les informations de travail d’un utilisateur](active-directory-users-work-info-azure-portal.md)
- [Gérer les profils utilisateur](active-directory-users-profile-azure-portal.md)
- [Suppression d’un utilisateur dans Azure AD](active-directory-users-delete-user-azure-portal.md)

<!----HONumber=AcomDC_0914_2016-->