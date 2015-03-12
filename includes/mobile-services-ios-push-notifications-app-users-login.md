
Ensuite, vous devez changer la façon dont les notifications Push sont inscrites pour vous assurer que l'utilisateur est authentifié avant de tenter une inscription. 

1. Dans **QSAppDelegate.m**, supprimez complètement l'implémentation de**didFinishLaunchingWithOptions** :

		
		- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:
		(NSDictionary *)launchOptions
		{
		    // Register for remote notifications
		    [[UIApplication sharedApplication] registerForRemoteNotificationTypes:
		    UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];
		    return YES;
		}

2. Ouvrez le fichier de projet **QSTodoListViewController.m** et, dans la méthode **viewDidLoad**, ajoutez le code supprimé au-dessus de l'ajout :

	
		- (void)viewDidAppear:(BOOL)animated
		{
		    MSClient *client = self.todoService.client;
		
		    if (client.currentUser != nil) {
		        return;
		    }
		
		    [client loginWithProvider:@"facebook" controller:self animated:YES completion:^(MSUser *user, NSError *error) {
		        [self refresh];
		    }];
		
		    // Register for remote notifications
		    [[UIApplication sharedApplication] registerForRemoteNotificationTypes:
		    UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];
		}

\<!--HONumber=42-->
