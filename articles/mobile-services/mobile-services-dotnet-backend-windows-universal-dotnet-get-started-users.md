<properties 
	pageTitle="Ajout de l’authentification à votre application Windows 8.1 universelle | Microsoft Azure" 
	description="Découvrez comment utiliser Mobile Services pour authentifier les utilisateurs de votre application Windows 8.1 universelle en utilisant divers fournisseurs d’identité, notamment Google, Facebook, Twitter et Microsoft." 
	services="mobile-services" 
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
	ms.date="10/05/2015" 
	ms.author="glenga"/>

# Ajout de l'authentification à votre application Mobile Services 

[AZURE.INCLUDE [mobile-services-selector-get-started-users](../../includes/mobile-services-selector-get-started-users.md)]

## Vue d'ensemble

Cette rubrique explique comment authentifier les utilisateurs dans Azure Mobile Services à partir d'une application Windows universelle. Dans ce didacticiel, vous allez ajouter l'authentification au projet de démarrage rapide à l'aide d'un fournisseur d'identité pris en charge par Mobile Services. Après avoir été authentifiée et autorisée par Mobile Services, la valeur de l'ID utilisateur s'affiche.

Ce didacticiel est basé sur le démarrage rapide de Mobile Services. Vous devez d’abord suivre le didacticiel [Prise en main de Mobile Services] ou le didacticiel [Ajout de Mobile Services à une application existante](mobile-services-dotnet-backend-windows-universal-dotnet-get-started-data.md).

>[AZURE.NOTE]Ce didacticiel vous explique comment effectuer une authentification dirigée vers le serveur des utilisateurs dans les applications Windows Store et Windows Phone Store 8.1. Pour plus d’informations sur l’authentification dirigée vers le client, consultez la rubrique [Connexion à Azure Mobile Services avec les Kits de développement logiciel (SDK) Google, Microsoft et Facebook](http://azure.microsoft.com/blog/2014/10/27/logging-in-with-google-microsoft-and-facebook-sdks-to-azure-mobile-services/).

##<a name="register"></a>Inscrire votre application pour l'authentification et configurer Mobile Services

[AZURE.INCLUDE [mobile-services-register-authentication](../../includes/mobile-services-register-authentication.md)]

[AZURE.INCLUDE [mobile-services-dotnet-backend-aad-server-extension](../../includes/mobile-services-dotnet-backend-aad-server-extension.md)]

##<a name="permissions"></a>Restriction des autorisations pour les utilisateurs authentifiés

[AZURE.INCLUDE [mobile-services-restrict-permissions-dotnet-backend](../../includes/mobile-services-restrict-permissions-dotnet-backend.md)]

&nbsp;&nbsp;6. Dans Visual Studio, cliquez avec le bouton droit de la souris sur le projet Windows Store pour l'application TodoList, puis cliquez sur **Définir comme projet de démarrage**.

&nbsp;&nbsp;7. Dans le projet partagé, ouvrez le fichier de projet App.xaml.cs, accédez à la définition [MobileServiceClient](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.aspx) et assurez-vous qu'elle est configurée pour une connexion au service mobile exécuté dans Azure.

>[AZURE.NOTE]Lorsque vous utilisez les outils Visual Studio pour connecter votre application à un service mobile, l'outil génère deux ensembles de définitions **MobileServiceClient**, une pour chaque plateforme cliente. C'est le bon moment pour simplifier le code généré en unifiant les définitions **MobileServiceClient** encapsulées dans `#if...#endif` en une seule définition encapsulée, utilisée par les deux versions de l'application. Cette opération n'est pas nécessaire si vous avez téléchargé l'application de démarrage rapide à partir du portail de gestion Azure.

&nbsp;&nbsp;8. Appuyez sur la touche F5 pour exécuter l'application Windows Store, et vérifiez qu'une exception non prise en charge avec le code d'état 401 (Unauthorized) est déclenchée après le démarrage de l'application.
   
&nbsp;&nbsp;Cela se produit, car l’application essaie d’accéder à Mobile Services en tant qu’utilisateur non authentifié, mais la table *TodoItem* nécessite désormais l’authentification.

Ensuite, vous allez mettre à jour l'application pour authentifier les utilisateurs avant de demander des ressources à partir du service mobile.

##<a name="add-authentication"></a>Ajout de l'authentification à l'application

[AZURE.INCLUDE [mobile-services-windows-universal-dotnet-authenticate-app](../../includes/mobile-services-windows-universal-dotnet-authenticate-app.md)]

>[AZURE.NOTE]Si vous avez inscrit les informations du package de l'application Windows Store avec Mobile Services, vous devez appeler la méthode <a href="http://go.microsoft.com/fwlink/p/?LinkId=311594" target="_blank">LoginAsync</a> en fournissant la valeur **true** pour le paramètre *useSingleSignOn*. Si vous ne le faites pas, vos utilisateurs seront toujours invités à se connecter à chaque appel de la méthode de connexion.

##<a name="tokens"></a>Stockage de jetons d'authentification sur le client

[AZURE.INCLUDE [mobile-services-windows-store-dotnet-authenticate-app-with-token](../../includes/mobile-services-windows-store-dotnet-authenticate-app-with-token.md)]


## <a name="next-steps"> </a>Étapes suivantes

Dans le didacticiel suivant, [Autorisation côté service des utilisateurs Mobile Services][Authorize users with scripts], vous allez prendre la valeur d'ID utilisateur fournie par Mobile Services sur la base d'un utilisateur authentifié et l'utiliser pour filtrer les données renvoyées par Mobile Services.

##Voir aussi

+ [Fonctionnalité des utilisateurs améliorée](http://azure.microsoft.com/blog/2014/10/02/custom-login-scopes-single-sign-on-new-asp-net-web-api-updates-to-the-azure-mobile-services-net-backend/)<br/> Vous pouvez obtenir d’autres données utilisateur gérées par le fournisseur d’identité dans votre service mobile en appelant la méthode **ServiceUser.GetIdentitiesAsync()** dans un serveur principal .NET. 

+ [Guide de fonctionnement Mobile Services .NET] <br/>En savoir plus sur l'utilisation de Mobile Services avec un client .NET.


<!-- Anchors. -->
[Register your app for authentication and configure Mobile Services]: #register
[Restrict table permissions to authenticated users]: #permissions
[Add authentication to the app]: #add-authentication
[Store authentication tokens on the client]: #tokens
[Next Steps]: #next-steps


<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Prise en main de Mobile Services]: mobile-services-dotnet-backend-windows-store-dotnet-get-started.md
[Get started with data]: ../mobile-services-dotnet-backend-windows-store-dotnet-get-started-data.md
[Get started with authentication]: ../mobile-services-dotnet-backend-windows-store-dotnet-get-started-users.md
[Get started with push notifications]: ../mobile-services-dotnet-backend-windows-store-dotnet-get-started-push.md
[Authorize users with scripts]: ../mobile-services-dotnet-backend-windows-store-dotnet-authorize-users-in-scripts.md
[JavaScript and HTML]: ../mobile-services-dotnet-backend-windows-store-javascript-get-started-users.md

[Azure Management Portal]: https://manage.windowsazure.com/
[Guide de fonctionnement Mobile Services .NET]: mobile-services-windows-dotnet-how-to-use-client-library.md
[Register your Windows Store app package for Microsoft authentication]: ../mobile-services-how-to-register-store-app-package-microsoft-authentication.md
 

<!---HONumber=Oct15_HO3-->