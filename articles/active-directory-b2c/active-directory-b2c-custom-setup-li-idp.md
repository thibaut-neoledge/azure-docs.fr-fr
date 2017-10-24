---
title: "Azure Active Directory B2C : Ajout de LinkedIn en tant que fournisseur d’identités OAuth2 à l’aide de stratégies personnalisées"
description: "Article sur la procédure à suivre pour configurer l’application LinkedIn à l’aide du protocole OAuth2 et de stratégies personnalisées"
services: active-directory-b2c
documentationcenter: 
author: yoelhor
manager: joroja
editor: 
ms.assetid: 
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 10/03/2017
ms.author: yoelh
ms.openlocfilehash: ac4c0212ffc13b24b6035756f1210d62b7b840c8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-b2c-add-linkedin-as-an-identity-provider-using-custom-policies"></a>Azure Active Directory B2C : Ajout de LinkedIn en tant que fournisseur d’identités à l’aide de stratégies personnalisées
[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Cet article indique comment activer la connexion pour les utilisateurs de comptes LinkedIn, à l’aide de [stratégies personnalisées](active-directory-b2c-overview-custom.md).

## <a name="prerequisites"></a>Composants requis
Suivez les étapes décrites dans [Bien démarrer avec les stratégies personnalisées](active-directory-b2c-get-started-custom.md).

Ces étapes sont les suivantes :
1.  créer une application de compte LinkedIn ;
2.  ajouter la clé d’application de compte LinkedIn à Azure AD B2C ;
3.  ajouter le fournisseur de revendications à une stratégie ;
4.  inscrire le fournisseur de revendications de compte LinkedIn à un parcours utilisateur ;
5.  charger la stratégie sur un locataire Azure AD B2C et la tester.

## <a name="step-1-create-a-linkedin-account-application"></a>Étape 1 : Créer une application de compte LinkedIn
Pour utiliser LinkedIn en tant que fournisseur d’identité dans Azure Active Directory (Azure AD) B2C, vous devez créer une application LinkedIn et lui fournir les paramètres appropriés. Vous pouvez inscrire une application LinkedIn ici : [https://LinkedIn.com/signup](https://LinkedIn.com/signup)

1.  Accédez au site web de [gestion des applications LinkedIn](https://www.linkedin.com/secure/developer?newapp=), connectez-vous avec vos informations d’identification de compte LinkedIn, puis cliquez sur **Créer une application**.

    ![Compte LinkedIn : création d’une application](media/active-directory-b2c-custom-setup-li-idp/adb2c-ief-setup-li-idp-new-app1.png)

2.  1.  Saisissez une valeur dans **Nom de la société**, puis dans les champs **Nom** descriptif et **Description** de votre nouvelle application.
    2.  Charger le **logo de l’application**
    3.  Sélectionner l’option **d’utilisation des applications**
    4.  Collez l’élément `https://login.microsoftonline.com` en tant que valeur du champ **URL du site web**.
    5.  Remplissez les champs **Adresse e-mail professionnelle** et **Téléphone professionnel**
    6.  Au bas de la page, lisez et acceptez les termes du contrat. Cliquez ensuite sur **Envoyer**

    ![Compte LinkedIn : configuration des propriétés de l’application](media/active-directory-b2c-custom-setup-li-idp/adb2c-ief-setup-li-idp-new-app2.png)

3.  Dans le menu, cliquez sur **Authentification**. Notez les valeurs des champs **ID client** et **Clé secrète client**.

    Pour les **URL de redirection autorisées**, collez la `https://login.microsoftonline.com/te/{tenant}.onmicrosoft.com/oauth2/authresp`. Remplacez {tenant} par le nom du locataire (par exemple, contosob2c.onmicrosoft.com). Vérifiez que vous utilisez le schéma HTTPS et cliquez sur **Ajouter**

    ![Compte LinkedIn : définition des URL de redirection autorisées](media/active-directory-b2c-custom-setup-li-idp/adb2c-ief-setup-li-idp-new-app3.png)

    > [!NOTE]
    >
    >La clé secrète client est une information d’identification de sécurité importante. Ne partagez pas cette clé secrète avec quiconque et ne la distribuez pas avec votre application.

4.  Cliquez sur l’onglet **Paramètres**, définissez le champ **État de l’application** sur **En direct**, puis cliquez sur **Mettre à jour**.

    ![Compte LinkedIn : définir l’état de l’application](media/active-directory-b2c-custom-setup-li-idp/adb2c-ief-setup-li-idp-new-app4.png)

## <a name="step-2-add-your-linkedin-application-key-to-azure-ad-b2c"></a>Étape 2 : Ajouter la clé d’application LinkedIn à Azure AD B2C
La fédération avec les comptes ADFS requiert une clé secrète client pour le compte LinkedIn, afin de faire confiance à Azure AD B2C au nom de l’application. Vous devez stocker la clé secrète d’application LinkedIn dans votre locataire Azure AD B2C :  

1.  Accédez à votre locataire Azure AD B2C, puis sélectionnez **Paramètres Azure AD B2C** > **Infrastructure d’expérience d’identité**
2.  Sélectionnez **Clés de stratégie** pour afficher les clés disponibles dans votre client.
3.  Cliquez sur **+Ajouter**.
4.  Pour **Options**, utilisez **Manuel**.
5.  Pour **Nom**, utilisez `LinkedInSecret`.  
    Il est possible que le préfixe `B2C_1A_` soit ajouté automatiquement.
6.  Dans la zone **Secret**, saisissez la clé secrète de votre application LinkedIn, provenant du site https://apps.dev.microsoft.com.
7.  Pour **Utilisation de la clé**, utilisez **Chiffrement**.
8.  Cliquez sur **Créer** 
9.  Vérifiez que vous avez créé la clé `B2C_1A_LinkedInSecret`.

## <a name="step-3-add-a-claims-provider-in-your-extension-policy"></a>Étape 3 : Ajouter un fournisseur de revendications à votre stratégie d’extension
Si vous souhaitez que les utilisateurs se connectent à l’aide d’un compte LinkedIn, vous devez définir LinkedIn en tant que fournisseur de revendications. En d’autres termes, vous devez spécifier des points de terminaison avec lesquels Azure AD B2C communique. Les points de terminaison fournissent un ensemble de revendications utilisées par Azure AD B2C pour vérifier qu’un utilisateur spécifique s’est authentifié.

Définissez LinkedIn en tant que fournisseur de revendications, en ajoutant le nœud `<ClaimsProvider>` dans votre fichier de stratégie d’extension :

1.  Ouvrez le fichier de stratégie d’extension (TrustFrameworkExtensions.xml) à partir de votre répertoire de travail. 
2.  Recherchez la section `<ClaimsProviders>`
3.  Ajoutez l’extrait de code XML suivant sous le nœud `<ClaimsProviders>` :  

  ```xml
  <ClaimsProvider>
    <Domain>linkedin.com</Domain>
    <DisplayName>LinkedIn</DisplayName>
    <TechnicalProfiles>
      <TechnicalProfile Id="LinkedIn-OAUTH">
        <DisplayName>LinkedIn</DisplayName>
        <Protocol Name="OAuth2" />
        <Metadata>
          <Item Key="ProviderName">linkedin</Item>
          <Item Key="authorization_endpoint">https://www.linkedin.com/oauth/v2/authorization</Item>
          <Item Key="AccessTokenEndpoint">https://www.linkedin.com/oauth/v2/accessToken</Item>
          <Item Key="ClaimsEndpoint">https://api.linkedin.com/v1/people/~:(id,first-name,last-name,email-address,headline)</Item>
          <Item Key="ClaimsEndpointAccessTokenName">oauth2_access_token</Item>
          <Item Key="ClaimsEndpointFormatName">format</Item>
          <Item Key="ClaimsEndpointFormat">json</Item>
          <Item Key="scope">r_emailaddress r_basicprofile</Item>
          <Item Key="HttpBinding">POST</Item>
          <Item Key="UsePolicyInRedirectUri">0</Item>
          <Item Key="client_id">Your LinkedIn application client ID</Item>
        </Metadata>
        <CryptographicKeys>
          <Key Id="client_secret" StorageReferenceId="B2C_1A_LinkedInSecret" />
        </CryptographicKeys>
        <OutputClaims>
          <OutputClaim ClaimTypeReferenceId="socialIdpUserId" PartnerClaimType="id" />
          <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="firstName" />
          <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="lastName" />
          <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="emailAddress" />
          <!--<OutputClaim ClaimTypeReferenceId="jobTitle" PartnerClaimType="headline" />-->
          <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="linkedin.com" />
          <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
        </OutputClaims>
        <OutputClaimsTransformations>
          <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName" />
          <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName" />
          <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId" />
          <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId" />
        </OutputClaimsTransformations>
        <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin" />
      </TechnicalProfile>
    </TechnicalProfiles>
  </ClaimsProvider>
  ```

4.  Remplacez la valeur `client_id` par votre ID client d’application LinkedIn.
5.  Enregistrez le fichier .

## <a name="step-4-register-the-linkedin-account-claims-provider-to-sign-up-or-sign-in-user-journey"></a>Étape 4 : Inscrire le fournisseur de revendications de compte LinkedIn pour s’inscrire ou se connecter à un parcours utilisateur
À ce stade, le fournisseur d’identité a été configuré. Toutefois, il n’est disponible dans aucun des écrans d’inscription/de connexion. Maintenant, vous devez ajouter le fournisseur d’identité du compte LinkedIn au parcours `SignUpOrSignIn` de votre utilisateur.

### <a name="step-41-make-a-copy-of-the-user-journey"></a>Étape 4.1 : Effectuer une copie du parcours utilisateur
Pour le rendre disponible, vous dupliquez un modèle de parcours utilisateur existant. Vous ajoutez ensuite le fournisseur d’identité LinkedIn :

> [!NOTE]
>
>Si vous avez copié l’élément `<UserJourneys>` à partir du fichier de base de votre stratégie vers le fichier d’extension (TrustFrameworkExtensions.xml), vous pouvez ignorer cette section.

1.  Ouvrez le fichier de base de votre stratégie (par exemple, TrustFrameworkBase.xml).
2.  Recherchez l’élément `<UserJourneys>` et copiez la totalité du contenu du nœud `<UserJourneys>`.
3.  Ouvrez le fichier d’extension (par exemple, TrustFrameworkExtensions.xml), puis recherchez l’élément `<UserJourneys>`. Si l’élément n’existe pas, ajoutez-en un.
4.  Collez l’intégralité du contenu du nœud `<UserJournesy>` que vous avez copié en tant qu’enfant de l’élément `<UserJourneys>`.

### <a name="step-42-display-the-button"></a>Étape 4.2 : Afficher le « bouton »
L’élément `<ClaimsProviderSelections>` définit la liste des options de sélection du fournisseur de revendications et leur ordre.  L’élément `<ClaimsProviderSelection>` est analogue à un bouton de fournisseur d’identité sur une page d’inscription/de connexion. Si vous ajoutez un élément `<ClaimsProviderSelection>` au compte LinkedIn, un nouveau bouton apparaît lorsqu’un utilisateur accède à la page. Pour ajouter cet élément :

1.  Recherchez le nœud `<UserJourney>` incluant `Id="SignUpOrSignIn"` dans le parcours utilisateur que vous avez copié.
2.  Localisez le nœud `<OrchestrationStep>` qui inclut `Order="1"`.
3.  Ajoutez l’extrait de code XML suivant sous le nœud `<ClaimsProviderSelections>` :
```xml
<ClaimsProviderSelection TargetClaimsExchangeId="LinkedInExchange" />
```

### <a name="step-43-link-the-button-to-an-action"></a>Étape 4.3 : Lier le bouton à une action
Maintenant que vous avez un bouton en place, vous devez le lier à une action. Dans ce cas, l’action effectuée correspond à la communication entre Azure AD B2C et le compte LinkedIn pour recevoir un jeton. Liez le bouton à une action en reliant le profil technique de votre fournisseur de revendications de compte LinkedIn :

1.  Recherchez l’élément `<OrchestrationStep>` qui inclut `Order="2"` dans le nœud `<UserJourney>`.
2.  Ajoutez l’extrait de code XML suivant sous le nœud `<ClaimsExchanges>` :

    ```xml
    <ClaimsExchange Id="LinkedInExchange" TechnicalProfileReferenceId="LinkedIn-OAuth" />
    ```

    > [!NOTE]
    >
    > * Vérifiez que `Id` a la même valeur que `TargetClaimsExchangeId` dans la section précédente.
    > * Vérifiez que l’ID `TechnicalProfileReferenceId` est défini sur le profil technique que vous avez créé plus haut (LinkedIn-OAuth).

## <a name="step-5-upload-the-policy-to-your-tenant"></a>Étape 5 : Charger la stratégie sur votre locataire
1.  Dans le [portail Azure](https://portal.azure.com), passez au [contexte de votre locataire Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md) et cliquez sur **Azure AD B2C**.
2.  Sélectionnez **Infrastructure d’expérience d’identité**.
3.  Cliquez sur **Toutes les stratégies**.
4.  Sélectionnez **Charger la stratégie**.
5.  Cochez la case **Remplacer la stratégie si elle existe**.
6.  **Téléchargez** TrustFrameworkExtensions.xml et vérifiez que sa validation n’échoue pas

## <a name="step-6-test-the-custom-policy-by-using-run-now"></a>Étape 6 : Tester la stratégie personnalisée en utilisant la commande Exécuter maintenant
1.  Ouvrez **Paramètres Azure AD B2C** et accédez à **Infrastructure d’expérience d’identité**.

    > [!NOTE]
    >
    >**Exécuter maintenant** nécessite la préinscription d’au moins une application sur le locataire. Pour découvrir comment inscrire des applications, consultez les articles sur Azure AD B2C [Bien démarrer](active-directory-b2c-get-started.md) et [Inscription des applications](active-directory-b2c-app-registration.md).

2.  Ouvrez **B2C_1A_signup_signin**, la stratégie personnalisée de partie de confiance que vous avez chargée. Sélectionnez **Exécuter maintenant**.
3.  Vous devriez être en mesure de vous connecter à l’aide de votre compte LinkedIn.

## <a name="step-7-optional-register-the-linkedin-account-claims-provider-to-profile-edit-user-journey"></a>Étape 7 : [Facultatif] Inscrire le fournisseur de revendications du compte LinkedIn à un parcours utilisateur Modification de profil
Vous pouvez également ajouter le fournisseur d’identité du compte LinkedIn au parcours utilisateur `ProfileEdit` de votre utilisateur. Pour le rendre disponible, répétez l’étape 4. Cette fois-ci, sélectionnez le nœud `<UserJourney>` qui inclut `Id="ProfileEdit"`. Enregistrez, chargez et testez votre stratégie.

## <a name="download-the-complete-policy-files"></a>Télécharger les fichiers de stratégie complets
Facultatif : nous vous recommandons de créer votre scénario avec vos propres fichiers de stratégie personnalisée après avoir suivi la procédure pas à pas Bien démarrer avec les stratégies personnalisées, au lieu d’utiliser ces exemples de fichiers.  [Exemples de fichiers de stratégie de référence](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-setup-li-app)