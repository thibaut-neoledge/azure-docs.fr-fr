

1. Dans Visual Studio, cliquez sur le dossier Contrôleurs, développez **Ajouter**, puis cliquez sur **Nouvel élément structuré**. La boîte de dialogue Add Scaffold s'affiche.

2. Développez **Azure Mobile Services**, cliquez sur **Contrôleur personnalisé Azure Mobile Services**, cliquez sur **Ajouter**, indiquez un **Nom de contrôleur** pour `CompleteAllController`, puis cliquez à nouveau sur **Ajouter**.

	![boîte de dialogue Add Scaffold de l'API Web](./media/mobile-services-dotnet-backend-create-custom-api/add-custom-api-controller.png)

	Une nouvelle classe de contrôleur vide appelée **CompleteAllController** est créée.

	>[AZURE.NOTE]Si la boîte de dialogue ne présente pas de vue de structure spécifique à Mobile Services, créez un **Contrôleur API web - Vide**. Dans cette nouvelle classe de contrôleur, ajoutez une propriété publique **Services**, qui renvoie le type **ApiServices**. Cette propriété est utilisée pour accéder aux paramètres spécifiques au serveur depuis votre contrôleur.

3. Dans **CompleteAllController.cs**, ajoutez l'instruction **using** suivante : Remplacez `todolistService` par l'espace de noms de votre projet de service mobile, qui doit correspondre au nom du service mobile auquel `Service` est ajouté.

		using System.Threading.Tasks;
		using todolistService.Models;

4. Dans **CompleteAllController.cs**, ajoutez la classe suivante pour encapsuler la réponse envoyée au client.

        // We use this class to keep parity with other Mobile Services
        // that use the JavaScript backend. This way the same client
        // code can call either type of Mobile Service backend.
        public class MarkAllResult
        {
            public int count;
        }

5. Ajoutez le code suivant au nouveau contrôleur. Remplacez `todolistContext` par le nom de la DbContext pour votre modèle de données, qui doit correspondre au nom du service mobile auquel `Context` est ajouté. Remplacez également le nom de schéma mentionné dans l’instruction UPDATE par le nom de votre service mobile. Ce code utilise la [classe de base de données](http://msdn.microsoft.com/library/system.data.entity.database.aspx) pour accéder à la table **TodoItems** directement afin de définir l'indicateur complété pour tous les éléments. Cette méthode prend en charge une requête POST et le nombre de lignes modifiées est renvoyé au client sous la forme d'une valeur entière.


	    // POST api/completeall
        public async Task<MarkAllResult> Post()
        {
            using (todolistContext context = new todolistContext())
            {
                // Get the database from the context.
                var database = context.Database;

                // Create a SQL statement that sets all uncompleted items
                // to complete and execute the statement asynchronously.
                var sql = @"UPDATE todolist.TodoItems SET Complete = 1 " +
                            @"WHERE Complete = 0; SELECT @@ROWCOUNT as count";

                var result = new MarkAllResult();
                result.count = await database.ExecuteSqlCommandAsync(sql);

                // Log the result.
                Services.Log.Info(string.Format("{0} items set to 'complete'.",
                    result.count.ToString()));

                return result;
            }
        }

	> [AZURE.TIP]Avec les autorisations par défaut, toute personne avec la clé d’application peut appeler l’API personnalisée. Toutefois, la clé d’application n’est pas considérée comme une information d’identification sécurisée, car elle peut ne pas être distribuée ou stockée de manière sécurisée. Restreignez l’accès aux utilisateurs authentifiés pour renforcer la sécurité.

<!---HONumber=Sept15_HO3-->