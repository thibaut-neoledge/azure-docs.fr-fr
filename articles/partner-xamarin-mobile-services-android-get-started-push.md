<properties 
	pageTitle="Ajouter des notifications Push à votre application Mobile Services - Mobile Services" 
	description="Découvrez comment utiliser les notifications Push dans les applications Xamarin.Android avec Azure Mobile Services." 
	documentationCenter="xamarin" 
	authors="ggailey777" 
	manager="dwrede" 
	services="mobile-services" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-xamarin-android" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="03/12/2015" 
	ms.author="glenga"/>

# Ajout de notifications push à votre application Mobile Services

[AZURE.INCLUDE [mobile-services-selector-get-started-push](../includes/mobile-services-selector-get-started-push.md)]

Cette rubrique montre comment utiliser Azure Mobile Services pour envoyer des notifications Push à une application Xamarin.Android. Dans ce didacticiel, vous allez ajouter des notifications push au projet [Prise en main de Mobile Services] à l'aide du service Google Cloud Messaging (GCM). Une fois la procédure terminée, votre service mobile envoie une notification Push chaque fois qu'un enregistrement est inséré.

Ce didacticiel vous familiarise avec les étapes de base permettant d'activer les notifications Push :

1. [Activer Google Cloud Messaging](#register)
2. [Configurer Mobile Services](#configure)
4. [Configurer le projet pour les notifications push](#configure-app)
5. [Ajouter le code des notifications push à votre application](#add-push)
6. [Insérer des données pour recevoir des notifications](#test)

Ce didacticiel requiert les éléments suivants :

+ Un compte Google actif.
+ Le composant client [Google Cloud Messaging]. Vous ajouterez ce composant au cours du didacticiel.

Les composants [Xamarin.Android] et [Azure Mobile Services][Azure Mobile Services Component] doivent déjà être installés dans votre projet dès la fin du didacticiel [Prise en main de Mobile Services] ou [Ajouter Mobile Services à une application existante].

##<a id="register"></a>Activer Google Cloud Messaging

[AZURE.INCLUDE [mobile-services-enable-Google-cloud-messaging](../includes/mobile-services-enable-Google-cloud-messaging.md)]

##<a id="configure"></a>Configurer votre service mobile pour l'envoi de demandes push

[AZURE.INCLUDE [mobile-services-android-configure-push](../includes/mobile-services-android-configure-push.md)]

##<a id="update-scripts"></a>Mettre à jour le script d'insertion inscrit pour envoyer des notifications

>[AZURE.NOTE]Les étapes suivantes vous montrent comment mettre à jour le script inscrit avec l'opération d'insertion sur la table TodoItem dans le portail de gestion Azure. Vous pouvez également accéder et modifier ce script de service mobile directement dans Visual Studio, dans le nœud Azure de l'Explorateur de serveurs.

[AZURE.INCLUDE [mobile-services-javascript-backend-android-push-insert-script](../includes/mobile-services-javascript-backend-android-push-insert-script.md)]


##<a id="configure-app"></a>Configurer le projet existant pour les notifications push

[AZURE.INCLUDE [mobile-services-xamarin-android-push-configure-project](../includes/mobile-services-xamarin-android-push-configure-project.md)]

##<a id="add-push"></a>Ajouter le code des notifications push à votre application

[AZURE.INCLUDE [mobile-services-xamarin-android-push-add-to-app](../includes/mobile-services-xamarin-android-push-add-to-app.md)]

##<a id="test"></a>Tester les notifications push dans votre application

Vous pouvez tester l'application en connectant directement un téléphone Android via un câble USB, ou en utilisant un appareil virtuel dans l'émulateur.

[AZURE.INCLUDE [mobile-services-android-push-notifications-test](../includes/mobile-services-android-push-notifications-test.md)]

Vous avez terminé ce didacticiel.

## <a name="next-steps"></a>Étapes suivantes

Découvrez Mobile Services et Notification Hubs dans les rubriques suivantes :

* [Ajouter Mobile Services à une application existante] <br/>En savoir plus sur le stockage et l'interrogation des données à l'aide de services mobiles.

* [Prise en main de l'authentification](partner-xamarin-mobile-services-android-get-started-users.md) <br/>En savoir plus sur l'authentification des utilisateurs de votre application avec des types de comptes différents utilisant des services mobiles.

* [Présentation de Notification Hubs](notification-hubs/notification-hubs-overview.md) <br/>Découvrez comment Notification Hubs opère pour envoyer des notifications à vos applications sur toutes les principales plateformes clientes.

* [Déboguer les applications Notification Hubs](http://go.microsoft.com/fwlink/p/?linkid=386630) </br>Instructions de dépannage et de débogage des solutions Notification Hubs.

* [Utilisation de la bibliothèque cliente .NET pour Mobile Services](mobile-services-windows-dotnet-how-to-use-client-library.md) <br/>En savoir plus sur l'utilisation de Mobile Services avec le code Xamarin C#.

* [Référence de script serveur Mobile Services](mobile-services-how-to-use-server-scripts.md) <br/>En savoir plus sur l'implémentation de logique métier dans votre service mobile.

<!-- URLs. -->
[Prise en main de Mobile Services]: partner-xamarin-mobile-services-ios-get-started.md
[Ajouter Mobile Services à une application existante]: partner-xamarin-mobile-services-android-get-started-data.md

[Google Cloud Messaging]: http://components.xamarin.com/view/GCMClient/
[Xamarin.Android]: http://xamarin.com/download/
[Azure Mobile Services Component]: http://components.xamarin.com/view/azure-mobile-services/
<!--HONumber=54-->