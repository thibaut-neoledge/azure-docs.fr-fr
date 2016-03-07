<properties
	pageTitle="Azure Active Directory B2C en version préliminaire : création d’un client Azure Active Directory B2C | Microsoft Azure"
	description="Rubrique sur la création d’un client Azure Active Directory B2C"
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
	ms.date="01/28/2016"
	ms.author="swkrish"/>

# Azure Active Directory B2C en version préliminaire : création d’un client Azure AD B2C

Pour commencer à utiliser Microsoft Azure Active Directory (Azure AD) B2C, suivez les trois étapes décrites dans cet article.

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## Étape 1 : souscrire un abonnement Azure

Vous pouvez ignorer cette étape si vous possédez déjà un abonnement Azure. Dans le cas contraire, souscrivez [un abonnement Azure](../active-directory/sign-up-organization.md) et accédez à Azure AD B2C.

> [AZURE.NOTE]
Actuellement, vous pouvez utiliser Azure Active Directory B2C en version préliminaire gratuitement, mais son utilisation est limitée à 50 000 utilisateurs par client. Un abonnement Azure est nécessaire pour accéder au [portail Azure Classic](http://manage.windowsazure.com/).

## Étape 2 : créer un client Azure AD B2C

Procédez comme suit pour créer un client Azure AD B2C. Actuellement, les fonctionnalités B2C ne peuvent pas être activées dans vos annuaires existants éventuels.

1. Connectez-vous au [portail Azure Classic](https://manage.windowsazure.com/) en tant qu’administrateur d’abonnements. Il s’agit du compte professionnel ou scolaire, ou du compte Microsoft que vous avez utilisé pour vous inscrire à Azure.
2. Cliquez sur **Nouveau** > **Services d’application** > **Active Directory** > **Annuaire** > **Création personnalisée**.

    ![Capture d’écran du début de la création d’un client](./media/active-directory-b2c-get-started/new-directory.png)

3. Choisissez un **nom**, un **nom de domaine** et un **pays ou une région** pour votre client.
4. Activez l’option **Ceci est un répertoire B2C**.
5. Cliquez sur la coche pour achever la configuration.

    ![Capture d’écran de la case à cocher pour créer un répertoire B2C](./media/active-directory-b2c-get-started/create-b2c-directory.png)

6. Votre client est désormais créé et apparaît dans l’extension Active Directory. Vous êtes par ailleurs administrateur général du client. Vous pouvez ajouter d’autres administrateurs généraux en fonction des besoins.

    > [AZURE.IMPORTANT]
    La création de votre client peut prendre jusqu’à deux minutes . Si vous rencontrez des problèmes lors de la création du client, consultez [Création d’un client Azure AD ou d’un client Azure AD B2C : problèmes et résolutions](active-directory-b2c-support-create-directory.md) pour obtenir des instructions.

## Étape 3 : accéder au panneau de fonctionnalités B2C sur le portail Azure

1. Accédez à l’extension Active Directory dans la barre de navigation sur le côté gauche.
2. Recherchez votre client sous l’onglet **Annuaire**, puis cliquez dessus.
3. Cliquez sur l’onglet **Configurer**.
4. Cliquez sur le lien **Gestion des paramètres B2C** dans la section **Administration B2C**.

    ![Capture d’écran de configuration de répertoire B2C](./media/active-directory-b2c-get-started/b2c-directory-configure-tab.png)

5. Le portail Azure avec le panneau de fonctionnalités B2C s’ouvre dans un nouvel onglet ou une nouvelle fenêtre de navigateur.

    > [AZURE.IMPORTANT]
    Il existe un problème connu dans lequel cette page ne se charge pas correctement (cela concerne un petit nombre de clients). En principe, l’actualisation du navigateur corrige le problème. Dans le cas contraire, contactez le support Azure.

6. Épinglez ce panneau sur votre Tableau d’accueil pour en faciliter l’accès. (L’outil Épingler est marqué en rouge dans le coin supérieur droit du panneau de fonctionnalités).

    ![Capture d’écran du panneau de fonctionnalités B2C](./media/active-directory-b2c-get-started/b2c-features-blade.png)

    > [AZURE.NOTE]
    Vous pouvez gérer les utilisateurs et groupes, la configuration de la réinitialisation du mot de passe libre-service et les fonctionnalités de promotion professionnelle de votre client via le [portail Azure Classic](https://manage.windowsazure.com/).

## Étapes suivantes

Découvrez comment inscrire une application avec Azure Active Directory B2C et créer une application de démarrage rapide en lisant [Azure Active Directory entreprise-client (B2C) en version préliminaire : inscription de votre application](active-directory-b2c-app-registration.md).

<!---HONumber=AcomDC_0224_2016-->