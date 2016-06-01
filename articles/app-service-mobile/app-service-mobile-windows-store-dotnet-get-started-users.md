<properties
	pageTitle="Ajout de l’authentification à votre plateforme Windows universelle (UWP) | Azure Mobile Apps"
	description="Découvrez comment utiliser Azure App Service Mobile Apps pour authentifier les utilisateurs de votre application de plateforme Windows universelle à l’aide de divers fournisseurs d'identité, notamment AAD, Google, Facebook, Twitter et Microsoft."
	services="app-service\mobile"
	documentationCenter="windows"
	authors="ggailey777"
	manager="erikre"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-windows"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="05/14/2016"
	ms.author="glenga"/>

# Ajout de l'authentification à votre application Windows

[AZURE.INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

Cette rubrique indique comment ajouter l'authentification basée sur le cloud à votre application mobile. Dans ce didacticiel, vous ajoutez l'authentification au projet de démarrage rapide de plateforme Windows universelle pour Mobile Apps à l'aide d'un fournisseur d'identité pris en charge par Azure App Service. Une fois l'utilisateur authentifié et autorisé par votre backend d'application Mobile, la valeur de l'ID utilisateur s'affiche.

Ce didacticiel est basé sur le démarrage rapide de Mobile Apps. Vous devez commencer par suivre le didacticiel [Prise en main de Mobile Apps](app-service-mobile-windows-store-dotnet-get-started.md).

##<a name="register"></a>Inscription de votre application pour l’authentification et configuration d’App Service

[AZURE.INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

##<a name="permissions"></a>Restriction des autorisations pour les utilisateurs authentifiés

[AZURE.INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

À présent, vous pouvez vérifier que l’accès anonyme à votre serveur principal a été désactivé. Avec le projet d’application Windows défini en tant que projet de démarrage, déployez et exécutez l'application, et vérifiez qu'une exception non prise en charge avec le code d'état 401 (Non autorisé) est déclenchée après le démarrage de l'application. Cette exception se produit, car l’application tente d’accéder à votre code Mobile App en tant qu’utilisateur non authentifié, alors que la table *TodoItem* requiert désormais une authentification.

Ensuite, vous allez mettre à jour l'application pour authentifier les utilisateurs avant de demander des ressources à partir de votre service App Service.

##<a name="add-authentication"></a>Ajout de l’authentification à l’application

1. Dans le fichier de projet d’application UWP MainPage.cs et ajoutez l'extrait de code suivant à la classe MainPage :
	
		// Define a member variable for storing the signed-in user. 
        private MobileServiceUser user;

        // Define a method that performs the authentication process
        // using a Facebook sign-in. 
        private async System.Threading.Tasks.Task<bool> AuthenticateAsync()
        {
            string message;
            bool success = false;
            try
            {
                // Change 'MobileService' to the name of your MobileServiceClient instance.
                // Sign-in using Facebook authentication.
                user = await App.MobileService
                    .LoginAsync(MobileServiceAuthenticationProvider.Facebook);
                message =
                    string.Format("You are now signed in - {0}", user.UserId);

                success = true;
            }
            catch (InvalidOperationException)
            {
                message = "You must log in. Login Required";
            }

            var dialog = new MessageDialog(message);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
            return success;
        }

    Ce code authentifie l’utilisateur à l’aide d’une connexion Facebook. Si vous utilisez un fournisseur d'identité différent de Facebook, remplacez la valeur de **MobileServiceAuthenticationProvider** ci-dessus par la valeur de votre fournisseur.

3. Commentez ou supprimez l'appel de la méthode **ButtonRefresh\_Click** (ou **InitLocalStoreAsync**) dans la méthode à substituer **OnNavigatedTo** existante. Cela empêche les données d'être chargées avant que l'utilisateur ne soit authentifié. Ensuite, vous allez ajouter à l’application un bouton **Connexion** qui déclenche l’authentification.

4. Ajoutez l'extrait de code suivant à la classe MainPage :

	    private async void ButtonLogin_Click(object sender, RoutedEventArgs e)
	    {
	        // Login the user and then load data from the mobile app.
	        if (await AuthenticateAsync())
	        {
	            // Switch the buttons and load items from the mobile app.
	            ButtonLogin.Visibility = Visibility.Collapsed;
	            ButtonSave.Visibility = Visibility.Visible;
	            //await InitLocalStoreAsync(); //offline sync support.
	            await RefreshTodoItems();
	        }
	    }
		
5. Ouvrez le fichier de projet MainPage.xaml, recherchez l’élément qui définit le bouton **Enregistrer** et remplacez-le par le code suivant :

        <Button Name="ButtonSave" Visibility="Collapsed" Margin="0,8,8,0" 
				Click="ButtonSave_Click">
            <StackPanel Orientation="Horizontal">
                <SymbolIcon Symbol="Add"/>
                <TextBlock Margin="5">Save</TextBlock>
            </StackPanel>
        </Button>
        <Button Name="ButtonLogin" Visibility="Visible" Margin="0,8,8,0" 
                Click="ButtonLogin_Click" TabIndex="0">
            <StackPanel Orientation="Horizontal">
                <SymbolIcon Symbol="Permissions"/>
                <TextBlock Margin="5">Sign in</TextBlock> 
            </StackPanel>
        </Button>

9. Appuyez sur la touche F5 pour exécuter l'application, puis cliquez sur le bouton **Se connecter** pour vous connecter à l'application avec le fournisseur d'identité choisi. Lorsque vous êtes connecté, l'application s'exécute sans erreur, et vous pouvez exécuter des requêtes sur votre backend et mettre à jour les données.


##<a name="tokens"></a>Stockage du jeton d’authentification sur le client

L’exemple précédent montrait une connexion standard, qui nécessite que le client contacte le fournisseur d’identité et le service d’application à chaque démarrage de l’application. Cette méthode est non seulement inefficace, mais vous pouvez rencontrer des problèmes d'utilisation si de nombreux clients tentent de lancer votre application en même temps. Une meilleure approche consiste à mettre en cache le jeton d’autorisation renvoyé par votre service d’application et à l’utiliser en premier avant de faire appel à la connexion via un fournisseur.

>[AZURE.NOTE]Vous pouvez mettre en cache le jeton émis par App Services, que vous utilisiez l’authentification gérée par un client ou gérée par un service. Ce didacticiel utilise cette dernière.

[AZURE.INCLUDE [mobile-windows-universal-dotnet-authenticate-app-with-token](../../includes/mobile-windows-universal-dotnet-authenticate-app-with-token.md)]

##Étapes suivantes

Maintenant que vous avez terminé ce didacticiel sur l'authentification de base, vous pouvez passer à l'un des didacticiels suivants :

+ [Ajouter des notifications Push à votre application Windows](app-service-mobile-windows-store-dotnet-get-started-push.md) Apprenez à ajouter la prise en charge des notifications push à votre application et à configurer le backend de votre application mobile pour utiliser Azure Notification Hubs afin d'envoyer des notifications push.

+ [Activation de la synchronisation hors connexion pour votre application Windows](app-service-mobile-windows-store-dotnet-get-started-offline-data.md) Apprenez à ajouter une prise en charge hors connexion à votre application à l'aide d'un backend d'application mobile. La synchronisation hors connexion permet aux utilisateurs finaux d'interagir avec une application mobile pour afficher, ajouter ou modifier des données, même lorsqu'il n'existe aucune connexion réseau.


<!-- URLs. -->
[Get started with your mobile app]: app-service-mobile-windows-store-dotnet-get-started.md

<!---HONumber=AcomDC_0518_2016-->