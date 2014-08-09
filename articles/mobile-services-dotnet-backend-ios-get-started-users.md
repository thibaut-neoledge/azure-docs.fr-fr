<properties pageTitle="Get started with authentication (Windows Store) | Mobile Dev Center" metaKeywords="authentication, Facebook, Google, Twitter, Microsoft Account, login" description="Learn how to use Mobile Services to authenticate users of your Windows Store app through a variety of identity providers, including Google, Facebook, Twitter, and Microsoft." metaCanonical="" services="mobile" documentationCenter="Mobile" title="Get started with authentication in Mobile Services" authors="Glenn Gailey" solutions="" manager="" editor="" />

Prise en main de l'authentification dans Mobile Services
========================================================

[Windows Store C\#](/fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-users "Windows Store C#")[Windows Store JavaScript](/fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-users "Windows Store JavaScript")[Windows Phone](/fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-users "Windows Phone")[iOS](/fr-fr/documentation/articles/mobile-services-dotnet-backend-ios-get-started-users "iOS")

[.NET backend](/fr-fr/documentation/articles/mobile-services-dotnet-backend-ios-get-started-users/ ".NET backend") | [JavaScript backend](/fr-fr/documentation/articles/mobile-services-ios-get-started-users/ "JavaScript backend")

Cette rubrique montre comment authentifier les utilisateurs dans Azure Mobile Services à partir de votre application. Dans ce didacticiel, vous allez ajouter l'authentification au projet de démarrage rapide à l'aide d'un fournisseur d'identité pris en charge par Mobile Services. Après avoir été authentifiée et autorisée par Mobile Services, la valeur de l'ID utilisateur s'affiche.

Ce didacticiel vous familiarise avec les étapes de base permettant d'activer l'authentification dans votre application :

1.  [Inscription de votre application pour l'authentification et configuration de Mobile Services]
2.  [Restriction des autorisations de table pour les utilisateurs authentifiés]
3.  [Ajout de l'authentification à l'application]

Ce didacticiel est basé sur le démarrage rapide de Mobile Services. Vous devez aussi d'abord suivre le didacticiel [Prise en main de Mobile Services].

Inscription de votre application pour l'authentification et configuration de Mobile Services
--------------------------------------------------------------------------------------------

[WACOM.INCLUDE [mobile-services-register-authentication](../includes/mobile-services-register-authentication.md)]

1.  Dans Visual Studio, ouvrez le fichier Web.config du projet de service mobile, et dans la section appSettings, définissez les valeurs de l'identificateur d'application et de la clé secrète partagée fournies par votre fournisseur d'identité.

    Ces paramètres sont utilisés pendant le développement local. Une fois votre projet de service mobile publié sur Azure, ces paramètres sont écrasés par les valeurs définies dans le portail.

Restriction des autorisations pour les utilisateurs authentifiés
----------------------------------------------------------------

[WACOM.INCLUDE [mobile-services-restrict-permissions-dotnet-backend](../includes/mobile-services-restrict-permissions-dotnet-backend.md)]

1.  Dans Xcode, ouvrez le projet que vous avez créé avec le didacticiel [Prise en main de Mobile Services](/fr-fr/documentation/articles/mobile-services-ios-get-started).

2.  Appuyez sur le bouton **Exécuter** pour générer le projet et démarrer l'application dans l'émulateur iPhone ; vérifiez qu'une exception non prise en charge avec le code d'état 401 (Non autorisé) est générée après le démarrage de l'application.

    Celle-ci se produit car l'application tente d'accéder à Mobile Services en tant qu'utilisateur non authentifié, mais la table *TodoItem* requiert désormais l'authentification.

    Ensuite, vous allez mettre à jour l'application pour authentifier les utilisateurs avant de demander des ressources à partir du service mobile. ##Ajout de l'authentification à l'application 

[WACOM.INCLUDE [mobile-services-ios-authenticate-app](../includes/mobile-services-ios-authenticate-app.md)]

## Étapes suivantes Dans le didacticiel suivant, 

[Autorisation côté service des utilisateurs Mobile Services][Autorisation des utilisateurs avec des scripts], vous allez prendre la valeur d'ID utilisateur fournie par Mobile Services sur la base d'un utilisateur authentifié et l'utiliser pour filtrer les données renvoyées par Mobile Services. 


<!-- Anchors. -->
[Inscription de votre application pour l'authentification et configuration de Mobile Services]: #register 
[Restriction des autorisations de table pour les utilisateurs authentifiés]: #permissions 
[Ajout de l'authentification à l'application]: #add-authentication 
[Étapes suivantes]: #next-steps 

<!-- URLs. -->
[Prise en main de Mobile Services]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-ios-get-started/ 
[Prise en main des données]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-ios-get-started-data/ 
[Prise en main de l'authentification]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-ios-get-started-users/ 
[Prise en main des notifications Push]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-ios-get-started-push/ 
[Autorisation des utilisateurs avec des scripts]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-ios-authorize-users-in-scripts 
[Portail de gestion Azure]: https://manage.windowsazure.com/ 
[Guide de fonctionnement Mobile Services .NET]: /en-us/develop/mobile/how-to-guides/work-with-net-client-library 
[Inscription du package de votre application Windows Store pour l'authentification Microsoft]: /fr-fr/documentation/articles/mobile-services-how-to-register-store-app-package-microsoft-authentication


