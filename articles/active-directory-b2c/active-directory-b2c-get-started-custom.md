---
title: "Azure Active Directory B2C : bien démarrer avec les stratégies personnalisées | Microsoft Docs"
description: "Une rubrique pour bien démarrer avec les stratégies personnalisées Azure Active Directory B2C"
services: active-directory-b2c
documentationcenter: 
author: gsacavdm
manager: krassk
editor: parakhj
ms.assetid: 658c597e-3787-465e-b377-26aebc94e46d
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 04/04/2017
ms.author: gsacavdm
ms.translationtype: Human Translation
ms.sourcegitcommit: 64bd7f356673b385581c8060b17cba721d0cf8e3
ms.openlocfilehash: 93fc922433f0ba60ee438db8d4dcb14527c208fc
ms.contentlocale: fr-fr
ms.lasthandoff: 05/02/2017

---
# <a name="azure-active-directory-b2c-getting-started-with-custom-policies"></a>Azure Active Directory B2C : bien démarrer avec les stratégies personnalisées

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Une fois que vous aurez suivi la procédure de cet article, votre stratégie personnalisée prendra en charge l’inscription ou la connexion de « comptes locaux » avec une adresse électronique et un mot de passe. Vous préparerez également votre environnement à l’ajout de fournisseurs d’identité supplémentaires (par exemple, Facebook ou Azure AD).  Il nécessaire de suivre ces étapes avant de se familiariser avec toutes les autres utilisations du moteur d’expérience d’identité d’Azure AD B2C et de nombreux concepts essentiels.

## <a name="prerequisites"></a>Composants requis

Avant de poursuivre, vérifiez que vous disposez d’un client Azure AD B2C. Un client Azure AD B2C est un conteneur comprenant tous vos utilisateurs, applications, stratégies, etc. Si vous n’en avez pas, vous devez en [créer un](active-directory-b2c-get-started.md).

### <a name="confirming-your-b2c-tenant"></a>Confirmer votre client B2C

Étant donné que les stratégies personnalisées sont toujours en version préliminaire privée, vérifiez que votre client Azure AD B2C est compatible avec le chargement de stratégies personnalisées :

1. Sur le [Portail Azure](https://portal.azure.com), [basculez dans le contexte de votre client Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md) et ouvrez le panneau Azure AD B2C.
1. Cliquez sur **Toutes les stratégies**.
1. Vérifiez que **Charger la stratégie** est disponible.  Si le bouton est désactivé, envoyez un e-mail à AADB2CPreview@microsoft.com.

## <a name="set-up-keys-for-your-custom-policy"></a>Configurer les clés de votre stratégie personnalisée

La première étape nécessaire pour utiliser les stratégies personnalisées consiste à configurer les clés.

[!INCLUDE [active-directory-b2c-setup-keys-custom.md](../../includes/active-directory-b2c-setup-keys-custom.md)]

## <a name="download-starter-pack-and-modify-policies"></a>Télécharger le pack de démarrage et modifier les stratégies

Les stratégies personnalisées sont un ensemble de fichiers XML qui doivent être chargés sur votre client Azure AD B2C. Nous fournissons un pack de démarrage que vous pouvez utiliser pour commencer. Le pack de démarrage contient :

* Le [fichier de base](active-directory-b2c-overview-custom.md#policy-files) de la stratégie. Il n’y a que peu de modifications à apporter la base.
* Le [fichier d’extension](active-directory-b2c-overview-custom.md#policy-files) de la stratégie.  C’est sur ce fichier que portent la plupart des modifications de la configuration.

C’est parti :

1. Téléchargez le pack de démarrage « Azure AD B2C Custom Policy Starter Pack » sur GitHub.  [Téléchargez le fichier zip](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip) ou exécutez :

    ```console
    git clone https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack
    ```

1. Ouvrez le dossier `SocialIDPAndLocalAccounts`.  Le fichier de base (`TrustFrameworkBase.xml`) de ce dossier comporte le contenu nécessaire aux comptes locaux et sociaux / d’entreprise. Le contenu social n’interfère pas avec les étapes d’opérationnalisation des comptes locaux.
1. Ouvrez `TrustFrameworkBase.xml`.  Si vous avez besoin d’un éditeur XML, essayez de [télécharger Visual Studio Code](https://code.visualstudio.com/download), un éditeur multiplatformes léger.
1. Dans l’élément `TrustFrameworkPolicy` racine, mettez à jour les attributs `TenantId` et `PublicPolicyUri` en remplaçant `{tenantName}` par votre client Azure AD B2C :

    ```xml
    <TrustFrameworkPolicy
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:xsd="http://www.w3.org/2001/XMLSchema"
    xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
    PolicySchemaVersion="0.3.0.0"
    TenantId="{tenantName}.onmicrosoft.com"
    PolicyId="B2C_1A_TrustFrameworkBase"
    PublicPolicyUri="http://{tenantName}.onmicrosoft.com">
    ```

1. Enregistrez le fichier.
1. Ouvrez `TrustFrameworkExtensions.xml` et apportez les mêmes modifications en remplaçant `{tenantName}` par votre client Azure AD B2C. Enregistrez le fichier.
1. Ouvrez `SignUpOrSignIn.xml` et apportez les mêmes modifications en remplaçant `{tenantName}` par votre client Azure AD B2C. Enregistrez le fichier.

>[!NOTE]
>Si votre éditeur XML prend en charge la validation, vous pouvez valider la conformité des fichiers au fichier de schéma XML `TrustFrameworkPolicy_0.3.0.0.xsd`, qui se trouve dans le dossier racine du pack de démarrage. La validation du schéma XML identifie les erreurs avant le chargement.

## <a name="register-policy-engine-applications"></a>Inscrire des applications du moteur de stratégie

Avec Azure AD B2C, vous devez inscrire deux applications supplémentaires : elles sont utilisées par le moteur pour inscrire et connecter les utilisateurs.

>[!NOTE]
>Ci-dessous, nous créons deux applications qui gèrent la connexion à l’aide de comptes locaux : PolicyEngine (une application web) et PolicyEngineProxy (une application native) avec une autorisation déléguée de PolicyEngine. Cette section est uniquement requise pour les clients Azure AD B2C lorsque l’utilisation de comptes locaux est attendue.

### <a name="create-the-policy-engine-application"></a>Créer l’application du moteur de stratégie

1. Sur le [Portail Azure](https://portal.azure.com), [basculez dans le contexte de votre client Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md).
1. Ouvrez le panneau `Azure Active Directory` (et non le panneau Azure AD B2C). Vous devrez peut-être cliquer sur **> Plus de services** pour le trouver.
1. Sélectionnez **Inscriptions d’applications**.
1. Cliquez sur **+ Inscription d’une nouvelle application**.
   * Nom : `PolicyEngine`
   * Type d’application : `Web app/API`
   * URL d’ouverture de session : `https://login.microsoftonline.com/{tenantName}.onmicrosoft.com`, où `{tenantName}` est votre client Azure AD B2C.
1. Cliquez sur **Create**.
1. Après la création, sélectionnez la nouvelle application `PolicyEngine`, copiez l’ID d’application et enregistrez-le en vue d’une utilisation future.

### <a name="create-the-policy-engine-proxy-application"></a>Créer l’application de proxy du moteur de stratégie

1. Sélectionnez **Inscriptions d’applications**.
1. Cliquez sur **+ Inscription d’une nouvelle application**.
   * Nom : `PolicyEngineProxy`
   * Type d’application : `Native`
   * URL d’ouverture de session : `https://login.microsoftonline.com/{tenantName}.onmicrosoft.com`, où `{tenantName}` est votre client Azure AD B2C.
1. Cliquez sur **Create**.
1. Après la création, sélectionnez l’application `PolicyEngineProxy`, copiez l’ID d’application et enregistrez-le en vue d’une utilisation future.
1. Sélectionnez **Autorisations requises**, puis **+ Ajouter** et enfin **Sélectionner une API**.
1. Recherchez le nom `PolicyEngine`, sélectionnez PolicyEngine dans les résultats, puis cliquez sur **Sélectionner**.
1. Cochez la case `Access PolicyEngine`, puis cliquez sur **Sélectionner**.
1. Cliquez sur **Done**.

### <a name="add-the-application-ids-to-your-custom-policy"></a>Ajouter les ID d’application à votre stratégie personnalisée

Pour créer une stratégie personnalisée avec comptes locaux activés, vous devez ajouter les ID d’application au fichier d’extensions (`TrustFrameworkExtensions.xml`).

1. Dans le fichier d’extensions (`TrustFrameworkExtensions.xml`), recherchez l’élément `<TechnicalProfile Id="login-NonInteractive">`.
1. Remplacez les deux instances de `{Policy Engine Proxy Application ID}` par l’ID de [l’application de proxy du moteur de stratégie que vous avez créée](#create-the-policy-engine-proxy-application).
1. Remplacez les deux instances de `{Policy Engine Application ID}` par l’ID de [l’application du moteur de stratégie que vous avez créée](#create-the-policy-engine-application).
1. Enregistrez votre fichier d’extensions.

## <a name="upload-the-policies-to-your-tenant"></a>Charger les stratégies vers votre client

1. Sur le [Portail Azure](https://portal.azure.com), [basculez dans le contexte de votre client Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md) et ouvrez le panneau Azure AD B2C.
1. Cliquez sur **Toutes les stratégies**.
1. Sélectionnez **Charger la stratégie**

    >[!WARNING]
    >Les fichiers de stratégie personnalisée doivent être chargés dans l’ordre suivant :

1. Chargez `TrustFrameworkBase.xml`.
1. Chargez `TrustFrameworkExtensions.xml`.
1. Chargez `SignUpOrSignin.xml`.

Lorsqu’un fichier est chargé, le nom est précédé de `B2C_1A_`.  Les stratégies prédéfinies, elles, commencent par `B2C_1_`.

## <a name="test-the-custom-policy-using-run-now"></a>Tester la stratégie personnalisée avec « Exécuter maintenant »

1. Ouvrez le **panneau Azure AD B2C** et accédez à **Toutes les stratégies**.
1. Sélectionnez la stratégie personnalisée que vous avez téléchargée, puis cliquez sur le bouton **Exécuter maintenant**.
1. Vous devriez pouvoir vous inscrire à l’aide d’une adresse de messagerie.

## <a name="next-steps"></a>Étapes suivantes

Le fichier de base que nous avons utilisé dans ce guide de démarrage contient déjà une partie du contenu dont vous aurez besoin pour ajouter d’autres fournisseurs d’identité. Pour configurer la connexion à l’aide de comptes Azure AD, [poursuivez ici](active-directory-b2c-setup-aad-custom.md).

## <a name="reference"></a>Référence

* Un **profil technique (PT)** est un élément qui définit le nom d’un point de terminaison, ses métadonnées et son protocole, et détaille l’échange de revendications que le moteur d’expérience d’identité doit effectuer.  La connexion par compte local est le profil technique utilisé par le moteur d’expérience d’identité pour effectuer une connexion par le biais d’un compte local.

