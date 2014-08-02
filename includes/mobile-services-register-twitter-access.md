

Vous devez authentifier vos applications avant d'accéder aux ressources des API Twitter v1.1. Tout d'abord, vous devez obtenir les informations d'identification requises pour demander l'accès en utilisant OAuth 2.0. Ensuite, vous allez les stocker en toute sécurité dans les paramètres de l'application pour vos services mobiles.

1.  Si vous ne l'avez pas déjà fait, suivez la procédure de la rubrique [Inscription de vos applications pour la connexion à Twitter avec Mobile Services](/en-us/documentation/articles/mobile-services-how-to-register-twitter-authentication/).

	Twitter génère les informations d'identification requises pour vous permettre d'accéder aux API Twitter v1.1. Vous pouvez obtenir ces informations d'identification à partir du site Web Twitter Developers.

1.  Rendez-vous sur le site Web [Twitter Developers](http://go.microsoft.com/fwlink/p/?LinkId=268300), connectez-vous avec votre compte Twitter, accédez à **My Applications**, puis sélectionnez votre application Twitter.

    ![](./media/mobile-services-register-twitter-access/mobile-twitter-my-apps.png)

2.  Sous l'onglet **Détails** de l'application, notez les valeurs suivantes :

    -   **Consumer key**
    -   **Consumer secret**
    -   **Access token**
    -   **Access token secret**

    ![](./media/mobile-services-register-twitter-access/mobile-twitter-app-secrets.png)

3.  Connectez-vous au [portail de gestion Azure](https://manage.windowsazure.com/), cliquez sur **Services mobiles**, puis cliquez sur votre service mobile.

4.  Cliquez sur l’onglet **Identité**, entrez les valeurs des champs **Consumer key** et **Consumer secret** obtenues sur le site de Twitter, puis cliquez sur **Enregistrer**.

    ![](./media/mobile-services-register-twitter-access/mobile-identity-tab-twitter-only.png)

5.  Cliquez sur l'onglet **Configurer**, faites défiler jusqu'à **Paramètres de l'application**, puis entrez une paire **Nom**/**Valeur** pour chacun des éléments suivants obtenus à partir du site de Twitter, puis cliquez sur **Enregistrer**.

    -   `TWITTER_ACCESS_TOKEN`
    -   `TWITTER_ACCESS_TOKEN_SECRET`

    ![](./media/mobile-services-register-twitter-access/mobile-schedule-job-app-settings.png)

    Ceci stocke le jeton d'accès Twitter dans les paramètres de l'application. Comme les informations d'identification sous l'onglet **Identité**, les informations d'identification d'accès sont également stockées dans les paramètres de l'application, et vous pouvez y accéder dans les scripts de votre serveur sans les coder en dur dans le fichier de script. Pour plus d'informations, consultez l'article [Paramètres de l'application](http://msdn.microsoft.com/en-us/library/windowsazure/b6bb7d2d-35ae-47eb-a03f-6ee393e170f7).


