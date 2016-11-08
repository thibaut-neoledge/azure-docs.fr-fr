---
title: Authentification de votre application avec le service d'authentification unique de la bibliothèque d'authentification Active Directory (Windows Store) | Microsoft Docs
description: Découvrez comment authentifier les utilisateurs pour l'authentification unique avec la bibliothèque d'authentification AD dans votre application Windows Store.
documentationcenter: windows
author: wesmc7777
manager: dwrede
editor: ''
services: mobile-services

ms.service: mobile-services
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-store
ms.devlang: dotnet
ms.topic: article
ms.date: 07/21/2016
ms.author: wesmc

---
# Authentification de votre application avec le service d'authentification unique de la bibliothèque d'authentification Active Directory
[!INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;

[!INCLUDE [mobile-services-selector-adal-sso](../../includes/mobile-services-selector-adal-sso.md)]

## Vue d'ensemble
Dans ce didacticiel, vous allez ajouter le processus d'authentification au projet de démarrage rapide à l'aide de la bibliothèque d'authentification Active Directory afin de prendre en charge les [opérations de connexion dirigées vers le client](http://msdn.microsoft.com/library/azure/jj710106.aspx) avec Azure Active Directory. Pour la prise en charge des [opérations de connexion dirigées vers le service](http://msdn.microsoft.com/library/azure/dn283952.aspx) avec Azure Active Directory, commencez par le didacticiel [Ajouter l'authentification à votre application Mobile Services](mobile-services-dotnet-backend-windows-universal-dotnet-get-started-users.md).

Pour qu'il soit possible d'authentifier les utilisateurs, vous devez inscrire votre application auprès d'Azure Active Directory (AAD). Cela se déroule en deux étapes : Vous devez d'abord inscrire votre service mobile et exposer les autorisations sur celui-ci. Vous devez ensuite inscrire votre application Windows Store et lui accorder l'accès à ces autorisations.

> [!NOTE]
> Ce didacticiel vise à mieux vous faire comprendre en quoi Mobile Services vous permet d'effectuer une authentification Azure Active Directory unique pour les applications Windows Store à l'aide d'une [opération de connexion dirigée vers le client](http://msdn.microsoft.com/library/azure/jj710106.aspx). Si vous n'avez aucune expérience de Mobile Services, suivez le didacticiel [Prise en main de Mobile Services].
> 
> 

## Configuration requise
Ce didacticiel requiert les éléments suivants :

* Visual Studio 2013 s'exécutant sous Windows 8.1.
* Exécution du didacticiel [Prise en main de Mobile Services].
* Kit de développement logiciel (SDK) Microsoft Azure Mobile Services – Package NuGet
* Bibliothèque d'authentification Active Directory – Package NuGet

[!INCLUDE [mobile-services-dotnet-adal-register-service](../../includes/mobile-services-dotnet-adal-register-service.md)]

## Inscription de votre application auprès d'Azure Active Directory
Pour inscrire l'application auprès d'Azure Active Directory, vous devez l'associer au Windows Store et posséder un identificateur de sécurité (SID) du package pour l'application. Le SID en question est enregistré avec les paramètres d'application natifs dans Azure Active Directory.

### Association de l'application à un nouveau nom d'application Store
1. Dans Visual Studio, cliquez avec le bouton droit sur l'application cliente, puis sélectionnez **Store** et **Associer l'application au Windows Store**.
   
    ![][1]
2. Connectez-vous à votre compte de centre de développement.
3. Entrez le nom de l'application que vous souhaitez réserver pour l'application et cliquez ensuite sur **Réserver**.
   
    ![][2]
4. Sélectionnez le nouveau nom d'application et cliquez sur **Suivant**.
5. Cliquez sur **Associer** pour associer l'application au nom du magasin.

### Extraction de l'identificateur de sécurité (SID) du package de votre application
Vous devez, à présent, extraire l'identificateur de sécurité (SID) du package qui sera configuré avec les paramètres d'application natifs.

1. Connectez-vous au [Tableau de bord du centre de développement Windows], puis cliquez sur **Modifier** sur l'application.
   
    ![][3]
2. Cliquez ensuite sur **Gestion des applications** > **Identité d'application** et copiez votre package SID sur la page.
   
    ![][4]

### Création de l'inscription de l'application native
1. Accédez à **Active Directory** dans le [portail classique], puis cliquez sur votre répertoire.
   
    ![][7]
2. Cliquez en haut sur l'onglet **Applications**, puis cliquez pour **AJOUTER** une application.
   
    ![][8]
3. Cliquez sur **Ajouter une application développée par mon organisation**.
4. Dans l'Assistant Ajout d'application, entrez un **Nom** pour votre application et cliquez sur le type **Application cliente native**. Ensuite, cliquez pour continuer.
   
    ![][9]
5. Dans la zone **URI de redirection**, collez le SID du package d'application que vous avez copié précédemment, puis cliquez pour terminer l'inscription.
   
    ![][10]
6. Cliquez sur l'onglet **Configurer** de l'application native et copiez l'**ID client**. Vous en aurez besoin ultérieurement.
   
    ![][11]
7. Faites défiler la page vers le bas jusqu'à la section **Autorisations pour d'autres applications** et accordez un accès complet à l'application du service mobile que vous avez inscrite précédemment. Cliquez ensuite sur **Enregistrer**.
   
    ![][12]

Votre service mobile est maintenant configuré dans AAD de manière à accepter des connexions via l'authentification unique à partir de votre application.

## Configuration du service mobile afin d'exiger une authentification
[!INCLUDE [mobile-services-restrict-permissions-dotnet-backend](../../includes/mobile-services-restrict-permissions-dotnet-backend.md)]

## Ajout du code d'authentification à l'application cliente
1. Ouvrez votre projet d'application cliente Windows Store dans Visual Studio.

[!INCLUDE [mobile-services-dotnet-adal-install-nuget](../../includes/mobile-services-dotnet-adal-install-nuget.md)]

1. Dans la fenêtre Explorateur de solutions de Visual Studio, ouvrez le fichier MainPage.cs et ajoutez les lignes suivantes à l'aide d'instructions.
   
        using Windows.UI.Popups;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
        using Newtonsoft.Json.Linq;
2. Ajoutez le code suivant à la classe MainPage qui déclare la méthode `AuthenticateAsync`.
   
        private MobileServiceUser user;
        private async Task AuthenticateAsync()
        {
            string authority = "<INSERT-AUTHORITY-HERE>";
            string resourceURI = "<INSERT-RESOURCE-URI-HERE>";
            string clientID = "<INSERT-CLIENT-ID-HERE>";
            while (user == null)
            {
                string message;
                try
                {
                  AuthenticationContext ac = new AuthenticationContext(authority);
                  AuthenticationResult ar = await ac.AcquireTokenAsync(resourceURI, clientID, (Uri) null);
                  JObject payload = new JObject();
                  payload["access_token"] = ar.AccessToken;
                  user = await App.MobileService.LoginAsync(MobileServiceAuthenticationProvider.WindowsAzureActiveDirectory, payload);
                  message = string.Format("You are now logged in - {0}", user.UserId);
                }
                catch (InvalidOperationException)
                {
                  message = "You must log in. Login Required";
                }
                var dialog = new MessageDialog(message);
                dialog.Commands.Add(new UICommand("OK"));
                await dialog.ShowAsync();
            }
        }
3. Dans le code de la méthode `AuthenticateAsync` ci-dessus, remplacez **INSERT-AUTHORITY-HERE** par le nom du client dans lequel vous avez déployé votre application ; le format doit être https://login.windows.net/tenant-name.onmicrosoft.com. Cette valeur peut être copiée depuis l’onglet Domaine de votre Azure Active Directory dans le [portail Azure Classic].
4. Dans le code de la méthode `AuthenticateAsync` ci-dessus, remplacez **INSERT-RESOURCE-URI-HERE** par l'**URI ID d'application** de votre service mobile. Si vous avez suivi les instructions de la rubrique [Inscription auprès d'Azure Active Directory], votre URI ID d'application doit être semblable à https://todolist.azure-mobile.net/login/aad.
5. Dans le code de la méthode `AuthenticateAsync` ci-dessus, remplacez **INSERT-CLIENT-ID-HERE** par l'ID client que vous avez copié depuis l'application cliente native.
6. Dans la fenêtre Explorateur de solutions de Visual Studio, ouvrez le fichier Package.appxmanifest dans le projet client. Cliquez sur l'onglet **Capacités**, puis activez **Application d'entreprise** et **Réseaux privés (client et serveur)**. Enregistrez le fichier.
   
    ![][14]
7. Dans le fichier MainPage.cs, mettez à jour le gestionnaire d'événements `OnNavigatedTo` pour appeler la méthode `AuthenticateAsync` comme suit.
   
       protected override async void OnNavigatedTo(NavigationEventArgs e)
       {
           await AuthenticateAsync();
           await RefreshTodoItems();
       }

## Test du client à l'aide de l'authentification
1. Exécutez l'application cliente dans Visual Studio.
2. Une invite s'affiche alors pour vous permettre de vous connecter à votre annuaire Azure Active Directory.
3. L'application authentifie et renvoie les tâches à effectuer.
   
    ![][15]

<!-- Images -->
[0]: ./media/mobile-services-windows-store-dotnet-adal-sso-authenticate/mobile-services-aad-app-manage-manifest.png
[1]: ./media/mobile-services-windows-store-dotnet-adal-sso-authentication/mobile-services-vs-associate-app.png
[2]: ./media/mobile-services-windows-store-dotnet-adal-sso-authentication/mobile-services-vs-reserve-store-appname.png
[3]: ./media/mobile-services-windows-store-dotnet-adal-sso-authentication/mobile-services-store-app-edit.png
[4]: ./media/mobile-services-windows-store-dotnet-adal-sso-authentication/mobile-services-store-app-services.png
[5]: ./media/mobile-services-windows-store-dotnet-adal-sso-authentication/mobile-services-live-services-site.png
[6]: ./media/mobile-services-windows-store-dotnet-adal-sso-authentication/mobile-services-store-app-package-sid.png
[7]: ./media/mobile-services-windows-store-dotnet-adal-sso-authentication/mobile-services-select-aad.png
[8]: ./media/mobile-services-windows-store-dotnet-adal-sso-authentication/mobile-services-aad-applications-tab.png
[9]: ./media/mobile-services-windows-store-dotnet-adal-sso-authentication/mobile-services-native-selection.png
[10]: ./media/mobile-services-windows-store-dotnet-adal-sso-authentication/mobile-services-native-sid-redirect-uri.png
[11]: ./media/mobile-services-windows-store-dotnet-adal-sso-authentication/mobile-services-native-client-id.png
[12]: ./media/mobile-services-windows-store-dotnet-adal-sso-authentication/mobile-services-native-add-permissions.png
[14]: ./media/mobile-services-windows-store-dotnet-adal-sso-authentication/mobile-services-package-appxmanifest.png
[15]: ./media/mobile-services-windows-store-dotnet-adal-sso-authentication/mobile-services-app-run.png

<!-- URLs. -->
[Inscription auprès d'Azure Active Directory]: mobile-services-how-to-register-active-directory-authentication.md
[portail Azure Classic]: https://manage.windowsazure.com/
[portail classique]: https://manage.windowsazure.com/
[Prise en main de Mobile Services]: mobile-services-dotnet-backend-windows-store-dotnet-get-started.md
[Prise en main de Mobile Services]: mobile-services-dotnet-backend-windows-store-dotnet-get-started.md
[Tableau de bord du centre de développement Windows]: http://go.microsoft.com/fwlink/p/?LinkID=266734

<!---HONumber=AcomDC_0727_2016-->