<properties linkid="develop-mobile-tutorials-get-started-with-users-android" urlDisplayName="Get Started with Authentication" pageTitle="Get started with authentication (Android) | Mobile Dev Center" metaKeywords="" description="Learn how to use Mobile Services to authenticate users of your Android app through a variety of identity providers, including Google, Facebook, Twitter, and Microsoft." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get started with authentication in Mobile Services" authors="ricksal" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="Mobile-Android" ms.devlang="Java" ms.topic="article" ms.date="09/23/2014" ms.author="ricksal" />

# Prise en main de l'authentification dans Mobile Services

[WACOM.INCLUDE [mobile-services-selector-get-started-users](../includes/mobile-services-selector-get-started-users.md)]

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">

<p>Cette rubrique montre comment authentifier les utilisateurs dans Azure Mobile Services &agrave; partir de votre application. Dans ce didacticiel, vous allez ajouter l'authentification au projet de d&eacute;marrage rapide &agrave; l'aide d'un fournisseur d'identit&eacute; pris en charge par Mobile Services. Apr&egrave;s avoir &eacute;t&eacute; authentifi&eacute;e et autoris&eacute;e par Mobile Services, la valeur de l'ID utilisateur s'affiche.</p>
</div>

<div class="dev-onpage-video-wrapper"><a href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Android-Getting-Started-with-Authentication-in-Windows-Azure-Mobile-Services" target="_blank" class="label">regarder le didacticiel</a> <a style="background-image: url('/media/devcenter/mobile/videos/mobile-android-get-started-authentication-180x120.png') !important;" href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Android-Getting-Started-with-Authentication-in-Windows-Azure-Mobile-Services" target="_blank" class="dev-onpage-video"><span class="icon">Lire la vid&eacute;o</span></a><span class="time">10:42</span></div>

</div>

Ce didacticiel vous familiarise avec les étapes de base permettant d'activer l'authentification dans votre application :

1.  [Inscription de votre application pour l'authentification et configuration de Mobile Services][Inscription de votre application pour l'authentification et configuration de Mobile Services]
2.  [Restriction des autorisations de table pour les utilisateurs authentifiés][Restriction des autorisations de table pour les utilisateurs authentifiés]
3.  [Ajout de l'authentification à l'application][Ajout de l'authentification à l'application]
4.  [Stockage des jetons d'authentification sur le client][Stockage des jetons d'authentification sur le client]
5.  [Actualisation des jetons expirés][Actualisation des jetons expirés]

Ce didacticiel est basé sur le démarrage rapide de Mobile Services. Vous devez aussi d'abord suivre le didacticiel [Prise en main de Mobile Services][Prise en main de Mobile Services].

Eclipse et Android 4.2 ou une version ultérieure sont requis pour suivre ce didacticiel.

## <a name="register"></a><span class="short-header">Inscription de votre application</span>Inscription de votre application pour l'authentification et configuration de Mobile Services

[WACOM.INCLUDE [mobile-services-register-authentication](../includes/mobile-services-register-authentication.md)]

## <a name="permissions"></a><span class="short-header">Restriction des autorisations</span>Restriction des autorisations pour les utilisateurs authentifiés

[WACOM.INCLUDE [mobile-services-restrict-permissions-javascript-backend](../includes/mobile-services-restrict-permissions-javascript-backend.md)]

1.  Dans Eclipse, ouvrez le projet que vous avez créé avec le didacticiel [Prise en main de Mobile Services][Prise en main de Mobile Services].

2.  Dans le menu **Exécuter**, cliquez sur **Exécuter** pour démarrer l'application ; vérifiez qu'une exception non prise en charge avec le code d'état 401 (Non autorisé) est générée après le démarrage de l'application.

    Cela se produit, car l'application essaye d'accéder à Mobile Services en tant qu'utilisateur non authentifié, mais la table *TodoItem* requiert désormais l'authentification.

Ensuite, vous allez mettre à jour l'application pour authentifier les utilisateurs avant de demander des ressources à partir du service mobile.

## <a name="add-authentication"></a><span class="short-header">Ajout de l'authentification</span>Ajout de l'authentification à l'application

[WACOM.INCLUDE [mobile-services-android-authenticate-app](../includes/mobile-services-android-authenticate-app.md)]

## <a name="cache-tokens"></a>Mise en cache des jetons d'authentification sur le client

[WACOM.INCLUDE [mobile-services-android-authenticate-app-with-token](../includes/mobile-services-android-authenticate-app-with-token.md)]

## <a name="refresh-tokens"></a>Actualisation du cache de jeton

[WACOM.INCLUDE [mobile-services-android-authenticate-app-refresh-token](../includes/mobile-services-android-authenticate-app-refresh-token.md)]

## <a name="next-steps"></a>Étapes suivantes

Dans le didacticiel suivant, [Autorisation des utilisateurs avec des scripts][Autorisation des utilisateurs avec des scripts], vous allez utiliser la valeur de l'ID utilisateur fournie par Mobile Services en fonction de l'utilisateur authentifié et l'utiliser pour filtrer les données renvoyées par Mobile Services.

  [mobile-services-selector-get-started-users]: ../includes/mobile-services-selector-get-started-users.md
  [Inscription de votre application pour l'authentification et configuration de Mobile Services]: #register
  [Restriction des autorisations de table pour les utilisateurs authentifiés]: #permissions
  [Ajout de l'authentification à l'application]: #add-authentication
  [Stockage des jetons d'authentification sur le client]: #cache-tokens
  [Actualisation des jetons expirés]: #refresh-tokens
  [Prise en main de Mobile Services]: /fr-fr/develop/mobile/tutorials/get-started-android
  [mobile-services-register-authentication]: ../includes/mobile-services-register-authentication.md
  [mobile-services-restrict-permissions-javascript-backend]: ../includes/mobile-services-restrict-permissions-javascript-backend.md
  [mobile-services-android-authenticate-app]: ../includes/mobile-services-android-authenticate-app.md
  [mobile-services-android-authenticate-app-with-token]: ../includes/mobile-services-android-authenticate-app-with-token.md
  [mobile-services-android-authenticate-app-refresh-token]: ../includes/mobile-services-android-authenticate-app-refresh-token.md
  [Autorisation des utilisateurs avec des scripts]: /fr-fr/develop/mobile/tutorials/authorize-users-in-scripts-android
