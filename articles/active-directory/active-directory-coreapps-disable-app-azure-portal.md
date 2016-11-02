<properties
	pageTitle="Désactiver les connexions utilisateur pour une application d’entreprise dans la version préliminaire d’Azure Active Directory | Microsoft Azure"
	description="Comment désactiver une application d’entreprise afin qu’aucun utilisateur ne puisse s’y connecter dans Azure Active Directory"
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


# Désactiver les connexions utilisateur pour une application d’entreprise dans la version préliminaire d’Azure Active Directory

Il est facile de désactiver une application d’entreprise afin qu’aucun utilisateur ne puisse s’y connecter dans la version préliminaire d’Azure Active Directory (Azure AD). [Nouveautés de la version préliminaire](active-directory-preview-explainer.md) Vous devez disposer des autorisations appropriées pour gérer l’application d’entreprise. Dans la version préliminaire actuelle, vous devez être administrateur global pour le répertoire.

## Comment désactiver les connexions des utilisateurs ?

1. Connectez-vous au [portail Azure](https://portal.azure.com) en utilisant un compte d’administrateur général pour le répertoire.

2. Sélectionnez **Plus de services**, saisissez **Azure Active Directory** dans la zone de texte, puis sélectionnez **Entrée**.

3. Sur le panneau **Azure Active Directory - *NomRépertoire*** panneau (autrement dit, le panneau Azure Active Directory pour le répertoire que vous gérez), sélectionnez **Applications d’entreprise**.

	![Ouverture des applications d’entreprise](./media/active-directory-coreapps-disable-app-azure-portal/open-enterprise-apps.png)

4. Sur le panneau**Applications d’entreprise**, sélectionnez **Toutes les applications**. Vous verrez une liste des applications que vous pouvez gérer.

5. Sur le panneau**Applications d’entreprise - Toutes les applications**, sélectionnez une application.

6. Sur le panneau ***NomApplication*** (autrement dit, le panneau avec le nom de l’application sélectionnée dans le titre), sélectionnez **Propriétés**.

	![Sélection de la commande Toutes les applications](./media/active-directory-coreapps-disable-app-azure-portal/select-app.png)

7. Dans le panneau ***NomApplication*** **- Propriétés**, sélectionnez **Non** pour **Les utilisateurs peuvent-ils se connecter ?**.

8. Sélectionnez la commande **Enregistrer**.

## Étapes suivantes

- [Voir tous mes groupes](active-directory-groups-view-azure-portal.md)
- [Affecter un utilisateur ou un groupe à une application d’entreprise](active-directory-coreapps-assign-user-azure-portal.md)
- [Suppression d’une affectation d’utilisateur ou de groupe à partir d’une application d’entreprise](active-directory-coreapps-remove-assignment-user-azure-portal.md)
- [Modifier le nom ou le logo d’une application d’entreprise](active-directory-coreapps-change-app-logo-azure-portal.md)

<!---HONumber=AcomDC_0914_2016-->