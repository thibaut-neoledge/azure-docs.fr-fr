<properties
	pageTitle="Association d’un appareil personnel à votre organisation | Microsoft Azure"
	description="Rubrique traitant de la façon dont les utilisateurs peuvent inscrire leurs ordinateurs Windows 10 personnels sur le réseau de leur entreprise."
	services="active-directory"
	documentationCenter=""
	authors="femila"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/21/2015"
	ms.author="femila"/>

# Association d’un appareil personnel à votre organisation

Association d’un appareil Windows 10 à votre organisation
--------------------------------------------------------------------------------------------
1.	Dans le menu **Démarrer**, sélectionnez **Paramètres**.
2.	Sélectionnez **Comptes** puis cliquez sur **Votre compte**.
3.	Cliquez sur **Ajouter un compte professionnel ou scolaire** puis saisissez votre compte professionnel.
4.	Vous êtes alors redirigé vers la page de connexion de votre organisation. Entrez votre nom d'utilisateur et un mot de passe et cliquez sur **OK**.
5.	Vous devrez ensuite relever un défi Multi-Factor Authentication. Cette étape est configurable par le personnel informatique.
6.	Azure AD vérifie ensuite si cet utilisateur/appareil exige une inscription Gestion des appareils mobiles (MDM).
7.	Windows enregistre ensuite l’appareil dans le répertoire de l'organisation dans Azure AD et l'inscrit dans MDM.
8.	Dans ce cas, si vous êtes un utilisateur géré, Windows conclut le processus d'installation et vous dirige vers le bureau via l'écran de connexion automatique.
9.	Si vous êtes un utilisateur fédéré, vous accédez à l'écran d'ouverture de session Windows et devez alors entrer vos informations d'identification.

## Informations supplémentaires
* [Extension des fonctionnalités du cloud aux appareils Windows 10 via Azure Active Directory Join](active-directory-azureadjoin-overview.md)
* [En savoir plus sur les scénarios d'utilisation et les considérations relatives au déploiement pour Azure AD Join](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Configuration d’Azure AD Join](active-directory-azureadjoin-setup.md)

<!---HONumber=Sept15_HO4-->