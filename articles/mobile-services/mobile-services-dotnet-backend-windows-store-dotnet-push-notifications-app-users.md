<properties 
	pageTitle="Envoi de notifications Push aux utilisateurs authentifiés (application Windows 8.1 universelle) | Azure Mobile Services" 
	description="Découvrez comment utiliser Azure Mobile Services pour envoyer des notifications Push à un utilisateur authentifié particulier qui exécute votre application Windows 8.1 universelle." 
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
	ms.date="07/01/2015" 
	ms.author="glenga"/>

# Envoi de notifications Push aux utilisateurs authentifiés

[AZURE.INCLUDE [mobile-services-selector-push-users](../../includes/mobile-services-selector-push-users.md)]

##Vue d'ensemble

Cette rubrique montre comment envoyer des notifications Push à un utilisateur authentifié sur un appareil inscrit. À l'inverse du précédent didacticiel relatif aux [notifications Push][Get started with push notifications], celui-ci modifie votre service mobile pour exiger l'authentification des utilisateurs avant que le client puisse s'inscrire auprès du concentrateur de notification pour les notifications Push. L'inscription est également modifiée pour ajouter une balise basée sur l'ID d'utilisateur affecté. Enfin, le code du serveur est mis à jour pour envoyer la notification uniquement à l'utilisateur authentifié, et non à toutes les inscriptions.
 
Ce didacticiel prend en charge les applications Windows Store et Windows Phone Store.

##Configuration requise 

Avant de commencer ce didacticiel, vous devez suivre les didacticiels Mobile Services suivants :

+ [Prise en main de l'authentification]<br/>Ajoute une exigence de connexion à l'exemple d'application TodoList.

+ [Prise en main des notifications Push]<br/>Configure l'exemple d'application TodoList pour les notifications Push à l'aide de Notification Hubs.

Une fois ces deux didacticiels terminés, vous saurez comment empêcher les utilisateurs non authentifiés de s'inscrire pour recevoir les notifications Push de votre service mobile.

##<a name="register"></a>Mise à jour du service pour demander l'authentification pour l'inscription

[AZURE.INCLUDE [mobile-services-dotnet-backend-push-notifications-app-users](../../includes/mobile-services-dotnet-backend-push-notifications-app-users.md)]

##<a name="update-app"></a>Mise à jour de l'application pour se connecter avant l'inscription

[AZURE.INCLUDE [mobile-services-windows-store-dotnet-push-notifications-app-users](../../includes/mobile-services-windows-store-dotnet-push-notifications-app-users.md)]

##<a name="test"></a>Test de l'application

[AZURE.INCLUDE [mobile-services-windows-test-push-users](../../includes/mobile-services-windows-test-push-users.md)]



<!-- Anchors. -->
[Updating the service to require authentication for registration]: #register
[Updating the app to log in before registration]: #update-app
[Testing the app]: #test
[Next Steps]: #next-steps


<!-- URLs. -->
[Prise en main de l'authentification]: ../mobile-services-dotnet-backend-windows-store-dotnet-get-started-users.md
[Get started with push notifications]: ../mobile-services-dotnet-backend-windows-store-dotnet-get-started-push.md
[Prise en main des notifications Push]: ../mobile-services-dotnet-backend-windows-store-dotnet-get-started-push.md

[Azure Management Portal]: https://manage.windowsazure.com/

 

<!---HONumber=July15_HO2-->