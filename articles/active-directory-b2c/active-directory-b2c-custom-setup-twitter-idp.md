---
title: "Azure Active Directory B2C : ajoutez Twitter en tant que fournisseur d’identité OAuth1 avec des stratégies personnalisées"
description: "Exemple d’utilisation de Twitter en tant que fournisseur d’identité avec le protocole OAuth1"
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
ms.date: 10/02/2017
ms.author: yoelh
ms.openlocfilehash: 8d7bd2ed1cba62677a7eff19ba7018cd25a4345c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-b2c-add-twitter-as-an-oauth1-identity-provider-using-custom-policies"></a>Azure Active Directory B2C : ajoutez Twitter en tant que fournisseur d’identité OAuth1 avec des stratégies personnalisées
[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Cet article vous montre comment activer l’identification pour les utilisateurs à partir de comptes Twitter via l’utilisation de [stratégies personnalisées](active-directory-b2c-overview-custom.md).

## <a name="prerequisites"></a>Composants requis
Suivez les étapes décrites dans [Bien démarrer avec les stratégies personnalisées](active-directory-b2c-get-started-custom.md).

Ces étapes sont les suivantes :

1.  Création d’une application de compte Twitter.
2.  Ajout de la clé d’application de compte Twitter à Azure AD B2C
3.  ajouter le fournisseur de revendications à une stratégie ;
4.  Inscription du fournisseur de revendications de compte Twitter à un parcours utilisateur
5.  charger et tester la stratégie sur un client Azure AD B2C

## <a name="step-1-create-a-twitter-account-application"></a>Étape 1 : Créer une application de compte Twitter
Pour utiliser Twitter en tant que fournisseur d’identité dans Azure Active Directory (Azure AD) B2C, vous devez créer une application Twitter et lui fournir les paramètres appropriés. Vous pouvez inscrire une application Twitter ici : [https://twitter.com/signup](https://twitter.com/signup)

1.  Accédez au site web [Twitter Developers](https://apps.twitter.com/) , connectez-vous avec vos identifiants Twitter, puis cliquez sur **Create New App**.

    ![Compte Twitter - Créer une application](media/active-directory-b2c-custom-setup-twitter-idp/adb2c-ief-setup-twitter-idp-new-app1.png)

2.  Entrez le **Nom** et une **Description** pour votre nouvelle application. Collez `https://login.microsoftonline.com` comme valeur pour **Site web**. Ensuite, pour **URL de rappel**, collez `https://login.microsoftonline.com/te/{tenant}.onmicrosoft.com/oauth2/authresp`. Remplacez {tenant} par le nom de votre locataire (par exemple contosob2c.onmicrosoft.com). et vérifiez que vous utilisez le schéma HTTPS. Au bas de la page, lisez et acceptez les termes du contrat. Ensuite, cliquez sur **Create your Twitter application**.

    ![Compte Twitter - Ajouter une nouvelle application](media/active-directory-b2c-custom-setup-twitter-idp/adb2c-ief-setup-twitter-idp-new-app2.png)

3.  Cliquez sur l’onglet **Paramètres**, activez l’option **Autoriser la connexion à Twitter via cette application**, puis cliquez sur **Mettre à jour les paramètres**.
4.  Sélectionnez l’onglet **Keys and Access Tokens** . Prenez note des valeurs de **Clé de consommateur (clé API)** et de **Secret de consommateur (clé secrète API)**.

    ![Compte Twitter - Définir les propriétés de l’application](media/active-directory-b2c-custom-setup-twitter-idp/adb2c-ief-setup-twitter-idp-new-app3.png)

> [!NOTE]
>
>La clé secrète consommateur est une information d’identification de sécurité importante. Ne partagez pas cette clé secrète avec quiconque et ne la distribuez pas avec votre application.

## <a name="step-2-add-the-twitter-account-application-key-to-azure-ad-b2c"></a>Étape 2 : Ajouter la clé d’application de compte Twitter à Azure AD B2C
La fédération avec les comptes Twitter nécessite une clé secrète client pour que le compte Twitter accorde la confiance à Azure AD B2C au nom de l’application. Vous devez stocker la clé secrète de consommateur de l’application Twitter dans votre locataire Azure AD B2C :  

1.  Accédez à votre locataire Azure AD B2C, puis sélectionnez **Paramètres Azure AD B2C** > **Infrastructure d’expérience d’identité**
2.  Sélectionnez **Clés de stratégie** pour afficher les clés disponibles dans votre client.
3.  Cliquez sur **+Ajouter**.
4.  Pour **Options**, utilisez **Manuel**.
5.  Pour **Nom**, utilisez `TwitterSecret`.  
    Il est possible que le préfixe `B2C_1A_` soit ajouté automatiquement.
6.  Dans la zone **Secret**, entrez la clé secrète de votre application Microsoft, provenant de https://apps.dev.microsoft.com.
7.  Pour **Utilisation de la clé**, utilisez **Chiffrement**.
8.  Cliquez sur **Créer**
9.  Vérifiez que vous avez créé la clé `B2C_1A_TwitterSecret`.

## <a name="step-3-add-a-claims-provider-in-your-extension-policy"></a>Étape 3 : Ajouter un fournisseur de revendications à votre stratégie d’extension

Si vous voulez que les utilisateurs se connectent à l’aide d’un compte Twitter, vous devez définir Twitter comme fournisseur de revendications. En d’autres termes, vous devez spécifier des points de terminaison avec lesquels Azure AD B2C communique. Les points de terminaison fournissent un ensemble de revendications utilisées par Azure AD B2C pour vérifier qu’un utilisateur spécifique s’est authentifié.

Définissez Twitter comme fournisseur de revendications en ajoutant le nœud `<ClaimsProvider>` dans votre fichier de stratégie d’extension :

1.  Ouvrez le fichier de stratégie d’extension (TrustFrameworkExtensions.xml) à partir de votre répertoire de travail. 
2.  Recherchez la section `<ClaimsProviders>`
3.  Ajoutez l’extrait de code XML suivant sous le nœud `<ClaimsProviders>` :  

    ```xml
    <ClaimsProvider>
        <Domain>twitter.com</Domain>
        <DisplayName>Twitter</DisplayName>
        <TechnicalProfiles>
        <TechnicalProfile Id="Twitter-OAUTH1">
            <DisplayName>Twitter</DisplayName>
            <Protocol Name="OAuth1" />
            <Metadata>
            <Item Key="ProviderName">Twitter</Item>
            <Item Key="authorization_endpoint">https://api.twitter.com/oauth/authenticate</Item>
            <Item Key="access_token_endpoint">https://api.twitter.com/oauth/access_token</Item>
            <Item Key="request_token_endpoint">https://api.twitter.com/oauth/request_token</Item>
            <Item Key="ClaimsEndpoint">https://api.twitter.com/1.1/account/verify_credentials.json?include_email=true</Item>
            <Item Key="ClaimsResponseFormat">json</Item>
            <Item Key="client_id">Your Twitter application consumer key</Item>
            </Metadata>
            <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_TwitterSecret" />
            </CryptographicKeys>
            <InputClaims />
            <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="socialIdpUserId" PartnerClaimType="user_id" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="screen_name" />
            <OutputClaim ClaimTypeReferenceId="email" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="twitter.com" />
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

4.  Remplacez la valeur de `client_id` par la clé secrète de consommateur de votre application de compte Twitter.

5.  Enregistrez le fichier .

## <a name="step-4-register-the-twitter-account-claims-provider-to-sign-up-or-sign-in-user-journey"></a>Étape 4 : Inscrire le fournisseur de revendications de compte Twitter pour s’inscrire ou se connecter à un parcours utilisateur
À ce stade, le fournisseur d’identité a été configuré. Toutefois, il n’est disponible dans aucun des écrans d’inscription/de connexion. Vous devez maintenant ajouter le fournisseur d’identité du compte Twitter au parcours utilisateur `SignUpOrSignIn` de votre utilisateur.

### <a name="step-41-make-a-copy-of-the-user-journey"></a>Étape 4.1 : Effectuer une copie du parcours utilisateur
Pour le rendre disponible, vous dupliquez un modèle de parcours utilisateur existant. Vous ajoutez ensuite le fournisseur d’identité Twitter :

> [!NOTE]
>
>Si vous avez copié l’élément `<UserJourneys>` à partir du fichier de base de votre stratégie dans le fichier d’extension (TrustFrameworkExtensions.xml), vous pouvez ignorer cette section.

1.  Ouvrez le fichier de base de votre stratégie (par exemple, TrustFrameworkBase.xml).
2.  Recherchez l’élément `<UserJourneys>` et copiez la totalité du contenu du nœud `<UserJourneys>`.
3.  Ouvrez le fichier d’extension (par exemple, TrustFrameworkExtensions.xml), puis recherchez l’élément `<UserJourneys>`. Si l’élément n’existe pas, ajoutez-en un.
4.  Collez l’intégralité du contenu du nœud `<UserJournesy>` que vous avez copié en tant qu’enfant de l’élément `<UserJourneys>`.

### <a name="step-42-display-the-button"></a>Étape 4.2 : Afficher le « bouton »
L’élément `<ClaimsProviderSelections>` définit la liste des options de sélection du fournisseur de revendications et leur ordre.  L’élément `<ClaimsProviderSelection>` est analogue à un bouton de fournisseur d’identité sur une page d’inscription/de connexion. Si vous ajoutez un élément `<ClaimsProviderSelection>` pour le compte Twitter, un nouveau bouton apparaît quand un utilisateur accède à la page. Pour ajouter cet élément :

1.  Recherchez le nœud `<UserJourney>` incluant `Id="SignUpOrSignIn"` dans le parcours utilisateur que vous avez copié.
2.  Localisez le nœud `<OrchestrationStep>` qui inclut `Order="1"`.
3.  Ajoutez l’extrait de code XML suivant sous le nœud `<ClaimsProviderSelections>` :

    ```xml
    <ClaimsProviderSelection TargetClaimsExchangeId="TwitterExchange" />
    ```

### <a name="step-43-link-the-button-to-an-action"></a>Étape 4.3 : Lier le bouton à une action
Maintenant que vous avez un bouton en place, vous devez le lier à une action. L’action est dans ce cas la communication d’Azure AD B2C avec le compte Twitter pour recevoir un jeton. Liez le bouton à une action en liant le profil technique de votre fournisseur de revendications du compte Twitter :

1.  Recherchez l’élément `<OrchestrationStep>` qui inclut `Order="2"` dans le nœud `<UserJourney>`.
2.  Ajoutez l’extrait de code XML suivant sous le nœud `<ClaimsExchanges>` :

    ```xml
    <ClaimsExchange Id="TwitterExchange" TechnicalProfileReferenceId="Twitter-OAUTH1" />
    ```

    > [!NOTE]
    >
    > * Vérifiez que `Id` a la même valeur que `TargetClaimsExchangeId` dans la section précédente.
    > * Vérifiez que l’ID `TechnicalProfileReferenceId` est défini sur le profil technique que vous avez créé plus haut (Twitter-OAUTH1).

## <a name="step-5-upload-the-policy-to-your-tenant"></a>Étape 5 : Charger la stratégie sur votre locataire
1.  Dans le [portail Azure](https://portal.azure.com), passez au [contexte de votre locataire Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md) et cliquez sur **Azure AD B2C**.
2.  Sélectionnez **Infrastructure d’expérience d’identité**.
3.  Cliquez sur **Toutes les stratégies**.
4.  Sélectionnez **Charger la stratégie**
5.  Cochez la case **Remplacer la stratégie si elle existe**.
6.  **Téléchargez** TrustFrameworkExtensions.xml et vérifiez que sa validation n’échoue pas

## <a name="step-6-test-the-custom-policy-by-using-run-now"></a>Étape 6 : Tester la stratégie personnalisée en utilisant Exécuter maintenant

1.  Ouvrez **Paramètres Azure AD B2C** et accédez à **Infrastructure d’expérience d’identité**.

    > [!NOTE]
    >
    >**Exécuter maintenant** nécessite la préinscription d’au moins une application sur le locataire. Pour découvrir comment inscrire des applications, consultez les articles sur Azure AD B2C [Bien démarrer](active-directory-b2c-get-started.md) et [Inscription des applications](active-directory-b2c-app-registration.md).

2.  Ouvrez **B2C_1A_signup_signin**, la stratégie personnalisée de partie de confiance que vous avez chargée. Sélectionnez **Exécuter maintenant**.
3.  Vous devez normalement pouvoir vous connecter à l’aide de votre compte Twitter.

## <a name="step-7-optional-register-the-twitter-account-claims-provider-to-profile-edit-user-journey"></a>Étape 7 : [Facultatif] Inscrire le fournisseur de revendications du compte Twitter à un parcours utilisateur Modification de profil
Vous pouvez également ajouter le fournisseur d’identité du compte Twitter au parcours utilisateur `ProfileEdit` de votre utilisateur. Pour le rendre disponible, vous répétez l’étape 4. Cette fois-ci, sélectionnez le nœud `<UserJourney>` qui inclut `Id="ProfileEdit"`. Enregistrez, chargez et testez votre stratégie.


## <a name="download-the-complete-policy-files"></a>Télécharger les fichiers de stratégie complets
Facultatif : nous vous recommandons de créer votre scénario avec vos propres fichiers de stratégie personnalisée après avoir suivi la procédure pas à pas Bien démarrer avec les stratégies personnalisées, au lieu d’utiliser ces exemples de fichiers.  [Exemples de fichiers de stratégie de référence](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-setup-twitter-app)