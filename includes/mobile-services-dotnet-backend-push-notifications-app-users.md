
1. Dans l'Explorateur de solutions de Visual Studio, développez le dossier App_Start et ouvrez le fichier de projet WebApiConfig.cs.

2. Ajoutez la ligne de code suivante à la méthode Register après la définition de **ConfigOptions** :

        options.PushAuthorization = 
            Microsoft.WindowsAzure.Mobile.Service.Security.AuthorizationLevel.User;
 
	Ceci impose l'authentification de l'utilisateur avant l'inscription aux notifications Push.

2. Cliquez avec le bouton droit sur le projet, cliquez sur **Ajouter**, puis cliquez sur **Classe…**.

3. Appelez la nouvelle classe vide `PushRegistrationHandler`, puis cliquez sur **Ajouter**.

4. En haut de la page de code, ajoutez les instructions **using** suivantes :

		using System.Threading.Tasks; 
		using System.Web.Http; 
		using System.Web.Http.Controllers; 
		using Microsoft.WindowsAzure.Mobile.Service; 
		using Microsoft.WindowsAzure.Mobile.Service.Notifications; 
		using Microsoft.WindowsAzure.Mobile.Service.Security; 

5. Remplacez la classe **PushRegistrationHandler** existante par le code suivant :
 
	    public class PushRegistrationHandler : INotificationHandler
	    {
	        public Task Register(ApiServices services, HttpRequestContext context,
            NotificationRegistration registration)
	        {
	            try
	            {
	                // Perform a check here for user ID tags, which are not allowed.
	                if(!ValidateTags(registration))
	                {
	                    throw new InvalidOperationException(
	                        "You cannot supply a tag that is a user ID.");                    
	                }
	
	                // Get the logged-in user.
	                var currentUser = context.Principal as ServiceUser;
	
	                // Add a new tag that is the user ID.
	                registration.Tags.Add(currentUser.Id);
	
	                services.Log.Info("Registered tag for userId: " + currentUser.Id);
	            }
	            catch(Exception ex)
	            {
	                services.Log.Error(ex.ToString());
	            }
	                return Task.FromResult(true);
	        }
	
	        private bool ValidateTags(NotificationRegistration registration)
	        {
	            // Create a regex to search for disallowed tags.
	            System.Text.RegularExpressions.Regex searchTerm =
	            new System.Text.RegularExpressions.Regex(@"facebook:|google:|twitter:|microsoftaccount:",
	                System.Text.RegularExpressions.RegexOptions.IgnoreCase);
	
	            foreach (string tag in registration.Tags)
	            {
	                if (searchTerm.IsMatch(tag))
	                {
	                    return false;
	                }
	            }
	            return true;
	        }
		
	        public Task Unregister(ApiServices services, HttpRequestContext context, 
	            string deviceId)
	        {
	            // This is where you can hook into registration deletion.
	            return Task.FromResult(true);
	        }
	    }

	La méthode **Register** est appelée lors de l'inscription. Ceci vous permet d'ajouter une balise à l'inscription, qui est l'ID de l'utilisateur connecté. Les balises fournies sont validées pour empêcher un utilisateur de s’inscrire avec l’ID d’un autre utilisateur Lorsqu’une notification est envoyée à cet utilisateur, elle est reçue sur ce périphérique et tout autre périphérique enregistré par l’utilisateur.

6. Développez le dossier Controllers, ouvrez le fichier de projet TodoItemController.cs, localisez la méthode **PostTodoItem** et remplacez la ligne de code qui appelle **SendAsync** par le code suivant :

        // Get the logged-in user.
		var currentUser = this.User as ServiceUser;
		
		// Use a tag to only send the notification to the logged-in user.
        var result = await Services.Push.SendAsync(message, currentUser.Id);

7. Republiez le projet de service mobile.

Le service utilise maintenant la balise de l'identifiant utilisateur pour envoyer une notification Push (avec le texte de l'élément inséré) à toutes les inscriptions créées par l'utilisateur connecté.
 

<!---HONumber=July15_HO3-->