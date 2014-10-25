### (Facultatif) Configurer votre service mobile .NET pour une connexion AAD

> [WACOM.NOTE] Ces étapes sont facultatives, car elles s'appliquent seulement au fournisseur Azure Active Directory.

1.  Installez le package NuGet **WindowsAzure.MobileServices.Backend.Security**.

2.  Dans Visual Studio, développez App\_Start et ouvrez le fichier WebApiConfig.cs. Ajoutez le code suivant à la méthode `Register`, immédiatement après l'instanciation de `options` :

        options.LoginProviders.Remove(typeof(AzureActiveDirectoryLoginProvider));
        options.LoginProviders.Add(typeof(AzureActiveDirectoryExtendedLoginProvider));


