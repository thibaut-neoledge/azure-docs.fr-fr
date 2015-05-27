<properties 
    pageTitle="Ajout d'un utilisateur dans RemoteApp" 
    description="Learn how to add users in RemoteApp" 
    services="remoteapp" 
    solutions="" 
	documentationCenter="" 
    authors="lizap" 
    manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="04/06/2015" 
    ms.author="elizapo" />

# Ajout d'un utilisateur dans RemoteApp

Avant que vos utilisateurs puissent afficher et utiliser les applications de RemoteApp, vous devez leur accorder l'accès. C'est l'étape la plus facile : sous l'onglet **Accès utilisateur**, entrez les informations de compte de l'utilisateur auquel accorder l'accès à ce service.

Quelles sont les informations de compte dont vous avez besoin ? Cela dépend du type de collection que vous avez créé (cloud ou hybride) et si vous utilisez Office 365 ProPlus dans cette collection.

## Identités d'utilisateurs prises en charge

Azure RemoteApp comporte deux méthodes de déploiement : cloud et hybride. Chacune d'elles prend en charge l'utilisation de différentes identités d'utilisateurs pour l'accès utilisateur aux applications.

Pour une collection hybride de RemoteApp, vous devez configurer une infrastructure de domaine Active Directory locale et un locataire Azure Active Directory avec intégration d'annuaire (et éventuellement l'authentification unique). Par ailleurs, vous devez créer des objets Active Directory dans l'annuaire local.

Pour une collection cloud de RemoteApp, tout utilisateur prenant en charge les identités Azure Active Directory peut recevoir l'accès utilisateur à RemoteApp pour inclure des comptes Microsoft. Consultez le tableau ci-dessous.

Les utilisateurs Office 365 sont des utilisateurs Azure Active Directory. S'ils possèdent des comptes Azure Active Directory hybrides synchronisés, ils peuvent bénéficier de l'accès utilisateur dans un déploiement hybride de RemoteApp.

Vous pouvez utiliser ce tableau de référence pour déterminer rapidement quelle identité est prise en charge dans votre collection et quelle configuration est requise pour Active Directory.

|Comptes d'utilisateurs |Cloud |Hybride|
|--------------|--------|------|
|Compte Microsoft| 	Oui|	Non|
|Azure Active Directory (Azure AD)| | |	
|Cloud Azure AD uniquement |Oui |Non |
|ADsync avec synchronisation de mot de passe |Oui |Oui |
|ADsync sans synchronisation de mot de passe|	Oui |Non |
|ADsync avec AD FS |Oui |Oui |
|Fournisseurs d'identités prises en charge Azure tiers (par exemple Ping) |Oui |Non|	
|Azure Multi-Factor Authentication |Oui |Oui |

Consultez [plus d'informations](remoteapp-ad.md) sur la configuration d'Active Directory pour RemoteApp.


**Remarque :** les utilisateurs Azure Active Directory doivent provenir du locataire associé à votre abonnement. (Vous pouvez afficher et modifier votre abonnement sous l'onglet **Paramètres** du portail. Consultez [Modifier le locataire Azure Active Directory utilisé par RemoteApp](remoteapp-changetenant.md) pour plus d'informations.)

## Informations de compte d'utilisateur Office 365 ProPlus
Si vous utilisez l'image de modèle Office 365 ProPlus dans votre collection *ou* si vous avez créé une image personnalisée qui utilise Office 365, vous êtes uniquement autorisé à ajouter des utilisateurs Azure Active Directory disposant d'abonnements Office 365 pour le domaine par défaut de votre abonnement. Consultez [Utilisation d'Office 365 avec Azure RemoteApp](remoteapp-o365.md) pour plus d'informations.

<!--HONumber=54-->