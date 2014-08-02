

**Remarque**

Pour effectuer la procédure de cette rubrique, vous devez disposer d'un compte Google avec une adresse de messagerie vérifiée. Pour créer un compte Google, accédez à [accounts.google.com](http://go.microsoft.com/fwlink/p/?LinkId=268302).

1.  Accédez au site Web [Google Cloud Console](http://cloud.google.com/console), connectez-vous avec vos informations d'identification de compte Google, puis cliquez sur **CREATE PROJECT**.

	![](./media/notification-hubs-android-get-started/mobile-services-google-new-project.png)   

    **Remarque**

    Si vous disposez déjà d'un projet en cours, vous êtes redirigé vers la page **Projects** une fois la connexion établie. Pour créer un projet depuis le tableau de bord, développez **API Project**, cliquez sur **Create...** sous **Other projects**, puis entrez un nom de projet et cliquez sur **Create project**.

2.  Entrez un nom de projet, acceptez les conditions de service et cliquez sur **Create**. Procédez à la vérification de SMS demandée et cliquez à nouveau sur **Create**.

3.  Notez le numéro du projet dans la section **Projects**.

    Plus loin dans le didacticiel, vous allez définir cette valeur en tant que variable PROJECT\_ID dans le client.

4.  Dans la colonne de gauche, cliquez sur **APIs & auth**, puis faites défiler la page et cliquez sur la touche bascule pour activer **Google Cloud Messaging for Android** et accepter les conditions de service.

    ![](./media/notification-hubs-android-get-started/mobile-services-google-enable-GCM.png)

5.  Cliquez sur **Credentials**, puis sur **CREATE NEW KEY**

	![](./media/notification-hubs-android-get-started/mobile-services-google-create-server-key.png)

6.  Dans **Create a new key**, cliquez sur **Server key**. Dans la fenêtre suivante, cliquez sur **Create**.

	![](./media/notification-hubs-android-get-started/mobile-services-google-create-server-key2.png)

7.  Prenez note de la valeur de **API key**.

	![](./media/notification-hubs-android-get-started/mobile-services-google-create-server-key3.png) 


