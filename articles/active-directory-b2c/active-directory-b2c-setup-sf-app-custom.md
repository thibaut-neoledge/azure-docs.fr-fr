---
title: "Azure Active Directory B2C : ajout du fournisseur SAML Salesforce à l’aide de stratégies personnalisées | Microsoft Docs"
description: "Une rubrique sur les stratégies personnalisées Azure Active Directory B2C"
services: active-directory-b2c
documentationcenter: 
author: gsacavdm
manager: krassk
editor: gsacavdm
ms.assetid: d7f4143f-cd7c-4939-91a8-231a4104dc2c
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 04/30/2017
ms.author: gsacavdm
ms.translationtype: Human Translation
ms.sourcegitcommit: 97fa1d1d4dd81b055d5d3a10b6d812eaa9b86214
ms.openlocfilehash: 1d97c75f3130ea6fdacbc6335b6e70677b4d226e
ms.contentlocale: fr-fr
ms.lasthandoff: 05/11/2017


---
# <a name="azure-active-directory-b2c-log-in-using-salesforce-accounts-via-saml"></a>Azure Active Directory B2C : connexion à l’aide de comptes Salesforce via SAML

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Cet article vous montre comment permettre aux utilisateurs d’une organisation Salesforce spécifique de se connecter à l’aide de [stratégies personnalisées](active-directory-b2c-overview-custom.md).

## <a name="prerequisites"></a>Composants requis

### <a name="azure-ad-b2c-setup"></a>Configuration d’Azure AD B2C
Assurez-vous d’avoir effectué toutes les étapes qui vous montrent comment [prendre en main les stratégies personnalisées](active-directory-b2c-get-started-custom.md).

notamment :

1. Création d’un client Azure AD B2C.
1. Création d’une application Azure AD B2C.
1. Inscription de deux applications de moteur de stratégie.
1. Configuration des clés.
1. Configuration du pack de démarrage.

### <a name="salesforce-setup"></a>Configuration de Salesforce
Ce didacticiel part du principe que :
1. Vous êtes déjà inscrit à un compte Salesforce. Vous pouvez vous inscrire pour bénéficier d’une version [Developer Edition gratuite](https://developer.salesforce.com/signup). 
1. Vous avez déjà [configuré un My Domain](https://help.salesforce.com/articleView?id=domain_name_setup.htm&language=en_US&type=0) pour votre organisation Salesforce.

## <a name="get-the-salesforce-saml-metadata"></a>Obtenir les métadonnées SAML Salesforce
>[!NOTE]
> Ce didacticiel suppose que vous utilisez la [Lightning Experience de Salesforce](https://developer.salesforce.com/page/Lightning_Experience_FAQ).

1. [Connectez-vous à Salesforce](https://login.salesforce.com/). 
1. Dans le menu de gauche, sous **Paramètres**, développez **Identité**, puis cliquez sur **Fournisseur d’identité**.
1. Cliquez sur **Enable Identity Provider** (Activer le fournisseur d’identité).
1. **Sélectionnez le certificat** que vous souhaitez que Salesforce utilise lors de la communication avec Azure AD B2C, puis cliquez sur **Enregistrer**. Vous pouvez utiliser le certificat par défaut.
1. Cliquez sur le bouton **Download Metadata** (Télécharger les métadonnées) maintenant disponible et enregistrez le fichier de métadonnées que vous utiliserez ultérieurement.

## <a name="add-a-saml-signing-certificate-to-azure-ad-b2c"></a>Ajouter un certificat de signature SAML à Azure AD B2C
Vous devez télécharger un certificat SAML dans le client Azure AD B2C à utiliser lors de la signature de ses requêtes SAML. Pour ce faire :

1. Accédez à votre client Azure AD B2C et ouvrez Paramètres **B2C Settings > Infrastructure d’expérience d’identité > Clés de stratégie**
1. Cliquez sur **+Ajouter**
1. Options :
 * Sélectionnez **Options > Charger**
 * **Nom** : > `ContosoIdpSamlCert`.  Le préfixe B2C_1A_ est ajouté automatiquement au nom de votre clé. Notez le nom complet (avec B2C_1A_), car il vous sera demandé ultérieurement dans cette stratégie.
 * Utilisez le **contrôle du fichier de chargement** pour sélectionner votre certificat et fournir le mot de passe du certificat, le cas échéant.
1. Cliquez sur **Créer**
1. Confirmez que vous avez créé la clé : `B2C_1A_ContosoIdpSamlCert`.

## <a name="create-the-salesforce-saml-claims-provider-in-your-base-policy"></a>Créer le fournisseur de revendications SAML Salesforce dans votre stratégie de base

Afin de permettre aux utilisateurs de se connecter à l’aide de Salesforce, vous devez définir Salesforce comme fournisseur de revendications. En d’autres termes, vous devez spécifier le point de terminaison avec lequel Azure AD B2C communiquera. Le point de terminaison *fournira* un ensemble de *revendications* utilisées par Azure AD B2C pour vérifier qu’un utilisateur spécifique s’est authentifié. Vous pouvez le faire en ajoutant un `<ClaimsProvider>` pour Salesforce dans le fichier d’extension de votre stratégie.

1. Ouvrez le fichier d’extension à partir de votre répertoire de travail (TrustFrameworkExtensions.xml).
1. Recherchez la section `<ClaimsProviders>`. Si elle n’existe pas, ajoutez-la sous le nœud racine.
1. Ajoutez un nouveau `<ClaimsProvider>` comme suit :

    ```XML
    <ClaimsProvider>
      <Domain>contoso</Domain>
      <DisplayName>Contoso</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Contoso">
          <DisplayName>Contoso</DisplayName>
          <Description>Login with your Contoso account</Description>
          <Protocol Name="SAML2"/>
          <Metadata>
            <Item Key="RequestsSigned">false</Item>
            <Item Key="WantsEncryptedAssertions">false</Item>
            <Item Key="PartnerEntity">
    <![CDATA[ <md:EntityDescriptor xmlns:md="urn:oasis:names:tc:SAML:2.0:metadata" entityID="https://contoso.com" validUntil="2026-10-05T23:57:13.854Z" xmlns:ds="http://www.w3.org/2000/09/xmldsig#"><md:IDPSSODescriptor protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol"><md:KeyDescriptor use="signing"><ds:KeyInfo><ds:X509Data><ds:X509Certificate>MIIErDCCA….qY9SjVXdu7zy8tZ+LqnwFSYIJ4VkE9UR1vvvnzO</ds:X509Certificate></ds:X509Data></ds:KeyInfo></md:KeyDescriptor><md:NameIDFormat>urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified</md:NameIDFormat><md:SingleSignOnService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-POST" Location="https://contoso.com/idp/endpoint/HttpPost"/><md:SingleSignOnService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://contoso.com/idp/endpoint/HttpRedirect"/></md:IDPSSODescriptor></md:EntityDescriptor>]]>
            </Item>
          </Metadata>       
          <CryptographicKeys>
            <Key Id="SamlAssertionSigning" StorageReferenceId="B2C_1A_ContosoIdpSamlCert"/>
            <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_ContosoIdpSamlCert "/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="socialIdpUserId" PartnerClaimType="userId"/>
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name"/>
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="family_name"/>
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email"/>
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name"/>
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="externalIdp"/>
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="SAMLIdp" />
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

Pour obtenir un jeton SAML à partir de Salesforce, vous devez définir les protocoles que Azure AD B2C doit utiliser pour communiquer avec Azure AD. Cela est effectué à l’intérieur de l’élément `<TechnicalProfile>` du `<ClaimsProvider>`.

1. Mettez à jour l’ID du nœud `<TechnicalProfile>`. Cet ID est utilisé pour faire référence à ce profil technique depuis les autres parties de la stratégie.
1. Mettez à jour la valeur pour `<DisplayName>`. Cette valeur s’affichera sur le bouton de connexion dans votre écran de connexion.
1. Mettez à jour la valeur pour `<Description>`.
1. Azure AD utilise le protocole OpenID Connect, assurez-vous donc que `<Protocol>` est « SAML2 ».

Vous devez mettre à jour la section `<Metadata>` dans le XML ci-dessus pour refléter les paramètres de configuration de votre client Azure AD spécifique. Dans le XML, mettez à jour les valeurs de métadonnées comme suit :

1. Mettez à jour la valeur de `<Item Key="PartnerEntity">` avec le contenu du fichier Metadata.xml que vous avez téléchargé à partir de Salesforce. **Assurez-vous que vous l’encapsulez avec <![CDATA[ …metadata… ]]>**.

1. Dans la section `<CryptographicKeys>` du XML ci-dessus, mettez à jour la valeur pour `StorageReferenceId` sur l’ID de certificat que vous avez défini (p. ex. ContosoSalesforceCert).

### <a name="upload-the-extension-file-for-verification"></a>Télécharger le fichier d’extension pour la vérification

À ce stade, vous avez configuré votre stratégie afin qu’Azure AD B2C sache comment communiquer avec votre annuaire Azure AD. Essayez de télécharger le fichier d’extension de votre stratégie juste pour confirmer qu’il ne présente aucun problème pour le moment. Pour ce faire :

1. Accédez au panneau **Toutes les stratégies** dans votre client Azure AD B2C.
1. Cochez la case **Remplacer la stratégie si elle existe**.
1. Téléchargez le fichier d’extension (TrustFrameworkExtensions.xml) et assurez-vous qu’il n’échoue pas à la validation.

## <a name="register-the-salesforce-saml-claims-provider-to-a-user-journey"></a>Enregistrer le fournisseur de revendications SAML Salesforce pour un parcours utilisateur

Vous devez maintenant ajouter le fournisseur d’identité SAML Salesforce à l’un de vos parcours utilisateur. À ce stade, le fournisseur d’identité a été défini, mais il n’est disponible dans aucun des écrans d’inscription/de connexion. Pour ce faire, nous créons un doublon d’un modèle de parcours utilisateur existant et le modifions afin qu’il dispose également du fournisseur d’identité Azure AD.

1. Ouvrez le fichier de base de votre stratégie (par exemple, TrustFrameworkBase.xml)
1. Recherchez l’élément `<UserJourneys>` et copiez l’intégralité de `<UserJourney>` avec ID = « SignUpOrSignIn ».
1. Ouvrez le fichier d’extension (par exemple, TrustFrameworkExtensions.xml) et recherchez l’élément `<UserJourneys>`. Si l’élément n’existe pas, ajoutez-en un.
1. Collez l’intégralité de `<UserJourney>` que vous avez copié en tant qu’enfant de l’élément `<UserJourneys>`.
1. Renommez l’ID du nouveau `<UserJourney>` (par exemple, SignUpOrSignUsingContoso).

### <a name="display-the-button"></a>Afficher le « bouton »

L’élément `<ClaimsProviderSelection>` est analogue à un bouton de fournisseur d’identité sur un écran d’inscription/de connexion. En ajoutant un élément `<ClaimsProviderSelection>` pour Salesforce, un nouveau bouton apparaît quand un utilisateur arrive sur la page. Pour ce faire :

1. Recherchez `<OrchestrationStep>` avec `Order="1"` dans le `<UserJourney>` que vous venez de créer.
1. Ajoutez ce qui suit :

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
    ```

1. Définissez `TargetClaimsExchangeId` sur une valeur appropriée. Nous vous recommandons de suivre la même convention que pour les autres : *\[ClaimProviderName\]Exchange*.

### <a name="link-the-button-to-an-action"></a>Lier le « bouton » à une action

Maintenant que vous avez un « bouton » en place, vous devez le lier à une action. L’action est, dans ce cas, la communication d’Azure AD B2C avec Salesforce pour recevoir un jeton SAML. Vous pouvez faire cela en liant le profil technique de votre fournisseur de revendications SAML Salesforce.

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

## <a name="create-a-connected-app-in-salesforce"></a>Créer une application connectée dans Salesforce
Vous allez devoir enregistrer Azure AD B2C en tant qu’application connectée dans Salesforce.

1. [Connectez-vous à Salesforce](https://login.salesforce.com/). 
1. Dans le menu de gauche, sous **Paramètres**, développez **Identité**, puis cliquez sur **Fournisseur d’identité**.
1. Dans la section inférieure **Fournisseurs de services**, cliquez sur **Service Providers are now created via Connected Apps (Les fournisseurs de services sont désormais créés via les applications connectées). Cliquez ici.**
1. Fournissez les **informations de base** requises pour votre application connectée.
1. Maintenant, dans la section **Paramètres de l’application web** :
    1. Cochez l’option **Activer SAML**.
    1. Entrez l’URL suivante dans le champ **ID d’entité** et assurez-vous de remplacer le `tenantName`. 
    
        ```
        https://login.microsoftonline.com/te/tenantName.onmicrosoft.com/B2C_1A_TrustFrameworkBase
        ```

    1. Entrez l’URL suivante dans le champ **ACS URL** (URL ACS) et assurez-vous de remplacer le `tenantName`. 
        ```
        https://login.microsoftonline.com/te/tenantName.onmicrosoft.com/B2C_1A_TrustFrameworkBase/samlp/sso/assertionconsumer
        ```

    1. Gardez les valeurs par défaut de tous les autres paramètres.
1. Faites défiler jusqu’en bas, puis cliquez sur le bouton **Enregistrer**.


## <a name="troubleshooting"></a>Résolution des problèmes

Testez la stratégie personnalisée que vous venez de télécharger en ouvrant son panneau et en cliquant sur « Exécuter maintenant ». En cas d’échec quelconque, consultez le guide de [résolution des problèmes](active-directory-b2c-troubleshoot-custom.md).

## <a name="next-steps"></a>Étapes suivantes
 
Envoyez vos commentaires à l’adresse [AADB2CPreview@microsoft.com](mailto:AADB2CPreview@microsoft.com).


