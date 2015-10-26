<properties
	pageTitle="Azure Active Directory B2C en version préliminaire : configuration Facebook | Microsoft Azure"
	description="Fourniture d’inscription et de connexion à des consommateurs disposant de comptes Facebook dans vos applications sécurisées par Azure Active Directory B2C"
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
	ms.date="10/08/2015"
	ms.author="swkrish"/>

# Azure Active Directory B2C en version préliminaire : fourniture d’inscription et de connexion à des consommateurs disposant de comptes Facebook

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## Création d’une application Facebook

Pour utiliser Facebook en tant que fournisseur d’identité dans Azure Active Directory (AD) B2C, vous devez commencer par créer une application Facebook et lui fournir les paramètres appropriés. Pour ce faire, vous avez besoin d’un compte Facebook. Si vous n’en avez pas, vous pouvez en obtenir un via le site [http://www.facebook.com/](https://www.facebook.com/).

1. Accédez au site web [Développeurs Facebook](https://developers.facebook.com/) et connectez-vous à l’aide des informations d’identification de votre compte Facebook.
2. Si ce n’est déjà fait, cliquez sur **Apps**, puis sur **Register as a Developer**, acceptez la politique et suivez les étapes de l’inscription.
3. Cliquez sur **Applications**, puis **Add a new App**. Ensuite, choisissez **Website** comme plateforme, puis cliquez sur **Skip and Create App ID**.

    ![FB - ajout de nouvelle application](./media/active-directory-b2c-setup-fb-app/fb-add-new-app.png)

    ![FB - ajout de nouvelle application - site web](./media/active-directory-b2c-setup-fb-app/fb-add-new-app-website.png)

    ![FB - création d’ID d’application](./media/active-directory-b2c-setup-fb-app/fb-new-app-skip.png)

4. Dans le formulaire, entrez une valeur **Display Name**, choisissez l’option **Category** appropriée, puis cliquez sur **Create App ID**. Remarque : cela nécessite que vous acceptiez les politiques de la plateforme Facebook et effectuiez une vérification de sécurité en ligne.

    ![FB - création d’ID d’application](./media/active-directory-b2c-setup-fb-app/fb-create-app-id.png)

5. Cliquez sur **Settings** dans la barre de navigation à gauche. Entrez une valeur **Contact Email** valide.
6. Cliquez sur **+Add Platform**, puis sélectionnez **Website**.

    ![FB - paramètres](./media/active-directory-b2c-setup-fb-app/fb-settings.png)

    ![FB - paramètres](./media/active-directory-b2c-setup-fb-app/fb-website.png)

7. Entrez [https://login.microsoftonline.com/](https://login.microsoftonline.com/) dans le champ **Site URL**, puis cliquez sur **Save Changes**.
8. Copiez la valeur **ID de l’application**. Cliquez sur **Show**, puis copiez la valeur **App Secret**. Vous aurez besoin de ces deux valeurs pour configurer Facebook en tant que fournisseur d’identité dans votre annuaire.

    > [AZURE.NOTE]La valeur **App Secret** est une information d’identification de sécurité importante.

    ![FB - URL de site](./media/active-directory-b2c-setup-fb-app/fb-site-url.png)

9. Cliquez sur l’onglet **Advanced** en haut, puis entrez [https://login.microsoftonline.com/te/{directory}/oauth2/authresp](https://login.microsoftonline.com/te/{directory}/oauth2/authresp) dans le champ **Valid OAuth redirect URIs** (dans la section **Security**), où **{directory}** est remplacé par le nom de votre annuaire (par exemple, contosob2c.onmicrosoft.com). Cliquez sur **Save Changes** en bas de la page.

    ![FB - URI de redirection OAuth](./media/active-directory-b2c-setup-fb-app/fb-oauth-redirect-uri.png)

10. Pour rendre votre application Facebook utilisable par Azure AD B2C, vous devez la rendre disponible publiquement. Vous pouvez le faire en cliquant sur **Status & Review** dans le volet de navigation gauche et en positionnant le commutateur en haut de la page sur **OUI**. Cliquez ensuite sur **Confirmer**.

    ![FB - URI de redirection OAuth](./media/active-directory-b2c-setup-fb-app/fb-app-public.png)

## Configuration de Facebook en tant que fournisseur d’identité dans votre annuaire

1. [Accédez au panneau de fonctionnalités B2C sur le portail Azure en version préliminaire](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade).
2. Dans le panneau de fonctionnalités B2C, cliquez sur **Fournisseurs d’identité**.
3. Cliquez sur **+Ajouter** dans la partie supérieure du panneau.
4. Fournissez un **Nom** convivial pour la configuration de fournisseur d’identité. Par exemple, entrez « FB ».
5. Cliquez sur **Type de fournisseur d’identité**, sélectionnez **Facebook**, puis cliquez sur **OK**.
6. Cliquez sur **Configurer ce fournisseur d’identité**, puis entrez l’**ID de l’application** et la **Question secrète de l’application** Facebook que vous avez créée précédemment, respectivement dans les champs **ID client** et **Clé secrète client**.
7. Cliquez sur **OK**, puis sur **Créer** pour enregistrer votre configuration Facebook.

<!---HONumber=Oct15_HO3-->