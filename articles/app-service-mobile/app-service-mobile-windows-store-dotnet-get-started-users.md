<properties
	pageTitle="Ajout de l’authentification à votre application Windows Runtime 8.1 universelle | Azure Mobile Apps"
	description="Découvrez comment utiliser Azure App Service Mobile Apps pour authentifier les utilisateurs de votre application Windows à l’aide de divers fournisseurs d'identité, notamment AAD, Google, Facebook, Twitter et Microsoft."
	services="app-service\mobile"
	documentationCenter="windows"
	authors="mattchenderson" 
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-windows"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="11/23/2015"
	ms.author="glenga"/>

# Ajout de l'authentification à votre application Windows

[AZURE.INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]
&nbsp;  
[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

Cette rubrique indique comment ajouter l'authentification basée sur le cloud à votre application mobile. Dans ce didacticiel, vous ajoutez l'authentification au projet de démarrage rapide Mobile Apps à l'aide d'un fournisseur d'identité pris en charge par Azure App Service. Une fois l'utilisateur authentifié et autorisé par votre backend d'application Mobile, la valeur de l'ID utilisateur s'affiche.

Ce didacticiel est basé sur le démarrage rapide de Mobile Apps. Vous devez commencer par suivre le didacticiel [Prise en main de Mobile Apps](app-service-mobile-windows-store-dotnet-get-started.md).

##<a name="register"></a>Inscription de votre application pour l’authentification et configuration d’App Service

[AZURE.INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

##<a name="permissions"></a>Restriction des autorisations pour les utilisateurs authentifiés

[AZURE.INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

Avec l’un des projets d’application Windows défini en tant que projet de démarrage, appuyez sur la touche F5 pour exécuter l'application, et vérifiez qu'une exception non prise en charge avec le code d'état 401 (Non autorisé) est déclenchée après le démarrage de l'application. Cette exception se produit, car l’application tente d’accéder à votre code Mobile App en tant qu’utilisateur non authentifié, alors que la table *TodoItem* requiert désormais une authentification.

Ensuite, vous allez mettre à jour l'application pour authentifier les utilisateurs avant de demander des ressources à partir de votre service App Service.

##<a name="add-authentication"></a>Ajout de l’authentification à l’application

[AZURE.INCLUDE [mobile-windows-universal-dotnet-authenticate-app](../../includes/mobile-windows-universal-dotnet-authenticate-app.md)]


##<a name="tokens"></a>Stockage du jeton d’authentification sur le client

L’exemple précédent montrait une connexion standard, qui nécessite que le client contacte le fournisseur d’identité et le service d’application à chaque démarrage de l’application. Cette méthode est non seulement inefficace, mais vous pouvez rencontrer des problèmes d'utilisation si de nombreux clients tentent de lancer votre application en même temps. Une meilleure approche consiste à mettre en cache le jeton d’autorisation renvoyé par votre service d’application et à l’utiliser en premier avant de faire appel à la connexion via un fournisseur.

>[AZURE.NOTE]Vous pouvez mettre en cache le jeton émis par App Services, que vous utilisiez l’authentification gérée par un client ou gérée par un service. Ce didacticiel utilise cette dernière.

[AZURE.INCLUDE [mobile-windows-universal-dotnet-authenticate-app-with-token](../../includes/mobile-windows-universal-dotnet-authenticate-app-with-token.md)]

##Étapes suivantes

Maintenant que vous avez terminé ce didacticiel sur l'authentification de base, vous pouvez passer à l'un des didacticiels suivants :

+ [Ajouter des notifications Push à votre application Windows](app-service-mobile-windows-store-dotnet-get-started-push.md) Apprenez à ajouter la prise en charge des notifications push à votre application et à configurer le backend de votre application mobile pour utiliser Azure Notification Hubs afin d'envoyer des notifications push.

+ [Activation de la synchronisation hors connexion pour votre application Windows](app-service-mobile-windows-store-dotnet-get-started-offline-data.md) Apprenez à ajouter une prise en charge hors connexion à votre application à l'aide d'un backend d'application mobile. La synchronisation hors connexion permet aux utilisateurs finaux d'interagir avec une application mobile pour afficher, ajouter ou modifier des données, même lorsqu'il n'existe aucune connexion réseau.


<!-- URLs. -->
[Get started with your mobile app]: app-service-mobile-windows-store-dotnet-get-started.md
 

<!---HONumber=AcomDC_1125_2015--->