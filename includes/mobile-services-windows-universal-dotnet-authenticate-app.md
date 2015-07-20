
1. Ouvrez le fichier projet partagé MainPage.cs et ajoutez l'extrait de code suivant à la classe MainPage :
	
		// Define a member variable for storing the signed-in user. 
        private MobileServiceUser user;

		// Define a method that performs the authentication process
		// using a Facebook sign-in. 
        private async System.Threading.Tasks.Task AuthenticateAsync()
        {
            while (user == null)
            {
                string message;
                try
                {
					// Change 'MobileService' to the name of your MobileServiceClient instance.
					// Sign-in using Facebook authentication.
                    user = await App.MobileService
                        .LoginAsync(MobileServiceAuthenticationProvider.Facebook);
                    message = 
                        string.Format("You are now signed in - {0}", user.UserId);
                }
                catch (InvalidOperationException)
                {
                    message = "You must log in. Login Required";
                }
                        
                var dialog = new MessageDialog(message);
                dialog.Commands.Add(new UICommand("OK"));
                await dialog.ShowAsync();
            }
        }

    Cet utilisateur est authentifié à l’aide d’une connexion Facebook. Si vous utilisez un fournisseur d'identité différent de Facebook, remplacez la valeur de **MobileServiceAuthenticationProvider** ci-dessus par la valeur de votre fournisseur.

3. Commentez ou supprimer l'appel de la méthode **RefreshTodoItems** dans la méthode à substituer **OnNavigatedTo** existante.

	Cela empêche les données d'être chargées avant que l'utilisateur ne soit authentifié.

	>[AZURE.NOTE]Pour s’authentifier à partir d’une application Windows Phone Store 8.1, vous devez appeler LoginAsync après la méthode **OnNavigated** et après que l’événement **Loaded** de la page a été déclenché. Pour cela, dans ce didacticiel, un bouton **Se connecter** est ajouté à l'application.

4. Ajoutez l'extrait de code suivant à la classe MainPage :

        private async void ButtonLogin_Click(object sender, RoutedEventArgs e)
        {
            // Login the user and then load data from the mobile service.
            await AuthenticateAsync();

            // Hide the login button and load items from the mobile service.
            this.ButtonLogin.Visibility = Windows.UI.Xaml.Visibility.Collapsed;
            await RefreshTodoItems();
        }
		
5. Dans le projet d'application Windows Store, ouvrez le fichier de projet MainPage.xaml, puis ajoutez l'élément **Bouton** suivant juste avant l'élément définissant le bouton **Enregistrer** :

		<Button Name="ButtonLogin" Click="ButtonLogin_Click" 
                        Visibility="Visible">Sign in</Button>

6. Dans le projet d'application Windows Phone, ajoutez les éléments **Bouton** suivants juste avant l'élément définissant le bouton **Enregistrer** :

		<Button Grid.Row ="1" Grid.Column="1" Name="ButtonLogin" Click="ButtonLogin_Click" 
                        Visibility="Visible" Margin="10, 0, 0, 0">Sign in</Button> 

5. Ouvrez le fichier de projet partagé App.xaml.cs, puis ajoutez l’instruction using suivante, si elle n’est pas déjà présente :

        using Microsoft.WindowsAzure.MobileServices;  
 
6. Dans le fichier de projet App.xaml.cs, ajoutez le code suivant :

        protected override void OnActivated(IActivatedEventArgs args)
        {
			// Windows Phone 8.1 requires you to handle the respose from the WebAuthenticationBroker.
            #if WINDOWS_PHONE_APP
            if (args.Kind == ActivationKind.WebAuthenticationBrokerContinuation)
            {
				// Completes the sign-in process started by LoginAsync.
				// Change 'MobileService' to the name of your MobileServiceClient instance. 
                App.MobileService.LoginComplete(args as WebAuthenticationBrokerContinuationEventArgs);
            }
            #endif

            base.OnActivated(args);
        }

	Si la méthode **OnActivated** existe déjà, ajoutez simplement le bloc de code `#if...#endif`.

8. Appuyez sur la touche F5 pour exécuter l'application Windows Store, puis cliquez sur le bouton **Se connecter** pour vous connecter à l'application avec le fournisseur d'identité choisi.

   	Lorsque vous êtes connecté, l'application doit s'exécuter sans erreur et vous devez pouvoir exécuter des requêtes Mobile Services et mettre à jour les données.

9. Cliquez avec le bouton droit sur le projet d'application Windows Phone Store, cliquez sur **Définir comme projet de démarrage**, puis répétez l'étape précédente pour vérifier que l'application Windows Phone Store s'exécute correctement.

<!---HONumber=July15_HO2-->