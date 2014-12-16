
L'exemple précédent montrait une connexion standard, qui nécessite que le client contacte le fournisseur d'identité et le service mobile à chaque démarrage de l'application. Cette méthode est non seulement inefficace, mais vous pouvez rencontrer des problèmes d'utilisation si de nombreux clients tentent de lancer votre application en même temps. Une meilleure approche consiste à mettre en cache le jeton d'autorisation renvoyé par Mobile Services et à l'utiliser en premier avant de faire appel à la connexion via un fournisseur. 


>[WACOM.NOTE] Vous pouvez mettre en cache le jeton émis par Mobile Services, que vous utilisiez l'authentification gérée par un client ou gérée par un service. Ce didacticiel utilise cette dernière.

1. La façon recommandée de chiffrer et de stocker des jetons d'authentification sur un client iOS est d'utiliser le Keychain. Pour cela, créez une classe KeychainWrapper, en copiant [KeychainWrapper.m](https://github.com/WindowsAzure-Samples/iOS-LensRocket/blob/master/source/client/LensRocket/Misc/KeychainWrapper.m) et [KeychainWrapper.h](https://github.com/WindowsAzure-Samples/iOS-LensRocket/blob/master/source/client/LensRocket/Misc/KeychainWrapper.h) depuis l'[exemple LensRocket](https://github.com/WindowsAzure-Samples/iOS-LensRocket). Nous utilisons cette classe KeychainWrapper, car la classe KeychainWrapper définie dans la documentation d'Apple ne compte pas pour le comptage de référence automatique (ARC, Automatic Reference Counting).


2. Ouvrez le fichier de projet **QSTodoListViewController.m** et ajoutez le code suivant :

		
		- (void) saveAuthInfo{
		    [KeychainWrapper createKeychainValue:self.todoService.client.currentUser.userId
				 forIdentifier:@"userid"];
		    [KeychainWrapper createKeychainValue:self.todoService.client.currentUser.mobileServiceAuthenticationToken
				 forIdentifier:@"token"];
		}
		
		
		- (void)loadAuthInfo {
		    NSString *userid = [KeychainWrapper keychainStringFromMatchingIdentifier:@"userid"];
		    if (userid) {
		        NSLog(@"userid: %@", userid);
		        self.todoService.client.currentUser = [[MSUser alloc] initWithUserId:userid];
		        self.todoService.client.currentUser.mobileServiceAuthenticationToken = [KeychainWrapper keychainStringFromMatchingIdentifier:@"token"];
		    }
		}
		


3. À la fin de la méthode **viewDidAppear** dans **QSTodoListViewController.m**, ajoutez un appel à saveAuthInfo. Avec cet appel, nous stockons simplement les propriétés userId et token.  



		- (void)viewDidAppear:(BOOL)animated
		{
		    MSClient *client = self.todoService.client;
		
		    if (client.currentUser != nil) {
		        return;
		    }
		
		    [client loginWithProvider:@"facebook" controller:self animated:YES completion:^(MSUser *user, NSError *error) {
		
		        [self saveAuthInfo];
		        [self refresh];
		    }];
		}

  
4. Maintenant que nous avons vu comment nous pouvons mettre en cache le jeton et l'identifiant de l'utilisateur, voyons comment nous pouvons charger cela quand l'application démarre. Dans la méthode **viewDidLoad** dans **QSTodoListViewController.m**, ajoutez un appel à loadAuthInfo, après que **self.todoService** a été initialisé. 
		
		- (void)viewDidLoad
		{
		    [super viewDidLoad];
		    
		    // Create the todoService - this creates the Mobile Service client inside the wrapped service
		    self.todoService = [QSTodoService defaultService];

			[self loadAuthInfo];
		    
		    // Set the busy method
		    UIActivityIndicatorView *indicator = self.activityIndicator;
		    self.todoService.busyUpdate = ^(BOOL busy)
		    {
		        if (busy)
		        {
		            [indicator startAnimating];
		        } else
		        {
		            [indicator stopAnimating];
		        }
		    };
		    
		    // have refresh control reload all data from server
		    [self.refreshControl addTarget:self
		                            action:@selector(onRefresh:)
		                  forControlEvents:UIControlEventValueChanged];
		
		    // load the data
		    [self refresh];
		}

5. Si l'application fait une demande auprès de votre service mobile, que celle-ci doit être honorée car l'utilisateur est authentifié, et que vous recevez une réponse 401 (erreur non autorisée), cela signifie que le jeton utilisateur que vous utilisez est expiré. Dans le gestionnaire d'achèvement pour chacune de nos méthodes qui interagit avec notre service mobile, nous pouvons vérifier s'il y a une réponse 401 ou bien nous pouvons gérer cette situation à un seul endroit : la méthode handleRequest de MSFilter.  Pour voir comment gérer ce scénario, consultez [ce post de blog](http://www.thejoyofcode.com/Handling_expired_tokens_in_your_application_Day_11_.aspx)

