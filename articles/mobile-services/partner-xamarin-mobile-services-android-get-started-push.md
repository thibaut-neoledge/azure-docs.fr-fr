---
title: Ajout de notifications Push à votre application Android Mobile Services | Microsoft Docs
description: Apprenez à configurer les notifications Push avec Google Cloud Messaging pour vos applications Xamarin.Android à l’aide d’Azure Mobile Services et Azure Notification Hubs.
documentationcenter: xamarin
author: ggailey777
manager: dwrede
services: mobile-services
editor: ''

ms.service: mobile-services
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: article
ms.date: 07/21/2016
ms.author: glenga

---
# Ajout de notifications push à votre application Mobile Services
[!INCLUDE [mobile-services-selector-get-started-push](../../includes/mobile-services-selector-get-started-push.md)]

&nbsp;

[!INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

> Pour la version Mobile Apps équivalente de cette rubrique, consultez [Ajouter des notifications Push à votre application Xamarin.Android](../app-service-mobile/app-service-mobile-xamarin-android-get-started-push.md).
> 
> 

## Vue d’ensemble
Cette rubrique montre comment utiliser Azure Mobile Services pour envoyer des notifications Push à une application Xamarin.Android. Dans ce didacticiel, vous allez ajouter des notifications push au projet [Prise en main de Mobile Services] à l'aide du service Google Cloud Messaging (GCM). Une fois la procédure terminée, votre service mobile envoie une notification Push chaque fois qu'un enregistrement est inséré.

Ce didacticiel requiert les éléments suivants :

* Un compte Google actif.
* Le composant client [Google Cloud Messaging]. Vous ajouterez ce composant au cours du didacticiel.

Les composants Xamarin et [Azure Mobile Services] doivent déjà être installés dans votre projet dès la fin du didacticiel [Prise en main de Mobile Services].

## <a id="register"></a>Activation de Google Cloud Messaging
[!INCLUDE [mobile-services-enable-Google-cloud-messaging](../../includes/mobile-services-enable-google-cloud-messaging.md)]

## <a id="configure"></a>Configurer votre service mobile pour l’envoi de demandes push
[!INCLUDE [mobile-services-android-configure-push](../../includes/mobile-services-android-configure-push.md)]

## <a id="update-scripts"></a>Mettre à jour le script d'insertion inscrit pour envoyer des notifications
> [!TIP]
> Les étapes suivantes montrent comment mettre à jour le script inscrit vers l’opération d’insertion sur la table TodoItem dans le portail Azure Classic. Vous pouvez également accéder et modifier ce script de service mobile directement dans Visual Studio, dans le nœud Azure de l'Explorateur de serveurs.
> 
> 

[!INCLUDE [mobile-services-javascript-backend-android-push-insert-script](../../includes/mobile-services-javascript-backend-android-push-insert-script.md)]

## <a id="configure-app"></a>Configurer le projet existant pour les notifications push
[!INCLUDE [mobile-services-xamarin-android-push-configure-project](../../includes/mobile-services-xamarin-android-push-configure-project.md)]

## <a id="add-push"></a>Ajouter le code des notifications push à votre application
[!INCLUDE [mobile-services-xamarin-android-push-add-to-app](../../includes/mobile-services-xamarin-android-push-add-to-app.md)]

## <a id="test"></a>Tester les notifications push dans votre application
Vous pouvez tester l'application en connectant directement un téléphone Android via un câble USB, ou en utilisant un appareil virtuel dans l'émulateur.

[!INCLUDE [mobile-services-android-push-notifications-test](../../includes/mobile-services-android-push-notifications-test.md)]

Vous avez terminé ce didacticiel.

## <a name="next-steps"></a>Étapes suivantes
Découvrez Mobile Services et Notification Hubs dans les rubriques suivantes :

* [Prise en main de l'authentification](mobile-services-android-get-started-users.md) <br/>En savoir plus sur l'authentification des utilisateurs de votre application avec des types de comptes différents utilisant des services mobiles.
* [Présentation de Notification Hubs](../notification-hubs/notification-hubs-push-notification-overview.md) <br/>Découvrez comment Notification Hubs opère pour envoyer des notifications à vos applications sur toutes les principales plateformes clientes.
* [Déboguer les applications Notification Hubs](http://go.microsoft.com/fwlink/p/?linkid=386630) </br>Instructions de dépannage et de débogage des solutions Notification Hubs.
* [Utilisation de la bibliothèque cliente .NET pour Mobile Services](mobile-services-dotnet-how-to-use-client-library.md) <br/>En savoir plus sur l'utilisation de Mobile Services avec le code Xamarin C#.
* [Référence de script serveur Mobile Services](mobile-services-how-to-use-server-scripts.md) <br/>En savoir plus sur l'implémentation de logique métier dans votre service mobile.

<!-- URLs. -->
[Prise en main de Mobile Services]: mobile-services-ios-get-started.md

[Google Cloud Messaging]: http://components.xamarin.com/view/GCMClient/
[Azure Mobile Services]: http://components.xamarin.com/view/azure-mobile-services/

<!---HONumber=AcomDC_0727_2016-->