1.  Accédez au site web [Google Cloud Console][Google Cloud Console], connectez-vous avec vos informations d'identification de compte Google, puis cliquez sur **CREATE PROJECT**.

    ![][0]

	> [WACOM.NOTE] Si vous disposez déjà d'un projet en cours, vous êtes redirigé vers la page **Projets** une fois la connexion établie. Pour créer un projet depuis le tableau de bord, développez **API Project**, cliquez sur **Create...** sous **Other projects**, puis entrez un nom de projet et cliquez sur **Create project**.

2.  Entrez un nom de projet, acceptez les conditions de service et cliquez sur **Create**. Procédez à la vérification de SMS demandée et cliquez à nouveau sur **Create**.

3.  Notez le numéro du projet dans la section **Projects**.

	Plus loin dans le didacticiel, vous allez définir cette valeur en tant que variable PROJECT\_ID dans le client.

4.  Dans la colonne de gauche, cliquez sur **APIs & auth**, puis faites défiler la page et cliquez sur la touche bascule pour activer **Google Cloud Messaging for Android** et accepter les conditions de service.

	![][1]

5.  Cliquez sur **Credentials**, puis sur **CREATE NEW KEY**

    ![][2]

6.  Dans **Create a new key**, cliquez sur **Server key**. Dans la fenêtre suivante, cliquez sur **Create**.

    ![][3]

7.  Prenez note de la valeur de **API key**.

    ![][4]

    Vous allez utiliser cette valeur de clé API afin d'activer Mobile Services pour l'authentification avec GCM et l'envoi des notifications push au nom de votre application.

  [Google Cloud Console]: http://cloud.google.com/console
  [0]: ./media/notification-hubs-android-get-started/mobile-services-google-new-project.png
  [1]: ./media/notification-hubs-android-get-started/mobile-services-google-enable-GCM.png
  [2]: ./media/notification-hubs-android-get-started/mobile-services-google-create-server-key.png
  [3]: ./media/notification-hubs-android-get-started/mobile-services-google-create-server-key2.png
  [4]: ./media/notification-hubs-android-get-started/mobile-services-google-create-server-key3.png
