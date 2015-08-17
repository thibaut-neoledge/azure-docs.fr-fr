

1. Ensuite, annulez les marques de commentaire ou ajoutez la ligne de code suivante et remplacez `<yourClient>` par la variable ajoutée au fichier service.js lorsque vous avez connecté votre projet au service mobile :

		var todoTable = <yourClient>.getTable('TodoItem');

   	Ce code crée un objet proxy (**todoTable**) pour la nouvelle table de base de données, à l'aide du filtre de mise en cache.

2. Remplacez la fonction **InsertTodoItem** par le code suivant :

		var insertTodoItem = function (todoItem) {
		    // Inserts a new row into the database. When the operation completes
		    // and Mobile Services has assigned an id, the item is added to the binding list.
		    todoTable.insert(todoItem).done(function (item) {
		        todoItems.push(item);
		    });
		};

	Ce code permet d'insérer un nouvel élément dans la table.

3. Remplacez la fonction **RefreshTodoItem** par le code suivant :

        var refreshTodoItems = function () {
            // This code refreshes the entries in the list by querying the table.
            // Results are filtered to remove completed items.
            todoTable.where({ complete: false })
                .read().done(function (results) {
                todoItems = new WinJS.Binding.List(results);
                listItems.winControl.itemDataSource = todoItems.dataSource;
            });
        };

   	Cela définit la liaison sur la collection d'éléments de todoTable, qui contient tous les objets **TodoItem** renvoyés depuis le service mobile.

4. Remplacez la fonction **UpdateCheckedTodoItem** par le code suivant :
        
        var updateCheckedTodoItem = function (todoItem) {
            // This code takes a freshly completed TodoItem and updates the database. 
            todoTable.update(todoItem);
            // Remove the completed item from the filtered list.
            todoItems.splice(todoItems.indexOf(todoItem), 1);
        };

   	Cela permet d'envoyer une mise à jour de l'élément au service mobile.

Maintenant que l'application a été mise à jour pour utiliser Mobile Services pour le stockage principal, le moment est venu de tester l'application avec Mobile Services.

<!---HONumber=August15_HO6-->