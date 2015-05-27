
L’exemple précédent montre une connexion standard, qui nécessite que le client contacte le fournisseur d’identité et App Service à chaque démarrage de l’application. Cette méthode est inefficace. Il serait préférable de mettre en cache le jeton d’autorisation renvoyé par App Service et de l’utiliser en premier avant de faire appel à une connexion via un fournisseur.

1. La méthode recommandée pour chiffrer et stocker des jetons d’authentification sur un client iOS est d’utiliser le trousseau iOS. Ce didacticiel utilise [SSKeychain](https://github.com/soffes/sskeychain), un wrapper simple autour du trousseau iOS. Suivez les instructions de la page SSKeychain et ajoutez-le à votre projet. Vérifiez que le paramètre **Enable Modules** est activé dans l’option **Build Settings** du projet (section **Apple LLVM - Languages - Modules**.)

2. Ouvrez **QSTodoListViewController.m** et ajoutez le code suivant :


		- (void) saveAuthInfo {
				[SSKeychain setPassword:self.todoService.client.currentUser.mobileServiceAuthenticationToken forService:@"AzureMobileServiceTutorial" account:self.todoService.client.currentUser.userId]
		}


		- (void)loadAuthInfo {
				NSString *userid = [[SSKeychain accountsForService:@"AzureMobileServiceTutorial"][0] valueForKey:@"acct"];
		    if (userid) {
		        NSLog(@"userid: %@", userid);
		        self.todoService.client.currentUser = [[MSUser alloc] initWithUserId:userid];
		         self.todoService.client.currentUser.mobileServiceAuthenticationToken = [SSKeychain passwordForService:@"AzureMobileServiceTutorial" account:userid];

		    }
		}

3. Dans la méthode `loginAndGetData`, modifiez le bloc de fin de l’appel `loginWithProvider:controller:animated:completion:` en ajoutant un appel à `saveAuthInfo` juste avant la ligne `[self refresh]`. Avec cet appel, nous stockons simplement les propriétés user Id et token.

				[self saveAuthInfo];

4. Nous allons également charger l’identifiant utilisateur et le jeton au démarrage de l’application. Dans la méthode `viewDidLoad` de **QSTodoListViewController.m**, ajoutez un appel à loadAuthInfo, juste après l’initialisation de `self.todoService`.

				[self loadAuthInfo];

<!--HONumber=54-->