

Vous devez authentifier vos applications avant d'accéder aux ressources des API Twitter v1.1. Tout d'abord, vous devez obtenir les informations d'identification requises pour demander l'accès en utilisant OAuth 2.0. Ensuite, vous allez les stocker en toute sécurité dans les paramètres de l'application pour vos services mobiles.

1. Si vous ne l'avez pas déjà fait, suivez la procédure de la rubrique [Inscription de vos applications pour la connexion à Twitter avec Mobile Services](../articles/mobile-services/mobile-services-how-to-register-twitter-authentication.md). 
  
  	Twitter génère les informations d'identification requises pour vous permettre d'accéder aux API Twitter v1.1. Vous pouvez obtenir ces informations d'identification à partir du site Web Twitter Developers.

2. Rendez-vous sur le site web [Développeurs Twitter](http://go.microsoft.com/fwlink/p/?LinkId=268300), connectez-vous à l'aide des informations d'identification de votre compte Twitter, puis sélectionnez votre application Twitter.

3. Sous l'onglet **Clés et jetons d'accès** de l'application, notez les valeurs suivantes :

	+ **Clé du client**
	+ **Secret du client**
	+ **Jeton d'accès**
	+ **Secret du jeton d'accès**

4. Connectez vous au [portail Azure Classic](https://manage.windowsazure.com/), cliquez sur **Mobile Services**, puis sur votre service mobile.

5. Cliquez sur l’onglet **Identité**, entrez les valeurs des champs **Clé de consommateur** et **Question secrète du consommateur** obtenues sur le site de Twitter, puis cliquez sur **Enregistrer**.

	![](./media/mobile-services-register-twitter-access/mobile-identity-tab-twitter-only.png)

2. Cliquez sur l'onglet **Configurer**, faites défiler jusqu'à **Paramètres de l'application**, puis entrez une paire **Nom**/**Valeur** pour chacun des éléments suivants obtenus à partir du site de Twitter, puis cliquez sur **Enregistrer**.

	+ `TWITTER_ACCESS_TOKEN`
	+ `TWITTER_ACCESS_TOKEN_SECRET`

	![](./media/mobile-services-register-twitter-access/mobile-schedule-job-app-settings.png)

	Ceci stocke le jeton d'accès Twitter dans les paramètres de l'application. Comme les informations d'identification sous l'onglet **Identité**, les informations d'identification d'accès sont également stockées dans les paramètres de l'application, et vous pouvez y accéder dans les scripts de votre serveur sans les coder en dur dans le fichier de script. Pour plus d'informations, consultez l'article [Paramètres de l'application].

<!-- URLs. -->
[Mobile Services server script reference]: http://go.microsoft.com/fwlink/?LinkId=262293
[Register your apps for Twitter login with Mobile Services]: ../articles/mobile-services/mobile-services-how-to-register-twitter-authentication.md
[Twitter Developers]: http://go.microsoft.com/fwlink/p/?LinkId=268300
[Paramètres de l'application]: http://msdn.microsoft.com/library/azure/b6bb7d2d-35ae-47eb-a03f-6ee393e170f7

<!---HONumber=AcomDC_0218_2016-->