---
title: Prise en main de l'authentification pour Mobile Apps dans l'application Xamarin.Forms | Microsoft Docs
description: Découvrez comment utiliser Mobile Apps pour authentifier les utilisateurs de votre application Xamarin Forms via divers fournisseurs d'identité, notamment AAD, Google, Facebook, Twitter et Microsoft.
services: app-service\mobile
documentationcenter: xamarin
author: wesmc7777
manager: dwrede
editor: ''

ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin
ms.devlang: dotnet
ms.topic: article
ms.date: 06/16/2016
ms.author: wesmc

---
# Ajout de l'authentification à votre application Xamarin.Forms
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

## Vue d’ensemble
Cette rubrique montre comment authentifier les utilisateurs d'une application App Service Mobile App à partir de votre application cliente. Dans ce didacticiel, vous allez ajouter l'authentification au projet de démarrage rapide Xamarin.Forms à l'aide d'un fournisseur d'identité pris en charge par App Service. Une fois l’utilisateur authentifié et autorisé par votre application Mobile App, la valeur de l’ID utilisateur s’affiche ; vous pouvez alors accéder aux données de table limitées.

## Configuration requise
Pour obtenir les meilleurs résultats avec ce didacticiel, nous vous recommandons de commencer par suivre le didacticiel [Créer une application Xamarin.Forms](app-service-mobile-xamarin-forms-get-started.md). Après avoir terminé ce didacticiel, vous disposerez d’un projet Xamarin.Forms qui est une application TodoList multiplateforme.

Si vous n’utilisez pas le projet de serveur du démarrage rapide téléchargé, vous devez ajouter le package d’extension d’authentification à votre projet. Pour plus d'informations sur les packages d'extension de serveur, consultez [Fonctionnement avec le Kit de développement logiciel (SDK) du serveur principal .NET pour Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

## Inscription de votre application pour l'authentification et configuration d'App Services
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## Restriction des autorisations pour les utilisateurs authentifiés
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

## Ajout de l'authentification à la bibliothèque de classes portable
Mobile Apps utilise la méthode d’extension [LoginAsync] sur le [MobileServiceClient] pour connecter un utilisateur avec l’authentification App Service. Cet exemple utilise un flux d’authentification géré par le serveur qui affiche l’interface de connexion du fournisseur dans l’application. Pour plus d’informations, voir la rubrique [Authentification gérée par le serveur](app-service-mobile-dotnet-how-to-use-client-library.md#serverflow). Pour offrir une meilleure expérience utilisateur dans votre application de production, vous pouvez envisager au lieu d’utiliser une [authentification gérée par le client](app-service-mobile-dotnet-how-to-use-client-library.md#clientflow).

Pour vous authentifier auprès d’un projet Xamarin Forms, vous devez définir une interface **IAuthenticate** dans la bibliothèque de classes portable de l’application. Vous devez également mettre à jour l’interface utilisateur définie dans la bibliothèque de classes portable pour ajouter un bouton **Connexion** que l’utilisateur devra sélectionner pour démarrer l’authentification. Une fois l’authentification effectuée, les données sont chargées depuis le serveur principal d’application mobile.

Vous devez implémenter l’interface **IAuthenticate** pour chaque plateforme prise en charge par votre application.

1. Dans Visual Studio ou Xamarin Studio, ouvrez App.cs à partir du projet dont le nom contient le terme **Portable** et qui correspond à un projet de bibliothèque de classes portable. Ajoutez ensuite l’instruction `using` suivante :
   
        using System.Threading.Tasks;
2. Dans App.cs, ajoutez la définition d’interface `IAuthenticate` suivante immédiatement avant la définition de classe `App`.
   
        public interface IAuthenticate
        {
            Task<bool> Authenticate();
        }
3. Ajoutez les membres statiques suivants à la classe **App** pour initialiser l’interface avec une implémentation spécifique à la plateforme.
   
        public static IAuthenticate Authenticator { get; private set; }
   
        public static void Init(IAuthenticate authenticator)
        {
            Authenticator = authenticator;
        }
4. Ouvrez TodoList.xaml à partir du projet de bibliothèque de classes portable, ajoutez l’élément **bouton** suivant dans l’élément de disposition *buttonsPanel*, après le bouton existant :
   
          <Button x:Name="loginButton" Text="Sign-in" MinimumHeightRequest="30" 
            Clicked="loginButton_Clicked"/>
   
    Ce bouton déclenche l’authentification gérée par le serveur auprès du serveur principal de votre application mobile.
5. Ouvrez TodoList.xaml.cs à partir du projet de bibliothèque de classes portable, puis ajoutez le champ suivant à la classe `TodoList` :
   
        // Track whether the user has authenticated. 
        bool authenticated = false;
6. Remplacez la méthode **OnAppearing** par le code suivant :
   
        protected override async void OnAppearing()
        {
            base.OnAppearing();
   
            // Refresh items only when authenticated.
            if (authenticated == true)
            {
                // Set syncItems to true in order to synchronize the data 
                // on startup when running in offline mode.
                await RefreshItems(true, syncItems: false);
   
                // Hide the Sign-in button.
                this.loginButton.IsVisible = false;
            }
        }
   
    Cela permet de s’assurer que les données seront actualisées par le service seulement une fois l’utilisateur authentifié.
7. Ajoutez le gestionnaire suivant pour l’événement **Clicked** à la classe **TodoList** :
   
        async void loginButton_Clicked(object sender, EventArgs e)
        {
            if (App.Authenticator != null)
                authenticated = await App.Authenticator.Authenticate();
   
            // Set syncItems to true to synchronize the data on startup when offline is enabled.
            if (authenticated == true)
                await RefreshItems(true, syncItems: false);
        }
8. Enregistrez vos modifications et régénérez le projet de bibliothèque de classes portable en vérifiant qu’il ne contient aucune erreur.

## Ajout de l'authentification à l'application Android
Cette section montre comment implémenter l’interface **IAuthenticate** dans le projet d’application Android. Vous pouvez ignorer cette section si vous n’utilisez pas d’appareils Android.

1. Dans Visual Studio ou Xamarin Studio, cliquez avec le bouton droit sur le projet **droid**, puis cliquez sur **Définir comme projet de démarrage**.
2. Appuyez sur F5 pour démarrer le projet dans le débogueur, puis vérifiez qu’une exception non prise en charge avec le code d’état 401 (Unauthorized) est déclenchée après le démarrage de l’application. Cette exception se produit car l’accès au serveur principal est limité aux seuls utilisateurs autorisés.
3. Ouvrez MainActivity.cs dans le projet Android et ajoutez les instructions `using` suivantes :
   
        using Microsoft.WindowsAzure.MobileServices;
        using System.Threading.Tasks;
4. Mettez à jour la classe **MainActivity** pour implémenter l’interface **IAuthenticate**, comme suit :
   
        public class MainActivity : global::Xamarin.Forms.Platform.Android.FormsApplicationActivity, IAuthenticate
5. Mettez à jour la classe **MainActivity** en ajoutant un champ **MobileServiceUser** et une méthode **Authenticate** requise par l’interface **IAuthenticate**, comme suit :
   
        // Define a authenticated user.
        private MobileServiceUser user;
   
        public async Task<bool> Authenticate()
        {
            var success = false;
            var message = string.Empty;
            try
            {
                // Sign in with Facebook login using a server-managed flow.
                user = await TodoItemManager.DefaultManager.CurrentClient.LoginAsync(this,
                    MobileServiceAuthenticationProvider.Facebook);
                if (user != null)
                {
                    message = string.Format("you are now signed-in as {0}.",
                        user.UserId);
                    success = true;
                }
            }
            catch (Exception ex)
            {
                message = ex.Message;
            }
   
            // Display the success or failure message.
            AlertDialog.Builder builder = new AlertDialog.Builder(this);
            builder.SetMessage(message);
            builder.SetTitle("Sign-in result");
            builder.Create().Show();
   
            return success;
        }

    Si vous utilisez un fournisseur d’identité différent de Facebook, choisissez une autre valeur pour [MobileServiceAuthenticationProvider].

1. Ajoutez le code suivant à la méthode **OnCreate** de la classe **MainActivity** avant l’appel à `LoadApplication()` :
   
        // Initialize the authenticator before loading the app.
        App.Init((IAuthenticate)this);
   
    L’authentificateur sera ainsi initialisé avant le chargement de l’application.
2. Recompilez et exécutez l’application, puis connectez-vous avec le fournisseur d’authentification choisi et vérifiez que vous êtes en mesure d’accéder aux données en tant qu’utilisateur authentifié.

## Ajout de l'authentification à l'application iOS
Cette section montre comment implémenter l’interface **IAuthenticate** dans le projet d’application iOS. Vous pouvez ignorer cette section si vous n’utilisez pas d’appareils iOS.

1. Dans Visual Studio ou Xamarin Studio, cliquez avec le bouton droit sur le projet **iOS**, puis cliquez sur **Définir comme projet de démarrage**.
2. Appuyez sur F5 pour démarrer le projet dans le débogueur, puis vérifiez qu’une exception non prise en charge avec le code d’état 401 (Unauthorized) est déclenchée après le démarrage de l’application. Cette exception se produit car l’accès au serveur principal est limité aux seuls utilisateurs autorisés.
3. Ouvrez AppDelegate.cs dans le projet iOS et ajoutez les instructions `using` suivantes :
   
        using Microsoft.WindowsAzure.MobileServices;
        using System.Threading.Tasks;
4. Mettez à jour la classe **AppDelegate** pour implémenter l’interface **IAuthenticate**, comme suit :
   
        public partial class AppDelegate : global::Xamarin.Forms.Platform.iOS.FormsApplicationDelegate, IAuthenticate
5. Mettez à jour la classe **AppDelegate** en ajoutant un champ **MobileServiceUser** et une méthode **Authenticate** requise par l’interface **IAuthenticate**, comme suit :
   
        // Define a authenticated user.
        private MobileServiceUser user;
   
        public async Task<bool> Authenticate()
        {
            var success = false;
            var message = string.Empty;
            try
            {
                // Sign in with Facebook login using a server-managed flow.
                if (user == null)
                {
                    user = await TodoItemManager.DefaultManager.CurrentClient
                        .LoginAsync(UIApplication.SharedApplication.KeyWindow.RootViewController,
                        MobileServiceAuthenticationProvider.Facebook);
                    if (user != null)
                    {
                        message = string.Format("You are now signed-in as {0}.", user.UserId);
                        success = true;                        
                    }
                }        
            }
            catch (Exception ex)
            {
               message = ex.Message;
            }
   
            // Display the success or failure message.
            UIAlertView avAlert = new UIAlertView("Sign-in result", message, null, "OK", null);
            avAlert.Show();         
   
            return success;
        }
   
    Si vous utilisez un fournisseur d’identité différent de Facebook, choisissez une autre valeur pour [MobileServiceAuthenticationProvider].
6. Ajoutez la ligne de code suivante à la méthode **FinishedLaunching** avant l’appel à `LoadApplication()` :
   
        App.Init(this);
   
    L’authentificateur sera ainsi initialisé avant le chargement de l’application.
7. Recompilez et exécutez l’application, puis connectez-vous avec le fournisseur d’authentification choisi et vérifiez que vous êtes en mesure d’accéder aux données en tant qu’utilisateur authentifié.

## Ajout de l’authentification à des projets d’application Windows
Cette section montre comment implémenter l’interface **IAuthenticate** dans les projets d’application Windows 8.1 et Windows Phone 8.1. Ces étapes s’appliquent également aux projets de plateforme Windows universelle (UWP). Vous pouvez ignorer cette section si vous n’utilisez pas d’appareils Windows.

1. Dans Visual Studio, cliquez avec le bouton droit sur le projet **WinApp** ou **WinPhone81**, puis cliquez sur **Définir comme projet de démarrage**.
2. Appuyez sur F5 pour démarrer le projet dans le débogueur, puis vérifiez qu’une exception non prise en charge avec le code d’état 401 (Unauthorized) est déclenchée après le démarrage de l’application. Cette exception se produit car l’accès au serveur principal est limité aux seuls utilisateurs autorisés.
3. Ouvrez MainPage.xaml.cs dans le projet d’application Windows et ajoutez l’instruction `using` suivante :
   
        using Microsoft.WindowsAzure.MobileServices;
        using System.Threading.Tasks;
        using Windows.UI.Popups;
        using <your_Portable_Class_Library_namespace>;
   
    Remplacez `<your_Portable_Class_Library_namespace>` par l’espace de noms de votre bibliothèque de classes portable.
4. Mettez à jour la classe **MainPage** pour implémenter l’interface **IAuthenticate**, comme suit :
   
        public sealed partial class MainPage : IAuthenticate
5. Mettez à jour la classe **MainPage** en ajoutant un champ **MobileServiceUser** et une méthode **Authenticate** requise par l’interface **IAuthenticate**, comme suit :
   
        // Define a authenticated user.
        private MobileServiceUser user;
   
        public async Task<bool> Authenticate()
        {
            string message = string.Empty;
            var success = false;
   
            try
            {
                // Sign in with Facebook login using a server-managed flow.
                if (user == null)
                {
                    user = await TodoItemManager.DefaultManager.CurrentClient
                        .LoginAsync(MobileServiceAuthenticationProvider.Facebook);
                    if (user != null)
                    {
                        success = true;
                        message = string.Format("You are now signed-in as {0}.", user.UserId);
                    }
                }
   
            }
            catch (Exception ex)
            {
                message = string.Format("Authentication Failed: {0}", ex.Message);
            }
   
            // Display the success or failure message.
            await new MessageDialog(message, "Sign-in result").ShowAsync();
   
            return success;
        }

    Si vous utilisez un fournisseur d’identité différent de Facebook, choisissez une autre valeur pour [MobileServiceAuthenticationProvider].

1. Ajoutez la ligne de code suivante dans le constructeur de la classe **MainPage** avant l’appel à `LoadApplication()` :
   
        // Initialize the authenticator before loading the app.
        <your_Portable_Class_Library_namespace>.App.Init(this);
   
    Remplacez `<your_Portable_Class_Library_namespace>` par l’espace de noms de votre bibliothèque de classes portable. S’il s’agit du projet WinApp, passez à l’étape 8. L’étape suivante s’applique uniquement au projet WinPhone81, qui suppose d’effectuer un rappel de connexion.
2. (Facultatif) Dans le projet **WinPhone81**, ouvrez MainPage.xaml.cs et ajoutez les instructions `using` suivantes :
   
        using Microsoft.WindowsAzure.MobileServices;
        using <your_Portable_Class_Library_namespace>;
   
    Remplacez `<your_Portable_Class_Library_namespace>` par l’espace de noms de votre bibliothèque de classes portable.
3. Ajoutez la substitution de méthode **OnActivated** suivante à la classe **App** :
   
       protected override void OnActivated(IActivatedEventArgs args)
       {
           base.OnActivated(args);
   
           // We just need to handle activation that occurs after web authentication. 
           if (args.Kind == ActivationKind.WebAuthenticationBrokerContinuation)
           {
               // Get the client and call the LoginComplete method to complete authentication.
               var client = TodoItemManager.DefaultManager.CurrentClient as MobileServiceClient;
               client.LoginComplete(args as WebAuthenticationBrokerContinuationEventArgs);
           }
       }
   
   Si la substitution de méthode existe déjà, ajoutez simplement le code conditionnel à partir de l’extrait de code ci-dessus.
4. Recompilez et exécutez l’application, puis connectez-vous avec le fournisseur d’authentification choisi et vérifiez que vous êtes en mesure d’accéder aux données en tant qu’utilisateur authentifié.

## Étapes suivantes
Maintenant que vous avez terminé ce didacticiel sur l'authentification de base, vous pouvez passer à l'un des didacticiels suivants :

* [Ajouter des notifications Push à votre application Android](app-service-mobile-xamarin-forms-get-started-push.md) Apprenez à ajouter la prise en charge des notifications Push à votre application et à configurer le serveur principal d’applications mobiles pour utiliser Azure Notification Hubs afin d’envoyer des notifications Push.
* [Activer la synchronisation hors connexion pour votre application](app-service-mobile-xamarin-forms-get-started-offline-data.md) Apprenez à ajouter une prise en charge hors connexion à votre application à l’aide d’un serveur principal d’applications mobiles. La synchronisation hors connexion permet aux utilisateurs finaux d'interagir avec une application mobile pour afficher, ajouter ou modifier des données, même lorsqu'il n'existe aucune connexion réseau.

<!-- Images. -->

<!-- URLs. -->
[apns object]: http://go.microsoft.com/fwlink/p/?LinkId=272333
[LoginAsync]: https://msdn.microsoft.com/library/azure/dn268341(v=azure.10).aspx
[MobileServiceClient]: https://msdn.microsoft.com/library/azure/JJ553674(v=azure.10).aspx
[MobileServiceAuthenticationProvider]: https://msdn.microsoft.com/library/azure/jj730936(v=azure.10).aspx

<!---HONumber=AcomDC_0629_2016-->