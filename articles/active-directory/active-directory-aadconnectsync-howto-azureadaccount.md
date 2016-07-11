<properties
	pageTitle="Azure AD Connect Sync : comment gérer le compte de service Azure AD | Microsoft Azure"
	description="Cette rubrique explique comment restaurer le compte de service Azure AD."
	services="active-directory"
    keywords="AADSTS70002, AADSTS50054, Comment réinitialiser le mot de passe du compte du service Connecteur de synchronisation Azure AD Connect"
	documentationCenter=""
	authors="andkjell"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/27/2016"
	ms.author="andkjell"/>

# Azure AD Connect Sync : comment gérer le compte de service Azure AD
Le compte de service utilisé par le connecteur Azure AD est censé être proposé en libre-service. Mais si vous devez réinitialiser les informations d’identification, cette rubrique vous concerne. Cela peut se produire par exemple si un administrateur général a réinitialisé par erreur le mot de passe du compte de service à l’aide de PowerShell.

## Réinitialisation des informations d'identification
Si le compte de service défini sur le Connecteur Azure AD ne peut pas contacter Azure AD en raison de problèmes d’authentification, le mot de passe peut être réinitialisé.

1. Connectez-vous au serveur de synchronisation Azure AD Connect et démarrez PowerShell.
2. Exécutez `Add-ADSyncAADServiceAccount`. ![PowerShell cmdlet addadsyncaadserviceaccount](./media/active-directory-aadconnectsync-howto-azureadaccount/addadsyncaadserviceaccount.png)
3. Fournissez les informations d’identification de l’administrateur général Azure AD.

Cette applet de commande réinitialisera le mot de passe du compte de service et l’actualisera dans Azure AD et dans le moteur de synchronisation.

## Problèmes connus pouvant être résolus par les procédures indiquées ci-après
Voici la liste des erreurs signalées par les clients qui ont été corrigées en suivant les procédures indiquées ci-après.

-----------
Événement 6900 Le serveur a rencontré une erreur inattendue lors du traitement d’une notification de modification de mot de passe : AADSTS70002 : erreur de validation des informations d’identification. AADSTS50054 : l’ancien mot de passe est utilisé pour l’authentification.

----------
Événement 659 Erreur lors de la récupération de la configuration de la synchronisation de stratégie de mot de passe. Microsoft.IdentityModel.Clients.ActiveDirectory.AdalServiceException : AADSTS70002 : erreur de validation des informations d’identification. AADSTS50054 : l’ancien mot de passe est utilisé pour l’authentification.

## Étapes suivantes
En savoir plus sur l’[intégration de vos identités locales avec Azure Active Directory](active-directory-aadconnect.md).

<!---HONumber=AcomDC_0629_2016-->