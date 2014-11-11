<properties pageTitle="Get started with authentication (Android) | Mobile Dev Center" metaKeywords="authentication, Facebook, Google, Twitter, Microsoft Account, login" description="Learn how to use Mobile Services to authenticate users of your Windows Store app through a variety of identity providers, including Google, Facebook, Twitter, and Microsoft." metaCanonical="" services="mobile" documentationCenter="Mobile" title="Get started with authentication in Mobile Services" authors="mahender" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-android" ms.devlang="java" ms.topic="article" ms.date="01/01/1900" ms.author="mahender" />

# Prise en main de l'authentification dans Mobile Services

<div class="dev-center-tutorial-selector sublanding">
<a href="/fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-users" title="Windows Store&nbsp;C#">Windows Store&nbsp;C#</a>
<a href="/fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-users" title="Windows Store JavaScript">Windows Store JavaScript</a>
<a href="/fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-users" title="Windows Phone">Windows Phone</a>
<a href="/fr-fr/documentation/articles/mobile-services-dotnet-backend-ios-get-started-users" title="iOS" >iOS</a>
<a href="/fr-fr/documentation/articles/mobile-services-dotnet-backend-android-get-started-users" title="Android" class="current">Android</a>
<a href="/fr-fr/documentation/articles/mobile-services-dotnet-backend-xamarin-ios-get-started-users" title="Xamarin.iOS">Xamarin.iOS</a>
<a href="/fr-fr/documentation/articles/mobile-services-dotnet-backend-xamarin-android-get-started-users" title="Xamarin.Android">Xamarin.Android</a>
</div>

<div class="dev-center-tutorial-subselector">
<a href="/fr-fr/documentation/articles/mobile-services-dotnet-backend-android-get-started-users/" title=".NET backend" class="current">.NET backend</a> | <a href="/fr-fr/documentation/articles/mobile-services-android-get-started-users/"  title="JavaScript backend">JavaScript backend</a></div>

Cette rubrique montre comment authentifier les utilisateurs dans Azure Mobile Services à partir de votre application. Dans ce didacticiel, vous allez ajouter l'authentification au projet de démarrage rapide à l'aide d'un fournisseur d'identité pris en charge par Mobile Services. Après avoir été authentifiée et autorisée par Mobile Services, la valeur de l'ID utilisateur s'affiche.

Ce didacticiel vous familiarise avec les étapes de base permettant d'activer l'authentification dans votre application :

1.  [Inscription de votre application pour l'authentification et configuration de Mobile Services][Inscription de votre application pour l'authentification et configuration de Mobile Services]
2.  [Restriction des autorisations de table pour les utilisateurs authentifiés][Restriction des autorisations de table pour les utilisateurs authentifiés]
3.  [Ajout de l'authentification à l'application][Ajout de l'authentification à l'application]
4.  [Enregistrement de jetons d'authentification sur le client][Enregistrement de jetons d'authentification sur le client]
5.  [Actualisation des jetons expirés][Actualisation des jetons expirés]

Ce didacticiel est basé sur le démarrage rapide de Mobile Services. Vous devez aussi d'abord suivre le didacticiel [Prise en main de Mobile Services][Prise en main de Mobile Services].

## <a name="register"></a>Inscription de votre application pour l'authentification et configuration de Mobile Services

[WACOM.INCLUDE [mobile-services-register-authentication](../includes/mobile-services-register-authentication.md)]

[WACOM.INCLUDE [mobile-services-dotnet-backend-aad-server-extension](../includes/mobile-services-dotnet-backend-aad-server-extension.md)]

## <a name="permissions"></a>Restriction des autorisations pour les utilisateurs authentifiés

[WACOM.INCLUDE [mobile-services-restrict-permissions-dotnet-backend](../includes/mobile-services-restrict-permissions-dotnet-backend.md)]

1.  Dans Eclipse, ouvrez le projet que vous avez créé avec le didacticiel [Prise en main de Mobile Services][Prise en main de Mobile Services].

2.  Dans le menu **Exécuter**, cliquez sur **Exécuter** pour démarrer l'application ; vérifiez qu'une exception non prise en charge avec le code d'état 401 (Non autorisé) est générée après le démarrage de l'application.

    Cela se produit, car l'application essaye d'accéder à Mobile Services en tant qu'utilisateur non authentifié, mais la table *TodoItem* requiert désormais l'authentification.

Ensuite, vous allez mettre à jour l'application pour authentifier les utilisateurs avant de demander des ressources à partir du service mobile.

## <a name="add-authentication"></a>Ajout de l'authentification à l'application

[WACOM.INCLUDE [mobile-services-android-authenticate-app](../includes/mobile-services-android-authenticate-app.md)]

## <a name="cache-tokens"></a>Mise en cache de jetons d'authentification sur le client

[WACOM.INCLUDE [mobile-services-android-authenticate-app-with-token](../includes/mobile-services-android-authenticate-app-with-token.md)]

## <a name="refresh-tokens"></a>Actualisation du cache des jetons

[WACOM.INCLUDE [mobile-services-android-authenticate-app-refresh-token](../includes/mobile-services-android-authenticate-app-refresh-token.md)]

## <a name="next-steps"></a>Étapes suivantes

Dans le didacticiel suivant, [Autorisation côté service des utilisateurs Mobile Services][Autorisation côté service des utilisateurs Mobile Services], vous allez prendre la valeur d'ID utilisateur fournie par Mobile Services sur la base d'un utilisateur authentifié et l'utiliser pour filtrer les données renvoyées par Mobile Services.



  [Inscription de votre application pour l'authentification et configuration de Mobile Services]: #register
  [Restriction des autorisations de table pour les utilisateurs authentifiés]: #permissions
  [Ajout de l'authentification à l'application]: #add-authentication
  [Enregistrement de jetons d'authentification sur le client]: #cache-tokens
  [Actualisation des jetons expirés]: #refresh-tokens
  [Prise en main de Mobile Services]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-android-get-started/
  [mobile-services-register-authentication]: ../includes/mobile-services-register-authentication.md
  [mobile-services-dotnet-backend-aad-server-extension]: ../includes/mobile-services-dotnet-backend-aad-server-extension.md
  [mobile-services-restrict-permissions-dotnet-backend]: ../includes/mobile-services-restrict-permissions-dotnet-backend.md
  [mobile-services-android-authenticate-app]: ../includes/mobile-services-android-authenticate-app.md
  [mobile-services-android-authenticate-app-with-token]: ../includes/mobile-services-android-authenticate-app-with-token.md
  [mobile-services-android-authenticate-app-refresh-token]: ../includes/mobile-services-android-authenticate-app-refresh-token.md
  [Autorisation côté service des utilisateurs Mobile Services]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-android-authorize-users-in-scripts
