
Tout d’abord, vous devez enregistrer votre application auprès d’un fournisseur d’identité sur son site, puis définir les informations d’identification dans votre service mobile.

1. Dans le [portail de gestion Azure], accédez à votre service mobile, puis cliquez sur **Tableau de bord** et notez la valeur du champ **URL du service mobile**.

2. Enregistrez votre application auprès de l’un des fournisseurs d’identité pris en charge suivants.

	* [Google](mobile-services-how-to-register-google-authentication.md)
	* [Facebook](mobile-services-how-to-register-facebook-authentication.md)
	* [Twitter](mobile-services-how-to-register-twitter-authentication.md)
	* [Microsoft](mobile-services-how-to-register-microsoft-authentication.md)
	* [Azure Active Directory](mobile-services-how-to-register-active-directory-authentication.md).  
	
    >[AZURE.IMPORTANT]Veillez à définir correctement l’URI de redirection pour votre service mobile dans le site de développement du fournisseur d’identité. Comme décrit dans les instructions pour chaque fournisseur ci-dessus, le chemin d’accès de l’URL de redirection est différent pour un service mobile principal .NET (`/signin-<provider>`) et pour un service mobile principal JavaScript (`/login/<provider>`). Un URI de redirection mal configuré empêche le client de se connecter à votre application. <br/>Gardez la clé secrète cliente pour vous, ne la communiquez pas.

3. Dans votre service mobile dans le [portail de gestion Azure], cliquez sur l’onglet **Identité** et entrez l’ID et le secret d’application que vous venez d’obtenir auprès du fournisseur d’identité.

Maintenant que vous avez configuré votre application et votre service mobile pour prendre en charge un fournisseur d’identité pour l’authentification, vous pouvez répéter ces étapes pour prendre en charge des fournisseurs d’identité supplémentaires.

[portail de gestion Azure]: https://manage.windowsazure.com/

<!---HONumber=July15_HO1-->