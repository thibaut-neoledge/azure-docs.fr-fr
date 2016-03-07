<properties
	pageTitle="Azure Active Directory B2C en version préliminaire : Multi-Factor Authentication | Microsoft Azure"
	description="Activation de Multi-Factor Authentication dans des applications accessibles aux consommateurs sécurisées par Azure Active Directory B2C"
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
	ms.date="01/04/2016"
	ms.author="swkrish"/>

# Azure Active Directory B2C en version préliminaire : activation d’Azure Multi-Factor Authentication dans vos applications accessibles aux consommateurs

Azure Active Directory (Azure AD) B2C s’intègre directement à [Azure Multi-Factor Authentication](../multi-factor-authentication/multi-factor-authentication.md) pour vous permettre d’ajouter une deuxième couche de sécurité aux expériences d’inscription et de connexion dans vos applications accessibles aux consommateurs. Vous pouvez faire cela sans écrire une seule ligne de code. Actuellement, nous prenons en charge un appel téléphonique et un message texte pour la vérification. Si vous déjà créé des stratégies d’inscription et de connexion, vous pouvez toujours activer Multi-Factor Authentication.

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

> [AZURE.NOTE]
Vous pouvez également activer Multi-Factor Authentication lors de la création de stratégies d’inscription et de connexion, pas seulement lors de la modification de stratégies existantes.

Cette fonctionnalité permettent aux applications de gérer des scénarios tels que le suivant :

- Vous n’avez pas besoin de Multi-Factor Authentication pour accéder à une application, mais en avez besoin pour accéder à une autre. Par exemple, le consommateur peut se connecter à une application d’assurance automobile avec un compte local ou social, mais il doit confirmer un numéro de téléphone avant d’accéder à l’application d’assurance domestique inscrite dans le même annuaire.
- Vous n’avez pas besoin de Multi-Factor Authentication pour accéder à une application en général, mais en avez besoin pour accéder à des portions sensibles de celle-ci. Par exemple, le consommateur peut se connecter à une application bancaire avec un compte local ou social pour consulter le solde de son compte, mais il doit confirmer un numéro de téléphone avant de tenter d’effectuer un virement.

## Modification de votre stratégie d’inscription pour activer Multi-Factor Authentication

1. [Suivez ces étapes pour accéder au panneau de fonctionnalités B2C sur le portail Azure](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade).
2. Cliquez sur **Stratégies d'inscription**.
3. Ouvrez votre stratégie d’inscription (par exemple, « B2C\_1\_SiUp ») en cliquant dessus.
4. Cliquez sur **Multi-Factor Authentication**, puis **activez** **État**. Cliquez sur **OK**.
5. Cliquez sur **Enregistrer** dans la partie supérieure du panneau.

La fonctionnalité « Exécuter maintenant » de la stratégie permet de vérifier l’expérience utilisateur. Vérifiez les éléments suivants :

Un compte de consommateur est créé dans votre annuaire avant l’étape Multi-Factor Authentication. Lors de cette étape, le consommateur est invité à fournir son numéro de téléphone et à le confirmer. Si la vérification réussit, le numéro de téléphone est associé au compte du consommateur en vue d’une utilisation ultérieure. Même si le consommateur annule ou abandonne, il peut être invité à confirmer à nouveau un numéro de téléphone lors de la prochaine connexion (avec Multi-Factor Authentication activée).

## Modification de votre stratégie de connexion pour activer Multi-Factor Authentication

1. [Suivez ces étapes pour accéder au panneau de fonctionnalités B2C sur le portail Azure](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade).
2. Cliquez sur **Stratégies d’authentification**.
3. Ouvrez votre stratégie de connexion (par exemple, « B2C\_1\_SiIn ») en cliquant dessus. Cliquez sur **Modifier** dans la partie supérieure du panneau.
4. Cliquez sur **Multi-Factor Authentication**, puis **activez** **État**. Cliquez sur **OK**.
5. Cliquez sur **Enregistrer** dans la partie supérieure du panneau.

La fonctionnalité « Exécuter maintenant » de la stratégie permet de vérifier l’expérience utilisateur. Vérifiez les éléments suivants :

Lorsque le consommateur se connecte (à l’aide d’un compte local ou social), si un numéro de téléphone vérifié est associé à son compte, il est invité à la confirmer. Si aucun numéro de téléphone n’est associé, le consommateur est invité à le fournir et à le confirmer. Si la vérification réussit, le numéro de téléphone est associé au compte du consommateur en vue d’une utilisation ultérieure.

<!---HONumber=AcomDC_0224_2016-->