
1. Dans l'Explorateur de solutions de Visual Studio, développez le dossier **Contrôleurs** du projet de service mobile. Ouvrez TodoItemController.cs. Au début du fichier, ajoutez les instructions `using` suivantes :

		using System;
		using System.Collections.Generic;

2. Mettez à jour la définition de méthode `PostTodoItem` avec le code suivant :

        public async Task<IHttpActionResult> PostTodoItem(TodoItem item)
        {
            TodoItem current = await InsertAsync(item);

            Dictionary<string, string> data = new Dictionary<string, string>()
            {
                { "message", item.Text}
            };
            GooglePushMessage message = new GooglePushMessage(data, TimeSpan.FromHours(1));

            try
            {
                var result = await Services.Push.SendAsync(message);
                Services.Log.Info(result.State.ToString());
            }
            catch (System.Exception ex)
            {
                Services.Log.Error(ex.Message, null, "Push.SendAsync Error");
            }
            return CreatedAtRoute("Tables", new { id = current.Id }, current);
        }

    Ce code envoie une notification Push (avec le texte de l'élément inséré) après l'insertion d'un élément ToDo. En cas d'erreur, le code ajoute une entrée dans le journal des erreurs consultable à partir de l'onglet **Journaux** du service mobile sur le portail de gestion.

3. Publiez à nouveau votre projet de service mobile dans Azure.

<!---HONumber=62-->