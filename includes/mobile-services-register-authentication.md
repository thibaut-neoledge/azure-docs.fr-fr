
* Cliquez sur Portail Azure > **Services mobiles** > votre service mobile > **Tableau de bord**, et notez la valeur de l’**URL du service mobile**.

* Inscrivez votre application à [Google](mobile-services-how-to-register-google-authentication.md), [Facebook](mobile-services-how-to-register-facebook-authentication.md), [Twitter](mobile-services-how-to-register-twitter-authentication.md), [Microsoft](mobile-services-how-to-register-microsoft-authentication.md) ou [Azure Active Directory](mobile-services-how-to-register-active-directory-authentication.md). Notez les valeurs de l’identité du client et de la clé secrète cliente générées par le fournisseur. Gardez la clé secrète cliente pour vous, ne la communiquez pas.

* Cliquez sur Portail Azure > **Services mobiles** > votre service mobile > **Identité** > paramètres de votre fournisseur d’identité. Entrez l’ID d’application et la valeur secrète de votre fournisseur. Votre service mobile et votre application sont désormais configurés pour utiliser votre fournisseur d’authentification. Vous pouvez éventuellement répéter toutes ces étapes pour chaque fournisseur d’identité supplémentaire que vous souhaitez prendre en charge.

    > [AZURE.IMPORTANT]Vérifiez que vous avez défini le bon URI de redirection sur le site du développeur de votre fournisseur d’identité. Comme décrit dans les instructions pour chaque fournisseur ci-dessus, l'URI de redirection peut être différent pour un backend .NET et pour un backend JavaScript. Un URI de redirection mal configuré peut faire que l’écran de connexion ne s’affiche pas correctement et que l’application fonctionne mal, de façon inattendue.

<!---HONumber=August15_HO6-->