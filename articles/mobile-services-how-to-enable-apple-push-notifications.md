<properties pageTitle="How to enable Apple push notifications" metaKeywords="" description="Follow this tutorial to create a new service using Azure Mobile Services." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="How to Apple push notifications" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="glenga" />

# Activation des notifications Push Apple

Cette rubrique vous explique comment activer les notifications Push sur votre application iOS grâce au service APNS (Apple Push Notification Service) d'Apple. Le certificat obtenu permet d'enregistrer l'application iOS pour les notifications Push dans le [portail de gestion Azure][portail de gestion Azure]. Pour obtenir un didacticiel complet de bout en bout, comprenant les mises à jour de votre application, consultez la rubrique [Prise en main des notifications Push][Prise en main des notifications Push].

[WACOM.INCLUDE [Enable Apple Push Notifications](../includes/enable-apple-push-notifications.md)]

À présent, vous pouvez utiliser ce certificat .p12 pour permettre aux services de s'authentifier avec APNS et d'envoyer des notifications Push de la part de votre application.





  [portail de gestion Azure]: https://manage.windowsazure.com/
  [Prise en main des notifications Push]: /fr-fr/documentation/articles/mobile-services-javascript-backend-ios-get-started-push/
