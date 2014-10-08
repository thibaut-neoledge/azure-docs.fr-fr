Votre service mobile étant prêt, vous pouvez mettre à jour l'application pour stocker les éléments dans Mobile Services et non dans la collection locale.

1.  Si vous n'avez pas encore installé le [Kit de développement logiciel (SDK) Mobile Services pour iOS][], faites-le maintenant.

2.  Dans l'Explorateur de projets dans Xcode, ouvrez les fichiers TodoService.m et TodoService.h situés dans le dossier Quickstart, puis ajoutez l'instruction import suivante :

        #import <WindowsAzureMobileServices/WindowsAzureMobileServices.h>  

3.  Dans le fichier ToDoService.h, recherchez la ligne de code de commentaire suivante :

        // Create an MSClient property comment in the #interface declaration for the TodoService. 

    À la suite de ce commentaire, ajoutez la ligne de code suivante :

        @property (nonatomic, strong)   MSClient *client;

    Cela permet de créer une propriété qui représente le client MSClient qui se connecte au service

4.  Dans le fichier TodoService.m, recherchez la ligne de code de commentaire suivante :

        // Create an MSTable property for your items. 

    À la suite de ce commentaire, ajoutez la ligne de code suivante dans la déclaration @interface :

        @property (nonatomic, strong)   MSTable *table;

    Cela permet de créer une représentation de propriété pour la table de votre service mobile.

5.  Dans le portail de gestion, cliquez sur **Mobile Services**, puis sur le service mobile que vous venez de créer.

6.  Cliquez sur l'onglet **Tableau de bord** et notez la valeur **URL du site**, puis cliquez sur **Gérer les clés** et notez la valeur de **Clé de l'application**.

    ![][]

    Ces valeurs sont nécessaires pour accéder au service mobile à partir de votre code d'application.

7.  Revenez dans Xcode, ouvrez le fichier TodoService.m et recherchez la ligne de code de commentaire suivante :

        // Initialize the Mobile Service client with your URL and key.

    À la suite de ce commentaire, ajoutez la ligne de code suivante :

        self.client = [MSClient clientWithApplicationURLString:@"APPURL" withApplicationKey:@"APPKEY"];

    Cela permet de créer une instance du client Mobile Services.

8.  Remplacez les valeurs **APPURL** et **APPKEY** de ce code par l'URL et la clé d'application du service mobile que vous avez acquis à l'étape 6.

9.  Recherchez la ligne de code de commentaire suivante :

        // Create an MSTable instance to allow us to work with the TodoItem table.

    À la suite de ce commentaire, ajoutez la ligne de code suivante :

        self.table = [self.client getTable:@"TodoItem"];

    Cela permet de créer l'instance de table TodoItem.

10. Recherchez la ligne de code de commentaire suivante :

        // Create a predicate that finds items where complete is false comment in the refreshDataOnSuccess method. 

    À la suite de ce commentaire, ajoutez la ligne de code suivante :

        NSPredicate * predicate = [NSPredicate predicateWithFormat:@"complete == NO"];

    Cela permet de créer une requête qui renvoie toutes les tâches non terminées.

11. Recherchez la ligne de code de commentaire suivante :

        // Query the TodoItem table and update the items property with the results from the service.

    Remplacez ce commentaire et l'appel de bloc **completion** suivant par le code ci-dessous :

        // Query the TodoItem table and update the items property with the results from the service
        [self.table readWhere:predicate completion:^(NSArray *items, NSInteger totalCount, NSError *error)
        {
           self.items = [items mutableCopy];
           completion();
        }]; 

12. Recherchez la méthode **addItem** et ajoutez le code suivant au corps de celle-ci :

        // Insert the item into the TodoItem table and add to the items array on completion
        [self.table insert:item completion:^(NSDictionary *result, NSError *error) {
            NSUInteger index = [items count];
            [(NSMutableArray *)items insertObject:item atIndex:index];

            // Let the caller know that we finished
            completion(index);
        }];

    Ce code envoie une requête d'insertion au service mobile.

13. Recherchez la méthode **completeItem** et ajoutez le code suivant au corps de celle-ci :

        // Update the item in the TodoItem table and remove from the items array on completion
        [self.table update:mutable completion:^(NSDictionary *item, NSError *error) {

            // TODO
            // Get a fresh index in case the list has changed
            NSUInteger index = [items indexOfObjectIdenticalTo:mutable];

            [mutableItems removeObjectAtIndex:index];

            // Let the caller know that we have finished
            completion(index);
        }]; 

    Ce code supprime les éléments TodoItems une fois ceux-ci terminés.

Maintenant que l'application a été mise à jour pour utiliser Mobile Services pour le stockage principal, le moment est venu de tester l'application avec Mobile Services.

  [Kit de développement logiciel (SDK) Mobile Services pour iOS]: https://go.microsoft.com/fwLink/p/?LinkID=266533
  []: ./media/mobile-services-ios-enable-mobile-service-access/mobile-dashboard-tab.png
