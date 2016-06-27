<properties
	pageTitle="Azure AD Connect Sync : comment gérer le compte de service Azure AD | Microsoft Azure"
	description="Cette rubrique explique comment restaurer le compte de service Azure AD."
	services="active-directory"
    keywords="Comment réinitialiser le mot de passe du compte du service Connecteur de synchronisation Azure AD Connect"
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
	ms.date="06/10/2016"
	ms.author="andkjell"/>

# Azure AD Connect Sync : comment gérer le compte de service Azure AD
Le compte de service utilisé par le connecteur Azure AD est censé être proposé en libre-service. Mais si vous devez réinitialiser les informations d’identification, cette rubrique vous concerne. Cela peut se produire par exemple si un administrateur général a réinitialisé par erreur le mot de passe du compte de service à l’aide de PowerShell.

## Réinitialisation des informations d'identification
Si le compte de service défini sur le Connecteur Azure AD ne peut pas contacter Azure AD en raison de problèmes d’authentification, le mot de passe peut être réinitialisé.

1. Connectez-vous au serveur de synchronisation Azure AD Connect et démarrez PowerShell.
2. Exécutez `Add-ADSyncAADServiceAccount`. ![PowerShell cmdlet addadsyncaadserviceaccount](./media/active-directory-aadconnectsync-howto-azureadaccount/addadsyncaadserviceaccount.png)
3. Fournissez les informations d’identification de l’administrateur général Azure AD.

Cette applet de commande réinitialisera le mot de passe du compte de service et l’actualisera dans Azure AD et dans le moteur de synchronisation.

## Étapes suivantes
En savoir plus sur l’[intégration de vos identités locales avec Azure Active Directory](active-directory-aadconnect.md).

<!---HONumber=AcomDC_0615_2016-->