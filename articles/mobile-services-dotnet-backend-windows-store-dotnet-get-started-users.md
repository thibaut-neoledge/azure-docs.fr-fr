<properties pageTitle="Get started with authentication (Windows Store) | Mobile Dev Center" metaKeywords="authentication, FAcebook, GOogle, Twitter, Microsoft Account, login" description="Learn how to use Mobile Services to authenticate users of your Windows Store app through a variety of identity providers, including Google, Facebook, Twitter, and Microsoft." metaCanonical="" services="mobile" documentationCenter="Mobile" title="Get started with authentication in Mobile Services" authors="Glenn Gailey" solutions="" manager="" editor="" />

Prise en main de l'authentification dans Mobile Services
========================================================

[Windows Store C\#](/fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-users "Windows Store C#") [Windows Store JavaScript](/fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-users "Windows Store JavaScript") [Windows Phone](/fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-users "Windows Phone")
[.NET backend](/fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-users/ ".NET backend") | [JavaScript backend](/fr-fr/documentation/articles/mobile-services-windows-store-dotnet-get-started-users/ "JavaScript backend")

Cette rubrique montre comment authentifier les utilisateurs dans Azure Mobile Services à partir de votre application. Dans ce didacticiel, vous allez ajouter l'authentification au projet de démarrage rapide à l'aide d'un fournisseur d'identité pris en charge par Mobile Services. Après avoir été authentifiée et autorisée par Mobile Services, la valeur de l'ID utilisateur s'affiche.

Ce didacticiel vous familiarise avec les étapes de base permettant d'activer l'authentification dans votre application :

1.  [Inscription de votre application pour l'authentification et configuration de Mobile Services](#register)
2.  [Restriction des autorisations de table pour les utilisateurs authentifiés](#permissions)
3.  [Ajout de l'authentification à l'application](#add-authentication)

Ce didacticiel est basé sur le démarrage rapide de Mobile Services. Vous devez aussi commencer par suivre le didacticiel [Prise en main de Mobile Services](/fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started/).

> [WACOM.NOTE] Ce didacticiel présente la méthode de base fournie par Mobile Services pour authentifier les utilisateurs à l'aide de différents fournisseurs d'identité. Cette méthode est facilement configurable et prend en charge plusieurs fournisseurs. Elle requiert toutefois que les utilisateurs se connectent à chaque fois que l'application démarre. Pour utiliser plutôt Live Connect afin de fournir une authentification unique dans votre application Windows Store, consultez la rubrique [Authentification unique pour les applications Windows Store à l'aide de Live Connect](/fr-fr/documentation/articles/mobile-services-windows-store-dotnet-single-sign-on).

Inscription de votre application pour l'authentification et configuration de Mobile Services
--------------------------------------------------------------------------------------------

[WACOM.INCLUDE [mobile-services-register-authentication](../includes/mobile-services-register-authentication.md)]

1.  Dans Visual Studio, ouvrez le fichier Web.config du projet de service mobile, et dans la section appSettings, définissez les valeurs de l'identificateur d'application et de la clé secrète partagée fournies par votre fournisseur d'identité.

    Ces paramètres sont utilisés pendant le développement local. Une fois votre projet de service mobile publié sur Azure, ces paramètres sont écrasés par les valeurs définies dans le portail.

2.  (Facultatif) Suivez la procédure décrite dans la rubrique [Inscription du package de votre application Windows Store pour l'authentification Microsoft](/fr-fr/documentation/articles/mobile-services-how-to-register-store-app-package-microsoft-authentication/).

    **Remarque**

    Cette étape est facultative, car elle concerne uniquement le fournisseur de connexion du compte Microsoft. Lorsque vous inscrivez les informations du package de votre application Windows Store auprès de Mobile Services, le client peut réutiliser les informations d'identification du compte Microsoft pour fournir une authentification unique. Si vous ne le faites pas, vos utilisateurs se connectant via le compte Microsoft seront invités à se connecter à chaque appel de la méthode de connexion. Suivez cette étape si vous prévoyez d'utiliser le fournisseur d'identité du compte Microsoft.

Restriction des autorisations pour les utilisateurs authentifiés
----------------------------------------------------------------

[WACOM.INCLUDE [mobile-services-restrict-permissions-dotnet-backend](../includes/mobile-services-restrict-permissions-dotnet-backend.md)]

1.  Dans Visual Studio 2013, ouvrez le projet que vous avez créé lorsque vous avez suivi le didacticiel [Prise en main de Mobile Services](/fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started/).

2.  Appuyez sur la touche F5 pour exécuter cette application basée sur le démarrage rapide ; vérifiez qu'une exception non prise en charge avec le code d'état 401 (Non autorisé) est générée après le démarrage de l'application.

    Celle-ci se produit car l'application tente d'accéder à Mobile Services en tant qu'utilisateur non authentifié, mais la table *TodoItem* requiert désormais l'authentification.

Ensuite, vous allez mettre à jour l'application pour authentifier les utilisateurs avant de demander des ressources à partir du service mobile.

Ajout de l'authentification à l'application
-------------------------------------------

[WACOM.INCLUDE [mobile-services-windows-dotnet-authenticate-app](../includes/mobile-services-windows-dotnet-authenticate-app.md)]

> [WACOM.NOTE] Si vous avez enregistré les informations du package de l'application Windows Store avec Mobile Services, vous devez appeler la méthode [LoginAsync](http://go.microsoft.com/fwlink/p/?LinkId=311594) en fournissant la valeur **true** pour le paramètre *useSingleSignOn*. Si vous ne le faites pas, vos utilisateurs seront toujours invités à se connecter à chaque appel de la méthode de connexion.

Étapes suivantes
----------------

Dans le didacticiel suivant, [Autorisation côté service des utilisateurs Mobile Services](/fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-authorize-users-in-scripts), vous allez prendre la valeur d'ID utilisateur fournie par Mobile Services sur la base d'un utilisateur authentifié et l'utiliser pour filtrer les données renvoyées par Mobile Services. Pour plus d'informations sur Mobile Services avec .NET, consultez le [Guide de fonctionnement Mobile Services .NET.](/en-us/develop/mobile/how-to-guides/work-with-net-client-library)

