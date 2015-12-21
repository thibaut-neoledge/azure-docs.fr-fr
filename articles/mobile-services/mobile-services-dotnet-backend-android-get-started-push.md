<properties
	pageTitle="Prise en main des notifications push (Android) | Microsoft Azure"
	description="Découvrez comment utiliser Azure Mobile Services pour envoyer des notifications Push à votre application Android .Net."
	services="mobile-services, notification-hubs"
	documentationCenter="android"
	authors="RickSaling"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-android"
	ms.devlang="java"
	ms.topic="article"
	ms.date="12/06/2015"
	ms.author="ricksal"/>

# Ajout de notifications push à votre application Mobile Services

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;


[AZURE.INCLUDE [mobile-services-selector-get-started-push](../../includes/mobile-services-selector-get-started-push.md)]

Cette rubrique montre comment utiliser Azure Mobile Services pour envoyer des notifications Push à une application Android. Dans ce didacticiel, vous ajoutez des notifications Push au projet de démarrage rapide à l'aide du service Google Cloud Messaging (GCM). Une fois la procédure terminée, votre service mobile envoie une notification Push chaque fois qu'un enregistrement est inséré.

Ce didacticiel est basé sur le démarrage rapide de Mobile Services. Avant de commencer ce didacticiel, vous devez suivre [Prise en main de Mobile Services] pour connecter votre projet au service mobile. En tant que tel, ce didacticiel requiert également Visual Studio 2013.

## Exemple de code
Pour afficher le code source terminé, cliquez [ici](https://github.com/RickSaling/mobile-services-samples/tree/push/GettingStartedWithPush).

## Activation de Google Cloud Messaging

[AZURE.INCLUDE [mobile-services-enable-Google-cloud-messaging](../../includes/mobile-services-enable-google-cloud-messaging.md)]

## Configurer votre service mobile pour l’envoi de demandes push

[AZURE.INCLUDE [mobile-services-android-configure-push](../../includes/mobile-services-android-configure-push.md)]

## Mettre à jour le service mobile pour l’envoi de notifications Push

[AZURE.INCLUDE [mobile-services-dotnet-backend-android-push-update-service](../../includes/mobile-services-dotnet-backend-android-push-update-service.md)]

## Ajout de notifications Push à votre application

###Vérification de la version du Kit de développement logiciel (SDK) Android

[AZURE.INCLUDE [mobile-services-verify-android-sdk-version](../../includes/mobile-services-verify-android-sdk-version.md)]


L'étape suivante consiste à installer les services Google Play. Google Cloud Messaging a des spécifications requises d'API minimales pour le développement et les tests, auxquelles la propriété **minSdkVersion** du manifeste doit se conformer.

Si vous envisagez de procéder à un test avec un appareil ancien, consultez la rubrique [Configuration du Kit de développement logiciel (SDK) des services Google Play] pour déterminer comment définir cette valeur et définissez-la correctement.

###Ajout de services Google Play au projet

[AZURE.INCLUDE [Ajout de services Google Play](../../includes/mobile-services-add-google-play-services.md)]

###Ajout de code

[AZURE.INCLUDE [mobile-services-android-getting-started-with-push](../../includes/mobile-services-android-getting-started-with-push.md)]

## Test de l'application avec le service mobile publié

Vous pouvez tester l'application en connectant directement un téléphone Android via un câble USB, ou en utilisant un appareil virtuel dans l'émulateur.

### Activation des notifications Push pour les tests en local

[AZURE.INCLUDE [mobile-services-dotnet-backend-configure-local-push](../../includes/mobile-services-dotnet-backend-configure-local-push.md)]

[AZURE.INCLUDE [mobile-services-android-push-notifications-test](../../includes/mobile-services-android-push-notifications-test.md)]

Vous avez terminé ce didacticiel.

## Étapes suivantes

Ce didacticiel a présenté les bases de l’activation d’une application Android pour utiliser Mobile Services et Notification Hubs afin d’envoyer des notifications Push. Vous pouvez ensuite envisager de consulter le didacticiel suivant, [Envoi de notifications Push aux utilisateurs authentifiés], qui explique comment utiliser des balises pour envoyer des notifications Push depuis votre service mobile vers un utilisateur authentifié uniquement.

+ [Envoi de notifications diffusées aux abonnés] <br/>En savoir plus sur l'inscription des utilisateurs et la réception des notifications Push pour les catégories qui les intéressent.

+ [Envoi de notifications basées sur un modèle aux abonnés] <br/>En savoir plus sur l'utilisation de modèles pour envoyer des notifications Push depuis un service mobile, sans avoir à gérer des charges utiles propres à la plateforme sur votre serveur principal.

Découvrez Mobile Services et Notification Hubs dans les rubriques suivantes :

* [Présentation de Notification Hubs] <br/>Découvrez comment Notification Hubs opère pour envoyer des notifications à vos applications sur toutes les principales plateformes clientes.

* [Débogage des applications Notification Hubs](http://go.microsoft.com/fwlink/p/?linkid=386630) </br>Instructions de dépannage et de débogage des solutions Notification Hubs.

* [Utilisation de la bibliothèque cliente Android pour Mobile Services] <br/>En savoir plus sur l'utilisation de Mobile Services avec Android.

<!-- Anchors. -->

[Create a new mobile service]: #create-service
[Download the service locally]: #download-the-service-locally
[Test the mobile service]: #test-the-service
[Download the GetStartedWithData project]: #download-app
[Update the app to use the mobile service for data access]: #update-app
[Test the Android App against the service hosted locally]: #test-locally-hosted
[Publish the mobile service to Azure]: #publish-mobile-service
[Test the Android App against the service hosted in Azure]: #test-azure-hosted
[Test the app against the published mobile service]: #test-app
[Next Steps]: #next-steps

<!-- Images. -->

<!-- URLs. -->
[Get started with push notifications (Eclipse)]: mobile-services-dotnet-backend-android-get-started-push-ec.md
[Prise en main de Mobile Services]: mobile-services-dotnet-backend-android-get-started.md
[Mobile Services SDK]: http://go.microsoft.com/fwlink/p/?LinkId=257545

[Utilisation de la bibliothèque cliente Android pour Mobile Services]: mobile-services-android-how-to-use-client-library.md

[Présentation de Notification Hubs]: ../notification-hubs-overview.md
[Envoi de notifications diffusées aux abonnés]: ../notification-hubs-windows-store-dotnet-send-breaking-news.md
[Envoi de notifications basées sur un modèle aux abonnés]: ../notification-hubs-windows-store-dotnet-send-localized-breaking-news.md

<!---HONumber=AcomDC_1210_2015-->