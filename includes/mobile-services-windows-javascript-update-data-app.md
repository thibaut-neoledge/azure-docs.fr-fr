

1. Dans le fichier de script default.js, ajoutez la définition de fonction suivante juste en dessous de la ligne de code qui définit la liste todoItems :
 
        // Add a filter that adds a header to prevent caching. This makes sure that the 
		// latest data is returned when the 'Refresh; button is clicked.        
        var noCachingFilter = function (request, next, callback) {
            if (request.type === 'GET' && !request.headers['If-Modified-Since']) {
                request.headers['If-Modified-Since'] = 'Mon, 27 Mar 1972 00:00:00 GMT';
            }
            next(request, callback);
        };

	Cela définit une fonction de filtre qui ajoute l'en-tête `If-Modified-Since` pour empêcher la mise en cache sur le client.
 
2. Ensuite, annulez les marques de commentaire ou ajoutez la ligne de code suivante et remplacez `<yourClient>` par la variable ajoutée au fichier service.js lorsque vous avez connecté votre projet au service mobile :

		var todoTable = <yourClient>.withFilter(noCachingFilter).getTable('TodoItem');

   	This code creates a proxy object (**todoTable**) for the new database table, using the caching filter. 

3. Replace the **InsertTodoItem** function with the following code:

		var insertTodoItem = function (todoItem) {
		    // Inserts a new row into the database. When the operation completes
		    // and Mobile Services has assigned an id, the item is added to the binding list.
		    todoTable.insert(todoItem).done(function (item) {
		        todoItems.push(item);
		    });
		};

	Ce code permet d'insérer un nouvel élément dans la table.

3. Remplacez la fonction **RefreshTodoItem** par le code suivant :

        var refreshTodoItems = function () {
            // This code refreshes the entries in the list by querying the table.
            // Results are filtered to remove completed items.
            todoTable.where({ complete: false })
                .read().done(function (results) {
                todoItems = new WinJS.Binding.List(results);
                listItems.winControl.itemDataSource = todoItems.dataSource;
            });
        };

   	Cela définit la liaison sur la collection d'éléments dans todoTable, qui contient tous les objets **TodoItem** renvoyés depuis le service mobile. 

4. Remplacez la fonction **UpdateCheckedTodoItem** par le code suivant :
        
        var updateCheckedTodoItem = function (todoItem) {
            // This code takes a freshly completed TodoItem and updates the database. 
            todoTable.update(todoItem);
            // Remove the completed item from the filtered list.
            todoItems.splice(todoItems.indexOf(todoItem), 1);
        };

   	Cela permet d'envoyer une mise à jour de l'élément au service mobile.

Maintenant que l'application a été mise à jour pour utiliser Mobile Services pour le stockage principal, le moment est venu de tester l'application avec Mobile Services.


<!--HONumber=42-->
