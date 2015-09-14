<properties
	pageTitle="Envoi de notifications Push aux utilisateurs authentifiés"
	description="Découvrez comment envoyer des notifications Push à"
	services="mobile-services,notification-hubs"
	documentationCenter="windows"
	authors="ggailey777"
	manager="dwrede"
	editor=""/>

<tags 
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-windows-phone"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="06/16/2015"
	ms.author="glenga"/>

# Envoi de notifications Push aux utilisateurs authentifiés

[AZURE.INCLUDE [mobile-services-selector-push-users](../../includes/mobile-services-selector-push-users.md)]

Cette rubrique montre comment envoyer des notifications Push à un utilisateur authentifié sur un appareil inscrit. À l’inverse du précédent didacticiel [Ajout de notifications Push à votre application], celui-ci modifie votre service mobile pour exiger l’authentification des utilisateurs avant que le client puisse s’inscrire auprès du concentrateur de notification pour les notifications Push. L'inscription est également modifiée pour ajouter une balise basée sur l'ID d'utilisateur affecté. Enfin, le code du serveur est mis à jour pour envoyer la notification uniquement à l'utilisateur authentifié, et non à toutes les inscriptions.

Ce didacticiel concerne les applications Windows Phone 8.0 et Windows Phone 8.1 Silverlight. Pour les applications Windows Phone 8.1 Store, reportez-vous à la [version dédiée à Windows Store](mobile-services-dotnet-backend-windows-store-dotnet-push-notifications-app-users.md).

##Configuration requise

Avant de commencer ce didacticiel, vous devez suivre les didacticiels Mobile Services suivants :

+ [Ajout de l’authentification à votre application]<br/>Ajoute une exigence de connexion à l’exemple d’application TodoList.

+ [Ajout de notifications Push à votre application]<br/>Configure l’exemple d’application TodoList pour les notifications Push à l’aide de Notification Hubs.

Une fois ces deux didacticiels terminés, vous saurez comment empêcher les utilisateurs non authentifiés de s'inscrire pour recevoir les notifications Push de votre service mobile.

##<a name="register"></a>Mise à jour du service pour demander l'authentification pour l'inscription

[AZURE.INCLUDE [mobile-services-dotnet-backend-push-notifications-app-users](../../includes/mobile-services-dotnet-backend-push-notifications-app-users.md)]

##<a name="update-app"></a>Mise à jour de l'application pour se connecter avant l'inscription

[AZURE.INCLUDE [mobile-services-windows-phone-push-notifications-app-users](../../includes/mobile-services-windows-phone-push-notifications-app-users.md)]


##<a name="test"></a>Test de l'application

[AZURE.INCLUDE [mobile-services-windows-test-push-users](../../includes/mobile-services-windows-test-push-users.md)]

<!-- Anchors. -->
[Updating the service to require authentication for registration]: #register
[Updating the app to log in before registration]: #update-app
[Testing the app]: #test
[Next Steps]: #next-steps


<!-- URLs. -->
[Ajout de l’authentification à votre application]: mobile-services-dotnet-backend-windows-phone-get-started-users.md
[Ajout de notifications Push à votre application]: mobile-services-dotnet-backend-windows-phone-get-started-push.md

[Azure Management Portal]: https://manage.windowsazure.com/

<!---HONumber=September15_HO1-->