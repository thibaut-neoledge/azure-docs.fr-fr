<properties 
	pageTitle="Prise en main de l'authentification (Windows Phone) | Centre de développement mobile" 
	description="Découvrez comment utiliser Mobile Services pour authentifier les utilisateurs de votre application Windows Phone via divers fournisseurs d'identité, notamment Google, Facebook, Twitter et Microsoft." 
	services="mobile-services" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-phone" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="05/01/2015" 
	ms.author="glenga"/>

# Ajout de l'authentification à votre application Mobile Services

[AZURE.INCLUDE [mobile-services-selector-get-started-users-legacy](../../includes/mobile-services-selector-get-started-users-legacy.md)]

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">

<p>Cette rubrique montre comment authentifier les utilisateurs dans Azure Mobile Services à partir de votre application. Dans ce didacticiel, vous allez ajouter l'authentification au projet de démarrage rapide à l'aide d'un fournisseur d'identité pris en charge par Mobile Services. Après avoir été authentifiée et autorisée par Mobile Services, la valeur de l'ID utilisateur s'affiche.</p>
</div>
<div class="dev-onpage-video-wrapper"><a href="http://go.microsoft.com/fwlink/?LinkId=298631" target="_blank" class="label">regarder le didacticiel</a> <a style="background-image: url('/media/devcenter/mobile/videos/mobile-wp8-get-started-authentication-180x120.png') !important;" href="http://go.microsoft.com/fwlink/?LinkId=298631" target="_blank" class="dev-onpage-video"><span class="icon">Lire la vidéo</span></a> <span class="time">10:50</span></div>
</div>

Ce didacticiel vous familiarise avec les étapes de base permettant d'activer l'authentification dans votre application :

1. [Inscrire votre application pour l'authentification et configurer Mobile Services]
2. [Restreindre les autorisations de table aux utilisateurs authentifiés]
3. [Ajouter l'authentification à l'application]

Ce didacticiel est basé sur le démarrage rapide de Mobile Services. Vous devez d'abord suivre le didacticiel [Ajouter Mobile Services à une application existante].

>[AZURE.NOTE]Ce didacticiel explique le flux d'authentification géré par Mobile Services via différents fournisseurs d'identité. Cette méthode est facilement configurable et prend en charge plusieurs fournisseurs. Pour utiliser plutôt Live Connect avec authentification gérée par client afin de fournir une authentification unique dans votre application Windows Phone, consultez la rubrique [Authentification unique pour les applications Windows Phone à l'aide de Live Connect]. En utilisant l'authentification gérée par un client, votre application a accès aux données utilisateur supplémentaires conservées par le fournisseur d'identité. Vous pouvez obtenir les mêmes données utilisateur dans votre service mobile en appelant la fonction **user.getIdentities()** dans des scripts serveur. Pour plus d'informations, consultez [ce billet](http://go.microsoft.com/fwlink/p/?LinkId=506605).

##<a name="register"></a>Inscrire votre application pour l'authentification et configurer Mobile Services


[AZURE.INCLUDE [mobile-services-register-authentication](../../includes/mobile-services-register-authentication.md)]


##<a name="permissions"></a>Restreindre les autorisations aux utilisateurs authentifiés


[AZURE.INCLUDE [mobile-services-restrict-permissions-javascript-backend](../../includes/mobile-services-restrict-permissions-javascript-backend.md)]


<ol start="3">
<li>Dans Visual Studio&#160;2012 Express pour Windows&#160;Phone, ouvrez le projet que vous avez créé avec le didacticiel <strong>Prise en main de Mobile Services</strong>.</li>

<li>Appuyez sur la touche&#160;F5 pour exécuter cette application basée sur le démarrage rapide&#160;; vérifiez qu'une exception non prise en charge avec le code d'état&#160;401 (Non autorisé) est générée après le démarrage de l'application. Cela se produit, car l'application essaye d'accéder à Mobile Services en tant qu'utilisateur non authentifié, mais la table <em>TodoItem</em> requiert désormais l'authentification.</li></ol>

Ensuite, vous allez mettre à jour l'application pour authentifier les utilisateurs avant de demander des ressources à partir du service mobile.

##<a name="add-authentication"></a>Ajouter l'authentification à l'application

[AZURE.INCLUDE [mobile-services-windows-phone-authenticate-app](../../includes/mobile-services-windows-phone-authenticate-app.md)]

##<a name="tokens"></a>Stocker les jetons d'authentification sur le client

[AZURE.INCLUDE [mobile-services-windows-phone-authenticate-app-with-token](../../includes/mobile-services-windows-phone-authenticate-app-with-token.md)]

## <a name="next-steps"> </a>Étapes suivantes

Dans le didacticiel suivant, [Autorisation côté service des utilisateurs Mobile Services](mobile-services-javascript-backend-service-side-authorization.md), vous allez prendre la valeur d'ID utilisateur fournie par Mobile Services sur la base d'un utilisateur authentifié et l'utiliser pour filtrer les données renvoyées par Mobile Services.

<!-- Anchors. -->
[Inscrire votre application pour l'authentification et configurer Mobile Services]: #register
[Restreindre les autorisations de table aux utilisateurs authentifiés]: #permissions
[Ajouter l'authentification à l'application]: #add-authentication
[Next Steps]: #next-steps

<!-- Images. -->
[1]: ./media/mobile-services-wp8-get-started-users/mobile-services-selection.png
[2]: ./media/mobile-services-wp8-get-started-users/mobile-service-uri.png
[3]: ./media/mobile-services-wp8-get-started-users/mobile-identity-tab.png
[4]: ./media/mobile-services-wp8-get-started-users/mobile-portal-data-tables.png
[5]: ./media/mobile-services-wp8-get-started-users/mobile-portal-change-table-perms.png

<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Ajouter Mobile Services à une application existante]: mobile-services-windows-phone-get-started-data.md
[Authorize users with scripts]: ../mobile-services-windows-phone-authorize-users-in-scripts.md
[Azure Management Portal]: https://manage.windowsazure.com/
[Authentification unique pour les applications Windows Phone à l'aide de Live Connect]: mobile-services-windows-phone-single-sign-on.md
 

<!---HONumber=July15_HO2-->