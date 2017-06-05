---
title: "Azure Active Directory B2C : bien démarrer avec les stratégies personnalisées | Microsoft Docs"
description: "Bien démarrer avec les stratégies personnalisées Azure Active Directory B2C"
services: active-directory-b2c
documentationcenter: 
author: rojasja
manager: krassk
editor: rojasja
ms.assetid: 658c597e-3787-465e-b377-26aebc94e46d
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 04/04/2017
ms.author: joroja;parahk;gsacavdm
ms.translationtype: Human Translation
ms.sourcegitcommit: fc4172b27b93a49c613eb915252895e845b96892
ms.openlocfilehash: 42824fe10e635257681f62ab1fec9b47abd4294a
ms.contentlocale: fr-fr
ms.lasthandoff: 05/12/2017

---
# <a name="azure-active-directory-b2c-getting-started-with-custom-policies"></a>Azure Active Directory B2C : bien démarrer avec les stratégies personnalisées

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Une fois que vous aurez suivi la procédure de cet article, votre stratégie personnalisée prendra en charge l’inscription ou la connexion de « comptes locaux » avec une adresse électronique et un mot de passe. Vous préparerez également votre environnement à l’ajout de fournisseurs d’identité supplémentaires (par exemple, Facebook ou Azure AD).  Nous vous recommandons de suivre ces étapes avant de vous familiariser avec toutes les autres utilisations de l’infrastructure d’expérience d’identité d’Azure AD B2C et de nombreux concepts essentiels.

## <a name="prerequisites"></a>Composants requis

Avant de poursuivre, vérifiez que vous disposez d’un client Azure AD B2C. Un client Azure AD B2C est un conteneur comprenant tous vos utilisateurs, applications, stratégies, etc. Si vous n’en avez pas, vous devez en [créer un](active-directory-b2c-get-started.md). Nous invitons tous les développeurs à suivre les procédures de stratégie intégrée Azure AD B2C et à configurer leur application avec des stratégies intégrées avant de continuer.  Vos applications fonctionneront avec les deux types de stratégies grâce à une simple modification du nom de la stratégie pour appeler la stratégie personnalisée.

L’accès à la modification de stratégie personnalisée requiert un abonnement Azure valide lié à votre client.

## <a name="add-signing-and-encryption-keys-to-your-b2c-tenant-for-use-by-custom-policies"></a>Ajouter à votre client B2C des clés de signature et de chiffrement utilisables par les stratégies personnalisées

1. Accédez au panneau **Identity Experience Framework** (Infrastructure d’expérience d’identité) dans les paramètres de votre client Azure AD B2C.
2. Sélectionnez **Clés de stratégie** pour afficher les clés disponibles dans votre client.
3. Créez `B2C_1A_TokenSigningKeyContainer` s’il n’existe pas :
 * Cliquez sur **Ajouter**.
 * Options > `Generate`.
 * Nom >`TokenSigningKeyContainer`. Le préfixe B2C_1A_ peut être ajouté automatiquement.
 * Type de clé > `RSA`.
 * Dates - Utilisez les valeurs par défaut.
 * Utilisation de la clé > `Signature`.
 * Cliquez sur **Créer**
4. Créez `B2C_1A_TokenEncryptionKeyContainer` s’il n’existe pas :
 * Cliquez sur **Ajouter**.
 * Options > `Generate`.
 * Nom >`TokenEncryptionKeyContainer`. Le préfixe B2C_1A_ peut être ajouté automatiquement.
 * Type de clé > `RSA`.
 * Dates - Utilisez les valeurs par défaut.
 * Utilisation de la clé > `Encryption`.
 * Cliquez sur **Créer**
5. Créez `B2C_1A_FacebookSecret`. Si vous disposez déjà d’un secret pour l’application Facebook, ajoutez-le en tant que clé de stratégie à votre client.  Dans le cas contraire, vous devez créer la clé avec une valeur d’espace réservé afin que vos stratégies puissent être validées.
 * Cliquez sur **Ajouter**.
 * Options > `Manual`.
 * Nom > `FacebookSecret`. Le préfixe B2C_1A_ peut être ajouté automatiquement.
 * Secret > entrez votre FacebookSecret à partir de developers.facebook.com ou entrez « 0 » comme espace réservé. *Ce n’est pas votre ID d’application Facebook.*
 * Utilisation de la clé > Signature.
 * Cliquez sur **Créer** et confirmez la création.

## <a name="register-identity-experience-framework-applications"></a>Inscrire les applications de l’infrastructure d’expérience d’identité

Avec Azure AD B2C, vous devez inscrire deux applications supplémentaires : elles sont utilisées par le moteur pour inscrire et connecter les utilisateurs.

>[!NOTE]
>Nous devons créer deux applications permettant de se connecter à l’aide de comptes locaux : IdentityExperienceFramework (une application web) et ProxyIdentityExperienceFramework (une application native) avec des autorisations déléguées de l’application IdentityExperienceFramework. Les comptes locaux existent uniquement dans votre client. Vos utilisateurs finaux se connectent avec une combinaison adresse e-mail/mot de passe unique pour accéder à vos applications inscrites auprès du client.

### <a name="create-the-identityexperienceframework-application"></a>Créer l’application IdentityExperienceFramework

1. Sur le [Portail Azure](https://portal.azure.com), [basculez dans le contexte de votre client Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md).
1. Ouvrez le panneau `Azure Active Directory` (et non le panneau Azure AD B2C). Vous devrez peut-être cliquer sur **> Plus de services** pour le trouver.
1. Sélectionnez **Inscriptions d’applications**.
1. Cliquez sur **+ Inscription d’une nouvelle application**.
   * Nom : `IdentityExperienceFramework`
   * Type d’application : `Web app/API`
   * URL de connexion : `https://login.microsoftonline.com/yourtenant.onmicrosoft.com`, où `yourtenant` est le nom de domaine de votre client Azure AD B2C.
1. Cliquez sur **Create**.
1. Après la création, sélectionnez la nouvelle application `IdentityExperienceFramework`, cliquez sur **Propriétés**, puis copiez l’ID d’application et enregistrez-le en vue d’une utilisation future.

### <a name="create-the-proxy-identity-experience-framework-application"></a>Créer l’application d’infrastructure d’expérience d’identité de proxy

1. Sélectionnez **Inscriptions d’applications**.
1. Cliquez sur **+ Inscription d’une nouvelle application**.
   * Nom : `ProxyIdentityExperienceFramework`
   * Type d’application : `Native`
   * URL d’ouverture de session : `https://login.microsoftonline.com/yourtenant.onmicrosoft.com`, où `yourtenant` est votre client Azure AD B2C.
1. Cliquez sur **Create**.
1. Après la création, sélectionnez l’application `ProxyIdentityExperienceFramework`, cliquez sur **Propriétés**, puis copiez l’ID d’application et enregistrez-le en vue d’une utilisation future.
1. Sélectionnez **Autorisations requises**, puis **+ Ajouter** et enfin **Sélectionner une API**.
1. Recherchez le nom `IdentityExperienceFramework`, sélectionnez IdentityExperienceFramework dans les résultats, puis cliquez sur **Sélectionner**.
1. Cochez la case `Access IdentityExperienceFramework`, puis cliquez sur **Sélectionner**.
1. Cliquez sur **Done**.
1. Cliquez sur **Accorder des autorisations**, puis confirmez en cliquant sur **Oui**.

## <a name="download-starter-pack-and-modify-policies"></a>Télécharger le pack de démarrage et modifier les stratégies

Les stratégies personnalisées sont un ensemble de fichiers XML qui doivent être chargés sur votre client Azure AD B2C. Nous proposons des packs de démarrage pour vous permettre d’être rapidement opérationnel. Chacun des packs de démarrage ci-dessous comporte le nombre minimum de profils techniques et de parcours utilisateur nécessaires pour réaliser les scénarios décrits :
 * LocalAccounts : vous permet d’utiliser uniquement les comptes locaux.
 * SocialAccounts : vous permet d’utiliser uniquement les comptes de réseaux sociaux (ou fédérés).
 * **SocialAndLocalAccounts** : c’est celui que nous allons utiliser dans le cadre de cette procédure.
 * SocialAndLocalAccountsWithMFA : des options MFA, de comptes locaux et de comptes de réseaux sociaux sont incluses dans celui-ci.

Chaque pack de démarrage contient :

* Le [fichier de base](active-directory-b2c-overview-custom.md#policy-files) de la stratégie. Il n’y a que peu de modifications à apporter la base.
* Le [fichier d’extension](active-directory-b2c-overview-custom.md#policy-files) de la stratégie.  C’est sur ce fichier que portent la plupart des modifications de la configuration.
* [Les fichiers de partie de confiance](active-directory-b2c-overview-custom.md#policy-files). Il s’agit de fichiers spécifiques de tâches appelés par votre application.

>[!NOTE]
>Si votre éditeur XML prend en charge la validation, vous pouvez valider la conformité des fichiers au fichier de schéma XML `TrustFrameworkPolicy_0.3.0.0.xsd`, qui se trouve dans le dossier racine du pack de démarrage. La validation du schéma XML identifie les erreurs avant le chargement.

C’est parti :

1. Téléchargez le pack de démarrage « active-directory-b2c-custom-policy-starterpack » à partir de GitHub.  [Téléchargez le fichier zip](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip) ou exécutez :

    ```console
    git clone https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack
    ```
2. Ouvrez le dossier `SocialAndLocalAccounts`.  Le fichier de base (`TrustFrameworkBase.xml`) de ce dossier comporte le contenu nécessaire aux comptes locaux et sociaux / d’entreprise. Le contenu social n’interfère pas avec les étapes d’opérationnalisation des comptes locaux.
3. Ouvrez `TrustFrameworkBase.xml`.  Si vous avez besoin d’un éditeur XML, [essayez Visual Studio Code](https://code.visualstudio.com/download), un éditeur multiplatformes léger.
4. Dans l’élément `TrustFrameworkPolicy` racine, mettez à jour les attributs `TenantId` et `PublicPolicyUri` en remplaçant `yourtenant.onmicrosoft.com` par le nom de domaine de votre client Azure AD B2C :

    ```xml
    <TrustFrameworkPolicy
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:xsd="http://www.w3.org/2001/XMLSchema"
    xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
    PolicySchemaVersion="0.3.0.0"
    TenantId="yourtenant.onmicrosoft.com"
    PolicyId="B2C_1A_TrustFrameworkBase"
    PublicPolicyUri="http://yourtenant.onmicrosoft.com">
    ```
>[!NOTE]
>`PolicyId` est le nom de la stratégie qui s’affiche dans le portail et le nom par lequel ce fichier de stratégie est référencé par d’autres fichiers de stratégie.

5. Enregistrez le fichier.
6. Ouvrez `TrustFrameworkExtensions.xml` et apportez ces deux mêmes modifications en remplaçant `yourtenant.onmicrosoft.com` par votre client Azure AD B2C. Effectuez le même remplacement dans l’élément `<TenantId>`, avec 3 modifications au total.  Enregistrez le fichier.
7. Ouvrez `SignUpOrSignIn.xml` et apportez les mêmes modifications en remplaçant `yourtenant.onmicrosoft.com` par votre client Azure AD B2C à trois endroits. Enregistrez le fichier.
8. Ouvrez la réinitialisation de mot de passe et les fichiers de modification de profil et apportez les mêmes modifications en remplaçant `yourtenant.onmicrosoft.com` par votre client Azure AD B2C à trois endroits de chaque fichier. Enregistrez les fichiers.

### <a name="add-the-application-ids-to-your-custom-policy"></a>Ajouter les ID d’application à votre stratégie personnalisée
Ajoutez les ID d’application au fichier d’extensions (`TrustFrameworkExtensions.xml`).

1. Dans le fichier d’extensions (`TrustFrameworkExtensions.xml`), recherchez l’élément `<TechnicalProfile Id="login-NonInteractive">`.
2. Remplacez les deux instances de `IdentityExperienceFrameworkAppId` par l’ID de l’application d’infrastructure d’expérience d’identité que vous avez créée précédemment. Voici un exemple :

```xml
<Item Key="client_id">8322dedc-cbf4-43bc-8bb6-141d16f0f489</Item>
```

3. Remplacez les deux instances de `ProxyIdentityExperienceFrameworkAppId` par l’ID de l’application d’infrastructure d’expérience d’identité de proxy que vous avez créée précédemment.
4. Enregistrez votre fichier d’extensions.

## <a name="upload-the-policies-to-your-tenant"></a>Charger les stratégies vers votre client

1. Sur le [Portail Azure](https://portal.azure.com), [basculez dans le contexte de votre client Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md) et ouvrez le panneau Azure AD B2C.
2. Cliquez sur **Identity Experience Framework** (Infrastructure d’expérience d’identité).
3. Sélectionnez **Charger une stratégie** pour charger des fichiers de stratégie.

    >[!WARNING]
    >Les fichiers de stratégie personnalisée doivent être chargés dans l’ordre suivant :

1. Chargez `TrustFrameworkBase.xml`.
2. Chargez `TrustFrameworkExtensions.xml`.
3. Chargez `SignUpOrSignin.xml`.
4. Chargez vos autres fichiers de stratégie.

Lorsqu’un fichier est chargé, son nom est précédé de `B2C_1A_`.

## <a name="test-the-custom-policy-using-run-now"></a>Tester la stratégie personnalisée avec « Exécuter maintenant »

1. Ouvrez **Paramètres Azure AD B2C** et accédez à **Identity Experience Framework** (Infrastructure d’expérience d’identité).

>[!NOTE]
>**Exécuter maintenant** : pour utiliser cette option, une application minimum doit être préinscrite auprès du client. Les applications doivent être inscrites auprès du client B2C à l’aide de la sélection de menu **Applications** dans B2C ou dans l’infrastructure d’expérience d’identité pour appeler des stratégies intégrées et personnalisées. Seule une inscription par application est nécessaire. 

Découvrez comment inscrire des applications dans les articles [Prise en main](active-directory-b2c-get-started.md) et [Inscription des applications](active-directory-b2c-app-registration.md) d’Azure AD B2C.  

2. Ouvrez la stratégie personnalisée de partie de confiance que vous avez téléchargée `B2C_1A_signup_signin`, puis cliquez sur le bouton **Exécuter maintenant**.


3. Vous devriez pouvoir vous inscrire à l’aide d’une adresse de messagerie.
4. Connectez-vous avec le même compte pour confirmer que la configuration est correcte.

>[!NOTE]
>Souvent, la mauvaise configuration de l’application IdentityExperienceFramework est à l’origine des échecs de connexion.


## <a name="next-steps"></a>Étapes suivantes

### <a name="add-facebook-as-an-identity-provider"></a>Ajouter Facebook en tant que fournisseur d’identité
Pour configurer Facebook :
1. [Configurez une application Facebook dans developers.facebook.com](active-directory-b2c-setup-fb-app.md).
2. [Ajoutez le secret d’application Facebook à votre client Azure AD B2C](#add-signing-and-encryption-keys-to-your-b2c-tenant-for-use-by-custom-policies).
3. Ajoutez l’ID de l’application Facebook au fichier de stratégie TrustFrameworkExtensions dans `Item Key="client_id"` :

```xml
<TechnicalProfile Id="Facebook-OAUTH">
  <Metadata>
  <!--Replace the value of client_id in this technical profile with your the Facebook App ID"-->
    <Item Key="client_id">00000000000000</Item>
```
4. Chargez le fichier de stratégie TrustFrameworkExtensions.xml sur votre client.
5. Effectuez un test à l’aide de l’option **Exécuter maintenant** ou appelez la stratégie directement à partir de votre application inscrite.

### <a name="add-azure-active-directory-as-an-identity-provider"></a>Ajouter Azure Active Directory en tant que fournisseur d’identité
Le fichier de base que nous avons utilisé dans ce guide de démarrage contient déjà une partie du contenu dont vous aurez besoin pour ajouter d’autres fournisseurs d’identité. Pour configurer la connexion à l’aide de comptes Azure AD, [poursuivez ici](active-directory-b2c-setup-aad-custom.md).


## <a name="reference"></a>Référence

[Vue d’ensemble](active-directory-b2c-overview-custom.md) des stratégies personnalisées dans Azure AD B2C à l’aide de l’infrastructure d’expérience d’identité

