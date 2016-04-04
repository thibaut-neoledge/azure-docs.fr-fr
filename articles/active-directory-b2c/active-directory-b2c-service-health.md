<properties
	pageTitle="Version préliminaire d'Azure Active Directory B2C : Intégrité du service | Microsoft Azure"
	description="Notifications sur les mesures d’atténuation, l’état et les problèmes mineurs d’Azure Active Directory B2C"
	services="active-directory-b2c"
	documentationCenter=""
	authors="swkrish"
	manager="msmbaldwin"
	editor="bryanla"/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/22/2016"
	ms.author="swkrish"/>

# Version préliminaire d'Azure Active Directory B2C : Intégrité du service

Cette page fournit des notifications sur les mesures d’atténuation, l'état et les problèmes mineurs de service. Vous pouvez marquer cette page à l’aide d’un signet afin de vous y référer ultérieurement. Continuez à surveiller le [Tableau de bord d’état Azure](https://azure.microsoft.com/status/) également.

### 22/03/2016 : bogue de l'authentification unique (SSO) sur les clients B2C

Un bogue de l'authentification unique (SSO) a été repéré à 13 h PST le 17/03/2016 et atténué à 10 h PST le 18/03/2016. Dans l’intervalle, moins de 100 consommateurs ont été affectés. Nous surveillons la situation de près. Conditions pour qu’un consommateur ait subi ce bogue :

1. Vous avez établi une stratégie d'authentification en configurant les « comptes locaux » comme unique fournisseur d'identité.
2. Un consommateur se connecte à Azure AD B2C avec succès la première fois.
3. Le consommateur essaie de se reconnecter mais, en lieu et place de l'authentification unique, un message d'erreur s’affiche.

La seule solution pour le consommateur (après l'étape 3) a été de fermer et de rouvrir le navigateur et de se réauthentifier.

<!---HONumber=AcomDC_0323_2016-->