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
	ms.date="05/16/2016"
	ms.author="swkrish"/>

# Version préliminaire d’Azure Active Directory B2C : dépôt de demandes de support

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

Pour déposer des demandes d’assistance pour Azure Active Directory (Azure AD) B2C sur le portail Azure, procédez comme suit :

1. [Suivez ces étapes pour accéder au panneau de fonctionnalités B2C sur le portail Azure](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade).
2. Basculez de votre client B2C vers un autre client auquel un abonnement Azure est associé. En règle générale, ce dernier est le client de vos employés ou le client par défaut créé pour vous lorsque vous avez souscrit un abonnement Azure. Pour en savoir plus, consultez [Association d’un abonnement Azure à Azure AD](active-directory-how-subscriptions-associated-directory.md#how-an-azure-subscription-is-related-to-azure-ad).

    ![Support - Basculer entre les clients](./media/active-directory-b2c-support/support-switch-dir.png)

3. Après avoir basculé entre les clients, cliquez sur **Aide + Support**.

    ![Support - Aide + Support](./media/active-directory-b2c-support/support-support.png)

4. Cliquez sur **Nouvelle demande de support**.

    ![Support - Nouveau](./media/active-directory-b2c-support/support-new.png)

5. Dans le panneau **Fonctions de base**, renseignez ces informations, puis cliquez sur **Suivant**.

    - Le **Type de problème** est **Technique**.
	- Sélectionnez l’**Abonnement** approprié.
    - Le **Service** est **Active Directory**.
    - Sélectionnez le **Plan de support** approprié. Si vous n’en avez pas, vous pouvez en adopter un [ici](https://azure.microsoft.com/support/plans/).

    ![Support - Fonctions de base](./media/active-directory-b2c-support/support-basics.png)

6. Dans le panneau **Problème**, renseignez ces détails, puis cliquez sur **Suivant**.

    - Sélectionnez le niveau de **Gravité** approprié.
    - Le **Type de problème** est **Aperçu B2C**.
    - Sélectionnez la **Catégorie** appropriée.
	- Décrivez votre problème dans le champ **Détails**. Fournissez des détails comme le nom du client B2C, la description du problème, les messages d’erreur, les ID de corrélation (si disponibles), etc.
    - Dans le champ **Délai d’exécution**, renseignez la date et l’heure (avec le fuseau horaire) de survenue du problème.
    - Sous **Téléchargement de fichiers**, chargez l’ensemble des captures et des fichiers nécessaires à la résolution du problème.

    ![Support - Problème](./media/active-directory-b2c-support/support-problem.png)

7. Dans le panneau **Informations de contact**, renseignez vos coordonnées. Cliquez sur **Create**. *Remarque : durant l’aperçu, le support associé à Azure AD B2C est disponible en anglais uniquement.*

    ![Support - Contact](./media/active-directory-b2c-support/support-contact.png)

8. Après avoir soumis votre demande d’assistance, vous pouvez la surveiller en cliquant sur **Aide + support** dans le tableau d’accueil, puis sur **Gérer les demandes de support**.

## Problème connu : dépôt d'une demande de support dans le contexte d'un client B2C

Si vous avez manqué l'étape 2 décrite ci-dessus et essayez de créer une demande de support dans le contexte de votre client B2C, l'erreur suivante s'affiche.

> [AZURE.IMPORTANT]
N'essayez pas de souscrire un nouvel abonnement Azure dans votre client B2C.

![Support - aucun abonnement](./media/active-directory-b2c-support/support-no-sub.png)

<!---HONumber=AcomDC_0518_2016-->