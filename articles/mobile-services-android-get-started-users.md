<properties 
	pageTitle="Prise en main de l'authentification (Android) | Centre de développement mobile" 
	description="Découvrez comment utiliser Mobile Services pour authentifier les utilisateurs de votre application Android via divers fournisseurs d'identité, notamment Google, Facebook, Twitter et Microsoft." 
	services="mobile-services" 
	documentationCenter="android" 
	authors="RickSaling" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-android" 
	ms.devlang="java" 
	ms.topic="article" 
	ms.date="02/03/2015" 
	ms.author="ricksal"/>

# Ajout d'une authentification à votre application Mobile Services

[AZURE.INCLUDE [mobile-services-selector-get-started-users](../includes/mobile-services-selector-get-started-users.md)]

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">
</div>

## Résumé

<p>Cette rubrique montre comment authentifier des utilisateurs dans Azure Mobile Services à partir de votre application. Dans ce didacticiel, vous allez ajouter l'authentification au projet de démarrage rapide à l'aide d'un fournisseur d'identité pris en charge par Mobile Services. Après avoir été authentifiée et autorisée par Mobile Services, la valeur de l'ID utilisateur s'affiche.</p>


<div class="dev-onpage-video-wrapper"><a href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Android-Getting-Started-with-Authentication-in-Windows-Azure-Mobile-Services" target="_blank" class="label">voir le didacticiel</a> <a style="background-image: url('/media/devcenter/mobile/videos/mobile-android-get-started-authentication-180x120.png') !important;" href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Android-Getting-Started-with-Authentication-in-Windows-Azure-Mobile-Services" target="_blank" class="dev-onpage-video"><span class="icon">Lire la vidéo</span></a><span class="time">10:42</span></div>
</div> 

Ce didacticiel vous familiarise avec les étapes de base permettant d'activer l'authentification dans votre application :


## Configuration requise

[AZURE.INCLUDE [mobile-services-android-prerequisites](../includes/mobile-services-android-prerequisites.md)]

<h2><a name="register"></a>Inscription de votre application pour l'authentification et configuration de Mobile Services</h2>

[AZURE.INCLUDE [mobile-services-register-authentication](../includes/mobile-services-register-authentication.md)] 

<h2><a name="permissions"></a>Restriction des autorisations pour les utilisateurs authentifiés</h2>

[AZURE.INCLUDE [mobile-services-restrict-permissions-javascript-backend](../includes/mobile-services-restrict-permissions-javascript-backend.md)] 

3. Dans Android Studio, ouvrez le projet que vous avez créé quand vous avez suivi le didacticiel [Prise en main de Mobile Services]. 

4. Dans le menu **Exécuter**, cliquez sur **Exécuter l'application** ; vérifiez qu'une exception non prise en charge avec le code d'état 401 (Non autorisé) est générée après le démarrage de l'application. 

	 Cela se produit car l'application essaye d'accéder à Mobile Services en tant qu'utilisateur non authentifié, mais la table _TodoItem_ requiert désormais l'authentification.

Ensuite, vous allez mettre à jour l'application pour authentifier les utilisateurs avant de demander des ressources à partir du service mobile.

<h2><a name="add-authentication"></a>Ajout de l'authentification à l'application</h2>

[AZURE.INCLUDE [mobile-services-android-authenticate-app](../includes/mobile-services-android-authenticate-app.md)]

## <a name="cache-tokens"></a>Mise en cache des jetons d'authentification sur le client

[AZURE.INCLUDE [mobile-services-android-authenticate-app-with-token](../includes/mobile-services-android-authenticate-app-with-token.md)] 

## <a name="refresh-tokens"></a>Actualisation du cache de jeton

[AZURE.INCLUDE [mobile-services-android-authenticate-app-refresh-token](../includes/mobile-services-android-authenticate-app-refresh-token.md)] 



## <a name="next-steps"></a>Étapes suivantes

Dans le didacticiel suivant, [Autorisation des utilisateurs avec des scripts], vous allez utiliser la valeur de l'ID utilisateur fournie par Mobile Services en fonction de l'utilisateur authentifié et l'utiliser pour filtrer les données renvoyées par Mobile Services. 

<!-- Anchors. -->
[Inscription de votre application pour l'authentification et configuration de Mobile Services]: #register
[Restriction des autorisations de table pour les utilisateurs authentifiés]: #permissions
[Ajout de l'authentification à l'application]: #add-authentication
[Stockage de jetons d'authentification sur le client]: #cache-tokens
[Actualisation des jetons expirés]: #refresh-tokens
[Étapes suivantes]:#next-steps

<!-- Images. -->




[4]: ./media/mobile-services-android-get-started-users/mobile-services-selection.png
[5]: ./media/mobile-services-android-get-started-users/mobile-service-uri.png







[13]: ./media/mobile-services-android-get-started-users/mobile-identity-tab.png
[14]: ./media/mobile-services-android-get-started-users/mobile-portal-data-tables.png
[15]: ./media/mobile-services-android-get-started-users/mobile-portal-change-table-perms.png


<!-- URLs. -->

[Page Envoyer une application]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Mes Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Kit de développement logiciel (SDK) Live pour Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Authentification unique pour les applications Windows Store à l'aide de Live Connect]: /develop/mobile/tutorials/single-sign-on-windows-8-dotnet
[Prise en main de Mobile Services]: /develop/mobile/tutorials/get-started-android
[Ajout de Mobile Services à une application existante]: /develop/mobile/tutorials/get-started-with-data-android
[Prise en main de l'authentification]: /develop/mobile/tutorials/get-started-with-users-android
[Prise en main des notifications Push]: /develop/mobile/tutorials/get-started-with-push-android
[Autorisation des utilisateurs avec des scripts]: /develop/mobile/tutorials/authorize-users-in-scripts-android

[Portail de gestion Azure]: https://manage.windowsazure.com/

<!--HONumber=47-->
