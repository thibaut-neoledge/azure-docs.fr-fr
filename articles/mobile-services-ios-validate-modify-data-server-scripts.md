<properties urlDisplayName="Validate Data" pageTitle="Utilisation de scripts serveur pour valider et modifier des données (iOS) | Centre de développement mobile" metaKeywords="" description="Découvrez comment valider et modifier les données envoyées à l'aide de scripts serveurs à partir de votre application iOS." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Validate and modify data in Mobile Services by using server scripts" authors="krisragh" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="10/10/2014" ms.author="krisragh" />

# Validation et modification de données dans Mobile Services à l'aide de scripts serveur

[WACOM.INCLUDE [mobile-services-selector-validate-modify-data](../includes/mobile-services-selector-validate-modify-data.md)]

Cette rubrique vous montre comment exploiter les scripts serveur dans Azure Mobile Services. Il est possible d'utiliser les scripts serveur inscrits dans un service mobile pour effectuer diverses opérations sur les données insérées et mises à jour, qu'il s'agisse de les valider ou de les modifier. Ce didacticiel vous apprend à définir et à inscrire les scripts serveur qui valident et modifient les données. Le comportement des scripts serveur ayant souvent un impact sur le client, vous allez également mettre à jour votre application iOS pour tirer profit de ces nouveaux comportements.

Ce didacticiel vous familiarise avec ces étapes de base :

1. [Ajout de la validation de longueur de chaîne]
2. [Mise à jour du client pour la prise en charge de la validation]


Ce didacticiel s'appuie sur la procédure et l'exemple d'application présentés dans le didacticiel précédent intitulé [Prise en main des données]. Avant de commencer, vous devez suivre le didacticiel [Prise en main des données].  

## <a name="string-length-validation"></a>Ajout de la validation

Il est toujours souhaitable de valider la longueur des données soumises par les utilisateurs. Vous devez d'abord inscrire un script qui valide la longueur des données de chaîne envoyées au service mobile et refuse les chaînes trop longues, en l'occurrence, celles qui font plus de 10 caractères.

1. Connectez-vous au [portail de gestion Azure], cliquez sur **Mobile Services**, puis sur votre application.

   	![][0]

2. Cliquez sur l'onglet **Données**, puis sur la table **TodoItem**.

   	![][1]

3. Cliquez sur **Script**, puis sélectionnez l'opération **Insérer**.

   	![][2]

4. Remplacez le script existant par la fonction suivante, puis cliquez sur **Enregistrer**.

        function insert(item, user, request) {
            if (item.text.length > 10) {
                request.respond(statusCodes.BAD_REQUEST, 'Text length must be 10 characters or less.');
            } else {
                request.execute();
            }
        }

    Ce script vérifie la longueur de la propriété **text** et envoie une réponse indiquant une erreur lorsque la chaîne dépasse 10 caractères. Sinon, la méthode **execute** est appelée pour effectuer l'insertion.

    <div class="dev-callout">
	<b>Remarque</b>
	<p>Vous pouvez supprimer un script inscrit dans l'onglet <strong>Script</strong> en cliquant sur <strong>Effacer</strong>, puis sur <strong>Enregistrer</strong>.</p></div>

## <a name="update-client-validation"></a>Mise à jour du client

Maintenant que le service mobile valide les données et envoie des réponses d'erreur, vous devez mettre à jour l'application afin qu'elle traite les réponses d'erreur de la validation.

1. Dans Xcode, ouvrez le projet que vous avez modifié avec le didacticiel [Prise en main des données].

2. Appuyez sur le bouton **Exécuter** (Commande+R) pour générer le projet et démarrer l'application, puis tapez un texte de plus de 10 caractères dans la zone de texte et cliquez sur l'icône Plus (**+**).

   	Notez que l'application génère une exception non prise en charge suite à la réponse 400 (Requête incorrecte) renvoyée par le service mobile.

3. Dans le fichier QSTodoService.m, recherchez la ligne de code suivante dans la méthode **addItem** :

        [self logErrorIfNotNil:error];

   	Après cette ligne de code, remplacez le reste du bloc completion par le code suivant :

        BOOL goodRequest = !((error) && (error.code == MSErrorMessageErrorCode));

        // detect text validation error from service.
        if (goodRequest) // The service responded appropriately
        {
            NSUInteger index = [items count];
            [(NSMutableArray *)items insertObject:result atIndex:index];

            // Let the caller know that we finished
            completion(index);
        }
        else{

            // if there's an error that came from the service
            // log it, and popup up the returned string.
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

4. Régénérez et démarrez l'application.

   	![][4]

  	Notez que l'erreur est traitée et que le message d'erreur est affiché pour l'utilisateur.

## <a name="add-timestamp"></a>Ajout d'un horodatage

Les tâches précédentes ont permis de valider une insertion, qui est acceptée ou rejetée. Maintenant, vous allez mettre à jour les données insérées en utilisant un script serveur qui ajoute une propriété d'horodatage à l'objet avant son insertion.

1. Sous l'onglet **Scripts** du [portail de gestion], remplacez le script **Insert** actuel par la fonction suivante, puis cliquez sur **Enregistrer**.

        function insert(item, user, request) {
            if (item.text.length > 10) {
                request.respond(statusCodes.BAD_REQUEST, 'Text length must be under 10');
            } else {
                item.createdAt = new Date();
                request.execute();
            }
        }

    Cette fonction enrichit le script d'insertion précédent en ajoutant une nouvelle propriété d'horodatage **createdAt** à l'objet avant son insertion par l'appel à **request**.**execute**.

    <div class="dev-callout"><b>Remarque</b>
	<p>Le schéma dynamique doit être activé la première fois que ce script d'insertion s'exécute. Avec le schéma dynamique activé, Mobile Services ajoute automatiquement la colonne <strong>createdAt</strong> à la table <strong>TodoItem</strong> lors de la première exécution. Par défaut, le schéma dynamique est activé pour un nouveau service mobile. Il doit être désactivé pour que l'application soit publiée.</p>
    </div>

2. Dans Visual Studio, appuyez sur la touche **F5** pour exécuter l'application, puis tapez une chaîne de texte (inférieure à 10 caractères) dans **Insert a TodoItem** et cliquez sur **Enregistrer**.

   	Notez que le nouvel horodatage n'apparaît pas dans l'interface utilisateur de l'application.

3. De retour dans le portail de gestion, cliquez sur l'onglet **Parcourir** dans la table **todoitem**.

   	Notez qu'une colonne **createdAt** apparaît désormais à l'écran et qu'une valeur d'horodatage est associée au nouvel élément inséré.

L'application iOS doit ensuite être mise à jour pour afficher cette nouvelle colonne.

## <a name="update-client-timestamp"></a>Nouvelle mise à jour du client

Le client Mobile Services ignore les données d'une réponse qu'elle ne peut pas sérialiser en propriétés de type défini. L'étape finale consiste à mettre à jour le client de manière à afficher ces nouvelles données.

1. Dans Visual Studio, ouvrez le fichier MainPage.xaml.cs, puis remplacez la classe **TodoItem** existante par la définition suivante :

	    public class TodoItem
	    {
	        public int Id { get; set; }

            [DataMember(Name="text")]
	        public string Text { get; set; }

            [DataMember(Name="complete")]
	        public bool Complete { get; set; }

            [DataMember(Name="createdAt")]
	        public DateTime? CreatedAt { get; set; }
	    }

    Cette nouvelle définition de classe inclut la nouvelle propriété d'horodatage en tant que type DateTime nullable.

    <div class="dev-callout"><b>Remarque</b>
	<p>L'attribut <strong>DataMemberAttribute</strong> indique au client de mapper la nouvelle propriété <strong>CreatedAt</strong> dans l'application à la colonne <strong>createdAt</strong> définie dans la table TodoItem (dont la casse est différente). En utilisant cet attribut, votre application peut avoir des noms de propriétés pour les objets qui diffèrent des noms de colonnes dans la base de données SQL. Sans cet attribut, une erreur se produirait en raison de la différence de casse.</p>
    </div>

2. Ajoutez l'élément XAML suivant juste en dessous de l'élément **CheckBoxComplete** dans le fichier MainPage.xaml :

        <TextBlock Name="WhenCreated" Text="{Binding CreatedAt}" VerticalAlignment="Center"/>

   	La nouvelle propriété **CreatedAt** s'affiche dans une zone de texte.

3. Appuyez sur la touche **F5** pour exécuter l'application.

   Notez que l'horodatage ne s'affiche que pour les éléments insérés une fois que le script d'insertion a été mis à jour.

4. Remplacez la méthode **RefreshTodoItems** existante par le code suivant :

        private void RefreshTodoItems()
        {
            // This query filters out completed TodoItems and
            // items without a timestamp.
            items = todoTable
               .Where(todoItem => todoItem.Complete == false
                   && todoItem.CreatedAt != null)
               .ToCollectionView();

            ListItems.ItemsSource = items;
        }

   	Cette méthode met à jour la requête pour éliminer également les éléments non associés à une valeur d'horodatage.

5. Appuyez sur la touche **F5** pour exécuter l'application.

   	Notez que tous les éléments créés sans valeur d'horodatage disparaissent de l'interface utilisateur.

Vous avez maintenant terminé ce didacticiel.

## <a name="next-steps"> </a>Étapes suivantes

Maintenant que vous avez terminé ce didacticiel, pensez à suivre le dernier didacticiel dans la série des données : [Affinage des requêtes au moyen de la pagination].

Les scripts serveur sont également utilisés dans le cadre du processus d'autorisation des utilisateurs et pour l'envoi de notifications Push. Pour plus d'informations, reportez-vous aux didacticiels suivants :

* [Autoriser les utilisateurs avec des scripts]
  <br/>En savoir plus sur le filtrage des données en fonction de l'ID d'un utilisateur authentifié.

* [Prise en main des notifications Push]
  <br/>En savoir plus sur l'envoi d'une notification Push très basique sur votre application.

* [Référence de script serveur Mobile Services]
  <br/>En savoir plus sur l'inscription et l'utilisation de scripts serveur.

<!-- Anchors. -->
[Ajout de la validation de longueur de chaîne]: #string-length-validation
[Mise à jour du client pour la prise en charge de la validation]: #update-client-validation
[Ajout d'un horodatage lors d'une insertion]: #add-timestamp
[Mise à jour du client pour l'affichage de l'horodatage]: #update-client-timestamp
[Étapes suivantes]: #next-steps

<!-- Images. -->
[0]: ./media/mobile-services-ios-validate-modify-data-server-scripts/mobile-services-selection.png
[1]: ./media/mobile-services-ios-validate-modify-data-server-scripts/mobile-portal-data-tables.png
[2]: ./media/mobile-services-ios-validate-modify-data-server-scripts/mobile-insert-script-users.png

[4]: ./media/mobile-services-ios-validate-modify-data-server-scripts/mobile-quickstart-data-error-ios.png

<!-- URLs. -->
[Référence de script serveur Mobile Services]: http://go.microsoft.com/fwlink/?LinkId=262293
[Prise en main de Mobile Services]: /en-us/develop/mobile/tutorials/get-started-ios
[Autoriser les utilisateurs avec des scripts]: /en-us/develop/mobile/tutorials/authorize-users-in-scripts-ios
[Affinage des requêtes à la pagination.]: /en-us/develop/mobile/tutorials/add-paging-to-data-ios
[Prise en main des données]: /en-us/develop/mobile/tutorials/get-started-with-data-ios
[Prise en main de l'authentification]: /en-us/develop/mobile/tutorials/get-started-with-users-ios
[Prise en main des notifications Push]: /en-us/develop/mobile/tutorials/get-started-with-push-ios

[Portail de gestion]: https://manage.windowsazure.com/
[Portail de gestion Azure]: https://manage.windowsazure.com/
