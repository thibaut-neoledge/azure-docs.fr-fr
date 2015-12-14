<properties
	pageTitle="Ajout de l’authentification sur iOS avec Azure Mobile Apps"
	description="Découvrez comment utiliser Azure Mobile Apps pour authentifier les utilisateurs de votre application iOS via divers fournisseurs d'identité, notamment AAD, Google, Facebook, Twitter et Microsoft."
	services="app-service\mobile"
	documentationCenter="ios"
	authors="krisragh" 
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="11/30/2015"
	ms.author="krisragh"/>

# Ajout de l'authentification à votre application iOS

[AZURE.INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]
&nbsp;  
[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

Dans ce didacticiel, vous allez ajouter l’authentification au projet de [Démarrage rapide iOS] en faisant appel à un fournisseur d’identité pris en charge. Ce didacticiel est basé sur le didacticiel [Démarrage rapide iOS], que vous devez effectuer en premier. Si vous n’utilisez pas le projet de serveur du démarrage rapide téléchargé, vous devez ajouter le package d’extension d’authentification à votre projet. Pour plus d'informations sur les packages d'extension de serveur, consultez [Fonctionnement avec le Kit de développement logiciel (SDK) du serveur principal .NET pour Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

##<a name="register"></a>Inscription de votre application pour l’authentification et configuration d’App Service

[AZURE.INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

##<a name="permissions"></a>Restriction des autorisations pour les utilisateurs authentifiés

[AZURE.INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

Dans Xcode, appuyez sur **Exécuter** pour démarrer l’application. Une exception se déclenche, car l’application essaye d’accéder au serveur principal en tant qu’utilisateur non authentifié alors que la table _TodoItem_ requiert désormais l’authentification.

##<a name="add-authentication"></a>Ajout de l'authentification à l'application

[AZURE.INCLUDE [app-service-mobile-ios-authenticate-app](../../includes/app-service-mobile-ios-authenticate-app.md)]


<!-- URLs. -->

[Démarrage rapide iOS]: app-service-mobile-ios-get-started.md

[Azure portal]: https://portal.azure.com
 

<!---HONumber=AcomDC_1203_2015--->