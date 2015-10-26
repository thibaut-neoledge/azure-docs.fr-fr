<properties 
    pageTitle="Ajout d'un utilisateur dans votre collection Azure RemoteApp" 
    description="Découvrez comment ajouter des utilisateurs dans votre collection Azure RemoteApp" 
    services="remoteapp" 
	documentationCenter="" 
    authors="lizap" 
    manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/12/2015" 
    ms.author="elizapo" />

# Procédure : ajout d'un utilisateur dans votre collection Azure RemoteApp

Avant que vos utilisateurs puissent afficher et utiliser vos applications dans Azure RemoteApp, vous devez leur accorder l’accès à votre collection. C'est l'étape la plus facile : sous l'onglet **Accès utilisateur**, entrez les informations de compte de l'utilisateur, puis cliquez sur la coche.

Quelles sont les informations de compte dont vous avez besoin ? Cela dépend du type de collection que vous avez créé (cloud ou hybride) et si vous utilisez Office 365 ProPlus dans cette collection.

## Identités d'utilisateurs prises en charge

Les différents types de collection (cloud ou hybride) prennent en charge différentes identités d’utilisateur pour l’accès aux applications.

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
|Fournisseurs d'identités prises en charge Azure tiers (par exemple Ping) |Oui |Oui|	
|Authentification multifacteur |Oui |Oui |

Consultez [plus d'informations](remoteapp-ad.md) sur la configuration d'Active Directory pour RemoteApp.


> [AZURE.NOTE]Les utilisateurs Azure Active Directory doivent provenir du locataire associé à votre abonnement. (Vous pouvez afficher et modifier votre abonnement sous l'onglet **Paramètres** du portail. Consultez [Modifier le locataire Azure Active Directory utilisé par RemoteApp](remoteapp-changetenant.md) pour plus d'informations.)

## Informations de compte d'utilisateur Office 365 ProPlus
Si vous utilisez l'image de modèle Office 365 ProPlus dans votre collection *ou* si vous avez créé une image personnalisée qui utilise Office 365, vous êtes uniquement autorisé à ajouter des utilisateurs Azure Active Directory disposant d'abonnements Office 365 pour le domaine par défaut de votre abonnement. Consultez [Utilisation d'Office 365 avec Azure RemoteApp](remoteapp-o365.md) pour plus d'informations.
 

<!---HONumber=Oct15_HO3-->