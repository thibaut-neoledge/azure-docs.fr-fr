
Ensuite, vous devez changer la façon dont les notifications Push sont inscrites pour vous assurer que l'utilisateur est authentifié avant de tenter une inscription. L'application cliente se met à jour selon la façon dont vous avez implémenté les notifications Push.

###Utilisation de l'Assistant Ajout de notification Push dans Visual Studio 2013 Update 2 ou ultérieur

Dans cette méthode, l'Assistant a généré un nouveau fichier push.register.cs dans votre projet.

1. Dans Visual Studio, dans l'Explorateur de solutions, ouvrez le fichier de projet app.xaml.cs et, dans le gestionnaire d'événements **OnLaunched**, placez en commentaire ou supprimez l'appel à la méthode **UploadChannel**. 

2. Ouvrez le fichier de projet push.register.cs et remplacez la méthode **UploadChannel** par le code suivant :

		public async static void UploadChannel()
		{
		    var channel = 
		        await Windows.Networking.PushNotifications.PushNotificationChannelManager
		        .CreatePushNotificationChannelForApplicationAsync();
		
		    try
		    {
		        // Create a native push notification registration.
		        await App.MobileService.GetPush().RegisterNativeAsync(channel.Uri);		        
		
		    }
		    catch (Exception exception)
		    {
		        HandleRegisterException(exception);
		    }
		}

	Ceci garantit que l'inscription est effectuée à l'aide de la même instance du client que celle qui a les informations d'identification de l'utilisateur authentifié. Si ce n'est pas le cas, l'inscription échouera avec une erreur Non autorisé (401).

3. Ouvrez le fichier de projet MainPage.xaml.cs et remplacez la méthode **OnNavigatedTo** par le code suivant :

	    protected override async void OnNavigatedTo(NavigationEventArgs e)
        {
            await AuthenticateAsync();            
            todolistPush.UploadChannel();
            RefreshTodoItems();
        }

	Dans ce code, vous devez remplacer le nom de classe push généré (`todolistPush`) par le nom de classe généré par l'Assistant, généralement au format <code><em>mobile_service</em>Push</code>.

###Notifications Push activées manuellement		

Dans cette méthode, vous avez ajouté du code pour l'inscription provenant du didacticiel directement dans le fichier de projet app.xaml.cs.

1. Dans Visual Studio, dans l'Explorateur de solutions, ouvrez le fichier de projet app.xaml.cs et, dans le gestionnaire d'événements **OnLaunched**, placez en commentaire ou supprimez l'appel à **InitNotificationsAsync**. 
 
2. Changez l'accessibilité de la méthode **InitNotificationsAsync** de `private` en `public` et ajoutez le modificateur `static`.

3. Ouvrez le fichier de projet MainPage.xaml.cs et remplacez la méthode **OnNavigatedTo** par le code suivant :

	    protected override async void OnNavigatedTo(NavigationEventArgs e)
        {
            await AuthenticateAsync();            
            App.InitNotificationsAsync();
            RefreshTodoItems();
        }
<!--HONumber=54-->