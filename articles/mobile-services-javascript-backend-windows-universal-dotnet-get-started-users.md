<properties pageTitle="Prise en main de l'authentification (Windows Store) | Centre de développement mobile" description="Découvrez comment utiliser Mobile Services pour authentifier les utilisateurs de votre application Windows Store via divers fournisseurs d'identité, notamment Google, Facebook, Twitter et Microsoft." services="mobile-services" documentationCenter="windows" authors="ggailey777" manager="dwrede" editor=""/>

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="09/18/2014" ms.author="glenga"/>

# Ajout d'une authentification à votre application Mobile Services

[AZURE.INCLUDE [mobile-services-selector-get-started-users](../includes/mobile-services-selector-get-started-users.md)]		

Cette rubrique explique comment authentifier les utilisateurs dans Azure Mobile Services à partir d'une application Windows universelle. Dans ce didacticiel, vous allez ajouter l'authentification au projet de démarrage rapide à l'aide d'un fournisseur d'identité pris en charge par Mobile Services. Après avoir été authentifiée et autorisée par Mobile Services, la valeur de l'ID utilisateur s'affiche.

Ce didacticiel vous familiarise avec les étapes de base permettant d'activer l'authentification dans votre application :

1. [Inscription de votre application pour l'authentification et configuration de Mobile Services]
2. [Restriction des autorisations de table pour les utilisateurs authentifiés]
3. [Ajout de l'authentification à l'application]
5. [Stockage de jetons d'authentification sur le client]

Ce didacticiel est basé sur le démarrage rapide de Mobile Services. Vous devez également commencer par suivre le didacticiel [Prise en main de Mobile Services]. 

>[AZURE.NOTE]Ce didacticiel vous explique comment authentifier les utilisateurs dans les applications Windows Store et Windows Phone Store 8.1. Pour une application Windows Phone 8,0 ou Windows Phone Silverlight 8.1, consultez la version [Prise en main de l'authentification dans Mobile Services](/fr-fr/documentation/articles/mobile-services-windows-phone-get-started-users).

>Ce didacticiel explique le flux d'authentification géré par Mobile Services via différents fournisseurs d'identité. Cette méthode est facilement configurable et prend en charge plusieurs fournisseurs. Pour utiliser plutôt Live Connect avec l'authentification gérée par un client afin de fournir une authentification unique dans votre application Windows Phone, consultez la rubrique [Authentification unique pour les applications Windows Phone à l'aide de Live Connect]. L'authentification gérée par un client permet à votre application d'accéder à des données supplémentaires détenues par le fournisseur d'identité. Vous pouvez obtenir les mêmes données utilisateur dans votre service mobile en appelant la fonction **user.getIdentities()** dans les scripts serveur. Pour plus d'informations, consultez [ce billet](http://go.microsoft.com/fwlink/p/?LinkId=506605).

##<a name="register"></a>Inscription de votre application pour l'authentification et configuration de Mobile Services

[AZURE.INCLUDE [mobile-services-register-authentication](../includes/mobile-services-register-authentication.md)] 

<ol start="5">
<li><p>(Facultatif) Suivez la procédure décrite dans la rubrique <a href="/fr-fr/documentation/articles/mobile-services-how-to-register-store-app-package-microsoft-authentication/">Inscription du package de votre application Windows Store pour l'authentification Microsoft</a>.</p>
<p>Notez que cette étape est facultative, car elle concerne uniquement le fournisseur de connexion du compte Microsoft. Lorsque vous inscrivez les informations du package de votre application Windows Store auprès de Mobile Services, le client peut réutiliser les informations d'identification du compte Microsoft pour fournir une authentification unique. Si vous ne le faites pas, vos utilisateurs se connectant via le compte Microsoft seront invités à se connecter à chaque appel de la méthode de connexion. Suivez cette étape si vous prévoyez d'utiliser le fournisseur d'identité du compte Microsoft.</p>
</li>
</ol>

##<a name="permissions"></a>Restriction des autorisations pour les utilisateurs authentifiés

[AZURE.INCLUDE [mobile-services-restrict-permissions-javascript-backend](../includes/mobile-services-restrict-permissions-javascript-backend.md)] 

<ol start="3">
<li><p>Dans Visual Studio, cliquez avec le bouton droit sur le projet Windows Store pour l'application TodoList, puis cliquez sur <strong>Définir comme projet de démarrage</strong>.</p></li>
<li><p>Dans le projet partagé, ouvrez le fichier de projet App.xaml.cs, accédez à la définition <a href="http://msdn.microsoft.com/fr-fr/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.aspx">MobileServiceClient</a> et assurez-vous qu'elle est configurée pour une connexion au service mobile exécuté dans Azure.</p>
<p>Notez que lorsque vous utilisez les outils Visual Studio pour connecter votre application à un service mobile, l'outil génère deux ensembles de définitions <strong>MobileServiceClient</strong>, une pour chaque plateforme cliente. C'est le bon moment pour simplifier le code généré en unifiant les définitions <code>#if...#endif</code> <strong>MobileServiceClient</strong> encapsulées dans en une seule définition désencapsulée, utilisée pour les deux versions de l'application. Cette opération n'est pas nécessaire si vous avez téléchargé l'application de démarrage rapide à partir du portail de gestion Azure.</p>
</li> 
<li><p>Appuyez sur la touche F5 pour exécuter l'application Windows Store, et vérifiez qu'une exception non prise en charge avec le code d'état 401 (Unauthorized) est déclenchée après le démarrage de l'application.</p>
   
   	<p>Cela se produit, car l'application tente d'accéder à Mobile Services en tant qu'utilisateur non authentifié, alors que la table <em>TodoItem</em> requiert désormais l'authentification.</p></li>
</ol>

Ensuite, vous allez mettre à jour l'application pour authentifier les utilisateurs avant de demander des ressources à partir du service mobile.

##<a name="add-authentication"></a> Ajout de l'authentification à l'application

[AZURE.INCLUDE [mobile-services-windows-universal-dotnet-authenticate-app](../includes/mobile-services-windows-universal-dotnet-authenticate-app.md)] 

##<a name="tokens"></a>Stockage du jeton d'authentification sur le client

[AZURE.INCLUDE [mobile-services-windows-store-dotnet-authenticate-app-with-token](../includes/mobile-services-windows-store-dotnet-authenticate-app-with-token.md)] 

## <a name="next-steps"> </a>Étapes suivantes

Dans le didacticiel suivant, [Autorisation côté service des utilisateurs Mobile Services][Autorisation des utilisateurs avec des scripts], vous allez prendre la valeur d'ID utilisateur fournie par Mobile Services sur la base d'un utilisateur authentifié et l'utiliser pour filtrer les données renvoyées par Mobile Services. Obtenez plus d'informations sur Mobile Services avec .NET dans le [guide de fonctionnement Mobile Services .NET].

<!-- Anchors. -->
[Inscription de votre application pour l'authentification et configuration de Mobile Services]: #register
[Restriction des autorisations de table pour les utilisateurs authentifiés]: #permissions
[Ajout de l'authentification à l'application]: #add-authentication
[Stockage de jetons d'authentification sur le client]: #tokens
[Étapes suivantes]:#next-steps


<!-- URLs. -->
[Page Soumette une application]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Mes Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Kit de développement logiciel (SDK) Live pour Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Authentification unique pour les applications Windows Store à l'aide de Live Connect]: /fr-fr/documentation/articles/mobile-services-windows-store-dotnet-single-sign-on
[Prise en main de Mobile Services]: /fr-fr/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-get-started/
[Prise en main des données]: /fr-fr/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-get-started-data/
[Prise en main de l'authentification]: /fr-fr/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-get-started-users/
[Prise en main des notifications Push]: /fr-fr/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-get-started-push/
[Autorisation des utilisateurs avec des scripts]: /fr-fr/documentation/articles/mobile-services-windows-store-dotnet-authorize-users-in-scripts
[JavaScript et HTML]: /fr-fr/documentation/articles/mobile-services-windows-store-javascript-get-started-users/

[Portail de gestion Azure]: https://manage.windowsazure.com/
[Guide de fonctionnement Mobile Services .NET]: /fr-fr/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library/
[Inscription du package de votre application Windows Store pour l'authentification Microsoft]: /fr-fr/documentation/articles/mobile-services-how-to-register-store-app-package-microsoft-authentication/


<!--HONumber=42-->
