<properties
	pageTitle="Configuration d’Azure Active Directory pour gérer l’accès aux applications en libre-service| Microsoft Azure"
	description="présentation de La gestion de groupes en libre-service qui permet aux utilisateurs de créer et de gérer des groupes de sécurité ou des groupes Office dans Azure Active Directory. Elle leur offre aussi la possibilité de faire une demande d’appartenance à un groupe de sécurité ou à un groupe Office."
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

# Configuration d’Azure Active Directory pour la gestion de groupe libre-service

La gestion de groupes en libre-service permet aux utilisateurs de créer et de gérer des groupes de sécurité ou groupe Office dans Azure Active Directory (Azure AD). Elle leur offre aussi la possibilité de faire une demande d’appartenance à un groupe de sécurité ou groupe Office, laquelle peut être approuvée ou refusée par le propriétaire du groupe. Grâce aux fonctionnalités de gestion de groupes en libre-service, le contrôle quotidien de l’appartenance à un groupe peut être délégué à des personnes qui ont une bonne connaissance du contexte professionnel de cette appartenance. Les fonctionnalités de gestion de groupe libre-service ne sont disponibles que pour les groupes de sécurité et les groupes Office 365 ; elles ne sont pas disponibles pour les groupes de sécurité activés pour la messagerie électronique ou les listes de distribution.

La gestion de groupes en libre-service se compose actuellement de deux scénarios essentiels : la gestion de groupes déléguée et la gestion de groupes en libre-service.


- **Gestion de groupes déléguée** : prenons l’exemple d’un administrateur qui gère l’accès à une application SaaS utilisée dans son entreprise. La gestion de ces droits d’accès devenant fastidieuse, cet administrateur demande au propriétaire de l’entreprise d’ajouter un nouveau groupe. Dès lors, l’administrateur octroie l’accès à l’application au nouveau groupe que le propriétaire de l’entreprise vient de créer et y place toutes les personnes ayant actuellement accès à l’application. Le propriétaire de l’entreprise peut ainsi ajouter d’autres utilisateurs, qui sont automatiquement approvisionnés dans l’application quelques instants plus tard. Le propriétaire de l’entreprise n’a pas besoin d’attendre que l’administrateur octroie l’accès à ses utilisateurs ; il peut lui-même effectuer cette tâche. L’administrateur peut répéter cette opération pour l’assistant administratif d’un autre groupe de l’entreprise, ce qui permettra aussi bien au propriétaire de l’entreprise qu’à cet assistant administratif de gérer l’accès de leurs utilisateurs respectifs, sans pouvoir modifier ou voir les utilisateurs du groupe de l’autre personne. L’administrateur a toujours la possibilité de voir l’ensemble des utilisateurs ayant accès à l’application et, si nécessaire, de bloquer les droits d’accès.


- **Gestion de groupes en libre-service** : prenons l’exemple de deux utilisateurs disposant tous deux d’un site SharePoint Online. Bien qu’ils les gèrent indépendamment, ils souhaitent les rendre facilement accessibles à chacune des deux équipes. Pour cela, ils créent un groupe dans Azure AD puis, dans SharePoint Online, chacun d’eux choisit ce même groupe pour lui donner accès aux deux sites. Ainsi, quand une personne souhaite obtenir un accès, elle en effectue la demande dans le volet d’accès, et dès que sa demande a été approuvée, elle obtient automatiquement un accès aux deux sites SharePoint Online. Par la suite, l’un des deux utilisateurs décide que toutes les personnes ayant accès à son site doivent également pouvoir accéder à une application SaaS. Il demande alors à l’administrateur de cette application SaaS d’ajouter des droits d’accès à son site pour cette application. Les demandes qu’il approuvera donneront ainsi accès non seulement aux deux sites SharePoint Online, mais également à cette application SaaS.

## Mise à disposition d’un groupe en libre-service pour l’utilisateur final

Sous l’onglet **Configurer** du portail Azure, définissez le commutateur **Gestion déléguée des groupes** avec la valeur Activé, puis le commutateur **Les utilisateurs peuvent créer des groupes de sécurité** ou le commutateur **Les utilisateurs peuvent créer des groupes Office** avec la valeur Activé.

Quand le commutateur **Les utilisateurs peuvent créer des groupes de sécurité** est défini avec la valeur **Activé**, tous les utilisateurs présents dans votre répertoire sont autorisés à créer des groupes de sécurité et à ajouter des membres à ces groupes. Ces nouveaux groupes apparaissent également dans le panneau d’accès pour tous les autres utilisateurs et les autres utilisateurs peuvent créer des demandes d’adhésion à ces groupes si le paramètre de stratégie sur les groupes le permet. Si ce commutateur a la valeur Désactivé, les utilisateurs ne peuvent pas créer de groupes, ni modifier les groupes existants dont ils sont propriétaires, mais ils peuvent toujours gérer les appartenances à ces groupes et approuver les demandes d’adhésion à leurs groupes provenant d’autres utilisateurs.

Vous pouvez également utiliser le commutateur **Utilisateurs pouvant utiliser le libre-service pour les groupes de sécurité** pour obtenir un contrôle d’accès plus fin des fonctionnalités de gestion de groupes en libre-service pour vos utilisateurs. Quand le commutateur **Les utilisateurs peuvent créer des groupes** est défini avec la valeur Activé, tous les utilisateurs présents dans votre répertoire sont autorisés à créer des groupes de sécurité et à ajouter des membres à ces groupes. Par ailleurs, en définissant le commutateur **Utilisateurs pouvant utiliser le libre-service pour les groupes de sécurité** avec la valeur Certains, vous limitez la gestion des groupes de sécurité à un groupe d’utilisateurs défini. Quand ce commutateur a la valeur Certains, un groupe appelé SSGMSecurityGroupsUsers est créé dans votre annuaire et seuls les utilisateurs désignés comme membres de ce groupe peuvent alors créer des groupes de sécurité et ajouter des membres aux groupes figurant dans votre annuaire. En définissant le commutateur **Utilisateurs pouvant utiliser le libre-service pour les groupes de sécurité** avec la valeur Tous, vous autorisez tous les utilisateurs de votre annuaire à créer des groupes de sécurité.

Vous pouvez également utiliser le champ **Groupe pouvant utiliser le libre-service pour les groupes de sécurité** (défini par défaut avec la valeur « SSGMSecurityGroupsUsers ») pour spécifier le nom personnalisé d’un groupe qui rassemble tous les utilisateurs autorisés à utiliser le libre-service et à créer des groupes de sécurité dans votre annuaire.

## Informations supplémentaires

Ces articles fournissent des informations supplémentaires sur Azure Active Directory.

* [Gestion de l’accès aux ressources avec les groupes Azure Active Directory](active-directory-manage-groups.md)

* [Index d’articles pour la gestion des applications dans Azure Active Directory](active-directory-apps-index.md)

* [Qu’est-ce qu’Azure Active Directory ?](active-directory-whatis.md)

* [Intégration des identités locales dans Azure Active Directory](active-directory-aadconnect.md)

<!---HONumber=AcomDC_0211_2016-->