<properties 
	pageTitle="Connexion de vos annuaires avec Azure AD Connect" 
	description="Description de la configuration personnalisée des annuaires connectés Azure AD Connect." 
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



# Connexion de vos annuaires avec Azure AD Connect

Pour Configuration personnalisée, un compte d’administrateur d'entreprise n'est pas requis pour la connexion aux forêts Active Directory. L’Assistant accepte un domaine ou un compte d'utilisateur local. Toutefois, le compte est utilisé comme compte de connecteur AD local, autrement dit, il s'agit du compte qui lit et écrit les informations d'annuaire pour la synchronisation.

Cela signifie que vous devez attribuer des autorisations supplémentaires pour activer les scénarios suivants :

Scénario |Autorisation
------------- | ------------- |
Synchronisation de mot de passe| <li>Répliquer les changements d’annuaires</li> <li>Répliquer tous les changements d’annuaires</li>
Déploiement Exchange hybride|Consultez la page [Autorisations et attributs en écriture différée AAD Sync Office 365 Exchange hybride](https://msdn.microsoft.com/library/azure/dn757602.aspx#exchange).
Écriture différée de mot de passe | <li>Modifier le mot de passe</li><li>Réinitialiser le mot de passe</li>
Utilisateur, Groupe et Écriture différée des appareils|Autorisations en écriture sur les objets d’annuaire et les attributs qui feront l’objet de l’écriture différée
Authentification unique et AD FS| Autorisations d’administrateur de domaine dans le domaine dans lequel se trouvent vos serveurs fédérés.





**Ressources supplémentaires**

* [Informations supplémentaires sur les autorisations et les comptes Azure AD Connect](active-directory-aadconnect-account-summary.md)
* [Autorisations pour la synchronisation de mot de passe](https://msdn.microsoft.com/library/azure/dn757602.aspx#psynch)
* [Autorisations pour Exchange hybride](https://msdn.microsoft.com/library/azure/dn757602.aspx#exchange)
* [Autorisations d’écriture différée du mot de passe](https://msdn.microsoft.com/library/azure/dn757602.aspx#pwriteback)
* [Installation personnalisée d’Azure AD Connect](active-directory-aadconnect-get-started-custom.md)
* [Azure AD Connect sur MSDN](https://msdn.microsoft.com/library/azure/dn832695.aspx)
 

<!---HONumber=62-->