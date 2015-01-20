<properties pageTitle="Autorisation côté service (Windows Phone) | Centre de développement mobile" metaKeywords="" description="Découvrez comment autoriser des utilisateurs sur le serveur principal JavaScript d'Azure Mobile Services." metaCanonical="" services="" documentationCenter="Mobile" title="Service-side authorization of Mobile Services users" authors="glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-phone" ms.devlang="dotnet" ms.topic="article" ms.date="09/29/2014" ms.author="glenga" />

# Autorisation côté service des utilisateurs Mobile Services

[WACOM.INCLUDE [mobile-services-selector-service-auth-users](../includes/mobile-services-selector-service-auth-users.md)]	


<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">

<p>Cette rubrique explique comment utiliser des scripts serveur pour autoriser les utilisateurs authentifiés à accéder aux données dans Azure Mobile Services à partir d'une application Windows Phone 8. Dans ce didacticiel, vous allez inscrire les scripts dans Mobile Services pour filtrer les requêtes sur la base de la valeur userId d'un utilisateur authentifié, en vous assurant que chaque utilisateur parvient à voir uniquement ses propres données.</p>
<p>Ce didacticiel est basé sur le démarrage rapide de Mobile Services et s'appuie sur le didacticiel précédent <a href="/fr-fr/develop/mobile/tutorials/get-started-with-users-wp8">Prise en main de l'authentification</a>. Avant de commencer ce didacticiel, vous devez d'abord suivre intégralement celui-ci : <a href="/fr-fr/develop/mobile/tutorials/get-started-with-users-wp8">Prise en main de l'authentification</a>.</p>
</div>
<div class="dev-onpage-video-wrapper"><a href="http://go.microsoft.com/fwlink/?LinkId=298630" target="_blank" class="label">regarder le didacticiel</a> <a style="background-image: url('/media/devcenter/mobile/videos/mobile-wp8-scripts-for-authentication-180x120.png') !important;" href="http://go.microsoft.com/fwlink/?LinkId=298630" target="_blank" class="dev-onpage-video"><span class="icon">Lire la vidéo</span></a> <span class="time">15:00</span></div>
</div> 

## <a name="register-scripts"></a>Inscription des scripts
Étant donné que l'application de démarrage rapide lit et insère les données, vous devez inscrire les scripts de ces opérations dans la table TodoItem.

1. Connectez-vous au [portail de gestion Azure], puis cliquez sur **Mobile Services** et sur l'application. 

   	![][0]

2. Cliquez sur l'onglet **Données**, puis sur la table **TodoItem**.

   	![][1]

3. Cliquez sur **Script**, puis sélectionnez l'opération **Insert**.

   	![][2]

4. Remplacez le script existant par la fonction suivante, puis cliquez sur **Enregistrer**.

        function insert(item, user, request) {
          item.userId = user.userId;    
          request.execute();
        }

    Ce script ajoute une valeur userId à l'élément, qui correspond à l'ID de l'utilisateur authentifié, avant de l'insérer dans la table TodoItem. 

    <div class="dev-callout"><b>Remarque</b>
	<p>Le schéma dynamique doit être activé la première fois que ce script d'insertion s'exécute. Avec le schéma dynamique activé, Mobile Services ajoute automatiquement la colonne <strong>userId</strong> dans la table <strong>TodoItem</strong> lors de la première exécution. Par défaut, le schéma dynamique est activé pour un nouveau service mobile. Il doit être désactivé avant que l'application soit publiée sur le Windows Phone Store.</p>
    </div>


5. Répétez les étapes 3 et 4 pour remplacer l'opération **Read** existante par la fonction suivante :

        function read(query, user, request) {
           query.where({ userId: user.userId });    
           request.execute();
        }

   	Ce script filtre les objets TodoItem renvoyés de façon à ce que chaque utilisateur reçoive uniquement les éléments qu'il a inséré.

## Test de l'application

1. Dans Visual Studio 2012 Express pour Windows Phone, ouvrez le projet que vous avez modifié en suivant le didacticiel [Prise en main de l'authentification].

2. Appuyez sur la touche F5 pour exécuter l'application, puis connectez-vous avec le fournisseur d'identité choisi. 

   	Remarquez que cette fois, bien que certains éléments provenant des didacticiels précédents figurent déjà dans la table TodoItem, aucun élément n'est renvoyé. Cela se produit parce que les éléments précédents ont été insérés sans la colonne userId et comportent maintenant des valeurs null.

3. Dans l'application, entrez du texte dans la zone de texte, puis cliquez sur **Enregistrer**.

   	![][3]

   	Cette action insère à la fois le texte et la valeur userId dans la table TodoItem du service mobile. Étant donné que le nouvel élément présente la valeur userId correcte, il est renvoyé par le service mobile.

5. De retour dans la table **todoitem** sur le [portail de gestion][Azure Management Portal], cliquez sur **Parcourir** et vérifiez que chaque élément nouvellement ajouté présente désormais une valeur userId associée.

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
[0]: ./media/mobile-services-windows-phone-authorize-users-in-scripts/mobile-services-selection.png
[1]: ./media/mobile-services-windows-phone-authorize-users-in-scripts/mobile-portal-data-tables.png
[2]: ./media/mobile-services-windows-phone-authorize-users-in-scripts/mobile-insert-script-users.png
[3]: ./media/mobile-services-windows-phone-authorize-users-in-scripts/mobile-quickstart-startup-wp8.png

<!-- URLs. -->
[Référence de script serveur Mobile Services]: http://go.microsoft.com/fwlink/?LinkId=262293
[Tableau de bord Mes applications]: http://go.microsoft.com/fwlink/?LinkId=262039
[Prise en main de Mobile Services]: /fr-fr/develop/mobile/tutorials/get-started/#create-new-service
[Prise en main des données]: /fr-fr/develop/mobile/tutorials/get-started-with-data-wp8
[Prise en main de l'authentification]: /fr-fr/develop/mobile/tutorials/get-started-with-users-wp8
[Prise en main des notifications Push]: /fr-fr/develop/mobile/tutorials/get-started-with-push-wp8

[Portail de gestion Azure]: https://manage.windowsazure.com/

<!--HONumber=35.2-->
