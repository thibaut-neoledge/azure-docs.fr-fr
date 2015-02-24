


1. Accédez au site web <a href="http://cloud.google.com/console" target="_blank">Google Cloud Console</a>, connectez-vous avec vos informations d'identification de compte Google, puis cliquez sur **Create Project**.

   	![](./media/notification-hubs-android-get-started/mobile-services-google-new-project.png)   

	>[AZURE.NOTE]Si vous disposez déjà d'un projet en cours, vous êtes redirigé vers la page <strong>Projects</strong> une fois la connexion établie. Pour créer un projet depuis le tableau de bord, développez <strong>API Project</strong>, cliquez sur <strong>Create...</strong> sous <strong>Other projects</strong>, puis entrez un nom de projet et cliquez sur <strong>Create project</strong>.

2. Entrez un nom de projet, acceptez les conditions de service et cliquez sur **Create**. Si nécessaire, procédez à la vérification par SMS, puis cliquez sur **Create** à nouveau.

3. Notez le numéro du projet dans la section **Projects**. 

	Plus loin dans le didacticiel, vous allez définir cette valeur en tant que variable PROJECT_ID dans le client.

4. Dans la colonne de gauche, cliquez sur **APIs & auth**, puis faites défiler la page et cliquez sur la touche bascule pour activer **Google Cloud Messaging for Android** et accepter les conditions de service. 

	![](./media/notification-hubs-android-get-started/mobile-services-google-enable-GCM.png)

5. Cliquez sur **Credentials**, puis sur **Create new Key** 

   	![](./media/notification-hubs-android-get-started/mobile-services-google-create-server-key.png)

6. Dans **Create a new key**, cliquez sur **Server key**. Dans la fenêtre suivante, cliquez sur **Create**.

   	![](./media/notification-hubs-android-get-started/mobile-services-google-create-server-key2.png)

7. Notez la valeur de **API KEY**.

   	![](./media/notification-hubs-android-get-started/mobile-services-google-create-server-key3.png) 

	Vous allez utiliser cette valeur de clé API afin d'activer Mobile Services pour l'authentification avec GCM et l'envoi des notifications Push au nom de votre application.

<!--HONumber=41-->
