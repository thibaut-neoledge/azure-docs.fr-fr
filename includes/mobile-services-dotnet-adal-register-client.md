## <a name="register-app-aad"></a>Inscription de votre application cliente auprès d'Azure Active Directory

1.  Accédez à **Active Directory** dans le [portail de gestion Azure][portail de gestion Azure], puis cliquez sur votre annuaire.

	![][0]

2.  Cliquez en haut sur l'onglet **Applications**, puis cliquez pour **AJOUTER** une application.

	![][1]

3.  Cliquez sur **Ajouter une application développée par mon organisation**.

4.  Dans l'Assistant Ajout d'application, entrez un **Nom** pour votre application et cliquez sur le type **Application cliente native**. Ensuite, cliquez pour continuer.

	![][2]

5.  Dans la zone **URI de redirection**, entrez le point de terminaison /login/done pour votre service mobile. Cette valeur doit être semblable à <https://todolist.azure-mobile.net/login/done>.

	![][3]

6.  Cliquez sur l'onglet **Configurer** de l'application native et copiez l'**ID client**. Vous en aurez besoin ultérieurement.

	![][4]

7.  Faites défiler la page vers le bas jusqu'à la section **Autorisations pour d'autres applications** et accordez un accès complet à l'application du service mobile que vous avez inscrite précédemment. Cliquez ensuite sur **Enregistrer**.

	![][5]

Votre service mobile est maintenant configuré dans AAD de manière à accepter des connexions via l'authentification unique à partir de votre application.

  [portail de gestion Azure]: https://manage.windowsazure.com/
  [0]: ./media/mobile-services-dotnet-adal-register-client/mobile-services-select-aad.png
  [1]: ./media/mobile-services-dotnet-adal-register-client/mobile-services-aad-applications-tab.png
  [2]: ./media/mobile-services-dotnet-adal-register-client/mobile-services-native-selection.png
  [3]: ./media/mobile-services-dotnet-adal-register-client/mobile-services-native-redirect-uri.png
  [4]: ./media/mobile-services-dotnet-adal-register-client/mobile-services-native-client-id.png
  [5]: ./media/mobile-services-dotnet-adal-register-client/mobile-services-native-add-permissions.png
