<properties linkid="develop-mobile-tutorials-authorize-users-in-scripts-xamarin-ios" urlDisplayName="Authorize Users in Scripts (Xamarin.iOS)" pageTitle="Authorize users in scripts (Xamarin.iOS) - Azure Mobile Services" metaKeywords="Azure authorizing user, Xamarin.iOS scripts authorization, authorize mobile services" description="Learn how to authorize users with scripts in your Azure Mobile Services app for Xamarin.iOS." metaCanonical="" disqusComments="1" umbracoNaviHide="1" title="Use scripts to authorize users in Mobile Services" documentationCenter="Mobile" authors="" />

Utilisation de scripts pour autoriser les utilisateurs dans Mobile Services
===========================================================================

[Windows Store C\#](/en-us/develop/mobile/tutorials/authorize-users-in-scripts-dotnet "Windows Store C#")[Windows Store JavaScript](/en-us/develop/mobile/tutorials/authorize-users-in-scripts-js "Windows Store JavaScript")[Windows Phone](/en-us/develop/mobile/tutorials/authorize-users-in-scripts-wp8 "Windows Phone")[iOS](/en-us/develop/mobile/tutorials/authorize-users-in-scripts-ios "iOS")[Android](/en-us/develop/mobile/tutorials/authorize-users-in-scripts-android "Android")[HTML](/en-us/develop/mobile/tutorials/authorize-users-in-scripts-html "HTML")[iOS C\#](/en-us/develop/mobile/tutorials/authorize-users-in-scripts-xamarin-ios "Xamarin.iOS")[Android C\#](/en-us/develop/mobile/tutorials/authorize-users-in-scripts-xamarin-android "Xamarin.Android")

Cette rubrique explique comment utiliser les scripts serveur pour autoriser les utilisateurs authentifiés à accéder aux données dans Azure Mobile Services à partir d'une application Xamarin.iOS. Dans ce didacticiel, vous allez inscrire les scripts dans Mobile Services pour filtrer les requêtes sur la base de la valeur userId d'un utilisateur authentifié, en vous assurant que chaque utilisateur parvient à voir uniquement ses propres données.

Ce didacticiel est basé sur le démarrage rapide de Mobile Services et s'appuie sur le didacticiel précédent [Prise en main de l'authentification](/en-us/develop/mobile/tutorials/get-started-with-users-xamarin-ios). Avant de commencer, vous devez suivre le didacticiel [Prise en main de l'authentification](/en-us/develop/mobile/tutorials/get-started-with-users-xamarin-ios).

Inscription des scripts
-----------------------

Étant donné que l'application de démarrage rapide lit et insère les données, vous devez inscrire les scripts de ces opérations dans la table TodoItem.

1.  Connectez-vous au [portail de gestion Azure](https://manage.windowsazure.com/), cliquez sur **Mobile Services**, puis sur l'application.

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

    **Remarque**

    Le schéma dynamique doit être activé la première fois que ce script d'insertion s'exécute. Avec le schéma dynamique activé, Mobile Services ajoute automatiquement la colonne **userId** à la table **TodoItem** lors de la première exécution. Par défaut, le schéma dynamique est activé pour un nouveau service mobile. Il doit être désactivé avant que l'application soit publiée sur le Windows Store.

5.  Répétez les étapes 3 et 4 pour remplacer l'opération **Read** existante par la fonction suivante :

         function read(query, user, request) {
            query.where({ userId: user.userId });    
            request.execute();
         }

    Ce script filtre les objets TodoItem renvoyés de façon à ce que chaque utilisateur reçoive uniquement les éléments qu'il a inséré.

Test de l'application
---------------------

1.  Dans Xamarin Studio ou Visual Studio, ouvrez le projet que vous avez modifié en suivant le didacticiel [Prise en main de l'authentification](/en-us/develop/mobile/tutorials/get-started-with-users-xamarin-ios).

2.  Appuyez sur le bouton **Exécuter** pour générer le projet, démarrez l'application dans l'émulateur iPhone, puis connectez-vous avec le fournisseur d'identité de votre choix.

   Remarquez que cette fois, bien que certains éléments provenant des didacticiels précédents figurent déjà dans la table TodoItem, aucun élément n'est renvoyé. Cela se produit parce que les éléments précédents ont été insérés sans la colonne userId et comportent maintenant des valeurs null.

3.  Dans l'application, entrez du texte dans **Insert a TodoItem**, puis cliquez sur **Enregistrer**.

   ![][3]

   Cette action insère à la fois le texte et la valeur userId dans la table TodoItem du service mobile. Étant donné que le nouvel élément dispose de la valeur userId correcte, il est renvoyé par le service mobile et affiché dans la seconde colonne.

4.  De retour dans la table **todoitem** sur le [portail de gestion](https://manage.windowsazure.com/), cliquez sur **Parcourir** et vérifiez que chaque élément récemment ajouté présente une valeur userId associée.

5.  (Facultatif) Si vous disposez de comptes de connexion supplémentaires, vous pouvez vous assurer que les utilisateurs peuvent uniquement afficher leurs propres données en fermant l'application, puis en la réexécutant. Une fois que la boîte de dialogue des informations d'identification apparaît, entrez une autre connexion, puis vérifiez que les éléments entrés sous le compte précédent ne s'affichent pas.

Étapes suivantes
----------------

Cela met fin à la série de didacticiels établissant les principes de base de l'utilisation de l'authentification. Pour plus d'informations sur les rubriques Mobile Services suivantes :

-   [Prise en main des données](/en-us/develop/mobile/tutorials/get-started-with-data-xamarin-ios)
    En savoir plus sur le stockage et l'interrogation des données à l'aide de Mobile Services.

-   [Prise en main des notifications Push](/en-us/develop/mobile/tutorials/get-started-with-push-xamarin-ios)
    En savoir plus sur l'envoi d'une notification Push très basique sur votre application.

-   [Référence de script serveur Mobile Services](http://go.microsoft.com/fwlink/p/?LinkId=262293)
    En savoir plus sur l'enregistrement et l'utilisation des scripts serveur.


[0]: ./media/partner-xamarin-mobile-services-ios-authorize-users-in-scripts/mobile-services-selection.png
[1]: ./media/partner-xamarin-mobile-services-ios-authorize-users-in-scripts/mobile-portal-data-tables.png
[2]: ./media/partner-xamarin-mobile-services-ios-authorize-users-in-scripts/mobile-insert-script-users.png
[3]: ./media/partner-xamarin-mobile-services-ios-authorize-users-in-scripts/mobile-quickstart-startup-ios.png
