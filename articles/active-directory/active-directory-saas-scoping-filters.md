<properties
	pageTitle="Approvisionnement d’applications basé sur les attributs avec filtres d’étendue | Microsoft Azure"
	description="Découvrez comment utiliser des filtres d’étendue pour empêcher les objets dans les applications qui prennent en charge l’approvisionnement automatisé des utilisateurs d’être aprovisionnés si un objet n’est pas conforme à vos besoins."
	services="active-directory"
	documentationCenter=""
	authors="markusvi"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/09/2016"
	ms.author="markusvi"/>


# Approvisionnement d’applications basé sur les attributs avec filtres d’étendue

L’objectif de cette section est d’expliquer comment utiliser des filtres d’étendue pour définir des règles basées sur des attributs qui déterminent quels utilisateurs seront approvisionnés pour l’application.





## Clauses et groupes d’étendue


![Filtre d’étendue][1]




Les filtres d’étendue sont définis par un ou plusieurs **groupes d’étendue**, chacun contenant une ou plusieurs **clauses**. Pour afficher les clauses d’un groupe d’étendue particulier, développez-le en cliquant sur la flèche située à gauche du nom du groupe.

Une **clause** détermine quels utilisateurs sont autorisés à traverser le filtre d’étendue en évaluant les attributs de chaque utilisateur. Par exemple, vous pouvez avoir une clause qui exige que l’attribut « état » d’un utilisateur soit égal à New York, ce qui signifie que seuls les utilisateurs de New York seront approvisionnés dans l’application.

![Nom du groupe d’étendue][2]



Chaque **groupe d’étendue** commence par une **clause** obligatoire, comme illustré dans la capture d’écran ci-dessus. Cette clause stipule simplement que l’utilisateur doit être préalablement affecté à l’application avant son évaluation par vos filtres d’étendue. Cette clause ne peut pas être supprimée ou modifiée.

Vous pouvez ajouter de nouvelles clauses ou de nouveaux groupes d’étendue en appuyant sur le bouton approprié. Vous pouvez attribuer un nom à chaque groupe d’étendue en modifiant sa propriété **Nom du groupe d’étendue**.





## Évaluation des filtres d’étendue

Lors de l’approvisionnement, nous testons chaque utilisateur affecté par rapport à vos filtres d’étendue pour déterminer si cet utilisateur mérite l’accès à l’application. On peut considérer chaque clause comme un test qui doit être réussi pour que l’utilisateur ne soit pas filtré et exclu.

Si vous avez défini plusieurs groupes d’étendue, chaque utilisateur doit satisfaire à au moins l’un d’eux pour accéder à l’application. Dans chaque groupe d’étendue, toutefois, l’utilisateur doit satisfaire à chaque clause pour satisfaire à ce groupe d’étendue spécifique.

En d’autres termes, on peut considérer les groupes d’étendue comme étant liés par une opération logique OU et les clauses au sein de ces groupes comme étant liées par une opération logique ET. Par exemple, considérez le filtre d’étendue ci-dessous :


![Nom du groupe d’étendue][2]


D’après de ce filtre d’étendue, les utilisateurs doivent satisfaire aux critères suivants pour être approvisionnés :

1. Ils doivent être affectés à l’application.

2. Ils doivent travailler dans le service Ingénierie

3. Ils doivent travailler à San Francisco ou au Canada.


##Articles connexes

- [Index d’articles pour la gestion des applications dans Azure Active Directory](active-directory-apps-index.md)
- [Automatisation de l’approvisionnement et de l’annulation de l’approvisionnement des utilisateurs pour les applications SaaS](active-directory-saas-app-provisioning.md)
- [Personnalisation des mappages d’attributs pour l’approvisionnement des utilisateurs](active-directory-saas-customizing-attribute-mappings.md)
- [Écriture d’expressions pour les mappages d’attributs](active-directory-saas-writing-expressions-for-attribute-mappings.md)
- [Notifications d’approvisionnement de comptes](active-directory-saas-account-provisioning-notifications.md)
- [Utilisation de SCIM pour activer la configuration automatique des utilisateurs et des groupes d’Azure Active Directory sur des applications](active-directory-scim-provisioning.md)
- [Liste des didacticiels sur l’intégration des applications SaaS](active-directory-saas-tutorial-list.md)

<!--Image references-->
[1]: ./media/active-directory-saas-scoping-filters/ic782811.png
[2]: ./media/active-directory-saas-scoping-filters/ic782812.png
[3]: ./active-directory-saas-scoping-filters/ic782813.png

<!---HONumber=AcomDC_0211_2016-->