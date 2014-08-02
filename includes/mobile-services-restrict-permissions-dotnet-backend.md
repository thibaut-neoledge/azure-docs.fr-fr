

Par défaut, toutes les requêtes adressées aux ressources du service mobile sont réservées aux clients qui présentent la clé de l'application, ce qui n'offre pas de sécurisation stricte de l'accès aux ressources. Pour sécuriser vos ressources, vous devez limiter l'accès aux clients authentifiés.

1.  Dans Visual Studio, ouvrez le projet qui contient votre service mobile.

2.  Dans l'Explorateur de solutions de Visual Studio, développez le dossier Contrôleurs et ouvrez le fichier projet TodoItemController.cs.

    La classe **TodoItemController** implémente l'accès aux données pour la table TodoItem.

3.  Ajoutez l'instruction `using` suivante au début de la page de code :

        using Microsoft.WindowsAzure.Mobile.Service.Security;

4.  Appliquez l'attribut AuthorizeLevel suivant à la classe **TodoItemController** :

        [AuthorizeLevel(AuthorizationLevel.User)] 

    Cela permet de s'assurer que toutes les opérations effectuées sur la table **TodoItem** requièrent un utilisateur authentifié.

    > [WACOM.NOTE] Appliquez l'attribut AuthorizeLevel aux méthodes individuelles pour définir des niveaux d'autorisation spécifiques sur les méthodes exposées par le contrôleur.

5.  Développez le dossier App\_Start, ouvrez le fichier projet WebApiConfig.cs, puis ajoutez le code suivant à la méthode **Register** :

        config.SetIsHosted(true);

    Ce code indique au projet de service mobile local de s'exécuter comme s'il était hébergé sous Azure, en honorant les paramètres AuthorizeLevel. Sans ce paramètre, toutes les requêtes HTTP adressées à *localhost* sont autorisées sans authentification malgré la présence du paramètre AuthorizeLevel.

6.  Publiez à nouveau votre projet de service.


