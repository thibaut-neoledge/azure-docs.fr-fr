

Par défaut, les points de terminaison définis dans votre application mobile sont exposés publiquement. Pour sécuriser vos ressources, vous devez limiter l'accès aux clients authentifiés.

1. Dans Visual Studio, ouvrez le projet qui contient le code de votre application mobile. 

2. Dans l'Explorateur de solutions de Visual Studio, développez le dossier Contrôleurs et ouvrez le fichier projet TodoItemController.cs.

	La classe **TodoItemController** implémente l'accès aux données pour la table TodoItem.

3. Appliquez l'attribut `Authorize` à la classe **TodoItemController** :

        [Authorize]
        public class TodoItemController : TableController<TodoItem>

	Cela permet de s'assurer que toutes les opérations effectuées sur la table **TodoItem** requièrent un utilisateur authentifié.

	>[AZURE.NOTE]Appliquez l’attribut Authorize aux méthodes individuelles pour définir des niveaux d’autorisation spécifiques sur les méthodes exposées par le contrôleur.

4. Republiez votre projet d’application mobile.

<!---HONumber=July15_HO4-->