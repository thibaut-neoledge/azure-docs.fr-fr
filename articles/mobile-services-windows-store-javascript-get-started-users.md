<properties linkid="develop-mobile-tutorials-get-started-with-users-js" urlDisplayName="Get Started with Authentication" pageTitle="Get started with authentication (JavaScript) | Mobile Dev Center" metaKeywords="" description="Learn how to use Mobile Services to authenticate users of your Windows Store JavaScript app through a variety of identity providers, including Google, Facebook, Twitter, and Microsoft." services="mobile-services" documentationCenter="Mobile" title="Get started with authentication in Mobile Services" authors="glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="javascript" ms.topic="article" ms.date="09/23/2014" ms.author="glenga" />

# Prise en main de l'authentification dans Mobile Services

> [AZURE.SELECTOR-LIST (Platform | Backend )]
- [(iOS | .NET)](/fr-fr/documentation/articles/mobile-services-dotnet-backend-ios-get-started-users/)
- [(iOS | JavaScript)](/fr-fr/documentation/articles/mobile-services-ios-get-started-users/)
- [(Windows C# | .NET)](/fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-universal-dotnet-get-started-users/)
- [(Windows C# | Javascript)](/fr-fr/documentation/articles/mobile-services-javascript-backend-windows-universal-dotnet-get-started-users/)
- [(Windows JavaScript | .NET)](/fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-users/)
- [(Windows JavaScript | Javascript)](/fr-fr/documentation/articles/mobile-services-windows-store-javascript-get-started-users/)
- [(Windows Phone | .NET)](/fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-users/)
- [(Windows Phone | Javascript)](/fr-fr/documentation/articles/mobile-services-windows-phone-get-started-users/)
- [(Android | .NET)](/fr-fr/documentation/articles/mobile-services-dotnet-backend-android-get-started-users/)
- [(Android | Javascript)](/fr-fr/documentation/articles/mobile-services-android-get-started-users/)
- [(Xamarin iOS | .NET)](/fr-fr/documentation/articles/mobile-services-dotnet-backend-xamarin-ios-get-started-users/)
- [(Xamarin iOS | Javascript)](/fr-fr/documentation/articles/partner-xamarin-mobile-services-ios-get-started-users/)
- [(HTML | Javascript)](/fr-fr/documentation/articles/mobile-services-html-get-started-users/)
- [(Xamarin Android | .NET)](/fr-fr/documentation/articles/mobile-services-dotnet-backend-xamarin-android-get-started-users/)
- [(Xamarin Android | Javascript)](/fr-fr/documentation/articles/partner-xamarin-mobile-services-android-get-started-users/)
- [(Appcelerator | Javascript)](/fr-fr/documentation/articles/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-users/)

Cette rubrique montre comment authentifier les utilisateurs dans Azure Mobile Services à partir de votre application. Dans ce didacticiel, vous allez ajouter l'authentification au projet de démarrage rapide à l'aide d'un fournisseur d'identité pris en charge par Mobile Services. Après avoir été authentifiée et autorisée par Mobile Services, la valeur de l'ID utilisateur s'affiche.

Ce didacticiel vous familiarise avec les étapes de base permettant d'activer l'authentification dans votre application :

1.  [Inscription de votre application pour l'authentification et configuration de Mobile Services]
2.  [Restriction des autorisations de table pour les utilisateurs authentifiés]
3.  [Ajout de l'authentification à l'application]
4.  [Stockage des jetons d'authentification sur le client]

Ce didacticiel est basé sur le démarrage rapide de Mobile Services. Vous devez aussi d'abord suivre le didacticiel [Prise en main de Mobile Services].

> [WACOM.NOTE]Ce didacticiel présente le flux d'authentification géré par Mobile Services à l'aide de différents fournisseurs d'identité. Cette méthode est facilement configurable et prend en charge plusieurs fournisseurs. Pour utiliser plutôt Live Connect avec authentification gérée par un client afin de fournir une authentification unique dans votre application Windows Phone, consultez la rubrique [Authentification unique pour les applications Windows Store à l'aide de Live Connect]. En utilisant l'authentification gérée par un client, votre application a accès aux données utilisateur supplémentaires conservées par le fournisseur d'identité. Vous pouvez obtenir les mêmes données utilisateur dans votre service mobile en appelant la fonction **user.getIdentities()** dans des scripts serveur. Pour plus d'informations, consultez [ce billet](http://go.microsoft.com/fwlink/p/?LinkId=506605).

## <a name="register"></a>Inscription de votre application pour l'authentification et configuration de Mobile Services

[WACOM.INCLUDE [mobile-services-register-authentication](../includes/mobile-services-register-authentication.md)]

1.  (Facultatif) Suivez la procédure décrite dans la rubrique <a href="/fr-fr/documentation/articles/mobile-services-how-to-register-store-app-package-microsoft-authentication/">Inscription du package de votre application Windows Store pour l'authentification Microsoft</a>.

    <div class="dev-callout"><b>Remarque</b>
    <p>Cette &eacute;tape est facultative, car elle concerne uniquement le fournisseur de connexion du compte Microsoft. Lorsque vous inscrivez les informations du package de votre application Windows Store aupr&egrave;s de Mobile Services, le client peut r&eacute;utiliser les informations d'identification du compte Microsoft pour fournir une authentification unique. Si vous ne le faites pas, vos utilisateurs se connectant via le compte Microsoft seront invit&eacute;s &agrave; se connecter &agrave; chaque appel de la m&eacute;thode de connexion. Suivez cette &eacute;tape si vous pr&eacute;voyez d'utiliser le fournisseur d'identit&eacute; du compte Microsoft.</p>
    </div>

Votre service mobile et votre application sont désormais configurés pour utiliser le fournisseur d'authentification choisi.

## <a name="permissions"></a>Restriction des autorisations pour les utilisateurs authentifiés

[WACOM.INCLUDE [mobile-services-restrict-permissions-javascript-backend](../includes/mobile-services-restrict-permissions-javascript-backend.md)] 
1.  Dans Visual Studio 2012 Express pour Windows 8, ouvrez le projet que vous avez créé avec le didacticiel <a href="/fr-fr/develop/mobile/tutorials/get-started/">Prise en main de Mobile Services</a>.

2.  Appuyez sur la touche F5 pour exécuter cette application basée sur le démarrage rapide ; vérifiez qu'une exception non prise en charge avec le code d'état 401 (Non autorisé) est générée après le démarrage de l'application.

    Cela se produit, car l'application essaye d'accéder à Mobile Services en tant qu'utilisateur non authentifié, mais la table *TodoItem* requiert désormais l'authentification.

Ensuite, vous allez mettre à jour l'application pour authentifier les utilisateurs avant de demander des ressources à partir du service mobile.

## <a name="add-authentication"></a>Ajout de l'authentification à l'application

[WACOM.INCLUDE [mobile-services-windows-store-javascript-authenticate-app](../includes/mobile-services-windows-store-javascript-authenticate-app.md)] 

## <a name="tokens"></a>Stockage des jetons d'authentification sur le client

[WACOM.INCLUDE [mobile-services-windows-store-javascript-authenticate-app-with-token](../includes/mobile-services-windows-store-javascript-authenticate-app-with-token.md)] 

## <a name="next-steps"> </a>Étapes suivantes

Dans le didacticiel suivant, [Autorisation côté service des utilisateurs Mobile Services][Autorisation côté service des utilisateurs Mobile Services], vous allez prendre la valeur d'ID utilisateur fournie par Mobile Services sur la base d'un utilisateur authentifié et l'utiliser pour filtrer les données renvoyées par Mobile Services.

  [Inscription de votre application pour l'authentification et configuration de Mobile Services]: #register
  [Restriction des autorisations de table pour les utilisateurs authentifiés]: #permissions
  [Ajout de l'authentification à l'application]: #add-authentication
  [Stockage des jetons d'authentification sur le client]: #tokens
  [Prise en main de Mobile Services]: /fr-fr/documentation/articles/mobile-services-windows-store-get-started/
  [Authentification unique pour les applications Windows Store à l'aide de Live Connect]: /fr-fr/documentation/articles/mobile-services-windows-store-javascript-single-sign-on
  [ce billet]: http://go.microsoft.com/fwlink/p/?LinkId=506605
  [Autorisation côté service des utilisateurs Mobile Services]: /fr-fr/documentation/articles/mobile-services-windows-store-javascript-authorize-users-in-scripts
