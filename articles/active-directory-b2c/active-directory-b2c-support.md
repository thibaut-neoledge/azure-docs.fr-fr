<properties
	pageTitle="Azure Active Directory B2C en version préliminaire : Support | Microsoft Azure"
	description="Dépôt de demandes de support pour Azure Active Directory B2C"
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

# Azure Active Directory B2C en version préliminaire : dépôt de demandes de prise en charge de fichiers pour Azure Active Directory B2C

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

Pour déposer des demandes d’assistance pour Azure Active Directory (AD) B2C sur le portail Azure, procédez comme suit :

1. [Suivez ces étapes pour accéder au panneau de fonctionnalités B2C sur le portail Azure](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade).
2. Basculez de votre client B2C vers un autre client auquel un abonnement Azure est associé. En règle générale, ce dernier est votre client des employés ou le client par défaut créé pour vous lorsque vous avez souscrit un abonnement Azure. Pour en savoir plus sur la relation entre les abonnements Azure et les annuaires Azure AD, lire [cet article](active-directory-how-subscriptions-associated-directory.md#how-an-azure-subscription-is-related-to-azure-ad).

    > [AZURE.IMPORTANT]
    Cette étape est obligatoire. Si vous ne l’exécutez pas, le processus échoue.

    ![Support - changement d’annuaires](./media/active-directory-b2c-support/support-switch-dir.png)

3. Après un changement d’annuaire, cliquez sur **Aide + support**.

    ![Support - support](./media/active-directory-b2c-support/support-support.png)

4. Pour déposer une demande de support pour Azure AD B2C, procédez de la manière décrite dans [cet article](http://blogs.msdn.com/b/mast/archive/2013/10/24/windows-azure-technical-support-for-msdn-technet-or-mpn-users-and-partners.aspx). Utilisez ces détails pour effectuer les étapes :

    - Le **Type de demande** est **technique**.
	- La **Ressource** est **Active Directory**.
	- Dans le panneau **Catégorie de problème**, sélectionnez **B2C en version préliminaire** comme **Type de problème** et la **Catégorie** appropriée.
	- Dans le panneau **Description**, décrivez votre problème de façon détaillée. Dans la zone de texte **Ressource**, entrez le nom de votre client B2C, par exemple, contosob2c.onmicrosoft.com.

5. Après avoir soumis votre demande d’assistance, vous pouvez la surveiller en cliquant sur **Aide + support** dans le tableau d’accueil, puis sur **Gérer les demandes de support**.

## Problème connu : dépôt d'une demande de support dans le contexte d'un client B2C

Si vous avez manqué l'étape 2 décrite ci-dessus et essayez de créer une demande de support dans le contexte de votre client B2C, l'erreur suivante s'affiche.

> [AZURE.IMPORTANT]
> N'essayez pas de souscrire un nouvel abonnement Azure dans votre client B2C.

![Support - aucun abonnement](./media/active-directory-b2c-support/support-no-sub.png)

<!---HONumber=AcomDC_0107_2016-->