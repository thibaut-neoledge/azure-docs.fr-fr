<properties
	pageTitle="Azure Active Directory B2C : configuration Amazon | Microsoft Azure"
	description="Fourniture d’inscription et de connexion à des consommateurs disposant de comptes Amazon dans vos applications sécurisées par Azure Active Directory B2C."
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

# Azure Active Directory B2C : fourniture d’inscription et de connexion à des consommateurs disposant de comptes Amazon

## Création d’une application Amazon

Pour utiliser Amazon en tant que fournisseur d’identité dans Azure Active Directory (Azure AD) B2C, vous devez créer une application Amazon et lui fournir les paramètres appropriés. Pour ce faire, vous avez besoin d’un compte Amazon. Si vous n’en avez pas, vous pouvez en obtenir un à l’adresse [http://www.amazon.com/](http://www.amazon.com/).

1. Accédez au [centre des développeurs Amazon](https://login.amazon.com/) et connectez-vous avec les informations d’identification de votre compte Amazon.
2. Si ce n’est déjà fait, cliquez sur **Sign Up**, suivez la procédure d’inscription pour développeur et acceptez la politique d’utilisation.
3. Cliquez sur **Register new application**.

    ![Inscription d’une nouvelle application sur le site Web d’Amazon](./media/active-directory-b2c-setup-amzn-app/amzn-new-app.png)

4. Fournissez les informations relatives à l’application (**Name**, **Description** et **Privacy Notice URL**), puis cliquez sur **Save**.

    ![Fourniture d’informations d’application pour l’inscription d’une nouvelle application sur Amazon](./media/active-directory-b2c-setup-amzn-app/amzn-register-app.png)

5. Dans la section **Web Settings**, copiez les valeurs **Client ID** et **Client Secret**. (Vous devez au préalable cliquer sur le bouton **Show Secret**.) Vous avez besoin de ces deux valeurs pour configurer Amazon en tant que fournisseur d’identité dans votre client. Cliquez sur **Edit** au bas de la section. La valeur **Client Secret** est une information d’identification de sécurité importante.

	![Fourniture de l’ID client et de la clé secrète client pour votre nouvelle application sur Amazon](./media/active-directory-b2c-setup-amzn-app/amzn-client-secret.png)

6. Entrez `https://login.microsoftonline.com` dans le champ **Allowed JavaScript Origins** et `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp` dans le champ **Allowed Return URLs**. Remplacez **{tenant}** par votre nom de client (par exemple, contoso.onmicrosoft.com). Cliquez sur **Save**. La valeur **{tenant}** respecte la casse.

    ![Fourniture de JavaScript Origins et Return URLs pour votre nouvelle application sur Amazon](./media/active-directory-b2c-setup-amzn-app/amzn-urls.png)

## Configuration d’Amazon en tant que fournisseur d’identité dans votre client

1. Suivez ces étapes pour [accéder au panneau de fonctionnalités B2C](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade) sur le portail Azure.
2. Dans le panneau de fonctionnalités B2C, cliquez sur **Fournisseurs d’identité**.
3. Cliquez sur **+Ajouter** dans la partie supérieure du panneau.
4. Fournissez un **Nom** convivial pour la configuration de fournisseur d’identité. Par exemple, entrez « Amzn ».
5. Cliquez sur **Type de fournisseur d’identité**, sélectionnez **Amazon**, puis cliquez sur **OK**.
6. Cliquez sur **Configurer ce fournisseur d’identité**, puis entrez l’ID client et la clé secrète client de l’application Amazon que vous avez créée précédemment.
7. Cliquez sur **OK**, puis sur **Créer** pour enregistrer votre configuration Amazon.

<!---HONumber=AcomDC_0727_2016-->