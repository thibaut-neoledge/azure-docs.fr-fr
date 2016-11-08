---
title: Ajouter l’authentification à une application Azure Mobile Services existante (iOS) | Microsoft Docs
description: Apprenez à utiliser Mobile Services pour authentifier les utilisateurs de votre application iOS par l'intermédiaire de divers fournisseurs d'identité, notamment Google, Facebook, Twitter et Microsoft.
services: mobile-services
documentationcenter: ios
author: krisragh
manager: erikre
editor: ''

ms.service: mobile-services
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 07/21/2016
ms.author: krisragh

---
# Ajout de l'authentification à une application Azure Mobile Services existante
[!INCLUDE [mobile-services-selector-get-started-users](../../includes/mobile-services-selector-get-started-users.md)]

&nbsp;

[!INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

> Pour la version Mobile Apps équivalente de cette rubrique, consultez [Ajout de l’authentification à votre application iOS](../app-service-mobile/app-service-mobile-ios-get-started-users.md).
> 
> 

Dans ce didacticiel, vous allez ajouter l'authentification au projet de démarrage rapide en faisant appel à un fournisseur d'identité pris en charge. Ce didacticiel est basé sur le didacticiel [Démarrage rapide de Mobile Services], que vous devez effectuer en premier.

## <a name="register"></a>Inscription de l'application pour l'authentification et configuration de Mobile Services
[!INCLUDE [mobile-services-register-authentication](../../includes/mobile-services-register-authentication.md)]

[!INCLUDE [mobile-services-dotnet-backend-aad-server-extension](../../includes/mobile-services-dotnet-backend-aad-server-extension.md)]

## <a name="permissions"></a>Restriction des autorisations pour les utilisateurs authentifiés
[!INCLUDE [mobile-services-restrict-permissions-dotnet-backend](../../includes/mobile-services-restrict-permissions-dotnet-backend.md)]

Dans Xcode, ouvrez le projet. Appuyez sur le bouton **Exécuter** pour démarrer l’application. Vérifiez qu'une exception avec le code d'état 401 (Non autorisé) est générée après le démarrage de l'application. Cela se produit, car l'application essaye d'accéder à Mobile Services en tant qu'utilisateur non authentifié, mais la table *TodoItem* requiert désormais l'authentification.

## <a name="add-authentication"></a>Ajout de l'authentification à l'application
[!INCLUDE [mobile-services-ios-authenticate-app](../../includes/mobile-services-ios-authenticate-app.md)]

## <a name="store-authentication"></a>Stockage des jetons d'authentification dans l'application
[!INCLUDE [mobile-services-ios-authenticate-app-with-token](../../includes/mobile-services-ios-authenticate-app-with-token.md)]

## <a name="next-steps"></a>Étapes suivantes
Dans le didacticiel suivant, [Autorisation côté service des utilisateurs Mobile Services], vous utiliserez la valeur d'ID utilisateur pour filtrer les données retournées.

<!-- Anchors. -->
[Register your app for authentication and configure Mobile Services]: #register
[Restrict table permissions to authenticated users]: #permissions
[Add authentication to the app]: #add-authentication
[Next Steps]: #next-steps
[Storing authentication tokens in your app]: #store-authentication

<!-- URLs. -->
[Autorisation côté service des utilisateurs Mobile Services]: mobile-services-dotnet-backend-service-side-authorization.md
[Démarrage rapide de Mobile Services]: mobile-services-dotnet-backend-ios-get-started.md
[Get started with authentication]: mobile-services-dotnet-backend-ios-get-started-users.md
[Get started with push notifications]: mobile-services-dotnet-backend-ios-get-started-push.md
[Authorize users with scripts]: mobile-services-dotnet-backend-service-side-authorization.md
[Mobile Services .NET How-to Conceptual Reference]: /develop/mobile/how-to-guides/work-with-net-client-library
[Register your Windows Store app package for Microsoft authentication]: ../mobile-services-how-to-register-store-app-package-microsoft-authentication.md

<!---HONumber=AcomDC_0727_2016-->