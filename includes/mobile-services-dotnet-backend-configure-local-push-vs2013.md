
Vous pouvez éventuellement tester les notifications push en exécutant votre service mobile sur la machine virtuelle ou l'ordinateur local avant de le publier sur Azure. Pour ce faire, vous devez définir les informations relatives au concentrateur de notification utilisé par votre application dans le fichier web.config. Ces informations sont uniquement utilisées lors d'une exécution locale pour la connexion au concentrateur de notification ; elles sont ignorées lors de la publication sur Azure.

1. Ouvrez le fichier readme.html dans le dossier de projet de l'application sur Windows ou WindowsPhone. 

	Cela affiche la page **Push setup is almost complete** si elle n'est pas déjà ouverte. La section **Step 3: Modify Web Config** contient les informations de connexion au concentrateur de notification.

2. Dans Visual Studio, accédez à votre projet de service mobile, ouvrez le fichier Web.config du service et dans **connectionStrings**, ajoutez la chaîne de connexion **MS_NotificationHubConnectionString** de la page **Push setup is almost complete**.

3. Dans **appSettings**, remplacez la valeur du paramètre d'application **MS_NotificationHubName** par le nom du concentrateur de notification de la page **Push setup is almost complete**.

4. Cliquez avec le bouton droit sur le projet de service mobile, puis cliquez sur **Déboguer**, puis sur **Démarrer une nouvelle instance**, puis notez l'URL racine du service de la page de démarrage affichée dans le navigateur.

	Il s'agit de l'URL de l'hôte local pour le projet du backend .NET. Cette URL sera utilisée pour tester l'application avec le service mobile exécuté sur l'ordinateur local.

Le projet de service mobile est désormais configuré pour se connecter au concentrateur de notification Azure lorsqu'il est exécuté localement. Notez qu'il est impératif d'utiliser le même nom de concentrateur de notification et la même chaîne de connexion que sur le portail, car ces paramètres de projet situés dans le fichier Web.config sont remplacés par les paramètres du portail lors d'une exécution sous Azure.
<!--HONumber=54-->