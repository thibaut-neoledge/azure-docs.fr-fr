<properties linkid="develop-mobile-tutorials-dotnet-backend-validate-modify-and-augment-data-javascript" urlDisplayName="Validate and Modify Data" pageTitle="Use the .Net backend to validate and modify data (Windows Store) | Mobile Dev Center" metaKeywords="" description="Learn how to validate, modify, and augment data for your Javascript Windows Store app with .Net backend Windows Azure Mobile Services." metaCanonical="" services="" documentationCenter="Mobile" title="Validate and modify data in Mobile Services by using the .Net backend" authors="wesmc" solutions="" manager="" editor="" />

Validation et modification de données dans Mobile Services à l'aide du .Net principal
=====================================================================================

[Windows Store C\#](/fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-validate-modify-data/ "Windows Store C#") [Windows Store JavaScript](/fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-validate-modify-data/ "Windows Store JavaScript") [Windows Phone](/fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-phone-validate-modify-data/ "Windows Phone")[iOS](/en-us/develop/mobile/tutorials/validate-modify-and-augment-data-ios "iOS") [Android](/en-us/develop/mobile/tutorials/validate-modify-and-augment-data-android "Android") [HTML](/en-us/develop/mobile/tutorials/validate-modify-and-augment-data-html "HTML")[Xamarin.iOS](/en-us/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-ios "Xamarin.iOS") [Xamarin.Android](/en-us/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-android "Xamarin.Android")

[.NET principal](/fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-validate-modify-data/ ".NET principal") | [JavaScript principal](/fr-fr/documentation/articles/mobile-services-windows-store-javascript-validate-modify-data-server-scripts/ "JavaScript principal")

Cette rubrique montre comment utiliser le code dans les Windows Azure Mobile Services .Net principaux pour valider et modifier des données. Le service .Net principal est un service HTTP intégré à l'infrastructure API Web. Si vous connaissez la classe `ApiController` définie avec l'infrastructure API Web, la classe `TableController` fournie par Mobile Services s'avèrera très intuitive. `TableController` est dérivée de la classe `ApiController` et fournit des fonctionnalités supplémentaires pour créer une interface avec une table de base de données. Vous pouvez l'utiliser pour effectuer des opérations sur les données insérées et mises à jour, qu'il s'agisse de les valider ou de les modifier, comme cela est expliqué dans ce didacticiel.

Ce didacticiel vous familiarise avec ces étapes de base :

1.  [Ajout de la validation de longueur de chaîne](#string-length-validation)
2.  [Mise à jour du client pour la prise en charge de la validation](#update-client-validation)
3.  [Test de la longueur de chaîne](#test-length-validation)
4.  [Ajout d'un horodatage pour CompleteDate](#add-timestamp)
5.  [Mise à jour du client pour l'affichage de CompleteDate](#update-client-timestamp)

Ce didacticiel s'appuie sur la procédure et l'exemple d'application présentés dans le didacticiel précédent intitulé, [Prise en main](/fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started/) ou [Prise en main des données](/fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/). Avant de commencer, vous devez suivre le didacticiel [Prise en main](/fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started/) ou [Prise en main des données](/fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/).

Ajout d'un code de validation au service mobile
-----------------------------------------------

[WACOM.INCLUDE [mobile-services-dotnet-backend-add-validation](../includes/mobile-services-dotnet-backend-add-validation.md)]

Mise à jour du client
---------------------

Maintenant que le service mobile est configuré pour valider les données et envoyer des réponses d'erreur pour une longueur de texte incorrecte, vous devez mettre à jour l'application afin qu'elle traite les réponses d'erreur de la validation. L'erreur sera levée à partir de l'appel de l'application cliente vers `IMobileServiceTable<TodoItem].InsertAsync()`.

1.  Dans la fenêtre de l'explorateur de solutions dans Visual Studio, accédez au projet client Javascript, puis développez le dossier **js**. Puis ouvrez le fichier default.js

2.  Dans default.js , remplacez la fonction **insertTodoItem** existante par la définition de fonction suivante :

         var insertTodoItem = function (todoItem) {
             // Ce code insère un nouvel élément TodoItem dans la base de données. Une fois que l'opération est terminée
             // et que Mobile Services a attribué un ID, l'élément est ajouté à la liste des liaisons
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

	Cette version de la fonction inclut une gestion des erreurs et affiche une boîte de dialogue de message avec le message d'erreur à partir de la réponse, ainsi que du texte et du code d'état.

Test de la longueur de chaîne
-----------------------------

1.  Dans l'explorateur de solutions de Visual Studio, cliquez avec le bouton droit sur le projet d'application cliente JavaScript, puis cliquez sur **Déboguer**, **Démarrer une nouvelle instance**.

2.  Entrez le texte du nouvel élément todo, dont la longueur doit dépasser les 10 caractères, puis cliquez sur **Enregistrer**.

    ![](./media/mobile-services-dotnet-backend-windows-store-javascript-validate-modify-data/mobile-services-invalid-text-length.png)

3.  En case de texte incorrect, vous obtiendrez une boîte de dialogue de message semblable à la suivante.

    ![](./media/mobile-services-dotnet-backend-windows-store-javascript-validate-modify-data/mobile-services-invalid-text-length-exception-dialog.png)

Ajout d'un horodatage pour CompleteDate
---------------------------------------

[WACOM.INCLUDE [mobile-services-dotnet-backend-add-completedate](../includes/mobile-services-dotnet-backend-add-completedate.md)]

Mise à jour du client pour l'affichage de completeDate
------------------------------------------------------

L'étape finale consiste à mettre à jour le client de manière à afficher les nouvelles données **completeDate**.

1.  Dans l'explorateur de solutions pour Visual Studio, dans le projet client JavaScript, ouvrez le fichier default.html. Remplacez l'élément de balise `div` de modèle de liaison par la définition ci-dessous. Puis enregistrez le fichier. Ceci ajoute une balise `div` dont la propriété innerText est liée à **completeDate**.

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

2.  Dans default.js, supprimez la fonction clause `.Where` dans la fonction **refreshTodoItems** existante, pour que les todoitems terminés soient inclus dans les résultats.

             var refreshTodoItems = function () {
                 // Ce code permet d'actualiser les entrées de l'affichage de liste en interrogeant la table TodoItems.
                 // Cette requête exclut les éléments TodoItems terminés.
                 todoTable.read()
                     .done(function (results) {
                         todoItems = new WinJS.Binding.List(results);
                         listItems.winControl.itemDataSource = todoItems.dataSource;
                     });
             };

3.  Dans default.js, mettez à jour la fonction **updateCheckedTodoItem** comme suit, pour que les éléments soient actualisés après une mise à jour et que les éléments terminés ne soient pas supprimés de la liste. Puis enregistrez le fichier.

             var updateCheckedTodoItem = function (todoItem) {
                 // Ce code prend un TodoItem qui vient d'être terminé et met à jour la base de données. 
                 todoTable.update(todoItem).done(function () {
                     refreshTodoItems();
                 });
             };

4.  Dans la fenêtre de l'explorateur de solutions de Visual Studio, cliquez avec le bouton droit sur la **Solution**, puis cliquez sur **Régénérer la solution** pour régénérer le client et le service principal .NET. Vérifiez que les deux projets ont été générés sans erreurs.

    ![](./media/mobile-services-dotnet-backend-windows-store-javascript-validate-modify-data/mobile-services-rebuild-solution.png)

5.  Appuyez sur **F5** pour lancer l'application cliente et le service en local. Ajoutez certains nouveaux éléments et cliquez pour indiquer que certains éléments sont terminés, pour afficher l'horodatage **CompleteDate** en cours de mise à jour.

    ![](./media/mobile-services-dotnet-backend-windows-store-javascript-validate-modify-data/mobile-services-final-local-app-run.png)

6.  Dans l'explorateur de solutions pour Visual Studio, cliquez avec le bouton droit sur le projet de service todolist, puis cliquez sur **Publier**. Publiez votre service .NET principal sur Microsoft Azure en utilisant le fichier de configuration de publication que vous avez téléchargé à partir du portail Azure.

7.  Mettez à jour le fichier default.js pour le projet client en annulant les marques de commentaire de la connexion à l'adresse du service mobile. Testez l'application par rapport au .NET principal hébergé dans votre compte Azure.

Étapes suivantes
----------------

Maintenant que vous avez terminé ce didacticiel, pensez à suivre le dernier didacticiel dans la série des données : [Affinage des requêtes à la pagination](/en-us/develop/mobile/tutorials/add-paging-to-data-dotnet).

Les scripts serveur sont également utilisés dans le cadre du processus d'autorisation des utilisateurs et pour l'envoi de notifications Push. Pour plus d'informations, reportez-vous aux didacticiels suivants :

-   [Autorisation des utilisateurs côté service](/fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-authorize-users-in-scripts/)
    En savoir plus sur le filtrage des données en fonction de l'ID d'un utilisateur authentifié.

-   [Prise en main des notifications Push](fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-push/)
    En savoir plus sur l'envoi d'une notification Push très basique sur votre application.

-   [Guide de fonctionnement Mobile Services .NET](/en-us/develop/mobile/how-to-guides/work-with-net-client-library)
    En savoir plus sur l'utilisation de Mobile Services avec .NET.


