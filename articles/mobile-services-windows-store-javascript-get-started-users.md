<properties linkid="develop-mobile-tutorials-get-started-with-users-js" urlDisplayName="Get Started with Authentication" pageTitle="Get started with authentication (JavaScript) | Mobile Dev Center" metaKeywords="" description="Learn how to use Mobile Services to authenticate users of your Windows Store JavaScript app through a variety of identity providers, including Google, Facebook, Twitter, and Microsoft." metaCanonical="http://www.windowsazure.com/fr-fr/develop/mobile/tutorials/get-started-with-users-dotnet/" services="" documentationCenter="Mobile" title="Get started with authentication in Mobile Services" authors="" solutions="" manager="" editor="" />

Prise en main de l'authentification dans Mobile Services
========================================================

<div class="dev-center-tutorial-selector sublanding"><a href="/en-us/documentation/articles/mobile-services-windows-store-dotnet-get-started-users" title="Windows Store C#">Windows Store C#</a><a href="/en-us/documentation/articles/mobile-services-windows-store-javascript-get-started-users" title="Windows Store JavaScript" class="current">Windows Store JavaScript</a><a href="/en-us/documentation/articles/mobile-services-windows-phone-get-started-users" title="Windows Phone">Windows Phone</a><a href="/en-us/documentation/articles/mobile-services-ios-get-started-users" title="iOS">iOS</a><a href="/en-us/documentation/articles/mobile-services-android-get-started-users" title="Android">Android</a><a href="/en-us/documentation/articles/mobile-services-html-get-started-users" title="HTML">HTML</a><a href="/en-us/documentation/articles/partner-xamarin-mobile-services-ios-get-started-users" title="Xamarin.iOS">Xamarin.iOS</a><a href="/en-us/documentation/articles/partner-xamarin-mobile-services-android-get-started-users" title="Xamarin.Android">Xamarin.Android</a></div>
<div class="dev-center-tutorial-subselector"><a href="/en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-users/" title=".NET backend">.NET backend</a> | <a href="/en-us/documentation/articles/mobile-services-windows-store-javascript-get-started-users/"  title="JavaScript backend" class="current">JavaScript backend</a></div>

Cette rubrique montre comment authentifier les utilisateurs dans Azure Mobile Services à partir de votre application. Dans ce didacticiel, vous allez ajouter l'authentification au projet de démarrage rapide à l'aide d'un fournisseur d'identité pris en charge par Mobile Services. Après avoir été authentifiée et autorisée par Mobile Services, la valeur de l'ID utilisateur s'affiche.

Ce didacticiel vous familiarise avec les étapes de base permettant d'activer l'authentification dans votre application :

1.  [Inscription de votre application pour l'authentification et configuration de Mobile Services](#register)
2.  [Restriction des autorisations de table pour les utilisateurs authentifiés](#permissions)
3.  [Ajout de l'authentification à l'application](#add-authentication)

Ce didacticiel est basé sur le démarrage rapide de Mobile Services. Vous devez aussi d'abord suivre le didacticiel [Prise en main de Mobile Services](/en-us/documentation/articles/mobile-services-windows-store-get-started/).

<div class="dev-callout"><b>Remarque</b>

<p>Ce didacticiel présente la méthode de base fournie par Mobile Services pour authentifier les utilisateurs à l'aide de différents fournisseurs d'identité. Cette méthode est facilement configurable et prend en charge plusieurs fournisseurs. Elle requiert toutefois que les utilisateurs se connectent à chaque fois que l'application démarre. Pour utiliser plutôt Live Connect afin de fournir une authentification unique dans votre application Windows Store, consultez la rubrique <a href="/en-us/develop/mobile/tutorials/single-sign-on-windows-8-js">Authentification unique pour les applications Windows Store à l'aide de Live Connect</a>.</p>
</div>

Inscription de votre application pour l'authentification et configuration de Mobile Services
--------------------------------------------------------------------------------------------

[WACOM.INCLUDE [mobile-services-register-authentication](../includes/mobile-services-register-authentication.md)]

<ol start="5">
<li><p>(Facultatif) Suivez la procédure décrite dans la rubrique [Inscription du package de votre application Windows Store pour l'authentification Microsoft](/en-us/documentation/articles/mobile-services-how-to-register-store-app-package-microsoft-authentication/).</p>

    <div class="dev-callout"><b>Remarque</b>

    <p>Cette étape est facultative, car elle concerne uniquement le fournisseur de connexion du compte Microsoft. Lorsque vous inscrivez les informations du package de votre application Windows Store auprès de Mobile Services, le client peut réutiliser les informations d'identification du compte Microsoft pour fournir une authentification unique. Si vous ne le faites pas, vos utilisateurs se connectant via le compte Microsoft seront invités à se connecter à chaque appel de la méthode de connexion. Suivez cette étape si vous prévoyez d'utiliser le fournisseur d'identité du compte Microsoft.</p>
	</div>
</li>
</ol>

Votre service mobile et votre application sont désormais configurés pour utiliser le fournisseur d'authentification choisi.

Restriction des autorisations pour les utilisateurs authentifiés
----------------------------------------------------------------

[WACOM.INCLUDE [mobile-services-restrict-permissions-javascript-backend](../includes/mobile-services-restrict-permissions-javascript-backend.md)]

<ol start="3">
<li><p>Dans Visual Studio 2012 Express pour Windows 8, ouvrez le projet que vous avez créé avec le didacticiel <a href="/en-us/develop/mobile/tutorials/get-started/">Prise en main de Mobile Services</a>.</p>

<li><p>Appuyez sur la touche F5 pour exécuter cette application basée sur le démarrage rapide ; vérifiez qu'une exception non prise en charge avec le code d'état 401 (Non autorisé) est générée après le démarrage de l'application.</p>


   <p>Cela se produit, car l'application essaye d'accéder à Mobile Services en tant qu'utilisateur non authentifié, mais la table *TodoItem* requiert désormais l'authentification.</p></li>
</ol>

Ensuite, vous allez mettre à jour l'application pour authentifier les utilisateurs avant de demander des ressources à partir du service mobile.

Ajout de l'authentification à l'application
-------------------------------------------

[WACOM.INCLUDE [mobile-services-windows-store-javascript-authenticate-app](../includes/mobile-services-windows-store-javascript-authenticate-app.md)]

Étapes suivantes
----------------

Dans le didacticiel suivant, [Autorisation côté service des utilisateurs Mobile Services](/en-us/documentation/articles/mobile-services-windows-store-javascript-authorize-users-in-scripts), vous allez prendre la valeur d'ID utilisateur fournie par Mobile Services sur la base d'un utilisateur authentifié et l'utiliser pour filtrer les données renvoyées par Mobile Services.

<!-- Anchors. -->
[Register your app for authentication and configure Mobile Services]: #register
[Restrict table permissions to authenticated users]: #permissions
[Add authentication to the app]: #add-authentication
[Next Steps]:#next-steps


<!-- URLs. -->
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Single sign-on for Windows Store apps by using Live Connect]: /en-us/documentation/articles/mobile-services-windows-store-javascript-single-sign-on
[Get started with Mobile Services]: /en-us/documentation/articles/mobile-services-windows-store-get-started/
[Get started with data]: /en-us/documentation/articles/mobile-services-windows-store-javascript-get-started-data/
[Get started with authentication]: /en-us/documentation/articles/mobile-services-windows-store-javascript-get-started-users/
[Get started with push notifications]: /en-us/documentation/articles/mobile-services-windows-store-javascript-get-started-push/
[Authorize users with scripts]: /en-us/documentation/articles/mobile-services-windows-store-javascript-authorize-users-in-scripts

[Azure Management Portal]: https://manage.windowsazure.com/
[Register your Windows Store app package for Microsoft authentication]: /en-us/develop/mobile/how-to-guides/register-windows-store-app-package