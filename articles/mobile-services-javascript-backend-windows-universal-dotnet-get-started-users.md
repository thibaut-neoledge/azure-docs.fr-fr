<properties pageTitle="Get started with authentication (Windows Store) | Mobile Dev Center" metaKeywords="authentication, Facebook, Google, Twitter, Microsoft Account, login" description="Learn how to use Mobile Services to authenticate users of your Windows Store app through a variety of identity providers, including Google, Facebook, Twitter, and Microsoft." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get started with authentication in Mobile Services" authors="Glenn Gailey" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="09/18/2014" ms.author="Glenn Gailey"></tags>

# Prise en main de l'authentification dans Mobile Services

[WACOM.INCLUDE [mobile-services-selector-get-started-users](../includes/mobile-services-selector-get-started-users.md)]

Cette rubrique explique comment authentifier les utilisateurs dans Azure Mobile Services à partir d'une application Windows universelle. Dans ce didacticiel, vous allez ajouter l'authentification au projet de démarrage rapide à l'aide d'un fournisseur d'identité pris en charge par Mobile Services. Après avoir été authentifiée et autorisée par Mobile Services, la valeur de l'ID utilisateur s'affiche.

Ce didacticiel vous familiarise avec les étapes de base permettant d'activer l'authentification dans votre application :

1.  [Inscription de votre application pour l'authentification et configuration de Mobile Services][Inscription de votre application pour l'authentification et configuration de Mobile Services]
2.  [Restriction des autorisations de table pour les utilisateurs authentifiés][Restriction des autorisations de table pour les utilisateurs authentifiés]
3.  [Ajout de l'authentification à l'application][Ajout de l'authentification à l'application]
4.  [Stockage de jetons d'authentification sur le client][Stockage de jetons d'authentification sur le client]

Ce didacticiel est basé sur le démarrage rapide de Mobile Services. Vous devez aussi d'abord suivre le didacticiel [Prise en main de Mobile Services][Prise en main de Mobile Services].

> [WACOM.NOTE]Ce didacticiel vous explique comment authentifier les utilisateurs dans les applications Windows Store et Windows Phone Store 8.1. Pour une application Windows Phone 8.0 ou Windows Phone Silverlight 8.1, consulter cette version de [Prise en main de l'authentification dans Mobile Services][Prise en main de l'authentification dans Mobile Services].

> Ce didacticiel explique le flux d’authentification géré par Mobile Services via différents fournisseurs d’identité. Cette méthode est facilement configurable et prend en charge plusieurs fournisseurs. Pour utiliser plutôt Live Connect avec l'authentification gérée par un client afin de fournir une authentification unique dans votre application Windows Phone, consultez la rubrique [Authentification unique pour les applications Windows Phone à l'aide de Live Connect][Authentification unique pour les applications Windows Phone à l'aide de Live Connect]. L'authentification gérée par un client permet à votre application d'accéder à des données supplémentaires détenues par le fournisseur d'identité. Vous pouvez obtenir les mêmes données utilisateur dans votre service mobile en appelant la fonction **user.getIdentities()** dans les scripts serveur. Pour plus d’informations, consulter [ce billet][ce billet].

## <a name="register"></a>Inscription de votre application pour l'authentification et configuration de Mobile Services

[WACOM.INCLUDE [mobile-services-register-authentication](../includes/mobile-services-register-authentication.md)]

1.  (Facultatif) Suivez la procédure décrite dans la rubrique [Inscription du package de votre application Windows Store pour l'authentification Microsoft][Inscription du package de votre application Windows Store pour l'authentification Microsoft].

    <div class="dev-callout"><b>Remarque</b>
    <p>Cette &eacute;tape est facultative, car elle concerne uniquement le fournisseur de connexion du compte Microsoft. Lorsque vous inscrivez les informations du package de votre application Windows Store aupr&egrave;s de Mobile Services, le client peut r&eacute;utiliser les informations d'identification du compte Microsoft pour fournir une authentification unique. Si vous ne le faites pas, vos utilisateurs se connectant via le compte Microsoft seront invit&eacute;s &agrave; se connecter &agrave; chaque appel de la m&eacute;thode de connexion. Suivez cette &eacute;tape si vous pr&eacute;voyez d'utiliser le fournisseur d'identit&eacute; du compte Microsoft.</p>
    </div>

## <a name="permissions"></a>Restriction des autorisations pour les utilisateurs authentifiés

[WACOM.INCLUDE [mobile-services-restrict-permissions-javascript-backend](../includes/mobile-services-restrict-permissions-javascript-backend.md)]

1.  Dans Visual Studio, cliquez avec le bouton droit de la souris sur le projet Windows Store pour l'application TodoList, puis cliquez sur **Définir comme projet de démarrage**.

2.  Dans le projet partagé, ouvrez le fichier de projet App.xaml.cs, accédez à la définition [MobileServiceClient][MobileServiceClient] et assurez-vous qu'elle est configurée pour une connexion au service mobile exécuté dans Azure.

    <div class="dev-callout"><strong>Remarque</strong><p>Lorsque vous utilisez les outils Visual Studio pour connecter votre application &agrave; un service mobile, l'outil g&eacute;n&egrave;re deux ensembles de d&eacute;finitions <strong>MobileServiceClient</strong>, une pour chaque plateforme cliente. C'est le bon moment pour simplifier le code g&eacute;n&eacute;r&eacute; en unifiant les d&eacute;finitions <strong>MobileServiceClient</strong> encapsul&eacute;es dans <code data-inline="1">#if...#endif</code> en une seule d&eacute;finition encapsul&eacute;e, utilis&eacute;e pour les deux versions de l'application.</p></div>

3.  Appuyez sur la touche F5 pour exécuter l'application Windows Store, et vérifiez qu'une exception non prise en charge avec le code d'état 401 (Unauthorized) est déclenchée après le démarrage de l'application.

    Cela se produit, car l'application essaye d'accéder à Mobile Services en tant qu'utilisateur non authentifié, mais la table *TodoItem* requiert désormais l'authentification.

Ensuite, vous allez mettre à jour l'application pour authentifier les utilisateurs avant de demander des ressources à partir du service mobile.

## <a name="add-authentication"></a>Ajout de l'authentification à l'application

[WACOM.INCLUDE [mobile-services-windows-universal-dotnet-authenticate-app](../includes/mobile-services-windows-universal-dotnet-authenticate-app.md)]

## <a name="tokens"></a>Stockage du jeton d'authentification sur le client

[WACOM.INCLUDE [mobile-services-windows-store-dotnet-authenticate-app-with-token](../includes/mobile-services-windows-store-dotnet-authenticate-app-with-token.md)]

## <a name="next-steps"> </a>Étapes suivantes

Dans le didacticiel suivant, [Autorisation côté service des utilisateurs Mobile Services][Autorisation côté service des utilisateurs Mobile Services], vous allez prendre la valeur d'ID utilisateur fournie par Mobile Services sur la base d'un utilisateur authentifié et l'utiliser pour filtrer les données renvoyées par Mobile Services. Obtenez plus d'informations sur Mobile Services avec .NET dans [le guide de fonctionnement Mobile Services .NET][le guide de fonctionnement Mobile Services .NET]


  [mobile-services-selector-get-started-users]: ../includes/mobile-services-selector-get-started-users.md
  [Inscription de votre application pour l'authentification et configuration de Mobile Services]: #register
  [Restriction des autorisations de table pour les utilisateurs authentifiés]: #permissions
  [Ajout de l'authentification à l'application]: #add-authentication
  [Stockage de jetons d'authentification sur le client]: #tokens
  [Prise en main de Mobile Services]: /fr-fr/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-get-started/
  [Prise en main de l'authentification dans Mobile Services]: /fr-fr/documentation/articles/mobile-services-windows-phone-get-started-users
  [Authentification unique pour les applications Windows Phone à l'aide de Live Connect]: /fr-fr/documentation/articles/mobile-services-windows-store-dotnet-single-sign-on
  [ce billet]: http://go.microsoft.com/fwlink/p/?LinkId=506605
  [mobile-services-register-authentication]: ../includes/mobile-services-register-authentication.md
  [Inscription du package de votre application Windows Store pour l'authentification Microsoft]: /fr-fr/documentation/articles/mobile-services-how-to-register-store-app-package-microsoft-authentication/
  [mobile-services-restrict-permissions-javascript-backend]: ../includes/mobile-services-restrict-permissions-javascript-backend.md
  [MobileServiceClient]: http://msdn.microsoft.com/fr-fr/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.aspx
  [mobile-services-windows-universal-dotnet-authenticate-app]: ../includes/mobile-services-windows-universal-dotnet-authenticate-app.md
  [mobile-services-windows-store-dotnet-authenticate-app-with-token]: ../includes/mobile-services-windows-store-dotnet-authenticate-app-with-token.md
  [Autorisation côté service des utilisateurs Mobile Services]: /fr-fr/documentation/articles/mobile-services-windows-store-dotnet-authorize-users-in-scripts
  [le guide de fonctionnement Mobile Services .NET]: /fr-fr/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library/
