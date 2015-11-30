<properties 
	pageTitle="Configuration d’un nouvel appareil avec Azure AD lors de l’installation | Microsoft Azure" 
	description="Rubrique qui explique comment les utilisateurs peuvent configurer Azure AD Join lors de l’introduction de l’interface logicielle lors de la première utilisation." 
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
	ms.date="11/17/2015" 
	ms.author="femila"/>

# Configuration d’un nouvel appareil avec Azure AD lors de l’installation

Dans Windows 10, les utilisateurs finaux peuvent joindre leurs appareils à Azure AD lors de l’introduction de l’interface logicielle au cours de la première utilisation. Ce système permet aux entreprises de distribuer des appareils emballés à leurs employés et étudiants, ou de les laisser choisir leur propre appareil (CYOD). Si vous installez la référence professionnelle ou entreprise de Windows 10, le programme d’installation pour les appareils détenus par une entreprise est affiché par défaut.

Pour joindre un appareil à Azure AD
-----------------------------------------------------------------------

1. Après l’étape **Préparation**, vous êtes invité par l'assistant d'installation.
2. Commencez par personnaliser la langue et la région, acceptez le CLUF et connectez-vous.
<center> ![](./media/active-directory-azureadjoin/active-directory-azureadjoin-customize-region.png) </center>
3. Sélectionnez le réseau à utiliser pour vous connecter à Internet.
4. Sélectionnez s'il s'agit d'un périphérique personnel ou d’un périphérique appartenant à l'entreprise :
5. Cliquez sur **Ce périphérique appartient à mon organisation**. L'expérience Azure AD Join démarre. Voici un des écrans que vous voyez dans la référence professionnelle de Windows 10. 
<center> ![](./media/active-directory-azureadjoin/active-directory-azureadjoin-who-owns-pc.png) </center>

6.	Entrez les informations d'identification fournies par votre organisation.
<center> ![](./media/active-directory-azureadjoin/active-directory-azureadjoin-sign-in.png) </center>
7.	Une fois que vous avez entré votre nom d'utilisateur, un client correspondant se trouve dans Azure AD. Si vous êtes dans un domaine fédéré, vous serez redirigé vers votre serveur local de service d’émission de jeton de sécurité (par exemple, AD FS). Si vous êtes un utilisateur dans des domaines non fédérés, vous devez entrer vos informations d'identification directement sur la page hébergée sur Azure AD. Vous allez également voir le logo de votre organisation et le texte d’accompagnement si la marque de votre société a été ajoutée.
8.	Vous devrez ensuite relever un défi Multi-Factor Authentication. Cette étape est configurable par le personnel informatique.
9.	Azure AD vérifie ensuite si cet utilisateur/appareil exige une inscription Gestion des appareils mobiles (MDM). 
10.	Windows enregistre ensuite l’appareil dans le répertoire de l'organisation dans Azure AD et l'inscrit dans MDM.
11.	Dans ce cas, si vous êtes un utilisateur géré, Windows conclut le processus d'installation et vous dirige vers le bureau via la connexion automatique.
12.	Si vous êtes un utilisateur fédéré, vous accédez à l'écran d'ouverture de session Windows et devez alors entrer vos informations d'identification pour vous connecter.

> [AZURE.NOTE]Joindre un domaine Active Directory local dans Windows Out-of-Box Experience n'est pas pris en charge. Par conséquent, si vous souhaitez joindre un ordinateur à un domaine, vous devez sélectionner le lien « Configurer plutôt Windows avec un compte local ». Vous pouvez ensuite joindre le domaine à partir des paramètres du PC comme vous l’avez déjà fait auparavant.

## Informations supplémentaires
* [Windows 10 pour l’entreprise : plusieurs manières d’utiliser des appareils professionnels](active-directory-azureadjoin-windows10-devices-overview.md)
* [Extension des fonctionnalités du cloud aux appareils Windows 10 via Azure Active Directory Join](active-directory-azureadjoin-user-upgrade.md)
* [Authentification des identités sans mot de passe avec Microsoft Passport](active-directory-azureadjoin-passport.md)
* [En savoir plus sur les scénarios d’utilisation pour Azure AD Join](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Connecter des appareils joints au domaine à Azure AD pour des expériences Windows 10](active-directory-azureadjoin-devices-group-policy.md)
* [Configuration d’Azure AD Join](active-directory-azureadjoin-setup.md)

<!---HONumber=Nov15_HO4-->