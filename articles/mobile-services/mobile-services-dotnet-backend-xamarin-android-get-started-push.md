<properties
	pageTitle="Prise en main de Mobile Services pour les applications Xamarin Android | Microsoft Azure"
	description="Découvrez comment utiliser Azure Mobile Services et Notification Hubs pour envoyer des notifications Push à votre application Xamarin Android."
	services="mobile-services"
	documentationCenter="xamarin"
	authors="ggailey777"
	manager="dwrede"
	editor="mollybos"/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-xamarin-android"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="12/11/2015"
	ms.author="glenga"/>

# Ajout de notifications push à votre application Mobile Services

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;


[AZURE.INCLUDE [mobile-services-selector-get-started-push](../../includes/mobile-services-selector-get-started-push.md)]

##Vue d'ensemble

Cette rubrique montre comment utiliser Azure Mobile Services pour envoyer des notifications Push à une application Xamarin.Android. Dans ce didacticiel, vous allez ajouter des notifications push au projet [Prise en main de Mobile Services] à l'aide du service Google Cloud Messaging (GCM). Une fois la procédure terminée, votre service mobile envoie une notification Push chaque fois qu'un enregistrement est inséré.

Ce didacticiel requiert les éléments suivants :

+ Un compte Google actif.
+ Le composant client [Google Cloud Messaging]. Vous ajouterez ce composant au cours du didacticiel.

Les composants [Xamarin.Android] et [Azure Mobile Services][Azure Mobile Services Component] doivent déjà être installés dans votre projet dès la fin du didacticiel [Prise en main de Mobile Services].

##<a id="register"></a>Activation de Google Cloud Messaging

[AZURE.INCLUDE [Activation de GCM](../../includes/mobile-services-enable-google-cloud-messaging.md)]

##<a id="configure"></a>Configuration du service mobile pour l'envoi de demandes Push

[AZURE.INCLUDE [mobile-services-android-configure-push](../../includes/mobile-services-android-configure-push.md)]

##<a id="update-server"></a>Mise à jour du service mobile pour l'envoi de notifications Push

[AZURE.INCLUDE [mobile-services-dotnet-backend-android-push-update-service](../../includes/mobile-services-dotnet-backend-android-push-update-service.md)]

##<a id="configure-app"></a>Configuration du projet existant pour les notifications Push

[AZURE.INCLUDE [mobile-services-xamarin-android-push-configure-project](../../includes/mobile-services-xamarin-android-push-configure-project.md)]

##<a id="add-push"></a>Ajouter le code des notifications push à votre application

[AZURE.INCLUDE [mobile-services-xamarin-android-push-add-to-app](../../includes/mobile-services-xamarin-android-push-add-to-app.md)]

##<a name="test-app"></a>Test de l'application avec le service mobile publié

Vous pouvez tester l'application en connectant directement un téléphone Android via un câble USB, ou en utilisant un appareil virtuel dans l'émulateur.

###<a id="local-testing"></a> Activation des notifications Push pour les tests en local

[AZURE.INCLUDE [mobile-services-dotnet-backend-configure-local-push](../../includes/mobile-services-dotnet-backend-configure-local-push.md)]

[AZURE.INCLUDE [mobile-services-android-push-notifications-test](../../includes/mobile-services-android-push-notifications-test.md)]

<!-- URLs. -->
[Prise en main de Mobile Services]: mobile-services-dotnet-backend-xamarin-android-get-started.md


[Google Cloud Messaging]: http://components.xamarin.com/view/GCMClient/
[Xamarin.Android]: http://xamarin.com/download/
[Azure Mobile Services Component]: http://components.xamarin.com/view/azure-mobile-services/

<!---HONumber=AcomDC_1217_2015-->