<properties
	pageTitle="Ajouter des notifications Push à votre application iOS à l'aide d'Azure Mobile Apps"
	description="Découvrez comment utiliser Azure Mobile Apps pour envoyer des notifications Push à votre application iOS."
	services="app-service\mobile"
	documentationCenter="ios"
	manager="dwrede"
	editor=""
	authors="krisragh"/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="objective-c"
	ms.topic="article"
	ms.date="11/25/2015"
	ms.author="krisragh"/>


# Ajout de notifications Push à votre application iOS

[AZURE.INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]&nbsp;[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

## Vue d'ensemble
Dans ce didacticiel, vous ajoutez des notifications Push au projet [Démarrage rapide iOS] afin qu'une notification Push soit envoyée chaque fois qu'un enregistrement est inséré. Ce didacticiel est basé sur le didacticiel [Démarrage rapide iOS] que vous devez lire en premier. Si vous n’utilisez pas le projet de serveur du démarrage rapide téléchargé, vous devez ajouter le package d’extension de notification Push à votre projet. Pour plus d'informations sur les packages d'extension de serveur, consultez [Fonctionnement avec le Kit de développement logiciel (SDK) du serveur principal .NET pour Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

Le [simulateur iOS ne prend pas en charge les notifications Push](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/iOS_Simulator_Guide/TestingontheiOSSimulator.html). Vous aurez donc besoin pour ce didacticiel d'un périphérique iOS physique et d'une [adhésion au programme de développement Apple](https://developer.apple.com/programs/ios/).

##<a name="create-hub"></a>Créer un concentrateur de notification

[AZURE.INCLUDE [app-service-mobile-create-notification-hub](../../includes/app-service-mobile-create-notification-hub.md)]

## <a id="register"></a>Inscrire une application pour les notifications Push

[AZURE.INCLUDE [Activer les notifications Push Apple](../../includes/enable-apple-push-notifications.md)]

## Configurer Azure pour l’envoi de notifications Push

[AZURE.INCLUDE [app-service-mobile-apns-configure-push](../../includes/app-service-mobile-apns-configure-push.md)]

##<a id="update-server"></a>Mettre à jour le projet de serveur pour l'envoi de notifications Push

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-configure-push-apns](../../includes/app-service-mobile-dotnet-backend-configure-push-apns.md)]

## <a name="publish-the-service"></a>Déployer le projet de serveur dans Azure

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-publish-service](../../includes/app-service-mobile-dotnet-backend-publish-service.md)]

## <a id="add-push"></a>Ajout de notifications Push à l'application

[AZURE.INCLUDE [app-service-mobile-add-push-notifications-to-ios-app.md](../../includes/app-service-mobile-add-push-notifications-to-ios-app.md)]

## <a id="test"></a>Tester les notifications push dans l'application

[AZURE.INCLUDE [Tester les notifications push dans l’application](../../includes/test-push-notifications-in-app.md)]

##<a id="more"></a>En savoir plus

* Les modèles vous apportent la souplesse nécessaire pour envoyer des notifications push multiplateformes et localisées. La rubrique [Utilisation de la bibliothèque client iOS pour Azure Mobile Apps](app-service-mobile-ios-how-to-use-client-library.md#templates) vous montre comment enregistrer des modèles.
* Les balises vous permettent de vous permettent de cibler des clients segmentés avec des notifications push. La rubrique [Utilisation du kit de développement logiciel (SDK) de serveur principal .NET pour Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#tags) vous montre comment ajouter des balises à l’installation d’un périphérique.

<!-- Anchors.  -->
[Generate iOS certificate signing request]: #certificates
[Register your app and enable push notifications]: #register
[Create a provisioning profile for the app]: #profile
[Add push notifications to the app]: #add-push
[Configure your mobile backend to send push requests]: #configure
[Update the server to send push notifications]: #update-server
[Publish the mobile backend to Azure]: #publish-mobile-service
[Test your app]: #test-the-service

<!-- Images. -->

<!-- URLs. -->
[Démarrage rapide iOS]: app-service-mobile-ios-get-started.md

<!---HONumber=AcomDC_1203_2015-->