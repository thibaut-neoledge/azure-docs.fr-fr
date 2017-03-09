---
title: "Ajout de l’authentification à Android à l’aide de Mobile Apps | Microsoft Docs"
description: "Découvrez comment utiliser la fonctionnalité Mobile Apps d’Azure App Service pour authentifier les utilisateurs de votre application Android via divers fournisseurs d’identité, notamment Google, Facebook, Twitter et Microsoft."
services: app-service\mobile
documentationcenter: android
author: ysxu
manager: adrianha
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
ms.sourcegitcommit: 817626dd3fc87db61280075b80cedf8b9ed77684
ms.openlocfilehash: e638495c10742388804e75f3277c50cf1e20c6a6
ms.lasthandoff: 12/13/2016


---
# <a name="add-authentication-to-your-android-app"></a>Ajout de l’authentification à votre application Android
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

## <a name="summary"></a>Résumé
Dans ce didacticiel, vous allez ajouter l’authentification au projet de démarrage rapide todolist sur Android en faisant appel à un fournisseur d’identité pris en charge. Ce didacticiel est basé sur le didacticiel [Prise en main de Mobile Apps] , que vous devez effectuer en premier.

## <a name="register"></a>Inscription de votre application pour l’authentification et configuration d’Azure App Service
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="permissions"></a>Restriction des autorisations pour les utilisateurs authentifiés
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

* Dans Android Studio, ouvrez le projet que vous avez terminé avec le didacticiel [Prise en main de Mobile Apps]. Dans le menu **Exécuter**, cliquez sur **Exécuter l’application** et vérifiez qu’une exception non prise en charge avec le code d’état 401 (Non autorisé) est générée après le démarrage de l’application.

     Cela se produit car l’application essaie d’accéder au serveur principal en tant qu’utilisateur non authentifié, mais la table *TodoItem* requiert désormais l’authentification.

Ensuite, mettez à jour l’application pour authentifier les utilisateurs avant de demander des ressources à partir du serveur principal Mobile Apps. 

## <a name="add-authentication-to-the-app"></a>Ajout de l'authentification à l'application
[!INCLUDE [mobile-android-authenticate-app](../../includes/mobile-android-authenticate-app.md)]

## <a name="cache-tokens"></a>Mise en cache de jetons d'authentification sur le client
[!INCLUDE [mobile-android-authenticate-app-with-token](../../includes/mobile-android-authenticate-app-with-token.md)]

## <a name="next-steps"></a>Étapes suivantes
Maintenant que vous avez terminé ce didacticiel sur l'authentification de base, vous pouvez passer à l'un des didacticiels suivants :

* [Ajout de notifications Push à votre application Android](app-service-mobile-android-get-started-push.md).
  Découvrez comment configurer votre serveur principal Mobile Apps pour utiliser Azure Notification Hubs afin d’envoyer des notifications Push.
* [Activation de la synchronisation hors connexion pour votre application Android](app-service-mobile-android-get-started-offline-data.md).
  Découvrez comment ajouter une prise en charge hors connexion à votre application à l’aide d’un serveur principal Mobile Apps. La synchronisation hors connexion permet aux utilisateurs d’interagir avec une application mobile &mdash;pour afficher, ajouter ou modifier des données&mdash;, même lorsqu’il n’existe aucune connexion réseau.

<!-- Anchors. -->
[Register your app for authentication and configure Mobile Services]: #register
[Restrict table permissions to authenticated users]: #permissions
[Add authentication to the app]: #add-authentication
[Store authentication tokens on the client]: #cache-tokens
[Refresh expired tokens]: #refresh-tokens
[Next Steps]:#next-steps


<!-- URLs. -->
[Prise en main de Mobile Apps]: app-service-mobile-android-get-started.md

