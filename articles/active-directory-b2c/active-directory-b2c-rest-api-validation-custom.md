---
title: "Azure AD B2C - Intégrer les échanges de revendications de l’API REST comme étape de validation dans les stratégies personnalisées | Microsoft Docs"
description: "Une rubrique sur les stratégies personnalisées Azure Active Directory B2C"
services: active-directory-b2c
documentationcenter: 
author: rojasja
manager: krassk
editor: rojasja
ms.assetid: 
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 04/24/2017
ms.author: joroja
ms.translationtype: Human Translation
ms.sourcegitcommit: 2db2ba16c06f49fd851581a1088df21f5a87a911
ms.openlocfilehash: 46abe48c3c9a7aab3fe013811d088a63957fe500
ms.contentlocale: fr-fr
ms.lasthandoff: 05/08/2017

---

# <a name="walkthrough-integrate-rest-api-claims-exchanges-in-your-azure-ad-b2c-user-journeys-as-validation-on-user-input"></a>Procédure pas à pas : intégration des échanges de revendications de l’API REST dans vos parcours utilisateur Azure Active Directory B2C comme une étape de validation sur saisie de l’utilisateur

**L’IEF** (Identity Experience Framework ou « infrastructure d’expérience d’identité ») sur laquelle repose Azure AD B2C permet au développeur d’identité d’intégrer une interaction avec une API RESTful dans un parcours utilisateur.  

À la fin de cette procédure pas à pas, vous serez en mesure de créer des parcours utilisateur Azure AD B2C qui interagissent avec les services RESTful.

L’IEF envoie des données dans des revendications et reçoit en retour des données via des revendications.  L’interaction avec l’API peut être conçue comme échange de revendications de l’API REST et profil de validation dans le cadre d’une étape d’orchestration.

- Cela valide en général la saisie de l’utilisateur.
- Si la valeur de l’utilisateur est rejetée, l’utilisateur peut essayer à nouveau de saisir une valeur valide, avec la possibilité de renvoyer un message d’erreur à l’utilisateur.

Cette interaction peut également être conçue comme une étape d’orchestration. Pour en savoir plus, consultez l’article [Procédure pas à pas : intégration des échanges de revendications de l’API REST dans vos parcours utilisateur Azure Active Directory B2C comme une étape d’orchestration](active-directory-b2c-rest-api-step-custom.md).

Pour l’exemple de profil de validation, nous allons utiliser le parcours utilisateur de modification de profil dans le fichier ProfileEdit.xml du pack de démarrage.

Nous pouvons vérifier que le prénom fourni par l’utilisateur dans la modification de profil ne figure sur aucune liste d’exclusion.

## <a name="prerequisites"></a>Composants requis

- Un client Azure AD B2C configuré pour effectuer une inscription/connexion à un compte local selon les indications de l’article [Getting Started](active-directory-b2c-get-started-custom.md) (Mise en route).
- Un point de terminaison API REST avec lequel vous allez interargir. Un site de démonstration [WingTipGames](https://wingtipgamesb2c.azurewebsites.net/) a été configuré avec un service d’API REST qui sera utilisé pour cette procédure pas à pas.

## <a name="step-1---prepare-the-rest-api-function"></a>Étape 1 : préparation de la fonction de l’API REST

> [!NOTE]
> Cet article ne traite pas de la configuration des fonctions de l’API REST. [Azure Function Apps](https://docs.microsoft.com/azure/azure-functions/functions-reference) fournit un excellent kit de ressources pour la création de services RESTful dans le cloud.

Nous avons créé une fonction Azure qui reçoit une revendication attendue comme « playerTag » et définit si cette revendication existe ou non. Vous pouvez accéder au code complet de la fonction Azure dans [GitHub](https://github.com/Azure-Samples/active-directory-b2c-advanced-policies/tree/master/AzureFunctionsSamples).

```
if (requestContentAsJObject.playerTag == null)
    {
        return request.CreateResponse(HttpStatusCode.BadRequest);
    }

    var playerTag = ((string) requestContentAsJObject.playerTag).ToLower();

    if (playerTag == "mcvinny" || playerTag == "msgates123" || playerTag == "revcottonmarcus")
    {
        return request.CreateResponse<ResponseContent>(
            HttpStatusCode.Conflict,
            new ResponseContent
            {
                version = "1.0.0",
                status = (int) HttpStatusCode.Conflict,
                userMessage = $"The player tag '{requestContentAsJObject.playerTag}' is already used."
            },
            new JsonMediaTypeFormatter(),
            "application/json");
    }

    return request.CreateResponse(HttpStatusCode.OK);
```

La revendication `userMessage` retournée par la fonction Azure est attendue par l’infrastructure d’expérience d’identité et sera présentée sous forme de chaîne à l’utilisateur en cas d’échec de la validation, notamment si l’état 409 conflit est retourné dans l’exemple ci-dessus.

## <a name="step-2---configure-the-restful-api-claims-exchange-as-a-technical-profile-in-your-trustframeworkextensionsxml-file"></a>Étape 2 : configuration de l’échange de revendications de l’API RESTful comme profil technique dans votre fichier TrustFrameworkExtensions.xml

Le profil technique est la configuration complète de l’échange souhaité avec le service RESTful. Ouvrez le fichier `TrustFrameworkExtensions.xml` et ajoutez l’extrait de code XML ci-dessous à l’intérieur de l’élément `<ClaimsProviders>`.

> [!NOTE]
> Vous pouvez considérer l’élément « Restful Provider, Version 1.0.0.0 » (décrit ci-dessous comme un protocole) comme étant la fonction qui interagit avec le service externe.  Vous trouverez ici <!-- TODO: Link to RESTful Provider schema definition>--> une définition complète du schéma.

```
<ClaimsProvider>
        <DisplayName>REST APIs</DisplayName>
        <TechnicalProfiles>
            <TechnicalProfile Id="AzureFunctions-CheckPlayerTagWebHook">

                    <DisplayName>Check Player Tag Web Hook Azure Function</DisplayName>
                    <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
                    <Metadata>
                        <Item Key="ServiceUrl">https://wingtipb2cfuncs.azurewebsites.net/api/CheckPlayerTagWebHook?code=L/05YRSpojU0nECzM4Tp3LjBiA2ZGh3kTwwp1OVV7m0SelnvlRVLCg==</Item>
                        <Item Key="AuthenticationType">None</Item>
                        <Item Key="SendClaimsIn">Body</Item>
                    </Metadata>
                    <InputClaims>
                        <InputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="playerTag" />
                    </InputClaims>
                    <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
            </TechnicalProfile>


            <TechnicalProfile Id="SelfAsserted-ProfileUpdate">

                <ValidationTechnicalProfiles>       
                    <ValidationTechnicalProfile ReferenceId="AzureFunctions-CheckPlayerTagWebHook" />       
                </ValidationTechnicalProfiles>

            </TechnicalProfile>
        </TechnicalProfiles>
    </ClaimsProvider>
```

L’élément `InputClaims` définit les revendications qui seront envoyées depuis le moteur d’expérience d’identité (IEE) vers le service REST. Dans l’exemple ci-dessus, le contenu de la revendication `givenName` sera envoyé au service REST sous la forme d’une revendication `playerTag`. Dans cet exemple, l’IEE n’attend pas de revendications en retour. Il attend une réponse du service REST et agit selon les codes d’état reçus.

## <a name="step-3---include-the-restful-service-claims-exchange-in-self-asserted-technical-profile-where-you-wish-to-validate-the-user-input"></a>Étape 3 : intégration de l’échange de revendications du service RESTful dans le profil technique Auto-déclaré dans lequel vous souhaitez valider la saisie de l’utilisateur

L’étape de validation est le plus souvent utilisée dans le cadre d’une interaction avec un utilisateur.  Toutes les interactions impliquant une saisie de l’utilisateur sont des **profils techniques Auto-déclaré**. Pour cet exemple, nous allons ajouter cette validation au profil technique **Self-Asserted-ProfileUpdate**.  Il s’agit du profil technique utilisé par le fichier de stratégie de partie de confiance `Profile Edit`.

Pour ajouter l’échange de revendications au profil technique Auto-déclaré :

1. Ouvrez le fichier TrustFrameworkBase et recherchez `<TechnicalProfile Id="SelfAsserted-ProfileUpdate">`.
2. En analysant la configuration de ce profil technique, nous pouvons voir que l’échange avec l’utilisateur est défini sous la forme de revendications pour l’utilisateur (revendications d’entrée) et de revendications attendues par le fournisseur Auto-déclaré (revendications de sortie).
3. Recherchez `TechnicalProfileReferenceId="SelfAsserted-ProfileUpdate`. Notez que ce profil est appelé en tant qu’étape d’orchestration numéro 6 de `<UserJourney Id="ProfileEdit">`.

## <a name="step-4---upload-and-test-the-profile-edit-rp-policy-file"></a>Étape 4 : téléchargement et test du fichier de stratégie de partie de confiance Modification de profil

1. Téléchargez la nouvelle version du fichier `TrustframeworkExtensions`.
2. Sélectionnez **Exécuter maintenant** pour tester le fichier de stratégie de partie de confiance Modification de profil.
3. Testez la validation en fournissant un des noms existants (par exemple : mcvinny) dans le champ **Prénom**. Si tout est correctement configuré, vous devriez recevoir un message indiquant à l’utilisateur que l’élément `player tag` est déjà utilisé.

## <a name="next-steps"></a>Étapes suivantes

[How to modify profile edit and user registration to gather additional information from your users](active-directory-b2c-create-custom-attributes-profile-edit-custom.md) (Comment modifier la modification de profil et l’inscription des utilisateurs pour collecter des informations supplémentaires à partir de vos utilisateurs)

[Procédure pas à pas : intégration des échanges de revendications de l’API REST dans vos parcours utilisateur Azure AD B2C comme une étape d’orchestration](active-directory-b2c-rest-api-step-custom.md)

