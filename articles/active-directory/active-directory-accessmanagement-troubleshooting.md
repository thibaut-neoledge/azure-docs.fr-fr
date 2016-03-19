
<properties
	pageTitle="Résolution des problèmes liés à l’appartenance dynamique à des groupes | Microsoft Azure"
	description="Cette rubrique contient des conseils pour résoudre les problèmes d’appartenance dynamique à des groupes dans Azure AD."
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


# Résolution des problèmes liés à l’appartenance dynamique à des groupes

| Symptôme | Action |
|--------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| J’ai configuré une règle sur un groupe, mais aucune appartenance n’est mise à jour dans le groupe. | Sous l’onglet Configuration de l’annuaire, vérifiez que le paramètre **Activer la gestion déléguée des groupes** a la valeur Oui. Ce paramètre s’affiche uniquement si vous êtes connecté en tant qu’utilisateur auquel est attribuée une licence Azure Active Directory Premium. Vérifiez les valeurs des attributs d’utilisateur sur la règle : des utilisateurs satisfont-ils à la règle ? |
| J’ai configuré une règle, mais les membres existants sur celle-ci sont à présent supprimés. | Ce comportement est normal. Lors de l’activation ou de la modification d’une règle, les membres existants du groupe sont supprimés. Les utilisateurs provenant de l’évaluation de la règle sont ajoutés en tant que membres du groupe. |
| Je ne vois pas instantanément de changement d’appartenance quand j’ajoute ou quand je modifie une règle. Pourquoi ? | L’évaluation de l’appartenance dédiée est effectuée régulièrement dans un processus asynchrone en arrière-plan. La durée de l’opération est déterminée par le nombre d’utilisateurs dans votre annuaire et par la taille du groupe créé à la suite de la règle. Généralement, les annuaires contenant peu d’utilisateurs constatent les changements d’appartenance au groupe en moins de quelques minutes. Le renseignement des annuaires contenant de nombreux utilisateurs peut prendre plus de 30 minutes. |

Ces articles fournissent des informations supplémentaires sur Azure Active Directory.

* [Gestion de l’accès aux ressources avec les groupes Azure Active Directory](active-directory-manage-groups.md)
* [Index d’articles pour la gestion des applications dans Azure Active Directory](active-directory-apps-index.md)
* [Qu’est-ce qu’Azure Active Directory ?](active-directory-whatis.md)
* [Intégration des identités locales dans Azure Active Directory](active-directory-aadconnect.md)

<!---HONumber=AcomDC_0211_2016-->