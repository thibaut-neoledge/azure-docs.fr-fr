<properties pageTitle="Autorisation côté service (Android) | Centre de développement mobile" description="Découvrez comment autoriser des utilisateurs sur le serveur principal JavaScript d'Azure Mobile Services." services="" documentationCenter="android" authors="ggailey777" manager="dwrede" editor=""/>

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="Mobile-Android" ms.devlang="Java" ms.topic="article" ms.date="09/29/2014" ms.author="glenga"/>

# Autorisation côté service des utilisateurs Mobile Services

[AZURE.INCLUDE [mobile-services-selector-service-auth-users](../includes/mobile-services-selector-service-auth-users.md)]	

Cette rubrique explique comment utiliser des scripts serveur pour autoriser les utilisateurs authentifiés à accéder aux données dans Azure Mobile Services à partir d'une application Android.  Dans ce didacticiel, vous allez inscrire les scripts dans Mobile Services pour filtrer les requêtes sur la base de la valeur userId d'un utilisateur authentifié, en vous assurant que chaque utilisateur parvient à voir uniquement ses propres données.

##Conditions préalables

[AZURE.INCLUDE [mobile-services-android-prerequisites](../includes/mobile-services-android-prerequisites.md)]  

## <a name="register-scripts"></a>Inscription des scripts
Étant donné que l'application de démarrage rapide lit et insère les données, vous devez inscrire les scripts de ces opérations dans la table TodoItem.

1. Connectez-vous au [portail de gestion Azure], puis cliquez sur **Mobile Services** et sur l'application. 

   	![][0]

2. Cliquez sur l'onglet **Données**, puis sur la table **TodoItem**.

   	![][1]

3. Cliquez sur **Script**, puis sélectionnez l'opération **Insert**.

   	![][2]

4. Remplacez le script existant par la fonction suivante, puis cliquez sur **Enregistrer**.
     function insert(item, user, request) {       item.userId = user.userId;           request.execute();     }
 Ce script ajoute une valeur userId à l'élément, qui correspond à l'ID de l'utilisateur authentifié, avant de l'insérer dans la table TodoItem. 
 > [AZURE.NOTE] Le schéma dynamique doit être activé la première fois que ce script d'insertion s'exécute. Avec le schéma dynamique activé, Mobile Services ajoute automatiquement la colonne **userId** à la table **TodoItem** lors de la première exécution. Par défaut, le schéma dynamique est activé pour un nouveau service mobile. Il doit être désactivé avant que l'application soit publiée sur le Windows Store.


5. Répétez les étapes 3 et 4 pour remplacer l'opération **Read** existante par la fonction suivante :
     function read(query, user, request) {        query.where({ userId: user.userId });            request.execute();     }

   	Ce script filtre les objets TodoItem renvoyés de façon à ce que chaque utilisateur reçoive uniquement les éléments qu'il a inséré.

## Test de l'application

1. Dans Eclipse, ouvrez le projet que vous avez modifié avec le didacticiel [Prise en main de l'authentification].

2. À partir du menu **Exécuter**, cliquez sur **Exécuter** pour démarrer l'application et vous connecter avec le fournisseur d'identité choisi. 

   	Remarquez que cette fois, bien que certains éléments provenant des didacticiels précédents figurent déjà dans la table TodoItem, aucun élément n'est renvoyé. Cela se produit parce que les éléments précédents ont été insérés sans la colonne userId et comportent maintenant des valeurs null.

3. Dans l'application, entrez du texte dans **Insérer un TodoItem**, puis cliquez sur **Enregistrer**.

   	Cette action insère à la fois le texte et la valeur userId dans la table TodoItem du service mobile. Étant donné que le nouvel élément dispose de la valeur userId correcte, il est renvoyé par le service mobile et affiché dans la seconde colonne.

5. De retour dans la table **todoitem** dans le [portail de gestion][portail de gestion Azure], cliquez sur **Parcourir** et vérifiez qu'une valeur userId est associée à chaque élément récemment ajouté.

6. (Facultatif) Si vous disposez de comptes de connexion supplémentaires, vous pouvez vous assurer que les utilisateurs peuvent uniquement afficher leurs propres données en fermant l'application, puis en l'exécutant de nouveau. Une fois que la boîte de dialogue des informations d'identification apparaît, entrez une autre connexion, puis vérifiez que les éléments entrés sous le compte précédent ne s'affichent pas.

## Étapes suivantes

Cela met fin à la série de didacticiels établissant les principes de base de l'utilisation de l'authentification. Pour plus d'informations sur les rubriques Mobile Services suivantes :

* [Prise en main des données]
  <br/>En savoir plus sur le stockage et l'interrogation des données à l'aide de Mobile Services.

* [Prise en main des notifications Push] 
  <br/>En savoir plus sur l'envoi d'une notification Push très basique sur votre application.

* [Référence de script serveur Mobile Services]
  <br/>En savoir plus sur l'enregistrement et l'utilisation des scripts serveur.

<!-- Anchors. -->
[Enregistrement de scripts serveur]: #register-scripts
[Étapes suivantes]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-android-authorize-users-in-scripts/mobile-services-selection.png
[1]: ./media/mobile-services-android-authorize-users-in-scripts/mobile-portal-data-tables.png
[2]: ./media/mobile-services-android-authorize-users-in-scripts/mobile-insert-script-users.png


<!-- URLs. -->
[Référence de script serveur Mobile Services]: http://go.microsoft.com/fwlink/p/?LinkId=262293
[Tableau de bord Mes applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Prise en main de Mobile Services]: /fr-fr/develop/mobile/tutorials/get-started-android
[Prise en main des données]: /fr-fr/develop/mobile/tutorials/get-started-with-data-android
[Prise en main de l'authentification]: /fr-fr/develop/mobile/tutorials/get-started-with-users-android
[Prise en main des notifications Push]: /fr-fr/develop/mobile/tutorials/get-started-with-push-android

[Portail de gestion Azure]: https://manage.windowsazure.com/


<!--HONumber=42-->
