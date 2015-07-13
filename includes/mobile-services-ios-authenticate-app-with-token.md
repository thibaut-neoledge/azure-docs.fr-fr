
L’exemple précédent contacte le fournisseur d’identité et le service mobile à chaque démarrage de l’application. Au lieu de cela, vous pouvez mettre en cache le jeton d’autorisation et essayer de l’utiliser d’abord.

* La méthode recommandée pour chiffrer et stocker des jetons d’authentification sur un client iOS est d’utiliser le trousseau iOS. Nous allons utiliser [SSKeychain](https://github.com/soffes/sskeychain), un wrapper simple autour du trousseau iOS. Suivez les instructions de la page SSKeychain et ajoutez-le à votre projet. Vérifiez que le paramètre **Enable Modules** est activé dans l’option **Build Settings** du projet (section **Apple LLVM - Languages - Modules**.)

* Ouvrez **QSTodoListViewController.m** et ajoutez le code suivant :

```
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
```

* Dans `loginAndGetData`, modifiez le bloc de fin de `loginWithProvider:controller:animated:completion:`. Ajoutez la ligne suivante juste avant `[self refresh]` pour stocker l’ID utilisateur et les propriétés du jeton :

```
				[self saveAuthInfo];
```

* Nous allons charger l’ID utilisateur et le jeton au démarrage de l’application. Dans le `viewDidLoad` dans **QSTodoListViewController.m**, ajoutez ce droit après l’initialisation de `self.todoService`.

```
				[self loadAuthInfo];
```

<!---HONumber=62-->