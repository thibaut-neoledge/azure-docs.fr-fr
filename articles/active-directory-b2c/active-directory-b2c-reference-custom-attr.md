<properties
	pageTitle="Azure Active Directory B2C en version préliminaire : attributs personnalisés | Microsoft Azure"
	description="Utilisation d’attributs personnalisés dans Azure Active Directory B2C pour collecter des informations sur vos consommateurs"
	services="active-directory-b2c"
	documentationCenter=""
	authors="swkrish"
	manager="msmbaldwin"
	editor="curtand"/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/15/2015"
	ms.author="swkrish"/>

#  Azure Active Directory B2C en version préliminaire : utilisation d’attributs personnalisés pour recueillir des informations sur vos clients

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

Votre annuaire Azure Active Directory (AD) B2C est fourni avec un ensemble intégré d’attributs tels que le prénom, le nom, la ville, le code postal, etc. Cependant, toute application accessible aux consommateurs a des exigences uniques en relation avec les informations (attributs) à recueillir auprès des consommateurs. Azure AD B2C vous permet d’étendre votre annuaire (plus précisément, l’ensemble d’attributs stockés sur chaque compte de consommateur). Vous pouvez créer des attributs personnalisés sur le [portail Azure en version préliminaire](https://portal.azure.com/) et utiliser celui-ci dans vos stratégies d’abonnement, comme illustré ci-dessous. Vous pouvez également lire et écrire ces attributs à l’aide de l’API Microsoft Azure AD Graph, comme illustré [ici](active-directory-b2c-devquickstarts-graph-dotnet.md).

> [AZURE.NOTE]Les attributs personnalisés utilisent les [Extensions de schéma d’annuaire de l’API Azure AD Graph](https://msdn.microsoft.com/library/azure/dn720459.aspx) de façon sous-jacente.

## Création d’un attribut personnalisé

1. [Accédez au panneau de fonctionnalités B2C sur le portail Azure en version préliminaire](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade).
2. Cliquez sur **Attributs utilisateur**.
3. Cliquez sur **+Ajouter** dans la partie supérieure du panneau.
4. Fournissez un **Nom** pour l’attribut personnalisé (par exemple, « ShoeSize ») et, éventuellement, une **Description**. Cliquez sur **Create**. Vous avez terminé !

    > [AZURE.NOTE]Actuellement, seul le **Type de données** « String » est disponible. À l’avenir, nous ajouterons des types de données supplémentaires (DateTime, Integer, etc.).

L’attribut personnalisé est actuellement disponible dans la liste des **attributs utilisateur**, et pour une utilisation dans vos stratégies d’inscription.

## Utilisation d’un attribut personnalisé dans votre stratégie d’inscription

1. Accédez au panneau de fonctionnalités B2C sur le [portail Azure en version préliminaire](htts://portal.azure.com/). Lisez [ici](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade) la procédure à suivre.
2. Cliquez sur **Stratégies d’inscription**.
3. Ouvrez votre stratégie d’inscription (par exemple, « B2C\_1\_SiUp ») en cliquant dessus. Cliquez sur **Modifier** dans la partie supérieure du panneau.
4. Cliquez sur **Attributs d’inscription**, puis sélectionnez l’attribut personnalisé (par exemple, « ShoeSize »). Cliquez sur **OK**.
5. Cliquez sur **revendications d’applications**, puis sélectionnez l’attribut personnalisé. Cliquez sur **OK**. 
6. Cliquez sur **Enregistrer** dans la partie supérieure du panneau. Vous avez terminé !

La fonctionnalité « Exécuter maintenant » de la stratégie permet de vérifier l’expérience utilisateur. Vous devez maintenant voir « ShoeSize » dans la liste d’attributs collectés lors de l’inscription du consommateur, et le voir dans le jeton retourné à votre application.

<!---HONumber=Sept15_HO3-->