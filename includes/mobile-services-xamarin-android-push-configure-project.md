
1. Dans la vue Solution (ou dans l’**Explorateur de solutions** dans Visual Studio), cliquez avec le bouton droit sur le dossier **Components**, cliquez sur **Get More Components...**, recherchez le composant **Google Cloud Messaging Client**, puis ajoutez-le au projet.
2. Ouvrez le fichier projet ToDoActivity.css et ajoutez l'instruction using suivante à la classe :
   
        using Gcm.Client;
3. Dans la classe **ToDoActivity** , ajoutez le nouveau code suivant : 
   
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
   
    Vous pouvez ainsi accéder à l’instance du client mobile depuis le processus de service de gestionnaire push.
4. Ajoutez le code ci-après à la méthode **OnCreate** après la création de **MobileServiceClient** :
   
     // Définir l’instance actuelle de TodoActivity.
     instance = this;
   
     // Vérifier que le client GCM est correctement configuré.
     GcmClient.CheckDevice(this); GcmClient.CheckManifest(this);
   
     // Inscrire l’application pour les notifications Push.
     GcmClient.Register(this, ToDoBroadcastReceiver.senderIDs);

Votre **ToDoActivity** est maintenant prêt pour l'ajout de notifications push.



<!--HONumber=Nov16_HO3-->


