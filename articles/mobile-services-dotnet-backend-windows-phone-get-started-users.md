<properties pageTitle="Get started with authentication (Windows Phone) | Mobile Dev Center" metaKeywords="authentication, FAcebook, GOogle, Twitter, Microsoft Account, login" description="Learn how to use Mobile Services to authenticate users of your Windows Phone app through a variety of identity providers, including Google, Facebook, Twitter, and Microsoft." metaCanonical="" services="mobile" documentationCenter="Mobile" title="Get started with authentication in Mobile Services" authors="Glenn Gailey" solutions="" manager="" editor="" />

Prise en main de l'authentification dans Mobile Services
========================================================

[Windows Store C\#](/en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-users "Windows Store C#")[Windows Store JavaScript](/en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-users "Windows Store JavaScript")[Windows Phone](/en-us/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-users "Windows Phone")[iOS](/en-us/documentation/articles/mobile-services-dotnet-backend-ios-get-started-users "iOS")
[.NET backend](/en-us/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-users/ ".NET backend") | [JavaScript backend](/en-us/documentation/articles/mobile-services-windows-phone-get-started-users/ "JavaScript backend")

Cette rubrique montre comment authentifier les utilisateurs dans Azure Mobile Services à partir d'une application Windows Phone. Dans ce didacticiel, vous allez ajouter l'authentification au projet de démarrage rapide à l'aide d'un fournisseur d'identité pris en charge par Mobile Services. Après avoir été authentifiée et autorisée par Mobile Services, la valeur de l'ID utilisateur s'affiche.

Ce didacticiel vous familiarise avec les étapes de base permettant d'activer l'authentification dans votre application :

1.  [Inscription de votre application pour l'authentification et configuration de Mobile Services](#register)
2.  [Restriction des autorisations de table pour les utilisateurs authentifiés](#permissions)
3.  [Ajout de l'authentification à l'application](#add-authentication)

Ce didacticiel est basé sur le démarrage rapide de Mobile Services. Vous devez aussi commencer par suivre le didacticiel [Prise en main de Mobile Services](/en-us/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started/).

> [WACOM.NOTE] Ce didacticiel présente la méthode de base fournie par Mobile Services pour authentifier les utilisateurs à l'aide de différents fournisseurs d'identité. Cette méthode est facilement configurable et prend en charge plusieurs fournisseurs. Elle requiert toutefois que les utilisateurs se connectent à chaque fois que l'application démarre. Pour utiliser plutôt Live Connect afin de fournir une authentification unique dans votre application Windows Phone, consultez la rubrique [Authentification unique pour les applications Windows Phone à l'aide de Live Connect](/en-us/documentation/articles/mobile-services-windows-phone-single-sign-on).

Inscription de votre application pour l'authentification et configuration de Mobile Services
--------------------------------------------------------------------------------------------

[WACOM.INCLUDE [mobile-services-register-authentication](../includes/mobile-services-register-authentication.md)]

1.  Dans Visual Studio, ouvrez le fichier Web.config du projet de service mobile, et dans la section appSettings, définissez les valeurs de l'identificateur d'application et de la clé secrète partagée fournies par votre fournisseur d'identité.

    Ces paramètres sont utilisés pendant le développement local. Une fois votre projet de service mobile publié sur Azure, ces paramètres sont écrasés par les valeurs définies dans le portail.

Restriction des autorisations pour les utilisateurs authentifiés
----------------------------------------------------------------

[WACOM.INCLUDE [mobile-services-restrict-permissions-dotnet-backend](../includes/mobile-services-restrict-permissions-dotnet-backend.md)]

1.  Dans Visual Studio 2013, ouvrez le projet que vous avez créé lorsque vous avez suivi le didacticiel [Prise en main de Mobile Services](/en-us/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started/).

2.  Appuyez sur la touche F5 pour exécuter cette application basée sur le démarrage rapide ; vérifiez qu'une exception non prise en charge avec le code d'état 401 (Non autorisé) est générée après le démarrage de l'application.

    Celle-ci se produit car l'application tente d'accéder à Mobile Services en tant qu'utilisateur non authentifié, mais la table *TodoItem* requiert désormais l'authentification.

Ensuite, vous allez mettre à jour l'application pour authentifier les utilisateurs avant de demander des ressources à partir du service mobile.

Ajout de l'authentification à l'application
-------------------------------------------

[WACOM.INCLUDE [mobile-services-windows-phone-authenticate-app](../includes/mobile-services-windows-phone-authenticate-app.md)]

Étapes suivantes
----------------

Dans le didacticiel suivant, [Autorisation côté service des utilisateurs Mobile Services](/en-us/documentation/articles/mobile-services-dotnet-backend-windows-phone-authorize-users-in-scripts), vous allez prendre la valeur d'ID utilisateur fournie par Mobile Services sur la base d'un utilisateur authentifié et l'utiliser pour filtrer les données renvoyées par Mobile Services. Pour plus d'informations sur Mobile Services avec .NET, consultez le [Guide de fonctionnement Mobile Services .NET.](/en-us/develop/mobile/how-to-guides/work-with-net-client-library)

