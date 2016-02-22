<properties

	pageTitle="Managing security groups in Azure Active Directory | Microsoft Azure"
	description="How to create and manage security groups to manage Azure resource access using Azure Active Directory."
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


#Gestion des groupes de sécurité dans Azure Active Directory

Dans Azure Active Directory (Azure AD), une des principales fonctionnalités est la possibilité de gérer l’accès aux ressources. Ces ressources peuvent faire partie de l’annuaire, comme dans le cas des autorisations de gestion des objets via des rôles dans l’annuaire ou les ressources externes à l’annuaire, comme les applications SaaS, les services Azure et les sites SharePoint ou les ressources locales. Un groupe peut être affecté à une ressource par le propriétaire de la ressource. Ce faisant, il octroie aux membres de ce groupe l’accès à la ressource. L’appartenance au groupe peut être gérée par le propriétaire du groupe. Le propriétaire de la ressource délègue l’autorisation d’affecter des utilisateurs à ses ressources au propriétaire du groupe.


##Comment créer et gérer un groupe de sécurité

**Pour créer un groupe dans le portail Azure**

1. Dans le portail Azure, cliquez sur **Active Directory**, puis sur le nom de l’annuaire de votre organisation.
2. Cliquez sur l’onglet **Groupes**.
3. Dans la page Groupes, cliquez sur **Ajouter un groupe**.
4. Dans la fenêtre **Ajouter un groupe**, spécifiez le nom et la description d’un groupe.
5. Cette tâche peut être effectuée à l’aide du portail des comptes Office 365, du portail des comptes Windows Intune ou du portail Azure, selon les services auxquels votre organisation est abonnée. Pour plus d’informations sur l’utilisation des portails pour gérer Azure Active Directory, consultez [Administration de votre annuaire Azure AD](active-directory-administer).

## Comment attribuer ou supprimer des utilisateurs dans un groupe de sécurité

**Pour ajouter un membre à un groupe dans le portail Azure**

1. Dans le portail Azure, cliquez sur **Active Directory**, puis sur le nom de l’annuaire de votre organisation.
2. Cliquez sur l’onglet **Groupes**.
3. Dans la page **Groupes**, cliquez sur le nom du groupe auquel vous souhaitez ajouter des membres. Par défaut, l’onglet **Membres** du groupe sélectionné s’affiche.
4. Dans la page de ce groupe, cliquez sur **Ajouter des membres**.
5. Dans la page **Ajouter des membres**, cliquez sur le nom de l’utilisateur ou du groupe à ajouter en tant que membre de ce groupe, puis vérifiez qu’il a été ajouté au volet Sélectionné.


**Pour supprimer un membre d’un groupe dans le portail Azure**

1. Dans le portail Azure, cliquez sur **Active Directory**, puis sur le nom de l’annuaire de votre organisation.
2. Cliquez sur l’onglet **Groupes**.
3. Dans la page Groupes, cliquez sur le nom du groupe dont vous souhaitez supprimer des membres.
4. Dans la page de ce groupe, cliquez sur l’onglet **Membres**.
5. Dans la page de ce groupe, cliquez sur le nom du membre à supprimer de ce groupe, puis sur **Supprimer**.
6. Confirmez la suppression de ce membre du groupe en cliquant sur **Oui** en réponse au message de confirmation de l’opération.


## Comment utiliser une règle pour gérer dynamiquement les membres d’un groupe de sécurité

**Pour activer l’appartenance dynamique à un groupe spécifique, procédez comme suit :**

1. Dans le portail Azure, sous l’onglet **Groupes**, sélectionnez le groupe à modifier, puis sous l’onglet **Configurer** de ce groupe, définissez le commutateur **Activer les appartenances dynamiques** sur **Oui**.
2. Vous pouvez à présent définir une seule règle simple pour le groupe, qui contrôle la manière dont l’appartenance dynamique fonctionne pour ce groupe. Assurez-vous que la case d’option **Ajouter des utilisateurs où** est activée, puis sélectionnez une propriété d’utilisateur dans le menu déroulant (par exemple, department, jobTitle, etc.).
3. Ensuite, sélectionnez une condition (Non égal à, Égal à, Ne commence pas par, Commence par, Ne contient pas, Contient, Ne correspond pas, Correspond), puis spécifiez une valeur pour la propriété d’utilisateur sélectionnée.

Par exemple, si un groupe est affecté à une application SaaS (pour plus d’informations, consultez Autorisation d’accès d’un groupe à une application SaaS dans Azure AD) et que vous activez l’appartenance dynamique à ce groupe en définissant une règle selon laquelle Ajouter des utilisateurs où a la valeur jobTitle qui est égale à (-eq) Sales Rep, tous les utilisateurs au sein de votre annuaire Azure AD dont la propriété jobTitle a la valeur Sales Rep ont accès à cette application SaaS.

## Informations supplémentaires

Ces articles fournissent des informations supplémentaires sur Azure Active Directory.

* [Gestion de l’accès aux ressources avec les groupes Azure Active Directory](active-directory-manage-groups.md)

* [Index d’articles pour la gestion des applications dans Azure Active Directory](active-directory-apps-index.md)

* [Qu’est-ce qu’Azure Active Directory ?](active-directory-whatis.md)

* [Intégration des identités locales dans Azure Active Directory](active-directory-aadconnect.md)

<!---HONumber=AcomDC_0211_2016-->