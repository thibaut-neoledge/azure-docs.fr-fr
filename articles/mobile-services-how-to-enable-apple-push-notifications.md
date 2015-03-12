<properties 
	pageTitle="Activation des notifications Push Apple" 
	description="Suivez ce didacticiel pour créer un nouveau service à l'aide d'Azure Mobile Services." 
	services="mobile-services, notification-hubs" 
	documentationCenter="ios" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-ios" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="11/21/2014" 
	ms.author="glenga"/>

# Activation des notifications Push Apple

Cette rubrique vous explique comment activer les notifications Push sur votre application iOS grâce au service APNS (Apple Push Notification Service) d'Apple. Le certificat obtenu permet d'enregistrer l'application iOS pour les notifications Push dans le [portail de gestion Azure][portail de gestion]. Pour obtenir un didacticiel complet de bout en bout, comprenant les mises à jour de votre application, consultez la rubrique [Prise en main des notifications Push]. 

[AZURE.INCLUDE [Enable Apple Push Notifications](../includes/enable-apple-push-notifications.md)]

À présent, vous pouvez utiliser ce certificat .p12 pour permettre aux services de s'authentifier avec APNS et d'envoyer des notifications Push de la part de votre application.

<!-- Anchors. -->


<!-- Images. -->


<!-- URLs. -->
[Prise en main des notifications Push]: /fr-fr/documentation/articles/mobile-services-javascript-backend-ios-get-started-push/
[Kit de développement logiciel (SDK) Mobile Services]: https://go.microsoft.com/fwLink/p/?LinkID=268375

[Portail de gestion]: https://manage.windowsazure.com/



<!--HONumber=42-->
