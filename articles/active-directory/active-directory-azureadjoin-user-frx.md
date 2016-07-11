<properties
	pageTitle="Configuration d’un nouvel appareil avec Azure AD lors de l’installation | Microsoft Azure"
	description="Rubrique qui explique comment les utilisateurs peuvent configurer Azure AD Join lors de l’introduction de l’interface logicielle lors de la première utilisation."
	services="active-directory"
	documentationCenter=""
	authors="femila"
	manager="swadhwa"
	editor=""
	tags="azure-classic-portal"/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/23/2016"
	ms.author="femila"/>

# Configuration d’un nouvel appareil avec Azure AD lors de l’installation

Sous Windows 10, les utilisateurs peuvent joindre leur appareil à Azure Active Directory (Azure AD) au cours de l’introduction de l’interface logicielle lors de la première utilisation. Ce système permet aux entreprises de distribuer des appareils emballés à leurs employés et étudiants, ou de les laisser choisir leur propre appareil (CYOD). Si l’édition Windows 10 Professionnel ou Windows 10 Entreprise est installée sur un appareil, l'expérience par défaut est le processus d'installation des appareils d'entreprise.

## Pour joindre un appareil à Azure AD


1. Lorsque vous mettez sous tension votre nouveau périphérique et démarrez le processus d'installation, le message **Préparation** s’affiche. Suivez les invites pour configurer votre appareil.
2. Commencez par personnaliser votre région et votre langue. Ensuite, acceptez les termes du contrat de licence du logiciel Microsoft. ![Personnalisation pour votre région](./media/active-directory-azureadjoin/active-directory-azureadjoin-customize-region.png)
3. Sélectionnez le réseau que vous souhaitez utiliser pour la connexion à Internet.
4. Sélectionnez si vous utilisez un périphérique personnel ou un périphérique appartenant à l'entreprise. S’il s’agit d’un périphérique appartenant à l’entreprise, cliquez sur **Ce périphérique appartient à mon organisation**. L'expérience Azure AD Join démarre. Voici un des écrans que vous voyez si vous utilisez Windows 10 Professionnel.
<center>
![Qui possède cet écran d’ordinateur](./media/active-directory-azureadjoin/active-directory-azureadjoin-who-owns-pc.png)

5.	Entrez les informations d'identification fournies par votre organisation.
<center>
![Écran de connexion](./media/active-directory-azureadjoin/active-directory-azureadjoin-sign-in.png)
6.	Une fois que vous avez entré votre nom d'utilisateur, un client correspondant se trouve dans Azure AD. Si vous êtes dans un domaine fédéré, vous serez redirigé vers votre serveur local de service d’émission de jeton de sécurité (STS) (par exemple, les services de fédération Azure Directory AD FS).
7. Si vous êtes un utilisateur dans des domaines non fédérés, entrez vos informations d'identification directement sur la page hébergée sur Azure AD. Si la marque de votre société a été ajoutée, vous allez également voir le logo de votre organisation et le texte d’accompagnement.
8.	Vous devrez ensuite relever un défi Multi-Factor Authentication. Ce défi est configurable par un administrateur informatique.
9.	Azure AD vérifie si cet utilisateur/appareil exige une inscription à la gestion des appareils mobiles.
10.	Windows enregistre l’appareil dans le répertoire de l’organisation dans Azure AD et l’inscrit dans la gestion des appareils mobiles, le cas échéant.
11.	Si vous êtes un utilisateur géré, Windows vous dirige vers le bureau par le biais du processus de connexion automatique.
12.	Si vous êtes un utilisateur fédéré, vous accédez à l’écran d’ouverture de session Windows pour entrer vos informations d’identification.

> [AZURE.NOTE] Joindre un domaine Active Directory Windows Server local dans Windows Out-of-Box Experience n'est pas pris en charge. Par conséquent, si vous souhaitez joindre un ordinateur à un domaine, il est préférable de sélectionner le lien **Configurer Windows à l’aide d’un compte local**. Vous pouvez ensuite joindre le domaine à partir des paramètres de votre ordinateur comme vous l’avez déjà fait auparavant.

## Informations supplémentaires
* [Windows 10 pour l’entreprise : plusieurs manières d’utiliser des appareils professionnels](active-directory-azureadjoin-windows10-devices-overview.md)
* [Extension des fonctionnalités du cloud aux appareils Windows 10 via Azure Active Directory Join](active-directory-azureadjoin-user-upgrade.md)
* [Authentification des identités sans mot de passe avec Microsoft Passport](active-directory-azureadjoin-passport.md)
* [En savoir plus sur les scénarios d’utilisation pour Azure AD Join](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Connecter des appareils joints au domaine à Azure AD pour des expériences Windows 10](active-directory-azureadjoin-devices-group-policy.md)
* [Configuration d’Azure AD Join](active-directory-azureadjoin-setup.md)

<!---HONumber=AcomDC_0629_2016-->