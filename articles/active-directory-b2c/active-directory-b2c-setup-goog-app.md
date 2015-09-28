<properties
	pageTitle="Azure Active Directory B2C en version préliminaire : configuration Google+ | Microsoft Azure"
	description="Fourniture d’inscription et de connexion à des consommateurs disposant de comptes Google+ dans vos applications sécurisées par Azure Active Directory B2C"
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

# Azure Active Directory B2C en version préliminaire : fourniture d’inscription et de connexion à des consommateurs disposant de comptes Google+

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## Création d’une application Google +

Pour utiliser Google+ en tant que fournisseur d’identité dans Azure Active Directory (AD) B2C, vous devez commencer par créer une application Google+ et lui fournir les paramètres appropriés. Pour ce faire, vous avez besoin d’un compte Google+. Si vous n’en avez pas, vous pouvez en obtenir un via le site [https://accounts.google.com/SignUp](https://accounts.google.com/SignUp).

1. Accédez à la [Console développeur de Google](https://console.developers.google.com/) et connectez-vous avec les informations d’identification de votre compte Google+.
2. Cliquez sur **Create Project**, entrez une valeur pour **Project name**, acceptez les conditions du service, puis cliquez sur **Create**.

    ![G+ - prise en main](./media/active-directory-b2c-setup-goog-app/google-get-started.png)

    ![G+ - nouveau projet](./media/active-directory-b2c-setup-goog-app/google-new-project.png)

3. Cliquez sur **APIs & Auth**, puis sur **Credentials** dans le volet de navigation de gauche.
4. Cliquez sur l’onglet **OAuth consent screen** en haut.

    ![G+ - informations d’identification](./media/active-directory-b2c-setup-goog-app/google-add-cred.png)

5. Sélectionnez ou spécifiez une valeur valide pour **Email address**, fournissez une valeur pour **Product name**, puis cliquez sur **Save**.

    ![G + - écran de consentement OAuth](./media/active-directory-b2c-setup-goog-app/google-consent-screen.png)

6. Sous **Application type**, sélectionnez **Web application**.
7. Fournissez une valeur **Name** pour votre application, entrez [https://login.microsoftonline.com](https://login.microsoftonline.com) dans le champ **Authorized redirect URIs** et [https://login.microsoftonline.com/te/{directory}/ oauth2/authresp](https://login.microsoftonline.com/te/{directory}/oauth2/authresp) dans le champ **Authorized redirect URIs**, où **{directory}** est remplacé par le nom de votre annuaire (par exemple, contosob2c.onmicrosoft.com). Cliquez sur **Create**.

    > [AZURE.NOTE]La valeur **{directory}** respecte la casse.

    ![G+ - création d’identifiant client](./media/active-directory-b2c-setup-goog-app/google-create-client-id.png)

8. Copier les valeurs de **ID Client** et **Client secret**. Vous aurez besoin de ces deux valeurs pour configurer Google+ en tant que fournisseur d’identité dans votre annuaire.

    > [AZURE.NOTE]La valeur **Client secret** est une information d’identification de sécurité importante.

    ![G+ - clé secrète client](./media/active-directory-b2c-setup-goog-app/google-client-secret.png)

## Configuration de Google+ en tant que fournisseur d’identité dans votre annuaire

1. [Accédez au panneau de fonctionnalités B2C sur le portail Azure en version préliminaire](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade).
2. Dans le panneau de fonctionnalités B2C, cliquez sur **Fournisseurs d’identité sociaux**.
3. Cliquez sur **+Ajouter** dans la partie supérieure du panneau.
4. Fournissez un **Nom** convivial pour la configuration de fournisseur d’identité. Par exemple, entrez « G+ ».
5. Cliquez sur **Type de fournisseur d’identité**, sélectionnez **Google**, puis cliquez sur **OK**.
6. Cliquez sur **Configurer ce fournisseur d’identité**, puis entrez l’**ID client** et la **Clé secrète client** de l’application Google+ que vous avez créée précédemment.
7. Cliquez sur **OK**, puis sur **Créer** pour enregistrer votre configuration Google+.

<!---HONumber=Sept15_HO3-->