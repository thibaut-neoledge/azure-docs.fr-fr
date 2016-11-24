**Objective-C**: 

1. Sur votre Mac, ouvrez *QSTodoListViewController.m* dans Xcode et ajoutez la méthode suivante. Remplacez *google* par *microsoftaccount*, *twitter*, *facebook* ou *windowsazureactivedirectory* si vous n’utilisez pas Google comme fournisseur d’identité. Si vous utilisez Facebook, [vous devrez autoriser les domaines Facebook dans votre application](https://developers.facebook.com/docs/ios/ios9#whitelist).
   
            - (void) loginAndGetData
            {
                MSClient *client = self.todoService.client;
                if (client.currentUser != nil) {
                    return;
                }
   
                [client loginWithProvider:@"google" controller:self animated:YES completion:^(MSUser *user, NSError *error) {
                    [self refresh];
                }];
            }
2. Remplacez `[self refresh]` dans `viewDidLoad` dans *QSTodoListViewController.m* par les éléments suivants :
   
            [self loginAndGetData];
3. Appuyez sur *Exécuter* pour démarrer l’application, puis ouvrez une session. Une fois connecté, vous devez être en mesure d’afficher la liste des tâches et d’effectuer des mises à jour.

**Swift**:

1. Sur votre Mac, ouvrez *ToDoTableViewController.swift* dans Xcode et ajoutez la méthode suivante. Remplacez *google* par *microsoftaccount*, *twitter*, *facebook* ou *windowsazureactivedirectory* si vous n’utilisez pas Google comme fournisseur d’identité. Si vous utilisez Facebook, [vous devrez autoriser les domaines Facebook dans votre application](https://developers.facebook.com/docs/ios/ios9#whitelist).
   
            func loginAndGetData() {
   
                guard let client = self.table?.client where client.currentUser == nil else {
                    return
                }
   
                client.loginWithProvider("google", controller: self, animated: true) { (user, error) in
                    self.refreshControl?.beginRefreshing()
                    self.onRefresh(self.refreshControl)
                }
            }
2. Supprimez les lignes `self.refreshControl?.beginRefreshing()` et `self.onRefresh(self.refreshControl)` à la fin de `viewDidLoad()` dans *ToDoTableViewController.swift*. Ajoutez un appel à `loginAndGetData()` à leur place :
   
            loginAndGetData()
3. Appuyez sur *Exécuter* pour démarrer l’application, puis ouvrez une session. Une fois connecté, vous devez être en mesure d’afficher la liste des tâches et d’effectuer des mises à jour.



<!--HONumber=Nov16_HO3-->


