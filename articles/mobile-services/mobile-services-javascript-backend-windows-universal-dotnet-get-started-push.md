<properties 
	pageTitle="Ajout de notifications Push à votre application Windows 8.1 universelle | Microsoft Azure" 
	description="Découvrez comment utiliser Azure Notification Hubs pour envoyer des notifications Push à votre application Windows 8.1 universelle à partir de votre service mobile principal JavaScript." 
	services="mobile-services,notification-hubs" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="11/10/2015" 
	ms.author="glenga"/>


# Ajout de notifications push à votre application Mobile Services

[AZURE.INCLUDE [mobile-services-selector-get-started-push](../../includes/mobile-services-selector-get-started-push.md)]

Cette rubrique explique comment utiliser Azure Mobile Services avec un backend JavaScript pour envoyer des notifications Push à une application Windows universelle. Dans ce didacticiel, vous allez activer des notifications Push à l'aide d'Azure Notification Hubs dans un projet d'application Windows universelle. Lorsque vous aurez terminé, votre service mobile enverra une notification Push depuis le backend JavaScript vers toutes les applications Windows Store et Windows Phone Store enregistrées, chaque fois qu'un enregistrement est inséré dans la table TodoList. Le concentrateur de notification que vous créez est fourni gratuitement avec votre service mobile, peut être géré indépendamment du service mobile et peut être utilisé par d'autres applications et services.

>[AZURE.NOTE]Cette rubrique vous présente l'utilisation des outils dans Visual Studio 2013 Update 3 pour ajouter la prise en charge des notifications Push depuis Mobile Services vers une application Windows universelle. La même procédure peut être utilisée pour ajouter la prise en charge des notifications Push depuis Mobile Services vers une application Windows Store ou Windows Phone Store 8.1. Pour ajouter des notifications Push à une application Windows Phone 8 ou Windows Phone Silverlight 8.1, consultez cette version de [Prise en main des notifications Push dans Mobile Services](mobile-services-javascript-backend-windows-phone-get-started-push.md).

Ce didacticiel vous familiarise avec les étapes de base permettant d'activer les notifications Push :

1. [Inscription de votre application pour les notifications Push](#register)
2. [Mise à jour du service pour l'envoi de notifications Push](#update-service)
3. [Test des notifications Push dans votre application](#test)

Pour réaliser ce didacticiel, vous avez besoin des éléments suivants :

* Un [compte Microsoft Store](http://go.microsoft.com/fwlink/p/?LinkId=280045) actif.
* [Visual Studio 2013 Express pour Windows](http://go.microsoft.com/fwlink/?LinkId=257546) Update 3 ou une version ultérieure

##<a id="register"></a>Inscription de votre application pour les notifications Push

[AZURE.INCLUDE [mobile-services-create-new-push-vs2013](../../includes/mobile-services-create-new-push-vs2013.md)]

&nbsp;&nbsp;6. Accédez au dossier de projet `\Services\MobileServices\your_service_name`, ouvrez le fichier de code push.register.cs généré et examinez la méthode **UploadChannel** qui enregistre l'URL de canal d'appareil avec le concentrateur de notification.

&nbsp;&nbsp;7. Ouvrez le fichier de code App.xaml.cs partagé et notez qu'un appel vers la méthode **UploadChannel** a été ajouté au gestionnaire d'événements **OnLaunched**. Cela garantit qu'une tentative d'inscription de l'appareil est effectuée chaque fois que l'application est lancée.

&nbsp;&nbsp;8. Répétez les étapes précédentes pour ajouter des notifications Push à un projet d’application Windows Phone Store. Ensuite, dans le fichier App.xaml.cs partagé, supprimez l’appel supplémentaire vers le **client de service mobile**, **UploadChannel** et le wrapper conditionnel `#if...#endif` restant. Les deux projets peuvent maintenant partager un même appel vers **UploadChannel**.

&nbsp;&nbsp;Notez que vous pouvez également simplifier le code généré en unifiant les définitions [MobileServiceClient] encapsulées dans `#if...#endif` en une seule définition non encapsulée, utilisée par les deux versions de l’application.

Maintenant que les notifications Push sont activées dans l'application, vous devez mettre à jour le service mobile pour les envoyer.

##<a id="update-service"></a>Mise à jour du service pour l'envoi de notifications Push

La procédure suivante permet de mettre à jour le script d'insertion enregistré dans la table TodoItem. Vous pouvez implémenter un code similaire dans n'importe quel script serveur ou n'importe où dans vos services backend.

[AZURE.INCLUDE [mobile-services-javascript-update-script-notification-hubs](../../includes/mobile-services-javascript-update-script-notification-hubs.md)]


##<a id="test"></a> Test des notifications Push dans votre application

[AZURE.INCLUDE [mobile-services-javascript-backend-windows-universal-test-push](../../includes/mobile-services-javascript-backend-windows-universal-test-push.md)]

## <a name="next-steps"> </a>Étapes suivantes

Ce didacticiel explique les règles de base de l'activation d'une application Windows Store pour utiliser Mobile Services et Notification Hubs afin d'envoyer des notifications Push. Nous vous invitons maintenant à suivre l'un des didacticiels suivants :

+ [Envoi de notifications Push à des utilisateurs authentifiés](mobile-services-javascript-backend-windows-store-dotnet-push-notifications-app-users.md)
	<br/>En savoir plus sur l'utilisation de balises pour envoyer des notifications Push depuis un service mobile uniquement à un utilisateur authentifié.

+ [Envoi de notifications diffusées aux abonnés](../notification-hubs-windows-store-dotnet-send-breaking-news.md)
	<br/>En savoir plus sur l'inscription des utilisateurs et la réception des notifications Push pour les catégories qui les intéressent.

+ [Envoi de notifications indépendantes de toute plateforme aux abonnés](../notification-hubs-aspnet-cross-platform-notify-users.md)
	<br/>En savoir plus sur l'utilisation de modèles pour envoyer des notifications Push depuis votre service mobile, sans avoir à gérer des charges utiles propres à la plateforme sur votre serveur principal.

Découvrez Mobile Services et Notification Hubs dans les rubriques suivantes :

* [Azure Notification Hubs : instructions relatives au diagnostic](../notification-hubs-diagnosing.md)
	<br/>En savoir plus sur la résolution des problèmes liés aux notifications Push.

* [Prise en main de l'authentification] <br/>En savoir plus sur l'authentification des utilisateurs de votre application avec des types de comptes différents utilisant des services mobiles.

* [Présentation de Notification Hubs] <br/>Découvrez comment Notification Hubs opère pour envoyer des notifications à vos applications sur toutes les principales plateformes clientes.

* [Utilisation d'un client .NET pour Azure Mobile Services] <br/>En savoir plus sur l'utilisation de Mobile Services à partir d'applications C# Windows.

<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Get started with Mobile Services]: mobile-services-dotnet-backend-windows-store-dotnet-get-started.md
[Prise en main de l'authentification]: mobile-services-javascript-backend-windows-universal-dotnet-get-started-users.md

[Send push notifications to authenticated users]: mobile-services-javascript-backend-windows-store-dotnet-push-notifications-app-users.md

[Présentation de Notification Hubs]: ../notification-hubs-overview.md

[Utilisation d'un client .NET pour Azure Mobile Services]: mobile-services-windows-dotnet-how-to-use-client-library.md
[MobileServiceClient]: http://go.microsoft.com/fwlink/p/?LinkId=302030
 

<!---HONumber=Nov15_HO4-->