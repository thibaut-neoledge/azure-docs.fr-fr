
Votre service mobile étant prêt, vous pouvez mettre à jour l'application pour stocker les éléments dans Mobile Services et non dans la collection locale.

1. Si vous n'avez pas encore installé le [Kit de développement logiciel (SDK) Mobile Services pour iOS](https://go.microsoft.com/fwLink/p/?LinkID=266533), faites-le maintenant. Une fois installé, copiez le répertoire WindowsAzureMobileServices.framework et remplacez le répertoire WindowsAzureMobileServices.framework inclus dans le projet téléchargé. De cette façon, vous utilisez le Kit de développement logiciel (SDK) Azure Mobile Services le plus récent.

2. Dans le fichier TodoService.h, recherchez la ligne de code de commentaire suivante :

        // TODO - create an MSClient proeprty

   	À la suite de ce commentaire, ajoutez la ligne de code suivante :

        @property (nonatomic, strong)   MSClient *client;

   	Cela permet de créer une propriété qui représente le client MSClient qui se connecte au service

3. Dans le fichier TodoService.m, recherchez la ligne de code de commentaire suivante :

        // TODO - create an MSTable property for your items

   	À la suite de ce commentaire, ajoutez la ligne de code suivante dans la déclaration @interface :

        @property (nonatomic, strong)   MSTable *table;

   	Cela permet de créer une représentation de propriété pour la table de votre service mobile.

4. Dans le portail de gestion, cliquez sur **Mobile Services**, puis sur le service mobile que vous venez de créer.

5. Cliquez sur l'onglet **Tableau de bord** et notez la valeur **URL du site**, puis cliquez sur **Gérer les clés** et notez la valeur de **Clé de l'application**.

   	![](./media/mobile-services-ios-enable-mobile-service-access/mobile-dashboard-tab.png)

  	Ces valeurs sont nécessaires pour accéder au service mobile à partir de votre code d'application.

6. Revenez dans Xcode, ouvrez le fichier TodoService.m et recherchez la ligne de code de commentaire suivante :

        // Initialize the Mobile Service client with your URL and key.

    À la suite de ce commentaire, ajoutez la ligne de code suivante :

        self.client = [MSClient clientWithApplicationURLString:@"APPURL" applicationKey:@"APPKEY"];

    Cela permet de créer une instance du client Mobile Services.

7. Remplacez les valeurs **APPURL** et **APPKEY** de ce code par l'URL et la clé d'application du service mobile que vous avez acquises à l'étape 6.

8. Recherchez la ligne de code de commentaire suivante :

        // Create an MSTable instance to allow us to work with the TodoItem table.

    À la suite de ce commentaire, ajoutez la ligne de code suivante :

        self.table = [self.client tableWithName:@"TodoItem"];

    Cela permet de créer l'instance de table TodoItem.

9. Recherchez la ligne de code de commentaire suivante :

 	    // Create a predicate that finds items where complete is false

    À la suite de ce commentaire, ajoutez la ligne de code suivante :

        NSPredicate * predicate = [NSPredicate predicateWithFormat:@"complete == NO"];

    Cela permet de créer une requête qui renvoie toutes les tâches non terminées.

10. Recherchez la ligne de code de commentaire suivante :

        // Query the TodoItem table and update the items property with the results from the service

   	Remplacez ce commentaire et l'appel de bloc **completion** suivant par le code ci-dessous :

        [self.table readWhere:predicate completion:^(NSArray *results, NSInteger totalCount, NSError *error)
		{
		   self.items = [results mutableCopy];
           completion();
        }];

11. Recherchez la méthode **addItem**, puis remplacez le corps de la méthode par le code suivant :

        // Insert the item into the TodoItem table and add to the items array on completion
        [self.table insert:item completion:^(NSDictionary *result, NSError *error) {
            NSUInteger index = [items count];
            [(NSMutableArray *)items insertObject:item atIndex:index];

            // Let the caller know that we finished
            completion(index);
        }];

    Ce code envoie une requête d'insertion au service mobile.

12. Recherchez la méthode **completeItem**, puis la ligne de code commentée suivante :

        // Update the item in the TodoItem table and remove from the items array on completion

    Remplacez le corps de la méthode à partir de ce point jusqu'à la fin de la méthode, par le code suivant :

        // Update the item in the TodoItem table and remove from the items array on completion
        [self.table update:mutable completion:^(NSDictionary *item, NSError *error) {

            // Get a fresh index in case the list has changed
            NSUInteger index = [items indexOfObjectIdenticalTo:mutable];

            [mutableItems removeObjectAtIndex:index];

            // Let the caller know that we have finished
            completion(index);
	    }];

   	Ce code supprime les éléments TodoItems une fois ceux-ci terminés.

13. Dans TodoListController.m, recherchez la méthode **onAdd** et remplacez-le par le code suivant :

      - (IBAction)onAdd:(id)sender
      {
          if (itemText.text.length  == 0)
          {
              return;
          }

          NSDictionary *item = @{ @"text" : itemText.text, @"complete" : @NO };
          UITableView *view = self.tableView;
          [self.todoService addItem:item completion:^(NSUInteger index)
          {
              NSIndexPath *indexPath = [NSIndexPath indexPathForRow:index inSection:0];
              [view insertRowsAtIndexPaths:@[ indexPath ]
                          withRowAnimation:UITableViewRowAnimationTop];
          }];

          itemText.text = @"";
      }


Maintenant que l'application a été mise à jour pour utiliser Mobile Services pour le stockage principal, le moment est venu de tester l'application avec Mobile Services.
