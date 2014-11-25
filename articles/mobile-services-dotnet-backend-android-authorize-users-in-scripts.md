<properties pageTitle="Autorisation côté service (Android) | Centre de développement mobile" metaKeywords="" description="Learn how to authorize users in the .NET backend of Azure Mobile Services." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Service-side authorization of Mobile Services users" authors="glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-android" ms.devlang="java" ms.topic="article" ms.date="10/20/2014" ms.author="glenga" />

# Autorisation côté service des utilisateurs Mobile Services

[WACOM.INCLUDE [mobile-services-selector-service-auth-users](../includes/mobile-services-selector-service-auth-users.md)]

Cette rubrique explique comment autoriser les utilisateurs authentifiés à accéder aux données dans Azure Mobile Services à partir d'une application Android. Dans ce didacticiel, vous allez ajouter du code aux méthodes d'accès aux données de votre contrôleur filtrant les requêtes selon la valeur userId d'un utilisateur authentifié, afin de garantir que chaque utilisateur peut voir uniquement ses propres données.

Ce didacticiel est basé sur le démarrage rapide de Mobile Services et s'appuie sur le didacticiel précédent [Prise en main de l'authentification]. Avant de commencer, vous devez suivre le didacticiel [Prise en main de l'authentification].  

## <a name="register-scripts"></a>Modification des méthodes d'accès aux données

[WACOM.INCLUDE [mobile-services-filter-user-results-dotnet-backend](../includes/mobile-services-filter-user-results-dotnet-backend.md)]


## Test de l'application

1. Dans Eclipse, ouvrez le projet que vous avez modifié avec le didacticiel [Prise en main de l'authentification].

2. À partir du menu **Exécuter**, cliquez sur **Exécuter** pour démarrer le projet dans l'émulateur Android.

   	Remarquez que cette fois, bien que certains éléments provenant des didacticiels précédents figurent déjà dans la table TodoItem, aucun élément n'est renvoyé. Cela se produit parce que les éléments précédents ont été insérés sans la colonne userId et comportent maintenant des valeurs null.

3. Dans l'application, entrez du texte dans **Ajouter une tâche**, puis cliquez sur **Enregistrer**.

   	![][3]

   	Cette action insère à la fois le texte et la valeur userId dans la table TodoItem du service mobile. Étant donné que le nouvel élément dispose de la valeur userId correcte, il est renvoyé par le service mobile et affiché dans la seconde colonne.

5. De retour dans la table **todoitem** sur le [portail de gestion][portail de gestion Azure], cliquez sur **Parcourir** et vérifiez que chaque élément récemment ajouté présente une valeur userId associée.

6. (Facultatif) Si vous disposez de comptes de connexion supplémentaires, vous pouvez vous assurer que les utilisateurs peuvent uniquement afficher leurs propres données en fermant l'application, puis en la réexécutant. Une fois que la boîte de dialogue des informations d'identification apparaît, entrez une autre connexion, puis vérifiez que les éléments entrés sous le compte précédent ne s'affichent pas.

## Étapes suivantes

Cela met fin à la série de didacticiels établissant les principes de base de l'utilisation de l'authentification. Pour plus d'informations sur les rubriques Mobile Services suivantes :

* [Prise en main des données]
  <br/>En savoir plus sur le stockage et l'interrogation des données à l'aide de Mobile Services.

* [Prise en main des notifications Push]
  <br/>En savoir plus sur l'envoi d'une notification Push très basique sur votre application.

* [Guide de fonctionnement de Mobile Services avec Android]
  <br/>En savoir plus sur l'utilisation de Mobile Services avec Android.


[Inscrire les scripts serveur]: #register-scripts
[Étapes suivantes]:#next-steps



[3]: ./media/mobile-services-dotnet-backend-android-authorize-users-in-scripts/mobile-quickstart-startup-android.png


[Prise en main de Mobile Services]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-android-get-started
[Prise en main des données]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-android-get-started-data
[Prise en main de l'authentification]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-android-get-started-users
[Prise en main des notifications Push]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-android-get-started-push

[Guide de fonctionnement de Mobile Services avec Android]: /fr-fr/documentation/articles/mobile-services-android-how-to-use-client-library/
