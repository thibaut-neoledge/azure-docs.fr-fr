
Revenir à Visual Studio et sélectionner le projet d'application cliente de code partagé (nommé ainsi " <nom de votre application>.Shared ")

1. Développez le fichier **App.xaml**, puis ouvrez le fichier **App.xaml.cs**. Recherchez la déclaration du membre MobileService, qui est initialisé avec une URL d'hôte local. Placez cette déclaration en commentaire (à l'aide de `CTRL+K,CTRL+C`) et supprimez les marques de commentaire de la déclaration (" CTRL+K,CTRL+U ") qui est connectée à votre service hébergé :

        // This MobileServiceClient has been configured to communicate with your local
        // test project for debugging purposes.
        //public static MobileServiceClient MobileService = new MobileServiceClient(
        //    "http://localhost:58454"
        //);

        // This MobileServiceClient has been configured to communicate with the Azure Mobile Service and
        // Azure Gateway using the application key. You're all set to start working with your Mobile Service!
        public static MobileServiceClient MobileService = new MobileServiceClient(
            "https://mymobileapp-code.azurewebsites.net",
            "https://myresourcegroupgateway.azurewebsites.net/Microsoft.Azure.AppService.ApiApps.Gateway",
            "XXXX-APPLICATION-KEY-XXXXX"
        );

2. Appuyez sur la touche F5 pour régénérer le projet et démarrer l'application du Windows Store, qui doit correspondre à votre projet de démarrage par défaut.

2. Dans l'application, tapez un texte explicite, par exemple *Complete the tutorial*, dans la zone de texte **Insert a TodoItem**, puis cliquez sur **Enregistrer**.

	![](./media/app-service-mobile-windows-universal-test-app-preview/mobile-quickstart-startup.png)

	Ceci envoie une requête POST vers le nouveau serveur principal d'applications mobiles hébergé dans Azure.

3. Arrêtez le débogage, changez le projet de démarrage par défaut de la solution Windows universelle en application du Windows Phone Store (cliquez avec le bouton droit sur le projet " <nom de votre application>.WindowsPhone " et cliquez sur **Définir comme projet de démarrage**), puis appuyez de nouveau sur F5.

	![](./media/app-service-mobile-windows-universal-test-app-preview/mobile-quickstart-completed-wp8.png)

	Notez que les données enregistrées à l'étape précédente sont chargées à partir de l'application mobile après le démarrage de l'application Windows.


<!--HONumber=49-->