---
title: "Ajouter des notifications Push à votre application Android à l’aide d’Azure Mobile Apps"
description: "Découvrez comment utiliser Azure Mobile Apps pour envoyer des notifications Push à votre application Android."
services: app-service\mobile
documentationcenter: android
manager: erikre
editor: 
author: ysxu
ms.assetid: 9058ed6d-e871-4179-86af-0092d0ca09d3
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: article
ms.date: 10/12/2016
ms.author: yuaxu
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: ac50b4986d6abd6f3a4af660560f4252b8c99fdb


---
# <a name="add-push-notifications-to-your-android-app"></a>Ajouter des notifications Push à votre application Android
[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## <a name="overview"></a>Vue d'ensemble
Dans ce didacticiel, vous ajoutez des notifications Push au projet [Démarrage rapide Android] afin qu'une notification Push soit envoyée chaque fois qu'un enregistrement est inséré.

Si vous n’utilisez pas le projet de serveur du démarrage rapide téléchargé, vous devrez ajouter le package d’extension de notification Push. Consultez [Fonctionnement avec le Kit de développement logiciel (SDK) du serveur principal .NET pour Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) pour plus d’informations.

## <a name="prerequisites"></a>Composants requis
Vous avez besoin des éléments suivants :

* Un IDE en fonction du serveur principal de votre projet :
  
  * [Android Studio](https://developer.android.com/sdk/index.html) Si cette application a un serveur principal Node.js.
  * [Visual Studio Community 2013](https://go.microsoft.com/fwLink/p/?LinkID=391934) ou version ultérieure si cette application est un service principal .net.
* Android 2.3 ou version ultérieure, Révision du référentiel Google 27 ou version ultérieure et Services Google Play 9.0.2 ou version ultérieure pour Firebase Cloud Messaging.
* Terminer le [Démarrage rapide Android].

## <a name="create-a-project-that-supports-firebase-cloud-messaging"></a>Créer un projet qui prend en charge Firebase Cloud Messaging
[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

## <a name="configure-a-notification-hub"></a>Configuration d’un hub de notification
[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

## <a name="configure-azure-to-send-push-notifications"></a>Configurer Azure pour l’envoi de notifications Push
[!INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push-for-firebase.md)]

## <a name="enable-push-notifications-for-the-server-project"></a>Activation des notifications push pour le projet de serveur
[!INCLUDE [app-service-mobile-dotnet-backend-configure-push-google](../../includes/app-service-mobile-dotnet-backend-configure-push-google.md)]

## <a name="add-push-notifications-to-your-app"></a>Ajout de notifications Push à votre application
Dans cette section, vous mettez à jour votre application Android client pour gérer les notifications push.

### <a name="verify-android-sdk-version"></a>Vérification de la version du Kit de développement logiciel (SDK) Android
[!INCLUDE [app-service-mobile-verify-android-sdk-version](../../includes/app-service-mobile-verify-android-sdk-version.md)]

L'étape suivante consiste à installer les services Google Play. Google Cloud Messaging a des spécifications requises d'API minimales pour le développement et les tests, auxquelles la propriété **minSdkVersion** du manifeste doit se conformer.

Si vous procédez à un test avec un appareil ancien, consultez la rubrique [Configuration du Kit de développement logiciel (SDK) des services Google Play] pour déterminer comment définir cette valeur.

### <a name="add-google-play-services-to-the-project"></a>Ajout de services Google Play au projet
[!INCLUDE [Add Play Services](../../includes/app-service-mobile-add-google-play-services.md)]

### <a name="add-code"></a>Ajout de code
[!INCLUDE [app-service-mobile-android-getting-started-with-push](../../includes/app-service-mobile-android-getting-started-with-push.md)]

## <a name="test-the-app-against-the-published-mobile-service"></a>Test de l'application avec le service mobile publié
Vous pouvez tester l’application en connectant directement un téléphone Android via un câble USB, ou en utilisant un appareil virtuel dans l’émulateur.

## <a name="more"></a>En savoir plus
<!-- URLs -->
[Démarrage rapide Android]: app-service-mobile-android-get-started.md

[Configuration du Kit de développement logiciel (SDK) des services Google Play]:https://developers.google.com/android/guides/setup



<!--HONumber=Nov16_HO3-->


