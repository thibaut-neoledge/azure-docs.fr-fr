---
title: "Azure Active Directory B2C : Ajout du fournisseur Azure AD à l’aide de stratégies personnalisées | Microsoft Docs"
description: "Une rubrique sur les stratégies personnalisées Azure Active Directory B2C"
services: active-directory-b2c
documentationcenter: 
author: parakhj
manager: krassk
editor: parakhj
ms.assetid: 31f0dfe5-1ad0-4a25-a53b-8acc71bcea72
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 04/04/2017
ms.author: parakhj
ms.translationtype: Human Translation
ms.sourcegitcommit: 2db2ba16c06f49fd851581a1088df21f5a87a911
ms.openlocfilehash: f520b46e9d37ac31c2ef5d78ef9044e62dd25a6f
ms.contentlocale: fr-fr
ms.lasthandoff: 05/08/2017


---
# <a name="azure-active-directory-b2c-log-in-using-azure-ad-accounts"></a>Azure Active Directory B2C : Connexion à l’aide de comptes Azure AD

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Cet article vous montre comment activer la connexion pour les utilisateurs à partir d’une organisation Azure AD spécifique à l’aide de [stratégies personnalisées](active-directory-b2c-overview-custom.md).

## <a name="prerequisites"></a>Composants requis

Assurez-vous d’avoir effectué toutes les étapes qui vous montrent comment [prendre en main les stratégies personnalisées](active-directory-b2c-get-started-custom.md).

notamment :

1. Création d’un client Azure AD B2C.
1. Création d’une application Azure AD B2C.
1. Inscription de deux applications de moteur de stratégie.
1. Configuration des clés.
1. Configuration du pack de démarrage.

## <a name="create-an-azure-ad-app"></a>Création d’une application Azure AD

Pour activer la connexion pour les utilisateurs d’une organisation Azure AD spécifique, vous devez inscrire une application au sein du client Azure AD de l’organisation.

>[!NOTE]
> Nous appellerons le client Azure AD du client `contoso.com` et celui d’Azure AD B2C `fabrikamb2c.onmicrosoft.com`.

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Dans la barre supérieure, cliquez sur votre compte et, dans la liste **Répertoire**, choisissez le client Active Directory de l’organisation auprès duquel vous voulez inscrire votre application (par ex. contoso.com).
1. Cliquez sur **Autres services** dans le volet de navigation gauche et recherchez**Inscriptions d’applications**.
1. Choisissez **Inscription d’une nouvelle application**.
1. Tapez un **Nom** pour votre application (par exemple, application Azure AD B2C)
1. Sélectionnez **Application web/API** pour Type d’application.
1. Pour « URL de connexion », entrez l’URL ci-dessous, où `yourtenant` doit être remplacé par le nom de votre client Azure AD B2C (p. ex. fabrikamb2c.onmicrosoft.com).

    ```
    https://login.microsoftonline.com/te/yourtenant.onmicrosoft.com/oauth2/authresp
    ```

1. Enregistrez **l’ID de l’application**.
1. Cliquez sur l’application nouvellement créée.
1. Dans le panneau Paramètres, cliquez sur **Clés**.
1. Créez une nouvelle clé et enregistrez-la pour la section suivante.

## <a name="add-the-azure-ad-key-to-azure-ad-b2c"></a>Ajouter la clé d’Azure AD à Azure AD B2C

Vous devez stocker la clé d’application `contoso.com` dans votre client Azure AD B2C. Pour ce faire :

1. Accédez à votre client Azure AD B2C et ouvrez B2C Settings (Paramètres B2C)>Identity Experience Framework (Infrastructure d’expérience d’identité)> Clés de stratégie.
1. Cliquez sur +Ajouter.
1. Options :
 * Sélectionnez Option >`Manual`.
 * Nom : > `ContosoAppSecret`. Choisissez un nom qui correspond au nom de votre client Azure AD.  Le préfixe B2C_1A_ est ajouté automatiquement au nom de votre clé.
 * Collez votre clé d’application dans la zone de texte `Secret`.
 * Sélectionnez Signature.
1. Cliquez sur `Create`
1. Confirmez que vous avez créé la clé : `B2C_1A_ContosoAppSecret`.

    Lorsque vous exécutez la commande, vérifiez que vous vous connectez avec le compte d’administrateur onmicrosoft.com local au locataire Azure AD B2C. Si vous recevez une erreur indiquant que « TokenSigningKeyContainer » ou `B2C_1A_TokenSigningKeyContainer` est introuvable, parcourez le guide de [prise en main](active-directory-b2c-get-started-custom.md).


## <a name="add-a-claims-provider-in-your-base-policy"></a>Ajoutez un fournisseur de revendications dans votre stratégie de base

Afin de permettre aux utilisateurs de se connecter à l’aide d’Azure AD, vous devez définir Azure AD comme fournisseur de revendications. En d’autres termes, vous devez spécifier un point de terminaison avec lequel Azure AD B2C communiquera. Le point de terminaison *fournira* un ensemble de *revendications* utilisées par Azure AD B2C pour vérifier qu’un utilisateur spécifique s’est authentifié. Vous pouvez le faire en ajoutant Azure AD en tant que `<ClaimsProvider>` dans le fichier d’extension de votre stratégie.

1. Ouvrez le fichier d’extension à partir de votre répertoire de travail (TrustFrameworkExtensions.xml).
1. Recherchez la section `<ClaimsProviders>`. Si elle n’existe pas, ajoutez-la sous le nœud racine.
1. Ajoutez un nouveau `<ClaimsProvider>` comme suit :

    ```XML
    <ClaimsProvider>
        <Domain>Contoso</Domain>
        <DisplayName>Login using Contoso</DisplayName>
        <TechnicalProfiles>
        <TechnicalProfile Id="ContosoProfile">
            <DisplayName>Contoso Employee</DisplayName>
            <Description>Login with your Contoso account</Description>
            <Protocol Name="OpenIdConnect"/>
            <OutputTokenFormat>JWT</OutputTokenFormat>
            <Metadata>
                <Item Key="METADATA">https://login.windows.net/contoso.com/.well-known/openid-configuration</Item>
                <Item Key="ProviderName">https://sts.windows.net/00000000-0000-0000-0000-000000000000/</Item>
                <Item Key="client_id">00000000-0000-0000-0000-000000000000</Item>
                <Item Key="IdTokenAudience">00000000-0000-0000-0000-000000000000</Item>
                <Item Key="response_types">id_token</Item>
                <Item Key="UsePolicyInRedirectUri">false</Item>
            </Metadata>
            <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="ContosoAppSecret"/>
            </CryptographicKeys>
            <OutputClaims>
                <OutputClaim ClaimTypeReferenceId="socialIdpUserId" PartnerClaimType="oid"/>
                <OutputClaim ClaimTypeReferenceId="tenantId" PartnerClaimType="tid"/>
                <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
                <OutputClaim ClaimTypeReferenceId="surName" PartnerClaimType="family_name" />
                <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
                <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="contosoAuthentication" />
                <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="AzureADContoso" />
            </OutputClaims>
            <OutputClaimsTransformations>
                <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName"/>
                <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName"/>
                <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId"/>
                <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId"/>
            </OutputClaimsTransformations>
            <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop"/>
        </TechnicalProfile>
        </TechnicalProfiles>
    </ClaimsProvider>
    ```

1. Sous le nœud `<ClaimsProvider>`, mettez à jour la valeur de `<Domain>` sur une valeur unique qui peut être utilisée pour la distinguer des autres fournisseurs d’identité.
1. Sous le nœud `<ClaimsProvider>`, mettez à jour la valeur de `<DisplayName>` sur un nom convivial pour le fournisseur de revendications. Cette valeur n’est pas utilisée actuellement.

### <a name="update-the-technical-profile"></a>Mise à jour du profil technique

Pour obtenir un jeton à partir du point de terminaison Azure AD, vous devez définir les protocoles que Azure AD B2C doit utiliser pour communiquer avec Azure AD. Cela est effectué à l’intérieur de l’élément `<TechnicalProfile>` du `<ClaimsProvider>`.

1. Mettez à jour l’ID du nœud `<TechnicalProfile>`. Cet ID est utilisé pour faire référence à ce profil technique depuis les autres parties de la stratégie.
1. Mettez à jour la valeur pour `<DisplayName>`. Cette valeur s’affichera sur le bouton de connexion dans votre écran de connexion.
1. Mettez à jour la valeur pour `<Description>`.
1. Azure AD utilise le protocole OpenID Connect, assurez-vous donc que `<Protocol>` est « OpenIDConnect ».

Vous devez mettre à jour la section `<Metadata>` dans le XML ci-dessus pour refléter les paramètres de configuration de votre client Azure AD spécifique. Dans le XML, mettez à jour les valeurs de métadonnées comme suit :

1. Définissez `<Item Key="METADATA">` sur `https://login.windows.net/yourAzureADtenant/.well-known/openid-configuration`, où `yourAzureADtenant` est le nom de votre client Azure AD (par exemple, contoso.com).
1. Ouvrez votre navigateur et accédez à l’URL `Metadata` que vous venez de mettre à jour.
1. Dans le navigateur, recherchez l’objet « issuer » et copiez sa valeur. Vous devriez obtenir quelque chose semblable à ce qui suit : `https://sts.windows.net/{tenantId}/`.
1. Collez la valeur de `<Item Key="ProviderName">` dans le XML.
1. Définissez `<Item Key="client_id">` sur la valeur `Application ID` de l’inscription de l’application.
1. Définissez `<Item Key="IdTokenAudience">` sur la valeur `Application ID` de l’inscription de l’application.
1. Vérifiez que `<Item Key="response_types">` est défini sur `id_token`.
1. Vérifiez que `<Item Key="UsePolicyInRedirectUri">` est défini sur `false`.

Vous devez également lier le secret Azure AD que vous avez inscrit auprès de votre client Azure AD B2C au `<ClaimsProvider>` d’Azure AD.

* Dans la section `<CryptographicKeys>` dans le XML ci-dessus, mettez à jour la valeur pour `StorageReferenceId` sur l’ID de référence de la clé secrète que vous avez définie (par exemple, ContosoAppSecret).

### <a name="upload-the-extension-file-for-verification"></a>Télécharger le fichier d’extension pour la vérification

À ce stade, vous avez configuré votre stratégie afin qu’Azure AD B2C sache comment communiquer avec votre annuaire Azure AD. Essayez de télécharger le fichier d’extension de votre stratégie juste pour confirmer qu’il ne présente aucun problème pour le moment. Pour ce faire :

1. Accédez au panneau **Toutes les stratégies** dans votre client Azure AD B2C.
1. Cochez la case **Remplacer la stratégie si elle existe**.
1. Téléchargez le fichier d’extension (TrustFrameworkExtensions.xml) et assurez-vous qu’il n’échoue pas à la validation.

## <a name="register-the-azure-ad-claims-provider-to-a-user-journey"></a>Enregistrer le fournisseur de revendications AD Azure pour un parcours utilisateur

Vous devez maintenant ajouter le fournisseur d’identité Azure AD dans un de vos parcours utilisateur. À ce stade, le fournisseur d’identité a été défini, mais il n’est disponible dans aucun des écrans d’inscription/de connexion. Pour ce faire, nous créons un doublon d’un modèle de parcours utilisateur existant et le modifions afin qu’il dispose également du fournisseur d’identité Azure AD.

1. Ouvrez le fichier de base de votre stratégie (par exemple, TrustFrameworkBase.xml)
1. Recherchez l’élément `<UserJourneys>` et copiez l’intégralité de `<UserJourney>` avec ID = « SignUpOrSignIn ».
1. Ouvrez le fichier d’extension (par exemple, TrustFrameworkExtensions.xml) et recherchez l’élément `<UserJourneys>`. Si l’élément n’existe pas, ajoutez-en un.
1. Collez l’intégralité de `<UserJourney>` que vous avez copié en tant qu’enfant de l’élément `<UserJourneys>`.
1. Renommez l’ID du nouveau `<UserJourney>` (par exemple, SignUpOrSignUsingContoso).

### <a name="display-the-button"></a>Afficher le « bouton »

L’élément `<ClaimsProviderSelection>` est analogue à un bouton de fournisseur d’identité sur un écran d’inscription/de connexion. En ajoutant un élément `<ClaimsProviderSelection>` pour Azure AD, un nouveau bouton apparaît quand un utilisateur arrive sur la page. Pour ce faire :

1. Recherchez `<OrchestrationStep>` avec `Order="1"` dans le `<UserJourney>` que vous venez de créer.
1. Ajoutez ce qui suit :

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
    ```

1. Définissez `TargetClaimsExchangeId` sur une valeur appropriée. Nous vous recommandons de suivre la même convention que pour les autres : *\[ClaimProviderName\]Exchange*.

### <a name="link-the-button-to-an-action"></a>Lier le « bouton » à une action

Maintenant que vous avez un « bouton » en place, vous devez le lier à une action. L’action est, dans ce cas, la communication d’Azure AD B2C avec Azure AD pour recevoir un jeton. Vous pouvez faire cela en liant le profil technique de votre fournisseur de revendications Azure AD.

1. Recherchez les `<OrchestrationStep>` avec `Order="2"` dans le nœud `<UserJourney>`
1. Ajoutez ce qui suit :

    ```XML
    <ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="ContosoProfile" />
    ```

1. Mettez à jour `Id` à la même valeur que celle de `TargetClaimsExchangeId` ci-dessus.
1. Mettez à jour `TechnicalProfileReferenceId` avec la valeur `Id` du profil technique créé précédemment (par exemple, ContosoProfile).

### <a name="upload-the-updated-extension-file"></a>Téléchargez le fichier d’extension mis à jour

Vous avez terminé la modification du fichier d’extension. Enregistrez et téléchargez ce fichier et assurez-vous que toutes les validations réussissent.

### <a name="update-the-rp-file"></a>Mise à jour du fichier RP

Vous devez maintenant mettre à jour le fichier RP qui initialisera le parcours utilisateur que vous venez de créer.

1. Effectuez une copie de SignUpOrSignIn.xml dans votre répertoire de travail et renommez-le (par exemple, SignUpOrSignInWithAAD.xml).
1. Ouvrez le nouveau fichier et mettez à jour l’attribut `PolicyId` pour `<TrustFrameworkPolicy>` avec une valeur unique. Ce sera le nom de votre stratégie (par exemple, SignUpOrSignInWithAAD).
1. Modifiez l’attribut `ReferenceId` dans `<DefaultUserJourney>` pour faire correspondre l’ID du nouveau parcours utilisateur que vous avez créé (par exemple, SignUpOrSignUsingContoso).
1. Enregistrez vos modifications et téléchargez le fichier.

## <a name="troubleshooting"></a>Résolution des problèmes

Testez la stratégie personnalisée que vous venez de télécharger en ouvrant son panneau et en cliquant sur « Exécuter maintenant ». En cas d’échec quelconque, consultez le guide de [résolution des problèmes](active-directory-b2c-troubleshoot-custom.md).

## <a name="next-steps"></a>Étapes suivantes

Fournissez des commentaires à AADB2CPreview@microsoft.com.

