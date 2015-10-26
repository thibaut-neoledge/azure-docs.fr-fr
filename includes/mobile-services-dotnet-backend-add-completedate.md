Dans cette section, nous allons modifier le modèle de notre base de données en ajoutant un nouveau champ d'horodatage appelé **CompleteDate**. Ce champ enregistre la dernière heure à laquelle la tâche (élément Todo) a été terminée. Entity Framework met à jour la base de données par rapport à la modification de notre modèle à l'aide de la classe d'initialiseur de base de données par défaut issue de [DropCreateDatabaseIfModelChanges](http://go.microsoft.com/fwlink/?LinkId=394621).

1. Dans l'Explorateur de solutions de Visual Studio, développez le dossier **App\_Start** dans le projet de service todolist. Ouvrez le fichier WebApiConfig.cs.

2. Dans le fichier WebApiConfig.cs, notez que votre classe d'initialiseur de base de données par défaut est issue de la classe `DropCreateDatabaseIfModelChanges`. Cela signifie qu'à chaque modification apportée au modèle, la table sera supprimée et recréée pour s'adapter au nouveau modèle. Par conséquent, les données dans la table seront perdues et la table sera réamorcée. Modifiez la méthode d’amorçage de l’initialiseur de base de données de manière à ce que les données amorcées se présentent comme suit lors de l’enregistrement du fichier WebApiConfig.cs.

    >[AZURE.NOTE]Lors de l'utilisation de l'initialiseur de base de données par défaut, Entity Framework supprime et recrée la base de données lorsqu'il détecte une modification du modèle de données dans la définition du modèle Code First. Pour modifier ce modèle de données et conserver les données existantes dans la base de données, vous devez utiliser les migrations Code First. Pour plus d'informations, consultez la rubrique [Utilisation des migrations Code First pour mettre à jour le modèle de données](../articles/mobile-services-dotnet-backend-how-to-use-code-first-migrations.md).

        List<TodoItem> todoItems = new List<TodoItem>
        {
          new TodoItem { Id = "1", Text = "First seed item", Complete = false },
          new TodoItem { Id = "2", Text = "Second seed item", Complete = false },
        };
     

3. Dans l'Explorateur de solutions de Visual Studio, développez le dossier **DataObjects** du projet de service todolist. Ouvrez le fichier TodoItem.cs et mettez à jour la classe TodoItem de manière à inclure le champ CompleteDate comme suit. Enregistrez ensuite le fichier TodoItem.cs.

        public class TodoItem : EntityData
        {
          public string Text { get; set; }
          public bool Complete { get; set; }
          public System.DateTime? CompleteDate { get; set; }
        }

4. Dans l'Explorateur de solutions de Visual Studio, développez le dossier **Controllers** du projet de service todolist. Ouvrez le fichier TodoItemController.cs et mettez à jour la méthode `PatchTodoItem` de manière à définir **CompleteDate** quand la propriété **Complete** passe de false à true. Enregistrez ensuite le fichier TodoItemController.cs.

        public Task<TodoItem> PatchTodoItem(string id, Delta<TodoItem> patch)
        {
            // If complete is being set to true and was false, set CompleteDate
            if ((patch.GetEntity().Complete == true) &&
                (GetTodoItem(id).Queryable.Single().Complete == false))
            {
                patch.TrySetPropertyValue("CompleteDate", System.DateTime.Now);
            }
            return UpdateAsync(id, patch);
        }


5. Regénérez le projet de service principal .NET todolist et vérifiez qu'aucune erreur de génération n'est survenue.

Mettez ensuite à jour l'application client pour afficher les nouvelles données **CompleteDate**.

<!---HONumber=Oct15_HO3-->