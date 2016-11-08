1. Ouvrez le fichier projet mainpage.xaml.cs et ajoutez l'extrait de code suivant à la classe MainPage :
   
        private MobileServiceUser user;
        private async Task Authenticate()
        {
            while (user == null)
            {
                string message;
                try
                {
                    user = await App.MobileServiceDotNetClient.LoginAsync(MobileServiceAuthenticationProvider.Twitter);
                    message = string.Format("You are now logged in - {0}", user.UserId);
                }
                catch (InvalidOperationException)
                {
                    message = "You must log in. Login Required";
                }
   
                var dialog = new MessageDialog(message);
                await dialog.ShowAsync();
            }
        }
   
    Cela crée une variable membre pour le stockage de l'utilisateur actuel et une méthode pour gérer le processus d'authentification. L'utilisateur est authentifié à l'aide d'un nom d'utilisateur Twitter.
   
   > [!NOTE]
   > Si vous utilisez un fournisseur d'identité différent de Twitter, remplacez la valeur de <strong>MobileServiceAuthenticationProvider</strong> ci-dessus par la valeur de votre fournisseur.</p> </div>
   > 
   > 
2. Supprimez le remplacement de méthode **OnNavigatedTo** existant (ou convertissez-le en commentaires) et remplacez-le par la méthode suivante qui gère l'événement **Loaded** pour la page.
   
        async void MainPage_Loaded(object sender, RoutedEventArgs e)
        {
            await Authenticate();
            RefreshTodoItems();
        }
   
       Cette méthode appelle la nouvelle méthode **Authenticate**.
3. Remplacez le constructeur MainPage par le code suivant :
   
        // Constructor
        public MainPage()
        {
            InitializeComponent();
            this.Loaded += MainPage_Loaded;
        }
   
       Ce constructeur enregistre également le gestionnaire pour l'événement Loaded.
4. Appuyez sur la touche F5 pour exécuter l'application et vous connecter à l'application avec le fournisseur d'identité choisi.
   
       Lorsque vous êtes connecté, l'application doit s'exécuter sans erreur et vous devez pouvoir exécuter des requêtes Mobile Services et mettre à jour les données.

<!---HONumber=AcomDC_0211_2016-->