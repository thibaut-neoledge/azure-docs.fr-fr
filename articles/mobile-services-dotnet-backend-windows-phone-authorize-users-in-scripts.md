<properties pageTitle="Service-side authorization (Windows Phone) | Mobile Dev Center" metaKeywords="" description="Learn how to authorize users in the .NET backend of Azure Mobile Services." metaCanonical="" services="" documentationCenter="Mobile" title="Service-side authorization of Mobile Services users" authors="glenga" solutions="" manager="" editor="" />

Autorisation côté service des utilisateurs Mobile Services
==========================================================

[Windows Store C\#](/en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-authorize-users-in-scripts "Windows Store C#")[Windows Store JavaScript](/en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-authorize-users-in-scripts "Windows Store JavaScript")[Windows Phone](/en-us/documentation/articles/mobile-services-dotnet-backend-windows-phone-authorize-users-in-scripts "Windows Phone")[iOS](/en-us/documentation/articles/mobile-services-dotnet-backend-ios-authorize-users-in-scripts "iOS")

[.NET principal](/en-us/documentation/articles/mobile-services-dotnet-backend-windows-phone-authorize-users-in-scripts/ ".NET principal") | [JavaScript principal](/en-us/documentation/articles/mobile-services-windows-phone-authorize-users-in-scripts/ "JavaScript principal")

Cette rubrique explique comment autoriser les utilisateurs authentifiés à accéder aux données dans Azure Mobile Services à partir d'une application Windows Phone. Dans ce didacticiel, vous allez ajouter du code aux méthodes d'accès aux données de votre contrôleur filtrant les requêtes selon la valeur userId d'un utilisateur authentifié, afin de garantir que chaque utilisateur peut voir uniquement ses propres données.

Ce didacticiel est basé sur le démarrage rapide de Mobile Services et s'appuie sur le didacticiel précédent [Prise en main de l'authentification](/en-us/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-users). Avant de commencer, vous devez suivre le didacticiel [Prise en main de l'authentification](/en-us/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-users).

Modification des méthodes d'accès aux données
---------------------------------------------

[WACOM.INCLUDE [mobile-services-filter-user-results-dotnet-backend](../includes/mobile-services-filter-user-results-dotnet-backend.md)]

Test de l'application
---------------------

1.  Dans Visual Studio 2013 pour Windows Phone, ouvrez le projet que vous avez modifié en suivant le didacticiel [Prise en main de l'authentification](/en-us/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-users).

2.  Appuyez sur la touche F5 pour exécuter l'application, puis connectez-vous avec le fournisseur d'identité choisi.

	Remarquez que cette fois, bien que certains éléments provenant des didacticiels précédents figurent déjà dans la table TodoItem, aucun élément n'est renvoyé. Cela se produit parce que les éléments précédents ont été insérés sans la colonne userId et comportent maintenant des valeurs null.

3.  Dans l'application, entrez du texte dans la zone de texte, puis cliquez sur **Enregistrer**.

	![][3]

	Cette action insère à la fois le texte et la valeur userId dans la table TodoItem du service mobile. Comme le nouvel élément présente la valeur userId adéquate, il est renvoyé par le service mobile.

4.  (Facultatif) Si vous disposez de comptes de connexion supplémentaires, vous pouvez vous assurer que les utilisateurs peuvent uniquement afficher leurs propres données en fermant l'application (Alt+F4), puis en la réexécutant. Une fois que la boîte de dialogue des informations d'identification apparaît, entrez une autre connexion, puis vérifiez que les éléments entrés sous le compte précédent ne s'affichent pas.

Étapes suivantes
----------------

Cela met fin à la série de didacticiels établissant les principes de base de l'utilisation de l'authentification. Pour plus d'informations sur les rubriques Mobile Services suivantes :

-   [Prise en main des données](/en-us/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-data)
    En savoir plus sur le stockage et l'interrogation des données à l'aide de Mobile Services.

-   [Prise en main des notifications Push](/en-us/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-push)
    En savoir plus sur l'envoi d'une notification Push très basique sur votre application.

-   [Guide de fonctionnement Mobile Services .NET](/en-us/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library/)
    En savoir plus sur l'utilisation de Mobile Services avec .NET.


<!-- Anchors. -->
[Register server scripts]: #register-scripts
[Next Steps]:#next-steps

<!-- Images. -->

[3]: ./media/mobile-services-dotnet-backend-windows-phone-authorize-users-in-scripts/mobile-quickstart-startup.png

<!-- URLs. -->
[Get started with Mobile Services]: /en-us/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started
[Get started with data]: /en-us/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-data
[Get started with authentication]: /en-us/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-users
[Get started with push notifications]: /en-us/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-push

[Mobile Services .NET How-to Conceptual Reference]: /en-us/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library/