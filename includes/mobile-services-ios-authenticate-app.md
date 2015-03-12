

1. Ouvrez le fichier projet QSTodoListViewController.m et, dans la méthode **viewDidLoad**, supprimez le code suivant qui permet de recharger les données dans la table :

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

   > [AZURE.NOTE] Si vous utilisez un fournisseur d'identité autre que Facebook, remplacez la valeur transmise à la méthode **loginWithProvider** ci-dessus par l'une des valeurs suivantes : _microsoftaccount_, _facebook_, _twitter_, _google_ ou _windowsazureactivedirectory_.
		
3. Appuyez sur le bouton **Exécuter** pour générer le projet, démarrez l'application dans l'émulateur iPhone, puis connectez-vous avec le fournisseur d'identité de votre choix.

   Lorsque vous êtes connecté, l'application doit s'exécuter sans erreur et vous devez pouvoir exécuter des requêtes Mobile Services et mettre à jour les données.

\<!--HONumber=42-->
