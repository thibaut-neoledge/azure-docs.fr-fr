## <a name="register-mobile-service-aad"></a>Inscription de votre service mobile auprès d'Azure Active Directory

Dans cette section, vous allez inscrire votre service mobile auprès d'Azure Active Directory et configurer des autorisations pour permettre l'emprunt d'identité d'authentification unique.

1.  Inscrivez votre application auprès d'Azure Active Directory en suivant la rubrique [Inscription auprès d'Azure Active Directory][].

2.  Sur le [portail de gestion Azure][], revenez à l'extension Azure Active Directory et cliquez sur votre annuaire actif.

3.  Cliquez sur l'onglet **Applications**, puis sur votre application.

4.  Cliquez sur **Gérer le manifeste**. Cliquez ensuite sur **Télécharger le manifeste** et enregistrez le manifeste de l'application dans un répertoire local.

	![][]

5.  Ouvrez le manifeste de l'application avec Visual Studio. En haut du fichier, recherchez la ligne des autorisations d'application. Celle-ci sera semblable à la suivante:

        "appPermissions": [],

    Remplacez cette ligne par les autorisations d'application suivantes, puis enregistrez le fichier.

        "appPermissions": [
            {
                "claimValue": "user_impersonation",
                "description": "Allow the application access to the mobile service",
                "directAccessGrantTypes": [],
                "displayName": "Have full access to the mobile service",
                "impersonationAccessGrantTypes": [
                    {
                        "impersonated": "User",
                        "impersonator": "Application"
                    }
                ],
                "isDisabled": false,
                "origin": "Application",
                "permissionId": "b69ee3c9-c40d-4f2a-ac80-961cd1534e40",
                "resourceScopeType": "Personal",
                "userConsentDescription": "Allow the application full access to the mobile service on your behalf",
                "userConsentDisplayName": "Have full access to the mobile service"
            }
        ],

2.  Sur le portail de gestion Azure, cliquez de nouveau sur **Gérer le manifeste**, puis sur **Télécharger le manifeste sur le serveur**. Accédez au manifeste que vous venez de mettre à jour et téléchargez-le sur le serveur.

<!-- URLs. -->

  [Inscription auprès d'Azure Active Directory]: /fr-fr/documentation/articles/mobile-services-how-to-register-active-directory-authentication/
  [portail de gestion Azure]: https://manage.windowsazure.com/
  []: ./media/mobile-services-dotnet-adal-register-service/mobile-services-aad-app-manage-manifest.png
