<properties linkid="develop-mobile-tutorials-validate-modify-and-augment-data-ios" urlDisplayName="Validate Data" pageTitle="Use server scripts to validate and modify data (iOS) | Mobile Dev Center" metaKeywords="" description="Learn how to validate and modify data sent using server scripts from your iOS app." metaCanonical="" services="" documentationCenter="Mobile" title="Validate and modify data in Mobile Services by using server scripts" authors="" solutions="" manager="" editor="" />

Validation et modification de données dans Mobile Services à l'aide de scripts serveur
======================================================================================

[Windows Store C\#](/en-us/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet "Windows Store C#")[Windows Store JavaScript](/en-us/develop/mobile/tutorials/validate-modify-and-augment-data-js "Windows Store JavaScript")[Windows Phone](/en-us/develop/mobile/tutorials/validate-modify-and-augment-data-wp8 "Windows Phone")[iOS](/en-us/develop/mobile/tutorials/validate-modify-and-augment-data-ios "iOS")[Android](/en-us/develop/mobile/tutorials/validate-modify-and-augment-data-android "Android")[HTML](/en-us/develop/mobile/tutorials/validate-modify-and-augment-data-html "HTML")[Xamarin.iOS](/en-us/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-ios "Xamarin.iOS")[Xamarin.Android](/en-us/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-android "Xamarin.Android")

Cette rubrique vous montre comment exploiter les scripts serveur dans Azure Mobile Services. Il est possible d'utiliser les scripts serveur inscrits dans un service mobile pour effectuer diverses opérations sur les données insérées et mises à jour, qu'il s'agisse de les valider ou de les modifier. Ce didacticiel vous apprend à définir et à inscrire les scripts serveur qui valident et modifient les données. Le comportement des scripts serveur ayant souvent un impact sur le client, vous allez également mettre à jour votre application iOS pour tirer profit de ces nouveaux comportements.

Ce didacticiel vous familiarise avec ces étapes de base :

1.  [Ajout de la validation de longueur de chaîne](#string-length-validation)
2.  [Mise à jour du client pour la prise en charge de la validation](#update-client-validation) 
3.  [Ajout d'un horodatage lors d'une insertion](#add-timestamp)
3.  [Mise à jour du client pour l'affichage de l'horodatage](#update-client-timestamp)

Ce didacticiel s'appuie sur la procédure et l'exemple d'application présentés dans le didacticiel précédent intitulé [Prise en main des données](/en-us/develop/mobile/tutorials/get-started-with-data-ios). Avant de commencer, vous devez suivre le didacticiel [Prise en main des données](/en-us/develop/mobile/tutorials/get-started-with-data-ios).

Ajout de la validation
----------------------

Il est toujours souhaitable de valider la longueur des données soumises par les utilisateurs. Vous devez d'abord inscrire un script qui valide la longueur des données de chaîne envoyées au service mobile et refuse les chaînes trop longues, en l'occurrence, celles qui font plus de 10 caractères.

1.  Connectez-vous au [portail de gestion Azure](https://manage.windowsazure.com/), cliquez sur **Mobile Services**, puis sur l'application.

      ![](./media/mobile-services-ios-validate-modify-data-server-scripts/mobile-services-selection.png)

2.  Cliquez sur l'onglet **Données**, puis sur la table **TodoItem**.

      ![](./media/mobile-services-ios-validate-modify-data-server-scripts/mobile-portal-data-tables.png)

3.  Cliquez sur **Script**, puis sélectionnez l'opération **Insert**.

      ![](./media/mobile-services-ios-validate-modify-data-server-scripts/mobile-insert-script-users.png)

4.  Remplacez le script existant par la fonction suivante, puis cliquez sur **Enregistrer**.

         function insert(item, user, request) {
             if (item.text.length > 10) {
                 request.respond(statusCodes.BAD_REQUEST, 'Text length must be 10 characters or less.');
             } else {
                 request.execute();
             }
         }

    Ce script vérifie la longueur de la propriété **text** et envoie une réponse indiquant une erreur lorsque la chaîne dépasse 10 caractères. Sinon, la méthode **execute** est appelée pour effectuer l'insertion.

    **Remarque**

    Sous l'onglet **Script**, vous pouvez supprimer un script inscrit en cliquant sur **Clear**, puis sur **Enregistrer**.

Mise à jour du client
---------------------

Maintenant que le service mobile valide les données et envoie des réponses d'erreur, vous devez mettre à jour l'application afin qu'elle traite les réponses d'erreur de la validation.

1.  Dans Xcode, ouvrez le projet que vous avez modifié avec le didacticiel [Prise en main des données](/en-us/develop/mobile/tutorials/get-started-with-data-ios).

2.  Appuyez sur le bouton **Exécuter** (Commande + R) pour générer le projet et démarrer l'application, puis tapez un texte de plus de 10 caractères dans la zone de texte et cliquez sur l'icône représentant un signe plus (**+**).

      Notez que l'application génère une exception non prise en charge suite à la réponse 400 (Requête incorrecte) renvoyée par le service mobile.   

3.  Dans le fichier QSTodoService.m, recherchez la ligne de code suivante dans la méthode **addItem** :

         [self logErrorIfNotNil:error]; 

      Après cette ligne de code, remplacez le reste du bloc completion par le code suivant :

         BOOL goodRequest = !((error) && (error.code == MSErrorMessageErrorCode));

         // Détectez une erreur de validation de texte du service.
         if (goodRequest) // Le service a répondu correctement
         {
             NSUInteger index = [items count];
             [(NSMutableArray *)items insertObject:result atIndex:index];
            
             // Informez l'appelant de la fin de l'opération
             completion(index);
         }
         else{
            
             // si une erreur due au service se produit,
             // l'enregistrer et afficher la chaîne renvoyée.
             if (error && error.code == MSErrorMessageErrorCode) {
                 NSLog(@"ERROR %@", error);
                 UIAlertView *av =
                 [[UIAlertView alloc]
                  initWithTitle:@"Request Failed"
                  message:error.localizedDescription
                  delegate:nil
                  cancelButtonTitle:@"OK"
                  otherButtonTitles:nil
                  ];
                 [av show];
             }
         }

      Ce code enregistre l'erreur dans la fenêtre de sortie et l'affiche pour l'utilisateur. 

4.  Régénérez et démarrez l'application.

      ![](./media/mobile-services-ios-validate-modify-data-server-scripts/mobile-quickstart-data-error-ios.png)

  Notez que l'erreur est traitée et que le message d'erreur est affiché pour l'utilisateur.

Étapes suivantes
----------------

Maintenant que vous avez terminé ce didacticiel, pensez à suivre le dernier didacticiel dans la série des données : [Affinage des requêtes à la pagination](/en-us/develop/mobile/tutorials/add-paging-to-data-ios).

Les scripts serveur sont également utilisés dans le cadre du processus d'autorisation des utilisateurs et pour l'envoi de notifications Push. Pour plus d'informations, reportez-vous aux didacticiels suivants :

-   [Autorisation des utilisateurs avec des scripts](/en-us/develop/mobile/tutorials/authorize-users-in-scripts-ios)
    <br/>En savoir plus sur le filtrage des données en fonction de l'ID d'un utilisateur authentifié.

-   [Prise en main des notifications Push](/en-us/develop/mobile/tutorials/get-started-with-push-ios)
    <br/>En savoir plus sur l'envoi d'une notification Push très basique sur votre application.

-   [Référence de script serveur Mobile Services](http://go.microsoft.com/fwlink/?LinkId=262293)
    <br/>En savoir plus sur l'enregistrement et l'utilisation des scripts serveur.


