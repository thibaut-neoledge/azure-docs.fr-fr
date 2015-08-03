
<properties 
	pageTitle="Résolution des problèmes liés à l’appartenance dynamique à des groupes | Microsoft Azure" 
	description="Cette rubrique contient des conseils pour résoudre les problèmes d’appartenance dynamique à des groupes dans Azure AD." 
	services="active-directory" 
	documentationCenter="" 
	authors="femila" 
	manager="swadhwa" 
	editor="Curtis"
	tags="azure-classic-portal"/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/13/2015" 
	ms.author="femila"/>


# Résolution des problèmes liés à l’appartenance dynamique à des groupes

| Symptôme | Action |
|--------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| J’ai configuré une règle sur un groupe, mais aucune appartenance n’est mise à jour dans le groupe. | Sous l’onglet Configuration de l’annuaire, vérifiez que le paramètre Activer la gestion déléguée des groupes a la valeur Oui. Notez que ce paramètre n’est visible que si vous êtes connecté avec un compte auquel une licence Azure Active Directory Premium est affectée. Vérifiez les valeurs des attributs d’utilisateur sur la règle : des utilisateurs satisfont-ils à la règle ? |
| J’ai configuré une règle, mais les membres existants sur celle-ci sont à présent supprimés. | Ce comportement est normal : lors de l’activation ou de la modification d’une règle, les membres existants du groupe sont supprimés. Les utilisateurs résultant de l’évaluation de la règle sont ajoutés en tant que membres au groupe. |
| Je ne vois pas instantanément de changement d’appartenance quand j’ajoute ou quand je modifie une règle. Pourquoi ? | L’évaluation de l’appartenance dédiée est effectuée régulièrement dans un processus asynchrone en arrière-plan. La durée de l’opération dépend du nombre d’utilisateurs dans votre client et de la taille du groupe créé à la suite de la règle. Généralement les clients avec peu d’utilisateurs constatent les changements d’appartenance au groupe en moins de quelques minutes. Le renseignement des clients avec de nombreux utilisateurs peut prendre plus de 30 minutes. |

Voici des rubriques qui fournissent des informations supplémentaires sur Azure Active Directory.

* [Gestion de l’accès aux ressources avec les groupes Azure Active Directory](active-directory-manage-groups.md)

* [Qu’est-ce qu’Azure Active Directory ?](active-directory-whatis.md)

* [Intégration des identités locales dans Azure Active Directory](active-directory-aadconnect.md)

<!---HONumber=July15_HO4-->