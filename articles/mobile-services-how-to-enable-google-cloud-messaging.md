<properties pageTitle="How to enable Google Cloud Messaging" metaKeywords="" description="Follow this tutorial to create a new service using Azure Mobile Services." metaCanonical="" services="" documentationCenter="Mobile" title="How to create a new mobile service" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-android" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="glenga" />

# Activation de Google Cloud Messaging

Cette rubrique vous indique comment activer votre application Android pour les notifications Push avec Google Cloud Messaging (GCM). La clé d'API obtenue permet d'enregistrer l'application Android pour les notifications Push dans le [portail de gestion Azure][portail de gestion Azure]. Pour obtenir un didacticiel complet de bout en bout, comprenant les mises à jour de votre application, consultez la rubrique [Prise en main des notifications Push][Prise en main des notifications Push].

[WACOM.INCLUDE [Enable GCM](../includes/mobile-services-enable-Google-cloud-messaging.md)]

À présent, vous pouvez utiliser cette valeur de clé API pour permettre aux services de s'authentifier avec GCM et d'envoyer des notifications Push de la part de votre application.


 


  [portail de gestion Azure]: https://manage.windowsazure.com/
  [Prise en main des notifications Push]: /fr-fr/documentation/articles/mobile-services-javascript-backend-android-get-started-push/
