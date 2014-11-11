<properties pageTitle="Service-side authorization (Android) | Mobile Dev Center" metaKeywords="" description="Learn how to authorize users in the JavaScript backend of Azure Mobile Services." metaCanonical="" services="" documentationCenter="Mobile" title="Service-side authorization of Mobile Services users" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="Mobile-Android" ms.devlang="Java" ms.topic="article" ms.date="01/01/1900" ms.author="glenga" />

# Autorisation côté service des utilisateurs Mobile Services

<div class="dev-center-tutorial-selector sublanding"><a href="/fr-fr/documentation/articles/mobile-services-windows-store-dotnet-authorize-users-in-scripts" title="Windows Store C#">Windows Store C#</a><a href="/fr-fr/documentation/articles/mobile-services-windows-store-javascript-authorize-users-in-scripts" title="Windows Store JavaScript">Windows Store JavaScript</a><a href="/fr-fr/documentation/articles/mobile-services-windows-phone-authorize-users-in-scripts" title="Windows Phone">Windows Phone</a><a href="/fr-fr/documentation/articles/mobile-services-ios-authorize-users-in-scripts" title="iOS">iOS</a><a href="/fr-fr/documentation/articles/mobile-services-android-authorize-users-in-scripts" title="Android" class="current">Android</a><a href="/fr-fr/documentation/articles/mobile-services-html-authorize-users-in-scripts" title="HTML" class="current">HTML</a><a href="/fr-fr/documentation/articles/partner-xamarin-mobile-services-ios-authorize-users-in-scripts" title="Xamarin.iOS">Xamarin.iOS</a><a href="/fr-fr/documentation/articles/partner-xamarin-mobile-services-android-authorize-users-in-scripts" title="Xamarin.Android" class="current">Xamarin.Android</a></div>

<div class="dev-center-tutorial-subselector"><a href="/fr-fr/documentation/articles/mobile-services-android-authorize-users-in-scripts/"  title="JavaScript backend" class="current">JavaScript backend</a></div>

Cette rubrique explique comment utiliser des scripts serveur pour autoriser les utilisateurs authentifiés à accéder aux données dans Azure Mobile Services à partir d'une application Android. Dans ce didacticiel, vous allez inscrire les scripts dans Mobile Services pour filtrer les requêtes sur la base de la valeur userId d'un utilisateur authentifié, en vous assurant que chaque utilisateur parvient à voir uniquement ses propres données.

Ce didacticiel est basé sur le démarrage rapide de Mobile Services et s'appuie sur le didacticiel précédent [Prise en main de l'authentification][Prise en main de l'authentification]. Avant de commencer, vous devez suivre le didacticiel [Prise en main de l'authentification][Prise en main de l'authentification].

## <a name="register-scripts"></a>Inscription des scripts

Étant donné que l'application de démarrage rapide lit et insère les données, vous devez inscrire les scripts de ces opérations dans la table TodoItem.

1.  Connectez-vous au [portail de gestion Azure][portail de gestion Azure], cliquez sur **Mobile Services**, puis sur l'application.

    ![][0]

2.  Cliquez sur l'onglet **Données**, puis sur la table **TodoItem**.

    ![][1]

3.  Cliquez sur **Script**, puis sélectionnez l'opération **Insert**.

    ![][2]

4.  Remplacez le script existant par la fonction suivante, puis cliquez sur **Enregistrer**.

        function insert(item, user, request) {
          item.userId = user.userId;    
          request.execute();
        }

    Ce script ajoute une valeur userId à l'élément, qui correspond à l'ID de l'utilisateur authentifié, avant de l'insérer dans la table TodoItem.

    <div class="dev-callout"><b>Remarque</b>
<p>Le sch&eacute;ma dynamique doit &ecirc;tre activ&eacute; la premi&egrave;re fois que ce script d'insertion s'ex&eacute;cute. Avec le sch&eacute;ma dynamique activ&eacute;, Mobile Services ajoute automatiquement la colonne <strong>userId</strong> &agrave; la table <strong>TodoItem</strong> lors de la premi&egrave;re ex&eacute;cution. Par d&eacute;faut, le sch&eacute;ma dynamique est activ&eacute; pour un nouveau service mobile. Il doit &ecirc;tre d&eacute;sactiv&eacute; avant que l'application soit publi&eacute;e sur le Windows Store.</p>
</div>

5.  Répétez les étapes 3 et 4 pour remplacer l'opération **Read** existante par la fonction suivante :

        function read(query, user, request) {
           query.where({ userId: user.userId });    
           request.execute();
        }

    Ce script filtre les objets TodoItem renvoyés de façon à ce que chaque utilisateur reçoive uniquement les éléments qu'il a inséré.

## Test de l'application

1.  Dans Eclipse, ouvrez le projet que vous avez modifié avec le didacticiel [Prise en main de l'authentification][Prise en main de l'authentification].

2.  À partir du menu **Exécuter**, cliquez sur **Exécuter** pour démarrer l'application et vous connecter avec le fournisseur d'identité choisi.

    Remarquez que cette fois, bien que certains éléments provenant des didacticiels précédents figurent déjà dans la table TodoItem, aucun élément n'est renvoyé. Cela se produit parce que les éléments précédents ont été insérés sans la colonne userId et comportent maintenant des valeurs null.

3.  Dans l'application, entrez du texte dans **Insert a TodoItem**, puis cliquez sur **Enregistrer**.

    Cette action insère à la fois le texte et la valeur userId dans la table TodoItem du service mobile. Étant donné que le nouvel élément dispose de la valeur userId correcte, il est renvoyé par le service mobile et affiché dans la seconde colonne.

4.  De retour dans la table **todoitem** sur le [portail de gestion][portail de gestion Azure], cliquez sur **Parcourir** et vérifiez que chaque élément récemment ajouté présente une valeur userId associée.

5.  (Facultatif) Si vous disposez de comptes de connexion supplémentaires, vous pouvez vous assurer que les utilisateurs peuvent uniquement afficher leurs propres données en fermant l'application, puis en la réexécutant. Une fois que la boîte de dialogue des informations d'identification apparaît, entrez une autre connexion, puis vérifiez que les éléments entrés sous le compte précédent ne s'affichent pas.

## Étapes suivantes

Cela met fin à la série de didacticiels établissant les principes de base de l'utilisation de l'authentification. Pour plus d'informations sur les rubriques Mobile Services suivantes :

-   [Prise en main des données][Prise en main des données]
   
    En savoir plus sur le stockage et l'interrogation des données à l'aide de Mobile Services.

-   [Prise en main des notifications Push][Prise en main des notifications Push]
 
    En savoir plus sur l'envoi d'une notification Push très basique sur votre application.

-   [Référence de script serveur Mobile Services][Référence de script serveur Mobile Services]
 
    En savoir plus sur l'enregistrement et l'utilisation des scripts serveur.


  [Prise en main de l'authentification]: /fr-fr/develop/mobile/tutorials/get-started-with-users-android
  [portail de gestion Azure]: https://manage.windowsazure.com/
  [0]: ./media/mobile-services-android-authorize-users-in-scripts/mobile-services-selection.png
  [1]: ./media/mobile-services-android-authorize-users-in-scripts/mobile-portal-data-tables.png
  [2]: ./media/mobile-services-android-authorize-users-in-scripts/mobile-insert-script-users.png
  [Prise en main des données]: /fr-fr/develop/mobile/tutorials/get-started-with-data-android
  [Prise en main des notifications Push]: /fr-fr/develop/mobile/tutorials/get-started-with-push-android
  [Référence de script serveur Mobile Services]: http://go.microsoft.com/fwlink/p/?LinkId=262293
