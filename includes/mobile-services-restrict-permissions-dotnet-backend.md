Par défaut, toutes les requêtes adressées aux ressources du service mobile sont réservées aux clients qui présentent la clé de l'application, ce qui n'offre pas de sécurisation stricte de l'accès aux ressources. Pour sécuriser vos ressources, vous devez limiter l'accès aux clients authentifiés.

1. Dans Visual Studio, ouvrez votre projet de service mobile, développez le dossier Contrôleurs, puis ouvrez **TodoItemController.cs**. La classe **TodoItemController** implémente l'accès aux données pour la table TodoItem. Ajoutez l'instruction `using` suivante :

		using Microsoft.WindowsAzure.Mobile.Service.Security;

2. Appliquez l'attribut _AuthorizeLevel_ suivant à la classe **TodoItemController** : Cela permet de s'assurer que toutes les opérations effectuées sur la table _TodoItem_ requièrent un utilisateur authentifié.

		[AuthorizeLevel(AuthorizationLevel.User)]

	>[AZURE.NOTE]Appliquez l'attribut AuthorizeLevel aux méthodes individuelles pour définir des niveaux d'autorisation spécifiques sur les méthodes exposées par le contrôleur.

3. Si vous souhaitez déboguer l'authentification localement, développez le dossier `App_Start`, ouvrez le fichier projet **WebApiConfig.cs**, puis ajoutez le code suivant à la méthode **Register** :  

		config.SetIsHosted(true);

	Ce code indique au projet de service mobile local de s'exécuter comme s'il était hébergé sous Azure, en honorant les paramètres *AuthorizeLevel*. Sans ce paramètre, toutes les requêtes HTTP adressées à localhost sont autorisées sans authentification malgré la présence du paramètre *AuthorizeLevel*. 

4. Publiez à nouveau votre projet.

<!--HONumber=49-->