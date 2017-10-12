---
title: Comment configurer l'authentification Facebook pour votre application App Services
description: "Découvrez comment configurer l'authentification Facebook pour votre application App Services."
services: app-service
documentationcenter: 
author: mattchenderson
manager: syntaxc4
editor: 
ms.assetid: b6b4f062-fcb4-47b3-b75a-ec4cb51a62fd
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 10/01/2016
ms.author: mahender
ms.openlocfilehash: c1b4c91d384c56c4f55bf8d31ced250f51c0d837
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-configure-your-app-service-application-to-use-facebook-login"></a>Comment configurer votre application App Service de manière à utiliser la connexion via Facebook
[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Cette rubrique montre comment configurer Azure App Service pour utiliser Facebook comme fournisseur d'authentification.

Pour effectuer la procédure de cette rubrique, vous devez disposer d'un compte Facebook avec une adresse de messagerie vérifiée et un numéro de téléphone mobile. Pour créer un compte Facebook, allez sur [facebook.com].

## <a name="register"></a>Inscription de votre application sur Facebook
1. Connectez-vous au [portail Azure]et accédez à votre application. Copiez votre **URL**. Vous l’utiliserez pour configurer votre application Facebook.
2. Dans une autre fenêtre de navigateur, accédez au site Web [Développeurs Facebook] et connectez-vous à l’aide des informations d’identification de votre compte Facebook.
3. (Facultatif) Si vous n’êtes pas encore inscrit, cliquez sur **Applications** > **S’inscrire en tant que développeur**, acceptez la politique et suivez la procédure d’inscription.
4. Cliquez sur **Mes applications** > **Ajouter une nouvelle application** > **Site Web** > **Ignorer et créer un ID d’application**. 
5. Dans **Nom d’affichage**, saisissez un nom unique pour votre application, saisissez votre **adresse de messagerie de contact**, choisissez une **catégorie** pour votre application, puis cliquez sur **Create App ID** (Créer ID application) et effectuez la vérification de sécurité. Vous serez alors redirigé vers le tableau de bord du développeur pour votre nouvelle application Facebook.
6. Sous « Facebook Login » (Connexion Facebook), cliquez sur **Get Started**(Prise en main). Ajoutez **l’URI de redirection** de votre application dans les **URI de redirection Valid OAuth**, puis cliquez sur **Enregistrer les modifications**. 
   
   > [!NOTE]
   > Votre URI de redirection correspond à l’URL de votre application suivie du chemin d’accès, */.auth/login/facebook/callback*. Par exemple : `https://contoso.azurewebsites.net/.auth/login/facebook/callback`. Assurez-vous d'utiliser le schéma HTTPS.
   > 
   > 
7. Dans le volet de navigation gauche, cliquez sur **Paramètres**. Dans le champ **Question secrète de l’application**, cliquez sur **Afficher**, fournissez votre mot de passe si nécessaire, puis notez les valeurs **ID de l’application** et **Question secrète de l’application**. Vous les utiliserez plus tard pour configurer l’application dans Azure.
   
   > [!IMPORTANT]
   > La clé secrète de l'application est une information d'identification de sécurité importante. Ne partagez cette clé secrète avec personne et ne la distribuez pas dans une application cliente.
   > 
   > 
8. Le compte Facebook qui a été utilisé pour inscrire l'application est un administrateur de l'application. À ce stade, seuls les administrateurs peuvent se connecter à cette application. Pour authentifier d’autres comptes Facebook, cliquez sur **Révision de l’application** et activez **Rendre public <nom-de-votre-application>** pour activer l’accès public général à l’aide de l’authentification Facebook.

## <a name="secrets"></a>Ajout des informations Facebook à votre application
1. Revenez au [portail Azure]et accédez à votre application. Cliquez sur **Paramètres** > **Authentification / Autorisation**, et vérifiez que **l’authentification App Service** est activée, sur **On**.
2. Cliquez sur **Facebook**, collez les valeurs correspondant à l’ID et à la question secrète de l’application que vous avez obtenues précédemment et activez éventuellement les étendues nécessaires à votre application, puis cliquez sur **OK**.
   
    ![][0]
   
    Par défaut, App Service fournit une authentification, mais ne restreint pas l'accès autorisé à votre contenu et aux API de votre site. Vous devez autoriser les utilisateurs dans votre code d'application.
3. (Facultatif) Pour restreindre l’accès à votre site aux seuls utilisateurs authentifiés par Facebook, définissez **Action à exécuter quand une demande n’est pas authentifiée** sur **Facebook**. Cela implique que toutes les demandes soient authentifiées. Toutes les demandes non authentifiées sont redirigées vers Facebook pour être authentifiées.
4. Lorsque vous avez terminé de configurer l’authentification, cliquez sur **Enregistrer**.

Vous êtes maintenant prêt à utiliser Facebook pour l'authentification dans votre application.

## <a name="related-content"></a>Contenu connexe
[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->
[0]: ./media/app-service-mobile-how-to-configure-facebook-authentication/mobile-app-facebook-settings.png

<!-- URLs. -->
[Développeurs Facebook]: http://go.microsoft.com/fwlink/p/?LinkId=268286
[facebook.com]: http://go.microsoft.com/fwlink/p/?LinkId=268285
[Get started with authentication]: /en-us/develop/mobile/tutorials/get-started-with-users-dotnet/
[portail Azure]: https://portal.azure.com/
