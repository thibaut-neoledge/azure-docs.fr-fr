

Mise à jour de l'application pour appeler l'API personnalisée
-------------------------------------------------------------

1.  Dans Visual Studio, ouvrez le fichier default.html dans votre projet de démarrage rapide, recherchez l'élément **button** intitulé `buttonRefresh` et ajoutez le nouvel élément juste après :

         <button id="buttonCompleteAll" style="margin-left: 5px">Complete All</button>

    Le nouveau bouton est ajouté à la page.

2.  Ouvrez le fichier de code default.js dans le dossier du projet `js`, recherchez la fonction **refreshTodoItems** et vérifiez que cette fonction contient le code suivant :

         todoTable.where({ complete: false })
            .read()
            .done(function (results) {
                todoItems = new WinJS.Binding.List(results);
                listItems.winControl.itemDataSource = todoItems.dataSource;
            });            

    Les éléments sont filtrés de manière à ce que les éléments terminés ne soient pas renvoyés par la requête.

3.  Après la fonction **refreshTodoItems**, ajoutez le code suivant :

         var completeAllTodoItems = function () {
             var okCommand = new Windows.UI.Popups.UICommand("OK");
            
             // Appelez de manière asynchrone l'API personnalisée à l'aide de la méthode POST. 
             mobileService.invokeApi("completeall", {
                 body: null,
                 method: "post"
             }).done(function (results) {
                 var message = results.result.count + " item(s) marked as complete.";
                 var dialog = new Windows.UI.Popups.MessageDialog(message);
                 dialog.commands.append(okCommand);
                 dialog.showAsync().done(function () {
                     refreshTodoItems();
                 });
             }, function (error) {
                 var dialog = new Windows.UI.Popups
                     .MessageDialog(error.message);
                 dialog.commands.append(okCommand);
                 dialog.showAsync().done();
             });
         };

         buttonCompleteAll.addEventListener("click", function () {
             completeAllTodoItems();
         });

    Cette méthode gère l'événement **Click** pour le nouveau bouton. La méthode **InvokeApiAsync** est appelée sur le client pour envoyer une requête POST à la nouvelle API personnalisée. Le résultat renvoyé par l'API personnalisée apparaît dans la boîte de message, avec les erreurs éventuelles.

Test de l'application
---------------------

1.  Dans Visual Studio, appuyez sur la touche **F5** pour régénérer le projet et démarrer l'application.

2.  Dans l'application, tapez du texte dans **Insert a TodoItem**, puis cliquez sur **Enregistrer**.

3.  Répétez l'étape précédente jusqu'à ce que vous ayez ajouté plusieurs éléments todo dans la liste.

4.  Cliquez sur le bouton **Complete All**.

	![](./media/mobile-services-windows-store-javascript-call-custom-api/mobile-custom-api-windows-store-completed.png)

    Un message s'affiche pour indiquer le nombre d'éléments marqués comme terminés, puis la requête filtrée est de nouveau exécutée pour supprimer tous les éléments de la liste.
