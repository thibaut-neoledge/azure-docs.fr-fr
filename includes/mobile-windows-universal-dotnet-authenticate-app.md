
1. Ouvrez le fichier projet partagé MainPage.cs et ajoutez l'extrait de code suivant à la classe MainPage :
   
        // Define a member variable for storing the signed-in user. 
        private MobileServiceUser user;
   
        // Define a method that performs the authentication process
        // using a Facebook sign-in. 
        private async System.Threading.Tasks.Task<bool> AuthenticateAsync()
        {
            string message;
            bool success = false;
            try
            {
                // Change 'MobileService' to the name of your MobileServiceClient instance.
                // Sign-in using Facebook authentication.
                user = await App.MobileService
                    .LoginAsync(MobileServiceAuthenticationProvider.Facebook);
                message =
                    string.Format("You are now signed in - {0}", user.UserId);
   
                success = true;
            }
            catch (InvalidOperationException)
            {
                message = "You must log in. Login Required";
            }
   
            var dialog = new MessageDialog(message);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
            return success;
        }
   
    Ce code authentifie l’utilisateur à l’aide d’une connexion Facebook. Si vous utilisez un fournisseur d'identité différent de Facebook, remplacez la valeur de **MobileServiceAuthenticationProvider** ci-dessus par la valeur de votre fournisseur.
2. Commentez ou supprimer l’appel de la méthode **RefreshTodoItems** dans la méthode à substituer **OnNavigatedTo** existante.
   
    Cela empêche les données d'être chargées avant que l'utilisateur ne soit authentifié. Ensuite, vous allez ajouter à l’application un bouton **Connexion** qui déclenche l’authentification.
3. Ajoutez l'extrait de code suivant à la classe MainPage :
   
        private async void ButtonLogin_Click(object sender, RoutedEventArgs e)
        {
            // Login the user and then load data from the mobile app.
            if (await AuthenticateAsync())
            {
                // Hide the login button and load items from the mobile app.
                ButtonLogin.Visibility = Windows.UI.Xaml.Visibility.Collapsed;
                //await InitLocalStoreAsync(); //offline sync support.
                await RefreshTodoItems();
            }
        }
4. Dans le projet d’application Windows Store, ouvrez le fichier de projet MainPage.xaml, puis ajoutez l’élément **Bouton** suivant juste avant l’élément définissant le bouton **Enregistrer** :
   
        <Button Name="ButtonLogin" Click="ButtonLogin_Click" 
                        Visibility="Visible">Sign in</Button>
5. Dans le projet d’application Windows Phone Store, ajoutez l’élément **Button** suivant dans le **ContentPanel**, après l’élément **TextBox** :
   
        <Button Grid.Row ="1" Grid.Column="1" Name="ButtonLogin" Click="ButtonLogin_Click" 
            Margin="10, 0, 0, 0" Visibility="Visible">Sign in</Button>
6. Ouvrez le fichier projet App.xaml.cs partagé et ajoutez le code suivant :
   
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
7. Appuyez sur la touche F5 pour exécuter l'application Windows Store, puis cliquez sur le bouton **Se connecter** pour vous connecter à l'application avec le fournisseur d'identité choisi. 
   
       When you are successfully logged-in, the app should run without errors, and you should be able to query your backend and make updates to data.
8. Cliquez avec le bouton droit sur le projet d'application Windows Phone Store, cliquez sur **Définir comme projet de démarrage**, puis répétez l'étape précédente pour vérifier que l'application Windows Phone Store s'exécute correctement.  



<!--HONumber=Jan17_HO3-->


