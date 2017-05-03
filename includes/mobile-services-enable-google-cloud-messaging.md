
1. Accédez à la [Console Google Cloud](https://console.developers.google.com/project)et connectez-vous avec les informations d’identification de votre compte Google. 
2. Cliquez sur **Créer un projet**, saisissez un nom de projet, puis cliquez sur **Créer**. Si nécessaire, procédez à la vérification par SMS, puis cliquez de nouveau sur **Create** .
   
    ![Création d’un projet](./media/mobile-services-enable-google-cloud-messaging/mobile-services-google-new-project.png)   
   
     Saisissez votre nouveau **Nom du projet**, et cliquez sur **Créer un projet**.
3. Cliquez sur le bouton **Utilitaires et plus** puis sur **Informations sur le projet**. Notez le **numéro de projet**. Vous devez définir cette valeur en tant que variable `SenderId` dans l'application cliente.
   
    ![Utilitaires et bien plus encore](./media/mobile-services-enable-google-cloud-messaging/notification-hubs-utilities-and-more.png)
4. Dans le tableau de bord du projet, cliquez sur **API Mobile**, sur **Google Cloud Messaging**, puis, à la page suivante, cliquez sur **Activer** et acceptez les conditions du service. 
   
    ![Activation de GCM](./media/mobile-services-enable-google-cloud-messaging/enable-GCM.png)
   
    ![Activation de GCM](./media/mobile-services-enable-google-cloud-messaging/enable-gcm-2.png) 
5. Dans le tableau de bord du projet, cliquez sur **Informations d’identification** > **Créer des informations d’identification** > **Clé API**. 
   
    ![](./media/mobile-services-enable-google-cloud-messaging/mobile-services-google-create-server-key.png)
6. Dans **Créer une nouvelle clé**, cliquez sur **Clé serveur**,saisissez un nom pour votre clé, puis cliquez sur **Créer**.
7. Prenez note de la valeur de **API KEY** .
   
    Cette valeur de clé API vous servira à activer Azure pour l’authentification avec GCM et l’envoi des notifications Push au nom de votre application.

