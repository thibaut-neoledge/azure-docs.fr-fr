

1. Dans Visual Studio, ouvrez le projet que vous avez modifié lorsque vous avez suivi le didacticiel **Prise en main des données**.

2. Appuyez sur la touche **F5** pour exécuter l'application, puis tapez du texte dans **Insert a TodoItem** et cliquez sur **Enregistrer**.

3. Répétez les étapes précédentes au moins trois fois de telle sorte que plus de trois éléments soient stockés dans la table TodoItem.

2. Dans le fichier default.js, remplacez la méthode **RefreshTodoItems** existante par le code suivant :

        var refreshTodoItems = function () {
            // Define a filtered query that returns the top 3 items.
            todoTable.where({ complete: false })
                .take(3)
                .read()
                .done(function (results) {
                    todoItems = new WinJS.Binding.List(results);
                    listItems.winControl.itemDataSource = todoItems.dataSource;
                });
        };

  	Cette requête, lorsqu'elle est exécutée pendant la liaison des données, renvoie les trois premiers éléments marqués comme terminés.

3. Appuyez sur la touche **F5** pour exécuter l'application.

  	Notez que seuls les trois premiers résultats de la table TodoItem sont affichés.

4. (Facultatif) Affichez l'URI de la requête envoyée au service mobile en utilisant un logiciel d'inspection des messages, tel que les outils destinés aux développeurs de navigateurs ou [Fiddler].

   	La méthode **take(3)** a été convertie en option de requête **$top=3** dans l'URI de requête.

5. Mettez à nouveau à jour la méthode **RefreshTodoItems** avec le code suivant :
            
        var refreshTodoItems = function () {
            // Define a filtered query that skips the first 3 items and 
            // then returns the next 3 items.
            todoTable.where({ complete: false })
                .skip(3)
                .take(3)
                .read()
                .done(function (results) {
                    todoItems = new WinJS.Binding.List(results);
                    listItems.winControl.itemDataSource = todoItems.dataSource;
                });
        };

   	Cette requête ignore les trois premiers résultats et renvoie les trois résultats suivants. Il s’agit en fait de la deuxième « page » de données, dont la taille est de trois éléments.

    > [AZURE.NOTE]Ce didacticiel s'appuie sur un scénario simplifié dans lequel les valeurs de pagination codées en dur sont transmises aux méthodes **Take** et **Skip**. Dans une application réelle, vous pouvez utiliser des requêtes semblables à celles indiquées plus haut avec un contrôle pager ou une interface utilisateur comparable pour permettre aux utilisateurs d’accéder aux pages précédentes et suivantes. Vous pouvez également appeler la méthode **includeTotalCount** pour obtenir le nombre total d'éléments disponibles sur le serveur, avec les données paginées.

6. (Facultatif) Là aussi, affichez l’URI de la requête envoyée au service mobile.

   	La méthode **skip(3)** a été convertie en option de requête **$skip=3** dans l'URI de requête.

<!-- URLs -->
[Fiddler]: http://go.microsoft.com/fwlink/?LinkID=262412

<!---HONumber=July15_HO4-->