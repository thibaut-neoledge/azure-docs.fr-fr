
1. Ouvrez le fichier projet MainPage.xaml.cs et ajoutez l'instruction using suivante :

        using Windows.UI.Popups;

2. Ajoutez l'extrait de code suivant à la classe MainPage :
	
        private MobileServiceUser user;
        private async System.Threading.Tasks.Task AuthenticateAsync()
        {
            while (user == null)
            {
                string message;
                try
                {
                    user = await App.MobileService
                        .LoginAsync(MobileServiceAuthenticationProvider.Facebook);
                    message = 
                        string.Format("You are now logged in - {0}", user.UserId);
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

    Cela crée une variable membre pour le stockage de l'utilisateur actuel et une méthode pour gérer le processus d'authentification. L'utilisateur est authentifié à l'aide d'une connexion Facebook. Si vous utilisez un fournisseur d'identité autre que Facebook, remplacez la valeur de **MobileServiceAuthenticationProvider** ci-dessus par la valeur de votre fournisseur.

3. Remplacez la méthode **OnNavigatedTo** à substituer par la méthode suivante qui appelle la nouvelle méthode **Authenticate** :

        protected override async void OnNavigatedTo(NavigationEventArgs e)
        {
            await AuthenticateAsync();
            RefreshTodoItems();
        }
		
4. Appuyez sur la touche F5 pour exécuter l'application et vous connecter à l'application avec le fournisseur d'identité choisi.

   	Lorsque vous êtes connecté, l'application doit s'exécuter sans erreur et vous devez pouvoir exécuter des requêtes Mobile Services et mettre à jour les données.

<!---HONumber=July15_HO2-->