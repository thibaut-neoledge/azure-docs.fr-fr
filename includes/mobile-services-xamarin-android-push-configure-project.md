
1. Dans l'affichage de solutions, développez le dossier **Composants** de l'application Xamarin.Android et assurez-vous que le package Azure Mobile Services est installé. 

2. Cliquez avec le bouton droit sur le dossier **Composants**, cliquez sur **Obtenir d'autres composants**, recherchez le composant **Client de messagerie Cloud Google** composant et l'ajouter au projet.

1. Ouvrez le fichier projet ToDoActivity.css et ajoutez l'instruction using suivante à la classe :

		using Gcm.Client;

2. Dans la classe **ToDoActivity**, ajoutez le nouveau code suivant :

        // Create a new instance field for this activity.
        static ToDoActivity instance = new ToDoActivity();

        // Return the current activity instance.
        public static ToDoActivity CurrentActivity
        {
            get
            {
                return instance;
            }
        }
        // Return the Mobile Services client.
        public MobileServiceClient CurrentClient
        {
            get
            {
                return client;
            }
        }

	Vous pouvez ainsi accéder à l'instance du client Mobile Services depuis le processus de service.

3. Modifiez la déclaration existante du client Mobile Services en public, comme suit :

		public MobileServiceClient client { get; private set; }

4.	Ajoutez le code suivant à la méthode **OnCreate** après la création de **MobileServiceClient** :

        // Set the current instance of TodoActivity.
        instance = this;

        // Make sure the GCM client is set up correctly.
        GcmClient.CheckDevice(this);
        GcmClient.CheckManifest(this);

        // Register the app for push notifications.
        GcmClient.Register(this, ToDoBroadcastReceiver.senderIDs);

Votre **ToDoActivity** est maintenant prêt pour l'ajout de notifications push.
<!--HONumber=54-->