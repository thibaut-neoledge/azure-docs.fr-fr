

Pour pouvoir authentifier les utilisateurs, vous devez inscrire votre application avec un fournisseur d'identité. Vous devez ensuite inscrire la clé secrète cliente générée par le fournisseur auprès de votre service d'application.

1. Connectez-vous au [portail Azure en version préliminaire], cliquez sur **Parcourir**, **Groupe de ressources**, puis sélectionnez le groupe de ressources de votre application mobile.

2. Sélectionnez votre passerelle, puis notez la valeur de l'**URL** sous **Propriétés**. Il se peut que le fournisseur d'identité vous demande cette valeur lorsque vous inscrivez votre application.

   	![](./media/app-service-mobile-register-authentication/gateway-uri.png)

3. Choisissez un fournisseur d’identité pris en charge dans la liste ci-dessous et suivez la procédure pour configurer votre application en fonction de ce fournisseur :

 - <a href="/fr-fr/documentation/articles/app-service-mobile-how-to-configure-active-directory-authentication-preview/" target="_blank">Azure Active Directory</a>
 - <a href="/fr-fr/documentation/articles/app-service-mobile-how-to-configure-facebook-authentication-preview/" target="_blank">Connexion Facebook</a>
 - <a href="/fr-fr/documentation/articles/app-service-mobile-how-to-configure-google-authentication-preview/" target="_blank">Connexion Google</a>
 - <a href="/fr-fr/documentation/articles/app-service-mobile-how-to-configure-microsoft-authentication-preview/" target="_blank">Compte Microsoft</a>
 - <a href="/fr-fr/documentation/articles/app-service-mobile-how-to-configure-twitter-authentication-preview/" target="_blank">Connexion Twitter</a>

	Votre application est maintenant configurée pour fonctionner avec le fournisseur d’authentification choisi.

4. (Facultatif) Répétez l’étape précédente pour configurer tout autre fournisseur d’identité que votre application doit prendre en charge. 

<!-- URLs. -->
[portail Azure en version préliminaire]: https://portal.azure.com/

<!---HONumber=July15_HO3-->