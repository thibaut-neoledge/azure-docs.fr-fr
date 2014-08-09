<properties linkid="develop-mobile-tutorials-get-started-with-users-wp8" urlDisplayName="Get Started with Authentication" pageTitle="Get started with authentication (Windows Phone) | Mobile Dev Center" metaKeywords="" description="Learn how to use Mobile Services to authenticate users of your Windows Phone app through a variety of identity providers, including Google, Facebook, Twitter, and Microsoft." metaCanonical="" services="" documentationCenter="Mobile" title="Get started with authentication in Mobile Services" authors="glenga" solutions="" manager="" editor="" />

Prise en main de l'authentification dans Mobile Services
========================================================

[Windows Store C\#](/fr-fr/documentation/articles/mobile-services-windows-store-dotnet-get-started-users "Windows Store C#")[Windows Store JavaScript](/fr-fr/documentation/articles/mobile-services-windows-store-javascript-get-started-users "Windows Store JavaScript")[Windows Phone](/fr-fr/documentation/articles/mobile-services-windows-phone-get-started-users "Windows Phone")[iOS](/fr-fr/documentation/articles/mobile-services-ios-get-started-users "iOS")[Android](/fr-fr/documentation/articles/mobile-services-android-get-started-users "Android")[HTML](/fr-fr/documentation/articles/mobile-services-html-get-started-users "HTML")[Xamarin.iOS](/fr-fr/documentation/articles/partner-xamarin-mobile-services-ios-get-started-users "Xamarin.iOS")[Xamarin.Android](/fr-fr/documentation/articles/partner-xamarin-mobile-services-android-get-started-users "Xamarin.Android")

[.NET backend](/fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-users/ ".NET backend") | [JavaScript backend](/fr-fr/documentation/articles/mobile-services-windows-phone-get-started-users/ "JavaScript backend")

Cette rubrique montre comment authentifier les utilisateurs dans Azure Mobile Services à partir de votre application. Dans ce didacticiel, vous allez ajouter l'authentification au projet de démarrage rapide à l'aide d'un fournisseur d'identité pris en charge par Mobile Services. Après avoir été authentifiée et autorisée par Mobile Services, la valeur de l'ID utilisateur s'affiche.

[regarder le didacticiel](http://go.microsoft.com/fwlink/?LinkId=298631) [Lire la vidéo](http://go.microsoft.com/fwlink/?LinkId=298631) 10:50

Ce didacticiel vous familiarise avec les étapes de base permettant d'activer l'authentification dans votre application :

1.  [Inscription de votre application pour l'authentification et configuration de Mobile Services](#register)
2.  [Restriction des autorisations de table pour les utilisateurs authentifiés](#permissions)
3.  [Ajout de l'authentification à l'application](#add-authentication)

Ce didacticiel est basé sur le démarrage rapide de Mobile Services. Vous devez aussi d'abord suivre le didacticiel [Prise en main de Mobile Services](/en-us/develop/mobile/tutorials/get-started-wp8).

Inscription de votre applicationInscription de votre application pour l'authentification et configuration de Mobile Services
----------------------------------------------------------------------------------------------------------------------------

Pour pouvoir authentifier les utilisateurs, vous devez inscrire votre application avec un fournisseur d'identité. Vous devez ensuite inscrire la clé secrète cliente générée par le fournisseur avec Mobile Services.

1.  Connectez-vous au [portail de gestion Azure](https://manage.windowsazure.com/), cliquez sur **Mobile Services**, puis sur le service mobile.

	![][1]

2.  Cliquez sur l'onglet **Tableau de bord**, puis notez la valeur **Mobile Service URL**.

	![][2]

    Il se peut que le fournisseur d'identité vous demande cette valeur lorsque vous inscrivez votre application.

3.  Choisissez un fournisseur d'identité pris en charge dans la liste ci-dessous et suivez la procédure pour inscrire votre application auprès de ce fournisseur :
	-   [Compte Microsoft](/en-us/develop/mobile/how-to-guides/register-for-microsoft-authentication/)
	-   [Connexion Facebook](/en-us/develop/mobile/how-to-guides/register-for-facebook-authentication/)
	-   [Connexion Twitter](/en-us/develop/mobile/how-to-guides/register-for-twitter-authentication/)
	-   [Connexion Google](/en-us/develop/mobile/how-to-guides/register-for-google-authentication/)
	-   [Azure Active Directory](/fr-fr/documentation/articles/mobile-services-how-to-register-active-directory-authentication/)

    	N'oubliez pas de noter les valeurs de l'identité du client et de la clé secrète cliente générées par le fournisseur.

    	**Remarque relative à la sécurité**

    	La clé secrète générée par le fournisseur est une information d'identification de sécurité importante. Ne partagez pas cette clé secrète avec quiconque et ne la distribuez pas avec votre application.

4.  De retour dans le portail de gestion, cliquez sur l'onglet **Identité**, entrez les valeurs de l'identificateur d'application et de la clé secrète partagée fournies par votre fournisseur d'identité, puis cliquez sur **Enregistrer**.

	![][3]

Votre service mobile et votre application sont désormais configurés pour utiliser le fournisseur d'authentification choisi.

Restriction des autorisations pour les utilisateurs authentifiés
----------------------------------------------------------------

1.  Dans le portail de gestion, cliquez sur l'onglet **Données**, puis sur la table **TodoItem**.

	![][4]

2.  Cliquez sur l'onglet **Autorisations**, définissez toutes les autorisations sur **Only authenticated users**, puis cliquez sur **Enregistrer**. Cela permet de s'assurer que toutes les opérations effectuées sur la table **TodoItem** requièrent un utilisateur authentifié. Cela simplifie aussi les scripts dans le didacticiel suivant, car il ne sera pas nécessaire d'autoriser la possibilité d'utilisateurs anonymes.

	![][5]

3.  Dans Visual Studio 2012 Express pour Windows Phone, ouvrez le projet que vous avez créé avec le didacticiel [Prise en main de Mobile Services](/en-us/develop/mobile/tutorials/get-started-wp8).

4.  Appuyez sur la touche F5 pour exécuter cette application basée sur le démarrage rapide ; vérifiez qu'une exception non prise en charge avec le code d'état 401 (Non autorisé) est générée après le démarrage de l'application.

	Cela se produit car l'application essaye d'accéder à Mobile Services en tant qu'utilisateur non authentifié, mais la table _TodoItem_ requiert désormais l'authentification.

Ensuite, vous allez mettre à jour l'application pour authentifier les utilisateurs avant de demander des ressources à partir du service mobile.

Ajout de l'authentification à l'application
-------------------------------------------

[WACOM.INCLUDE [mobile-services-windows-phone-authenticate-app](../includes/mobile-services-windows-phone-authenticate-app.md)]

Étapes suivantes
----------------

Dans le didacticiel suivant, [Autorisation côté service des utilisateurs Mobile Services](/en-us/develop/mobile/tutorials/authorize-users-in-scripts-wp8), vous allez prendre la valeur d'ID utilisateur fournie par Mobile Services sur la base d'un utilisateur authentifié et l'utiliser pour filtrer les données renvoyées par Mobile Services.

<!-- Anchors. -->
[Register your app for authentication and configure Mobile Services]: #register
[Restrict table permissions to authenticated users]: #permissions
[Add authentication to the app]: #add-authentication
[Next Steps]:#next-steps

<!-- Images. -->
[1]: ./media/mobile-services-wp8-get-started-users/mobile-services-selection.png
[2]: ./media/mobile-services-wp8-get-started-users/mobile-service-uri.png
[3]: ./media/mobile-services-wp8-get-started-users/mobile-identity-tab.png
[4]: ./media/mobile-services-wp8-get-started-users/mobile-portal-data-tables.png
[5]: ./media/mobile-services-wp8-get-started-users/mobile-portal-change-table-perms.png

<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Get started with Mobile Services]: /en-us/develop/mobile/tutorials/get-started-wp8
[Get started with data]: /en-us/develop/mobile/tutorials/started-with-data-wp8
[Get started with authentication]: /en-us/develop/mobile/tutorials/get-started-with-users-wp8
[Get started with push notifications]: /en-us/develop/mobile/tutorials/get-started-with-push-wp8
[Authorize users with scripts]: /en-us/develop/mobile/tutorials/authorize-users-in-scripts-wp8

[Azure Management Portal]: https://manage.windowsazure.com/