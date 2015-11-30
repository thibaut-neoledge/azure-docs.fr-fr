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
	ms.date="11/17/2015"
	ms.author="glenga"/>

# Ajout de l'authentification à votre application Windows

[AZURE.INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]
&nbsp;  
[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

Cette rubrique montre comment authentifier les utilisateurs d'une application App Service Mobile App à partir de votre application cliente. Dans ce didacticiel, vous allez ajouter l'authentification au projet de démarrage rapide à l'aide d'un fournisseur d'identité pris en charge par App Service. Une fois l'utilisateur authentifié et autorisé par votre application Mobile App, la valeur de l'ID utilisateur s'affiche.

Ce didacticiel est basé sur le démarrage rapide de Mobile App. Vous devez commencer par suivre le didacticiel [Prise en main de votre application mobile].

##<a name="register"></a>Inscription de votre application pour l’authentification et configuration d’App Service

[AZURE.INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

##<a name="permissions"></a>Restriction des autorisations pour les utilisateurs authentifiés

[AZURE.INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

&nbsp;&nbsp;4. Dans Visual Studio, ouvrez le fichier de projet partagé App.xaml.cs dans votre projet d’application cliente et assurez-vous que l’instance **MobileServiceClient** est configurée pour utiliser l’URL du serveur principal de Mobile App et la passerelle.

&nbsp;&nbsp;5. Avec l’un des projets d’application Windows défini en tant que projet de démarrage, appuyez sur la touche F5 pour exécuter l'application, et vérifiez qu'une exception non prise en charge avec le code d'état 401 (Non autorisé) est déclenchée après le démarrage de l'application.

Cette exception se produit, car l’application tente d’accéder à votre code Mobile App en tant qu’utilisateur non authentifié alors que la table *TodoItem* requiert désormais une authentification.

Ensuite, vous allez mettre à jour l'application pour authentifier les utilisateurs avant de demander des ressources à partir de votre service App Service.

##<a name="add-authentication"></a>Ajout de l’authentification à l’application

[AZURE.INCLUDE [app-service-mobile-windows-universal-dotnet-authenticate-app](../../includes/app-service-mobile-windows-universal-dotnet-authenticate-app.md)]


##<a name="tokens"></a>Stockage du jeton d’authentification sur le client

[AZURE.INCLUDE [app-service-mobile-windows-store-dotnet-authenticate-app-with-token](../../includes/app-service-mobile-windows-store-dotnet-authenticate-app-with-token.md)]

##Étapes suivantes


<!-- URLs. -->
[Prise en main de votre application mobile]: app-service-mobile-windows-store-dotnet-get-started.md
 

<!---HONumber=Nov15_HO4-->