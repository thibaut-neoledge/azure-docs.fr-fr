<properties linkid="develop-mobile-tutorials-validate-modify-and-augment-data-html" urlDisplayName="Validate Data - HTML5" pageTitle="User server scripts to validate and modify data (HTML 5) | Mobile Dev Center" metaKeywords="" description="Learn how to validate and modify data sent using server scripts from your HTML app." metaCanonical="" services="" documentationCenter="Mobile" title="Validate and modify data in Mobile Services by using server scripts" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-html" ms.devlang="javascript" ms.topic="article" ms.date="01/01/1900" ms.author="glenga"></tags>

# Validation et modification de données dans Mobile Services à l'aide de scripts serveur

<div class="dev-center-tutorial-selector sublanding"> 
    <a href="/fr-fr/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet" title="Windows Store C#">Windows Store C#</a><a href="/fr-fr/develop/mobile/tutorials/validate-modify-and-augment-data-js" title="Windows Store JavaScript">Windows Store JavaScript</a><a href="/fr-fr/develop/mobile/tutorials/validate-modify-and-augment-data-wp8" title="Windows Phone">Windows Phone</a><a href="/fr-fr/develop/mobile/tutorials/validate-modify-and-augment-data-ios" title="iOS">iOS</a><a href="/fr-fr/develop/mobile/tutorials/validate-modify-and-augment-data-android" title="Android">Android</a><a href="/fr-fr/develop/mobile/tutorials/validate-modify-and-augment-data-html" title="HTML" class="current">HTML</a><a href="/fr-fr/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-ios" title="Xamarin.iOS">Xamarin.iOS</a><a href="/fr-fr/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-android" title="Xamarin.Android" class="current">Xamarin.Android</a>
</div>

Cette rubrique vous montre comment exploiter les scripts serveur dans Azure Mobile Services. Il est possible d'utiliser les scripts serveur inscrits dans un service mobile pour effectuer diverses opérations sur les données insérées et mises à jour, qu'il s'agisse de les valider ou de les modifier. Ce didacticiel vous apprend à définir et à inscrire les scripts serveur qui valident et modifient les données. Le comportement des scripts serveur ayant souvent un impact sur le client, vous allez également mettre à jour votre application HTML pour tirer profit de ces nouveaux comportements.

Ce didacticiel vous familiarise avec ces étapes de base :

1.  [Ajout de la validation de longueur de chaîne][]
2.  [Mise à jour du client pour la prise en charge de la validation][]
3.  [Ajout d'un horodatage lors d'une insertion][]
4.  [Mise à jour du client pour l'affichage de l'horodatage][]

Ce didacticiel s'appuie sur la procédure et l'exemple d'application présentés dans le didacticiel précédent intitulé [Prise en main des données][]. Avant de commencer, vous devez suivre le didacticiel [Prise en main des données][].

## <a name="string-length-validation"></a>Ajout de la validation

Il est toujours souhaitable de valider la longueur des données soumises par les utilisateurs. Vous devez d'abord inscrire un script qui valide la longueur des données de chaîne envoyées au service mobile et refuse les chaînes trop longues, en l'occurrence, celles qui font plus de 10 caractères.

1.  Connectez-vous au [portail de gestion Azure][], cliquez sur **Mobile Services**, puis sur l'application.

    ![][]

2.  Cliquez sur l'onglet **Données**, puis sur la table **TodoItem**.

    ![][1]

3.  Cliquez sur **Script**, puis sélectionnez l'opération **Insert**.

    ![][2]

4.  Remplacez le script existant par la fonction suivante, puis cliquez sur **Enregistrer**.

        function insert(item, user, request) {
            if (item.text.length > 10) {
                request.respond(statusCodes.BAD_REQUEST, {
                    error: "Text cannot exceed 10 characters"
                });
            } else {
                request.execute();
            }
        }

    Ce script vérifie la longueur de la propriété **TodoItem.text** et envoie une réponse indiquant une erreur lorsque la chaîne dépasse 10 caractères. Sinon, la fonction **execute** est appelée pour effectuer l'insertion.

    <div class="dev-callout"> 
<b>Remarque</b> 
<p>Sous l'onglet <strong>Script</strong>, vous pouvez supprimer un script inscrit en cliquant sur <strong>Effacer</strong>, puis sur <strong>Enregistrer</strong>.</p></div>

## <a name="update-client-validation"></a>Mise à jour du client

Maintenant que le service mobile valide les données et envoie des réponses d'erreur, vous devez mettre à jour l'application afin qu'elle traite les réponses d'erreur de la validation.

1.  Exécutez l'un des fichiers de commandes suivants à partir du sous-dossier **server** du projet que vous avez modifié dans le didacticiel [Prise en main des données][].

    -   **launch-windows** (pour les ordinateurs Windows)
    -   **launch-mac.command** (pour les ordinateurs Mac OS X)
    -   **launch-linux.sh** (pour les ordinateurs Linux)

    <div class="dev-callout"><b>Remarque</b>
    <p>Sur un ordinateur Windows, appuyez sur la touche &laquo;&nbsp;R&nbsp;&raquo; lorsque PowerShell vous demande de confirmer l'ex&eacute;cution du script. Vous pouvez recevoir un avertissement de votre navigateur Web vous recommandant de ne pas ex&eacute;cuter le script, car il a &eacute;t&eacute; t&eacute;l&eacute;charg&eacute; depuis Internet. Lorsque cela se produit, vous devez demander au navigateur de continuer &agrave; charger le script.</p>
</div>

    Un serveur Web démarre sur votre ordinateur local pour héberger l'application.

2.  Ouvrez le fichier app.js, puis remplacez le gestionnaire d'événements **$('\#add-item').submit()** par le code suivant :

        $('#add-item').submit(function(evt) {
            var textbox = $('#new-item-text'),
                itemText = textbox.val();
            if (itemText !== '') {
                todoItemTable.insert({ text: itemText, complete: false })
                    .then(refreshTodoItems, function(error){
                    alert(JSON.parse(error.request.responseText).error);
                });
            }
            textbox.val('').focus();
            evt.preventDefault();
        });

3.  Dans un navigateur Web, accédez à <http://localhost:8000/>, puis tapez du texte dans **Ajouter une nouvelle tâche** et cliquez sur **Ajouter**.

    Notez que l'opération échoue et que la gestion des erreurs affiche la réponse d'erreur dans une boîte de dialogue.

## <a name="add-timestamp"></a>Ajout d'un horodatage

Les tâches précédentes ont permis de valider une insertion, qui est acceptée ou rejetée. Maintenant, vous allez mettre à jour les données insérées en utilisant un script serveur qui ajoute une propriété d'horodatage à l'objet avant son insertion.

<div class="dev-callout"><b>Remarque</b>
<p>La propri&eacute;t&eacute; d'horodatage <b>createdAt</b> illustr&eacute;e ici est d&eacute;sormais redondante. Mobile Services cr&eacute;e automatiquement une propri&eacute;t&eacute; syst&egrave;me <b>__createdAt</b> pour chaque table.</p>
</div>

1.  Sous l'onglet **Scripts** du [portail de gestion][portail de gestion Azure], remplacez le script **Insert** actuel par la fonction suivante, puis cliquez sur **Enregistrer**.

        function insert(item, user, request) {
            if (item.text.length > 10) {
                request.respond(statusCodes.BAD_REQUEST, {
                    error: 'Text length must be under 10'
                });
            } else {
                item.createdAt = new Date();
                request.execute();
            }
        }

    Cette fonction enrichit le script d'insertion précédent en ajoutant une nouvelle propriété d'horodatage **createdAt** à l'objet avant son insertion par l'appel à **request**.**execute**.

    <div class="dev-callout"><b>Remarque</b>
<p>Le sch&eacute;ma dynamique doit &ecirc;tre activ&eacute; la premi&egrave;re fois que ce script d'insertion s'ex&eacute;cute. Avec le sch&eacute;ma dynamique activ&eacute;, Mobile Services ajoute automatiquement la colonne <strong>createdAt</strong> &agrave; la table <strong>TodoItem</strong>. Par d&eacute;faut, le sch&eacute;ma dynamique est activ&eacute; pour un nouveau service mobile. Il doit &ecirc;tre d&eacute;sactiv&eacute; avant que l'application ne soit publi&eacute;e.</p>
</div>

2.  Dans le navigateur Web, rechargez la page, puis tapez un texte (de moins de 10 caractères) dans **Ajouter une nouvelle tâche** et cliquez sur **Ajouter**.

    Notez que le nouvel horodatage n'apparaît pas dans l'interface utilisateur de l'application.

3.  De retour dans le portail de gestion, cliquez sur l'onglet **Parcourir** dans la table **todoitem**.

    Notez qu'une colonne **createdAt** apparaît désormais à l'écran et qu'une valeur d'horodatage est associée au nouvel élément inséré.

L'application doit ensuite être mise à jour pour afficher cette nouvelle colonne.

## <a name="update-client-timestamp"></a>Nouvelle mise à jour du client

Le client Mobile Services ignore les données d'une réponse qu'elle ne peut pas sérialiser en propriétés de type défini. L'étape finale consiste à mettre à jour le client de manière à afficher ces nouvelles données.

1.  Dans votre éditeur, ouvrez le fichier app.js, puis remplacez la fonction **refreshTodoItems** par le code suivant :

        function refreshTodoItems() {
            var query = todoItemTable.where(function () {
                return (this.complete === false && this.createdAt !== null);
            });

            query.read().then(function(todoItems) {
                var listItems = $.map(todoItems, function(item) {
                    return $('<li>')
                        .attr('data-todoitem-id', item.id)
                        .append($('<button class="item-delete">Delete</button>'))
                        .append($('<input type="checkbox" class="item-complete">')
                            .prop('checked', item.complete))
                        .append($('<div>').append($('<input class="item-text">').val(item.text))
                        .append($('<span class="timestamp">' 
                            + (item.createdAt && item.createdAt.toDateString() + ' '
                            + item.createdAt.toLocaleTimeString() || '') 
                            + '</span>')));

                });

                $('#todo-items').empty().append(listItems).toggle(listItems.length > 0);
                $('#summary').html('<strong>' + todoItems.length + '</strong> item(s)');
            });
        }

    Ce code affiche la partie date de la nouvelle propriété **createdAt**.

2.  Dans votre éditeur, ouvrez le fichier style.css et remplacez les styles sur la classe `item-text` par le code suivant :

        .item-text { width: 70%; height: 26px; line-height: 24px; 
            border: 1px solid transparent; background-color: transparent; }
        .timestamp { width: 30%; height: 40px; font-size: .75em; }

    Ce code redimensionne la zone de texte et définit le style du nouveau texte d'horodatage.

3.  Rechargez la page.

    Notez que l'horodatage ne s'affiche que pour les éléments insérés une fois que le script d'insertion a été mis à jour.

4.  Dans la fonction **refreshTodoItems**, remplacez à nouveau la ligne de code qui définit la requête par le code suivant :

         var query = todoItemTable.where(function () {
                return (this.complete === false && this.createdAt !== null);
            });

    Cette fonction met à jour la requête pour éliminer également les éléments non associés à une valeur d'horodatage.

5.  Rechargez la page.

    Notez que tous les éléments créés sans valeur d'horodatage disparaissent de l'interface utilisateur.

Vous avez maintenant terminé ce didacticiel.

## <a name="next-steps"> </a>Étapes suivantes

Maintenant que vous avez terminé ce didacticiel, pensez à suivre le dernier didacticiel dans la série des données : [Affinage des requêtes au moyen de la pagination][].

Pour plus d'informations, consultez les pages [Utilisation des scripts serveur][] et [Guide de fonctionnement Mobile Services HTML/JavaScript][]

  [Windows Store C#]: /fr-fr/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet "Windows Store C#"
  [Windows Store JavaScript]: /fr-fr/develop/mobile/tutorials/validate-modify-and-augment-data-js "Windows Store JavaScript"
  [Windows Phone]: /fr-fr/develop/mobile/tutorials/validate-modify-and-augment-data-wp8 "Windows Phone"
  [iOS]: /fr-fr/develop/mobile/tutorials/validate-modify-and-augment-data-ios "iOS"
  [Android]: /fr-fr/develop/mobile/tutorials/validate-modify-and-augment-data-android "Android"
  [HTML]: /fr-fr/develop/mobile/tutorials/validate-modify-and-augment-data-html "HTML"
  [Xamarin.iOS]: /fr-fr/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-ios "Xamarin.iOS"
  [Xamarin.Android]: /fr-fr/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-android "Xamarin.Android"
  [Ajout de la validation de longueur de chaîne]: #string-length-validation
  [Mise à jour du client pour la prise en charge de la validation]: #update-client-validation
  [Ajout d'un horodatage lors d'une insertion]: #add-timestamp
  [Mise à jour du client pour l'affichage de l'horodatage]: #update-client-timestamp
  [Prise en main des données]: /fr-fr/develop/mobile/tutorials/get-started-with-data-html
  [portail de gestion Azure]: https://manage.windowsazure.com/
  []: ./media/mobile-services-html-validate-modify-data-server-scripts/mobile-services-selection.png
  [1]: ./media/mobile-services-html-validate-modify-data-server-scripts/mobile-portal-data-tables.png
  [2]: ./media/mobile-services-html-validate-modify-data-server-scripts/mobile-insert-script-users.png
  [Affinage des requêtes au moyen de la pagination]: /fr-fr/develop/mobile/tutorials/add-paging-to-data-html
  [Utilisation des scripts serveur]: /fr-fr/develop/mobile/how-to-guides/work-with-server-scripts
  [Guide de fonctionnement Mobile Services HTML/JavaScript]: /fr-fr/develop/mobile/how-to-guides/work-with-html-js-client
