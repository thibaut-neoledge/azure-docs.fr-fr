Vous devez authentifier vos applications avant d'accéder aux ressources des nouvelles API Twitter v1.1. Tout d'abord, vous devez obtenir les informations d'identification requises pour demander l'accès en utilisant OAuth 2.0. Ensuite, vous allez les stocker en toute sécurité dans les paramètres de l'application pour vos services mobiles.

1. Si ce n'est déjà fait, suivez la procédure de la rubrique <a href="../articles/mobile-services-how-to-register-twitter-authentication.md/" target="_blank">Inscrire des applications pour la connexion à Twitter avec Mobile Services</a>. 

  	Twitter génère les informations d'identification requises pour vous permettre d'accéder aux API Twitter v1.1. Vous pouvez obtenir ces informations d'identification à partir du site Web Twitter Developers. 

2. Rendez-vous sur le site web <a href="http://go.microsoft.com/fwlink/p/?LinkId=268300" target="_blank">Développeurs Twitter</a>, connectez-vous à l'aide des informations d'identification de votre compte Twitter, puis sélectionnez votre application Twitter.

3. Sous l'onglet **Clés et les jetons d'accès** de l'application, notez les valeurs suivantes :

	+ **Consumer key**
	+ **Consumer secret**
	+ **Access token**
	+ **Access token secret**

4. Connectez-vous au [portail de gestion Microsoft Azure], cliquez sur **Mobile Services**, puis sur le service mobile.

5. Cliquez sur l'onglet **Identité**, entrez les valeurs des champs **Consumer key** et **Consumer secret** obtenues sur le site de Twitter, puis cliquez sur **Enregistrer**. 

	![](./media/mobile-services-register-twitter-access/mobile-identity-tab-twitter-only.png)

2. Cliquez sur l'onglet **Configurer**, faites défiler jusqu'à **Paramètres de l'application** et entrez une paire **Nom** et **Valeur** pour chacun des paramètres suivants obtenus à partir du site de Twitter, puis cliquez sur **Enregistrer**.

	+ `TWITTER_ACCESS_TOKEN`
	+ `TWITTER_ACCESS_TOKEN_SECRET`

	![](./media/mobile-services-register-twitter-access/mobile-schedule-job-app-settings.png)

	Cette opération stocke le jeton d'accès Twitter dans les paramètres de l'application. Comme les informations d'identification sous l'onglet **Identité**, les informations d'identification d'accès sont également stockées dans les paramètres de l'application, et vous pouvez y accéder dans les scripts de votre serveur sans les coder en dur dans le fichier de script. Pour plus d'informations, consultez la rubrique [Paramètres de l'application].

<!-- URLs. -->
[Référence de script serveur Mobile Services]: http://go.microsoft.com/fwlink/?LinkId=262293
[WindowsAzure.com]: http://www.windowsazure.com/
[Portail de gestion Microsoft Azure]: https://manage.windowsazure.com/
[Inscrire des applications pour la connexion à Twitter avec Mobile Services]: ../articles/mobile-services-how-to-register-twitter-authentication.md
[Développeurs Twitter]: http://go.microsoft.com/fwlink/p/?LinkId=268300
[Paramètres de l'application]: http://msdn.microsoft.com/library/windowsazure/b6bb7d2d-35ae-47eb-a03f-6ee393e170f7

<!--HONumber=49-->