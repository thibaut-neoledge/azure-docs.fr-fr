---
title: 'Azure Active Directory B2C : configuration Facebook | Microsoft Docs'
description: "Fourniture d’inscription et de connexion à des consommateurs disposant de comptes Facebook dans vos applications sécurisées par Azure Active Directory B2C."
services: active-directory-b2c
documentationcenter: 
author: swkrish
manager: mbaldwin
editor: bryanla
ms.assetid: b875f235-a1d2-4abb-b9f0-b89beac38a32
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/06/2016
ms.author: swkrish
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 329e22f307a1db5b8ab957caaa7222e452e0b008


---
# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-facebook-accounts"></a>Azure Active Directory B2C : fourniture d’inscription et de connexion à des consommateurs disposant de comptes Facebook
## <a name="create-a-facebook-application"></a>Création d’une application Facebook
Pour utiliser Facebook en tant que fournisseur d’identité dans Azure Active Directory (Azure AD) B2C, vous devez créer une application Facebook et lui fournir les paramètres appropriés. Pour ce faire, vous avez besoin d’un compte Facebook. Si vous n’en avez pas, vous pouvez en obtenir un à l’adresse [https://www.facebook.com/](https://www.facebook.com/).

1. Accédez au site web [Facebook pour les développeurs](https://developers.facebook.com/) et connectez-vous à l’aide des informations d’identification de votre compte Facebook.
2. Si ce n’est pas déjà fait, vous devez vous inscrire en tant que développeur Facebook. Pour cela, cliquez sur **Register** (dans le coin supérieur droit de la page), acceptez les politiques de Facebook et suivez les étapes de l’inscription.
3. Cliquez sur **Mes applications**, puis sur **Ajouter une nouvelle application**. Choisissez **Site Web** comme plateforme, puis cliquez sur **Ignorer et créer un ID d’application**.
   
    ![Facebook - ajout d’une nouvelle application](./media/active-directory-b2c-setup-fb-app/fb-add-new-app.png)
   
    ![Facebook - ajout d’une nouvelle application - site Web](./media/active-directory-b2c-setup-fb-app/fb-add-new-app-website.png)
   
    ![Facebook - création d’un ID d’application](./media/active-directory-b2c-setup-fb-app/fb-new-app-skip.png)
4. Dans le formulaire, entrez une valeur **Nom d’affichage** et une **adresse e-mail** valide, choisissez l’option **Catégorie** appropriée, puis cliquez sur **Créer un ID d’application**. Cela nécessite que vous acceptiez les politiques de la plateforme Facebook et effectuiez une vérification de sécurité en ligne.
   
    ![Facebook - création d’un nouvel ID d’application](./media/active-directory-b2c-setup-fb-app/fb-create-app-id.png)
5. Cliquez sur **Settings** dans la barre de navigation à gauche.
6. Cliquez sur **+ Ajouter une plateforme**, puis sélectionnez **Site Web**.
   
    ![Facebook - paramètres](./media/active-directory-b2c-setup-fb-app/fb-settings.png)
   
    ![Facebook - paramètres - site Web](./media/active-directory-b2c-setup-fb-app/fb-website.png)
7. Entrez [https://login.microsoftonline.com/](https://login.microsoftonline.com/) dans le champ **URL du site**, puis cliquez sur **Enregistrer les modifications**.
   
    ![Facebook - URL du site](./media/active-directory-b2c-setup-fb-app/fb-site-url.png)
8. Copiez la valeur **ID de l’application**. Cliquez sur **Afficher**, puis copiez la valeur **Clé secrète de l’application**. Vous aurez besoin de ces deux valeurs pour configurer Facebook en tant que fournisseur d’identité dans votre client. **App Secret** est une information d’identification de sécurité importante.
   
    ![Facebook - ID et clé secrète de l’application](./media/active-directory-b2c-setup-fb-app/fb-app-id-app-secret.png)
9. Cliquez sur **+ Ajouter un produit** dans la barre de navigation gauche, puis que le bouton **Prise en main** à côté de **Connexion Facebook**.
   
    ![Facebook - Connexion Facebook](./media/active-directory-b2c-setup-fb-app/fb-login.png)
10. Entrez `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp` dans le champ **URI de redirection OAuth valides** dans la section **Paramètres du client OAuth**. Remplacez **{tenant}** par votre nom de client (par exemple, contosob2c.onmicrosoft.com). Cliquez sur **Save Changes** en bas de la page.
    
    ![Facebook - OAuth Redirect URI](./media/active-directory-b2c-setup-fb-app/fb-oauth-redirect-uri.png)
11. Pour rendre votre application Facebook utilisable par Azure AD B2C, vous devez la rendre disponible publiquement. Vous pouvez le faire en cliquant sur **Révision de l’application** dans le volet de navigation gauche, en positionnant le commutateur en haut de la page sur **OUI** et en cliquant sur **Confirmer**.
    
    ![Facebook - App publique](./media/active-directory-b2c-setup-fb-app/fb-app-public.png)

## <a name="configure-facebook-as-an-identity-provider-in-your-tenant"></a>Configuration de Facebook en tant que fournisseur d’identité dans votre client
1. Suivez ces étapes pour [accéder au panneau de fonctionnalités B2C](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade) sur le portail Azure.
2. Dans le panneau de fonctionnalités B2C, cliquez sur **Fournisseurs d’identité**.
3. Cliquez sur **+Ajouter** dans la partie supérieure du panneau.
4. Fournissez un **Nom** convivial pour la configuration de fournisseur d’identité. Par exemple, entrez « FB ».
5. Cliquez sur **Type de fournisseur d’identité**, sélectionnez **Facebook**, puis cliquez sur **OK**.
6. Cliquez sur **Configurer ce fournisseur d’identité**, puis entrez l’ID de l’application et la question secrète (de l’application Facebook que vous avez créée précédemment) respectivement dans les champs **ID client** et **Clé secrète client**.
7. Cliquez sur **OK**, puis sur **Créer** pour enregistrer votre configuration Facebook.




<!--HONumber=Nov16_HO3-->


