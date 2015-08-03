<properties 
	pageTitle="Groupes dédiés dans Azure Active Directory | Microsoft Azure" 
	description="Une rubrique qui explique comment gérer les mots de passe dans Azure AD." 
	services="active-directory" 
	documentationCenter="" 
	authors="femila" 
	manager="swadhwa" 
	editor=""
	tags="azure-classic-portal"/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/13/2015" 
	ms.author="femila"/>

# Groupes dédiés dans Azure Active Directory

Dans Azure Active Directory, des groupes dédiés sont automatiquement créés, et l’adhésion à ces groupes est également automatique. Vous ne pouvez pas ajouter ou retirer des membres à ces groupes dédiés via le portail de gestion Azure, les applets Windows PowerShell ou via un programme. Pour activer les groupes dédiés, dans le portail de gestion Azure, dans l’onglet Configurer, définissez l’option Activer les groupes dédiés sur Oui.

Dans la version préliminaire publique actuelle, une fois que l’option Activer les groupes dédiés est définie sur Oui, vous pouvez configurer le répertoire pour qu’il crée automatiquement le groupe dédié Tous les utilisateurs. Pour ce faire, définissez l’option Activer le groupe Tous les utilisateurs sur Oui. Vous pouvez ensuite également modifier le nom de ce groupe dédié, en effectuant votre saisie dans le champ Nom affiché du groupe Tous les utilisateurs.

Le groupe dédié Tous les utilisateurs peut vous permettre d’attribuer les mêmes autorisations à l’ensemble des utilisateurs de votre répertoire. Par exemple, vous pouvez autoriser l’ensemble des utilisateurs de votre répertoire à accéder à l’application SaaS, en attribuant l’accès à cette application au groupe dédié Tous les utilisateurs.

Voici quelques rubriques vous fournissant des informations supplémentaires sur Azure Active Directory.

* [Gestion de l’accès aux ressources avec les groupes Azure Active Directory](active-directory-manage-groups.md)

* [Qu’est-ce qu’Azure Active Directory ?](active-directory-whatis.md)

* [Intégration de vos identités locales avec Azure Active Directory](active-directory-aadconnect.md)

<!---HONumber=July15_HO4-->