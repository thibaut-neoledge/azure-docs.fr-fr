

>[AZURE.NOTE]Pour effectuer cette procédure, vous devez disposer d’un compte Google avec une adresse de messagerie valide. Pour créer un compte Google, accédez à <a href="http://go.microsoft.com/fwlink/p/?LinkId=268302" target="_blank">accounts.google.com</a>.


1. Accédez au site web <a href="http://cloud.google.com/console" target="_blank">Google Cloud Console</a>, connectez-vous avec vos informations d'identification de compte Google, puis cliquez sur **Create Project**.

   	![](./media/notification-hubs-android-get-started/mobile-services-google-new-project.png)

	>[AZURE.NOTE]Si vous disposez déjà d'un projet en cours, vous êtes redirigé vers la page <strong>Projects</strong> une fois la connexion établie. Pour créer un projet depuis le tableau de bord, développez <strong>API Project</strong>, cliquez sur <strong>Create...</strong> sous <strong>Other projects</strong>, puis entrez un nom de projet et cliquez sur <strong>Create project</strong>.

2. Entrez un nom de projet, acceptez les conditions de service et cliquez sur **Create**. Si nécessaire, procédez à la vérification par SMS, puis cliquez de nouveau sur **Create**.

3. Notez le numéro du projet dans la section **Projects**.

	Plus loin dans le didacticiel, vous allez définir cette valeur en tant que variable PROJECT_ID dans le client.

4. Dans la colonne de gauche, développez **APIs & auth**, cliquez sur **APIs**, faites défiler la page vers le bas et cliquez sur le bouton bascule pour activer **Google Cloud Messaging for Android**, puis acceptez les conditions de service.

	![](./media/notification-hubs-android-get-started/mobile-services-google-enable-GCM.png)

5. Cliquez sur **Credentials**, puis sur **Create new Key**

   	![](./media/notification-hubs-android-get-started/mobile-services-google-create-server-key.png)

6. Dans **Create a new key**, cliquez sur **Server key**. Dans la fenêtre suivante, cliquez sur **Create**.

   	![](./media/notification-hubs-android-get-started/mobile-services-google-create-server-key2.png)

7. Prenez note de la valeur de **API KEY**.

   	![](./media/notification-hubs-android-get-started/mobile-services-google-create-server-key3.png)

	Cette valeur de clé API vous servira à activer Azure pour l’authentification avec GCM et l’envoi des notifications Push au nom de votre application.

<!---HONumber=July15_HO4-->