---
title: "Azure Active Directory B2C : personnalisation de l’interface utilisateur avec des stratégies personnalisées | Microsoft Docs"
description: "Une rubrique sur la personnalisation de l’interface utilisateur lors de l’utilisation de stratégies personnalisées dans Azure AD B2C"
services: active-directory-b2c
documentationcenter: 
author: saeeda
manager: krassk
editor: gsacavdm
ms.assetid: 658c597e-3787-465e-b377-26aebc94e46d
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 04/04/2017
ms.author: saeeda
ms.translationtype: Human Translation
ms.sourcegitcommit: 125f05f5dce5a0e4127348de5b280f06c3491d84
ms.openlocfilehash: 9de6a57671cf65c4aa5d8695d21e0932175b1183
ms.contentlocale: fr-fr
ms.lasthandoff: 05/22/2017

---
# <a name="azure-active-directory-b2c-ui-customization-in-a-custom-policy"></a>Azure Active Directory B2C : personnalisation de l’interface utilisateur dans une stratégie personnalisée

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Après avoir suivi cet article, vous disposerez d’une stratégie personnalisée d’inscription et de connexion avec votre marque et votre apparence.  Azure AD B2C permet le contrôle quasi complet du HTML et CSS présentés à l’utilisateur final.  Lorsque vous utilisez une stratégie personnalisée, la personnalisation de l’interface utilisateur est configurée dans le XML au lieu d’utiliser des contrôles dans le portail Azure.

## <a name="prerequisites"></a>Composants requis

Avant de poursuivre, vous suivre [Prise en main des stratégies personnalisées](active-directory-b2c-get-started-custom.md).  Vous devez disposer d’une stratégie personnalisée fonctionnelle pour l’inscription et la connexion avec des comptes locaux.

## <a name="the-page-ui-customization-feature"></a>La fonctionnalité de personnalisation de l’interface utilisateur de page

Avec la fonctionnalité de personnalisation de l’interface utilisateur de page, vous pouvez personnaliser l’apparence d’une stratégie personnalisée.  Conservez la marque et la cohérence visuelle entre votre application et Azure AD B2C.

Voici comment cela fonctionne : Azure AD B2C exécute le code dans l’explorateur client et utilise une approche moderne appelée [partage de ressources cross-origin (CORS)](http://www.w3.org/TR/cors/).  Tout d’abord, vous spécifiez une URL dans la stratégie personnalisée avec du contenu HTML personnalisé.  Azure AD B2C fusionne des éléments de l’interface utilisateur avec le contenu HTML chargé depuis votre URL, et affiche la page pour le consommateur.

## <a name="creating-your-html5-content"></a>Création de votre contenu HTML5

Nous allons créer du contenu HTML avec le nom de la marque de votre produit dans le titre.

1. Cliquez sur **Copier** sur cet extrait de code HTML.  Il s’agit de HTML5 bien formé avec un élément vide appelé `<div id="api"></div>` situé quelque part dans le `<body>`. Cet élément marque l’endroit où le contenu Azure AD B2C est inséré.

   ```html
   <!DOCTYPE html>
   <html>
   <head>
       <title>My Product Brand Name</title>
   </head>
   <body>
       <div id="api"></div>
   </body>
   </html>
   ```

1. Collez dans un éditeur de texte et enregistrez le fichier sous `customize-ui.html`

## <a name="create-a-blob-storage-account"></a>Création d’un compte de stockage d’objets blob

>[!NOTE]
> Dans ce guide, nous utilisons le stockage Blob Azure pour héberger notre contenu.  Vous pouvez également héberger votre contenu sur un serveur web, mais il est nécessaire que vous [activiez le partage des ressources cross-origin (CORS) sur votre serveur web](https://enable-cors.org/server.html).

Nous allons héberger ce code HTML sur le stockage Blob Azure.

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Dans le menu Hub, sélectionnez **Nouveau** -> **Stockage** -> **Compte de stockage**.
1. Entrez un **nom** unique pour votre compte de stockage.
1. Le **Modèle de déploiement** peut être laissé sur **Gestionnaire de ressources**.
1. Modifiez **Type de compte** sur **Stockage Blob**.
1. Vous pouvez laisser **Performances** sur **Standard**.
1. Vous pouvez laisser **Réplication** sur **RA-GRS**.
1. Vous pouvez laisser **Niveau d’accès** sur **À chaud**.
1. Vous pouvez laisser **Chiffrement du service de stockage** sur **Désactivé**.
1. Sélectionnez **l’abonnement** de votre compte de stockage.
1. **Créez un groupe de ressources** ou sélectionnez-en un.
1. Sélectionnez **l’emplacement géographique** de votre compte de stockage.
1. Cliquez sur **Créer** pour créer le compte de stockage.
1. Attendez la fin du déploiement et le panneau du compte de stockage s’ouvre automatiquement.

## <a name="create-a-container"></a>Création d’un conteneur

Nous allons créer un conteneur public sur le stockage Blob Azure.

1. Basculez vers l’onglet gauche appelé **Vue d’ensemble**.
1. Cliquez sur **+ conteneur**.
1. Pour **Nom**, saisissez `$root`
1. Définissez **Type d’accès** sur **Blob**.
1. Cliquez sur ’$root’ pour ouvrir le nouveau conteneur.
1. Cliquez sur **Télécharger**.
1. Cliquez sur l’icône de dossier en regard de `Select a file`.
1. Accédez à `customize-ui.html`, que nous avons créé dans [la section précédente](#the-page-ui-customization-feature).
1. Cliquez sur **Télécharger**.
1. Sélectionnez l’objet blob appelé `customize-ui.html` que nous avons téléchargé.
1. En regard de **URL**, cliquez sur le bouton Copier.
1. Ouvrez un navigateur et accédez à cette URL.  S’il est inaccessible, assurez-vous que le type d’accès du conteneur est défini sur blob.

## <a name="configure-cors"></a>Configuration de CORS

Ensuite nous configurons le stockage Blob Azure pour le partage des ressources cross-origin (CORS).

>[!NOTE]
>Vous souhaitez tester la fonctionnalité de personnalisation de l’interface utilisateur à l’aide de notre exemple de code HTML et de contenu CSS ?  Nous vous avons fourni [un outil simple d’assistance](active-directory-b2c-reference-ui-customization-helper-tool.md) qui télécharge et configure notre exemple de contenu sur votre compte de stockage Blob Azure.  Si vous utilisez l’outil, passez directement à [Modifier votre stratégie personnalisée d’inscription ou de connexion](#modify-your-sign-up-or-sign-in-custom-policy)

1. Dans le panneau de stockage, sous Paramètres, ouvrez **CORS**.
1. Cliquez sur **+ Ajouter**.
1. Réglez `Allowed origins` sur `*`.
1. Dans la liste déroulante appelée `Allowed verbs`, sélectionnez à la fois `GET` et `OPTIONS`.
1. Réglez `Allowed headers` sur `*`.
1. Réglez `Exposed headers` sur `*`.
1. Réglez `Maximum age (seconds)` sur `200`.
1. Cliquez sur **Add**.

## <a name="testing-cors"></a>Test de CORS

Nous allons confirmer que nous sommes prêts.

1. Accédez à http://test-cors.org/ et collez l’URL dans le champ `Remote URL`.
1. Cliquez sur **Envoyer la demande**
1. Si vous recevez une erreur, assurez-vous que vos [paramètres CORS](#configure-cors) sont corrects.  Vous serez peut-être amené à effacer le cache de votre navigateur ou essayer une session de navigation privée avec `CTRL-SHIFT-P`.

## <a name="modify-your-sign-up-or-sign-in-custom-policy"></a>Modifier votre stratégie personnalisée d’inscription ou de connexion

1. Sous la balise `<TrustFrameworkPolicy>` de niveau supérieur, vous devriez trouver la balise `<BuildingBlocks>`.  À l’intérieur de la balise `<BuildingBlocks>`, ajoutez une balise `ContentDefinitions` en copiant cet exemple.  Remplacez `{your_storage_account}` par le nom de votre compte de stockage.

  ```xml
  <BuildingBlocks>
    <ContentDefinitions>
      <ContentDefinition Id="api.idpselections">
        <LoadUri>https://{your_storage_account}.blob.core.windows.net/customize-ui.html</LoadUri>
      </ContentDefinition>
    </ContentDefinitions>
  </BuildingBlocks>
  ```

## <a name="upload-your-updated-custom-policy"></a>Téléchargement de votre stratégie personnalisée mise à jour

1. Dans le [portail Azure](https://portal.azure.com), [basculez dans le contexte de votre client Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md) et ouvrez le panneau Azure AD B2C.
1. Cliquez sur **Toutes les stratégies**.
1. Cliquez sur **Télécharger stratégie**
1. Téléchargez `SignUpOrSignin.xml` avec la balise `ContentDefinitions` que nous avons ajoutée.

## <a name="test-the-custom-policy-using-run-now"></a>Testez la stratégie personnalisée à l’aide de « Exécuter maintenant »

1. Ouvrez le **panneau Azure AD B2C** et accédez à **Toutes les stratégies**.
1. Sélectionnez la stratégie personnalisée que vous avez téléchargée, puis cliquez sur le bouton **Exécuter maintenant**.
1. Vous devriez pouvoir vous inscrire à l’aide d’une adresse de messagerie.

## <a name="next-steps"></a>Étapes suivantes

Ce [guide de référence de personnalisation de l’interface utilisateur pour les stratégies intégrées](active-directory-b2c-reference-ui-customization.md) contient des informations supplémentaires sur les éléments d’interface utilisateur qui peuvent être personnalisés.  Il n’existe aucune différence de personnalisation de l’interface entre les stratégies intégrées et les stratégies personnalisées.

