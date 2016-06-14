<properties
	pageTitle="Configuration d’Azure Active Directory pour gérer l’accès aux applications en libre-service| Microsoft Azure"
	description="La gestion de groupes en libre-service permet aux utilisateurs de créer et de gérer des groupes de sécurité ou des groupes Office 365 dans Azure Active Directory. Elle leur offre aussi la possibilité de faire une demande d’appartenance à un groupe de sécurité ou à un groupe Office 365."
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
	ms.topic="get-started-article"
	ms.date="06/03/2016"
	ms.author="curtand"/>

# Configuration d’Azure Active Directory pour la gestion de groupe en libre-service

La gestion de groupes en libre-service permet aux utilisateurs de créer et gérer des groupes de sécurité ou Office 365 dans Azure Active Directory (Azure AD). Les utilisateurs peuvent également faire une demande d’appartenance à un groupe de sécurité ou Office 365. Le propriétaire du groupe peut alors approuver ou refuser l’appartenance. Ainsi, le contrôle quotidien de l’appartenance à un groupe peut être délégué à des personnes qui ont une bonne connaissance du contexte professionnel de cette appartenance. Les fonctionnalités de gestion de groupes en libre-service ne sont disponibles que pour les groupes de sécurité et les groupes Office 365. Elles ne sont pas disponibles pour les groupes de sécurité activés pour la messagerie électronique ou les listes de distribution.

La gestion de groupes en libre-service se compose actuellement de deux scénarios essentiels : la gestion de groupes déléguée et la gestion de groupes en libre-service.

- **Gestion de groupes déléguée** : prenons l’exemple d’un administrateur qui gère l’accès à une application SaaS utilisée dans son entreprise. La gestion de ces droits d’accès devenant fastidieuse, cet administrateur demande au propriétaire de l’entreprise d’ajouter un nouveau groupe. L’administrateur accorde l’accès à l’application au nouveau groupe et ajoute au groupe toutes les personnes accédant déjà à l’application. Le propriétaire de l’entreprise peut ainsi ajouter d’autres utilisateurs, qui sont automatiquement approvisionnés dans l’application. Il n’a pas besoin d’attendre que l’administrateur gère l’accès des utilisateurs. Si l’administrateur accorde la même autorisation à un responsable d’un autre groupe de l’entreprise, cette personne peut également gérer l’accès de ses propres utilisateurs. Le propriétaire de l’entreprise ne peut pas visualiser ni gérer les utilisateurs du responsable, et inversement. L’administrateur a toujours la possibilité de voir l’ensemble des utilisateurs ayant accès à l’application et, si nécessaire, de bloquer les droits d’accès.

- **Gestion de groupes en libre-service** : prenons l’exemple de deux utilisateurs disposant tous deux d’un site SharePoint Online. Ils les gèrent indépendamment. Cependant, ils veulent les rendre accessibles à chacune des deux équipes. Pour cela, ils peuvent créer un groupe dans Azure AD, puis, dans SharePoint Online, chacun d’eux choisit ce même groupe pour lui donner accès aux deux sites. Ainsi, quand une personne souhaite obtenir un accès, elle en effectue la demande dans le volet d’accès, et dès que sa demande a été approuvée, elle obtient automatiquement un accès aux deux sites SharePoint Online. Par la suite, l’un des deux utilisateurs décide que toutes les personnes ayant accès au site doivent également pouvoir accéder à une application SaaS. L’administrateur de l’application SaaS peut ajouter des droits d’accès à l’application pour le site SharePoint Online. Les demandes qu’il approuvera par la suite donneront ainsi accès non seulement aux deux sites SharePoint Online, mais également à cette application SaaS.

## Mise à disposition d’un groupe en libre-service pour l’utilisateur final

1. Dans le [Portail Azure Classic](https://manage.windowsazure.com), ouvrez votre annuaire Azure AD.

2. Dans l’onglet **Configurer**, activez **Gestion déléguée des groupes**.

3. Activez **Les utilisateurs peuvent créer des groupes de sécurité** ou **Les utilisateurs peuvent créer des groupes Office**.

Quand **Les utilisateurs peuvent créer des groupes de sécurité** est activé, tous les utilisateurs présents dans votre répertoire sont autorisés à créer des groupes de sécurité et à ajouter des membres à ces groupes. Ces nouveaux groupes apparaissent également dans le volet d’accès de tous les autres utilisateurs. Si le paramètre de stratégie du groupe l’autorise, d’autres utilisateurs peuvent créer des demandes d’adhésion à ces groupes. Si **Les utilisateurs peuvent créer des groupes de sécurité** est désactivé, les utilisateurs ne peuvent pas créer de groupes et ne peuvent pas modifier les groupes existants dont ils sont propriétaires. Cependant, ils peuvent toujours gérer les appartenances de ces groupes et approuver les demandes d’adhésion d’autres utilisateurs à leurs groupes.

Vous pouvez également utiliser **Utilisateurs pouvant utiliser le libre-service pour les groupes de sécurité** pour obtenir un contrôle d’accès plus fin de la gestion de groupes en libre-service pour vos utilisateurs. Quand **Les utilisateurs peuvent créer des groupes** est activé, tous les utilisateurs présents dans votre répertoire sont autorisés à créer des groupes et à ajouter des membres à ces groupes. Par ailleurs, en définissant **Utilisateurs pouvant utiliser le libre-service pour les groupes de sécurité** sur Certains, vous limitez la gestion des groupes à un groupe d’utilisateurs défini. Dans ce cas, vous devez ajouter des utilisateurs au groupe SSGMSecurityGroupsUsers avant que ceux-ci ne puissent créer des groupes et y ajouter des membres. En définissant **Utilisateurs pouvant utiliser le libre-service pour les groupes de sécurité** sur Tous, vous autorisez tous les utilisateurs de votre répertoire à créer des groupes.

Vous pouvez également utiliser la zone **Groupe pouvant utiliser le libre-service pour les groupes de sécurité** pour spécifier un nom personnalisé pour un groupe dont les membres peuvent utiliser le libre-service.

## Informations supplémentaires

Ces articles fournissent des informations supplémentaires sur Azure Active Directory.

* [Gestion de l’accès aux ressources avec les groupes Azure Active Directory](active-directory-manage-groups.md)

* [Index d’articles pour la gestion des applications dans Azure Active Directory](active-directory-apps-index.md)

* [Qu’est-ce qu’Azure Active Directory ?](active-directory-whatis.md)

* [Intégration des identités locales dans Azure Active Directory](active-directory-aadconnect.md)

<!---HONumber=AcomDC_0608_2016-->