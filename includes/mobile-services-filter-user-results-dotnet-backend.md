Maintenant que l'authentification est requise pour accéder aux données de la table TodoItem, vous pouvez utiliser la valeur userID attribuée par Mobile Services pour filtrer les données renvoyées.

> [WACOM.NOTE] Pour les méthodes ci-dessous, l'attribut **RequiresAuthorizationAttribute** doit être appliqué au paramètre **Authorizationlevel** de **User**. L'accès à la table est ainsi limité aux utilisateurs authentifiés.

1.  Dans Visual Studio 2013, ouvrez votre projet de service mobile, développez le dossier DataObjects, puis ouvrez le fichier projet TodoItem.cs.

    La classe TodoItem définit l'objet de données et vous devez ajouter une propriété UserId pour le filtrage.

2.  Ajoutez la nouvelle propriété UserId suivante à la classe **TodoItem** :

        public string UserId { get; set; }

    > [WACOM.NOTE] Lors de l'utilisation de l'initialiseur de base de données par défaut, Entity Framework supprime et recrée la base de données lorsqu'il détecte une modification du modèle de données dans la définition du modèle Code First. Pour modifier ce modèle de données et conserver les données existantes dans la base de données, vous devez utiliser les migrations Code First. L'initialiseur par défaut ne peut pas être utilisé avec une base de données SQL dans Azure. Pour plus d'informations, consultez la rubrique [Utilisation des migrations Code First pour mettre à jour le modèle de données][Utilisation des migrations Code First pour mettre à jour le modèle de données].

3.  Dans l'Explorateur de solutions, développez le dossier Contrôleurs, ouvrez le fichier projet TodoItemController.cs et ajoutez l'instruction **using** suivante :

        using Microsoft.WindowsAzure.Mobile.Service.Security;

    La classe **TodoItemController** implémente l'accès aux données pour la table TodoItem.

4.  Recherchez la méthode **PostTodoItem** et ajoutez le code suivant au début de celle-ci :

        // Get the logged-in user.
        var currentUser = User as ServiceUser;

        // Set the user ID on the item.
        item.UserId = currentUser.Id;

    Ce code ajoute à l'élément une valeur UserId correspondant à l'ID de l'utilisateur authentifié avant de l'insérer dans la table TodoItem.

5.  Recherchez la méthode **GetAllTodoItems** et remplacez l'instruction **return** existante par la ligne de code suivante :

        // Get the logged-in user.
        var currentUser = User as ServiceUser;

        return Query().Where(todo => todo.UserId == currentUser.Id);

    Cette requête filtre les objets TodoItem retournés de façon à ce que chaque utilisateur reçoive seulement les éléments qu'il a insérés. Vous pouvez également

6.  Publiez à nouveau le projet de service mobile dans Azure.

  [Utilisation des migrations Code First pour mettre à jour le modèle de données]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-how-to-use-code-first-migrations
