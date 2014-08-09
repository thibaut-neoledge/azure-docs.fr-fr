<properties linkid="develop-mobile-tutorials-get-started-with-users-js" urlDisplayName="Get Started with Authentication" pageTitle="Get started with authentication (JavaScript) | Mobile Dev Center" metaKeywords="" description="Learn how to use Mobile Services to authenticate users of your Windows Store JavaScript app through a variety of identity providers, including Google, Facebook, Twitter, and Microsoft." metaCanonical="http://www.windowsazure.com/fr-fr/develop/mobile/tutorials/get-started-with-users-dotnet/" services="" documentationCenter="Mobile" title="Get started with authentication in Mobile Services" authors="" solutions="" manager="" editor="" />

Prise en main de l'authentification dans Mobile Services
========================================================

[Windows Store C\#](/fr-fr/documentation/articles/mobile-services-windows-store-dotnet-get-started-users "Windows Store C#")[Windows Store JavaScript](/fr-fr/documentation/articles/mobile-services-windows-store-javascript-get-started-users "Windows Store JavaScript")[Windows Phone](/fr-fr/documentation/articles/mobile-services-windows-phone-get-started-users "Windows Phone")[iOS](/fr-fr/documentation/articles/mobile-services-ios-get-started-users "iOS")[Android](/fr-fr/documentation/articles/mobile-services-android-get-started-users "Android")[HTML](/fr-fr/documentation/articles/mobile-services-html-get-started-users "HTML")[Xamarin.iOS](/fr-fr/documentation/articles/partner-xamarin-mobile-services-ios-get-started-users "Xamarin.iOS")[Xamarin.Android](/fr-fr/documentation/articles/partner-xamarin-mobile-services-android-get-started-users "Xamarin.Android")
[.NET backend](/fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-users/ ".NET backend") | [JavaScript backend](/fr-fr/documentation/articles/mobile-services-windows-store-javascript-get-started-users/ "JavaScript backend")

Cette rubrique montre comment authentifier les utilisateurs dans Azure Mobile Services à partir de votre application. Dans ce didacticiel, vous allez ajouter l'authentification au projet de démarrage rapide à l'aide d'un fournisseur d'identité pris en charge par Mobile Services. Après avoir été authentifiée et autorisée par Mobile Services, la valeur de l'ID utilisateur s'affiche.

Ce didacticiel vous familiarise avec les étapes de base permettant d'activer l'authentification dans votre application :

1.  [Inscription de votre application pour l'authentification et configuration de Mobile Services](#register)
2.  [Restriction des autorisations de table pour les utilisateurs authentifiés](#permissions)
3.  [Ajout de l'authentification à l'application](#add-authentication)

Ce didacticiel est basé sur le démarrage rapide de Mobile Services. Vous devez aussi d'abord suivre le didacticiel [Prise en main de Mobile Services](/fr-fr/documentation/articles/mobile-services-windows-store-get-started/).

**Remarque**

Ce didacticiel présente la méthode de base fournie par Mobile Services pour authentifier les utilisateurs à l'aide de différents fournisseurs d'identité. Cette méthode est facilement configurable et prend en charge plusieurs fournisseurs. Elle requiert toutefois que les utilisateurs se connectent à chaque fois que l'application démarre. Pour utiliser plutôt Live Connect afin de fournir une authentification unique dans votre application Windows Store, consultez la rubrique [Authentification unique pour les applications Windows Store à l'aide de Live Connect](/en-us/develop/mobile/tutorials/single-sign-on-windows-8-js).

Inscription de votre application pour l'authentification et configuration de Mobile Services
--------------------------------------------------------------------------------------------

[WACOM.INCLUDE [mobile-services-register-authentication](../includes/mobile-services-register-authentication.md)]

1.  (Facultatif) Suivez la procédure décrite dans la rubrique [Inscription du package de votre application Windows Store pour l'authentification Microsoft](/fr-fr/documentation/articles/mobile-services-how-to-register-store-app-package-microsoft-authentication/).

    **Remarque**

    Cette étape est facultative, car elle concerne uniquement le fournisseur de connexion du compte Microsoft. Lorsque vous inscrivez les informations du package de votre application Windows Store auprès de Mobile Services, le client peut réutiliser les informations d'identification du compte Microsoft pour fournir une authentification unique. Si vous ne le faites pas, vos utilisateurs se connectant via le compte Microsoft seront invités à se connecter à chaque appel de la méthode de connexion. Suivez cette étape si vous prévoyez d'utiliser le fournisseur d'identité du compte Microsoft.

Votre service mobile et votre application sont désormais configurés pour utiliser le fournisseur d'authentification choisi.

Restriction des autorisations pour les utilisateurs authentifiés
----------------------------------------------------------------

[WACOM.INCLUDE [mobile-services-restrict-permissions-javascript-backend](../includes/mobile-services-restrict-permissions-javascript-backend.md)]

1.  Dans Visual Studio 2012 Express pour Windows 8, ouvrez le projet que vous avez créé avec le didacticiel [Prise en main de Mobile Services](/en-us/develop/mobile/tutorials/get-started/).

2.  Appuyez sur la touche F5 pour exécuter cette application basée sur le démarrage rapide ; vérifiez qu'une exception non prise en charge avec le code d'état 401 (Non autorisé) est générée après le démarrage de l'application.

    Cela se produit, car l'application essaye d'accéder à Mobile Services en tant qu'utilisateur non authentifié, mais la table *TodoItem* requiert désormais l'authentification.

Ensuite, vous allez mettre à jour l'application pour authentifier les utilisateurs avant de demander des ressources à partir du service mobile.

Ajout de l'authentification à l'application
-------------------------------------------

[WACOM.INCLUDE [mobile-services-windows-store-javascript-authenticate-app](../includes/mobile-services-windows-store-javascript-authenticate-app.md)]

Étapes suivantes
----------------

Dans le didacticiel suivant, [Autorisation côté service des utilisateurs Mobile Services](/fr-fr/documentation/articles/mobile-services-windows-store-javascript-authorize-users-in-scripts), vous allez prendre la valeur d'ID utilisateur fournie par Mobile Services sur la base d'un utilisateur authentifié et l'utiliser pour filtrer les données renvoyées par Mobile Services.

