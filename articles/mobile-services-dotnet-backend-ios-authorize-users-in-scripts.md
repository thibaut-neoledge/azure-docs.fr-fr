<properties pageTitle="Service-side authorization (iOS) | Mobile Dev Center" metaKeywords="" description="Learn how to authorize users in the .NET backend of Azure Mobile Services." metaCanonical="" services="" documentationCenter="Mobile" title="Service-side authorization of Mobile Services users" authors="krisragh" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="01/01/1900" ms.author="krisragh" />

# Autorisation côté service des utilisateurs Mobile Services

<div class="dev-center-tutorial-selector sublanding"><a href="/fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-authorize-users-in-scripts" title="Windows Store C#">Windows Store C#</a><a href="/fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-authorize-users-in-scripts" title="Windows Store JavaScript">Windows Store JavaScript</a><a href="/fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-phone-authorize-users-in-scripts" title="Windows Phone">Windows Phone</a><a href="/fr-fr/documentation/articles/mobile-services-dotnet-backend-ios-authorize-users-in-scripts" title="iOS" class="current">iOS</a></div>

<div class="dev-center-tutorial-subselector"><a href="/fr-fr/documentation/articles/mobile-services-dotnet-backend-ios-authorize-users-in-scripts/" title=".NET backend" class="current">.NET backend</a> | <a href="/fr-fr/documentation/articles/mobile-services-ios-authorize-users-in-scripts/"  title="JavaScript backend">JavaScript backend</a></div>

Cette rubrique explique comment autoriser les utilisateurs authentifiés à accéder aux données dans Azure Mobile Services à partir d'une application iOS. Dans ce didacticiel, vous allez ajouter du code aux méthodes d'accès aux données de votre contrôleur filtrant les requêtes selon la valeur userId d'un utilisateur authentifié, afin de garantir que chaque utilisateur peut voir uniquement ses propres données.

Ce didacticiel est basé sur le démarrage rapide de Mobile Services et s'appuie sur le didacticiel précédent [Prise en main de l'authentification][Prise en main de l'authentification]. Avant de commencer, vous devez suivre le didacticiel [Prise en main de l'authentification][Prise en main de l'authentification].

## <a name="register-scripts"></a>Modification des méthodes d'accès aux données

[WACOM.INCLUDE [mobile-services-filter-user-results-dotnet-backend](../includes/mobile-services-filter-user-results-dotnet-backend.md)]

## Test de l'application

1.  Dans Xcode, ouvrez le projet que vous avez modifié avec le didacticiel [Prise en main de l'authentification][Prise en main de l'authentification].

2.  Appuyez sur le bouton **Exécuter** pour générer le projet, démarrez l'application dans l'émulateur iPhone, puis connectez-vous avec le fournisseur d'identité de votre choix.

    Remarquez que cette fois, bien que certains éléments provenant des didacticiels précédents figurent déjà dans la table TodoItem, aucun élément n'est renvoyé. Cela se produit parce que les éléments précédents ont été insérés sans la colonne userId et comportent maintenant des valeurs null.

3.  Dans l'application, entrez du texte dans **Insert a TodoItem**, puis cliquez sur **Enregistrer**.

    ![][0]

    Cette action insère à la fois le texte et la valeur userId dans la table TodoItem du service mobile. Étant donné que le nouvel élément dispose de la valeur userId correcte, il est renvoyé par le service mobile et affiché dans la seconde colonne.

4.  De retour dans la table **todoitem** sur le [portail de gestion][portail de gestion Azure], cliquez sur **Parcourir** et vérifiez que chaque élément récemment ajouté présente une valeur userId associée.

5.  (Facultatif) Si vous disposez de comptes de connexion supplémentaires, vous pouvez vous assurer que les utilisateurs peuvent uniquement afficher leurs propres données en fermant l'application, puis en la réexécutant. Une fois que la boîte de dialogue des informations d'identification apparaît, entrez une autre connexion, puis vérifiez que les éléments entrés sous le compte précédent ne s'affichent pas.

<!--## Next steps  This concludes the tutorials that demonstrate the basics of working with authentication. Consider finding out more about the following Mobile Services topics:  * [Get started with data]   <br/>Learn more about storing and querying data using Mobile Services.  * [Get started with push notifications]    <br/>Learn how to send a very basic push notification to your app.    * [Mobile Services .NET How-to Conceptual Reference]   <br/>Learn more about how to use Mobile Services with .NET. --> 

  [Prise en main de l'authentification]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-ios-get-started-users
  [mobile-services-filter-user-results-dotnet-backend]: ../includes/mobile-services-filter-user-results-dotnet-backend.md
  [0]: ./media/mobile-services-dotnet-backend-ios-authorize-users-in-scripts/mobile-quickstart-startup-ios.png
