<properties
	pageTitle="Configuration d’un appareil Windows 10 avec Azure AD à partir des paramètres| Microsoft Azure"
	description="Explique comment les utilisateurs peuvent rejoindre Azure AD via le menu Paramètres."
	services="active-directory"
	documentationCenter=""
	authors="femila"
	manager="stevenpo"
	editor=""
	tags="azure-classic-portal"/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/26/2016"
	ms.author="femila"/>

# Configuration d’un appareil Windows 10 avec Azure AD à partir des paramètres
Si vous utilisez déjà Windows 7 ou Windows 8 et que votre ordinateur ou appareil a été mis à niveau vers Windows 10, vous pouvez le joindre à Azure Active Directory (Azure AD) avec le menu Paramètres.

## Joindre votre ordinateur à Azure AD à partir du menu Paramètres


1. Dans le menu **Démarrer**, cliquez sur l’icône **Paramètres**.
2. Accédez à **Paramètres**, sélectionnez **Système**->**Info produit**->**Rejoindre Azure AD**.
<center> ![Rejoindre Azure AD à partir du menu Paramètres](./media/active-directory-azureadjoin/active-directory-azureadjoin-settings.png) </center>

3. Cliquez sur **Continuer** dans la fenêtre de message Azure AD Join.
<center> ![Fenêtre de message Rejoindre Azure AD](./media/active-directory-azureadjoin/active-directory-azureadjoin-message.png) </center>
4. Indiquez vos informations d’identification de connexion. Ce processus de connexion inclut toutes les étapes requises pour l’authentification. Si vous faites partie d’un client fédéré, votre administrateur vous proposera un processus hébergé par votre organisation.
<center> ![Entrer des informations d’identification de connexion](./media/active-directory-azureadjoin/active-directory-azureadjoin-sign-in.png) </center>
5. Si votre organisation a configuré Azure Multi-Factor Authentication pour joindre vos appareils à Azure AD, vous devrez fournir le second facteur avant de pouvoir continuer.
6. Cliquez sur **Accepter** sur l’écran **Autoriser la gestion de ce périphérique**.
7. Le message « Votre appareil est maintenant joint à votre organisation dans Azure AD » doit apparaître.


## Informations supplémentaires
* [En savoir plus sur les scénarios d’utilisation pour Azure AD Join](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Connecter des appareils joints au domaine à Azure AD pour des expériences Windows 10](active-directory-azureadjoin-devices-group-policy.md)
* [Configuration d’Azure AD Join](active-directory-azureadjoin-setup.md)
* [Authentification des identités sans mot de passe avec Microsoft Passport](active-directory-azureadjoin-passport.md)

<!---HONumber=AcomDC_0302_2016-->