<properties 
	pageTitle="Raisons pour lesquelles un compte d’administrateur général Azure AD est requis pour configurer Azure AD Connect"
	description="Description de la configuration personnalisée et des raisons pour lesquelles nous demandons un compte d’administrateur général."
	services="active-directory"
	documentationCenter=""
	authors="billmath"
	manager="stevenpo"
	editor="curtand"/>

<tags 
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/24/2015"
	ms.author="billmath"/>

# Raisons pour lesquelles un compte d’administrateur général Azure AD est requis pour configurer Azure AD Connect

Le tableau suivant présente les raisons pour lesquelles un compte d’administrateur général est obligatoire pour configurer Azure AD Connect.

Dans les conditions suivantes | Description 
------------- | ------------- |
Pour la configuration rapide et la mise à niveau de la synchronisation d’annuaire | Nous activons la synchronisation (le cas échéant) dans votre annuaire Azure AD et créons le compte Azure AD qui sera utilisé pour les opérations de synchronisation continue (compte Connecteur Azure AD). 
Pour la configuration personnalisée | Nous activons la synchronisation dans votre annuaire Azure AD et créons le compte Azure AD qui sera utilisé pour les opérations de synchronisation continue (compte Connecteur Azure AD). Pour l’authentification unique avec l'option AD FS, nous lisons et configurons les propriétés de fédération dans Azure AD.



**Ressources supplémentaires**


* [Informations supplémentaires sur les autorisations et les comptes Azure AD Connect](active-directory-aadconnect-account-summary.md)
* [Installation personnalisée d’Azure AD Connect](active-directory-aadconnect-get-started-custom.md)

<!---HONumber=August15_HO9-->