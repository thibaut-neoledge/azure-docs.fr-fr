<properties 
	pageTitle="Raisons de la nécessité d’un compte administrateur d’entreprise" 
	description="Description de la configuration personnalisée." 
	services="active-directory" 
	documentationCenter="" 
	authors="billmath" 
	manager="swadhwa" 
	editor="curtand"/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/28/2015" 
	ms.author="billmath"/>

# Raisons de la nécessité d’un compte administrateur d’entreprise pour se connecter à AD DS lors de la configuration d’Azure AD Connect

Le tableau suivant présente les raisons pour lesquelles un compte administrateur d’entreprise est obligatoire pour configurer Azure AD Connect.

Dans les conditions suivantes | Description 
------------- | ------------- |
Pour la configuration rapide et la mise à niveau de la synchronisation d’annuaire | <li>Pour la configuration rapide, nous créons le compte Active Directory local qui est utilisé pour la synchronisation (également appelé compte du connecteur AD) et attribuons des autorisations appropriées pour la synchronisation et la synchronisation de mot de passe.</li> <li>Pour la mise à niveau de la synchronisation d’annuaire, nous réinitialisons le mot de passe sur le compte actuellement configuré du connecteur AD et configurons le nouveau service de synchronisation Azure AD Connect pour qu’il utilise ce compte. </li>



**Ressources supplémentaires**


* [Informations supplémentaires sur les autorisations et les comptes Azure AD Connect](active-directory-aadconnect-account-summary.md)
* [Installation personnalisée d’Azure AD Connect](active-directory-aadconnect-get-started-custom.md)
* [Azure AD Connect sur MSDN](https://msdn.microsoft.com/library/azure/dn832695.aspx) 

<!---HONumber=July15_HO4-->