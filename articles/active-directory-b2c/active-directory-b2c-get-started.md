<properties
	pageTitle="Azure Active Directory B2C en version préliminaire : création d’un annuaire Azure Active Directory B2C | Microsoft Azure"
	description="Rubrique sur la création d’un annuaire Azure Active Directory B2C"
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

# Azure Active Directory B2C en version préliminaire : création d’un annuaire Azure AD B2C

Pour commencer à utiliser Azure Active Directory (AD) B2C, suivez les 3 étapes décrites ci-dessous.

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## Étape 1 : souscrire un abonnement Azure

Si vous disposez déjà d’un abonnement Azure, passez à l’étape suivante. Dans le cas contraire, souscrivez [un abonnement Azure](sign-up-organization.md) et accédez à Azure AD B2C.

> [AZURE.NOTE]Azure AD B2C en version préliminaire est actuellement disponible gratuitement pour une utilisation limitée (jusqu’à 50 000 utilisateurs par annuaire). Un abonnement Azure est nécessaire pour accéder ai [portail Azure](http://manage.windowsazure.com/).

## Étape 2 : créer un annuaire Azure AD B2C

Procédez comme suit pour créer un annuaire Azure AD B2C. Actuellement, les fonctionnalités B2C ne peuvent pas être activées dans vos annuaires existants éventuels.

1. Connectez-vous au [portail Azure](https://manage.windowsazure.com/) en tant qu’administrateur d’abonnements. Il s’agit du compte professionnel ou scolaire, ou du compte Microsoft que vous avez utilisé pour vous inscrire à Azure.
2. Cliquez sur **Nouveau** > **Services d’application** > **Active Directory** > **Annuaire** > **Création personnalisée**.

    ![Créer un annuaire](./media/active-directory-b2c-get-started/new-directory.png)

3. Choisissez un **nom**, un **nom de domaine** et un **pays ou région** pour votre annuaire.
4. Activez l’option **Ceci est un répertoire B2C**.
5. Cliquez sur la coche pour achever la configuration.

    ![Créer un annuaire B2C](./media/active-directory-b2c-get-started/create-b2c-directory.png)

6. Votre annuaire est désormais créé et apparaît dans l’extension Active Directory. Vous êtes par ailleurs administrateur général de l’annuaire. Vous pouvez ajouter d’autres administrateurs généraux en fonction des besoins.

    > [AZURE.IMPORTANT]La création de votre annuaire peut prendre jusqu’à deux minutes . Si vous rencontrez des problèmes lors de la création d’annuaire, voir cet [article](active-directory-b2c-support-create-directory.md) pour obtenir des instructions.

## Étape 3 : accéder au panneau de fonctionnalités B2C sur le portail Azure en version préliminaire

1. Accédez à l’extension Active Directory dans la barre de navigation sur le côté gauche.
2. Recherchez votre annuaire sous l’onglet **Annuaire**, puis cliquez dessus.
3. Cliquez sur l’onglet **Configurer**.
4. Cliquez sur le lien **Gestion des paramètres B2C** dans la section **Administration B2C**.

    ![Créer un annuaire B2C](./media/active-directory-b2c-get-started/b2c-directory-configure-tab.png)

4. Le [portail Azure en version préliminaire](https://portal.azure.com/) avec le panneau de fonctionnalités B2C s’ouvre dans un nouvel onglet ou une nouvelle fenêtre de navigateur.
5. Épinglez ce panneau (voir l’angle supérieur droit) sur votre Tableau d’accueil pour en faciliter l’accès.

    ![Panneau de fonctionnalités B2C](./media/active-directory-b2c-get-started/b2c-features-blade.png)

    > [AZURE.NOTE]Vous pouvez gérer les utilisateurs et groupes, la configuration de la réinitialisation du mot de passe libre-service et les fonctionnalités de promotion professionnelle de votre annuaire via le [portail Azure](https://manage.windowsazure.com/).

## Étapes suivantes

Passez à l’[inscription d’une application auprès d’Azure AD B2C et à la création d’une application de démarrage rapide](active-directory-b2c-app-registration.md).

<!---HONumber=Sept15_HO3-->