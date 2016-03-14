<properties
	pageTitle="Azure Active Directory B2C en version préliminaire : configuration de compte Microsoft | Microsoft Azure"
	description="Fourniture d’inscription et de connexion à des consommateurs disposant de comptes Microsoft dans vos applications sécurisées par Azure Active Directory B2C."
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
	ms.date="01/12/2016"
	ms.author="swkrish"/>

# Azure Active Directory B2C en version préliminaire : fourniture d’inscription et de connexion à des consommateurs disposant de comptes Microsoft

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## Créer une application de compte Microsoft

Pour utiliser un compte Microsoft en tant que fournisseur d’identité dans Azure Active Directory (Azure AD) B2C, vous devez créer une application de compte Microsoft et lui fournir les paramètres appropriés. Pour ce faire, vous avez besoin d’un compte Microsoft. Si vous n’en avez pas, vous pouvez en obtenir un à l’adresse [https://www.live.com/](https://www.live.com/).

1. Accédez au [Centre de développement de compte Microsoft](https://account.live.com/developers/applications) et connectez-vous avec votre compte Microsoft.
2. Cliquez sur **Créer une application**.

    ![Compte Microsoft - Ajouter une nouvelle application](./media/active-directory-b2c-setup-msa-app/msa-add-new-app.png)

3. Saisissez un **Nom d’application** puis cliquez sur **J’accepte**. Cela exige que vous acceptiez les conditions d’utilisation des services Microsoft.

    ![Compte Microsoft - Nom d’application](./media/active-directory-b2c-setup-msa-app/msa-app-name.png)

4. Cliquez sur **Paramètres API** dans la barre de navigation à gauche. Entrez une valeur **Contact Email** valide.

    ![Compte Microsoft - Paramètres API](./media/active-directory-b2c-setup-msa-app/msa-api-settings.png)

5. Entrez `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp` dans le champ **URL de redirection**. Remplacez **{tenant}** par votre nom de client (par exemple, contosob2c.onmicrosoft.com). Cliquez sur **Enregistrer** au bas de la page.

    ![Compte Microsoft - URL de redirection](./media/active-directory-b2c-setup-msa-app/msa-redirect-url.png)

6. Cliquez sur **Paramètres des applications** dans la barre de navigation à gauche. Copiez les valeurs de **ID Client** et **Client secret**. Vous aurez besoin de ces deux valeurs pour configurer le compte Microsoft en tant que fournisseur d’identité dans votre client. La valeur **Client secret** est une information d’identification de sécurité importante.

    ![Compte Microsoft - Clé secrète client](./media/active-directory-b2c-setup-msa-app/msa-client-secret.png)

## Configuration du compte Microsoft en tant que fournisseur d’identité dans votre client

1. Suivez ces étapes pour [accéder au panneau de fonctionnalités B2C](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade) sur le portail Azure.
2. Dans le panneau de fonctionnalités B2C, cliquez sur **Fournisseurs d’identité**.
3. Cliquez sur **+Ajouter** dans la partie supérieure du panneau.
4. Fournissez un **Nom** convivial pour la configuration de fournisseur d’identité. Par exemple, saisissez « MSA ».
5. Cliquez sur **Type de fournisseur d’identité**, sélectionnez **Compte Microsoft**, puis cliquez sur **OK**.
6. Cliquez sur **Configurer ce fournisseur d’identité**, puis saisissez l’ID client et la clé secrète client de l’application de compte Microsoft que vous avez créée précédemment.
7. Cliquez sur **OK**, puis sur **Créer** pour enregistrer votre configuration de compte Microsoft.

<!---HONumber=AcomDC_0302_2016-->