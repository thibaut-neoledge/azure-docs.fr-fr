
1. Cliquez sur Portail Azure > **Services mobiles** > votre service mobile > **Tableau de bord**, et notez la valeur de l’**URL du service mobile**.

2. Enregistrez votre application auprès de l’un au moins des fournisseurs d’authentification suivants :
   * [Google](mobile-services-how-to-register-google-authentication.md)
   * [Facebook](mobile-services-how-to-register-facebook-authentication.md)
   * [Twitter](mobile-services-how-to-register-twitter-authentication.md)
   * [Microsoft](mobile-services-how-to-register-microsoft-authentication.md)
   * [Azure Active Directory](mobile-services-how-to-register-active-directory-authentication.md). 
   
   Notez les valeurs de l’identité du client et de la clé secrète cliente générées par le fournisseur. Gardez la clé secrète cliente pour vous, ne la communiquez pas.

3. Dans le portail Azure, cliquez sur **Mobile Services** > votre service mobile > **Identité** > paramètres de votre fournisseur d'identité, puis entrez l'ID client et la valeur secrète de votre fournisseur. 
 
Votre service mobile et votre application sont désormais configurés pour utiliser votre fournisseur d’authentification. Vous pouvez éventuellement répéter toutes ces étapes pour chaque fournisseur d’identité supplémentaire que vous souhaitez prendre en charge.

    > [AZURE.IMPORTANT] Verify that you've set the correct redirect URI on your identity provider's developer site. As described in the linked instructions for each provider above, the redirect URI may be different for a .NET backend service vs. for a JavaScript backend service. An incorrectly configured redirect URI may result in the login screen not being displayed properly and the app malfunctioning in unexpected ways.

<!---HONumber=September15_HO1-->