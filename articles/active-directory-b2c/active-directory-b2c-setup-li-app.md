<properties
	pageTitle="Azure Active Directory B2C en version préliminaire : configuration LinkedIn | Microsoft Azure"
	description="Fourniture d’inscription et de connexion à des consommateurs disposant de comptes LinkedIn dans vos applications sécurisées par Azure Active Directory B2C"
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
	ms.date="04/19/2016"
	ms.author="swkrish"/>

# Azure Active Directory B2C en version préliminaire : fourniture d’inscription et de connexion à des consommateurs disposant de comptes LinkedIn

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## Création d’une application LinkedIn

Pour utiliser LinkedIn en tant que fournisseur d’identité dans Azure Active Directory (Azure AD) B2C, vous devez créer une application LinkedIn et lui fournir les paramètres appropriés. Pour ce faire, vous avez besoin d’un compte LinkedIn. Si vous n’en avez pas, vous pouvez en obtenir un à l’adresse [https://www.linkedin.com/](https://www.linkedin.com/).

1. Accédez au site web [Développeurs LinkedIn](https://www.developer.linkedin.com/) et connectez-vous à l’aide des informations d’identification de votre compte LinkedIn.
2. Cliquez sur **My App** dans la barre de menu, puis sur **Create Application**.

    ![LinkedIn - nouvelle application](./media/active-directory-b2c-setup-li-app/linkedin-new-app.png)

3. Dans le formulaire **Create a New Application** écran, entrez les informations pertinentes (**Company Name**, **Name**, **Description**, **Application Logo URL**, **Application Use**, **Website URL**, **Business Email** et **Business Phone**).
4. Acceptez les **conditions d’utilisation de l’API LinkedIn**, puis cliquez sur **Submit**.

    ![LinkedIn - inscription d’application](./media/active-directory-b2c-setup-li-app/linkedin-register-app.png)

5. Copiez les valeurs de **Client ID** et **Client Secret**. (Vous les trouverez sous **Authentication Keys**.) Vous aurez besoin de ces deux valeurs pour configurer LinkedIn en tant que fournisseur d’identité dans votre client.

	>[AZURE.NOTE] La valeur **Client Secret** est une information d’identification de sécurité importante.

6. Entrez `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp` dans le champ **Authorized Redirect URLs** (sous **OAuth 2.0**). Remplacez **{tenant}** par votre nom de client (par exemple, contoso.onmicrosoft.com). Cliquez sur **Add**, puis sur **Update**. La valeur **{tenant}** respecte la casse.

    ![LinkedIn - configuration d’application](./media/active-directory-b2c-setup-li-app/linkedin-setup.png)

## Configuration de LinkedIn en tant que fournisseur d’identité dans votre client

1. Suivez ces étapes pour [accéder au panneau de fonctionnalités B2C](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade) sur le portail Azure.
2. Dans le panneau de fonctionnalités B2C, cliquez sur **Fournisseurs d’identité**.
3. Cliquez sur **+Ajouter** dans la partie supérieure du panneau.
4. Fournissez un **Nom** convivial pour la configuration de fournisseur d’identité. Par exemple, entrez « LI ».
5. Cliquez sur **Type de fournisseur d’identité**, sélectionnez **LinkedIn**, puis cliquez sur **OK**.
6. Cliquez sur **Configurer ce fournisseur d’identité**, puis entrez l’ID client et la clé secrète client de l’application LinkedIn que vous avez créée précédemment.
7. Cliquez sur **OK**, puis sur **Créer** pour enregistrer votre configuration LinkedIn.

<!---HONumber=AcomDC_0518_2016-->