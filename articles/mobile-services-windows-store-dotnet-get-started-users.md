<properties pageTitle="Get started with authentication (Windows Store) | Mobile Dev Center" metaKeywords="authentication, Facebook, Google, Twitter, Microsoft Account, login" description="Learn how to use Mobile Services to authenticate users of your Windows Store app through a variety of identity providers, including Google, Facebook, Twitter, and Microsoft." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get started with authentication in Mobile Services" authors="Glenn Gailey" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="09/23/2014" ms.author="Glenn Gailey"></tags>

# Prise en main de l'authentification dans Mobile Services

[WACOM.INCLUDE [mobile-services-selector-get-started-users][]]

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">
<p>Cette rubrique montre comment authentifier les utilisateurs dans Azure Mobile Services &agrave; partir de votre application. Dans ce didacticiel, vous allez ajouter l'authentification au projet de d&eacute;marrage rapide &agrave; l'aide d'un fournisseur d'identit&eacute; pris en charge par Mobile Services. Apr&egrave;s avoir &eacute;t&eacute; authentifi&eacute;e et autoris&eacute;e par Mobile Services, la valeur de l'ID utilisateur s'affiche.</p>
<p>Vous pouvez regarder une version vid&eacute;o de ce didacticiel en cliquant sur le clip &agrave; droite.</p>
</div>

<div class="dev-onpage-video-wrapper"><a href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Introduction-to-Windows-Azure-Mobile-Services" target="_blank" class="label">regarder le didacticiel</a> <a style="background-image: url('/media/devcenter/mobile/videos/get-started-with-users-windows-store-180x120.png') !important;" href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Windows-Store-app-Getting-Started-with-Authentication-in-Windows-Azure-Mobile-Services" target="_blank" class="dev-onpage-video"><span class="icon">Lire la vid&eacute;o</span></a> <span class="time">10:04:00</span></div>

</div>

> [WACOM.NOTE]L'authentification n'est actuellement pas prise en charge pour les applications Windows Phone Store 8.1 lors de l'utilisation de la bibliothèque cliente JavaScript des services mobiles Si vous avez un projet d'application Windows universelle qui utilise le client JavaScript, vous ne pourrez pas authentifier les utilisateurs sur Windows Phone à l'heure actuelle.

Ce didacticiel vous familiarise avec les étapes de base permettant d'activer l'authentification dans votre application :

1.  [Inscription de votre application pour l'authentification et configuration de Mobile Services][]
2.  [Restriction des autorisations de table pour les utilisateurs authentifiés][]
3.  [Ajout de l'authentification à l'application][]
4.  [Stockage des jetons d'authentification sur le client][]

Ce didacticiel est basé sur le démarrage rapide de Mobile Services. Vous devez aussi d'abord suivre le didacticiel [Prise en main de Mobile Services][].

> [WACOM.NOTE]Ce didacticiel présente le flux d'authentification géré par Mobile Services à l'aide de différents fournisseurs d'identité. Cette méthode est facilement configurable et prend en charge plusieurs fournisseurs. Pour utiliser plutôt Live Connect avec authentification gérée par un client afin de fournir une authentification unique dans votre application Windows Phone, consultez la rubrique [Authentification unique pour les applications Windows Store à l'aide de Live Connect][]. En utilisant l'authentification gérée par un client, votre application a accès aux données utilisateur supplémentaires conservées par le fournisseur d'identité. Vous pouvez obtenir les mêmes données utilisateur dans votre service mobile en appelant la fonction **user.getIdentities()** dans des scripts serveur. Pour plus d'informations, consultez [ce billet][].

## <a name="register"></a>Inscription de votre application pour l'authentification et configuration de Mobile Services

[WACOM.INCLUDE [mobile-services-register-authentication][]]

1.  (Facultatif) Suivez la procédure décrite dans la rubrique [Inscription du package de votre application Windows Store pour l'authentification Microsoft][].

    <div class="dev-callout"><b>Remarque</b>
    <p>Cette &eacute;tape est facultative, car elle concerne uniquement le fournisseur de connexion du compte Microsoft. Lorsque vous inscrivez les informations du package de votre application Windows Store aupr&egrave;s de Mobile Services, le client peut r&eacute;utiliser les informations d'identification du compte Microsoft pour fournir une authentification unique. Si vous ne le faites pas, vos utilisateurs se connectant via le compte Microsoft seront invit&eacute;s &agrave; se connecter &agrave; chaque appel de la m&eacute;thode de connexion. Suivez cette &eacute;tape si vous pr&eacute;voyez d'utiliser le fournisseur d'identit&eacute; du compte Microsoft.</p>
    </div>

## <a name="permissions"></a>Restriction des autorisations pour les utilisateurs authentifiés

[WACOM.INCLUDE [mobile-services-restrict-permissions-javascript-backend][]]

1.  Dans Visual Studio 2012 Express pour Windows 8, ouvrez le projet que vous avez créé avec le didacticiel [Prise en main de Mobile Services][1].

2.  Appuyez sur la touche F5 pour exécuter cette application basée sur le démarrage rapide ; vérifiez qu'une exception non prise en charge avec le code d'état 401 (Non autorisé) est générée après le démarrage de l'application.

    Cela se produit, car l'application essaye d'accéder à Mobile Services en tant qu'utilisateur non authentifié, mais la table *TodoItem* requiert désormais l'authentification.

Ensuite, vous allez mettre à jour l'application pour authentifier les utilisateurs avant de demander des ressources à partir du service mobile.

## <a name="add-authentication"></a>Ajout de l'authentification à l'application

[WACOM.INCLUDE [mobile-services-windows-dotnet-authenticate-app][]]

## <a name="tokens"></a>Stockage du jeton d'authentification sur le client

[WACOM.INCLUDE [mobile-services-windows-store-dotnet-authenticate-app-with-token][]]

## <a name="next-steps"> </a>Étapes suivantes

Dans le didacticiel suivant, [Autorisation côté service des utilisateurs Mobile Services][], vous allez prendre la valeur d'ID utilisateur fournie par Mobile Services sur la base d'un utilisateur authentifié et l'utiliser pour filtrer les données renvoyées par Mobile Services. Obtenez plus d'informations sur Mobile Services avec .NET dans [le guide de fonctionnement Mobile Services .NET][]

  [mobile-services-selector-get-started-users]: ../includes/mobile-services-selector-get-started-users.md
  [regarder le didacticiel]: http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Introduction-to-Windows-Azure-Mobile-Services
  [<span class="icon">Lire la vidéo</span>]: http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Windows-Store-app-Getting-Started-with-Authentication-in-Windows-Azure-Mobile-Services
  [Inscription de votre application pour l'authentification et configuration de Mobile Services]: #register
  [Restriction des autorisations de table pour les utilisateurs authentifiés]: #permissions
  [Ajout de l'authentification à l'application]: #add-authentication
  [Stockage des jetons d'authentification sur le client]: #tokens
  [Prise en main de Mobile Services]: /fr-fr/documentation/articles/mobile-services-windows-store-get-started/
  [Authentification unique pour les applications Windows Store à l'aide de Live Connect]: /fr-fr/documentation/articles/mobile-services-windows-store-dotnet-single-sign-on
  [ce billet]: http://go.microsoft.com/fwlink/p/?LinkId=506605
  [mobile-services-register-authentication]: ../includes/mobile-services-register-authentication.md
  [Inscription du package de votre application Windows Store pour l'authentification Microsoft]: /fr-fr/documentation/articles/mobile-services-how-to-register-store-app-package-microsoft-authentication/
  [mobile-services-restrict-permissions-javascript-backend]: ../includes/mobile-services-restrict-permissions-javascript-backend.md
  [1]: /fr-fr/documentation/articles/mobile-services-windows-store-get-started
  [mobile-services-windows-dotnet-authenticate-app]: ../includes/mobile-services-windows-dotnet-authenticate-app.md
  [mobile-services-windows-store-dotnet-authenticate-app-with-token]: ../includes/mobile-services-windows-store-dotnet-authenticate-app-with-token.md
  [Autorisation côté service des utilisateurs Mobile Services]: /fr-fr/documentation/articles/mobile-services-windows-store-dotnet-authorize-users-in-scripts
  [le guide de fonctionnement Mobile Services .NET]: /fr-fr/develop/mobile/how-to-guides/work-with-net-client-library
