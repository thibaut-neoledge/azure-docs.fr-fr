
Par défaut, les API d’un backend d’application mobile peuvent être appelées de manière anonyme. Vous devez ensuite restreindre l’accès aux clients authentifiés uniquement.

1. Sur votre ordinateur, ouvrez le projet du serveur dans Visual Studio et accédez à **Contrôleurs** > **TodoItemController.cs**.

2. Ajoutez l’attribut `[Authorize]` à la classe **TodoItemController**, comme suit : Cela exige que toutes les opérations effectuées sur la table TodoItem le soient par un utilisateur authentifié. Pour restreindre l’accès à des méthodes spécifiques, vous pouvez également appliquer cet attribut à ces méthodes uniquement au lieu de la classe.


        [Authorize]
        public class TodoItemController : TableController<TodoItem>
   
    Cela exige que toutes les opérations effectuées sur la table TodoItem le soient par un utilisateur authentifié. Pour restreindre l’accès à des méthodes spécifiques, vous pouvez également appliquer cet attribut à ces méthodes uniquement au lieu de la classe.
   
3. Publiez à nouveau votre projet de serveur.

<!---HONumber=Oct15_HO3-->