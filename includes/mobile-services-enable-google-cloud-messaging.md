
1. Accédez à la [Console Google Cloud](https://console.developers.google.com/project) et connectez-vous avec les informations d’identification de votre compte Google. 
 
2. Cliquez sur **Créer un projet**, saisissez un nom de projet, puis cliquez sur **Créer**. Si nécessaire, procédez à la vérification par SMS, puis cliquez de nouveau sur **Create**.

   	![](./media/mobile-services-enable-google-cloud-messaging/mobile-services-google-new-project.png)

	 Saisissez votre nouveau **Nom du projet**, et cliquez sur **Créer un projet**.

3. Notez le numéro du projet dans la section **Projects**. Vous devez définir cette valeur en tant que variable *PROJECT\_ID* dans le client.

4. Dans le tableau de bord du projet, cliquez sur **Utiliser les API Google** > **Messagerie Cloud pour Android**, puis, sur la page suivante, cliquez sur **Activer l’API**.

5. Dans le gestionnaire d’API, cliquez sur **Informations d’identification** > **Ajouter des informations d’identification** > **Clé API**.

   	![](./media/mobile-services-enable-google-cloud-messaging/mobile-services-google-create-server-key.png)

6. Dans **Créer une nouvelle clé**, cliquez sur **Clé serveur**,saisissez un nom pour votre clé, puis cliquez sur **Créer**.

7. Prenez note de la valeur de **API KEY**.

	Cette valeur de clé API vous servira à activer Azure pour l’authentification avec GCM et l’envoi des notifications Push au nom de votre application.

<!---HONumber=AcomDC_1203_2015-->