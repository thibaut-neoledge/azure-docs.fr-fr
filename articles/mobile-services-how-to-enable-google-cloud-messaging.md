<properties 
	pageTitle="Activation de Google Cloud Messaging" 
	description="Suivez ce didacticiel pour créer un nouveau service à l'aide d'Azure Mobile Services." 
	services="mobile-services, notification-hubs" 
	documentationCenter="android" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-android" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="11/21/2014" 
	ms.author="glenga"/>

# Activation de Google Cloud Messaging

Cette rubrique vous indique comment activer votre application Android pour les notifications Push avec Google Cloud Messaging (GCM). La clé API obtenue est utilisée pour inscrire l'application Android pour les notifications push dans les [portail de gestion Azure][portail de gestion]. Pour obtenir un didacticiel complet de bout en bout, comprenant les mises à jour de votre application, consultez la rubrique [Prise en main des notifications Push]. 

[AZURE.INCLUDE [Enable GCM](../includes/mobile-services-enable-Google-cloud-messaging.md)]

À présent, vous pouvez utiliser cette valeur de clé API pour permettre aux services de s'authentifier avec GCM et d'envoyer des notifications Push de la part de votre application.

<!-- Anchors. -->


<!-- Images. -->


<!-- URLs. -->
[Prise en main des notifications Push]: /fr-fr/documentation/articles/mobile-services-javascript-backend-android-get-started-push/
[Visual Studio 2012 Express pour Windows Phone]: https://go.microsoft.com/fwLink/p/?LinkID=268374
[Kit de développement logiciel (SDK) Mobile Services]: https://go.microsoft.com/fwLink/p/?LinkID=268375

[Portail de gestion]: https://manage.windowsazure.com/
[Version principale .NET]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started


<!--HONumber=42-->
