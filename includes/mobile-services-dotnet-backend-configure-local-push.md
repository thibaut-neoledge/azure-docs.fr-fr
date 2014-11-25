Vous pouvez éventuellement tester les notifications push en exécutant votre service mobile sur la machine virtuelle ou l'ordinateur local avant de le publier sur Azure. Pour ce faire, vous devez définir les informations relatives au concentrateur de notification utilisé par votre application dans le fichier web.config. Ces informations sont uniquement utilisées lors d'une exécution locale pour la connexion au concentrateur de notification ; elles sont ignorées lors de la publication sur Azure.

1.  Dans l'onglet **Push** de votre service mobile, cliquez sur le lien **Concentrateur de notification**.

	![][0]

	Vous accédez alors au concentrateur de notification utilisé par votre service mobile.

2.  Sur la page du concentrateur de notification, notez le nom de votre concentrateur de notification, puis cliquez sur **Afficher la chaîne de connexion**.

	![][1]

3.  Dans **Accès aux informations de connexion**, copiez la chaîne de connexion **DefaultFullSharedAccessSignature**.

	![][2]

4.  Dans Visual Studio, accédez à votre projet de service mobile, ouvrez le fichier Web.config du service et dans **connectionStrings**, remplacez la chaîne de connexion **MS\_NotificationHubConnectionString** par la chaîne de connexion de l'étape précédente.

5.  Dans **appSettings**, remplacez la valeur du paramètre d'application **MS\_NotificationHubName** par le nom du concentrateur de notification.

Le projet de service mobile est désormais configuré pour se connecter au concentrateur de notification Azure lorsqu'il est exécuté localement. Notez qu'il est impératif d'utiliser le même nom de concentrateur de notification et la même chaîne de connexion que sur le portail car ces paramètres de projet Web.config sont remplacés par les paramètres du portail lors d'une exécution sous Azure.

  [0]: ./media/mobile-services-dotnet-backend-configure-local-push/link-to-notification-hub.png
  [1]: ./media/mobile-services-dotnet-backend-configure-local-push/notification-hub-page.png
  [2]: ./media/mobile-services-dotnet-backend-configure-local-push/notification-hub-connection-string.png
