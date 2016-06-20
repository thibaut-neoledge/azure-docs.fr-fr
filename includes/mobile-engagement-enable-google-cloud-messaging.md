
###Créer un projet Google Cloud Messaging avec une clé API

>[AZURE.NOTE] Pour effectuer cette procédure, vous devez disposer d’un compte Google avec une adresse de messagerie valide. Pour créer un compte Google, accédez à <a href="http://go.microsoft.com/fwlink/p/?LinkId=268302" target="_blank">accounts.google.com</a>.

1. Accédez à la [Console Google Cloud](https://console.developers.google.com/project) et connectez-vous avec les informations d’identification de votre compte Google.

2. Cliquez sur **Atteindre le projet**, puis cliquez sur **Créer un projet**.
<!--
   	![](./media/mobile-engagement-enable-google-cloud-messaging/new-project.png)

   	![](./media/mobile-engagement-enable-google-cloud-messaging/new-project-2.png)   
-->
3. Entrez un nom de projet.

4. Prenez note du numéro de projet qui apparaît sous la zone de texte **Nom du projet**. Vous en aurez besoin plus loin dans le didacticiel pour l’insérer dans le fichier manifeste Android. ![](./media/mobile-engagement-enable-google-cloud-messaging/project-number.png)
5. Cliquez sur **Create**.

6. Dans la colonne de gauche, assurez-vous que **Présentation** est sélectionné puis, sous API Mobiles, cliquez sur **Google Cloud Messaging**. Sur la page suivante, cliquez sur **Activer**.

	![](./media/mobile-engagement-enable-google-cloud-messaging/enable-GCM.png)
<!--
	![](./media/mobile-engagement-enable-google-cloud-messaging/enable-gcm-2.png)
-->
7. Sur la page suivante, cliquez sur **Accéder aux informations d’identification** et, sur la page suivante, sélectionnez **Google Cloud Messaging** dans la première liste déroulante et **Serveur web** dans l’autre, puis cliquez sur **De quelles informations d’identification ai-je besoin ?**

   	![](./media/mobile-engagement-enable-google-cloud-messaging/create-server-key.png)

8. Dans **Ajouter des informations d’identification à votre projet**, cliquez sur **Créer une clé API**.

   	![](./media/mobile-engagement-enable-google-cloud-messaging/create-server-key5.png)
<!--
   	![](./media/mobile-engagement-enable-google-cloud-messaging/create-server-key6.png)
-->
9. Prenez note de la valeur de **API KEY**. Vous allez utiliser cette valeur de clé API ultérieurement pour configurer dans la section « Push Natif ». Maintenant, cliquez sur **Terminé**.

<!---HONumber=AcomDC_0608_2016-->