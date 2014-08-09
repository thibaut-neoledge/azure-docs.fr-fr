

1.  Dans l'Explorateur de solutions de Visual Studio, cliquez avec le bouton droit sur le dossier Controllers du projet de service mobile, développez **Add**, puis cliquez sur **New Scaffolded Item**.

    La boîte de dialogue Add Scaffold s'affiche.

2.  Développez **Azure Mobile Services**, cliquez sur **Azure Mobile Services Custom Controller**, cliquez sur **Add**, indiquez un **nom de contrôleur** pour `CompleteAllController`, puis cliquez à nouveau sur **Add**.

    ![](./media/mobile-services-dotnet-backend-create-custom-api/add-custom-api-controller.png)

    Une nouvelle classe de contrôleur vide appelée **CompleteAllController** est créée.

> [WACOM.NOTE]Si la boîte de dialogue ne présente pas de vue de structure spécifique à Mobile Services, créez un nouveau **Web API Controller - Empty**. Dans cette nouvelle classe de contrôleur, ajoutez une propriété publique **Services**, qui renvoie le type **ApiServices**. Cette propriété est utilisée pour accéder aux paramètres spécifiques au serveur depuis votre contrôleur.

1.  Dans le nouveau fichier de projet CompleteAllController.cs, ajoutez les instructions **using** suivantes :

         using System.Threading.Tasks;
         using todolistService.Models;

    Dans le code ci-dessus, remplacez `todolistService` par l'espace de noms de votre projet de service mobile, qui doit correspondre au nom du service mobile auquel est ajouté `Service`.

2.  Ajoutez le code suivant au nouveau contrôleur :

         // POST api/completeall        
         public Task<int> Post()
         {
             using (todolistContext context = new todolistContext())
             {
                 // Obtenir la base de données à partir du contexte.
                 var database = context.Database;

                 // Créer une instruction SQL qui définit tous les éléments non terminés
                 // pour terminer et exécuter l'instruction de manière asynchrone.
                 var sql = @"UPDATE TodoItems SET Complete = 1 " +
                             @"WHERE Complete = 0; SELECT @@ROWCOUNT as count";
                 var result = database.ExecuteSqlCommandAsync(sql);

                 // Consigner le résultat.
                 Services.Log.Info(string.Format("{0} items set to 'complete'.", 
                     result.ToString()));
                    
                 return result;
             }
         }

    Dans le code ci-dessus, remplacez `todolistContext` par le nom de la DbContext pour votre modèle de données, qui doit correspondre au nom du service mobile auquel est ajouté `Context`. Ce code utilise la [classe de base de données](http://msdn.microsoft.com/fr-fr/library/system.data.entity.database(v=vs.113).aspx) pour accéder à la table **TodoItems** directement afin de définir l'indicateur complété pour tous les éléments. Cette méthode prend en charge une requête POST et le nombre de lignes modifiées est renvoyé au client sous la forme d'une valeur entière.

    > [WACOM.NOTE] Les autorisations par défaut sont définies, ce qui signifie que tous les utilisateurs de l'application peuvent appeler l'API personnalisée. Toutefois, la clé de l'application n'étant pas distribuée ou stockée de façon sécurisée, elle ne peut pas être considérée comme une information d'identification sécurisée. De ce fait, vous devez restreindre l'accès aux utilisateurs authentifiés pour les opérations qui modifient les données ou affectent le service mobile.

Ensuite, vous allez modifier l'application de démarrage rapide pour ajouter un bouton et du code qui appelle de manière asynchrone la nouvelle API personnalisée.

