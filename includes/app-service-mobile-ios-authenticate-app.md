

1. Ouvrez **QSTodoListViewController.m**, puis, dans la méthode **viewDidLoad**, supprimez la ligne suivante :

        [self refresh];

2.	Juste après la méthode **viewDidLoad**, ajoutez le code suivant :  

        - (void)viewDidAppear:(BOOL)animated
        {
            MSClient *client = self.todoService.client;

            if (client.currentUser != nil) {
                return;
            }

            [client loginWithProvider:@"facebook" controller:self animated:YES completion:^(MSUser *user, NSError *error) {
                [self refresh];
            }];
        }

    > [AZURE.NOTE] Si vous utilisez un fournisseur d'identité autre que Facebook, modifiez la valeur transmise à **loginWithProvider**. Les valeurs prises en charge sont : _microsoftaccount_, _facebook_, _twitter_, _google_ ou _windowsazureactivedirectory_.

3. Appuyez sur **Exécuter** pour démarrer l'application, puis connectez-vous avec votre fournisseur d'identité choisi. Une fois connecté, vous devez être en mesure d'afficher la liste des tâches et d'effectuer des mises à jour.

<!--HONumber=49-->