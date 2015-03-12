
1. Accédez au site web <a href="http://cloud.google.com/console" target="_blank">de la console cloud Google,</a> connectez-vous avec les informations d'identification de votre compte Google, puis cliquez sur **Create Projet**.

   	![](./media/notification-hubs-android-get-started/mobile-services-google-new-project.png)   

	>[AZURE.NOTE]Si vous disposez d'un projet existant, vous êtes dirigé vers la page <strong>Projects</strong> après la connexion. Pour créer un nouveau projet à partir du tableau de bord, développez <strong>API Project</strong>, cliquez sur <strong>Create...</strong> sous <strong>Other Projects</strong>, puis entrez un nom de projet et cliquez sur <strong>Create Project</strong>.

2. Entrez un nom de projet, acceptez les conditions de service et cliquez sur **Create**. Si nécessaire, procédez à la vérification par SMS, puis cliquez de nouveau sur **Create**.

3. Notez le numéro du projet dans la section **Projects**. 

	Plus loin dans le didacticiel, vous allez définir cette valeur en tant que variable PROJECT_ID dans le client.

4. Dans la colonne de gauche, développez **APIs & auth**, cliquez sur **API**, faites défiler la page vers le bas et cliquez sur le bouton bascule pour activer **Google Cloud Messaging for Android**, puis acceptez les conditions de service. 

	![](./media/notification-hubs-android-get-started/mobile-services-google-enable-GCM.png)

5. Cliquez sur **Credentials**, puis sur **Create new Key** 

   	![](./media/notification-hubs-android-get-started/mobile-services-google-create-server-key.png)

6. Dans **Create a new key**, cliquez sur **Server key**. Dans la fenêtre suivante, cliquez sur **Create**.

   	![](./media/notification-hubs-android-get-started/mobile-services-google-create-server-key2.png)

7. Notez la valeur de **API KEY**.

   	![](./media/notification-hubs-android-get-started/mobile-services-google-create-server-key3.png) 

	Vous allez utiliser cette valeur de clé API afin d'activer Mobile Services pour l'authentification avec GCM et l'envoi des notifications Push au nom de votre application.


<!--HONumber=45--> 
