---
title: "Azure Active Directory B2C : Intégration des échanges de revendications de l’API REST dans votre parcours utilisateur Azure AD B2C comme validation d’une entrée de l’utilisateur"
description: "Essayer l’intégration des échanges de revendications de l’API REST dans votre parcours utilisateur Azure AD B2C comme validation d’une entrée de l’utilisateur"
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
ms.date: 09/30/2017
ms.author: yoelh
ms.openlocfilehash: da7b8326a89fc6eb0bc3e7365c522d4a3adc809b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-b2c-integrate-rest-api-claims-exchanges-in-your-azure-ad-b2c-user-journey-as-validation-on-user-input"></a>Azure Active Directory B2C : Intégration des échanges de revendications de l’API REST dans votre parcours utilisateur Azure AD B2C comme validation d’une entrée de l’utilisateur
L’infrastructure d’expérience d’identité sur laquelle repose Azure Active Directory B2C (Azure AD B2C) vous permet d’intégrer une API RESTful à un parcours utilisateur. Dans cette procédure pas à pas, nous allons vous montrer comment B2C interagit avec les services RESTful de .NET Framework (API web).

## <a name="introduction"></a>Introduction
Azure AD B2C vous permet d’ajouter votre propre logique métier dans un parcours utilisateur en appelant votre service RESTful. L’infrastructure d’expérience d’identité envoie des données au service RESTful dans la collection **Revendications d’entrée** et reçoit des données de RESTful dans la collection **Revendications de sortie**. Avec l’intégration de service RESTful, vous pouvez :

* Valider les données d’entrée utilisateur pour empêcher que les données mal formées soient rendues persistantes dans Azure AD. Si la valeur de l’utilisateur n’est pas valide, votre service RESTful renvoie un message d’erreur qui demande à l’utilisateur de fournir une entrée. Par exemple, vous pouvez vérifier que l’adresse e-mail fournie par l’utilisateur existe dans votre base de données de clients.
* Remplacer les revendications d’entrée. Par exemple, si l’utilisateur entre le prénom en minuscules ou en majuscules, vous pouvez mettre en forme le nom et mettre seulement la première lettre en majuscules.
* Enrichir les données utilisateur en intégrant davantage une application métier d’entreprise. Votre service RESTful peut recevoir l’adresse e-mail de l’utilisateur, interroger la base de données de clients et renvoyer le numéro de fidélité de l’utilisateur dans B2C. Les revendications renvoyées peuvent être stockées dans le compte AAD utilisateur, évaluées dans les **étapes d’orchestration** suivantes ou incluses dans le jeton d’accès.
* Exécuter une logique métier personnalisée : envoyer des notifications Push, mettre à jour des bases de données d’entreprise, exécuter un processus de migration utilisateur, gestion des autorisations, audit et bien plus encore.

L’intégration aux services RESTful peut être conçue sous la forme d’un échange de revendications ou d’un profil technique de validation :

* **Profil technique de validation** - L’appel au service RESTful se produit à l’intérieur de ValidationTechnicalProfile d’un TechnicalProfile donné. Le profil technique de validation valide les données fournies par l’utilisateur avant la suite du parcours utilisateur. Avec le profil technique de validation, vous pouvez :
  * Envoyer des revendications d’entrée
  * Valider les revendications d’entrée et générer des messages d’erreur personnalisés
  * Renvoyer des revendications de sortie

* **Échange de revendications** - Similaire au profil technique de validation précédemment décrit, mais se produit à l’intérieur d’une étape d’orchestration. Cette définition est limitée à :
   * Envoyer des revendications d’entrée
   * Renvoyer des revendications de sortie

## <a name="restful-walkthrough"></a>Procédure pas à pas RESTful
Dans cette procédure pas à pas, vous allez développer une API web .NET Framework qui valide l’entrée utilisateur et fournit le numéro de fidélité de l’utilisateur. Par exemple, votre application peut accorder l’accès aux « avantages platinum » en fonction du numéro de fidélité.

Vue d’ensemble :
*   Développement du service RESTful (API web .NET Framework)
*   Utilisation du service RESTful dans le parcours utilisateur
*   Envoi des revendications d’entrée et lecture de celles-ci dans votre code
*   Validation du prénom de l’utilisateur
*   Renvoi du numéro de fidélité 
*   Ajout du numéro de fidélité au jeton web JSON (JWT)

## <a name="prerequisites"></a>Composants requis
Suivez les étapes décrites dans [Bien démarrer avec les stratégies personnalisées](active-directory-b2c-get-started-custom.md).

## <a name="step-1-create-an-aspnet-web-api"></a>Étape 1 : Créer une API web ASP.NET
1.  Dans Visual Studio, créez un projet en sélectionnant **Fichier > Nouveau > Projet**.
2.  Dans la boîte de dialogue **Nouveau projet**, sélectionnez **Visual C# > Web > Application web ASP.NET (.NET Framework)**.
3.  Nommez l’application, par exemple Contoso.AADB2C.API, puis sélectionnez **OK**.

    ![Créer un projet Visual Studio](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-create-project.png)

4.  Sélectionner le modèle **API web** ou **Application API Azure**
5.  Vérifier que l’authentification est définie sur **Aucune authentification**

    ![Sélectionner le modèle d’API web](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-select-web-api.png)

6.  Cliquer sur **OK** pour créer le projet

## <a name="step-2-prepare-the-rest-api-endpoint"></a>Étape 2 : Préparer le point de terminaison de l’API REST

### <a name="step-21-add-data-models"></a>Étape 2.1 Ajouter des modèles de données
Les modèles représentent les données des revendications d’entrée et de sortie dans votre service RESTful. Votre code lit les données d’entrée en désérialisant le modèle de revendications d’entrée à partir de la chaîne JSON vers l’objet C# (votre modèle). L’API Web ASP.NET désérialise automatiquement le modèle de revendications de sortie dans JSON, puis écrit les données sérialisées dans le corps du message de réponse HTTP. Commençons par créer un modèle qui représente les revendications d’entrée.

1.  Si l’Explorateur de solutions n’est pas déjà visible, cliquez sur le menu **Vue** et sélectionnez **Explorateur de solutions**. Dans l’Explorateur de solutions, cliquez avec le bouton droit sur le dossier Modèles. Dans le menu contextuel, sélectionnez **Ajouter**, puis **Classe**.

    ![Ajouter un modèle](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-add-model.png)

2.  Nommez la classe `InputClaimsModel` et ajoutez les propriétés suivantes à la classe `InputClaimsModel`.

    ```C#
    namespace Contoso.AADB2C.API.Models
    {
        public class InputClaimsModel
        {
            public string email { get; set; }
            public string firstName { get; set; }
            public string lastName { get; set; }
        }
    }
    ```

3.  Créez un modèle `OutputClaimsModel` et ajoutez les propriétés suivantes à la classe `OutputClaimsModel`.

    ```C#
    namespace Contoso.AADB2C.API.Models
    {
        public class OutputClaimsModel
        {
            public string loyaltyNumber { get; set; }
        }
    }
    ```

4.  Créez un modèle `B2CResponseContent` supplémentaire. Ce modèle est utilisé pour générer des messages d’erreur de validation d’entrée. Ajoutez les propriétés suivantes à la classe `B2CResponseContent`, indiquez les références manquantes et enregistrez le fichier.

    ```C#
    namespace Contoso.AADB2C.API.Models
    {
        public class B2CResponseContent
        {
            public string version { get; set; }
            public int status { get; set; }
            public string userMessage { get; set; }

            public B2CResponseContent(string message, HttpStatusCode status)
            {
                this.userMessage = message;
                this.status = (int)status;
                this.version = Assembly.GetExecutingAssembly().GetName().Version.ToString();
            }    
        }
    }
    ```

### <a name="step-22-add-a-controller"></a>Étape 2.2 : Ajouter un contrôleur
Dans l’API web, un _contrôleur_ est un objet qui gère les requêtes HTTP. Nous ajoutons un contrôleur qui renvoie les revendications de sortie, ou, si le prénom n’est pas valide, génère le message d’erreur HTTP de conflit.

1.  Dans **l’Explorateur de solutions**, cliquez avec le bouton droit sur le dossier Contrôleurs. Sélectionnez **Ajouter**, puis **Contrôleur**.

    ![Ajouter un contrôleur](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-add-controller-1.png)

2.  Dans la boîte de dialogue **Ajouter une structure**, cliquez sur **Web API Controller - Empty** (Contrôleur d’API web - Vide). Cliquez sur **Add**.

    !![Sélectionner Contrôleur API web 2 - Vide](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-add-controller-2.png)

3.  Dans la boîte de dialogue **Ajouter un contrôleur**, nommez le contrôleur `IdentityController`, puis cliquez sur **Ajouter**.

    ![Taper le nom du contrôleur](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-add-controller-3.png)

    La génération de modèles automatique crée un fichier nommé IdentityController.cs dans le dossier Contrôleurs.

4.  Si ce fichier n’est pas déjà ouvert, double-cliquez dessus pour l’ouvrir. Remplacez le code de ce fichier par les lignes de code suivantes :

    ```C#
    using Contoso.AADB2C.API.Models;
    using Newtonsoft.Json;
    using System;
    using System.NET;
    using System.Web.Http;

    namespace Contoso.AADB2C.API.Controllers
    {
        public class IdentityController: ApiController
        {
            [HttpPost]
            public IHttpActionResult SignUp()
            {
                // If not data came in, then return
                if (this.Request.Content == null) throw new Exception();

                // Read the input claims from the request body
                string input = Request.Content.ReadAsStringAsync().Result;

                // Check input content value
                if (string.IsNullOrEmpty(input))
                {
                    return Content(HttpStatusCode.Conflict, new B2CResponseContent("Request content is empty", HttpStatusCode.Conflict));
                }

                // Convert the input string into InputClaimsModel object
                InputClaimsModel inputClaims = JsonConvert.DeserializeObject(input, typeof(InputClaimsModel)) as InputClaimsModel;

                if (inputClaims == null)
                {
                    return Content(HttpStatusCode.Conflict, new B2CResponseContent("Can not deserialize input claims", HttpStatusCode.Conflict));
                }

                // Run input validation
                if (inputClaims.firstName.ToLower() == "test")
                {
                    return Content(HttpStatusCode.Conflict, new B2CResponseContent("Test name is not valid, please provide a valid name", HttpStatusCode.Conflict));
                }

                // Create output claims object and set the loyalty number with random value
                OutputClaimsModel outputClaims = new OutputClaimsModel();
                outputClaims.loyaltyNumber = new Random().Next(100, 1000).ToString();

                // Return the output claim(s)
                return Ok(outputClaims);
            }
        }
    }
    ```

## <a name="step-3-publish-to-azure"></a>Étape 3 : Publier dans Azure
1.  Dans **l’Explorateur de solutions**, cliquez avec le bouton droit sur le projet **Contoso.AADB2C.API**, puis sélectionnez **Publier**.

    ![Publier dans Microsoft Azure App Service](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-publish-to-azure-1.png)

2.  Assurez-vous que **Microsoft Azure App Service** est sélectionné, puis sélectionnez **Publier**.

    ![Créer un Microsoft Azure App Service](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-publish-to-azure-2.png)

    Cette opération affiche la boîte de dialogue **Créer App Service**, qui vous permet de créer toutes les ressources Azure nécessaires pour exécuter l’application web ASP.NET dans Azure.

> [!NOTE]
>Pour plus d’informations sur la publication, consultez : [Création d’une application web ASP.NET dans Azure](https://docs.microsoft.com/en-us/azure/app-service-web/app-service-web-get-started-dotnet#publish-to-azure)

3.  Sous **Nom de l’application web**, tapez un nom d’application unique (les caractères autorisés sont `a-z`, `0-9` et `-`). L’URL de l’application web est `http://<app_name>.azurewebsites.NET`, où `<app_name>` correspond au nom de votre application web. Vous pouvez accepter le nom généré automatiquement, qui est unique.

    Sélectionnez **Créer** pour commencer à créer les ressources Azure.

    ![Fournir les propriétés App Service](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-publish-to-azure-3.png)

4.  Une fois que l’Assistant a terminé, il publie l’application web ASP.NET dans Azure, puis lance l’application dans le navigateur par défaut. Copiez l’URL.

## <a name="step-4-add-the-new-claim-loyaltynumber-to-the-schema-of-your-trustframeworkextensionsxml-file"></a>Étape 4 : Ajouter la nouvelle revendication `loyaltyNumber` au schéma de votre fichier TrustFrameworkExtensions.xml
La revendication `loyaltyNumber` n’est définie pour l’instant nulle part dans notre schéma. Ajoutez donc une définition à l’intérieur de l’élément `<BuildingBlocks>`. Vous pouvez trouver cet élément au début du fichier TrustFrameworkExtensions.xml.

```xml
<BuildingBlocks>
    <ClaimsSchema>
        <ClaimType Id="loyaltyNumber">
            <DisplayName>loyaltyNumber</DisplayName>
            <DataType>string</DataType>
            <UserHelpText>Customer loyalty number</UserHelpText>
        </ClaimType>
    </ClaimsSchema>
</BuildingBlocks>
```

## <a name="step-5-adding-claims-provider"></a>Étape 5 : Ajouter le fournisseur de revendications 
Chaque fournisseur de revendications doit avoir un ou plusieurs profils techniques qui déterminent les points de terminaison et les protocoles nécessaires pour communiquer avec ce fournisseur de revendications. Un fournisseur de revendications peut avoir plusieurs profils techniques pour diverses raisons. Par exemple, plusieurs profils techniques peuvent être définis, car le fournisseur de revendications envisagé prend en charge plusieurs protocoles, divers points de terminaison avec des fonctionnalités distinctes ou publie des revendications différentes à divers niveaux d’assurance. Il peut être acceptable de publier des revendications sensibles dans un parcours utilisateur, mais pas dans un autre. 

L’extrait de code XML suivant contient le nœud `ClaimsProvider` avec deux `TechnicalProfile` :
* `<TechnicalProfile Id="REST-API-SignUp">`définit votre service RESTful. Le `Proprietary` est décrit en tant que protocole pour un fournisseur basé sur RESTful. L’élément `InputClaims` définit les revendications qui seront envoyées depuis B2C vers le service REST. Dans cet exemple, le contenu de la revendication `givenName` est envoyé au service REST en tant que `firstName`, le contenu de la revendication `surename` est envoyé au service REST en tant que `lastName`, le `email` est envoyé tel quel. L’élément `OutputClaims` définit les revendications qui récupèrent à partir du service RESTful vers B2C.

* `<TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">` ajoute le profil technique de validation au profil technique existant (défini dans la stratégie de base). Lors du parcours d’inscription, le profil technique de validation appelle le profil technique ci-dessus. Si le service RESTful renvoie une erreur HTTP 409 (conflit), le message d’erreur s’affiche pour l’utilisateur. 

Recherchez le nœud `<ClaimsProviders>` et ajoutez l’extrait de code XML suivant sous le nœud `<ClaimsProviders>` :

```xml
<ClaimsProvider>
    <DisplayName>REST APIs</DisplayName>
    <TechnicalProfiles>
    
    <!-- Custom Restful service -->
    <TechnicalProfile Id="REST-API-SignUp">
        <DisplayName>Validate user's input data and return loyaltyNumber claim</DisplayName>
        <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
        <Metadata>
        <Item Key="ServiceUrl">https://yourdomain.azurewebsites.NET/api/identity/signup</Item>
        <Item Key="AuthenticationType">None</Item>
        <Item Key="SendClaimsIn">Body</Item>
        </Metadata>
        <InputClaims>
        <InputClaim ClaimTypeReferenceId="email" />
        <InputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="firstName" />
        <InputClaim ClaimTypeReferenceId="surname" PartnerClaimType="lastName" />
        </InputClaims>
        <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="loyaltyNumber" PartnerClaimType="loyaltyNumber" />
        </OutputClaims>
        <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
    </TechnicalProfile>

<!-- Change LocalAccountSignUpWithLogonEmail technical profile to support your validation technical profile -->
    <TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
        <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="loyaltyNumber" PartnerClaimType="loyaltyNumber" />
        </OutputClaims>
        <ValidationTechnicalProfiles>
        <ValidationTechnicalProfile ReferenceId="REST-API-SignUp" />
        </ValidationTechnicalProfiles>
    </TechnicalProfile>

    </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="step-6-add-the-loyaltynumber-claim-to-your-relying-party-policy-file-so-the-claim-is-sent-to-your-application"></a>Étape 6 : Ajouter la revendication `loyaltyNumber` à votre fichier de stratégie de partie de confiance pour que la revendication soit envoyée à votre application
Ouvrez votre fichier de partie de confiance SignUpOrSignIn.xml et modifiez l’élément `<TechnicalProfile Id="PolicyProfile">` en y ajoutant ceci : `<OutputClaim ClaimTypeReferenceId="loyaltyNumber" />`.

Une fois que vous avez ajouté la nouvelle revendication, la `RelyingParty` ressemble à ceci :

```xml
<RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
    <TechnicalProfile Id="PolicyProfile">
        <DisplayName>PolicyProfile</DisplayName>
        <Protocol Name="OpenIdConnect" />
        <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="email" />
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
        <OutputClaim ClaimTypeReferenceId="identityProvider" />
        <OutputClaim ClaimTypeReferenceId="loyaltyNumber" DefaultValue="" />
        </OutputClaims>
        <SubjectNamingInfo ClaimType="sub" />
    </TechnicalProfile>
    </RelyingParty>
</TrustFrameworkPolicy>
```

## <a name="step-7-upload-the-policy-to-your-tenant"></a>Étape 7 : Charger la stratégie sur votre locataire
1.  Dans le [portail Azure](https://portal.azure.com), passez au [contexte de votre locataire Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md) et ouvrez **Azure AD B2C**
2.  Sélectionnez **Infrastructure d’expérience d’identité**
3.  Ouvrez **Toutes les stratégies**. 
4.  Sélectionnez **Charger la stratégie**
5.  Cochez la case **Remplacer la stratégie si elle existe**.
6.  **Téléchargez** TrustFrameworkExtensions.xml et vérifiez que sa validation n’échoue pas
7.  Répétez la dernière étape et chargez le fichier SignUpOrSignIn.xml

## <a name="step-8-test-the-custom-policy-by-using-run-now"></a>Étape 8 : Tester la stratégie personnalisée en utilisant Exécuter maintenant
1.  Ouvrez **Paramètres Azure AD B2C** et accédez à **Infrastructure d’expérience d’identité**.

> [!NOTE]
>
>**Exécuter maintenant** nécessite la préinscription d’au moins une application sur le locataire. Pour découvrir comment inscrire des applications, consultez les articles sur Azure AD B2C [Bien démarrer](active-directory-b2c-get-started.md) et [Inscription des applications](active-directory-b2c-app-registration.md).


2.  Ouvrez **B2C_1A_signup_signin**, la stratégie personnalisée de partie de confiance que vous avez chargée. Sélectionnez **Exécuter maintenant**.

    ![](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-run.png)

3.  Essayez de taper « Test » dans le champ **Prénom** ; B2C affiche un message d’erreur en haut de la page.

    ![Tester votre stratégie](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-test.png)

4.  Essayez d’entrer un nom (différent de « test ») dans le champ **Prénom**. B2C inscrit l’utilisateur, puis envoie le loyaltyNumber à votre application. Notez le numéro dans ce JWT.

```
{
  "typ": "JWT",
  "alg": "RS256",
  "kid": "X5eXk4xyojNFum1kl2Ytv8dlNP4-c57dO6QGTVBwaNk"
}.{
  "exp": 1507125903,
  "nbf": 1507122303,
  "ver": "1.0",
  "iss": "https://login.microsoftonline.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
  "aud": "e1d2612f-c2bc-4599-8e7b-d874eaca1ee1",
  "acr": "b2c_1a_signup_signin",
  "nonce": "defaultNonce",
  "iat": 1507122303,
  "auth_time": 1507122303,
  "loyaltyNumber": "290",
  "given_name": "Emily",
  "emails": ["B2cdemo@outlook.com"]
}
```

## <a name="optional-download-the-complete-policy-files-and-code"></a>[Facultatif] Télécharger les fichiers de stratégie et le code complets
* Nous vous recommandons de créer votre scénario avec vos propres fichiers de stratégie personnalisée après avoir suivi la procédure pas à pas Bien démarrer avec les stratégies personnalisées, au lieu d’utiliser ces exemples de fichiers.  [Exemples de fichiers de stratégie de référence](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw)
* Vous pouvez télécharger le code complet ici [Exemple de solution Visual Studio pour référence](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw/).
    
## <a name="next-steps"></a>Étapes suivantes
1.  [Azure Active Directory B2C: Secure your RESTful services using HTTP basic authentication](active-directory-b2c-custom-rest-api-netfw-secure-basic.md) (Azure Active Directory B2C : Sécuriser vos services RESTful à l’aide de l’authentification HTTP de base)
2.  [Azure Active Directory B2C: Secure your RESTful services using client certificates](active-directory-b2c-custom-rest-api-netfw-secure-cert.md) (Azure Active Directory B2C : Sécuriser vos services RESTful à l’aide de certificats client)