<properties linkid="develop-mobile-tutorials-validate-modify-and-augment-data-js" urlDisplayName="Validate Data" pageTitle="Use server scripts to validate and modify data (JavaScript) | Mobile Dev Center" metaKeywords="" description="Learn how to validate and modify data sent using server scripts from your Windows Store JavaScript app." metaCanonical="http://www.windowsazure.com/fr-fr/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet/" services="" documentationCenter="Mobile" title="Validate and modify data in Mobile Services by using server scripts" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="javascript" ms.topic="article" ms.date="01/01/1900" ms.author="glenga"></tags>

# Validation et modification de données dans Mobile Services à l'aide de scripts serveur

<div class="dev-center-tutorial-selector sublanding"> 
<a href="/fr-fr/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet" title="Windows Store C#">Windows Store C#</a>
<a href="/fr-fr/develop/mobile/tutorials/validate-modify-and-augment-data-js" title="Windows Store JavaScript" class="current">Windows Store JavaScript</a>
<a href="/fr-fr/develop/mobile/tutorials/validate-modify-and-augment-data-wp8" title="Windows Phone">Windows Phone</a><a href="/fr-fr/develop/mobile/tutorials/validate-modify-and-augment-data-ios" title="iOS">iOS</a>
<a href="/fr-fr/develop/mobile/tutorials/validate-modify-and-augment-data-android" title="Android">Android</a>
<a href="/fr-fr/develop/mobile/tutorials/validate-modify-and-augment-data-html" title="HTML">HTML</a>
<a href="/fr-fr/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-ios" title="Xamarin.iOS">Xamarin.iOS</a>
<a href="/fr-fr/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-android" title="Xamarin.Android">Xamarin.Android</a>
</div>
<div class="dev-center-tutorial-subselector">
	<a href="/fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-validate-modify-data/" title=".NET backend">.NET backend</a> | 
	<a href="/fr-fr/documentation/articles/mobile-services-windows-store-javascript-validate-modify-data-server-scripts/"  title="JavaScript backend" class="current">JavaScript backend</a>
</div>

Cette rubrique vous montre comment exploiter les scripts serveur dans Azure Mobile Services. Il est possible d'utiliser les scripts serveur inscrits dans un service mobile pour effectuer diverses opérations sur les données insérées et mises à jour, qu'il s'agisse de les valider ou de les modifier. Ce didacticiel vous apprend à définir et à inscrire les scripts serveur qui valident et modifient les données. Le comportement des scripts serveur ayant souvent un impact sur le client, vous allez également mettre à jour votre application Windows Store pour tirer profit de ces nouveaux comportements.

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
                request.respond(statusCodes.BAD_REQUEST, 'Text length must be under 10');
            } else {
                request.execute();
            }
        }

    Ce script vérifie la longueur de la propriété **TodoItem.text** et envoie une réponse indiquant une erreur lorsque la chaîne dépasse 10 caractères. Sinon, la méthode **execute** est appelée pour effectuer l'insertion.

    <div class="dev-callout">

    <b>Remarque</b><br /><br />
    Sous l'onglet <b>Script</b>, vous pouvez supprimer un script inscrit en cliquant sur <b>Effacer</b>, puis sur <b>Enregistrer</b>.

    </div>

## <a name="update-client-validation"></a>Mise à jour du client

Maintenant que le service mobile valide les données et envoie des réponses d'erreur, vous devez mettre à jour l'application afin qu'elle traite les réponses d'erreur de la validation.

1.  Dans Visual Studio 2012 Express pour Windows 8, ouvrez le projet que vous avez modifié en suivant le didacticiel [Prise en main des données][].

2.  Appuyez sur la touche **F5** pour exécuter l'application, puis tapez une chaîne de texte dépassant 10 caractères dans **Insert a TodoItem** et cliquez sur **Enregistrer**.

    Notez que l'application génère une exception non prise en charge suite à la réponse 400 (Requête incorrecte) renvoyée par le service mobile.

3.  Ouvrez le fichier default.js, puis remplacez la méthode **InsertTodoItem** existante par le code suivant :

        var insertTodoItem = function (todoItem) {
            // Inserts a new row into the database. When the operation completes
            // and Mobile Services has assigned an id, the item is added to the binding list.
            todoTable.insert(todoItem).done(function (item) {
                todoItems.push(item);
            }, function (error) {
                // Create the error message dialog and set its content to the error
                // message contained in the response.
                var msg = new Windows.UI.Popups.MessageDialog(
                    error.request.responseText);
                msg.showAsync();
            });
        };

    Cette version de la méthode inclut la gestion des erreurs qui affiche une réponse d'erreur dans une boîte de dialogue.

## <a name="add-timestamp"></a>Ajout d'un horodatage

Les tâches précédentes ont permis de valider une insertion, qui est acceptée ou rejetée. Maintenant, vous allez mettre à jour les données insérées en utilisant un script serveur qui ajoute une propriété d'horodatage à l'objet avant son insertion.

<div class="dev-callout">
<br />
<b>Remarque</b><br /><br />
La propriété d'horodatage <b>createdAt</b> illustrée ici est désormais redondante. Mobile Services crée automatiquement une propriété système <b>__createdAt</b> pour chaque table.

</div>

1.  Sous l'onglet **Scripts** du [portail de gestion][portail de gestion Azure], remplacez le script **Insert** actuel par la fonction suivante, puis cliquez sur **Enregistrer**.

        function insert(item, user, request) {
            if (item.text.length > 10) {
                request.respond(statusCodes.BAD_REQUEST, 'Text length must be under 10');
            } else {
                item.createdAt = new Date();
                request.execute();
            }
        }

    Cette fonction enrichit le script d'insertion précédent en ajoutant une nouvelle propriété d'horodatage **createdAt** à l'objet avant son insertion par l'appel à **request**.**execute**.

    <div class="dev-callout">
	
    <b>Remarque</b><br /><br />
    Le schéma dynamique doit être activé la première fois que ce script d'insertion s'exécute. Avec le schéma dynamique activé, Mobile Services ajoute automatiquement la colonne <b>createdAt</b> à la table <b>TodoItem</b>. Par défaut, le schéma dynamique est activé pour un nouveau service mobile. Il doit être désactivé avant que l'application soit publiée sur le Windows Store.<br /><br />
    </div>

2.  Dans Visual Studio, appuyez sur la touche **F5** pour exécuter l'application, puis tapez une chaîne de texte (inférieure à 10 caractères) dans **Insert a TodoItem** et cliquez sur **Enregistrer**.

    Notez que le nouvel horodatage n'apparaît pas dans l'interface utilisateur de l'application.

3.  De retour dans le portail de gestion, cliquez sur l'onglet **Parcourir** dans la table **todoitem**.

    Notez qu'une colonne **createdAt** apparaît désormais à l'écran et qu'une valeur d'horodatage est associée au nouvel élément inséré.

L'application Windows Store doit ensuite être mise à jour pour afficher cette nouvelle colonne.

## <a name="update-client-timestamp"></a>Nouvelle mise à jour du client

Le client Mobile Services ignore les données d'une réponse qu'elle ne peut pas sérialiser en propriétés de type défini. L'étape finale consiste à mettre à jour le client de manière à afficher ces nouvelles données.

1.  Dans Visual Studio, ouvrez le fichier default.html, puis ajoutez l'élément HTML suivant dans la grille TemplateItem :

        <div style="-ms-grid-column: 4; -ms-grid-row-align: center; margin-left: 5px" 
            data-win-bind="innerText: createdAt"></div>  

    La nouvelle propriété **createdAt** s'affiche.

2.  Appuyez sur **F5** pour lancer l'application.

    Notez que l'horodatage ne s'affiche que pour les éléments insérés une fois que le script d'insertion a été mis à jour.

3.  Dans le fichier default.js, remplacez la méthode **RefreshTodoItems** existante par le code suivant :

        var refreshTodoItems = function () {
            // More advanced query that filters out completed items. 
            todoTable.where(function () {
                return (this.complete === false && this.createdAt !== null);
            })
            .read()
            .done(function (results) {
                todoItems = new WinJS.Binding.List(results);
                listItems.winControl.itemDataSource = todoItems.dataSource;
            });
        };

    Cette méthode met à jour la requête pour éliminer également les éléments non associés à une valeur d'horodatage.

4.  Appuyez sur **F5** pour lancer l'application.

    Notez que tous les éléments créés sans valeur d'horodatage disparaissent de l'interface utilisateur.

Vous avez maintenant terminé ce didacticiel.

## <a name="next-steps"> </a>Étapes suivantes

Maintenant que vous avez terminé ce didacticiel, pensez à suivre le dernier didacticiel dans la série des données : [Affinage des requêtes au moyen de la pagination][].

Les scripts serveur sont également utilisés dans le cadre du processus d'autorisation des utilisateurs et pour l'envoi de notifications Push. Pour plus d'informations, reportez-vous aux didacticiels suivants :

-   [Autorisation des utilisateurs avec des scripts][]

    En savoir plus sur le filtrage des données en fonction de l'ID d'un utilisateur authentifié.

-   [Prise en main des notifications Push][]

    En savoir plus sur l'envoi d'une notification Push très basique sur votre application.

-   [Référence de script serveur Mobile Services][]

    En savoir plus sur l'enregistrement et l'utilisation des scripts serveur.

  [Windows Store C#]: /fr-fr/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet "Windows Store C#"
  [Windows Store JavaScript]: /fr-fr/develop/mobile/tutorials/validate-modify-and-augment-data-js "Windows Store JavaScript"
  [Windows Phone]: /fr-fr/develop/mobile/tutorials/validate-modify-and-augment-data-wp8 "Windows Phone"
  [iOS]: /fr-fr/develop/mobile/tutorials/validate-modify-and-augment-data-ios "iOS"
  [Android]: /fr-fr/develop/mobile/tutorials/validate-modify-and-augment-data-android "Android"
  [HTML]: /fr-fr/develop/mobile/tutorials/validate-modify-and-augment-data-html "HTML"
  [Xamarin.iOS]: /fr-fr/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-ios "Xamarin.iOS"
  [Xamarin.Android]: /fr-fr/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-android "Xamarin.Android"
  [Serveur principal .NET]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-validate-modify-data/ ".NET backend"
  [JavaScript backend]: /fr-fr/documentation/articles/mobile-services-windows-store-javascript-validate-modify-data-server-scripts/ "JavaScript backend"
  [Ajout de la validation de longueur de chaîne]: #string-length-validation
  [Mise à jour du client pour la prise en charge de la validation]: #update-client-validation
  [Ajout d'un horodatage lors d'une insertion]: #add-timestamp
  [Mise à jour du client pour l'affichage de l'horodatage]: #update-client-timestamp
  [Prise en main des données]: /fr-fr/develop/mobile/tutorials/get-started-with-data-js
  [portail de gestion Azure]: https://manage.windowsazure.com/
  []: ./media/mobile-services-windows-store-javascript-validate-modify-data-server-scripts/mobile-services-selection.png
  [1]: ./media/mobile-services-windows-store-javascript-validate-modify-data-server-scripts/mobile-portal-data-tables.png
  [2]: ./media/mobile-services-windows-store-javascript-validate-modify-data-server-scripts/mobile-insert-script-users.png
  [Affinage des requêtes au moyen de la pagination]: /fr-fr/develop/mobile/tutorials/add-paging-to-data-js
  [Autorisation des utilisateurs avec des scripts]: /fr-fr/develop/mobile/tutorials/authorize-users-in-scripts-js
  [Prise en main des notifications Push]: /fr-fr/develop/mobile/tutorials/get-started-with-push-js
  [Référence de script serveur Mobile Services]: http://go.microsoft.com/fwlink/?LinkId=262293
