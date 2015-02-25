<properties pageTitle="Utilisation du service principal .Net pour valider et modifier des données (Windows Store) | Centre de développement mobile" description="Découvrez comment valider, modifier et compléter les données pour votre application Windows Store JavaScript avec Microsoft Azure Mobile Services sur le serveur principal .Net." services="mobile-services" documentationCenter="windows" authors="wesmc7777" manager="dwrede" editor=""/>

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="javascript" ms.topic="article" ms.date="09/26/2014" ms.author="wesmc"/>

# Validation et modification de données dans Mobile Services à l'aide du service principal .NET

[AZURE.INCLUDE [mobile-services-selector-validate-modify-data](../includes/mobile-services-selector-validate-modify-data.md)]

Cette rubrique montre comment utiliser le code dans vos services mobiles Azure .Net principaux pour valider et modifier des données. Le service principal .NET est un service HTTP intégré à l'infrastructure API web. Si vous connaissez la classe `ApiController` définie avec l'infrastructure API Web, la classe `TableController` fournie par Mobile Services s'avèrera très intuitive. `TableController` est dérivée de la classe `ApiController` et fournit des fonctionnalités supplémentaires pour créer une interface avec une table de base de données. Vous pouvez l'utiliser pour effectuer des opérations sur les données insérées et mises à jour, qu'il s'agisse de les valider ou de les modifier, comme cela est expliqué dans ce didacticiel. 

Ce didacticiel vous familiarise avec ces étapes de base :

1. [Ajout de la validation de longueur de chaîne]
2. [Mise à jour du client pour la prise en charge de la validation]
3. [Test de la validation de la longueur]
4. [Ajout d'un horodatage pour CompleteDate]
5. [Mise à jour du client pour l'affichage de CompleteDate]

Ce didacticiel s'appuie sur la procédure et l'exemple d'application présentés dans le didacticiel précédent, [Prise en main] ou [Prise en main des données](/fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/). Avant de commencer, vous devez suivre le didacticiel [Prise en main] ou [Prise en main des données](/fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/).  

## <a name="string-length-validation"></a>Ajout d'un code de validation au service mobile

[AZURE.INCLUDE [mobile-services-dotnet-backend-add-validation](../includes/mobile-services-dotnet-backend-add-validation.md)]


## <a name="update-client-validation"></a>Mise à jour du client

Maintenant que le service mobile est configuré pour valider les données et envoyer des réponses d'erreur pour une longueur de texte incorrecte, vous devez mettre à jour l'application afin qu'elle traite les réponses d'erreur de la validation. L'erreur sera levée à partir de l'appel de l'application cliente à  `IMobileServiceTable<TodoItem].InsertAsync()`.

1. Dans la fenêtre de l'Explorateur de solutions de Visual Studio, accédez au projet client Javascript, puis développez le dossier **js**. Puis ouvrez le fichier default.js

2. Dans default.js , remplacez la fonction **insertTodoItem** existante par la définition de fonction suivante :


        var insertTodoItem = function (todoItem) {
            // This code inserts a new TodoItem into the database. When the operation completes
            // and Mobile Services has assigned an id, the item is added to the Binding List
            todoTable.insert(todoItem)
                .then(function (item) {
                  todoItems.push(item);
                },
                function (error) {
                  var msgDialog =
                    new Windows.UI.Popups.MessageDialog(JSON.parse(error.request.responseText).message,
                    error.request.statusText + "(" + error.request.status + ")");
                  msgDialog.showAsync();
                });
        };

   	Cette version de la fonction inclut une gestion des erreurs et affiche une `MessageDialog` avec le message d'erreur à partir de la réponse, ainsi que du texte et du code d'état.

## <a name="test-length-validation"></a>Test de la longueur de chaîne

1. Dans l'explorateur de solutions de Visual Studio, cliquez avec le bouton droit sur le projet d'application cliente JavaScript, puis cliquez sur **Déboguer**, **Démarrer une nouvelle instance**.

2. Entrez le texte du nouvel élément todo, dont la longueur doit dépasser les 10 caractères, puis cliquez sur **Enregistrer**.

    ![][1]

3. En case de texte incorrect, vous obtiendrez une boîte de dialogue de message semblable à la suivante.

    ![][2]

## <a name="add-timestamp"></a>Ajout d'un champ d'horodatage pourCompleteDate


[AZURE.INCLUDE [mobile-services-dotnet-backend-add-completedate](../includes/mobile-services-dotnet-backend-add-completedate.md)]

## <a name="update-client-timestamp"></a>Mise à jour du client pour l'affichage de completeDate

L'étape finale consiste à mettre à jour le client de manière à afficher les nouvelles données **completeDate**. 


1. Dans l'explorateur de solutions pour Visual Studio, dans le projet client JavaScript, ouvrez le fichier default.html. Remplacez l'élément de balise `div` de modèle de liaison par la définition ci-dessous. Puis enregistrez le fichier. Cela ajoute une balise `div` dont la propriété innerText est liée à **completeDate**.
	      
        <div id="TemplateItem" data-win-control="WinJS.Binding.Template">
          <div style="display: -ms-grid; -ms-grid-columns: 3">
            <input class="itemCheckbox" type="checkbox" data-win-bind="checked: complete; 
              dataContext: this" />
            <div style="-ms-grid-column: 2; -ms-grid-row-align: center; margin-left: 5px" 
              data-win-bind="innerText: text">
            </div>
            <div style="-ms-grid-column: 3; -ms-grid-row-align: center; margin-left: 10px" 
              data-win-bind="innerText: completeDate">
            </div>
          </div>
        </div>



2. Dans default.js, supprimez la fonction clause `.Where` dans la fonction **refreshTodoItems** existante, pour que les todoitems terminés soient inclus dans les résultats.

            var refreshTodoItems = function () {
                // This code refreshes the entries in the list view be querying the TodoItems table.
                // The query excludes completed TodoItems
                todoTable.read()
                    .done(function (results) {
                        todoItems = new WinJS.Binding.List(results);
                        listItems.winControl.itemDataSource = todoItems.dataSource;
                    });
            };


3. Dans default.js, mettez à jour la fonction **updateCheckedTodoItem** comme suit, pour que les éléments soient actualisés après une mise à jour et que les éléments terminés ne soient pas supprimés de la liste. Puis enregistrez le fichier.	

            var updateCheckedTodoItem = function (todoItem) {
                // This code takes a freshly completed TodoItem and updates the database. 
                todoTable.update(todoItem).done(function () {
                    refreshTodoItems();
                });
            };


4. Dans la fenêtre de l'explorateur de solutions de Visual Studio, cliquez avec le bouton droit sur la **Solution**, puis cliquez sur **Régénérer la solution** pour régénérer le client et le service principal .NET. Vérifiez que les deux projets ont été générés sans erreurs.

    ![][3]
	
5. Appuyez sur **F5** pour lancer l'application cliente et le service en local. Ajoutez certains nouveaux éléments et cliquez pour indiquer que certains éléments sont terminés, pour afficher l'horodatage **CompleteDate** en cours de mise à jour.

    ![][4]

6. Dans l'explorateur de solutions pour Visual Studio, cliquez avec le bouton droit sur le projet de service todolist, puis cliquez sur **Publier**. Publiez votre service .NET principal sur Microsoft Azure en utilisant le fichier de configuration de publication que vous avez téléchargé à partir du portail Azure.

7. Mettez à jour le fichier default.js pour le projet client en annulant les marques de commentaire de la connexion à l'adresse du service mobile. Testez l'application par rapport au .NET principal hébergé dans votre compte Azure.




## <a name="next-steps"> </a>Étapes suivantes

Maintenant que vous avez terminé ce didacticiel, pensez à suivre le dernier didacticiel dans la série des données : [Affinage des requêtes au moyen de la pagination]

Les scripts serveur sont également utilisés dans le cadre du processus d'autorisation des utilisateurs et pour l'envoi de notifications Push. Pour plus d'informations, reportez-vous aux didacticiels suivants :

* [Autorisation côté service des utilisateurs]
  <br/>En savoir plus sur le filtrage des données en fonction de l'ID d'un utilisateur authentifié.

* [Prise en main des notifications Push] 
  <br/>En savoir plus sur l'envoi d'une notification Push très basique sur votre application.

* [Guide de fonctionnement Mobile Services .NET]
  <br/>En savoir plus sur l'utilisation de Mobile Services avec .NET

<!-- Anchors. -->
[Ajout de la validation de longueur de chaîne]: #string-length-validation
[Mise à jour du client pour la prise en charge de la validation]: #update-client-validation
[Test de la longueur de chaîne]: #test-length-validation
[Ajout d'un horodatage pour CompleteDate]: #add-timestamp
[Mise à jour du client pour l'affichage de CompleteDate]: #update-client-timestamp
[Étapes suivantes]: #next-steps

<!-- Images. -->
[1]: ./media/mobile-services-dotnet-backend-windows-store-javascript-validate-modify-data/mobile-services-invalid-text-length.png
[2]: ./media/mobile-services-dotnet-backend-windows-store-javascript-validate-modify-data/mobile-services-invalid-text-length-exception-dialog.png
[3]: ./media/mobile-services-dotnet-backend-windows-store-javascript-validate-modify-data/mobile-services-rebuild-solution.png
[4]: ./media/mobile-services-dotnet-backend-windows-store-javascript-validate-modify-data/mobile-services-final-local-app-run.png



<!-- URLs. -->
[Prise en main de Mobile Services]: /fr-fr/develop/mobile/tutorials/get-started/#create-new-service
[Autorisation côté service des utilisateurs]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-authorize-users-in-scripts/
[Affinage des requêtes au moyen de la pagination]: /fr-fr/develop/mobile/tutorials/add-paging-to-data-dotnet
[Mise en route]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started/
[Prise en main de l'authentification]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-users/
[Prise en main des notifications Push]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-push/
[JavaScript et HTML]: /fr-fr/develop/mobile/tutorials/validate-modify-and-augment-data-js

[Portail de gestion]: https://manage.windowsazure.com/
[Portail de gestion Azure]: https://manage.windowsazure.com/
[Guide de fonctionnement Mobile Services .NET]: /fr-fr/develop/mobile/how-to-guides/work-with-net-client-library


<!--HONumber=42-->
