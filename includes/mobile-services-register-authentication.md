

Pour pouvoir authentifier les utilisateurs, vous devez inscrire votre application avec un fournisseur d'identité. Vous devez ensuite inscrire la clé secrète cliente générée par le fournisseur avec Mobile Services.

1.  Connectez-vous au [portail de gestion Azure](https://manage.windowsazure.com/), puis cliquez sur **Mobile Services** et sur votre service mobile.

	![](./media/mobile-services-register-authentication/mobile-services-selection.png)

2.  Cliquez sur l'onglet **Tableau de bord**, puis notez la valeur **URL du service mobile**.

	![](./media/mobile-services-register-authentication/mobile-service-uri.png)

    Il se peut que le fournisseur d'identité vous demande cette valeur lorsque vous inscrivez votre application.

3.  Choisissez un fournisseur d'identité pris en charge dans la liste ci-dessous et suivez la procédure pour inscrire votre application auprès de ce fournisseur :

	-   [Compte Microsoft](/en-us/develop/mobile/how-to-guides/register-for-microsoft-authentication/)
	-   [Connexion Facebook](/en-us/develop/mobile/how-to-guides/register-for-facebook-authentication/)
	-   [Connexion Twitter](/en-us/develop/mobile/how-to-guides/register-for-twitter-authentication/)
	-   [Connexion Google](/en-us/develop/mobile/how-to-guides/register-for-google-authentication/)
	-   [Azure Active Directory](/fr-fr/documentation/articles/mobile-services-how-to-register-active-directory-authentication/)

		N'oubliez pas de noter les valeurs de l'identité du client et de la clé secrète cliente générées par le fournisseur.

		**Remarque relative à la sécurité**

		La clé secrète générée par le fournisseur est une information d'identification de sécurité importante. Ne partagez pas cette clé secrète avec quiconque et ne la distribuez pas avec votre application.

1.  De retour dans le portail de gestion, cliquez sur l'onglet **Identité**, entrez les valeurs de l'identificateur d'application et de la clé secrète partagée fournies par votre fournisseur d'identité, puis cliquez sur **Enregistrer**.

	![](./media/mobile-services-register-authentication/mobile-identity-tab.png)

    Votre service mobile et votre application sont désormais configurés pour utiliser le fournisseur d'authentification choisi.


