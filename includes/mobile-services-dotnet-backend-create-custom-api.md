

1. Dans l'Explorateur de solutions de Visual Studio, cliquez avec le bouton droit sur le dossier Contrôleurs du projet de service mobile, développez **Ajouter**, puis cliquez sur **Nouvel élément structuré**.

	La boîte de dialogue Add Scaffold s'affiche.

2. Développez **Azure Mobile Services**, cliquez sur **Azure Mobile Services Custom Controller**, puis sur **Ajouter**, indiquez un **nom de contrôleur** pour CompleteAllController, puis cliquez à nouveau sur  **Ajouter** again.

	![Web API Add Scaffold dialog](./media/mobile-services-dotnet-backend-create-custom-api/add-custom-api-controller.png)

	Une nouvelle classe de contrôleur vide appelée **CompleteAllController** est créée..

>[WACOM.NOTE]Si la boîte de dialogue ne présente pas de vue de structure spécifique à Mobile Services, créez un nouveau **Contrôleur API web - Vide**. Dans cette nouvelle classe de contrôleur, ajoutez une propriété publique **Services**, qui renvoie le type **ApiServices**. Cette propriété est utilisée pour accéder aux paramètres spécifiques au serveur depuis votre contrôleur.

3. Dans le nouveau fichier de projet CompleteAllController.cs, ajoutez les instructions **using** suivantes :

		using System.Threading.Tasks;
		using todolistService.Models;

	Dans le code ci-dessus, remplacez todolistService par l'espace de noms de votre projet de service mobile, qui doit correspondre au nom du service mobile auquel est ajouté Service. 

4. Dans CompleteAllController.cs, ajoutez la définition de classe suivante à l'espace de noms. Cette classe contient la réponse envoyée au client.

        // We use this class to keep parity with other Mobile Services
        // that use the JavaScript backend. This way the same client
        // code can call either type of Mobile Service backend.
        public class MarkAllResult
        {
            public Int32 count;
        }


5. Ajoutez le code suivant au nouveau contrôleur :

	    // POST api/completeall        
        public async Task<MarkAllResult> Post()
        {
            using (todolistContext context = new todolistContext())
            {
                // Get the database from the context.
                var database = context.Database;

                // Create a SQL statement that sets all uncompleted items
                // to complete and execute the statement asynchronously.
                var sql = @"UPDATE todolistService.TodoItems SET Complete = 1 " +
                            @"WHERE Complete = 0; SELECT @@ROWCOUNT as count";

                var result = new MarkAllResult();
                result.count = await database.ExecuteSqlCommandAsync(sql);

                // Log the result.
                Services.Log.Info(string.Format("{0} items set to 'complete'.", 
                    result.count.ToString()));
                
                return result;
            }
        }

	Dans le code ci-dessus, remplacez todolistContext par le nom de la DbContext pour votre modèle de données, qui doit correspondre au nom du service mobile auquel est ajouté Context. Remplacez également le nom de schéma mentionné dans l'instruction UPDATE par le nom de votre service mobile. 

	Ce code utilise la [classe de base de données](http://msdn.microsoft.com/fr-fr/library/system.data.entity.database.aspx) pour accéder à la table **TodoItems** directement afin de définir l'indicateur complété pour tous les éléments. Cette méthode prend en charge une demande POST et le nombre de lignes modifiées est renvoyé au client sous la forme d'une valeur entière.

	> [WACOM.NOTE] es autorisations par défaut sont définies, ce qui signifie que tous les utilisateurs de l'application peuvent appeler l'API personnalisée. Toutefois, la clé de l'application n'étant pas distribuée ou stockée de façon sécurisée, elle ne peut pas être considérée comme une information d'identification sécurisée. De ce fait, vous devez restreindre l'accès aux utilisateurs authentifiés pour les opérations qui modifient les données ou affectent le service mobile. 

Ensuite, vous allez modifier l'application de démarrage rapide pour ajouter un bouton et du code qui appelle de manière asynchrone la nouvelle API personnalisée.

