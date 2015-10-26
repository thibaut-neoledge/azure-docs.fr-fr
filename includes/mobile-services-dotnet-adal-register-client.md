## <a name="register-app-aad"></a>Inscription de votre application cliente auprès d'Azure Active Directory

1. Accédez à **Active Directory** dans le [portail de gestion Azure], puis cliquez sur votre annuaire.

   ![](./media/mobile-services-dotnet-adal-register-client/mobile-services-select-aad.png)

2. Cliquez en haut sur l'onglet **Applications**, puis cliquez pour **AJOUTER** une application. 

   ![](./media/mobile-services-dotnet-adal-register-client/mobile-services-aad-applications-tab.png)

3. Cliquez sur **Ajouter une application développée par mon organisation**.

4. Dans l'Assistant Ajout d'application, entrez un **Nom** pour votre application et cliquez sur le type **Application cliente native**. Ensuite, cliquez pour continuer.

   ![](./media/mobile-services-dotnet-adal-register-client/mobile-services-native-selection.png)

5. Dans la zone **URI de redirection**, entrez le point de terminaison /login/done pour votre service mobile. Cette valeur doit être similaire à https://todolist.azure-mobile.net/login/done.

   ![](./media/mobile-services-dotnet-adal-register-client/mobile-services-native-redirect-uri.png)

6. Cliquez sur l'onglet **Configurer** de l'application native et copiez l'**ID client**. Vous en aurez besoin ultérieurement.

   ![](./media/mobile-services-dotnet-adal-register-client/mobile-services-native-client-id.png)

7. Faites défiler la page jusqu'à la section **Autorisations à d'autres applications** et cliquez sur le bouton **Ajouter une application**. Choisissez **Autres** à partir du menu Afficher et recherchez todo. Cliquez sur **TodoList** pour ajouter le service mobile que vous avez enregistré précédemment et cliquez sur la coche pour finir. Accordez l’accès à l’application de service mobile. Cliquez ensuite sur **Enregistrer**.

   ![](./media/mobile-services-dotnet-adal-register-client/mobile-services-native-add-permissions.png)

Votre service mobile est maintenant configuré dans AAD de manière à accepter des connexions via l'authentification unique à partir de votre application.


[portail de gestion Azure]: https://manage.windowsazure.com/

<!---HONumber=Oct15_HO3-->