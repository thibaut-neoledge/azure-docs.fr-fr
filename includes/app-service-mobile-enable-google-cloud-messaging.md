1. Accédez à [Google Cloud Console](https://console.developers.google.com/project), connectez-vous avec vos informations d’identification de compte Google, puis cliquez sur **Select a project**, puis sur **Create a project**.
2. Entrez un nom de projet, acceptez les conditions de service et cliquez sur **Create**. Si nécessaire, procédez à la vérification par SMS, puis cliquez de nouveau sur **Create**.
3. Notez le numéro du projet dans la section **Projects**.
   
    Plus loin dans le didacticiel, vous allez définir cette valeur en tant que variable PROJECT\_ID dans le client.
4. Cliquez sur **Enable and manage APIs** sous **User Google APIs**, puis sur **Cloud Messaging for Android**. Sur la page suivante, cliquez sur **Enable API**.
5. Cliquez sur **Credentials**, puis sur **Add Credential**->**API Key**
6. Dans **Create a new key**, cliquez sur **Server key**. Dans la fenêtre suivante, cliquez sur **Create**.
7. Prenez note de la valeur de **API KEY**.
   
    Cette valeur de clé API vous servira à activer Azure pour l’authentification avec GCM et l’envoi des notifications Push au nom de votre application.

<!---HONumber=AcomDC_1203_2015-->