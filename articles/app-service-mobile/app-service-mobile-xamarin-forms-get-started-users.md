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
	ms.date="05/05/2016"
	ms.author="wesmc"/>

# Ajout de l'authentification à votre application Xamarin.Forms

[AZURE.INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

##Vue d’ensemble

Cette rubrique montre comment authentifier les utilisateurs d'une application App Service Mobile App à partir de votre application cliente. Dans ce didacticiel, vous allez ajouter l'authentification au projet de démarrage rapide Xamarin.Forms à l'aide d'un fournisseur d'identité pris en charge par App Service. Une fois l’utilisateur authentifié et autorisé par votre application Mobile App, la valeur de l’ID utilisateur s’affiche ; vous pouvez alors accéder aux données de table limitées.

Vous devez commencer par suivre le didacticiel [Création d’une application Xamarin.Forms](app-service-mobile-xamarin-forms-get-started.md). Si vous n’utilisez pas le projet de serveur du démarrage rapide téléchargé, vous devez ajouter le package d’extension d’authentification à votre projet. Pour plus d'informations sur les packages d'extension de serveur, consultez [Fonctionnement avec le Kit de développement logiciel (SDK) du serveur principal .NET pour Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

##Inscription de votre application pour l'authentification et configuration d'App Services

[AZURE.INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

##Restriction des autorisations pour les utilisateurs authentifiés

[AZURE.INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]


##Ajout de l'authentification à la bibliothèque de classes portable

Mobile Apps utilise une méthode `MobileServiceClient.LoginAsync` spécifique à une plateforme pour afficher les données de cache et l'interface de connexion. Pour vous authentifier avec un projet Xamarin Forms, vous allez définir une interface `IAuthenticate` dans la bibliothèque de classes portable. Chaque plateforme que vous souhaitez prendre en charge implémentera cette interface dans le projet de plateforme spécifique.

Vous pourrez également mettre à jour l’interface utilisateur définie dans la bibliothèque de classes portable, en ajoutant un bouton de connexion. L’utilisateur devra cliquer sur ce bouton pour s’authentifier après le démarrage de l’application.

1. Dans Visual Studio ou Xamarin Studio, ouvrez App.cs à partir du projet **portable**. Ajoutez l’instruction `using` suivante au fichier.

		using System.Threading.Tasks;

2. Dans App.cs, ajoutez la définition d’interface `IAuthenticate` suivante immédiatement avant la définition de classe `App`.

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


4. Ouvrez TodoList.xaml.cs à partir du projet **portable**. Ajoutez l’indicateur suivant à la classe `TodoList` pour indiquer si l’utilisateur s’est authentifié ou non.

        bool authenticated = false;


5. Dans TodoList.xaml.cs, mettez à jour la méthode `OnAppearing` de manière à actualiser les éléments uniquement si l’utilisateur est authentifié.


        protected override async void OnAppearing()
        {
            base.OnAppearing();

            // Set syncItems to true in order to synchronize the data on startup when running in offline mode
            if (authenticated == true)
                await RefreshItems(true, syncItems: false);
        }

6. Dans TodoList.xaml.cs, en haut du constructeur de la classe `TodoList`, définissez le bouton de connexion suivant et cliquez sur Gestionnaire...

        public TodoList()
        {
            InitializeComponent();

            manager = TodoItemManager.DefaultManager;

            var loginButton = new Button
            {
                Text = "Login",
                TextColor = Xamarin.Forms.Color.Black,
                BackgroundColor = Xamarin.Forms.Color.Lime,
            };
            loginButton.Clicked += loginButton_Clicked;

            Xamarin.Forms.StackLayout bp = buttonsPanel as StackLayout;
            Xamarin.Forms.StackLayout bpParentStack = bp.Parent.Parent as StackLayout;

            bpParentStack.Padding = new Xamarin.Forms.Thickness(10, 30, 10, 20);
            bp.Orientation = StackOrientation.Vertical;
            bp.Children.Add(loginButton);

			...

7. Dans TodoList.xaml.cs, ajoutez le gestionnaire suivant pour l’événement associé à un clic sur le bouton de connexion

        async void loginButton_Clicked(object sender, EventArgs e)
        {
            if (App.Authenticator != null)
                authenticated = await App.Authenticator.Authenticate();

            // Set syncItems to true in order to synchronize the data on startup when running in offline mode
            if (authenticated == true)
                await RefreshItems(true, syncItems: false);
        }


8. Enregistrez vos modifications et générez le projet de bibliothèque de classes portable en vérifiant qu’il ne contient aucune erreur.


##Ajout de l'authentification à l'application Android

Dans cette section, vous allez ajouter l'authentification pour le projet Android. Vous pouvez ignorer cette section si vous n'utilisez pas d'appareils Android.

1. Dans Visual Studio ou Xamarin Studio, cliquez avec le bouton droit sur le projet **Android**, puis cliquez sur **Définir comme projet de démarrage**.

2. Exécutez le projet dans le débogueur pour vérifier qu'une exception non prise en charge avec le code d'état 401 (Unauthorized) est déclenchée après le démarrage de l'application. Cela se produit, car vous avez restreint l'accès au serveur principal aux utilisateurs autorisés uniquement.

3. Ensuite, ouvrez MainActivity.cs dans le projet Android et ajoutez l’instruction `using` suivante.

		using Microsoft.WindowsAzure.MobileServices;
		using System.Threading.Tasks;

4. Mettez à jour la classe `MainActivity` pour implémenter l’interface `IAuthenticate`.

		public class MainActivity : global::Xamarin.Forms.Platform.Android.FormsApplicationActivity, IAuthenticate


5. Mettez à jour la classe `MainActivity` en ajoutant un champ `MobileServiceUser` et la méthode `Authenticate` illustrés ci-dessous pour prendre en charge l’interface `IAuthenticate`.

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

        private void CreateAndShowDialog(String message, String title)
        {
            AlertDialog.Builder builder = new AlertDialog.Builder(this);

            builder.SetMessage(message);
            builder.SetTitle(title);
            builder.Create().Show();
        }


6. Mettez à jour la méthode `OnCreate` de la classe `MainActivity` pour initialiser l’authentificateur avant le chargement de l’application.

        App.Init((IAuthenticate)this);

        LoadApplication(new App());



7. Régénérez l'application et exécutez-la. Connectez-vous avec le fournisseur d'authentification choisi et vérifiez que vous êtes en mesure d'accéder à la table en tant qu'utilisateur authentifié.



##Ajout de l'authentification à l'application iOS

Dans cette section, vous allez ajouter l'authentification pour le projet iOS. Vous pouvez ignorer cette section si vous n'utilisez pas d'appareils iOS.

1. Dans Visual Studio ou Xamarin Studio, cliquez avec le bouton droit sur le projet **iOS**, puis cliquez sur **Définir comme projet de démarrage**.

2. Exécutez le projet dans le débogueur pour vérifier qu'une exception non prise en charge avec le code d'état 401 (Unauthorized) est déclenchée après le démarrage de l'application. Cela se produit, car vous avez restreint l'accès au serveur principal aux utilisateurs autorisés uniquement.

3. Ensuite, ouvrez AppDelegate.cs dans le projet iOS et ajoutez l’instruction `using` suivante.

		using Microsoft.WindowsAzure.MobileServices;
		using System.Threading.Tasks;

4. Mettez à jour la classe `AppDelegate` pour implémenter l’interface `IAuthenticate`.

		public partial class AppDelegate : global::Xamarin.Forms.Platform.iOS.FormsApplicationDelegate, IAuthenticate


5. Mettez à jour la classe `AppDelegate` en ajoutant un champ `MobileServiceUser` et la méthode `Authenticate` illustrés ci-dessous pour prendre en charge l’interface `IAuthenticate`.

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
                    if (user != null)
                    {
                        UIAlertView avAlert = new UIAlertView("Authentication", "You are now logged in " + user.UserId, null, "OK", null);
                        avAlert.Show();
                    }
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

6. Mettez à jour la méthode `FinishedLaunching` de la classe `AppDelegate` pour initialiser l’authentificateur avant le chargement de l’application.

        App.Init(this);

		LoadApplication (new App ());



7. Régénérez l'application et exécutez-la. Connectez-vous avec le fournisseur d'authentification choisi et vérifiez que vous êtes en mesure d'accéder à la table en tant qu'utilisateur authentifié.




##Ajout de l'authentification à l'application Windows

Dans cette section, vous allez ajouter l'authentification pour le projet WinApp. Vous pouvez ignorer cette section si vous n'utilisez pas d'appareils Windows.

1. Dans Visual Studio, cliquez avec le bouton droit sur le projet **WinApp**, puis cliquez sur **Définir comme projet de démarrage**.

2. Exécutez le projet dans le débogueur pour vérifier qu'une exception non prise en charge avec le code d'état 401 (Unauthorized) est déclenchée après le démarrage de l'application. Cela se produit, car vous avez restreint l'accès au serveur principal aux utilisateurs autorisés uniquement.

3. Ensuite, ouvrez MainPage.xaml.cs dans le projet WinApp et ajoutez l’instruction `using` suivante. Remplacez <*espace de noms de votre bibliothèque de classes portable*> par l'espace de noms de votre bibliothèque de classes portable.

		using Microsoft.WindowsAzure.MobileServices;
		using System.Threading.Tasks;
		using <Your portable class library namespace>;

4. Mettez à jour la classe `MainPage` pour implémenter l’interface `IAuthenticate`.

	    public sealed partial class MainPage : IAuthenticate


5. Mettez à jour la classe `MainPage` en ajoutant un champ `MobileServiceUser` et la méthode `Authenticate` illustrés ci-dessous pour prendre en charge l’interface `IAuthenticate`.

	Si vous souhaitez utiliser un autre `MobileServiceAuthenticationProvider` que Facebook, apportez également les modifications nécessaires.

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
					if (user != null)
					{
	                    var messageDialog = new Windows.UI.Popups.MessageDialog(
								string.Format("you are now logged in - {0}", user.UserId), "Authentication");
	                    messageDialog.ShowAsync();
					}
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

6. Mettez à jour le constructeur de la classe `MainPage` pour initialiser l’authentificateur avant le chargement de l’application. Remplacez <*Your portable class library namespace*> par l’espace de noms de votre bibliothèque de classes portable.

        public MainPage()
        {
            this.InitializeComponent();

            <Your portable class library namespace>.App.Init(this);

            LoadApplication(new <Your portable class library namespace>.App());
        }



7. Régénérez l'application et exécutez-la. Connectez-vous avec le fournisseur d'authentification choisi et vérifiez que vous êtes en mesure d'accéder à la table en tant qu'utilisateur authentifié.


##Ajout de l’authentification à l’application Windows Phone 8.1

Dans cette section, vous allez ajouter une authentification pour le projet WinPhone81. Vous pouvez ignorer cette section si vous n’utilisez pas d’appareils Windows Phone 8.1.

1. Dans Visual Studio, cliquez avec le bouton droit sur le projet **WinPhone81**, puis cliquez sur **Définir comme projet de démarrage**.

2. Exécutez le projet dans le débogueur pour vérifier qu'une exception non prise en charge avec le code d'état 401 (Unauthorized) est déclenchée après le démarrage de l'application. Cela se produit, car vous avez restreint l'accès au serveur principal aux utilisateurs autorisés uniquement.


3. Ensuite, ouvrez MainPage.xaml.cs dans le projet WinPhone81 et ajoutez l’instruction `using` suivante. Remplacez <*Your portable class library namespace*> par l’espace de noms de votre bibliothèque de classes portable.

		using Microsoft.WindowsAzure.MobileServices;
		using System.Threading.Tasks;
		using <Your portable class library namespace>;

4. Mettez à jour la classe `MainPage` pour implémenter l’interface `IAuthenticate`.

	    public sealed partial class MainPage : IAuthenticate


5. Mettez à jour la classe `MainPage` en ajoutant un champ `MobileServiceUser` et la méthode `Authenticate` illustrés ci-dessous pour prendre en charge l’interface `IAuthenticate`.

	Si vous souhaitez utiliser un autre `MobileServiceAuthenticationProvider` que Facebook, apportez également les modifications nécessaires.

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
					if (user != null)
					{
	                    var messageDialog = new Windows.UI.Popups.MessageDialog(
								string.Format("you are now logged in - {0}", user.UserId), "Authentication");
	                    messageDialog.ShowAsync();
					}
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

6. Mettez à jour le constructeur de la classe `MainPage` pour initialiser l’authentificateur avant le chargement de l’application. Remplacez <*Your portable class library namespace*> par l’espace de noms de votre bibliothèque de classes portable.

        public MainPage()
        {
            this.InitializeComponent();

            this.NavigationCacheMode = NavigationCacheMode.Required;

            <Your portable class library namespace>.App.Init(this);

            LoadApplication(new <Your portable class library namespace>.App());
        }

7. Sur Windows Phone, vous devez également effectuer la connexion. Ouvrez App.xaml.cs et ajoutez l’instruction `using` et le code suivants au gestionnaire `OnActivated` dans la `App` classe.

	```
		using Microsoft.WindowsAzure.MobileServices;
	```

		protected override void OnActivated(IActivatedEventArgs args)
		{
		    base.OnActivated(args);

		    if (args.Kind == ActivationKind.WebAuthenticationBrokerContinuation)
		    {
		        var client = TodoItemManager.DefaultManager.CurrentClient as MobileServiceClient;
		        client.LoginComplete(args as WebAuthenticationBrokerContinuationEventArgs);
		    }
		}



8. Régénérez l'application et exécutez-la. Connectez-vous avec le fournisseur d'authentification choisi et vérifiez que vous êtes en mesure d'accéder à la table en tant qu'utilisateur authentifié.

<!-- Images. -->

<!-- URLs. -->
[apns object]: http://go.microsoft.com/fwlink/p/?LinkId=272333

<!---HONumber=AcomDC_0511_2016-->