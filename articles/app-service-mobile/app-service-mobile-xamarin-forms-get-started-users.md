<properties 
	pageTitle="Prise en main de l'authentification pour Mobile Apps dans l'application Xamarin.Forms" 
	description="Découvrez comment utiliser Mobile Apps pour authentifier les utilisateurs de votre application Xamarin Forms via divers fournisseurs d'identité, notamment AAD, Google, Facebook, Twitter et Microsoft." 
	services="app-service\mobile" 
	documentationCenter="xamarin" 
	authors="wesmc7777"
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="app-service-mobile" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-xamarin" 
	ms.devlang="dotnet" 
	ms.topic="article"
	ms.date="11/30/2015" 
	ms.author="wesmc"/>

# Ajout de l'authentification à votre application Xamarin.Forms

[AZURE.INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]&nbsp;[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

##Vue d'ensemble

Cette rubrique montre comment authentifier les utilisateurs d'une application App Service Mobile App à partir de votre application cliente. Dans ce didacticiel, vous allez ajouter l'authentification au projet de démarrage rapide Xamarin.Forms à l'aide d'un fournisseur d'identité pris en charge par App Service. Une fois l'utilisateur authentifié et autorisé par votre application Mobile App, la valeur de l'ID utilisateur s'affiche ; vous pouvez alors accéder aux données de table limitées.

Vous devez d'abord terminer le [didacticiel de démarrage rapide Xamarin.Forms](app-service-mobile-xamarin-forms-get-started.md). Si vous n’utilisez pas le projet de serveur du démarrage rapide téléchargé, vous devez ajouter le package d’extension d’authentification à votre projet. Pour plus d'informations sur les packages d'extension de serveur, consultez [Fonctionnement avec le Kit de développement logiciel (SDK) du serveur principal .NET pour Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).


##Inscription de votre application pour l'authentification et configuration d'App Services

[AZURE.INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

##Restriction des autorisations pour les utilisateurs authentifiés

[AZURE.INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]


##Ajout de l'authentification à la bibliothèque de classes portable 

Mobile Apps utilise une méthode `MobileServiceClient.LoginAsync` spécifique à une plateforme pour afficher les données de cache et l'interface de connexion. Pour vous authentifier avec un projet Xamarin Forms, vous allez définir une interface `IAuthenticate` dans la bibliothèque de classes portable. Chaque plateforme que vous souhaitez prendre en charge peut implémenter cette interface dans le projet de plateforme spécifique. Vous allez ajouter le code pour l'authentification avant de lancer des appels sur la table restreinte à partir de la bibliothèque de classes portable.

1. Dans Visual Studio ou Xamarin Studio, ouvrez App.cs à partir du projet **portable**. Ajoutez l'instruction `using` suivante au fichier.

		using System.Threading.Tasks;

2. Dans App.cs, ajoutez la définition d'interface `IAuthenticate` suivante immédiatement avant la définition de classe `App`.

	    public interface IAuthenticate
	    {
	        Task<bool> Authenticate();
	    };

3. Dans App.cs, ajoutez les membres statiques suivants pour initialiser l'interface avec une implémentation spécifique à la plateforme.

		public class App : Application
		{
	
	        public static IAuthenticate Authenticator { get; private set; }
	
	        public static void Init(IAuthenticate authenticator)
	        {
	            Authenticator = authenticator;
	        }
	
			...

4. Ouvrez TodoList.xaml.cs à partir du projet **portable** et mettez à jour la méthode `OnAppearing` pour vous authentifier avant d'essayer d'actualiser les éléments de la table.


        protected override async void OnAppearing()
        {
            base.OnAppearing();

            if (App.Authenticator != null)
                await App.Authenticator.Authenticate();

            // Set syncItems to true in order to synchronize the data on startup when running in offline mode
            await RefreshItems(true, syncItems: false);
        }


5. Enregistrez vos modifications et générez le projet de portail en vérifiant qu'il ne contient aucune erreur.


##Ajout de l'authentification à l'application Android

Dans cette section, vous allez ajouter l'authentification pour le projet Android. Vous pouvez ignorer cette section si vous n'utilisez pas d'appareils Android.

1. Dans Visual Studio ou Xamarin Studio, cliquez avec le bouton droit sur le projet **Android**, puis cliquez sur **Définir comme projet de démarrage**.

2. Exécutez le projet dans le débogueur pour vérifier qu'une exception non prise en charge avec le code d'état 401 (Unauthorized) est déclenchée après le démarrage de l'application. Cela se produit, car vous avez restreint l'accès au serveur principal aux utilisateurs autorisés uniquement.

3. Ensuite, ouvrez MainActivity.cs dans le projet Android et ajoutez l'instruction `using` suivante.

		using Microsoft.WindowsAzure.MobileServices;
		using System.Threading.Tasks;

4. Mettez à jour la classe `MainActivity` pour implémenter l'interface `IAuthenticate`.

		public class MainActivity : global::Xamarin.Forms.Platform.Android.FormsApplicationActivity, IAuthenticate


5. Mettez à jour la classe `MainActivity` en ajoutant un champ `MobileServiceUser` et la méthode `Authenticate` illustrés ci-dessous pour prendre en charge l'interface `IAuthenticate`.
 
	Si vous souhaitez utiliser un autre `MobileServiceAuthenticationProvider` au lieu de Facebook, modifiez également cela.

		// Define a authenticated user.
		private MobileServiceUser user;
	
        public async Task<bool> Authenticate()
        {
            var success = false;
            try
            {
                // Sign in with Facebook login using a server-managed flow.
                user = await TodoItemManager.DefaultManager.CurrentClient.LoginAsync(this,
                    MobileServiceAuthenticationProvider.Facebook);
                CreateAndShowDialog(string.Format("you are now logged in - {0}",
                    user.UserId), "Logged in!");

                success = true;
            }
            catch (Exception ex)
            {
                CreateAndShowDialog(ex.Message, "Authentication failed");
            }
            return success;
        }

6. Mettez à jour la méthode `OnCreate` de la classe `MainActivity` pour initialiser l'authentificateur avant le chargement de l'application.

        App.Init((IAuthenticate)this);

        LoadApplication(new App());



7. Régénérez l'application et exécutez-la. Connectez-vous avec le fournisseur d'authentification choisi et vérifiez que vous êtes en mesure d'accéder à la table en tant qu'utilisateur authentifié.



##Ajout de l'authentification à l'application iOS

Dans cette section, vous allez ajouter l'authentification pour le projet iOS. Vous pouvez ignorer cette section si vous n'utilisez pas d'appareils iOS.

1. Dans Visual Studio ou Xamarin Studio, cliquez avec le bouton droit sur le projet **iOS**, puis cliquez sur **Définir comme projet de démarrage**.

2. Exécutez le projet dans le débogueur pour vérifier qu'une exception non prise en charge avec le code d'état 401 (Unauthorized) est déclenchée après le démarrage de l'application. Cela se produit, car vous avez restreint l'accès au serveur principal aux utilisateurs autorisés uniquement.

3. Ensuite, ouvrez AppDelegate.cs dans le projet iOS et ajoutez l'instruction `using` suivante.

		using Microsoft.WindowsAzure.MobileServices;
		using System.Threading.Tasks;

4. Mettez à jour la classe `AppDelegate` pour implémenter l'interface `IAuthenticate`.

		public partial class AppDelegate : global::Xamarin.Forms.Platform.iOS.FormsApplicationDelegate, IAuthenticate


5. Mettez à jour la classe `AppDelegate` en ajoutant un champ `MobileServiceUser` et la méthode `Authenticate` illustrés ci-dessous pour prendre en charge l'interface `IAuthenticate`.
 
	Si vous souhaitez utiliser un autre `MobileServiceAuthenticationProvider` au lieu de Facebook, modifiez également cela.

		// Define a authenticated user.
		private MobileServiceUser user;
	
        public async Task<bool> Authenticate()
        {
            var success = false;
            try
            {
                // Sign in with Facebook login using a server-managed flow.
                if (user == null)
                {
                    user = await TodoItemManager.DefaultManager.CurrentClient.LoginAsync(UIApplication.SharedApplication.KeyWindow.RootViewController,
                        MobileServiceAuthenticationProvider.Facebook);
                }

                success = true;
            }
            catch (Exception ex)
            {
                UIAlertView avAlert = new UIAlertView("Authentication failed", ex.Message, null, "OK", null);
                avAlert.Show();
            }
            return success;
        }

6. Mettez à jour la méthode `FinishedLaunching` de la classe `AppDelegate` pour initialiser l'authentificateur avant le chargement de l'application.

        App.Init((IAuthenticate)this);

		LoadApplication (new App ());



7. Régénérez l'application et exécutez-la. Connectez-vous avec le fournisseur d'authentification choisi et vérifiez que vous êtes en mesure d'accéder à la table en tant qu'utilisateur authentifié.




##Ajout de l'authentification à l'application Windows

Dans cette section, vous allez ajouter l'authentification pour le projet WinApp. Vous pouvez ignorer cette section si vous n'utilisez pas d'appareils Windows.

1. Dans Visual Studio, cliquez avec le bouton droit sur le projet **WinApp**, puis cliquez sur **Définir comme projet de démarrage**.

2. Exécutez le projet dans le débogueur pour vérifier qu'une exception non prise en charge avec le code d'état 401 (Unauthorized) est déclenchée après le démarrage de l'application. Cela se produit, car vous avez restreint l'accès au serveur principal aux utilisateurs autorisés uniquement.

3. Ensuite, ouvrez MainPage.xaml.cs dans le projet WinApp et ajoutez l'instruction `using` suivante. Remplacez <*espace de noms de votre bibliothèque de classes portable*> par l'espace de noms de votre bibliothèque de classes portable.

		using Microsoft.WindowsAzure.MobileServices;
		using System.Threading.Tasks;
		using <Your portable class library namespace>;

4. Mettez à jour la classe `MainPage` pour implémenter l'interface `IAuthenticate`.

	    public sealed partial class MainPage : IAuthenticate


5. Mettez à jour la classe `MainPage` en ajoutant un champ `MobileServiceUser` et la méthode `Authenticate` illustrés ci-dessous pour prendre en charge l'interface `IAuthenticate`.
 
	Si vous souhaitez utiliser un autre `MobileServiceAuthenticationProvider` au lieu de Facebook, modifiez également cela.

        // Define a authenticated user.
        private MobileServiceUser user;

        public async Task<bool> Authenticate()
        {
            var success = false;
            try
            {
                // Sign in with Facebook login using a server-managed flow.
                if (user == null)
                {
                    user = await TodoItemManager.DefaultManager.CurrentClient.LoginAsync(MobileServiceAuthenticationProvider.Facebook);
                    var messageDialog = new Windows.UI.Popups.MessageDialog(
							string.Format("you are now logged in - {0}", user.UserId), "Authentication");
                    messageDialog.ShowAsync();
                }

                success = true;
            }
            catch (Exception ex)
            {
                var messageDialog = new Windows.UI.Popups.MessageDialog(ex.Message, "Authentication Failed");
                messageDialog.ShowAsync();
            }
            return success;
        }

6. Mettez à jour le constructeur de la classe `MainPage` pour initialiser l'authentificateur avant le chargement de l'application. Remplacez <*espace de noms de votre bibliothèque de classes portable*> par l'espace de noms de votre bibliothèque de classes portable.

        public MainPage()
        {
            this.InitializeComponent();

            <Your portable class library namespace>.App.Init((IAuthenticate)this);
            
            LoadApplication(new WesmcMobileAppGaTest.App());
        }



7. Régénérez l'application et exécutez-la. Connectez-vous avec le fournisseur d'authentification choisi et vérifiez que vous êtes en mesure d'accéder à la table en tant qu'utilisateur authentifié.




<!-- Images. -->

<!-- URLs. -->
[Xamarin Studio]: http://xamarin.com/platform
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Xcode]: https://go.microsoft.com/fwLink/?LinkID=266532
[Installing Xamarin.iOS on Windows]: http://developer.xamarin.com/guides/ios/getting_started/installation/windows/
[apns object]: http://go.microsoft.com/fwlink/p/?LinkId=272333


 

<!---HONumber=AcomDC_1203_2015-->