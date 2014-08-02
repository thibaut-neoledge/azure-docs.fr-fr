<properties linkid="develop-mobile-tutorials-get-started-with-users-ios" urlDisplayName="Get Started with Authentication (iOS)" pageTitle="Get started with authentication (iOS) | Mobile Dev Center" metaKeywords="Azure registering application, Azure authentication, application authenticate, authenticate mobile services, Mobile Services iOS" description="Learn how to use Mobile Services to authenticate users of your iOS app through a variety of identity providers, including Google, Facebook, Twitter, and Microsoft." metaCanonical="" services="" documentationCenter="Mobile" title="Get started with authentication in Mobile Services" authors="" solutions="" manager="" editor="" />

Prise en main de l'authentification dans Mobile Services
========================================================

[Windows Store C\#](/en-us/documentation/articles/mobile-services-windows-store-dotnet-get-started-users "Windows Store C#")[Windows Store JavaScript](/en-us/documentation/articles/mobile-services-windows-store-javascript-get-started-users "Windows Store JavaScript")[Windows Phone](/en-us/documentation/articles/mobile-services-windows-phone-get-started-users "Windows Phone")[iOS](/en-us/documentation/articles/mobile-services-ios-get-started-users "iOS")[Android](/en-us/documentation/articles/mobile-services-android-get-started-users "Android")[HTML](/en-us/documentation/articles/mobile-services-html-get-started-users "HTML")[Xamarin.iOS](/en-us/documentation/articles/partner-xamarin-mobile-services-ios-get-started-users "Xamarin.iOS")[Xamarin.Android](/en-us/documentation/articles/partner-xamarin-mobile-services-android-get-started-users "Xamarin.Android")

[.NET backend](/en-us/documentation/articles/mobile-services-dotnet-backend-ios-get-started-users/ ".NET backend") | [JavaScript backend](/en-us/documentation/articles/mobile-services-ios-get-started-users/ "JavaScript backend")

Cette rubrique montre comment authentifier les utilisateurs dans Azure Mobile Services à partir de votre application iOS. Dans ce didacticiel, vous allez ajouter l'authentification au projet de démarrage rapide à l'aide d'un fournisseur d'identité pris en charge par Mobile Services. Après avoir été authentifiée et autorisée par Mobile Services, la valeur de l'ID utilisateur s'affiche.

Ce didacticiel vous familiarise avec les étapes de base permettant d'activer l'authentification dans votre application :

1.  [Inscription de votre application pour l'authentification et configuration de Mobile Services]
2.  [Restriction des autorisations de table pour les utilisateurs authentifiés]
3.  [Ajout de l'authentification à l'application]

Ce didacticiel est basé sur le démarrage rapide de Mobile Services. Vous devez aussi d'abord suivre le didacticiel [Prise en main de Mobile Services].

XCode 4.5 et iOS 5.0 ou versions ultérieures sont requis pour suivre ce didacticiel.

Inscription de votre application pour l'authentification et configuration de Mobile Services
--------------------------------------------------------------------------------------------

[WACOM.INCLUDE [mobile-services-register-authentication](../includes/mobile-services-register-authentication.md)]

Restriction des autorisations pour les utilisateurs authentifiés
----------------------------------------------------------------

[WACOM.INCLUDE [mobile-services-restrict-permissions-javascript-backend](../includes/mobile-services-restrict-permissions-javascript-backend.md)]

1.  Dans Xcode, ouvrez le projet que vous avez créé avec le didacticiel [Prise en main de Mobile Services](/en-us/documentation/articles/mobile-services-ios-get-started).

2.  Appuyez sur le bouton **Exécuter** pour générer le projet et démarrer l'application dans l'émulateur iPhone ; vérifiez qu'une exception non prise en charge avec le code d'état 401 (Non autorisé) est générée après le démarrage de l'application.

    Cela se produit, car l'application essaye d'accéder à Mobile Services en tant qu'utilisateur non authentifié, mais la table *TodoItem* requiert désormais l'authentification.

    Ensuite, vous allez mettre à jour l'application pour authentifier les utilisateurs avant de demander des ressources à partir du service mobile. ##Ajout de l'authentification à l'application 

[WACOM.INCLUDE [mobile-services-ios-authenticate-app](../includes/mobile-services-ios-authenticate-app.md)] 

## Étapes suivantes Dans le didacticiel suivant, 
[Autorisation côté service des utilisateurs Mobile Services][Autorisation des utilisateurs avec des scripts], vous allez prendre la valeur d'ID utilisateur fournie par Mobile Services sur la base d'un utilisateur authentifié et l'utiliser pour filtrer les données renvoyées par Mobile Services. 

[Inscription de votre application pour l'authentification et configuration de Mobile Services]: \#register 
[Restriction des autorisations de table pour les utilisateurs authentifiés]: \#permissions 
[Ajout de l'authentification à l'application]: \#add-authentication 
[Étapes suivantes]:\#next-steps 
[4]: ./media/mobile-services-ios-get-started-users/mobile-services-selection.png 
[5]: ./media/mobile-services-ios-get-started-users/mobile-service-uri.png 
[13]: ./media/mobile-services-ios-get-started-users/mobile-identity-tab.png 
[14]: ./media/mobile-services-ios-get-started-users/mobile-portal-data-tables.png 
[15]: ./media/mobile-services-ios-get-started-users/mobile-portal-change-table-perms.png 
[Page Soumette une application]: http://go.microsoft.com/fwlink/p/?LinkID=266582 
[Mes applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039 
[Kit de développement logiciel (SDK) Live]: http://go.microsoft.com/fwlink/p/?LinkId=262253 
[Authentification unique pour les applications Windows Store à l'aide de Live Connect]: /en-us/develop/mobile/tutorials/single-sign-on-windows-8-dotnet 
[Prise en main de Mobile Services]: /en-us/develop/mobile/tutorials/get-started-ios 
[Prise en main de données]: /en-us/develop/mobile/tutorials/get-started-with-data-ios 
[Prise en main de l'authentification]: /en-us/develop/mobile/tutorials/get-started-with-users-ios 
[Prise en main des notifications Push]: /en-us/develop/mobile/tutorials/get-started-with-push-ios 
[Autorisation des utilisateurs avec des scripts]: /en-us/develop/mobile/tutorials/authorize-users-in-scripts-ios 
[Portail de gestion Azure]: https://manage.windowsazure.com/


