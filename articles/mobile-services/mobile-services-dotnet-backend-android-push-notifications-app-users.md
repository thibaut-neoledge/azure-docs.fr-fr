<properties 
	pageTitle="Envoi de notifications Push à des utilisateurs authentifiés" 
	description="Découvrez comment envoyer des notifications Push à" 
	services="mobile-services,notification-hubs" 
	documentationCenter="android" 
	authors="wesmc7777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="" 
	ms.devlang="java" 
	ms.topic="article" 
	ms.date="02/23/2015" 
	ms.author="wesmc"/>

# Envoi de notifications Push à des utilisateurs authentifiés

[AZURE.INCLUDE [mobile-services-selector-push-users](../../includes/mobile-services-selector-push-users.md)]

## Vue d'ensemble

Cette rubrique montre comment envoyer des notifications Push à un utilisateur authentifié sur un appareil inscrit. Contrairement au précédent didacticiel relatif aux [notifications Push][Prise en main des notifications Push], celui-ci modifie votre service mobile pour exiger l'authentification des utilisateurs avant que le client puisse s'inscrire auprès du concentrateur de notification pour les notifications Push. L'inscription est également modifiée pour ajouter une balise basée sur l'ID d'utilisateur affecté. Enfin, le code du serveur est mis à jour pour envoyer la notification uniquement à l'utilisateur authentifié, et non à toutes les inscriptions.


Ce didacticiel prend en charge les applications Android.

## Configuration requise 

Avant de commencer ce didacticiel, vous devez suivre les didacticiels Mobile Services suivants :

+ [Ajout de l'authentification à votre application Mobile Services]<br/>Ajoute une exigence de connexion à l'exemple d'application TodoList.

+ [Prise en main des notifications Push]<br/>Configure l'exemple d'application TodoList pour utiliser les notifications Push avec Notification Hubs. 

Une fois ces deux didacticiels terminés, vous saurez comment empêcher les utilisateurs non authentifiés de s'inscrire pour recevoir les notifications Push de votre service mobile.

## Mise à jour du service pour exiger l'authentification pour l'inscription

[AZURE.INCLUDE [mobile-services-dotnet-backend-push-notifications-app-users](../../includes/mobile-services-dotnet-backend-push-notifications-app-users.md)] 

## Mise à jour de l'application pour se connecter avant l'inscription

[AZURE.INCLUDE [mobile-services-android-push-notifications-app-users](mobile-services-android-push-notifications-app-users.md)] 

## Test de l'application

[AZURE.INCLUDE [mobile-services-android-test-push-users](../../includes/mobile-services-android-test-push-users.md)] 


<!---##Next steps

In the next tutorial, [Service-side authorization of Mobile Services users][Authorize users with scripts], you will take the user ID value provided by Mobile Services based on an authenticated user and use it to filter the data returned by Mobile Services. Learn more about how to use Mobile Services with .NET in [Mobile Services .NET How-to Conceptual Reference]-->


<!-- URLs. -->
[Ajout de l'authentification à votre application Mobile Services]: /documentation/articles/mobile-services-dotnet-backend-android-get-started-users/
[Prise en main des notifications Push]: /documentation/articles/mobile-services-dotnet-backend-android-get-started-push/

[Portail de gestion Azure]: https://manage.windowsazure.com/
[Guide de fonctionnement Mobile Services .NET]: /develop/mobile/how-to-guides/work-with-net-client-library

<!--HONumber=47-->
 