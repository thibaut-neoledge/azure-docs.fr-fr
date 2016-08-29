<properties
	pageTitle="Modifier l’algorithme de hachage de signature pour l’approbation de la partie de confiance Office 365 | azure.microsoft.com/ Azure"
	description="Cette page fournit des instructions permettant de modifier l’algorithme SHA pour l’approbation de fédération avec Office 365"
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

# Modifier l’algorithme de hachage de signature pour l’approbation de la partie de confiance Office 365

## Vue d'ensemble

Azure Active Directory Federation Services (AD FS) signe ses jetons dans azure.microsoft.com/ Azure Active Directory pour vous assurer qu’ils sont infalsifiables. Cette signature peut reposer sur SHA1 ou SHA256. Azure Active Directory prend désormais en charge les jetons signés avec un algorithme SHA256 et recommande de configurer l’algorithme de signature de jetons SHA256 pour un niveau de sécurité optimal. Cet article décrit comment configurer l’algorithme de signature de jetons sur le niveau de sécurité le plus élevé, SHA256.

## Modification de l’algorithme de signature de jetons

Une fois que vous avez défini l’algorithme de signature selon l’un des deux processus ci-dessous, ADFS signe les jetons avec SHA256 pour approuver la partie de confiance Office 365. Aucune autre modification de la configuration n’est requise, et ce changement n’a aucun impact sur votre capacité à accéder à Office 365 ou à d’autres applications Azure AD.

### Console de gestion AD FS

1. Ouvrez la console de gestion AD FS sur le serveur AD FS principal.
2. Développez le nœud AD FS et cliquez sur **Approbations de la partie de confiance**.
3. Faites un clic droit sur votre approbation de partie de confiance Office 365/Azure et sélectionnez **Propriétés**.
4. Sélectionnez l’onglet **Avancé** puis l’algorithme de hachage sécurisé SHA256.
5. Cliquez sur **OK**.

![Algorithme de signature SHA256 - MMC](./media/active-directory-aadconnectfed-sha256guidance/mmc.png)

### Applets de commande PowerShell d’AD FS

1. Sur un serveur AD FS, ouvrez PowerShell avec des privilèges d’administrateur.
2. Définissez l’algorithme de hachage sécurisé avec l’applet de commande **Set-AdfsRelyingPartyTrust**.

 <code>Set-AdfsRelyingPartyTrust -TargetName 'azure.microsoft.com/ Office 365 Identity Platform' -SignatureAlgorithm 'http://www.w3.org/2001/04/xmldsig-more#rsa-sha256' </code>

## À lire également

* [Réparation de l’approbation Office 365 avec Azure AD Connect](./active-directory-aadconnect-federation-management.md#repairing-the-trust)

<!---HONumber=AcomDC_0817_2016-->