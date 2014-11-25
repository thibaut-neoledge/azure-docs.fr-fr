<properties linkid="develop-mobile-tutorials-get-started-with-users-wp8" urlDisplayName="Get Started with Authentication" pageTitle="Get started with authentication (Windows Phone) | Mobile Dev Center" metaKeywords="" description="Learn how to use Mobile Services to authenticate users of your Windows Phone app through a variety of identity providers, including Google, Facebook, Twitter, and Microsoft." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get started with authentication in Mobile Services" authors="glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-phone" ms.devlang="dotnet" ms.topic="article" ms.date="09/23/2014" ms.author="glenga" />

# Prise en main de l'authentification dans Mobile Services

[WACOM.INCLUDE [mobile-services-selector-get-started-users](../includes/mobile-services-selector-get-started-users.md)]

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">

<p>Cette rubrique montre comment authentifier les utilisateurs dans Azure Mobile Services &agrave; partir de votre application. Dans ce didacticiel, vous allez ajouter l'authentification au projet de d&eacute;marrage rapide &agrave; l'aide d'un fournisseur d'identit&eacute; pris en charge par Mobile Services. Apr&egrave;s avoir &eacute;t&eacute; authentifi&eacute;e et autoris&eacute;e par Mobile Services, la valeur de l'ID utilisateur s'affiche.</p>
</div>

<div class="dev-onpage-video-wrapper"><a href="http://go.microsoft.com/fwlink/?LinkId=298631" target="_blank" class="label">regarder le didacticiel</a> <a style="background-image: url('/media/devcenter/mobile/videos/mobile-wp8-get-started-authentication-180x120.png') !important;" href="http://go.microsoft.com/fwlink/?LinkId=298631" target="_blank" class="dev-onpage-video"><span class="icon">Lire la vid&eacute;o</span></a> <span class="time">10:50:00</span></div>

</div>

Ce didacticiel vous familiarise avec les étapes de base permettant d'activer l'authentification dans votre application :

1.  [Inscription de votre application pour l'authentification et configuration de Mobile Services][Inscription de votre application pour l'authentification et configuration de Mobile Services]
2.  [Restriction des autorisations de table pour les utilisateurs authentifiés][Restriction des autorisations de table pour les utilisateurs authentifiés]
3.  [Ajout de l'authentification à l'application][Ajout de l'authentification à l'application]

Ce didacticiel est basé sur le démarrage rapide de Mobile Services. Vous devez aussi d'abord suivre le didacticiel [Prise en main de Mobile Services][Prise en main de Mobile Services].

> [WACOM.NOTE]Ce didacticiel présente le flux d'authentification géré par Mobile Services à l'aide de différents fournisseurs d'identité. Cette méthode est facilement configurable et prend en charge plusieurs fournisseurs. Pour utiliser plutôt Live Connect avec authentification gérée par client afin de fournir une authentification unique dans votre application Windows Phone, consultez la rubrique [Authentification unique pour les applications Windows Phone à l'aide de Live Connect][Authentification unique pour les applications Windows Phone à l'aide de Live Connect]. En utilisant l'authentification gérée par un client, votre application a accès aux données utilisateur supplémentaires conservées par le fournisseur d'identité. Vous pouvez obtenir les mêmes données utilisateur dans votre service mobile en appelant la fonction **user.getIdentities()** dans des scripts serveur. Pour plus d'informations, consultez [ce billet][ce billet].

## <a name="register"></a>Inscription de votre application pour l'authentification et configuration de Mobile Services

[WACOM.INCLUDE [mobile-services-register-authentication](../includes/mobile-services-register-authentication.md)]

## <a name="permissions"></a>Restriction des autorisations pour les utilisateurs authentifiés

[WACOM.INCLUDE [mobile-services-restrict-permissions-javascript-backend](../includes/mobile-services-restrict-permissions-javascript-backend.md)]

1.  Dans Visual Studio 2012 Express pour Windows Phone, ouvrez le projet que vous avez créé avec le didacticiel [Prise en main de Mobile Services][Prise en main de Mobile Services].

2.  Appuyez sur la touche F5 pour exécuter cette application basée sur le démarrage rapide ; vérifiez qu'une exception non prise en charge avec le code d'état 401 (Non autorisé) est générée après le démarrage de l'application.

    Cela se produit, car l'application essaye d'accéder à Mobile Services en tant qu'utilisateur non authentifié, mais la table *TodoItem* requiert désormais l'authentification.

Ensuite, vous allez mettre à jour l'application pour authentifier les utilisateurs avant de demander des ressources à partir du service mobile.

## <a name="add-authentication"></a>Ajout de l'authentification à l'application

[WACOM.INCLUDE [mobile-services-windows-phone-authenticate-app](../includes/mobile-services-windows-phone-authenticate-app.md)]

## <a name="tokens"></a>Stockage des jetons d'authentification sur le client

[WACOM.INCLUDE [mobile-services-windows-phone-authenticate-app-with-token](../includes/mobile-services-windows-phone-authenticate-app-with-token.md)]

## <a name="next-steps"> </a>Étapes suivantes

Dans le didacticiel suivant, [Autorisation côté service des utilisateurs Mobile Services][Autorisation côté service des utilisateurs Mobile Services], vous allez prendre la valeur d'ID utilisateur fournie par Mobile Services sur la base d'un utilisateur authentifié et l'utiliser pour filtrer les données renvoyées par Mobile Services.

  [Inscription de votre application pour l'authentification et configuration de Mobile Services]: #register
  [Restriction des autorisations de table pour les utilisateurs authentifiés]: #permissions
  [Ajout de l'authentification à l'application]: #add-authentication
  [Prise en main de Mobile Services]: /fr-fr/documentation/articles/mobile-services-windows-phone-get-started
  [Authentification unique pour les applications Windows Phone à l'aide de Live Connect]: /fr-fr/documentation/articles/mobile-services-windows-phone-single-sign-on
  [ce billet]: http://go.microsoft.com/fwlink/p/?LinkId=506605
  [Autorisation côté service des utilisateurs Mobile Services]: /fr-fr/documentation/articles/mobile-services-windows-phone-authorize-users-in-scripts
