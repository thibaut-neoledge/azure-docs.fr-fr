<properties
	pageTitle="Création d’une règle simple pour configurer l’appartenance dynamique à un groupe | Microsoft Azure"
	description="Explique comment créer une règle simple pour configurer l’appartenance dynamique à un groupe."
	services="active-directory"
	documentationCenter=""
	authors="curtand"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/09/2016"
	ms.author="curtand"/>


# Création d’une règle simple pour configurer l’appartenance dynamique à un groupe

Pour activer l’appartenance dynamique à un groupe spécifique, procédez comme suit :

1. Dans le portail Azure, sous l’onglet **Groupes**, sélectionnez le groupe à modifier, puis sous l’onglet **Configurer** de ce groupe, définissez le commutateur **Activer les appartenances dynamiques** sur **Oui**.

2. Vous pouvez à présent définir une seule règle simple pour le groupe, qui contrôle la manière dont l’appartenance dynamique fonctionne pour ce groupe. Assurez-vous que l’option **Ajouter des utilisateurs où** est cochée, puis sélectionnez une propriété d’utilisateur dans la liste (par exemple, department, jobTitle, etc.).

3. Ensuite, sélectionnez une condition (Non égal à, Égal à, Ne commence pas par, Commence par, Ne contient pas, Contient, Ne correspond pas, Correspond), puis spécifiez une valeur pour la propriété d’utilisateur sélectionnée. Par exemple, si un groupe est affecté à une application SaaS et que vous activez les appartenances dynamiques à ce groupe en définissant une règle selon laquelle **Ajouter des utilisateurs où** a la valeur jobTitle qui est égale à (-eq)Sales Rep, tous les utilisateurs au sein de votre annuaire Azure AD dont la propriété jobTitle a la valeur Sales Rep ont accès à cette application SaaS.

4. Notez que vous pouvez définir une règle d’appartenance dynamique sur les groupes de sécurité ou Office L’appartenance dynamique à des groupes nécessite une licence Azure AD Premium.

Ces articles fournissent des informations supplémentaires sur Azure Active Directory.

* [Gestion de l’accès aux ressources avec les groupes Azure Active Directory](active-directory-manage-groups.md)
* [Index d’articles pour la gestion des applications dans Azure Active Directory](active-directory-apps-index.md)
* [Qu’est-ce qu’Azure Active Directory ?](active-directory-whatis.md)
* [Intégration des identités locales dans Azure Active Directory](active-directory-aadconnect.md)

<!---HONumber=AcomDC_0218_2016-->