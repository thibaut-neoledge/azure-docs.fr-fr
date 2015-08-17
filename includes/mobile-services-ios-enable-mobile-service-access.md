
À présent, mettons à jour l'application pour stocker les éléments dans Azure Mobile Services et non dans la collection locale en mémoire.

* Dans **TodoService.h**, recherchez la ligne suivante :

```
// TODO - create an MSClient property
```

Remplacez ce commentaire par la ligne suivante. Cela permet de créer une propriété qui représente le client `MSClient` à connecter au service.

```
@property (nonatomic, strong)   MSClient *client;
```


* Dans **TodoService.m**, recherchez la ligne suivante :

```
// TODO - create an MSTable property for your items
```

Remplacez ce commentaire par la ligne suivante dans la déclaration `@interface`. Cela permet de créer une représentation de propriété pour la table de votre service mobile.

```
@property (nonatomic, strong)   MSTable *table;
```


* Dans le portail de gestion, cliquez sur **Mobile Services**, puis sur le service mobile. Cliquez sur l'onglet **Tableau de bord**, puis notez l'**URL du site**. Ensuite, cliquez sur **Gérer les clés** et prenez note de la **Clé de l'application**. Ces valeurs sont nécessaires pour accéder au service mobile à partir de votre code d'application.


* Dans **TodoService.m**, recherchez la ligne suivante :

```
// Initialize the Mobile Service client with your URL and key.
```

À la suite de ce commentaire, ajoutez la ligne de code suivante. Remplacez `APPURL` et `APPKEY` par l'URL de site et la clé d'application obtenues à l'étape précédente.

```
self.client = [MSClient clientWithApplicationURLString:@"APPURL" applicationKey:@"APPKEY"];
```


* Dans **TodoService.m**, recherchez la ligne suivante :

```
// Create an MSTable instance to allow us to work with the TodoItem table.
```

Remplacez ce commentaire par la ligne suivante. Cela permet de créer l'instance de table TodoItem.

```
self.table = [self.client tableWithName:@"TodoItem"];
```


* Dans **TodoService.m**, recherchez la ligne suivante :

```
// Create a predicate that finds items where complete is false
```

Remplacez ce commentaire par la ligne suivante. Cela permet de créer une requête qui renvoie toutes les tâches non terminées.

```
NSPredicate * predicate = [NSPredicate predicateWithFormat:@"complete == NO"];
```


* Recherchez la ligne suivante :

```
// Query the TodoItem table and update the items property with the results from the service
```

Remplacez le commentaire et l'appel de bloc **completion** suivant par le code ci-dessous :

```
[self.table readWhere:predicate completion:^(NSArray *results, NSInteger totalCount, NSError *error)
{
self.items = [results mutableCopy];
   completion();
}];
```


* Recherchez la méthode **addItem**, puis remplacez son corps par le code suivant. Ce code envoie une requête d'insertion au service mobile.

```
// Insert the item into the TodoItem table and add to the items array on completion
[self.table insert:item completion:^(NSDictionary *result, NSError *error) {
    NSUInteger index = [items count];
    [(NSMutableArray *)items insertObject:item atIndex:index];

    // Let the caller know that we finished
    completion(index);
}];
```


* Recherchez la méthode **completeItem**, puis la ligne de code commentée suivante :

```
// Update the item in the TodoItem table and remove from the items array on completion
```

Remplacez le corps de la méthode à partir de ce point jusqu'à la fin de la méthode, par le code suivant. Ce code supprime les éléments todo une fois ceux-ci terminés.

```
// Update the item in the TodoItem table and remove from the items array on completion
[self.table update:mutable completion:^(NSDictionary *item, NSError *error) {

    // Get a fresh index in case the list has changed
    NSUInteger index = [items indexOfObjectIdenticalTo:mutable];

    [mutableItems removeObjectAtIndex:index];

    // Let the caller know that we have finished
    completion(index);
}];
```


* Dans TodoListController.m, recherchez la méthode **onAdd** et remplacez-le par le code suivant :

```
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
```

<!---HONumber=August15_HO6-->