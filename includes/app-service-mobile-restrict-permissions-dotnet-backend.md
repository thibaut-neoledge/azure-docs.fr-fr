

Par défaut, toutes les requêtes adressées aux ressources de l’application mobile App Service sont réservées aux clients qui présentent la clé de l’application, ce qui n’offre pas de sécurisation stricte de l’accès aux ressources. Pour sécuriser vos ressources, vous devez limiter l'accès aux clients authentifiés.

1. Dans Visual Studio, ouvrez le projet qui contient le code de votre application mobile. 

2. Dans l'Explorateur de solutions de Visual Studio, développez le dossier Contrôleurs et ouvrez le fichier projet TodoItemController.cs.

	La classe **TodoItemController** implémente l'accès aux données pour la table TodoItem.

3. Ajoutez l’instruction `using` suivante au début de la page de code :

		using Microsoft.Azure.Mobile.Security;

4. Appliquez l'attribut AuthorizeLevel suivant à la classe **TodoItemController** :

		[AuthorizeLevel(AuthorizationLevel.User)] 

	Cela permet de s'assurer que toutes les opérations effectuées sur la table **TodoItem** requièrent un utilisateur authentifié.

	>[AZURE.NOTE]Appliquez l’attribut AuthorizeLevel aux méthodes individuelles pour définir des niveaux d’autorisation spécifiques sur les méthodes exposées par le contrôleur.

5. Si vous souhaitez déboguer l'authentification localement, développez le dossier App_Start, ouvrez le fichier projet WebApiConfig.cs, puis ajoutez le code suivant à la méthode **Register** :

		config.SetIsHosted(true);
	
	Ce code indique au projet local de s’exécuter comme s’il était hébergé sous Azure, en honorant les paramètres AuthorizeLevel. Sans ce paramètre, toutes les requêtes HTTP adressées à *localhost* sont autorisées sans authentification malgré la présence du paramètre AuthorizeLevel.

6. Republiez votre projet d’application mobile.

<!---HONumber=July15_HO3-->