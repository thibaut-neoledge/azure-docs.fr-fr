---
title: "Ajout de l’authentification sur iOS avec Azure Mobile Apps"
description: "Découvrez comment utiliser Azure Mobile Apps pour authentifier les utilisateurs de votre application iOS via divers fournisseurs d&quot;identité, notamment AAD, Google, Facebook, Twitter et Microsoft."
services: app-service\mobile
documentationcenter: ios
author: ysxu
manager: yochayk
editor: 
ms.assetid: ef3d3cbe-e7ca-45f9-987f-80c44209dc06
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: dotnet
ms.topic: article
ms.date: 10/01/2016
ms.author: yuaxu
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 4df1f27e5cefe9813fdc79b96d42e5c5d033901c


---
# <a name="add-authentication-to-your-ios-app"></a>Ajout de l'authentification à votre application iOS
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

Dans ce didacticiel, vous allez ajouter l’authentification au projet de [Démarrage rapide iOS] en faisant appel à un fournisseur d’identité pris en charge. Ce didacticiel est basé sur le didacticiel [Démarrage rapide iOS] , que vous devez effectuer en premier.

## <a name="a-nameregisteraregister-your-app-for-authentication-and-configure-the-app-service"></a><a name="register"></a>Inscription de votre application pour l’authentification et configuration d’App Service
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="a-namepermissionsarestrict-permissions-to-authenticated-users"></a><a name="permissions"></a>Restreindre les autorisations aux utilisateurs authentifiés
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

Dans Xcode, appuyez sur **Exécuter** pour démarrer l’application. Une exception se déclenche, car l’application essaye d’accéder au serveur principal en tant qu’utilisateur non authentifié alors que la table *TodoItem* requiert désormais l’authentification.

## <a name="a-nameadd-authenticationaadd-authentication-to-app"></a><a name="add-authentication"></a>Ajout de l'authentification à l'application
[!INCLUDE [app-service-mobile-ios-authenticate-app](../../includes/app-service-mobile-ios-authenticate-app.md)]

<!-- URLs. -->

[Démarrage rapide iOS]: app-service-mobile-ios-get-started.md

[portail Azure]: https://portal.azure.com



<!--HONumber=Nov16_HO3-->


