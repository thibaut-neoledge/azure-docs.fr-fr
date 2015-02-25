<properties pageTitle="Prise en main de l'authentification (JavaScript) | Centre de développement mobile" description="Découvrez comment utiliser Mobile Services pour authentifier les utilisateurs de votre application Windows Store JavaScript via divers fournisseurs d'identité, notamment Google, Facebook, Twitter et Microsoft." services="mobile-services" documentationCenter="windows" authors="ggailey777" manager="dwrede" editor=""/>

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="javascript" ms.topic="article" ms.date="09/23/2014" ms.author="glenga"/>

# Ajouter une authentification à votre application Mobile Services

[AZURE.INCLUDE [mobile-services-selector-get-started-users-legacy](../includes/mobile-services-selector-get-started-users-legacy.md)]

Cette rubrique montre comment authentifier les utilisateurs dans Azure Mobile Services à partir de votre application.  Dans ce didacticiel, vous allez ajouter l'authentification au projet de démarrage rapide à l'aide d'un fournisseur d'identité pris en charge par Mobile Services. Après avoir été authentifiée et autorisée par Mobile Services, la valeur de l'ID utilisateur s'affiche.  

Ce didacticiel vous familiarise avec les étapes de base permettant d'activer l'authentification dans votre application :

1. [Inscription de votre application pour l'authentification et configuration de Mobile Services]
2. [Restriction des autorisations de table pour les utilisateurs authentifiés]
3. [Ajout de l'authentification à l'application]
4. [Stockage de jetons d'authentification sur le client]

Ce didacticiel est basé sur le démarrage rapide de Mobile Services. Vous devez également commencer par suivre le didacticiel [Prise en main de Mobile Services]. 

>[AZURE.NOTE]Ce didacticiel explique le flux d'authentification géré par Mobile Services via différents fournisseurs d'identité. Cette méthode est facilement configurable et prend en charge plusieurs fournisseurs. Pour utiliser plutôt Live Connect avec l'authentification gérée par un client afin de fournir une authentification unique dans votre application Windows Phone, consultez la rubrique [Authentification unique pour les applications Windows Phone à l'aide de Live Connect]. L'authentification gérée par un client permet à votre application d'accéder à des données supplémentaires détenues par le fournisseur d'identité. Vous pouvez obtenir les mêmes données utilisateur dans votre service mobile en appelant la fonction **user.getIdentities()** dans les scripts serveur. Pour plus d'informations, consultez [ce billet](http://go.microsoft.com/fwlink/p/?LinkId=506605).

##<a name="register"></a> Inscription de votre application pour l'authentification et configuration de Mobile Services

[AZURE.INCLUDE [mobile-services-register-authentication](../includes/mobile-services-register-authentication.md)] 

<ol start="5">
<li><p>(Facultatif) Suivez la procédure décrite dans la rubrique <a href="/fr-fr/documentation/articles/mobile-services-how-to-register-store-app-package-microsoft-authentication/">Inscription du package de votre application Windows Store pour l'authentification Microsoft</a>.</p>

    
	<p>Cette étape est facultative, car elle concerne uniquement le fournisseur de connexion du compte Microsoft. Lorsque vous inscrivez les informations du package de votre application Windows Store auprès de Mobile Services, le client peut réutiliser les informations d'identification du compte Microsoft pour fournir une authentification unique. Si vous ne le faites pas, vos utilisateurs se connectant via le compte Microsoft seront invités à se connecter à chaque appel de la méthode de connexion. Suivez cette étape si vous prévoyez d'utiliser le fournisseur d'identité du compte Microsoft.</p>
</li>
</ol>
Votre service mobile et votre application sont désormais configurés pour utiliser le fournisseur d'authentification choisi.

##<a name="permissions"></a> Restriction des autorisations pour les utilisateurs authentifiés

[AZURE.INCLUDE [mobile-services-restrict-permissions-javascript-backend](../includes/mobile-services-restrict-permissions-javascript-backend.md)] 

<ol start="3">
<li><p>Dans Visual Studio 2012 Express pour Windows 8, ouvrez le projet que vous avez créé avec le didacticiel <a href="/fr-fr/develop/mobile/tutorials/get-started/">Prise en main de Mobile Services</a>.</p></li> 
<li><p>Appuyez sur la touche F5 pour exécuter cette application basée sur le démarrage rapide ; vérifiez qu'une exception non prise en charge avec le code d'état 401 (Non autorisé) est générée après le démarrage de l'application.</p>
   
   	<p>Cela se produit, car l'application tente d'accéder à Mobile Services en tant qu'utilisateur non authentifié, alors que la table <em>TodoItem</em> requiert désormais l'authentification.</p></li>
</ol>

Ensuite, vous allez mettre à jour l'application pour authentifier les utilisateurs avant de demander des ressources à partir du service mobile.

##<a name="add-authentication"></a> Ajout de l'authentification à l'application

[AZURE.INCLUDE [mobile-services-windows-store-javascript-authenticate-app](../includes/mobile-services-windows-store-javascript-authenticate-app.md)] 

##<a name="tokens"></a>Stockage de jetons d'authentification sur le client

[AZURE.INCLUDE [mobile-services-windows-store-javascript-authenticate-app-with-token](../includes/mobile-services-windows-store-javascript-authenticate-app-with-token.md)] 

## <a name="next-steps"> </a>Étapes suivantes

Dans le didacticiel suivant, [Autorisation côté service des utilisateurs Mobile Services][Autorisation des utilisateurs avec des scripts], vous allez prendre la valeur d'ID utilisateur fournie par Mobile Services sur la base d'un utilisateur authentifié et l'utiliser pour filtrer les données renvoyées par Mobile Services. 


<!-- Anchors. -->
[Inscription de votre application pour l'authentification et configuration de Mobile Services]: #register
[Restriction des autorisations de table pour les utilisateurs authentifiés]: #permissions
[Ajout de l'authentification à l'application]: #add-authentication
[Stockage de jetons d'authentification sur le client]: #tokens
[Étapes suivantes]:#next-steps


<!-- URLs. -->
[Mes Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Kit de développement logiciel (SDK) Live pour Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Authentification unique pour les applications Windows Store à l'aide de Live Connect]: /fr-fr/documentation/articles/mobile-services-windows-store-javascript-single-sign-on
[Prise en main de Mobile Services]: /fr-fr/documentation/articles/mobile-services-windows-store-get-started/
[Prise en main des données]: /fr-fr/documentation/articles/mobile-services-windows-store-javascript-get-started-data/
[Prise en main de l'authentification]: /fr-fr/documentation/articles/mobile-services-windows-store-javascript-get-started-users/
[Prise en main des notifications Push]: /fr-fr/documentation/articles/mobile-services-windows-store-javascript-get-started-push/
[Autorisation des utilisateurs avec des scripts]: /fr-fr/documentation/articles/mobile-services-windows-store-javascript-authorize-users-in-scripts

[Portail de gestion Azure]: https://manage.windowsazure.com/
[Inscription du package de votre application Windows Store pour l'authentification Microsoft]: /fr-fr/develop/mobile/how-to-guides/register-windows-store-app-package


<!--HONumber=42-->
