
Vous devez ensuite modifier la méthode d'enregistrement des notifications Push afin de vous assurer que l'utilisateur est bien authentifié avant toute tentative d'inscription.

1. Dans l'Explorateur de solutions de Visual Studio, ouvrez le fichier projet app.xaml.cs et supprimez (ou placez en commentaires) l'appel de la méthode **AcquirePushChannel** dans le gestionnaire d'événements **Application\_Launching**. 
 
2. Modifiez l'accessibilité de la méthode **AcquirePushChannel** de `private` en `public` et ajoutez le modificateur `static`.

3. Ouvrez le fichier de projet MainPage.xaml.cs et remplacez la méthode **OnNavigatedTo** par le code suivant :

	    protected override async void OnNavigatedTo(NavigationEventArgs e)
        {
            await AuthenticateAsync();            
            App.AcquirePushChannel();
            RefreshTodoItems();
        }

<!---HONumber=August15_HO6-->