---
title: Comment configurer l&quot;authentification Azure Active Directory pour votre application App Services
description: "Découvrez comment configurer l&quot;authentification Azure Active Directory pour votre application App Services."
author: mattchenderson
services: app-service
documentationcenter: 
manager: adrianha
editor: 
ms.assetid: 6ec6a46c-bce4-47aa-b8a3-e133baef22eb
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 10/01/2016
ms.author: mahender
translationtype: Human Translation
ms.sourcegitcommit: cfe4957191ad5716f1086a1a332faf6a52406770
ms.openlocfilehash: 1f8813370995a7a55c9d87b8ec1b60ae0fe623f5
ms.lasthandoff: 03/09/2017


---
# <a name="how-to-configure-your-app-service-application-to-use-azure-active-directory-login"></a>Configurer votre application App Service pour utiliser la connexion Azure Active Directory
[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Cette rubrique montre comment configurer Azure App Services pour utiliser Azure Active Directory comme fournisseur d’authentification.

## <a name="express"> </a>Configuration d'Azure Active Directory à l'aide de la configuration rapide
1. Dans le [portail Azure], accédez à votre application. Cliquez sur **Paramètres**, puis sur **Authentification/Autorisation**.
2. Si la fonctionnalité Authentification / Autorisation n’est pas activée, positionnez le commutateur sur **On**.
3. Cliquez sur **Azure Active Directory**, puis sur **Rapide** sous **Mode de gestion**.
4. Cliquez sur **OK** pour inscrire l'application dans Azure Active Directory. Une nouvelle inscription est alors créée. Si vous choisissez une inscription existante à la place, cliquez sur **Sélectionner une application existante** et recherchez le nom d’une inscription précédemment créée au sein de votre locataire.
   Cliquez sur l'inscription pour la sélectionner, puis sur **OK**. Cliquez ensuite sur **OK** dans le panneau des paramètres Azure Active Directory.
   
   ![][0]
   
   Par défaut, App Service fournit une authentification, mais ne restreint pas l'accès autorisé à votre contenu et aux API de votre site. Vous devez autoriser les utilisateurs dans votre code d'application.
5. (Facultatif) Pour restreindre l’accès à votre site aux seuls utilisateurs authentifiés par Azure Active Directory, définissez **Action à exécuter quand une demande n’est pas authentifiée** sur **Se connecter avec Azure Active Directory**. Cela implique que toutes les demandes soient authentifiées. Toutes les demandes non authentifiées sont redirigées vers Azure Active Directory pour être authentifiées.
6. Cliquez sur **Enregistrer**.

Vous êtes maintenant prêt à utiliser Azure Active Directory pour l'authentification dans votre application.

## <a name="advanced"> </a>(Méthode alternative) Configurer manuellement Azure Active Directory avec des paramètres avancés
Vous pouvez également choisir de fournir des paramètres de configuration manuellement. Il s’agit de la solution préférée si le locataire AAD que vous voulez utiliser diffère de celui avec lequel vous vous connectez à Azure. Pour terminer la configuration, vous devez d’abord créer une inscription dans Azure Active Directory, puis fournir des informations d’inscription à App Service.

### <a name="register"> </a>Inscription de votre application auprès d’Azure Active Directory
1. Connectez-vous au [portail Azure]et accédez à votre application. Copiez votre **URL**. Elle vous permettra de configurer votre application Azure Active Directory.
2. Connectez-vous au [portail Azure Classic] et accédez à **Active Directory**.
   
    ![][2]
3. Sélectionnez votre annuaire, puis l’onglet **Applications** en haut de la page. Cliquez sur **AJOUTER** en bas de la page pour créer une inscription d’application.
4. Cliquez sur **Ajouter une application développée par mon organisation**.
5. Dans l’Assistant Ajout d’application, entrez un **Nom** pour votre application et cliquez sur le type **Application Web et/ou API Web**. Ensuite, cliquez pour continuer.
6. Dans la zone **URL DE CONNEXION** , collez l'URL de l'application que vous avez copiée précédemment. Entrez cette URL dans la zone **URI ID d’application** . Ensuite, cliquez pour continuer.
7. Une fois que l'application a été ajoutée, cliquez sur l'onglet **Configurer** . Remplacez **l’URL de réponse** sous **Authentification unique** par l’URL de votre application en y ajoutant le chemin d’accès */.auth/login/aad/callback*. Par exemple, `https://contoso.azurewebsites.net/.auth/login/aad/callback`. Assurez-vous d'utiliser le schéma HTTPS.
   
    ![][3]
8. Cliquez sur **Enregistrer**. Copiez ensuite l’ **ID client** pour l’application. Vous configurerez l’application pour utiliser cet ID plus tard.
9. Dans la barre de commandes située en bas, cliquez sur **Afficher les points de terminaison**, puis copiez l’URL du **document de métadonnées de fédération** et téléchargez ce document ou ouvrez-le dans un navigateur.
10. L’élément racine **EntityDescriptor** doit contenir un attribut **entityID** au format `https://sts.windows.net/` suivi d’un GUID propre à votre client (appelé « ID client »). Copiez cette valeur qui servira d' **URL de l'émetteur**. Vous configurerez l’application pour utiliser cet ID plus tard.

### <a name="secrets"> </a>Ajout d'informations Azure Active Directory à votre application
1. Revenez au [portail Azure]et accédez à votre application. Cliquez sur **Paramètres**, puis sur **Authentification/Autorisation**.
2. Si la fonctionnalité Authentification/Autorisation n’est pas activée, positionnez le commutateur sur **Activé**.
3. Cliquez sur **Azure Active Directory**, puis sur **Avancé** sous **Mode de gestion**. Collez-y les valeurs d’ID locataire et d’URL de l’émetteur que vous avez obtenues précédemment. Cliquez ensuite sur **OK**.
   
   ![][1]
   
   Par défaut, App Service fournit une authentification, mais ne restreint pas l'accès autorisé à votre contenu et aux API de votre site. Vous devez autoriser les utilisateurs dans votre code d'application.
4. (Facultatif) Pour restreindre l’accès à votre site aux seuls utilisateurs authentifiés par Azure Active Directory, définissez **Action à exécuter quand une demande n’est pas authentifiée** sur **Se connecter avec Azure Active Directory**. Cela implique que toutes les demandes soient authentifiées. Toutes les demandes non authentifiées sont redirigées vers Azure Active Directory pour être authentifiées.
5. Cliquez sur **Enregistrer**.

Vous êtes maintenant prêt à utiliser Azure Active Directory pour l'authentification dans votre application.

## <a name="optional-configure-a-native-client-application"></a>(Facultatif) Configurer une application cliente native
Azure Active Directory permet également d’inscrire les clients natifs, ce qui offre un contrôle accru du mappage d’autorisations. Cela est utile si vous souhaitez effectuer des connexions à l’aide d’une bibliothèque telle que **Active Directory Authentication Library**.

1. Accédez à **Active Directory** dans le [portail Azure Classic].
2. Sélectionnez votre annuaire, puis l’onglet **Applications** en haut de la page. Cliquez sur **AJOUTER** en bas de la page pour créer une inscription d’application.
3. Cliquez sur **Ajouter une application développée par mon organisation**.
4. Dans l’Assistant Ajout d’application, entrez un **Nom** pour votre application et cliquez sur le type **Application cliente native**. Ensuite, cliquez pour continuer.
5. Dans la zone **URI de redirection**, entrez le point de terminaison */.auth/login/done* de votre site à l’aide du modèle HTTPS. Cette valeur doit être semblable à *https://contoso.azurewebsites.net/.auth/login/done*. Si vous créez une application Windows, utilisez plutôt le [SID de package](app-service-mobile-dotnet-how-to-use-client-library.md#package-sid) en tant qu’URI.
6. Une fois l’application native ajoutée, cliquez sur l’onglet **Configurer** . Recherchez l’ **ID client** et notez-en la valeur.
7. Faites défiler la page vers le bas jusqu’à la section **Autorisations pour d’autres applications** et cliquez sur **Ajouter une application**.
8. Recherchez l’application web que vous avez inscrite précédemment et cliquez sur l’icône plus, puis sur la coche pour fermer la boîte de dialogue. Si l’application web est introuvable, recherchez son inscription et ajoutez une nouvelle URL de réponse (par exemple, la version HTTP de votre URL en cours). Cliquez sur Enregistrer, puis répétez ces étapes ; l’application doit apparaître dans la liste.
9. Dans la nouvelle entrée que vous venez d’ajouter, ouvrez la liste déroulante **Autorisations déléguées** et sélectionnez **Accès (appName)**. Cliquez ensuite sur **Enregistrer**.

Vous avez maintenant configuré une application cliente native qui peut accéder à votre application App Service.

## <a name="related-content"> </a>Contenu connexe
[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/mobile-app-aad-express-settings.png
[1]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/mobile-app-aad-advanced-settings.png
[2]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-navigate-aad.png
[3]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-aad-app-configure.png

<!-- URLs. -->

[portail Azure]: https://portal.azure.com/
[portail Azure Classic]: https://manage.windowsazure.com/
[alternative method]:#advanced

