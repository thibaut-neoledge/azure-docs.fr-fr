<properties
	pageTitle="Fédération avec Azure AD Connect | Microsoft Azure"
	description="Cette page centralise toute la documentation portant sur les opérations AD FS avec Azure AD Connect"
	services="active-directory"
	documentationCenter=""
	authors="anandyadavmsft"
	manager="femila"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/14/2016"
	ms.author="anandy"/>


# Fédération avec Azure AD Connect

Azure AD Connect vous permet de configurer la fédération avec AD FS et Azure AD au niveau local. Avec l’authentification de fédération, vous pouvez autoriser les utilisateurs à se connecter aux services Azure AD avec leurs mots de passe locaux sans avoir à les saisir de nouveau, et ce, alors qu’ils sont sur le réseau d’entreprise. L’option de fédération avec les services de fédération d’Azure Directory (AD FS) vous permet de déployer un nouveau service AD FS dans la batterie de serveurs Windows Server 2012 R2 ou d’en spécifier un existant.

Cette rubrique présente les fonctions associées à la fédération pour Azure AD Connect et répertorie les liens vers toutes les autres rubriques qui lui sont associées. Pour obtenir les liens vers Azure AD Connect, consultez Intégration de vos identités locales avec Azure Active Directory.

## Azure AD Connect : rubriques sur la fédération

| Rubrique | Sujet qu’elle aborde et quand la consulter |
|:------|:-----------|
| **Options de connexion de l’utilisateur via Azure AD Connect** ||
| [Présentation des options de connexion de l’utilisateur](active-directory-aadconnect-user-signin.md) | Description des options de connexion de l’utilisateur et de leur impact sur l’expérience utilisateur de connexion à Azure |
| **Installation d’AD FS avec Azure AD Connect**||
| [Conditions préalables](active-directory-aadconnect-get-started-custom.md#ad-fs-configuration-pre-requisites) | Conditions préalables à l’installation d’AD FS via Azure AD Connect|
| [Configurer la batterie de serveurs AD FS](active-directory-aadconnect-get-started-custom.md#configuring-federation-with-ad-fs) | Installation d’une nouvelle batterie de serveurs AD FS avec Azure AD Connect |
| **Modification de la configuration AD FS** | |
| [Réparation de l’approbation](active-directory-aadconnect-federation-management.md#reparing-the-trust) | Rétablissement de l’approbation actuelle entre AD FS et Office 365 / Azure au niveau local |
| [Ajout d’un nouveau serveur AD FS](active-directory-aadconnect-federation-management.md#adding-a-new-ad-fs-server) | Extension de batterie de serveurs AD FS avec l’ajout d’un serveur AD FS après l’installation initiale |
| [Ajout d’un nouveau serveur WAP AD FS](active-directory-aadconnect-federation-management.md#adding-a-new-wap-server) | Extension de batterie de serveurs AD FS avec l’ajout d’un serveur WAP après l’installation initiale |
| [Ajouter un nouveau domaine fédéré](active-directory-aadconnect-federation-management.md#add-a-new-federated-domain) | Ajout d’un domaine à fédérer avec Azure AD |
|**Tâches postérieures à l’installation**||
| [Ajout du logo / de l’illustration personnalisé(e) de la société](active-directory-aadconnect-federation-management.md#add-custom-company-logo-or-illustration)| Modifier l’expérience de connexion via la spécification du logo personnalisé qui apparaîtra sur la page de connexion AD FS |
| [Ajouter une description de connexion](active-directory-aadconnect-federation-management.md#add-sign-in-description) | Modification de la description de la connexion sur la page de connexion AD FS | 
| [Modification des règles de revendication AD FS](active-directory-aadconnect-federation-management.md#modifying-ad-fs-claim-rules) | Modifier / ajouter des règles de revendication dans AD FS pour configurer la synchronisation Azure AD Connect |


## Ressources supplémentaires

* [Intégration de vos identités locales avec Azure Active Directory](active-directory-aadconnect.md)
* [Déploiement des services AD FS dans Azure](active-directory-aadconnect-azure-adfs.md)

<!---HONumber=AcomDC_0928_2016-->