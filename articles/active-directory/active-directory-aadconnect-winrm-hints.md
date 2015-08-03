<properties 
	pageTitle="Azure AD Connect - Conseils sur la gestion à distance Windows" 
	description="Azure AD Connect - Conseils sur la gestion à distance Windows dans le cadre de l’utilisation d’AD FS." 
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

# Azure AD Connect - Conseils sur la gestion à distance Windows


Lorsque vous utilisez Azure AD Connect pour déployer Active Directory Federation Services ou le proxy d'application web, vérifiez les conseils ci-dessous sur la configuration requise pour garantir la réussite de la connectivité et de la configuration :

- Si le serveur cible est joint à un domaine, assurez-vous que la gestion à distance Windows est activée. 
	* Dans une fenêtre de commandes PSH avec élévation de privilèges, utilisez la commande « Enable-PSRemoting-force ». 

- Si le serveur cible n’est pas un ordinateur WAP joint à un domaine, il existe quelques conditions requises supplémentaires.
	- Sur l'ordinateur cible (ordinateur WAP): 

- Vérifiez que le service winrm (Windows Remote Management/WS-Management) s'exécute via le composant logiciel enfichable Services.

- Dans une fenêtre de commandes PSH avec élévation de privilèges, utilisez la commande « Enable-PSRemoting-force ».
	- Sur l'ordinateur sur lequel s'exécute l'Assistant (si l'ordinateur cible n’est pas joint à un domaine ou s’il est joint à un domaine non fiable) : 

- Dans une fenêtre de commandes PSH avec élévation de privilèges, utilisez la commande « Set-Item WSMan:\localhost\Client\TrustedHosts –Value <DMZServerFQDN> -Force –Concatenate »
	- Dans le Gestionnaire de serveur :
		- Ajoutez un hôte WAP DMZ au pool d’ordinateurs (Gestionnaire de serveur -> Gérer -> Ajouter des serveurs... Onglet Utiliser DNS) 
		- Onglet Gestionnaire de serveur, onglet Tous les serveurs : cliquez avec le bouton droit sur le serveur WAP et sélectionnez Gérer en tant que..., entrez des informations d'identification locales (et pas un domaine) pour l'ordinateur WAP. 
		- Pour valider la connectivité à distance PSH, dans le Gestionnaire de serveur, onglet Tous les serveurs : cliquez avec le bouton droit sur le serveur WAP et choisissez Windows PowerShell. Une session à distance PSH doit s’ouvrir pour garantir le fait que des sessions PowerShell distantes peuvent être établies. 

**Ressources supplémentaires**


* [Informations supplémentaires sur les autorisations et les comptes Azure AD Connect](active-directory-aadconnect-account-summary.md)
* [Installation personnalisée d’Azure AD Connect](active-directory-aadconnect-get-started-custom.md)
* [Azure AD Connect sur MSDN](https://msdn.microsoft.com/library/azure/dn832695.aspx) 

<!---HONumber=July15_HO4-->