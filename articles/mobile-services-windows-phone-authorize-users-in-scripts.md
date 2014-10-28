<properties pageTitle="Service-side authorization (Windows Phone) | Mobile Dev Center" metaKeywords="" description="Learn how to authorize users in the JavaScript backend of Azure Mobile Services." metaCanonical="" services="" documentationCenter="Mobile" title="Service-side authorization of Mobile Services users" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-phone" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="glenga"></tags>

# Autorisation côté service des utilisateurs Mobile Services

<div class="dev-center-tutorial-selector sublanding"><a href="/fr-fr/documentation/articles/mobile-services-windows-store-dotnet-authorize-users-in-scripts" title="Windows Store C#">Windows Store C#</a><a href="/fr-fr/documentation/articles/mobile-services-windows-store-javascript-authorize-users-in-scripts" title="Windows Store JavaScript">Windows Store JavaScript</a><a href="/fr-fr/documentation/articles/mobile-services-windows-phone-authorize-users-in-scripts" title="Windows Phone" class="current">Windows Phone</a><a href="/fr-fr/documentation/articles/mobile-services-ios-authorize-users-in-scripts" title="iOS" class="current">iOS</a><a href="/fr-fr/documentation/articles/mobile-services-android-authorize-users-in-scripts" title="Android" class="current">Android</a><a href="/fr-fr/documentation/articles/mobile-services-html-authorize-users-in-scripts" title="HTML" class="current">HTML</a><a href="/fr-fr/documentation/articles/partner-xamarin-mobile-services-ios-authorize-users-in-scripts" title="Xamarin.iOS">Xamarin.iOS</a><a href="/fr-fr/documentation/articles/partner-xamarin-mobile-services-android-authorize-users-in-scripts" title="Xamarin.Android" class="current">Xamarin.Android</a></div>

<div class="dev-center-tutorial-subselector"><a href="/fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-phone-authorize-users-in-scripts/" title=".NET backend">.NET backend</a> | <a href="/fr-fr/documentation/articles/mobile-services-windows-phone-authorize-users-in-scripts/"  title="JavaScript backend" class="current">JavaScript backend</a></div>

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">

<p>Cette rubrique explique comment utiliser des scripts serveur pour autoriser les utilisateurs authentifi&eacute;s &agrave; acc&eacute;der aux donn&eacute;es dans Azure Mobile Services &agrave; partir d'une application Windows Phone&nbsp;8. Dans ce didacticiel, vous allez inscrire les scripts dans Mobile Services pour filtrer les requ&ecirc;tes sur la base de la valeur userId d'un utilisateur authentifi&eacute;, en vous assurant que chaque utilisateur parvient &agrave; voir uniquement ses propres donn&eacute;es.</p>
<p>Ce didacticiel est bas&eacute; sur le d&eacute;marrage rapide de Mobile Services et s'appuie sur le didacticiel pr&eacute;c&eacute;dent <a href="/fr-fr/develop/mobile/tutorials/get-started-with-users-wp8">Prise en main de l'authentification</a>. Avant de commencer, vous devez suivre le didacticiel <a href="/fr-fr/develop/mobile/tutorials/get-started-with-users-wp8">Prise en main de l'authentification</a>.</p>
</div>

<div class="dev-onpage-video-wrapper"><a href="http://go.microsoft.com/fwlink/?LinkId=298630" target="_blank" class="label">regarder le didacticiel</a> <a style="background-image: url('/media/devcenter/mobile/videos/mobile-wp8-scripts-for-authentication-180x120.png') !important;" href="http://go.microsoft.com/fwlink/?LinkId=298630" target="_blank" class="dev-onpage-video"><span class="icon">Lire la vid&eacute;o</span></a> <span class="time">15:00:00</span></div>

</div>

## <a name="register-scripts"></a>Inscription des scripts

Étant donné que l'application de démarrage rapide lit et insère les données, vous devez inscrire les scripts de ces opérations dans la table TodoItem.

1.  Connectez-vous au [portail de gestion Azure][], cliquez sur **Mobile Services**, puis sur l'application.

    ![][]

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
<p>Le sch&eacute;ma dynamique doit &ecirc;tre activ&eacute; la premi&egrave;re fois que ce script d'insertion s'ex&eacute;cute. Avec le sch&eacute;ma dynamique activ&eacute;, Mobile Services ajoute automatiquement la colonne <strong>userId</strong> &agrave; la table <strong>TodoItem</strong> lors de la premi&egrave;re ex&eacute;cution. Par d&eacute;faut, le sch&eacute;ma dynamique est activ&eacute; pour un nouveau service mobile. Il doit &ecirc;tre d&eacute;sactiv&eacute; avant que l'application soit publi&eacute;e sur le Windows Phone Store.</p>
</div>

5.  Répétez les étapes 3 et 4 pour remplacer l'opération **Read** existante par la fonction suivante :

        function read(query, user, request) {
           query.where({ userId: user.userId });    
           request.execute();
        }

    Ce script filtre les objets TodoItem renvoyés de façon à ce que chaque utilisateur reçoive uniquement les éléments qu'il a inséré.

## Test de l'application

1.  Dans Visual Studio 2012 Express pour Windows Phone, ouvrez le projet que vous avez modifié en suivant le didacticiel [Prise en main de l'authentification][].

2.  Appuyez sur la touche F5 pour exécuter l'application, puis connectez-vous avec le fournisseur d'identité choisi.

    Remarquez que cette fois, bien que certains éléments provenant des didacticiels précédents figurent déjà dans la table TodoItem, aucun élément n'est renvoyé. Cela se produit parce que les éléments précédents ont été insérés sans la colonne userId et comportent maintenant des valeurs null.

3.  Dans l'application, entrez du texte dans la zone de texte, puis cliquez sur **Enregistrer**.

    ![][3]

    Cette action insère à la fois le texte et la valeur userId dans la table TodoItem du service mobile. Étant donné que le nouvel élément présente la valeur userId correcte, il est renvoyé par le service mobile.

4.  De retour dans la table **todoitem** sur le [portail de gestion][portail de gestion Azure], cliquez sur **Parcourir** et vérifiez que chaque élément récemment ajouté présente une valeur userId associée.

## Étapes suivantes

Cela met fin à la série de didacticiels établissant les principes de base de l'utilisation de l'authentification. Pour plus d'informations sur les rubriques Mobile Services suivantes :

-   [Prise en main des données][]

    En savoir plus sur le stockage et l'interrogation des données à l'aide de Mobile Services.

-   [Prise en main des notifications Push][]

    En savoir plus sur l'envoi d'une notification Push très basique sur votre application.

-   [Référence de script serveur Mobile Services][]

    En savoir plus sur l'enregistrement et l'utilisation des scripts serveur.

  [Windows Store C#]: /fr-fr/documentation/articles/mobile-services-windows-store-dotnet-authorize-users-in-scripts "Windows Store C#"
  [Windows Store JavaScript]: /fr-fr/documentation/articles/mobile-services-windows-store-javascript-authorize-users-in-scripts "Windows Store JavaScript"
  [Windows Phone]: /fr-fr/documentation/articles/mobile-services-windows-phone-authorize-users-in-scripts "Windows Phone"
  [iOS]: /fr-fr/documentation/articles/mobile-services-ios-authorize-users-in-scripts "iOS"
  [Android]: /fr-fr/documentation/articles/mobile-services-android-authorize-users-in-scripts "Android"
  [HTML]: /fr-fr/documentation/articles/mobile-services-html-authorize-users-in-scripts "HTML"
  [Xamarin.iOS]: /fr-fr/documentation/articles/partner-xamarin-mobile-services-ios-authorize-users-in-scripts "Xamarin.iOS"
  [Xamarin.Android]: /fr-fr/documentation/articles/partner-xamarin-mobile-services-android-authorize-users-in-scripts "Xamarin.Android"
  [.NET backend]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-phone-authorize-users-in-scripts/ ".NET backend"
  [JavaScript backend]: /fr-fr/documentation/articles/mobile-services-windows-phone-authorize-users-in-scripts/ "JavaScript backend"
  [Prise en main de l'authentification]: /fr-fr/develop/mobile/tutorials/get-started-with-users-wp8
  [regarder le didacticiel]: http://go.microsoft.com/fwlink/?LinkId=298630
  [portail de gestion Azure]: https://manage.windowsazure.com/
  []: ./media/mobile-services-windows-phone-authorize-users-in-scripts/mobile-services-selection.png
  [1]: ./media/mobile-services-windows-phone-authorize-users-in-scripts/mobile-portal-data-tables.png
  [2]: ./media/mobile-services-windows-phone-authorize-users-in-scripts/mobile-insert-script-users.png
  [3]: ./media/mobile-services-windows-phone-authorize-users-in-scripts/mobile-quickstart-startup-wp8.png
  [Prise en main des données]: /fr-fr/develop/mobile/tutorials/get-started-with-data-wp8
  [Prise en main des notifications Push]: /fr-fr/develop/mobile/tutorials/get-started-with-push-wp8
  [Référence de script serveur Mobile Services]: http://go.microsoft.com/fwlink/?LinkId=262293
