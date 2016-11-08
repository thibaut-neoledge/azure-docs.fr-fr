

1. Connectez-vous à la [console Firebase](https://firebase.google.com/console/). Créer un nouveau projet Firebase si vous n’en avez pas encore.
2. Après avoir créé votre projet, cliquez sur **Add Firebase to your Android app** (Ajouter Firebase à votre application Android) et suivez les instructions fournies.
   
    ![](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-add-firebase-to-android-app.png)
3. Dans la Console Firebase, cliquez sur la roue dentée pour votre projet, puis sur **Paramètres du projet**.
   
    ![](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-firebase-console-project-settings.png)
4. Cliquez sur l’onglet **Cloud Messaging** dans les paramètres de votre projet et copiez la valeur de **Server key** et **Sender ID**. Ces valeurs seront utilisées ultérieurement pour configurer la stratégie d’accès du hub de notification et votre gestionnaire de notification dans l’application.

<!---HONumber=AcomDC_0706_2016-->