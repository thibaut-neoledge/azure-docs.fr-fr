1. Dans l’**Explorateur de solutions** de Visual Studio, développez le dossier **Contrôleurs** du projet de serveur principal mobile. Ouvrez **TodoItemController.cs**. Au début du fichier, ajoutez les instructions `using` suivantes :

        using Microsoft.Azure.Mobile.Server.Notifications;


2. Remplacez la méthode `PostTodoItem` par le code suivant :
        
        public async Task<IHttpActionResult> PostTodoItem(TodoItem item)
        {
            TodoItem current = await InsertAsync(item);

            HttpConfiguration config = this.Configuration;

            TemplatePushMessage message = new TemplatePushMessage();
            message.Add("message", item.Text);

            try
            {
                var client = new PushClient(config);
                var result = await client.SendAsync(message);

                config.Services.GetTraceWriter().Info(result.State.ToString());
            }
            catch (System.Exception ex)
            {
                config.Services.GetTraceWriter().Error(ex.Message, null, "Push.SendAsync Error");
            }

            return CreatedAtRoute("Tables", new { id = current.Id }, current);
        }

<!---HONumber=Oct15_HO3-->