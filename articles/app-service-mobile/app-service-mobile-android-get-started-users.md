---
title: "Ajout de l’authentification à Android à l’aide de Mobile Apps | Microsoft Docs"
description: "Découvrez comment utiliser Mobile Apps dans Azure App Service pour authentifier les utilisateurs de votre application Android via divers fournisseurs d’identité, notamment Google, Facebook, Twitter et Microsoft."
services: app-service\mobile
documentationcenter: android
author: ysxu
manager: erikre
editor: 
ms.assetid: 1fc8e7c1-6c3c-40f4-9967-9cf5e21fc4e1
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: article
ms.date: 10/01/2016
ms.author: yuaxu
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 3caf6edb1e3a33a451ec8773b1e14fe0c95734a1


---
# <a name="add-authentication-to-your-android-app"></a>Ajout de l’authentification à votre application Android
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

## <a name="summary"></a>Résumé
Dans ce didacticiel, vous allez ajouter l’authentification au projet de démarrage rapide todolist sur Android en faisant appel à un fournisseur d’identité pris en charge. Ce didacticiel est basé sur le didacticiel [Prise en main de Mobile Apps] , que vous devez effectuer en premier.

## <a name="a-nameregisteraregister-your-app-for-authentication-and-configure-the-app-service"></a><a name="register"></a>Inscription de votre application pour l’authentification et configuration d’App Service
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="a-namepermissionsarestrict-permissions-to-authenticated-users"></a><a name="permissions"></a>Restreindre les autorisations aux utilisateurs authentifiés
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

* Dans Android Studio, ouvrez le projet que vous avez terminé avec le didacticiel [Prise en main de Mobile Apps]. Dans le menu **Exécuter**, cliquez sur **Exécuter l’application** et vérifiez qu’une exception non prise en charge avec le code d’état 401 (Non autorisé) est générée après le démarrage de l’application.
  
     Cela se produit, car l’application essaie d’accéder au serveur principal en tant qu’utilisateur non authentifié, mais la table *TodoItem* requiert désormais l’authentification.

Ensuite, mettez à jour l’application pour authentifier les utilisateurs avant de demander des ressources à partir du serveur principal d’applications mobiles.

## <a name="add-authentication-to-the-app"></a>Ajout de l'authentification à l'application
[!INCLUDE [mobile-android-authenticate-app](../../includes/mobile-android-authenticate-app.md)]

## <a name="a-namecache-tokensacache-authentication-tokens-on-the-client"></a><a name="cache-tokens"></a>Mise en cache de jetons d'authentification sur le client
[!INCLUDE [mobile-android-authenticate-app-with-token](../../includes/mobile-android-authenticate-app-with-token.md)]

## <a name="next-steps"></a>Étapes suivantes
Maintenant que vous avez terminé ce didacticiel sur l'authentification de base, vous pouvez passer à l'un des didacticiels suivants :

* [Ajouter des notifications Push à votre application Android](app-service-mobile-android-get-started-push.md) Apprenez à configurer votre serveur principal Mobile Apps pour utiliser Azure Notification Hubs afin d’envoyer des notifications Push.
* [Activer la synchronisation hors connexion pour votre application Android](app-service-mobile-android-get-started-offline-data.md) Apprenez à ajouter une prise en charge hors connexion à votre application à l’aide d’un serveur principal Mobile Apps. La synchronisation hors connexion permet aux utilisateurs finaux d’interagir avec une application mobile &mdash;pour afficher, ajouter ou modifier des données&mdash;, même lorsqu’il n’existe aucune connexion réseau.

<!-- Anchors. -->
[Inscrire votre application pour l'authentification et configurer Mobile Services]: #register
[Restreindre les autorisations de table aux utilisateurs authentifiés]: #permissions
[Ajouter l'authentification à l'application]: #add-authentication
[Stocker les jetons d'authentification sur le client]: #cache-tokens
[Actualiser les jetons expirés]: #refresh-tokens
[Next Steps]:#next-steps


<!-- URLs. -->
[Prise en main de Mobile Apps]: app-service-mobile-android-get-started.md



<!--HONumber=Nov16_HO3-->


