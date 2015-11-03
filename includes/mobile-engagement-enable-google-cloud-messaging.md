>[AZURE.NOTE]Pour effectuer cette procédure, vous devez disposer d’un compte Google avec une adresse de messagerie valide. Pour créer un compte Google, accédez à <a href="http://go.microsoft.com/fwlink/p/?LinkId=268302" target="_blank">accounts.google.com</a>.

1. Accédez à [Google Cloud Console](https://console.developers.google.com/project), connectez-vous avec vos informations d’identification de compte Google, puis cliquez sur **Create Project**.

   	![](./media/mobile-engagement-enable-google-cloud-messaging/new-project.png)

   	![](./media/mobile-engagement-enable-google-cloud-messaging/new-project-2.png)

2. Entrez un nom de projet, acceptez les conditions de service et cliquez sur **Create**. Si nécessaire, procédez à la vérification par SMS, puis cliquez de nouveau sur **Create**.

3. Notez le numéro du projet dans la section **Projects**. Vous en aurez besoin plus loin dans le didacticiel pour l’insérer dans le fichier manifeste Android.

   	![](./media/mobile-engagement-enable-google-cloud-messaging/project-number.png)

4. Dans la colonne de gauche, développez **APIs & auth**, cliquez sur **APIs**, faites défiler la page vers le bas et cliquez sur **Google Cloud Messaging for Android**. Dans la page suivante, cliquez sur **Enable API** et acceptez les conditions de service.

	![](./media/mobile-engagement-enable-google-cloud-messaging/enable-GCM.png)

	![](./media/mobile-engagement-enable-google-cloud-messaging/enable-gcm-2.png)

5. Cliquez sur **Informations d’identification**, puis sur **Ajouter des informations d’identification**->**Clé API**.

   	![](./media/mobile-engagement-enable-google-cloud-messaging/create-server-key.png)

6. Dans **Create a new key**, cliquez sur **Server key**. Dans la fenêtre suivante, cliquez sur **Create**.

   	![](./media/mobile-engagement-enable-google-cloud-messaging/create-server-key5.png)


   	![](./media/mobile-engagement-enable-google-cloud-messaging/create-server-key6.png)

7. Prenez note de la valeur de **API KEY**. Vous allez utiliser cette valeur de clé API ultérieurement pour configurer dans la section « Push Natif ».

<!---HONumber=Nov15_HO1-->