<properties
	pageTitle="Groupes dédiés dans Azure Active Directory | Microsoft Azure"
	description="Vue d’ensemble du fonctionnement et de la création des groupes dédiés dans Azure Active Directory."
	services="active-directory"
	documentationCenter=""
	authors="curtand"
	manager="stevenpo"
	editor=""
	/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/09/2016"
	ms.author="curtand"/>

# Groupes dédiés dans Azure Active Directory

Dans Azure Active Directory, des groupes dédiés sont automatiquement créés, et l’adhésion à ces groupes est également automatique. Vous ne pouvez pas ajouter ou retirer des membres à ces groupes dédiés via le portail Azure, les applets Windows PowerShell ou via un programme. Pour activer les groupes dédiés, dans le portail Azure, sous l’onglet Configurer, définissez l’option **Activer les groupes dédiés sur Oui**.

Une fois que l’option Activer les groupes dédiés est définie sur **Oui**, vous pouvez configurer l’annuaire pour qu’il crée automatiquement le groupe dédié Tous les utilisateurs. Pour ce faire, définissez l’option **Activer le groupe Tous les utilisateurs** sur **Oui**. Vous pouvez ensuite également modifier le nom de ce groupe dédié, en effectuant votre saisie dans le champ **Nom d’affichage du groupe Tous les utilisateurs**.

Le groupe dédié Tous les utilisateurs peut vous permettre d’attribuer les mêmes autorisations à l’ensemble des utilisateurs de votre répertoire. Par exemple, vous pouvez autoriser l’ensemble des utilisateurs de votre répertoire à accéder à l’application SaaS, en attribuant l’accès à cette application au groupe dédié Tous les utilisateurs.

Remarque : le groupe « Tous les utilisateurs » dédié inclut tous les utilisateurs de l’annuaire, dont les invités et les utilisateurs externes. Si vous avez besoin d’un groupe qui exclut les utilisateurs externes, vous pouvez créer un groupe avec une règle dynamique telle que

(user.userPrincipalName -notContains "#EXT#@")

Pour un groupe qui exclut tous les invités, utilisez une règle telle que

(user.userType -ne "Guest")

Cet article explique en détail comment créer une règle pour gérer les membres d’un groupe dans Azure Active Directory :

* [Création d’une règle simple pour configurer l’appartenance dynamique au groupe](active-directory-accessmanagement-simplerulegroup.md)


Ces articles fournissent des informations supplémentaires sur Azure Active Directory.

* [Gestion de l’accès aux ressources avec les groupes Azure Active Directory](active-directory-manage-groups.md)
* [Index d’articles pour la gestion des applications dans Azure Active Directory](active-directory-apps-index.md)
* [Qu’est-ce qu’Azure Active Directory ?](active-directory-whatis.md)
* [Intégration de vos identités locales avec Azure Active Directory](active-directory-aadconnect.md)

<!---HONumber=AcomDC_0224_2016-->