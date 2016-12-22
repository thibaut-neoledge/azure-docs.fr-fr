
1. Dans le [Portail Azure](https://portal.azure.com/), cliquez sur **Parcourir tout** > **App Services**, puis cliquez sur votre serveur principal Mobile Apps. Sous **Paramètres**, cliquez sur **App Service Push (Notification Push App Service)**, puis sur le nom de votre hub de notification.
2. Accédez à **Google (GCM)**, entrez la valeur de **clé serveur** obtenue auprès de Firebase au cours de la procédure précédente, puis cliquez sur **Enregistrer**.

    ![Définition de la clé API GCM dans le portail](./media/app-service-mobile-android-configure-push/mobile-push-api-key.png)

Le serveur principal Mobile Apps est désormais configuré pour utiliser Firebase Cloud Messaging. Ceci vous permet d’envoyer des notifications Push à votre application en cours d’exécution sur un appareil Android à l’aide du hub de notification.

<!-- URLs. -->


<!-- images -->


<!--HONumber=Dec16_HO2-->


