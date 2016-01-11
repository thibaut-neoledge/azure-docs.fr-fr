<properties
	pageTitle="Ajouter des notifications Push à votre application Android à l’aide d’Azure Mobile Apps"
	description="Découvrez comment utiliser Azure Mobile Apps pour envoyer des notifications Push à votre application Android."
	services="app-service\mobile"
	documentationCenter="android"
	manager="dwrede"
	editor=""
	authors="ysxu"/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-android"
	ms.devlang="java"
	ms.topic="article"
	ms.date="12/18/2015"
	ms.author="yuaxu"/>

# Ajouter des notifications Push à votre application Android

[AZURE.INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]
&nbsp;  
[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

## Vue d'ensemble
Dans ce didacticiel, vous ajoutez des notifications Push au projet [Démarrage rapide Android] afin qu’une notification Push soit envoyée chaque fois qu’un enregistrement est inséré. Ce didacticiel est basé sur le didacticiel [Démarrage rapide Android], que vous devez effectuer en premier. Si vous n’utilisez pas le projet de serveur du démarrage rapide téléchargé, vous devez ajouter le package d’extension de notification Push à votre projet. Pour plus d'informations sur les packages d'extension de serveur, consultez [Fonctionnement avec le Kit de développement logiciel (SDK) du serveur principal .NET pour Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

##Composants requis

Pour réaliser ce didacticiel, vous avez besoin des éléments suivants :

* [Compte Google](http://go.microsoft.com/fwlink/p/?LinkId=268302) avec une adresse électronique vérifiée.
* [Visual Studio Community 2013](https://go.microsoft.com/fwLink/p/?LinkID=391934) : inutile pour un projet de serveur principal Node.js.
* Suivez le [didacticiel de démarrage rapide](../app-service-mobile-android-get-started.md).

##<a name="create-hub"></a>Créer un Notification Hub

[AZURE.INCLUDE [app-service-mobile-create-notification-hub](../../includes/app-service-mobile-create-notification-hub.md)]

## Activation de Google Cloud Messaging

[AZURE.INCLUDE [mobile-services-enable-google-cloud-messaging](../../includes/mobile-services-enable-google-cloud-messaging.md)]

##Configurer le backend d’application mobile pour l’envoi de demandes de notifications Push

[AZURE.INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push.md)]

##<a id="update-service"></a>Mettre à jour le projet de serveur pour l’envoi de notifications Push

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-configure-push-google](../../includes/app-service-mobile-dotnet-backend-configure-push-google.md)]

## Ajout de notifications Push à votre application

Vous devez vous assurer que votre projet d’application Android est prêt à gérer les notifications Push.

###Vérification de la version du Kit de développement logiciel (SDK) Android

[AZURE.INCLUDE [app-service-mobile-verify-android-sdk-version](../../includes/app-service-mobile-verify-android-sdk-version.md)]

L'étape suivante consiste à installer les services Google Play. Google Cloud Messaging a des spécifications requises d'API minimales pour le développement et les tests, auxquelles la propriété **minSdkVersion** du manifeste doit se conformer.

Si vous envisagez de procéder à un test avec un appareil ancien, consultez la rubrique [Configuration du Kit de développement logiciel (SDK) des services Google Play] pour déterminer comment définir cette valeur et définissez-la correctement.

###Ajout de services Google Play au projet

[AZURE.INCLUDE [Ajout de services Google Play](../../includes/app-service-mobile-add-google-play-services.md)]

###Ajout de code

[AZURE.INCLUDE [app-service-mobile-android-getting-started-with-push](../../includes/app-service-mobile-android-getting-started-with-push.md)]

## Test de l'application avec le service mobile publié

Vous pouvez tester l'application en connectant directement un téléphone Android via un câble USB, ou en utilisant un appareil virtuel dans l'émulateur.

##<a id="more"></a>En savoir plus

* Les balises vous permettent de vous permettent de cibler des clients segmentés avec des notifications push. La rubrique [Utiliser le Kit de développement logiciel (SDK) de serveur principal .NET pour Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) vous montre comment ajouter des balises à l’installation d’un appareil.

<!-- URLs -->
[Démarrage rapide Android]: app-service-mobile-android-get-started.md

<!----HONumber=AcomDC_1223_2015-->