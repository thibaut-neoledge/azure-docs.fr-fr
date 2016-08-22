<properties
	pageTitle="Modifier l’algorithme de hachage signature pour l’approbation de partie de confiance O365 | Microsoft Azure"
	description="Cette page fournit des instructions permettant de modifier l’algorithme SHA pour l’approbation de fédération avec O365"
    keywords="SHA1, SHA256, O365, fédération, aadconnect, adfs, ad fs, modifier sha, approbation de fédération, approbation de partie de confiance"
	services="active-directory"
	documentationCenter=""
	authors="anandyadavmsft"
	manager="samueld"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/01/2016"
	ms.author="anandy"/>

#Modifier l’algorithme de hachage signature pour l’approbation de partie de confiance O365

##Vue d'ensemble

AD FS signe ses jetons sur Azure Active Directory pour s’assurer qu’ils ne peuvent pas être falsifiés. Cette signature peut reposer sur SHA1 ou SHA256. Microsoft Azure Active Directory prend maintenant en charge les jetons signés avec un algorithme SHA256 et recommande de configurer l’algorithme de signature de jeton sur SHA256 pour un niveau de sécurité maximal. Cet article décrit l’étape de définition de l’algorithme de signature de jeton pour le niveau de sécurité le plus élevé, SHA256.

##Modification de l’algorithme de signature de jetons

Une fois que vous avez défini l’algorithme de signature comme indiqué dans les étapes ci-dessous, AD FS commencera à signer les jetons pour l’approbation de partie de confiance O365 avec l’algorithme SHA-256. Il n’existe aucune modification de configuration supplémentaire nécessaire, et cette modification n’a aucun impact sur vos utilisateurs qui accèdent à Office 365 ou d’autres applications Azure AD.

###Utilisation de la console de gestion

* Ouvrez la console de gestion AD FS sur le serveur AD FS principal.
* Développez le nœud AD FS et cliquez sur les approbations de partie de confiance.
* Faites un clic droit sur votre approbation de partie de confiance O365/azure/Azure et sélectionnez Propriétés.
* Sélectionnez l’onglet Avancé et sélectionnez l’algorithme de hachage sécurisé SHA256
* Cliquez sur OK

![Algorithme de signature SHA256 - MMC](./media/active-directory-aadconnectfed-sha256guidance/mmc.png)

###Utilisation des applets de commande PowerShell AD FS

* Sur n’importe quel serveur AD FS, ouvrez PowerShell avec les privilèges d’administrateur
* Définissez l’algorithme de hachage sécurisé avec l’applet de commande Set-AdfsRelyingPartyTrust

 <code>Set-AdfsRelyingPartyTrust -TargetName 'Microsoft Office 365 Identity Platform' -SignatureAlgorithm 'http://www.w3.org/2001/04/xmldsig-more#rsa-sha256' </code>

##Lisez également

* [Réparer l’approbation O365 avec AAD Connect](./active-directory-aadconnect-federation-management.md#repairing-the-trust)

<!---HONumber=AcomDC_0810_2016-->