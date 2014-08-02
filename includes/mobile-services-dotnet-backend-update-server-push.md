<ol>

1.  Dans l'Explorateur de solutions de Visual Studio, développez le dossier **Contrôleurs** du projet de service mobile. Ouvrez TodoItemController.cs et mettez à jour la définition de la méthode `PostTodoItem` avec le code suivant :

         public async Task<IHttpActionResult> PostTodoItem(TodoItem item)
         {
             TodoItem current = await InsertAsync(item);
             WindowsPushMessage message = new WindowsPushMessage();
             message.XmlPayload = @"<
        xml version=""1.0"" encoding=""utf-8""
        >" +
                                  @"<toast><visual><binding template=""ToastText01"">" +
                                  @"<text id=""1"">" + item.Text + @"</text>" +
                                  @"</binding></visual></toast>";
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


