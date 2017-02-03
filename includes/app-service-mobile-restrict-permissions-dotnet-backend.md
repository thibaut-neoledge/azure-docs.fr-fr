
Par défaut, les API d’un serveur principal Mobile Apps peuvent être appelées de manière anonyme. Vous devez ensuite restreindre l’accès aux clients authentifiés uniquement.  

* **Serveur principal Node.js (par le biais du Portail Azure)** :  

    Dans les paramètres Mobile Apps, cliquez sur **Tables faciles**, puis sélectionnez votre table. Cliquez sur **Modifier les autorisations**, sélectionnez **Accès authentifié uniquement** pour toutes les autorisations, puis cliquez sur **Enregistrer**.
* **Serveur principal .NET (C#)** :  

    Dans le projet du serveur, accédez à **Contrôleurs** > **TodoItemController.cs**. Ajoutez l’attribut `[Authorize]` à la classe **TodoItemController** comme suit : Pour restreindre l’accès à des méthodes spécifiques, vous pouvez également appliquer cet attribut à ces méthodes uniquement au lieu de la classe. Publier à nouveau le projet de serveur

        [Authorize]
        public class TodoItemController : TableController<TodoItem>

* **Serveur principal Node.js (via le code Node.js)** :  

    Pour demander l’authentification pour l’accès des tables, ajoutez la ligne suivante au script de serveur Node.js :

        table.access = 'authenticated';

    Pour plus d’informations, consultez la section [Procédure : exiger une authentification pour l’accès aux tables](../articles/app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#howto-tables-auth). Pour savoir comment télécharger le projet de code de démarrage rapide de votre site, consultez [Procédure : télécharger le projet de code de démarrage rapide du serveur principal Node.js à l’aide de Git](../articles/app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#download-quickstart).


<!--HONumber=Dec16_HO2-->


