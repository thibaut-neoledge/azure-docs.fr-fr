
Il est toujours souhaitable de valider la longueur des données soumises par les utilisateurs. Dans cette section, vous allez ajouter un script au service mobile qui valide la longueur des données de chaîne envoyées au service mobile et refuse les chaînes trop longues, en l'occurrence, celles qui font plus de 10 caractères.

1. Démarrez Visual Studio avec l'option **Exécuter en tant qu'administrateur** et ouvrez la solution contenant le projet de service mobile sur lequel vous avez travaillé dans le didacticiel [Prise en main] ou [Prise en main des données](../articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data.md).
2. Dans la fenêtre de l'Explorateur de solutions, développez le projet de service de la liste des tâches, puis **Contrôleurs**. Ouvrez le fichier TodoItemController.cs qui fait partie du projet de service mobile.
   
       ![](./media/mobile-services-dotnet-backend-add-validation/mobile-services-open-todoitemcontroller.png)
3. Remplacez la méthode `PostTodoItem` par la méthode suivante qui permet de vérifier que la chaîne de texte ne dépasse pas 10 caractères. Pour les éléments dont la longueur de texte dépasse 10 caractères, la méthode renvoie un code d’état HTTP 400 (Requête incorrecte) accompagné d’un message descriptif.

        public async Task<IHttpActionResult> PostTodoItem(TodoItem item)
        {
            if (item.Text.Length > 10)
            {
                return BadRequest("The Item's Text length must not be greater than 10.");
            }
            else
            {
                TodoItem current = await InsertAsync(item);
                return CreatedAtRoute("Tables", new { id = current.Id }, current);
            } 
        }



1. Cliquez avec le bouton droit sur le projet de service, puis cliquez sur **Générer** pour générer le projet de service mobile. Vérifiez qu’aucune erreur n’est survenue.
   
       ![](./media/mobile-services-dotnet-backend-add-validation/mobile-services-build-dotnet-service.png)
2. Cliquez avec le bouton droit sur le projet de service, puis cliquez sur **Publier**. Publiez le service mobile sur votre compte Microsoft Azure à l'aide des paramètres de publication précédemment utilisés dans le didacticiel [Prise en main] ou [Prise en main des données](../articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data.md).
   
   > [!NOTE]
   > Vous pouvez également le tester avec le service hébergé localement dans IIS Express. Pour plus d'informations, consultez le didacticiel [Prise en main des données](../articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data.md).
   > 
   > 
   
    ![](./media/mobile-services-dotnet-backend-add-validation/mobile-services-publish-dotnet-service.png)

<!-- URLs. -->
[Prise en main]: ../articles/mobile-services/mobile-services-dotnet-backend-windows-store-dotnet-get-started.md

<!---HONumber=Oct15_HO3-->