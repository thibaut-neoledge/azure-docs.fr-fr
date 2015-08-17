1. Inscrivez le serveur principal de votre application mobile auprès de votre client Azure Active Directory en suivant la rubrique [Configuration de votre application mobile avec Azure Active Directory].

2. Accédez à **Active Directory** dans le [portail de gestion Azure]

   ![](./media/app-service-mobile-adal-register-app/app-service-navigate-aad.png)

3. Sélectionnez votre annuaire, puis l’onglet **Applications** en haut de la page. Cliquez sur **AJOUTER** en bas de la page pour créer une inscription d’application. 

4. Cliquez sur **Ajouter une application développée par mon organisation**.

5. Dans l'Assistant Ajout d'application, entrez un **Nom** pour votre application et cliquez sur le type **Application cliente native**. Ensuite, cliquez pour continuer.

6. Dans la zone **URI de redirection**, entrez le point de terminaison /login/done pour votre passerelle App Service. Cette valeur doit être similaire à https://contoso.azurewebsites.net/login/done.

7. Une fois l’application native ajoutée, cliquez sur l’onglet **Configurer**. Copiez l’**ID client**. Vous en aurez besoin ultérieurement.

8. Faites défiler la page vers le bas jusqu’à la section **Autorisations à d’autres applications** et cliquez sur **Ajouter une application**.

9. Recherchez l’application web que vous avez inscrite précédemment et cliquez sur l’icône plus, puis sur la coche pour fermer la boîte de dialogue.

10. Dans la nouvelle entrée que vous venez d’ajouter, ouvrez la liste déroulante **Autorisations déléguées**, sélectionnez **Accès (appName)**. Cliquez ensuite sur **Enregistrer**.

   ![](./media/app-service-mobile-adal-register-app/aad-native-client-add-permissions.png)

Votre application est désormais configurée dans AAD, et les utilisateurs peuvent donc se connecter à l’aide de l’authentification unique AAD.

[portail de gestion Azure]: https://manage.windowsazure.com/
[Configuration de votre application mobile avec Azure Active Directory]: ../articles/app-service-how-to-configure-active-directory-authentication-preview.md

<!---HONumber=August15_HO6-->