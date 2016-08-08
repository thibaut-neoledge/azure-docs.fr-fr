<properties
	pageTitle="Azure Active Directory B2C : configuration Google+ | Microsoft Azure"
	description="Fourniture d’inscription et de connexion à des consommateurs disposant de comptes Google+ dans vos applications sécurisées par Azure Active Directory B2C."
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
	ms.date="07/24/2016"
	ms.author="swkrish"/>

# Azure Active Directory B2C : fourniture d’inscription et de connexion à des consommateurs disposant de comptes Google+

## Création d’une application Google+

Pour utiliser Google+ en tant que fournisseur d’identité dans Azure Active Directory (Azure AD) B2C, vous devez créer une application Google+ et lui fournir les paramètres appropriés. Pour ce faire, vous avez besoin d’un compte Google+. Si vous n’en avez pas, vous pouvez en obtenir un à l’adresse [https://accounts.google.com/SignUp](https://accounts.google.com/SignUp).

1. Accédez à la [Console développeur de Google](https://console.developers.google.com/) et connectez-vous avec les informations d’identification de votre compte Google+.
2. Cliquez sur **Créer un projet**, saisissez un **nom de projet**, puis cliquez sur **Créer**.

    ![Google+ - Prise en main](./media/active-directory-b2c-setup-goog-app/google-get-started.png)

    ![Google+ - Nouveau projet](./media/active-directory-b2c-setup-goog-app/google-new-project.png)

3. Cliquez sur **API Manager**, puis sur **Credentials** dans le volet de navigation de gauche.
4. Cliquez sur l’onglet **OAuth consent screen** en haut.

    ![Google+ - Informations d’identification](./media/active-directory-b2c-setup-goog-app/google-add-cred.png)

5. Sélectionnez ou spécifiez une valeur valide pour **Email address**, fournissez une valeur pour **Product name**, puis cliquez sur **Save**.

    ![Google+ - Écran de consentement OAuth](./media/active-directory-b2c-setup-goog-app/google-consent-screen.png)

6. Cliquez sur **New credentials**, puis sur **OAuth client ID**.

    ![Google+ - Écran de consentement OAuth](./media/active-directory-b2c-setup-goog-app/google-add-oauth2-client-id.png)

7. Sous **Application type**, sélectionnez **Web application**.

    ![Google+ - Écran de consentement OAuth](./media/active-directory-b2c-setup-goog-app/google-web-app.png)

8. Fournissez un **nom** pour votre application, entrez `https://login.microsoftonline.com` dans le champ **Authorized JavaScript origins**, et `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp` dans le champ **Authorized redirect URIs**. Remplacez **{tenant}** par votre nom de client (par exemple, contosob2c.onmicrosoft.com). La valeur **{tenant}** respecte la casse. Cliquez sur **Create**.

    ![Google+ - Créer un identifiant client](./media/active-directory-b2c-setup-goog-app/google-create-client-id.png)

9. Copiez les valeurs de **ID Client** et **Client secret**. Vous aurez besoin de ces deux valeurs pour configurer Google+ en tant que fournisseur d'identité dans votre client. La valeur **Client secret** est une information d’identification de sécurité importante.

    ![Google+ - Clé secrète client](./media/active-directory-b2c-setup-goog-app/google-client-secret.png)

## Configuration de Google+ en tant que fournisseur d'identité dans votre client

1. Suivez ces étapes pour [accéder au panneau de fonctionnalités B2C](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade) sur le portail Azure.
2. Dans le panneau de fonctionnalités B2C, cliquez sur **Fournisseurs d’identité**.
3. Cliquez sur **+Ajouter** dans la partie supérieure du panneau.
4. Fournissez un **Nom** convivial pour la configuration de fournisseur d’identité. Par exemple, entrez « G+ ».
5. Cliquez sur **Type de fournisseur d’identité**, sélectionnez **Google**, puis cliquez sur **OK**.
6. Cliquez sur **Configurer ce fournisseur d’identité**, puis entrez l’ID client et la clé secrète client de l’application Google+ que vous avez créée précédemment.
7. Cliquez sur **OK**, puis sur **Créer** pour enregistrer votre configuration Google+.

<!---HONumber=AcomDC_0727_2016-->