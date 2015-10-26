

Par défaut, toutes les requêtes adressées aux ressources du service mobile sont réservées aux clients qui présentent la clé de l'application, ce qui n'offre pas de sécurisation stricte de l'accès aux ressources. Pour sécuriser vos ressources, vous devez limiter l’accès aux clients authentifiés.

1. Dans Visual Studio, ouvrez votre projet de service mobile, développez le dossier Contrôleurs, puis ouvrez **TodoItemController.cs**. La classe **TodoItemController** implémente l'accès aux données pour la table TodoItem. Ajoutez l'instruction `using` suivante :

		using Microsoft.WindowsAzure.Mobile.Service.Security;

2. Appliquez l'attribut _AuthorizeLevel_ suivant à la classe **TodoItemController** :

		[AuthorizeLevel(AuthorizationLevel.User)]

	Cela permet de s’assurer que toutes les opérations effectuées sur la table _TodoItem_ requièrent un utilisateur authentifié. Vous pouvez également appliquer l’attribut *AuthorizeLevel* au niveau de la méthode.

3. (Facultatif) Si vous souhaitez déboguer l'authentification localement, développez le dossier `App_Start`, ouvrez **WebApiConfig.cs**, puis ajoutez le code suivant à la méthode **Register**.

		config.SetIsHosted(true);

	Ce code indique au projet de service mobile local de s'exécuter comme s'il était hébergé sous Azure, en honorant les paramètres *AuthorizeLevel*. Sans ce paramètre, toutes les requêtes HTTP adressées à localhost sont autorisées sans authentification malgré la présence du paramètre *AuthorizeLevel*l. Lorsque vous activez le mode auto-hébergé, vous devez également définir une valeur pour la clé d'application locale.

4. (Facultatif) Dans le fichier de projet web.config, définissez une valeur de chaîne pour le paramètre d'application `MS_ApplicationKey`.

	Mot de passe que vous utilisez (sans nom d'utilisateur) pour tester les pages d'aide API lorsque vous exécutez le service localement. Cette valeur de chaîne n'est pas utilisée par le site actif dans Azure, et vous n'avez pas besoin d'utiliser la clé d'application actuelle ; toute valeur de chaîne valide fonctionnera.
 
4. Publiez à nouveau votre projet.

<!---HONumber=Oct15_HO3-->