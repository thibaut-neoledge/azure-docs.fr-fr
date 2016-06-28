
###Créer un projet Google Cloud Messaging avec une clé API

>[AZURE.NOTE] Pour effectuer cette procédure, vous devez disposer d’un compte Google avec une adresse de messagerie valide. Pour créer un compte Google, accédez à <a href="http://go.microsoft.com/fwlink/p/?LinkId=268302" target="_blank">accounts.google.com</a>.

1. Accédez à la [Console Google Cloud](https://console.developers.google.com/project) et connectez-vous avec les informations d’identification de votre compte Google.

2. Accédez à **All Projects (Tous les projets)**, puis cliquez sur **Create Project (Créer un projet)**.

3. Entrez un **Project name (Nom de projet)**, puis cliquez sur **Create (Créer)**.

4. Une fois le projet créé, veillez à noter le **Project number (Numéro de projet)** qui est une valeur numérique longue. Vous aurez besoin ultérieurement de ce numéro qui se trouve dans la section **IAM & Admin (IAM et administration)** des **Settings (Paramètres)** de votre projet.
 
	![](./media/mobile-engagement-enable-google-cloud-messaging/project-number.png)

5. Maintenant, nous allons créer une clé pour la plateforme de messagerie Google Cloud qui sera utilisée par notre plateforme pour envoyer des notifications aux appareils Android. Accédez à la section **API Manager (Gestionnaires des API)** et cliquez sur **Google Cloud Messaging** sous **Mobile APIs (API pour mobiles)**.

	![](./media/mobile-engagement-enable-google-cloud-messaging/gcm.png)

6. Dans la page suivante, cliquez sur le bouton **Activer**. Le tableau de bord vous invite à créer des informations d’identification. Pour ce faire, cliquez sur le bouton **Go to Credentials (Aller aux informations d’identification)**.

	![](./media/mobile-engagement-enable-google-cloud-messaging/enable-GCM.png)

6. Sélectionnez **Google Cloud Messaging** dans la première liste déroulante et **Serveur web** dans l’autre, puis cliquez sur **De quelles informations d’identification ai-je besoin ?**

   	![](./media/mobile-engagement-enable-google-cloud-messaging/create-server-key.png)

7. Dans **Ajouter des informations d’identification à votre projet**, cliquez sur **Créer une clé API**.

   	![](./media/mobile-engagement-enable-google-cloud-messaging/create-server-key5.png)

8. Prenez note de la valeur de **API KEY**. Vous allez utiliser cette valeur de clé API ultérieurement pour configurer dans la section « Push Natif ». Maintenant, cliquez sur **Terminé**.

<!---HONumber=AcomDC_0622_2016-->