

Pour authentifier les utilisateurs, inscrivez votre application auprès d'un fournisseur d'identité, puis enregistrez les informations d'identification de client générées par le fournisseur avec Azure Mobile Services.

1. Connectez-vous au portail de gestion Azure, cliquez sur **Mobile Services**, puis sur le service mobile.

2. Cliquez sur l'onglet **Tableau de bord** et notez la valeur **URL du service mobile**. Il se peut que le fournisseur d'identité vous demande cette valeur lorsque vous inscrivez votre application.

3. Choisissez un fournisseur d'identité pris en charge dans la liste ci-dessous. Suivez les étapes pour inscrire votre application auprès de ce fournisseur. N'oubliez pas de noter les valeurs de l'identité du client et de la clé secrète cliente générées par le fournisseur.

 - <a href="/documentation/articles/mobile-services-how-to-register-microsoft-authentication/" target="_blank">Compte Microsoft</a>
 - <a href="/documentation/articles/mobile-services-how-to-register-facebook-authentication/" target="_blank">Facebook</a>
 - <a href="/documentation/articles/mobile-services-how-to-register-twitter-authentication/" target="_blank">Twitter</a>
 - <a href="/documentation/articles/mobile-services-how-to-register-google-authentication/" target="_blank">Google</a>
 - <a href="/documentation/articles/mobile-services-how-to-register-active-directory-authentication/" target="_blank">Azure Active Directory</a>

    > [AZURE.IMPORTANT] La clé secrète générée par le fournisseur est une information d'identification de sécurité importante. Ne partagez pas cette clé secrète avec quiconque et ne la distribuez pas avec votre application.

4. De retour dans le portail de gestion, cliquez sur l'onglet **Identité**, entrez les valeurs de l'identificateur d'application et de la clé secrète partagée fournies par votre fournisseur d'identité, puis cliquez sur **Enregistrer**. Votre service mobile et votre application sont désormais configurés pour utiliser le fournisseur d'authentification choisi.

    > [AZURE.IMPORTANT] Vérifiez que vous avez défini le bon URI de redirection sur le site du développeur de votre fournisseur d'identité. Comme décrit dans les instructions liées pour chaque fournisseur ci-dessus, l'URI de redirection peut être différent pour un service principal .NET et pour un service principal JavaScript. Un URI de redirection mal configuré peut faire que l'écran de connexion ne s'affiche pas correctement et que l'application fonctionne mal, de façon inattendue.

<!--HONumber=47-->
