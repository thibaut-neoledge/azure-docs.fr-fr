### (Facultatif) Configurer votre Service Mobile .NET pour Azure Active Directory

>[AZURE.NOTE]Ces étapes sont facultatives, car elles s'appliquent seulement au fournisseur de connexion Azure Active Directory.

1. Installez le [package NuGet WindowsAzure.MobileServices.Backend.Security](https://www.nuget.org/packages/WindowsAzure.MobileServices.Backend.Security).

2. Dans Visual Studio, développez App_Start et ouvrez WebApiConfig.cs. Ajoutez l'instruction `using` suivante au début :

        using Microsoft.WindowsAzure.Mobile.Service.Security.Providers;

3. Toujours dans WebApiConfig.cs, ajoutez le code suivant à la méthode `Register`, immédiatement après l'instanciation de `options` :

        options.LoginProviders.Remove(typeof(AzureActiveDirectoryLoginProvider));
        options.LoginProviders.Add(typeof(AzureActiveDirectoryExtendedLoginProvider));

<!---HONumber=July15_HO4-->